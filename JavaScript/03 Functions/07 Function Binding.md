Losing “this”

(Note there is a 3rd solution in Class Fields)

We’ve already seen examples of losing this. Once a method is passed somewhere separately from the object – this is lost.
 
Here’s how it may happen with setTimeout:
 
let user = {
  firstName: "John",
  sayHi() {
    alert(`Hello, ${this.firstName}!`);
  }
};
 
setTimeout(user.sayHi, 1000); // Hello, undefined!

The last line can be written as

let f = user.sayHi; 
setTimeout(f, 1000); // lost user context

The task is quite typical – we want to pass an object method somewhere else (here – to the scheduler) where it will be called. How to make sure that it will be called in the right context?

Solution 1: A Wrapper

The simplest solution is to use a wrapping function:
 
let user = {
  firstName: "John",
  sayHi() {
    alert(`Hello, ${this.firstName}!`);
  }
};
 
setTimeout(function() {
  user.sayHi(); // Hello, John!
}, 1000);

What if before setTimeout triggers (there’s one second delay!) user changes value? Then, suddenly, it will call the wrong object!

Solution 2: bind

let user = {
  firstName: "John",
  sayHi() {
    alert(`Hello, ${this.firstName}!`);
  }
};

let sayHi = user.sayHi.bind(user);
 
// can run it without an object
sayHi(); // Hello, John!
 
setTimeout(sayHi, 1000); // Hello, John!
