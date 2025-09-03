# Lock

The block-structured approach to synchronization is based on a simple notion of what a lock is. This approach has a number of shortcomings, as follows:
	
- Only one type of lock exists.
- It applies equally to all synchronized operations on the locked object.
- The lock is acquired at the start of the synchronized block or method.
- The lock is released at the end of the block or method.
- Either the lock is acquired or the thread blocks indefinitely—no other outcomes are possible.

If we were going to reengineer the support for locks, we could potentially change several things for the better:

- Add different types of locks (such as reader/writer locks).
- Not restrict locks to blocks (allow a lock in one method and an unlock in another).
- If a thread cannot acquire a lock (e.g., if another thread has the lock), allow the thread to back out or carry on or do something else—a tryLock().
- Allow a thread to attempt to acquire a lock and give up after a certain amount of time.

The key to realizing all of these possibilities is the Lock interface in java.util.concurrent.locks. This interface ships with the following implementations:

- **ReentrantLock**—This is essentially the equivalent of the familiar lock used in Java synchronized blocks but more flexible.
- **ReentrantReadWriteLock**—This can provide better performance in cases where there are many readers but few writers.
 
NOTE Other implementations exist, both within the JDK and written by third parties, but these are by far the most common.

In Deadlock we discussed how locks must be obtained in same order and gave an example of taking the lowest bank account id as the first lock to obtain. 

With a Lock we still need to maintain the principle that locks are always acquired in the same order, but we can use a single block that works for either order, rather than an if/else as shown in Deadlock.

Here is an example of how we could do that (see Blocking Queue for a lock-free implementation):

```java
private final Lock lock = new ReentrantLock();

public boolean transferTo(SafeAccount other, int amount) {
	// We also need code to check to see amount > 0, throw if not
	// ...

	if (accountId == other.getAccountId()) {
		// Can't transfer to your own account
		return false;
	}

	var firstLock = accountId < other.getAccountId() ? lock : other.lock;
	var secondLock = firstLock == lock ? other.lock : lock;

	firstLock.lock();
	try {
		secondLock.lock();
		try {
			if (balance >= amount) {
				balance = balance - amount;
				other.deposit(amount);
				return true;
			}
			return false;
		} finally {
			secondLock.unlock();
		}
	} finally {
		firstLock.unlock();
	}
}
```

The pattern of an initial call to lock() combined with a try ... finally block, where the lock is released in the finally, is a great addition to your toolbox:

 ```java
 Lock l = ...;
 l.lock();
 try {
	 // access the resource protected by this lock
 } finally {
	 l.unlock();
 }
 ```


# Condition

Another aspect of the API provided by `java.util.concurrent` are the condition objects. 

These objects play the same role in the API as `wait()` and `notify()` do in the original intrinsic API but are more flexible. They provide the ability for threads to wait indefinitely for some condition and to be woken up when that condition becomes true.
 
However, unlike the intrinsic API (where the object monitor has only a single condition for signaling), the Lock interface allows the programmer to create as many condition objects as they like. This allows a separation of concerns—for example, the lock can have multiple, disjoint groups of methods that can use separate conditions.
 
A condition object (which implements the interface `Condition`) is created by calling the `newCondition()` method on a lock object. 

As well as condition objects, the API provides a number of latches and barriers as concurrency primitives that may be useful in some circumstances – discussed next.
