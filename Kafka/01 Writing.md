Note: a producer object can be used by multiple threads to send messages.
![[Pasted image 20250903163302.png]]We start producing messages to Kafka by creating a ProducerRecord, which must include the topic we want to send the record to and a value.

Optionally, we can also specify a key, a partition, a timestamp, and/or a collection of headers.

Once we send the ProducerRecord, the first thing the producer will do is serialize the key and value objects to byte arrays so they can be sent over the network.

Next, if we didn’t explicitly specify a partition, the data is sent to a partitioner. The partitioner will choose a partition for us, usually based on the ProducerRecord key.

Once a partition is selected, the producer adds the record to a batch of records that will also be sent to the same topic and partition. A separate thread is responsible for sending those batches of records to the appropriate Kafka brokers.

When the broker receives the messages, it sends back a response. If the messages were successfully written to Kafka, it will return a RecordMetadata object with the topic, partition, and the offset of the record within the partition.

If the broker failed to write the messages, it will return an error. When the producer receives an error, it may retry sending the message a few more times before giving up and returning an error.

# Methods of Sending

- Asynchronous send

- We call the send() method with a callback function, which gets triggered when it receives a response from the Kafka broker.
- org.apache.kafka.Clients.producer.Callback has the following method:

- public void onCompletion(RecordMetadata recordMetadata, Exception e);

- The callbacks execute in the producer’s main thread.

- This guarantees that when we send two messages to the same partition one after another, their callbacks will be executed in the same order that we sent them.
- But it also means that the callback should be reasonably fast to avoid delaying the producer and preventing other messages from being sent. It is not recommended to perform a blocking operation within the callback. Instead, you should use another thread to perform any blocking operation concurrently.

- Fire-and-forget

- We send a message to the server and don’t really care if it arrives successfully or not. Most of the time, it will arrive successfully, since Kafka is highly available and the producer will retry sending messages automatically. However, in case of nonretriable errors or timeout, messages will get lost and the application will not get any information or exceptions about this.

- Synchronous send

- Technically, Kafka producer is always asynchronous—the send() method returns a Future object. However, we use get() to wait on the Future and see if the send() was successful or not before sending the next record.
- This leads to very poor performance, and as a result, synchronous sends are usually not used in production applications (but are very common in code examples).

```java
ProducerRecord<String, String> record = new ProducerRecord<>("CustomerCountry", "Precision Products", "France");
try {
    producer.send(record [, callback]);
} catch (Exception e) { //see note below
    e.printStackTrace();
}
```

Note that the catch block here does not include errors that may occur while sending messages to Kafka brokers or in the brokers themselves, but an exception if the producer encountered errors before sending the message to Kafka. Those can be, for example, a SerializationException when it fails to serialize the message, a BufferExhaustedException or TimeoutException if the buffer is full, or an InterruptException if the sending thread was interrupted.

# Retryable Exceptions

KafkaProducer has two types of errors.

1. Retriable errors are those that can be resolved by sending the message again. For example, a connection error can be resolved because the connection may get reestablished. A “not leader for partition” error can be resolved when a new leader is elected for the partition and the client metadata is refreshed.

	- KafkaProducer can be configured to retry those errors automatically, so the application code will get retriable exceptions only when the number of retries was exhausted and the error was not resolved.

2. Some errors will not be resolved by retrying—for example, “Message size too large.”

	- In those cases, KafkaProducer will not attempt a retry and will return the exception immediately.
