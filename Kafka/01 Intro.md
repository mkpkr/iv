Kafka is often described as a “distributed commit log” or more recently as a “distributing streaming platform.” 
Data within Kafka is stored durably, in order, can be read deterministically and can be distributed within the system to provide additional protections against failures, as well as significant opportunities for scaling performance.

![[Pasted image 20250903163131.png]]# Messages

The unit of data within Kafka is called a message. (The word record is used interchangably).

A message is simply an array of bytes as far as Kafka is concerned, so the data contained within it does not have a specific format or meaning to Kafka.

A message can have an optional piece of metadata, which is referred to as a key. The key is also a byte array and, as with the message, has no specific meaning to Kafka. Keys are used when messages are to be written to partitions in a more controlled manner.

For efficiency, messages are written into Kafka in batches. A batch is just a collection of messages, all of which are being produced to the same topic and partition. An individual round trip across the network for each message would result in excessive overhead.

This is a trade-off between latency and throughput: the larger the batches, the more messages that can be handled per unit of time, but the longer it takes an individual message to propagate.

Batches are also typically compressed, providing more efficient data transfer and storage at the cost of some processing power.

# Schema

While messages are opaque byte arrays to Kafka itself, it is recommended that additional structure, or schema, be imposed on the message content so that it can be easily understood. Options include JSON and XML, both easy to use and human readable. However, they lack features such as robust type handling and compatibility between schema versions.

Many Kafka developers favor the use of Apache Avro, a serialization framework that provides a compact format, schemas that are separate from the message payloads and that do not require code to be generated when they change, and strong data typing and schema evolution, with both backward and forward compatibility.

# Topics and Partitions

Messages in Kafka are categorized into topics. Topics are additionally broken down into a number of partitions. Going back to the “commit log” description, a partition is a single log.

Messages are written to it in an append-only fashion and are read in order from beginning to end.

There is no guarantee of message ordering across the entire topic, just within a single partition.

![[Pasted image 20250903163145.png]]Each partition can be hosted on a different server, which means that a single topic can be scaled horizontally across multiple servers to provide performance far beyond the ability of a single server.

Additionally, partitions can be replicated, such that different servers will store a copy of the same partition in case one server fails.

Topics can also be configured as log compacted, which means that Kafka will retain only the last message produced with a specific key. This can be useful for changelog-type data, where only the last update is interesting.

# Producers and Consumers

By default, the producer will balance messages over all partitions of a topic evenly. In some cases, the producer will direct messages to specific partitions. This is typically done using the message key and a partitioner that will generate a hash of the key and map it to a specific partition. This ensures that all messages produced with a given key will get written to the same partition.

The producer could also use a custom partitioner that follows other business rules for mapping messages to partitions.

The consumer subscribes to one or more topics and reads the messages in the order in which they were produced to each partition. The consumer keeps track of which messages it has already consumed by keeping track of the offset of messages. Each message in a given partition has a unique offset, and the following message has a greater offset (though not necessarily monotonically greater). A consumer can stop and restart without losing its place.

Consumers work as part of a consumer group. The group ensures that each partition is only consumed by one member.

![[Pasted image 20250903163214.png]]
Here there are three consumers in a single group consuming a topic. Two of the consumers are working from one partition each, while the third consumer is working from two partitions. The mapping of a consumer to a partition is often called ownership of the partition by the consumer.

In this way, consumers can horizontally scale to consume topics with a large number of messages. Additionally, if a single consumer fails, the remaining members of the group will reassign the partitions being consumed to take over for the missing member.

# Brokers and Clusters

A single Kafka server is called a broker. The broker receives messages from producers, assigns offsets to them, and writes the messages to storage on disk. It also services consumers, responding to fetch requests for partitions and responding with the messages that have been published. Depending on the specific hardware and its performance characteristics, a single broker can easily handle thousands of partitions and millions of messages per second.

Kafka brokers are designed to operate as part of a cluster. Within a cluster, one broker will also function as the cluster controller,elected automatically from the live members of the cluster, responsible for administrative operations, including assigning partitions to brokers and monitoring for broker failures.

A partition is owned by a single broker in the cluster, and that broker is called the leader of the partition.

A replicated partition is assigned to additional brokers, called followers of the partition. Replication provides redundancy of messages in the partition, such that one of the followers can take over leadership if there is a broker failure.

All producers must connect to the leader in order to publish messages, but consumers may fetch from either the leader or one of the followers.

![[Pasted image 20250903163230.png]]Kafka brokers are configured with a default retention setting for topics, either retaining messages for some period of time (e.g., 7 days) or until the partition reaches a certain size in bytes (e.g., 1 GB). Once these limits are reached, messages are expired and deleted. In this way, the retention configuration defines a minimum amount of data available at any time. Individual topics can also be configured with their own retention settings so that messages are stored for only as long as they are useful.

# Multiple Clusters & Mirror Maker

As Kafka deployments grow, it is often advantageous to have multiple clusters:

- Segregation of types of data
- Isolation for security requirements
- Multiple datacenters (disaster recovery)

When working with multiple datacenters in particular, it is often required that messages be copied between them. The replication mechanisms within the Kafka clusters are designed only to work within a single cluster, not between multiple clusters.

The Kafka project includes a tool called MirrorMaker, used for replicating data to other clusters.

At its core, MirrorMaker is simply a Kafka consumer and producer, linked together with a queue. Messages are consumed from one Kafka cluster and produced to another.