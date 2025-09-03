“Promisification” is a long word for a simple transformation. It’s the conversion of a function that accepts a callback into a function that returns a promise.

Such transformations are often required in real-life, as many functions and libraries are callback-based. But promises are more convenient, so it makes sense to promisify them.

Remember, a promise may have only one result, but a callback may technically be called many times.

So promisification is only meant for functions that call the callback once. Further calls will be ignored.

An example of promisification, we have a function with a callback such as:

function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
 
  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));
 
  document.head.append(script);
}
 
// usage:
// loadScript('path/script.js', (err, script) => {...})
 
This is a typical callback (a function with two arguments: err, result).
 
We can make this a promise by creating a new wrapper function around the original loadScript function. It calls it providing its own callback that translates to promise resolve/reject.
 
let loadScriptPromise = function(src) {
  return new Promise((resolve, reject) => {
    loadScript(src, (err, script) => {
      if (err) reject(err);
      else resolve(script);
    });
  });
};
 
// usage:
// loadScriptPromise('path/script.js').then(...)
 
 
In practice we may need to promisify more than one function, so it makes sense to use a helper.
 
We’ll call it promisify(f): it accepts a to-promisify function f and returns a wrapper function.
 
function promisify(f) {
  return function (...args) { // return a wrapper-function
    return new Promise((resolve, reject) => {
      function callback(err, result) { // our custom callback for f
        if (err) {
          reject(err);
        } else {
          resolve(result);
        }
      }
 
      args.push(callback); // append our custom callback to the end of f arguments
 
      f.call(this, ...args); // call the original function
    });
  };
}
 
// usage:
// let loadScriptPromise = promisify(loadScript);
// loadScriptPromise(...).then(...);
 
Here, promisify assumes that the original function expects a callback with exactly two arguments (err, result). That’s what we encounter most often. 
