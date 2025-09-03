18:39

	• Kafka is often described as a “distributed commit log” or more recently as a “distributing streaming platform.”  
	
	• Message (record) - array of bytes (no specific meaning to kafka)
		○ Recommended to use a schema like Avro (provides both backward and forward compatiility)
	• Has an optionak key  - array of bytes (no specific meaning to kafka)
	• (Records can, in addition to key and value, also include headers. Record headers give you the ability to add some metadata about the Kafka record, without adding any extra information to the key/value pair of the record itself. )
	• Messages are written in batches
		○ This is a trade-off between latency and throughput: the larger the batches, the more messages that can be handled per unit of time, but the longer it takes an individual message to propagate. 
	
	• Messages categorized into topics, broken down into partitions - parition is a single log
	• Paritions can be replicated.
	• By default, producer balances messages over all parititons evenly. But it can use the message key hash to map keys to paritions, so that each key is only located on one parition, and is therefore kept in order.
	 
	• Topics can be log compacted - retaining only the last message produced with a specific key - useful for changelog type data where only the last update is interesting.
	
	• The consumer keeps track of which messages it has already consumed by keeping track of the offset of messages. 
	• Consumers work as part of a consumer group. The group ensures that each partition is only consumed by one member.  
	
	• Data is only available to consumers after it has been committed to Kafka—meaning it was written to all in-sync replicas. This means that consumers get data that is guaranteed to be consistent.
	
	• Creating a topic with a large number of partitions is a good idea - it allows adding more consumers when the load increases. Keep in mind that there is no point in adding more consumers than you have partitions in a topic—some of the consumers will just be idle. 
	
	• One consumer per thread is the rule. To run multiple consumers in the same group in one application, you will need to run each in its own thread. 
	
	• A single Kafka server is called a broker. The broker receives messages from producers, assigns offsets to them, and writes the messages to storage on disk. Depending on the specific hardware and its performance characteristics, a single broker can easily handle thousands of partitions and millions of messages per second. 
	
	• A partition is owned by a single broker in the cluster, and that broker is called the leader of the partition.  
	• A replicated partition is assigned to additional brokers, called followers of the partition.
	
	• All producers must connect to the leader in order to publish messages, but consumers may fetch from either the leader or one of the followers.  
	
	• Methods of sending: asynchonous (callback), fire and forget, synchronous send (poor performance - not typically used in production)
	
	• acks
		○ how many partition replicas must receive the record before the producer can consider the write successful. 
		○ 0,1,all (all means all in sync replicas)
		○ You trade off reliability for producer latency. However, end-to-end latency is measured from the time a record was produced until it is available for consumers to read and is identical for all three options.  
	
	• The reason is that, in order to maintain consistency, Kafka will not allow consumers to read records until they are written to all in sync replicas. Therefore, if you care about end-to-end latency, rather than just the producer latency, there is no trade-off to make: you will get the same end-to-end latency if you choose the most reliable option. 
	
	• enable.idempotence - supports only once semantics, must also set:
		○ must also set:
			▪ max.in.flight.requests.per.con⁠nection < 5 
			▪ retries > 0 
acks=all

![[Pasted image 20250903163045.png]]

	
	• auto commit:
		○ Every five seconds the consumer will commit the latest offset that your client received from poll(). The five-second interval is the default and is controlled by setting auto.commit.interval.ms. 
		○ Just like everything else in the consumer, the automatic commits are driven by the poll loop. Whenever you poll, the consumer checks if it is time to commit, and if it is, it will commit the offsets it returned in the previous poll. It doesn’t know which events were actually processed, so it is critical to always process all the events returned by poll() before calling poll() again. (Just like poll(), close() also commits offsets automatically.) This is usually not an issue, but pay attention when you handle exceptions or exit the poll loop prematurely. 
		○ Duplicate messages are also more likely because we may have processed messages when there is a crash but the commit interval has not passed so the messages we processed were not committed and will be re-read 
	• alternatives: disable auto commit and use commitSync() or commitAsync() - note that commitAsync() will not retry
	• a common pattern is to use commitAsync(), with commitSync() just before shutdown
