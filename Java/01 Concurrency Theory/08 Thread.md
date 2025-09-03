![[Pasted image 20250903215833.png]]
- NEW—the Thread object has been created, but the actual OS thread has not.
	- The actual creation of the OS thread is done by the start() method, which calls into native code to actually perform the relevant system calls

- RUNNABLE—The thread is available to run. The OS is responsible for scheduling it.
	- The Java thread state model does not distinguish between whether a RUNNABLE thread is actually physically executing at that precise moment or is waiting (in the run queue).

- BLOCKED—The thread is not running; it needs to acquire a lock or is in a system call.
- WAITING—The thread is not running; it has called Object.wait() or Thread .join().
- TIMED_WAITING—The thread is not running; it has called Thread.sleep().
- TERMINATED—The thread is not running; it has completed execution.

The scheduler will place a **RUNNABLE** thread into the run queue and, at some later point, will find a core for it to run upon. From there, the thread can proceed by consuming its time allocation and be placed back into the run queue to await further processor time slices.

As well as this scheduling action, the thread itself can indicate that it isn’t able to make use of the core at this time. This can be achieved in two different ways; in both cases, the thread is immediately removed from the core by the OS:

1. The program code indicates by calling Thread.sleep() that the thread should wait for a fixed time before continuing.

	a. The Java thread transitions into the **TIMED_WAITING** state, and the operating system sets a timer.
	b. When it expires, the sleeping thread is woken up and is ready to run again and is placed back in the run queue.

2. The thread recognizes that it must wait until some external condition has been met and calls Object.wait().

	a. The thread will transition into **WAITING** and will wait indefinitely.
	b. It will not normally wake up until the operating system signals that the condition may have been met—usually by some other thread calling Object.notify() on the current object.

As well as these two possibilities that are under the threads control, a thread can transition into the **BLOCKED** state because it’s waiting on I/O or to acquire a lock held by another thread.

Finally, if the OS thread corresponding to a Java Thread has ceased execution, then that thread object will have transitioned into the **TERMINATED** state.

**Note:  Never use `Thread.stop()` or `Thread.suspend()`**