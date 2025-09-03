13 April 2023
10:12

In JavaScript we can only inherit from a single object. There can be only one [[Prototype]] for an object. And a class may extend only one other class.
 
A mixin is a class containing methods that can be used by other classes without a need to inherit from it.

class User {
  constructor(name) {
    this.name = name;
  }
}

// mixin
let sayHiMixin = {
  sayHi() {
    alert(`Hello ${this.name}`);
  },
  sayBye() {
    alert(`Bye ${this.name}`);
  }
};
 
// copy the methods
Object.assign(User.prototype, sayHiMixin);
 
// now User can say hi
new User("Dude").sayHi(); // Hello Dude!
 

Mixins can make use of inheritance inside themselves.
 
For instance, here sayHiMixin inherits from sayMixin:
 
let sayMixin = {
  say(phrase) {
    alert(phrase);
  }
};
 
let sayHiMixin = {
  __proto__: sayMixin, // (or we could use Object.setPrototypeOf to set the prototype here)
 
  sayHi() {
    // call parent method
    super.say(`Hello ${this.name}`);
  },
  sayBye() {
    super.say(`Bye ${this.name}`);
  }
};

 
// copy the methods
Object.assign(User.prototype, sayHiMixin);
 
// now User can say hi
new User("Dude").sayHi(); // Hello Dude!
 
Please note that the call to the parent method super.say() from sayHiMixin looks for the method in the prototype of that mixin, not the class. That’s because methods sayHi and sayBye were initially created in sayHiMixin. So even though they got copied, their [[HomeObject]] internal property references sayHiMixin so uses super methods from there (as discussed in Overriding -> "super Remembers its Parent")

![[Pasted image 20250903152724.png]]