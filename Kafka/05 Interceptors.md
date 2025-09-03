There are times when you want to modify the behavior of your Kafka client application without modifying its code, perhaps because you want to add identical behavior to all applications in the organization. Or perhaps you don’t have access to the original code.
 
Kafka’s ProducerInterceptor interceptor includes two key methods:
 
	• ProducerRecord<K, V> onSend(ProducerRecord<K, V> record)
		○ This method will be called before the produced record is sent to Kafka, indeed before it is even serialized. 
		○ When overriding this method, you can capture information about the sent record and even modify it. 
		○ Be sure to return a valid ProducerRecord from this method. The record that this method returns will be serialized and sent to Kafka.
	
	• void onAcknowledgement(RecordMetadata metadata, Exception exception)
		○ This method will be called if and when Kafka responds with an acknowledgment for a send. The method does not allow modifying the response from Kafka, but you can capture information about the response.
 
Common use cases for producer interceptors include 

	• capturing monitoring and tracing information
	• enhancing the message with standard headers, especially for lineage tracking purposes
redacting sensitive information