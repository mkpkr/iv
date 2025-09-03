USEFUL INFO -> [https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster/](https://www.confluent.io/blog/how-choose-number-topics-partitions-kafka-cluster/)

When the default partitioner is used, the mapping of keys to partitions is consistent only as long as the number of partitions in a topic does not change. So as long as the number of partitions is constant, you can be sure that, for example, records regarding user 045189 will always get written to partition 34. This allows all kinds of optimization when reading data from partitions. However, the moment you add new partitions to the topic, this is no longer guaranteed—the old records will stay in partition 34 while new records may get written to a different partition.

When partitioning keys is important, the easiest solution is to create topics with sufficient partitions and never add partitions.

When to use a custom parititoner? Imagine a store where one of your customers accounts for 10-20% of your total daily volume. What we really want is to give that customer its own partition (or several) and then use hash partitioning to map the rest of the accounts to all other partitions.

See Kafka Definitive Guide (Chatper 3) for an example iof a custom partitioner in this case.