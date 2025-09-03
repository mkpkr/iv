We call the action of updating the current position in the partition an offset commit. Unlike traditional message queues, Kafka does not commit records individually. Instead, consumers commit the last message they’ve successfully processed from a partition and implicitly assume that every message before the last was also successfully processed.

Consumers send a message to Kafka, which updates a special __consumer_offsets topic with the committed offset for each partition. As long as all your consumers are up, running, and churning away, this will have no impact.

However, if a consumer crashes or a new consumer joins the consumer group, this will trigger a rebalance. After a rebalance, each consumer may be assigned a new set of partitions than the one it processed before. In order to know where to pick up the work, the consumer will read the latest committed offset of each partition and continue from there.

If the committed offset is smaller than the offset of the last message the client processed, the messages between the last processed offset and the committed offset will be processed twice.

![[Pasted image 20250903164051.png]]
If the committed offset is larger than the offset of the last message the client actually processed, all messages between the last processed offset and the committed offset will be missed by the consumer group.

![[Pasted image 20250903164103.png]]
When committing offsets either automatically or without specifying the intended offsets, the default behavior is to commit the offset after the last offset that was returned by poll(). This is important to keep in mind when attempting to manually commit specific offsets or seek to commit specific offsets. However, it is also tedious to repeatedly read “Commit the offset that is one larger than the last offset the client received from poll(),” and 99% of the time it does not matter. So, we are going to write “Commit the last offset” when we refer to the default behavior, and if you need to manually manipulate offsets, please keep this note in mind.

# Commit Methods


	• Automatic Commit
		○ enable.auto.commit=true
		○ Every five seconds the consumer will commit the latest offset that your client received from poll(). The five-second interval is the default and is controlled by setting auto.commit.interval.ms. 
		○ Just like everything else in the consumer, the automatic commits are driven by the poll loop. Whenever you poll, the consumer checks if it is time to commit, and if it is, it will commit the offsets it returned in the previous poll. It doesn’t know which events were actually processed, so it is critical to always process all the events returned by poll() before calling poll() again. (Just like poll(), close() also commits offsets automatically.) This is usually not an issue, but pay attention when you handle exceptions or exit the poll loop prematurely.
		○ Duplicate messages are also more likely because we may have processed messages when there is a crash but the commit interval has not passed so the messages we processed were not committed and will be re-read
		
	• Commit Current Offset - Synchronous
		○ Most developers exercise more control over the time at which offsets are committed—both to eliminate the possibility of missing messages and to reduce the number of messages duplicated during rebalancing. The Consumer API has the option of committing the current offset at a point that makes sense to the application developer rather than based on a timer.
		○ By setting enable.auto.commit=false, offsets will only be committed when the application explicitly chooses to do so. The simplest and most reliable of the commit APIs is commitSync(). This API will commit the latest offset returned by poll() and return once the offset is committed, throwing an exception if the commit fails for some reason.
		○ It is important to remember that commitSync() will commit the latest offset returned by poll(), so if you call commitSync() before you are done processing all the records in the collection, you risk missing the messages that were committed but not processed, in case the application crashes. If the application crashes while it is still processing records in the collection, all the messages from the beginning of the most recent batch until the time of the rebalance will be processed twice—this may or may not be preferable to missing messages.

```java
		while (true) {
		    ConsumerRecords<String, String> records = consumer.poll(timeout);
		    for (ConsumerRecord<String, String> record : records) {
		        System.out.printf("topic = %s, partition = %d, offset = %d, customer = %s, country = %s\n",
		            record.topic(), record.partition(), record.offset(), record.key(), record.value());
		    }
		    try {
		        consumer.commitSync();
		    } catch (CommitFailedException e) {
		        log.error("commit failed", e);
		    }
		}

```

		
	• Asynchronous Commit
		○ One drawback of manual commit is that the application is blocked until the broker responds to the commit request. This will limit the throughput of the application. Throughput can be improved by committing less frequently, but then we are increasing the number of potential duplicates that a rebalance may create.
		○ Another option is the asynchronous commit API: instead of waiting for the broker to respond to a commit, we just send the request and continue on.
	 
```java
		while (true) {
		    ConsumerRecords<String, String> records = consumer.poll(timeout);
		    for (ConsumerRecord<String, String> record : records) {
		        System.out.printf("topic = %s, partition = %s, offset = %d, customer = %s, country = %s\n",
		            record.topic(), record.partition(), record.offset(), record.key(), record.value());
		    }
		    consumer.commitAsync();
		}

```

		
		○ The drawback is that while commitSync() will retry the commit until it either succeeds or encounters a nonretriable failure, commitAsync() will not retry. The reason it does not retry is that by the time commitAsync() receives a response from the server, there may have been a later commit that was already successful.
		○ We mention this complication and the importance of correct order of commits because commitAsync() also gives you an option to pass in a callback that will be triggered when the broker responds. It is common to use the callback to log commit errors or to count them in a metric, but if you want to use the callback for retries, you need to be aware of the problem with commit order.
			▪ If using callbacks for retries - a simple pattern to get the commit order right for asynchronous retries is to use a monotonically increasing sequence number. Increase the sequence number every time you commit, and add the sequence number at the time of the commit to the commitAsync callback. When you’re getting ready to send a retry, check if the commit sequence number the callback got is equal to the instance variable; if it is, there was no newer commit and it is safe to retry. If the instance sequence number is higher, don’t retry because a newer commit was already sent.
	
## Combining Synchronous and Asynchronous Commits

Normally, occasional failures to commit without retrying are not a huge problem because if the problem is temporary, the following commit will be successful. But if we know that this is the last commit before we close the consumer, or before a rebalance, we want to make extra sure that the commit succeeds.

Therefore, a common pattern is to combine commitAsync() with commitSync() just before shutdown. Here is how it works (we will discuss how to commit just before rebalance when we get to the section about rebalance listeners):

```java
try {
    while (!closing) {
        ConsumerRecords<String, String> records = consumer.poll(timeout);
        for (ConsumerRecord<String, String> record : records) {
            //...
        }
        consumer.commitAsync();
    }
    consumer.commitSync();
} catch (Exception e) {
    log.error("Unexpected error", e);
} finally {
    consumer.close();
}


```

## Committing a Specified Offset

What if poll() returns a huge batch and you want to commit offsets in the middle of the batch to avoid having to process all those rows again if a rebalance occurs? 

```
private Map<TopicPartition, OffsetAndMetadata> currentOffsets = new HashMap<>();
int count = 0;

//...
 
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
    for (ConsumerRecord<String, String> record : records) {
        
        // ...process data...

        currentOffsets.put(new TopicPartition(record.topic(), record.partition()),
                           new OffsetAndMetadata(record.offset()+1, "no metadata"));

        if (count % 1000 == 0) 
            consumer.commitAsync(currentOffsets, null); //could also use commitSync
        count++;
    }
}
```
# Consuming Records with Specific Offsets

- `seekToBeginning(Collection<TopicPartition> tp)`
- `seekToEnd(Collection<TopicPartition> tp)`
- `seek(TopicPartition partition, long offset)`