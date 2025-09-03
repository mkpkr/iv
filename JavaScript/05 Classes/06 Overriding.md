Method Overriding

Classes provide "super" keyword for that.

	• super.method(...) to call a parent method.
	• super(...) to call a parent constructor (inside our constructor only).

Arrow functions do not get their own super:

class Rabbit extends Animal {
  stop() {
    setTimeout(() => super.stop(), 1000); // call parent stop after 1sec
  }
}

If we specified a “regular” function here, there would be an error:
 
// Unexpected super
setTimeout(function() { super.stop() }, 1000);


Constructor Overriding

Default Constructor
 
According to the specification, if a class extends another class and has no constructor, then the following “empty” constructor is generated:
 
class Rabbit extends Animal {
  // generated for extending classes without own constructors
  constructor(...args) {
    super(...args);
  }
}

Custom Constructor

First Attempt:

class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  // ...
}
 
class Rabbit extends Animal {
 
  constructor(name, earLength) {
    this.speed = 0;
    this.name = name;
    this.earLength = earLength;
  }
 
  // ...
}
 
// Doesn't work!
let rabbit = new Rabbit("White Rabbit", 10); // Error: this is not defined.

Constructors in inheriting classes must call super(), and do it before using this.

In JavaScript, there’s a distinction between a constructor function of an inheriting class (so-called “derived constructor”) and other functions. A derived constructor has a special internal property that affects its behavior with new: [[ConstructorKind]]:"derived".
  
When a regular function is executed with new, it creates an empty object and assigns it to this.

But when a derived constructor runs, it doesn’t do this. It expects the parent constructor to do this job.

So a derived constructor must call super in order to execute its parent (base) constructor, otherwise the object for this won’t be created. And we’ll get an error.
 
class Animal {
 
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
 
  // ...
}


class Rabbit extends Animal {
 
  constructor(name, earLength) {
    super(name);
    this.earLength = earLength;
  }
 
  // ...
}
 
// now fine
let rabbit = new Rabbit("White Rabbit", 10);


super Remembers its Parent

Methods have an internal property [[HomeObject]] that allows super to work.

We are used to functions being "free", they are not bound to any context and this can change depending on how they are called.

The very existence [[HomeObject]] means methods are not "free" like functions.

However, the only place this property is used and you will notice the "non-freeness" is methods that use super.

super will always refer to its original parent object, even if the method is assigned to a variable of another object later.

let animal = {
  sayHi() {
    alert(`I'm an animal`);
  }
};
 
// rabbit inherits from animal
let rabbit = {
  __proto__: animal,
  sayHi() {
    super.sayHi();
  }
};


let plant = {
  sayHi() {
    alert("I'm a plant");
  }
};
 
// tree inherits from plant
let tree = {
  __proto__: plant,
  sayHi: rabbit.sayHi
};
 
tree.sayHi();  // I'm an animal (?!?)
