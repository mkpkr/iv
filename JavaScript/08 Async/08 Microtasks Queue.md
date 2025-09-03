Also described in EventLoop and MicroTasks  with example including a timeout (macro task).

Promise handlers .then/.catch/.finally are always asynchronous.
 
Even when a Promise is immediately resolved, the code on the lines below .then/.catch/.finally will still execute before these handlers.
  
let promise = Promise.resolve();
promise.then(() => alert("promise done!")); // this alert shows last
alert("code finished"); // this alert shows first

If you run it, you see "code finished" first, and then "promise done!."
 
Why did the .then trigger afterwards?
 
Asynchronous tasks need proper management. For that, the ECMA standard specifies an internal queue PromiseJobs, more often referred to as the “microtask queue” (V8 term).
 
As stated in the specification:
 
	• The queue is first-in-first-out: tasks enqueued first are run first.
	• Execution of a task is initiated only when nothing else is running.

Or, to put it more simply, when a promise is ready, its .then/catch/finally handlers are put into the queue; they are not executed yet. When the JavaScript engine becomes free from the current code, it takes a task from the queue and executes it.

If we’d like to execute a function asynchronously (after the current code), but before changes are rendered or new events handled, we can schedule it with queueMicrotask.

![[Pasted image 20250903155334.png]]