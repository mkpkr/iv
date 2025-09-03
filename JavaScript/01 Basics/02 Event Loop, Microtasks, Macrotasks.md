Browser JavaScript execution flow, as well as in Node.js, is based on an single thread event loop.
 
Event Loop (Macrotask Queue)

The event loop concept is very simple. There’s an endless loop, where the JavaScript engine waits for tasks, executes them and then sleeps, waiting for more tasks.
 
The general algorithm of the engine:
 
	1. While there are tasks:
		a. execute them, starting with the oldest task.
	2. Sleep until a task appears, then go to 1.

The JavaScript engine does nothing most of the time, it only runs if a script/handler/event activates.
 
Examples of tasks:
 
	• When an external script <script src="..."> loads, the task is to execute it.
	• When a user moves their mouse, the task is to dispatch mousemove event and execute handlers.
	• When the time is due for a scheduled setTimeout, the task is to run its callback.
	• …and so on.

Tasks are set – the engine handles them – then waits for more tasks (while sleeping and consuming close to zero CPU).

It may happen that a task comes while the engine is busy, then it’s enqueued.
The tasks form a queue, so-called “macrotask queue” (v8 term):

![[Pasted image 20250903104656.png]]

For instance, while the engine is busy executing a script, a user may move their mouse causing mousemove, and setTimeout may be due and so on, these tasks form a queue, as illustrated on the picture above.

Tasks from the queue are processed on “first come – first served” basis. When the engine browser is done with the script, it handles mousemove event, then setTimeout handler, and so on.

Two more details:
	1. Rendering never happens while the engine executes a task. It doesn’t matter if the task takes a long time. Changes to the DOM are painted only after the task is complete.
	2. If a task takes too long, the browser can’t do other tasks, such as processing user events. So after a time, it raises an alert like “Page Unresponsive”, suggesting killing the task with the whole page. That happens when there are a lot of complex calculations or a programming error leading to an infinite loop.

Show Progress

If you want to show progress of an ongoing task, you must use setTimeout to breakup the portions of work, and show the progress at the intervals of those portions of work.

For example, innerHTML will not update until setTimeout is called:

(https://javascript.info/event-loop#use-case-2-progress-indication)

<div id="progress"></div>
 
<script>
  let i = 0;
 
  function count() {
 
    // do a piece of the heavy job
    do {
      i++;
      progress.innerHTML = i; //will only update when current task in macro task queue (event loop) completes
    } while (i % 1e3 != 0);
 
    if (i < 1e7) {
      setTimeout(count); //cause a break in the event loop, so the DOM can update and other tasks can be executed
    }
 
  }
 
  count();
</script>


Microtasks

Also see Async/Microtasks Queue

Microtasks come solely from our code. 

They are usually created by promises: an execution of .then/catch/finally handler becomes a microtask. 

Microtasks are used “under the cover” of await as well, as it’s another form of promise handling.
 
There’s also a special function queueMicrotask(func) that queues func for execution in the microtask queue.
 
Immediately after every macrotask, the engine executes all tasks from microtask queue, prior to running any other macrotasks or rendering or anything else.
 
setTimeout(() => alert("timeout")); //3
Promise.resolve().then(() => alert("promise")); //2
alert("code"); //1

	1. code shows first, because it’s a regular synchronous call.
	2. promise shows second, because .then passes through the microtask queue, and runs after the current code.
	3. timeout shows last, because it’s a macrotask.

![[Pasted image 20250903104807.png]]

If we’d like to execute a function asynchronously (after the current code), but before changes are rendered or new events handled, we can schedule it with queueMicrotask.