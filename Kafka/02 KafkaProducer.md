# Mandatory Properties
A Kafka producer has three mandatory properties:

	• bootstrap.servers
		○ List of host:port pairs of brokers that the producer will use to establish initial connection to the Kafka cluster. 
		○ This list doesn’t need to include all brokers, since the producer will get more information after the initial connection. But it is recommended to include at least two, so in case one broker goes down, the producer will still be able to connect to the cluster.
		
	• key.serializer
		○ Name of a class that will be used to serialize the keys of the records we will produce to Kafka.
		○ Should be set to a name of a class that implements the org.apache.kafka.common.serialization.Serializer interface. The producer will use this class to serialize the key object to a byte array. The Kafka client package includes 
			▪ ByteArraySerializer (which doesn’t do much)
			▪ String Serial⁠izer
			▪ IntegerSerializer, 
			▪ and much more, so if you use common types, there is no need to implement your own serializers.
		○ Setting key.serializer is required even if you intend to send only values, but you can use the Void type for the key and the VoidSerializer.
		
	• value.serializer
		○ Name of a class that will be used to serialize the values of the records we will produce to Kafka.

# Optional Properties

The producer has a large number of configuration parameters that are documented in [Apache Kafka documentation](https://oreil.ly/RkxSS), and many have reasonable defaults, so there is no reason to tinker with every single parameter. However, some of the parameters have a significant impact on memory use, performance, and reliability of the producers.

## client.id

client.id is a logical identifier for the client and the application it is used in. This can be any string and will be used by the brokers to identify messages sent from the client. It is used in logging and metrics and for quotas. Choosing a good client name will make troubleshooting much easier—it is the difference between “We are seeing a high rate of authentication failures from IP 104.27.155.134” vs “...from the Order Validation service”

## acks

The acks parameter controls how many partition replicas must receive the record before the producer can consider the write successful. By default, Kafka will respond that the record was written successfully after the leader received the record (release 3.0 of Apache Kafka is expected to change this default).

This option has a significant impact on the durability of written messages, discussed more in [Reliability](onenote:#Reliability&section-id={71653c44-04ff-46e9-9137-7860adf31275}&page-id={ccb04d49-7b89-45fd-b707-b304a80dd7ae}&end).


	• acks=0
		○ The producer will not wait for a reply from the broker before assuming the message was sent successfully. If the broker does not receive the message, the producer will not know about it, and the message will be lost. However, because the producer is not waiting for any response from the server, it can send messages as fast as the network will support, so this setting can be used to achieve very high throughput.
	• acks=1
		○ The producer will receive a success response from the broker the moment the leader replica receives the message. If the message can’t be written to the leader (e.g., if the leader crashed and a new leader was not elected yet), the producer will receive an error response and can retry sending the message, avoiding potential loss of data. The message can still get lost if the leader crashes and the latest messages were not yet replicated to the new leader.
	• acks=all
		○ The producer will receive a success response from the broker once all in sync replicas receive the message. This is the safest mode, however the latency will be higher, since we will be waiting for more than just one broker to receive the message.

You trade off reliability for producer latency. However, end-to-end latency is measured from the time a record was produced until it is available for consumers to read and is identical for all three options.

The reason is that, in order to maintain consistency, Kafka will not allow consumers to read records until they are written to all in sync replicas. Therefore, if you care about end-to-end latency, rather than just the producer latency, there is no trade-off to make: you will get the same end-to-end latency if you choose the most reliable option.

## Message Delivery Time

We divide the time spent sending a ProduceRecord into two time intervals that are handled separately:

- Time until an async call to send() returns. During this interval, the thread that called send() will be blocked.
- From the time an async call to send() returned successfully until the callback is triggered (with success or failure). This is the same as from the point a Produce Re⁠cord was placed in a batch for sending until Kafka responds with success, nonretriable failure, or we run out of time allocated for sending.
![[Pasted image 20250903163611.png]]


	• max.block.ms
		○ Controls how long the producer may block when calling send() and when explicitly requesting metadata via partitionsFor(). Those methods may block when the producer’s send buffer is full or when metadata is not available. When max.block.ms is reached, a timeout exception is thrown.
		
	• linger.ms
		○ Controls the amount of time to wait for additional messages before sending the current batch. KafkaProducer sends a batch of messages either when the current batch is full or when the linger.ms limit is reached. 
		○ By default, the producer will send messages as soon as there is a sender thread available to send them, even if there’s just one message in the batch. By setting linger.ms higher than 0, we instruct the producer to wait a few milliseconds to add additional messages to the batch before sending it to the brokers. This increases latency a little and significantly increases throughput—the overhead per message is much lower, and compression, if enabled, is much better.
	
	• delivery.timeout.ms
		○ This will limit the amount of time spent from the point a record is ready for sending (send() returned successfully and the record is placed in a batch) until either the broker responds or the client gives up, including time spent on retries. This time should be greater than linger.ms and request.timeout.ms. If you try to create a producer with an inconsistent timeout configuration, you will get an exception. Messages can be successfully sent much faster than delivery.timeout.ms, and typically will.
		○ If the producer exceeds delivery.timeout.ms while retrying, the callback will be called with the exception that corresponds to the error that the broker returned before retrying. If delivery.timeout.ms is exceeded while the record batch was still waiting to be sent, the callback will be called with a timeout exception.
		○ You can configure the delivery timeout to the maximum time you’ll want to wait for a message to be sent, typically a few minutes, and then leave the default number of retries (virtually infinite). With this configuration, the producer will keep retrying for as long as it has time to keep trying (or until it succeeds). This is a much more reasonable way to think about retries. Our normal process for tuning retries is: “In case of a broker crash, it typically takes leader election 30 seconds to complete, so let’s keep retrying for 120 seconds just to be on the safe side.” Instead of converting this mental dialog to number of retries and time between retries, you just configure deliver.timeout.ms to 120.
	
	• request.timeout.ms
		○ This parameter controls how long the producer will wait for a reply from the server when sending data. Note that this is the time spent waiting on each producer request before giving up; it does not include retries, time spent before sending, and so on. If the timeout is reached without reply, the producer will either retry sending or complete the callback with a TimeoutException.
	
	• retries and retry.backoff.ms
		○ When the producer receives an error message from the server, the error could be transient (e.g., a lack of leader for a partition). In this case, the value of the retries parameter will control how many times the producer will retry sending the message before giving up and notifying the client of an issue. 
		○ By default, the producer will wait 100 ms between retries, but you can control this using the retry.backoff.ms parameter.
		○ We recommend against using these parameters in the current version of Kafka. Instead, test how long it takes to recover from a crashed broker (i.e., how long until all partitions get new leaders), and set delivery.timeout.ms such that the total amount of time spent retrying will be longer than the time it takes the Kafka cluster to recover from the crash—otherwise, the producer will give up too soon.
		○ Not all errors will be retried by the producer. Some errors are not transient and will not cause retries (e.g., “message too large” error). In general, because the producer handles retries for you, there is no point in handling retries within your own application logic. You will want to focus your efforts on handling nonretriable errors or cases where retry attempts were exhausted.

## batch.size

When multiple records are sent to the same partition, the producer will batch them together. This parameter controls the amount of memory in bytes (not messages!) that will be used for each batch. When the batch is full, all the messages in the batch will be sent. However, this does not mean that the producer will wait for the batch to become full. The producer will send half-full batches and even batches with just a single message in them. Therefore, setting the batch size too large will not cause delays in sending messages; it will just use more memory for the batches.

Setting the batch size too small will add some overhead because the producer will need to send messages more frequently.

## buffer.memory

This config sets the amount of memory the producer will use to buffer messages waiting to be sent to brokers. If messages are sent by the application faster than they can be delivered to the server, the producer may run out of space, and additional send() calls will block for max.block.ms and wait for space to free up before throwing an exception. Note that unlike most producer exceptions, this timeout is thrown by send() and not by the resulting Future.

## compression.type

By default, messages are sent uncompressed. This parameter can be set to snappy, gzip, lz4, or zstd. 

	• Snappy compression was invented by Google to provide decent compression ratios with low CPU overhead and good performance, so it is recommended in cases where both performance and bandwidth are a concern. 
	• Gzip compression will typically use more CPU and time but results in better compression ratios, so it is recommended in cases where network bandwidth is more restricted. 

By enabling compression, you reduce network utilization and storage, which is often a bottleneck when sending messages to Kafka.

## max.in.flight.requests.per.connection

This controls how many message batches the producer will send to the server without receiving responses. Higher settings can increase memory usage while improving throughput. [Apache’s wiki experiments show](https://oreil.ly/NZmJ0) that in a single-DC environment, the throughput is maximized with only 2 in-flight requests; however, the default value is 5 and shows similar performance.

### Ordering Guarantees

Apache Kafka preserves the order of messages within a partition. Setting the retries parameter to nonzero and the max.in. flight.requests.per.connection to more than 1 means that it is possible that the broker will fail to write the first batch of messages, succeed in writing the second (which was already in-flight), and then retry the first batch and succeed, thereby reversing the order.

Since we want at least two in-flight requests for performance reasons, and a high number of retries for reliability reasons, the best solution is to set enable.idempotence=true.

This guarantees message ordering with up to five in-flight requests and also guarantees that retries will not introduce duplicates.

## max.request.size

This setting controls the size of a produce request sent by the producer. It caps both the size of the largest message that can be sent and the number of messages that the producer can send in one request. For example, with a default maximum request size of 1 MB, the largest message you can send is 1 MB, or the producer can batch 1,024 messages of size 1 KB each into one request. In addition, the broker has its own limit on the size of the largest message it will accept (message.max.bytes). It is usually a good idea to have these configurations match, so the producer will not attempt to send messages of a size that will be rejected by the broker.

## receive.buffer.bytes and send.buffer.bytes

These are the sizes of the TCP send and receive buffers used by the sockets when writing and reading data. If these are set to –1, the OS defaults will be used. It is a good idea to increase these when producers or consumers communicate with brokers in a different datacenter, because those network links typically have higher latency and lower bandwidth.

## enable.idempotence

Starting in version 0.11, Kafka supports exactly once semantics. Exactly once is a fairly large topic, but idempotent producer is a simple and highly beneficial part of it.

Suppose you configure your producer to maximize reliability: acks=all and a decently large delivery.timeout.ms to allow sufficient retries. These make sure each message will be written to Kafka at least once. In some cases, this means that messages will be written to Kafka more than once. For example, imagine that a broker received a record from the producer, wrote it to local disk, and the record was successfully replicated to other brokers, but then the first broker crashed before sending a response to the producer. The producer will wait until it reaches request.time⁠out.ms and then retry. The retry will go to the new leader that already has a copy of this record since the previous write was replicated successfully. You now have a duplicate record.

To avoid this, you can set enable.idempotence=true. When the idempotent producer is enabled, the producer will attach a sequence number to each record it sends. If the broker receives records with the same sequence number, it will reject the second copy and the producer will receive the harmless DuplicateSequenceException.


Enabling idempotence requires 
	• max.in.flight.requests.per.con⁠nection < 5
	• retries > 0
	• acks=all

If incompatible values are set, a ConfigException will be thrown.