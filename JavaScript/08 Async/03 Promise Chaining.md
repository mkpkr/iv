Promise chaining allows us to perform asynchronous actions sequentially.

Every call to then returns a new promise, so that we can call the next then on it.


Returning a Value
 
When a handler returns a value, it becomes the result of that promise, so the next .then is called with it.
 
new Promise(function(resolve, reject) {
 
  setTimeout(() => resolve(1), 1000);
 
}).then(function(result) { 
 
  alert(result); // 1
  return result * 2;
 
}).then(function(result) {
 
  alert(result); // 2
  return result * 2;
 
}).then(function(result) {
 
  alert(result); // 4
 
});
 

Note that calling:
 
promise.then(...);
promise.then(...);
promise.then(...);
 
...is not promise chaining - this is adding multiple handlers to the same promise.


Returning a Promise

If a handler returns a promise, further handlers wait until it settles, and then get its result.
 
For instance:

new Promise(function(resolve, reject) {
 
  setTimeout(() => resolve(1), 1000);
 
}).then(function(result) {
 
  alert(result); // 1
 
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(result * 2), 1000);
  });
 
}).then(function(result) {
 
  alert(result); // 2
 
  return new Promise((resolve, reject) => {
    setTimeout(() => resolve(result * 2), 1000);
  });
 
}).then(function(result) {
 
  alert(result); // 4
 
});


Example: loadScript

Here we load three scripts that rely on the previous loaded scripts, we then call several functions inside the scripts.

loadScript("/article/promise-chaining/one.js")
  .then(script => loadScript("/article/promise-chaining/two.js"))
  .then(script => loadScript("/article/promise-chaining/three.js"))
  .then(script => {
    // scripts are loaded, we can use functions declared there
    one();
    two();
    three();
  });


We could add .then to each loadscript call, but it would look bad as it indents grows to the right:

loadScript("/article/promise-chaining/one.js").then(script1 => {
  loadScript("/article/promise-chaining/two.js").then(script2 => {
    loadScript("/article/promise-chaining/three.js").then(script3 => {
      one();
      two();
      three();
    });
  });
});

People who start to use promises sometimes don’t know about chaining, so they write it this way. Generally, chaining is preferred.


Thenables

To be precise, a handler may return not exactly a promise, but a so-called “thenable” object – an arbitrary object that has a method .then. It will be treated the same way as a promise.
 
The idea is that 3rd-party libraries may implement “promise-compatible” objects of their own. They can have an extended set of methods, but also be compatible with native promises, because they implement .then.
