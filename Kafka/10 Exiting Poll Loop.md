When you decide to shut down the consumer, and you want to exit immediately even though the consumer may be waiting on a long poll(), you will need another thread to call consumer.wakeup(). If you are running the consumer loop in the main thread, this can be done from ShutdownHook. 

Note that consumer.wakeup() is the only consumer method that is safe to call from a different thread. Calling wakeup will cause poll() to exit with WakeupException, or if consumer.wakeup() was called while the thread was not waiting on poll, the exception will be thrown on the next iteration when poll() is called. 

The WakeupException doesn’t need to be handled, but before exiting the thread, you must call consumer.close(). Closing the consumer will commit offsets if needed and will send the group coordinator a message that the consumer is leaving the group. The consumer coordinator will trigger rebalancing immediately, and you won’t need to wait for the session to timeout before partitions from the consumer you are closing will be assigned to another consumer in the group.
 
```java
Runtime.getRuntime().addShutdownHook(new Thread() {
    public void run() {
        System.out.println("Starting exit...");
        consumer.wakeup();
        try {
            mainThread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
});
 
//...

try {
    // looping until ctrl-c, the shutdown hook will cleanup on exit
    while (true) {
        ConsumerRecords<String, String> records = movingAvg.consumer.poll(Duration.ofMillis(10000));
        for (ConsumerRecord<String, String> record : records) {
           //...
        }
        for (TopicPartition tp: consumer.assignment())
            System.out.println("Committing offset at position:" + consumer.position(tp));
        consumer.commitSync();
    }
} catch (WakeupException e) {
    // ignore for shutdown
} finally {
    consumer.close();
    System.out.println("Closed consumer and we are done");
}
```
