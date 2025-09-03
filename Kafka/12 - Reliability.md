# Replication Factor


	• Availability
		○ A partition with just one replica will become unavailable even during a routine restart of a single broker. The more replicas we have, the higher availability we can expect.
	• Durability
		○ Each replica is a copy of all the data in a partition. If a partition has a single replica and the disk becomes unusable for any reason, we’ve lost all the data in the partition. With more copies, especially on different storage devices, the probability of losing all of them is reduced.
	• Throughput
		○ With each additional replica, we multiply the inter-broker traffic. If we produce to a partition at a rate of 10 MBps, then a single replica will not generate any replication traffic. If we have 2 replicas, then we’ll have 10 MBps replication traffic, with 3 replicas it will be 20 MBps, and with 5 replicas it will be 40 MBps. We need to take this into account when planning the cluster size and capacity.
	• End-to-end latency
		○ Each produced record has to be replicated to all in-sync replicas before it is available for consumers. In theory, with more replicas, there is higher probability that one of these replicas is a bit slow and therefore will slow the consumers down. In practice, if one broker becomes slow for any reason, it will slow down every client that tries using it, regardless of replication factor.
	• Cost
		○ This is the most common reason for using a replication factor lower than 3 for noncritical data. The more replicas we have of our data, the higher the storage and network costs. Since many storage systems already replicate each block 3 times, it sometimes makes sense to reduce costs by configuring Kafka with a replication factor of 2. Note that this will still reduce availability compared to a replication factor of 3, but durability will be guaranteed by the storage device.



Kafka will always make sure each replica for a partition is on a separate broker. In some cases, this is not safe enough. If all replicas for a partition are placed on brokers that are on the same rack, and the top-of-rack switch misbehaves, we will lose availability of the partition regardless of the replication factor. To protect against rack-level misfortune, we recommend placing brokers in multiple racks and using the broker.rack broker configuration parameter to configure the rack name for each broker. If rack names are configured, Kafka will make sure replicas for a partition are spread across multiple racks in order to guarantee even higher availability. When running Kafka in cloud environments, it is common to consider availability zones as separate racks.

## Minimum In-Sync Replicas

There are cases where even though we configured a topic to have three replicas, we may be left with a single in-sync replica (brokers down, network issues etc). If this replica becomes unavailable, we may have to choose between availability and consistency. Note that part of the problem is that, per Kafka reliability guarantees, data is considered committed when it is written to all in-sync replicas, even when all means just one replica and the data could be lost if that replica is unavailable.

When we want to be sure that committed data is written to more than one replica, we need to set the minimum number of in-sync replicas to a higher value. If a topic has three replicas and we set min.insync.replicas to 2, then producers can only write to a partition in the topic if at least two out of the three replicas are in sync.

# Reliable Producers

There are two important things that everyone who writes applications that produce to Kafka must pay attention to:

- Use the correct acks configuration to match reliability requirements
- Handle errors correctly both in configuration and in code

## Send Acknowledgments

Producers can choose between three different acknowledgment modes:


	• acks=0
		○ This means that a message is considered to be written successfully to Kafka if the producer managed to send it over the network. We will still get errors if the object we are sending cannot be serialized or if the network card failed, but we won’t get any error if the partition is offline, a leader election is in progress, or even if the entire Kafka cluster is unavailable. 
		○ Running with acks=0 has low produce latency (which is why we see a lot of benchmarks with this configuration), but it will not improve end-to-end latency (remember that consumers will not see messages until they are replicated to all available replicas).
	
	• acks=1
		○ This means that the leader will send either an acknowledgment or an error the moment it gets the message and writes it to the partition data file (but not necessarily synced to disk). We can lose data if the leader shuts down or crashes and some messages that were successfully written to the leader and acknowledged were not replicated to the followers before the crash. 
		○ With this configuration, it is also possible to write to the leader faster than it can replicate messages and end up with under-replicated partitions, since the leader will acknowledge messages from the producer before replicating them.
	
	• acks=all
		○ This means that the leader will wait until all in-sync replicas get the message before sending back an acknowledgment or an error. In conjunction with the min.insync.replicas configuration on the broker, this lets us control how many replicas get the message before it is acknowledged. This is the safest option—the producer won’t stop trying to send the message before it is fully committed. This is also the option with the longest producer latency—the producer waits for all in-sync replicas to get all the messages before it can mark the message batch as “done” and carry on.

## Configuring Producer Retries

There are two parts to handling errors in the producer: the errors that the producers handle automatically for us and the errors that we, as developers using the producer library, must handle.

The producer can handle retriable errors. When the producer sends messages to a broker, the broker can return either a success or an error code. Those error codes belong to two categories—errors that can be resolved after retrying and errors that won’t be resolved. For example, if the broker returns the error code LEADER_NOT_AVAILABLE, the producer can try sending the message again—maybe a new broker was elected and the second attempt will succeed. This means that LEADER_NOT_AVAILABLE is a retriable error. On the other hand, if a broker returns an INVALID_CONFIG exception, trying the same message again will not change the configuration. This is an example of a nonretriable error.

In general, when our goal is to never lose a message, our best approach is to configure the producer to keep trying to send the messages when it encounters a retriable error. And the best approach to retries, as recommended in [KafkaProducer](onenote:#KafkaProducer&section-id={71653c44-04ff-46e9-9137-7860adf31275}&page-id={457315b7-f1a3-4bb7-b672-59d9e8e9be30}&end), is to leave the number of retries at its current default (MAX_INT, or effectively infinite) and use delivery.timout.ms to configure the maximum amount of time we are willing to wait until giving up on sending a message—the producer will retry sending the message as many times as possible within this time interval.

Retrying to send a failed message includes a risk that both messages were successfully written to the broker, leading to duplicates. Retries and careful error handling can guarantee that each message will be stored at least once, but not exactly once. Using enable.idempotence=true will cause the producer to include additional information in its records, which brokers will use to skip duplicate messages caused by retries.

## Additional Error Handling

Using the built-in producer retries is an easy way to correctly handle a large variety of errors without loss of messages, but as developers, we must still be able to handle other types of errors. These include:


	• Nonretriable broker errors, such as errors regarding message size, authorization errors, etc.
	• Errors that occur before the message was sent to the broker—for example, serialization errors
	• Errors that occur when the producer exhausted all retry attempts or when the available memory used by the producer is filled to the limit due to using all of it to store messages while retrying
	• Timeouts
 
In [Writing](onenote:#Writing&section-id={71653c44-04ff-46e9-9137-7860adf31275}&page-id={7ddd471f-c7ce-4871-8b06-4b68e2142391}&end) we discussed how to write error handlers. The content of these error handlers is specific to the application and its goals—do we throw away “bad messages”? Log errors? Stop reading messages from the source system? Apply back pressure to the source system to stop sending messages for a while? Store these messages in a directory on the local disk? These decisions depend on the architecture and the product requirements. Just note that if all the error handler is doing is retrying to send the message, then we’ll be better off relying on the producer’s retry functionality.

# Reliable Consumers

Data is only available to consumers after it has been committed to Kafka—meaning it was written to all in-sync replicas. This means that consumers get data that is guaranteed to be consistent. The only thing consumers are left to do is make sure they keep track of which messages they’ve read and which messages they haven’t. This is key to not losing messages while consuming them.

The main way consumers can lose messages is when committing offsets for events they’ve read but haven’t completely processed yet. This way, when another consumer picks up the work, it will skip those messages and they will never get processed.

This was discussed in [Commits  Offsets](onenote:#Commits%20%20Offsets&section-id={71653c44-04ff-46e9-9137-7860adf31275}&page-id={455e616e-6869-4fad-8f82-79784d46dd44}&end)

## Consumers May Need to Retry

In some cases, after calling poll and processing records, some records are not fully processed and will need to be processed later. For example, we may try to write records from Kafka to a database but find that the database is not available at that moment and we need to retry later. Note that unlike traditional pub/sub messaging systems, Kafka consumers commit offsets and do not “ack” individual messages. This means that if we failed to process record #30 and succeeded in processing record #31, we should not commit offset #31—this would result in marking as processed all the records up to #31 including #30, which is usually not what we want. Instead, try following one of the following two patterns.

One option when we encounter a retriable error is to commit the last record we processed successfully. We’ll then store the records that still need to be processed in a buffer (so the next poll won’t override them), use the consumer pause() method to ensure that additional polls won’t return data, and keep trying to process the records.

A second option when encountering a retriable error is to write it to a separate topic and continue. A separate consumer group can be used to handle retries from the retry topic, or one consumer can subscribe to both the main topic and to the retry topic but pause the retry topic between retries. This pattern is similar to the dead-letter-queue system used in many messaging systems.