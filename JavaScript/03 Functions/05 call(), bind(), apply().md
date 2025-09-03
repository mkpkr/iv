JavaScript gives exceptional flexibility when dealing with functions. They can be passed around, used as objects, and now we’ll see how to forward calls between them and decorate them.

	• We can use call to specify what this should refer to and call the method: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call
	• Or we can use bind to specify what this should refer to in a future execution: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
	• We cans use apply simnilarly to call but it uses an array rather than individual arguments https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply


call vs apply

The only syntax difference between call and apply is that call expects a list of arguments, while apply takes an array-like object with them.

	• funct.call(context, ar1, ar2, ..., argN)
	• func.call(context, ...arguments);
		○ The spread syntax ... allows to pass iterable args as the list to call.
	• func.apply(context, arguments);
		○ The apply accepts only array-like args.


Caching Decorator Example

function slow(x) {
  // there can be a heavy CPU-intensive job here
  alert(`Called with ${x}`);
  return x;
}
 
function cachingDecorator(func) {
  let cache = new Map();
 
  return function(x) {
    if (cache.has(x)) {    // if there's such key in cache
      return cache.get(x); // read the result from it
    }
 
    let result = func(x);  // otherwise call func
 
    cache.set(x, result);  // and cache (remember) the result
    return result;
  };
}
 
slow = cachingDecorator(slow);
 
alert( slow(1) ); // slow(1) is cached and the result returned
alert( "Again: " + slow(1) ); // slow(1) result returned from cache
 
alert( slow(2) ); // slow(2) is cached and the result returned
alert( "Again: " + slow(2) ); // slow(2) result returned from cache


Object Methods Do Not Work

The caching decorator above is not suited to work with object methods.

For instance, in the code below worker.slow() stops working after the decoration:

// we'll make worker.slow caching
let worker = {
  someMethod() {
    return 1;
  },
 
  slow(x) {
    // scary CPU-heavy task here
    alert("Called with " + x);
    return x * this.someMethod(); // this == undefined
  }
};
 
// same code as before
function cachingDecorator(func) {
  let cache = new Map();
  return function(x) {
    if (cache.has(x)) {
      return cache.get(x);
    }
    let result = func(x);
    cache.set(x, result);
    return result;
  };
}
 
alert( worker.slow(1) ); // the original method works
worker.slow = cachingDecorator(worker.slow); // now make it caching
alert( worker.slow(2) ); // Whoops! Error: Cannot read property 'someMethod' of undefined


When we call func(x), this is not set to the object worker like it would have been if we called worker.slow()

We could see a similar issue if we ran

let func = worker.slow;
func(2);

Using func.call/apply for the context

There’s a special built-in function method that allows to call a function explicitly setting this:

func.call(context, arg1, arg2, ...)

context is the object we want to set as this

function sayHi() {
  alert(this.name);
}
 
let user = { name: "John" };
let admin = { name: "Admin" };
 
// use call to pass different objects as "this"
sayHi.call( user ); // John
sayHi.call( admin ); // Admin

Fixing The Caching Code

Update the call to func with the following to fix it:

let result = func.call(this, x); // "this" is passed correctly now

