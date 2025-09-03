A promise is a special JavaScript object that links the “producing code” and the “consuming code” together. 

See end of this page for real example.

Producer – The Promise

The “producing code” takes whatever time it needs to produce the promised result, and the “promise” makes that result available to all of the subscribed code when it’s ready.

let promise = new Promise(function(resolve, reject) {
  // executor (the producing code) calls resolve or reject when done
  // typically this will be asyncronous code
});

The function passed to new Promise is called the executor. When new Promise is created, the executor runs automatically. It contains the producing code which should eventually produce the result.
 
resolve and reject are callbacks provided by JavaScript itself. Our code is only inside the executor, and it calls one of the two provided call back functions.
 
When the executor obtains the result, be it soon or late, doesn’t matter, it should call one of these callbacks:
 
	• resolve(value) — if the job is finished successfully, with result value
	• reject(error) — if an error has occurred, error is the error object

The promise object returned by the new Promise constructor has these internal properties:

	• state
		○ initially "pending"
		○ then changes to either "fulfilled" when resolve is called 
		○ or "rejected" when reject is called
	• result
		○ initially undefined
		○ then changes to value when resolve(value) is called 
or error when reject(error) is called

![[Pasted image 20250903155132.png]]
let promise = new Promise(function(resolve, reject) {
  // executor (the producing code)
  setTimeout(() => resolve("done"), 1000);
});

let promise = new Promise(function(resolve, reject) {
  // executor (the producing code)
  setTimeout(() => reject(new Error("Whoops!")), 1000);
});


Consumer – then, catch, finally

then

Consuming functions can be registered (subscribed) using the methods then and catch.

promise.then(
  function(result) { /* handle a successful result */ },
  function(error) { /* handle an error */ }
);

For example:
 
// resolve runs the first function in then
promise.then(
  result => alert(result),
  error => alert(error)
);
 
If we are only interested in successful completions we can provide just one argument:
 
promise.then(alert);

catch
 
If we’re interested only in errors, then we can use use catch(errorHandlingFunction), :
 
// .catch(f) is the same as promise.then(null, f)
promise.catch(alert); // shows "Error: Whoops!" after 1 second


finally
 
finally(f) - f runs always, when the promise is settled: be it resolve or reject.
 
The idea of finally is to set up a handler for performing cleanup/finalizing after the previous operations are complete.
 
// 1) runs when the promise is settled, doesn't matter successfully or not
promise.finally(() => stop loading indicator)

// 2) so the loading indicator is always stopped before we go on to the next handler
       .then(result => show result, 
             err => show error)
 
 
A finally handler has no arguments, it doesn’t get the outcome of the previous handler (outcome is passed through instead, to the next suitable handler).

In finally we don’t know whether the promise is successful or not. That’s all right, as our task is usually to perform “general” finalizing procedures.
 
However, if a finally handler throws an error, this error goes to the next handler, instead of any previous outcome from the promise.


Settled Promises

We can attach handlers to settled promises

If a promise is pending, then/catch/finally handlers wait for its outcome.
 
Sometimes, it might be that a promise is already settled when we add a handler to it. In such case, these handlers just run immediately:
 
// the promise becomes resolved immediately upon creation
let promise = new Promise(resolve => resolve("done!"));
 
promise.then(alert); // done! (shows up right now)


Re-written Example From Callbacks

function loadScript(src) {
  return new Promise(function(resolve, reject) {
    let script = document.createElement('script');
    script.src = src;
 
    script.onload = () => resolve(script);
    script.onerror = () => reject(new Error(`Script load error for ${src}`));
 
    document.head.append(script);
  });
}
 
 
let promise = loadScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js");
 
promise.then(
  script => alert(`${script.src} is loaded!`),
  error => alert(`Error: ${error.message}`)
);

//a second handler
promise.then(script => alert('Another handler...'));


|                                                                                                                                       |                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Promise                                                                                                                               | Callback                                                                                                                                                                         |
| Promises allow us to do things in the natural order. First, we run loadScript(script), and .then we write what to do with the result. | We must have a callback function at our disposal when calling loadScript(script, callback). In other words, we must know what to do with the result before loadScript is called. |
| We can call .then on a Promise as many times as we want. Each time, we’re adding a new handler, a new subscribing function.           | There can be only one callback.                                                                                                                                                  |
