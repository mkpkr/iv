Sometimes you know you have a single consumer that always needs to read data from all the partitions in a topic, or from a specific partition in a topic. In this case, there is no reason for groups or rebalances—just assign the consumer-specific topic and/or partitions, consume messages, and commit offsets on occasion (although you still need to configure group.id to commit offsets, without calling subscribe the consumer won’t join any group).

When you know exactly which partitions the consumer should read, you don’t subscribe to a topic—instead, you assign yourself a few partitions. A consumer can either subscribe to topics (and be part of a consumer group) or assign itself partitions, but not both at the same time.

```java
List<PartitionInfo> partitionInfos = consumer.partitionsFor("topic");
 
if (partitionInfos != null) {
    for (PartitionInfo partition : partitionInfos) {
        partitions.add(new TopicPartition(partition.topic(), partition.partition()));
    }
    consumer.assign(partitions);
 
    while (true) {
        ConsumerRecords<String, String> records = consumer.poll(timeout);
 
        for (ConsumerRecord<String, String> record: records) {
            //...
        }
        consumer.commitSync();
    }
}
```
Other than the lack of rebalances and the need to manually find the partitions, everything else is business as usual. Keep in mind that if someone adds new partitions to the topic, the consumer will not be notified. You will need to handle this by checking consumer.partitionsFor() periodically or simply by bouncing the application whenever partitions are added.