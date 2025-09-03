A consumer will want to do some cleanup work before exiting and also before partition rebalancing.
 
If you know your consumer is about to lose ownership of a partition, you will want to commit offsets of the last event you’ve processed. Perhaps you also need to close file handles, database connections, and such.
 
The Consumer API allows you to run your own code when partitions are added or removed from the consumer. You do this by passing a ConsumerRebalanceListener when calling the subscribe() method. ConsumerRebalanceListener has three methods you can implement:

	• public void onPartitionsAssigned(Collection<TopicPartition> partitions)
		○ Called after partitions have been reassigned to the consumer but before the consumer starts consuming messages. 
		○ This is where you prepare or load any state that you want to use with the partition, seek to the correct offsets if needed, or similar. 
		○ Any preparation done here should be guaranteed to return within max.poll.timeout.ms so the consumer can successfully join the group.
	
	• public void onPartitionsRevoked(Collection<TopicPartition> partitions)
		○ Called when the consumer has to give up partitions that it previously owned—either as a result of a rebalance or when the consumer is being closed. 
		○ In the common case, when an eager rebalancing algorithm is used, this method is invoked before the rebalancing starts and after the consumer stopped consuming messages. 
		○ If a cooperative rebalancing algorithm is used, this method is invoked at the end of the rebalance, with just the subset of partitions that the consumer has to give up. 
		○ This is where you want to commit offsets, so whoever gets this partition next will know where to start.
	
	• public void onPartitionsLost(Collection<TopicPartition> partitions)
		○ Only called when a cooperative rebalancing algorithm is used, and only in exceptional cases where the partitions were assigned to other consumers without first being revoked by the rebalance algorithm (in normal cases, onPartitionsRevoked() will be called). 
		○ This is where you clean up any state or resources that are used with these partitions. Note that this has to be done carefully—the new owner of the partitions may have already saved its own state, and you’ll need to avoid conflicts. 
		○ Note that if you don’t implement this method, onPartitionsRevoked() will be called instead.
 
If you use a cooperative rebalancing algorithm, note that:

	• onPartitionsAssigned() will be invoked on every rebalance, as a way of notifying the consumer that a rebalance happened. However, if there are no new partitions assigned to the consumer, it will be called with an empty collection.
	• onPartitionsRevoked() will be invoked in normal rebalancing conditions, but only if the consumer gave up the ownership of partitions. It will not be called with an empty collection.
	• onPartitionsLost() will be invoked in exceptional rebalancing conditions, and the partitions in the collection will already have new owners by the time the method is invoked.
 
If you implemented all three methods, you are guaranteed that during a normal rebalance, onPartitionsAssigned() will be called by the new owner of the partitions that are reassigned only after the previous owner completed onPartitionsRevoked() and gave up its ownership.
 
