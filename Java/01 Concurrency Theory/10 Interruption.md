Iterruption is opt-in, not forced. The following join() will block forever:

```java {5}
var t = new Thread(() -> { while (true); });
t.start();

t.interrupt();
t.join(); //blocks forever
```

The while loop above never checks the interrupted state, which we do below:

```java {2}
var t = new Thread(() -> { 
	while (!Thread.interrupted()); 
});
t.start();
 
t.interrupt();
t.join();
```

# InterruptedException

It is common for methods in the JDK that are blocking—whether on IO, waiting on a lock, or other scenarios—to check the thread interrupt state. The convention is that these methods will throw InterruptedException, a checked exception.

This explains why, for instance, Thread.sleep() requires you to add the InterruptedException to the method signature or handle it.

## interrupted() vs isInterupted()

The following does not print "Thread was interrupted":

```
var t = new Thread(() -> { 
	try {
		Thread.sleep(1000);
	} catch (InterruptedException e) {
		e.printStackTrace();
	}
	
	if (thisThread.isInterrupted()) {
		System.out.println("Thread was interrupted");
	}
});

t.start();
t.interrupt();
t.join();
```

 The thread was interrupted so why doesn't it satisy the if condition?

When `Thread.sleep` is interrupted, before throwing `InterruptedException`, it calls `Thread.interrupted()` **to clear the state**, because it considers the exception handled.

- `public static boolean interrupted()`
	- Called statically on current running thread
	- Get the interrupted status of the thread, and clear the interrupted status of the thread

- `public boolean isInterrupted()`
	- Called on a thread object
	- Get the interrupted status of the thread, do not clear