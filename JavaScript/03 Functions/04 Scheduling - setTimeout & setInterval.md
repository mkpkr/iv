	• setTimeout allows us to run a function once after the interval of time.
	• setInterval allows us to run a function repeatedly, starting after the interval of time, then repeating continuously at that interval.
	• clearTimeout – cancel the scheduled timer function

let timerId = setTimeout(func|code, [delay], [arg1], [arg2], ...)
//or
let timerId = setInterval(func|code, [delay], [arg1], [arg2], ...)

//cancel
clearTimeout(timerId);


If the first argument is a string, then JavaScript creates a function from it.
 
But using strings is not recommended, use arrow functions instead of them:
 
setTimeout(() => alert('Hello'), 1000);
 
 
More Flexible Interval Scheduling
 
There are two ways of running something regularly.
 
One is setInterval. The other one is a nested setTimeout, like this:
 
/** instead of:
let timerId = setInterval(() => alert('tick'), 2000);
*/
 
let timerId = setTimeout(function tick() {
   alert('tick');
   timerId = setTimeout(tick, 2000);
}, 2000);
 
The nested setTimeout is a more flexible method than setInterval. This way the next call may be scheduled differently, depending on the results of the current one.
 
And if the functions that we’re scheduling are CPU-hungry, then we can measure the time taken by the execution and plan the next call sooner or later.
 
Nested setTimeout allows to set the delay between the executions more precisely than setInterval - we can set the delay for after the function finishes, not when the function started (think Java scheduleAtFixedRate vs scheduleWithFixedDelay)
 
Garbage Collection

When a function is passed in setInterval/setTimeout, an internal reference is created to it and saved in the scheduler. It prevents the function from being garbage collected, even if there are no other references to it, until clearInterval is called.
 
There’s a side effect. A function references the outer lexical environment, so, while it lives, outer variables live too. They may take much more memory than the function itself. 

So when we don’t need the scheduled function anymore, it’s better to cancel it, even if it’s very small.
 
 
Zero Delay setTimeout

There’s a special use case: setTimeout(func, 0), or just setTimeout(func).
 
This schedules the execution of func as soon as possible. But the scheduler will invoke it only after the currently executing script is complete.
 
So the function is scheduled to run “right after” the current script.
 
For instance, this outputs “Hello”, then immediately “World”:
 
setTimeout(() => alert("World"));
 
alert("Hello");

The first line “puts the call into calendar after 0ms”. But the scheduler will only “check the calendar” after the current script is complete, so "Hello" is first, and "World" – after it.
 
 
Zero delay is in fact not zero (in a browser)

In the browser, there’s a limitation of how often nested timers can run. The HTML Living Standard says: “after five nested timers, the interval is forced to be at least 4 milliseconds.”.

That limitation comes from ancient times and many scripts rely on it, so it exists for historical reasons.

For server-side JavaScript, that limitation does not exist, and there exist other ways to schedule an immediate asynchronous job, like setImmediate for Node.js. So this note is browser-specific.
