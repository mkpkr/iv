Old browsers may need a polyfill: Class fields are a recent addition to the language.

class User {
  name = "John";
 
  sayHi() {
    alert(`Hello, ${this.name}!`);
  }
}
 
new User().sayHi(); // Hello, John!


Binding Alternative

As discussed in Function Binding, we also lose this from class methods passed as callbacks such as:

class Button {
  constructor(value) {
    this.value = value;
  }
 
  click() {
    alert(this.value);
  }
}
 
let button = new Button("hello");
 
setTimeout(button.click, 1000); // undefined

Two approaches to fixing this were discussed:

	1. Pass a wrapper-function, such as setTimeout(() => button.click(), 1000).
	2. Bind the method to object, e.g. in the constructor.

Class fields provide another, quite elegant syntax:

class Button {
  constructor(value) {
    this.value = value;
  }

  click = () => {
    alert(this.value);
  }
}
 
let button = new Button("hello");
setTimeout(button.click, 1000); // hello

The class field click = () => {...} is created on a per-object basis, there’s a separate function for each Button object, with this inside it referencing that object. 

We can pass button.click around anywhere, and the value of this will always be correct.
 
That’s especially useful in browser environment, for event listeners.

