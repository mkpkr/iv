You can’t have multiple consumers that belong to the same group in one thread, and you can’t have multiple threads safely use the same consumer.

One consumer per thread is the rule. To run multiple consumers in the same group in one application, you will need to run each in its own thread. It is useful to wrap the consumer logic in its own object and then use Java’s ExecutorService to start multiple threads, each with its own consumer.

The Confluent blog has a [tutorial](https://oreil.ly/8YOVe) that shows how to do just that.

Another approach can be to have one consumer populate a queue of events and have multiple worker threads perform work from this queue. You can see an example of this pattern in a [blog post](https://oreil.ly/uMzj1) from Igor Buzatović.

# Mandatory Properties

Creating a KafkaConsumer is very similar to creating a KafkaProducer—you create a Java Properties instance with the properties you want to pass to the consumer.  To start, we just need to use the three mandatory properties: bootstrap.servers, key.deserializer, and value.deserializer.

There is a fourth property, which is not strictly mandatory but very commonly used. The property is group.id, and it specifies the consumer group the KafkaConsumer instance belongs to. While it is possible to create consumers that do not belong to any consumer group, this is uncommon.

# Subscribing

```java
KafkaConsumer<String, String> consumer =  
    new KafkaConsumer<String, String>(props);

consumer.subscribe(Collections.singletonList("customerCountries"));
```

It is also possible to call subscribe with a regular expression. The expression can match multiple topic names, and if someone creates a new topic with a name that matches, a rebalance will happen almost immediately and the consumers will start consuming from the new topic.

```java
consumer.subscribe(Pattern.compile("test.*"));
```

WARNING: If your Kafka cluster has large number of partitions, perhaps 30,000 or more, you should be aware that the filtering of topics for the subscription is done on the client side. This means that when you subscribe to a subset of topics via a regular expression rather than via an explicit list, the consumer will request the list of all topics and their partitions from the broker in regular intervals. When the topic list is large and there are many consumers, the size of the list of topics and partitions is significant, and the regular expression subscription has significant overhead on the broker, client, and network. There are cases where the bandwidth used by the topic metadata is larger than the bandwidth used to send data. This also means that in order to subscribe with a regular expression, the client needs permissions to describe all topics in the cluster—that is, a full describe grant on the entire cluster.

# The Poll Loop

At the heart of the Consumer API is a simple loop for polling the server for more data. The main body of a consumer will look as follows:

Duration timeout = Duration.ofMillis(100);
 
```java
while (true) {
    ConsumerRecords<String, String> records = consumer.poll(timeout);
 
    for (ConsumerRecord<String, String> record : records) {
        System.out.printf("topic = %s, partition = %d, offset = %d, " + "customer = %s, country = %s\n",
                          record.topic(), record.partition(), record.offset(), record.key(), record.value());
        int updatedCount = 1;
        if (custCountryMap.containsKey(record.value())) {
            updatedCount = custCountryMap.get(record.value()) + 1;
        }
        custCountryMap.put(record.value(), updatedCount);
 
        JSONObject json = new JSONObject(custCountryMap);
        System.out.println(json.toString());
    }
}
```

If poll() is not invoked for longer than max.poll.interval.ms, the consumer will be considered dead and evicted from the consumer group, so avoid doing anything that can block for unpredictable intervals inside the poll loop.

# Optional Properties

## fetch.min.bytes

Allows a consumer to specify the minimum amount of data that it wants to receive from the broker when fetching records, reducing the load on both the consumer and the broker, by default one byte. If a broker receives a request for records from a consumer but the new records amount to fewer bytes than fetch.min.bytes, the broker will wait until more messages are available before sending the records back to the consumer.

You will want to set this parameter higher than the default if the consumer is using too much CPU when there isn’t much data available, or reduce load on the brokers when you have a large number of consumers—although keep in mind that increasing this value can increase latency for low-throughput cases.

## fetch.max.wait.ms

Max wait time before returning records, by default Kafka will wait up to 500 ms. This results in up to 500 ms of extra latency in case there is not enough data flowing to the Kafka topic to satisfy the minimum amount of data to return. If you want to limit the potential latency (usually due to SLAs controlling the maximum latency of the application), you can set fetch.max.wait.ms to a lower value.

If you set fetch.max.wait.ms to 100 ms and fetch.min.bytes to 1 MB, Kafka will receive a fetch request from the consumer and will respond with data either when it has 1 MB of data to return or after 100 ms, whichever happens first.

## fetch.max.bytes

The maximum bytes that Kafka will return whenever the consumer polls a broker (50 MB by default). Note that records are sent to the client in batches, and if the first record-batch that the broker has to send exceeds this size, the batch will be sent and the limit will be ignored. This guarantees that the consumer can continue making progress.

## max.poll.records

Controls the maximum number of records that a single call to poll() will return. Use this to control the amount of data (but not the size of data) your application will need to process in one iteration of the poll loop.

## max.partition.fetch.bytes

This property controls the maximum number of bytes the server will return per partition (1 MB by default). Note that controlling memory usage using this configuration can be quite complex, as you have no control over how many partitions will be included in the broker response. Therefore, we highly recommend using fetch.max.bytes instead, unless you have special reasons to try and process similar amounts of data from each partition.

## session.timeout.ms & heartbeat.interval.ms

The amount of time a consumer can be out of contact with the brokers while still considered alive defaults to 10 seconds. If more than session.timeout.ms passes without the consumer sending a heartbeat to the group coordinator, it is considered dead and the group coordinator will trigger a rebalance.

This property is closely related to heartbeat.interval.ms, which controls how frequently the Kafka consumer will send a heartbeat to the group coordinator, whereas ses⁠sion.timeout.ms controls how long a consumer can go without sending a heartbeat. Therefore, those two properties are typically modified together—heartbeat.interval.ms must be lower than session.timeout.ms and is usually set to one-third of the timeout value.

Setting session.timeout.ms lower than the default will allow consumer groups to detect and recover from failure sooner but may also cause unwanted rebalances. Setting it higher will reduce the chance of accidental rebalance but also means it will take longer to detect a real failure.

## max.poll.interval.ms

Length of time during which the consumer can go without polling before it is considered dead. As mentioned earlier, heartbeats and session timeouts are the main mechanism by which Kafka detects dead consumers and takes their partitions away. However, we also mentioned that heartbeats are sent by a background thread. There is a possibility that the main thread consuming from Kafka is deadlocked, but the background thread is still sending heartbeats. This means that records from partitions owned by this consumer are not being processed. The easiest way to know whether the consumer is still processing records is to check whether it is asking for more records. However, the intervals between requests for more records are difficult to predict and depend on the amount of available data, the type of processing done by the consumer, and sometimes on the latency of additional services.

In applications that need to do time-consuming processing on each record that is returned, max.poll.records is used to limit the amount of data returned and therefore limit the duration before the application is available to poll() again. Even with max.poll.records defined, the interval between calls to poll() is difficult to predict, and max.poll.interval.ms is used as a fail-safe or backstop.

It has to be an interval large enough that it will very rarely be reached by a healthy consumer but low enough to avoid significant impact from a hanging consumer.

The default value is 5 minutes.

## default.api.timeout.ms

This is the timeout that will apply to (almost) all API calls made by the consumer when you don’t specify an explicit timeout while calling the API. The default is 1 minute, and since it is higher than the request timeout default, it will include a retry when needed.

The notable exception to APIs that use this default is the poll() method that always requires an explicit timeout.

## request.timeout.ms

This is the maximum amount of time the consumer will wait for a response from the broker. If the broker does not respond within this time, the client will assume the broker will not respond at all, close the connection, and attempt to reconnect.

This configuration defaults to 30 seconds, and it is recommended not to lower it.

## auto.offset.reset

This property controls the behavior of the consumer when it starts reading a partition for which it doesn’t have a committed offset, or if the committed offset it has is invalid (usually because the consumer was down for so long that the record with that offset was already aged out of the broker).

The default is “latest,” which means that lacking a valid offset, the consumer will start reading from the newest records (records that were written after the consumer started running). The alternative is “earliest,” which means that lacking a valid offset, the consumer will read all the data in the partition, starting from the very beginning. Setting auto.offset.reset to none will cause an exception to be thrown when attempting to consume from an invalid offset.

## enable.auto.commit

This parameter controls whether the consumer will commit offsets automatically, and defaults to true. Set it to false if you prefer to control when offsets are committed, which is necessary to minimize duplicates and avoid missing data. If you set enable.auto.commit to true, then you might also want to control how frequently offsets will be committed using auto.commit.interval.ms.

## partition.assignment.strategy

Partitions are assigned to consumers in a consumer group. A PartitionAssignor is a class that, given consumers and topics they subscribed to, decides which partitions will be assigned to which consumer. By default, Kafka has the following assignment strategies:

	• Range (default)
		○ org.apache.kafka.clients.consumer.RangeAssignor
		○ Assigns to each consumer a consecutive subset of partitions from each topic it subscribes to.
	• RoundRobin
		○ org.apache.kafka.clients.consumer.RoundRobinAssignor
		○ Takes all the partitions from all subscribed topics and assigns them to consumers sequentially, one by one.
	• Sticky
		○ org.apache.kafka. clients.consumer.StickyAssignor
		○ The Sticky Assignor has two goals: the first is to have an assignment that is as balanced as possible, and the second is that in case of a rebalance, it will leave as many assignments as possible in place, minimizing the overhead associated with moving partition assignments from one consumer to another. 
	• Cooperative Sticky
		○ org.apache.kafka.clients.consumer. CooperativeStickyAssignor
		○ This assignment strategy is identical to that of the Sticky Assignor but supports cooperative rebalances in which consumers can continue consuming from the partitions that are not reassigned.

## client.id

This can be any string, and will be used by the brokers to identify requests sent from the client, such as fetch requests. It is used in logging and metrics, and for quotas.

## group.instance.id

This can be any unique string and is used to provide a consumer with static group membership.

## receive.buffer.bytes & send.buffer.bytes

These are the sizes of the TCP send and receive buffers used by the sockets when writing and reading data. If these are set to –1, the OS defaults will be used. It can be a good idea to increase these when producers or consumers communicate with brokers in a different datacenter, because those network links typically have higher latency and lower bandwidth.

## offsets.retention.minutes (broker config)

This is a broker configuration, but it is important to be aware of it due to its impact on consumer behavior. As long as a consumer group has active members (i.e., members that are actively maintaining membership in the group by sending heartbeats), the last offset committed by the group for each partition will be retained by Kafka, so it can be retrieved in case of reassignment or restart.

However, once a group becomes empty, Kafka will only retain its committed offsets to the duration set by this configuration—7 days by default. Once the offsets are deleted, if the group becomes active again it will behave like a brand-new consumer group with no memory of anything it consumed in the past.