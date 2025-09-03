When you add a method to a class like this (only this method syntax, see section below)...

class Person {
  name = 'Max';

  constructor() {
    this.age = 30;
  }

  greet() {
    console.log(
      'Hi, I am ' + this.name + ' and I am ' + this.age + ' years old.'
    );
  }

...it is actually created on the prototype, because while fields become properties on an object which typically get changed/updated, methods stay the same and JS puts them on the object's prototype.

This is an optimization and is desired behavior!

All objects created using the same constructor will get the same object as their prototype, meaning the exact same object in memory. So the method is only created once no matter how many objects we create.

New Method on Each Instance

If you use other syntax, the method will be created in memory with each object instance which can (probably only very slightly) impact performance:

greet = () => { // typically used regardless of the performance difference if we want this to always refer to surrounding class without needing bind

greet = function() {

Creating a property using the constructor would also create it on the object newly every time:

  constructor() {
    this.age = 30;
    this.greet = function() { ... }
  }
![[Pasted image 20250903153309.png]]