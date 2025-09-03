class MyClass {
  // class methods
  constructor() { ... }
  method1() { ... }
  method2() { ... }
  method3() { ... }
  ...
}

Then use new MyClass() to create a new object with all the listed methods.
 
The constructor() method is called automatically by new, so we can initialize the object there, e.g:

class User {
 
  constructor(name) {
    this.name = name;
  }
 
  sayHi() {
    alert(this.name);
  }
 
}
 
// Usage:
let user = new User("John");
user.sayHi();

![[Pasted image 20250903152200.png]]

Class Expression

Just like functions, classes can be defined inside another expression, passed around, returned, assigned, etc.
 
Here’s an example of a class expression:
 
let User = class {
  sayHi() {
    alert("Hello");
  }
};

Named Class Expression

Similar to Named Function Expressions, class expressions may have a name. If a class expression has a name, it’s visible inside the class only:

Note there is no "Named Class Expression" term in the spec, but it is similar to Named Function Expression.

let User = class MyClass {
  sayHi() {
    alert(MyClass); // MyClass name is visible only inside the class
  }
};

new User().sayHi(); // works, shows MyClass definition
alert(MyClass); // error, MyClass name isn't visible outside of the class

Dynamic Classes

We can even make classes dynamically “on-demand”, like this:

function makeClass(phrase) {
  return class {
    sayHi() {
      alert(phrase);
    }
  };
}

let User = makeClass("Hello");
new User().sayHi();
