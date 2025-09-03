The "prototype" property is widely used by the core of JavaScript itself. All built-in constructor functions use it.


Object.prototype

let obj = {};
alert( obj ); // "[object Object]" ?

Where’s the code that generates the string "[object Object]"? That’s a built-in toString method, but where is it? The obj is empty!

The short notation obj = {} is the same as obj = new Object(), where Object is a built-in object constructor function, with its own prototype referencing a huge object with toString and other methods.

![[Pasted image 20250903153843.png]]

let obj = {};
 
alert(obj.__proto__ === Object.prototype); // true
 
alert(obj.toString === obj.__proto__.toString); //true
alert(obj.toString === Object.prototype.toString); //true

alert(Object.prototype.__proto__); // null

Other built-in prototypes

Other built-in objects such as Array, Date, Function and others also keep methods in prototypes.

For instance, when we create an array [1, 2, 3], the default new Array() constructor is used internally. So Array.prototype becomes its prototype and provides methods. That’s very memory-efficient.

By specification, all of the built-in prototypes have Object.prototype on the top.

![[Pasted image 20250903153859.png]]

Strings, numbers and booleans are not objects, but if we try to access their properties, temporary wrapper objects are created using built-in constructors String, Number and Boolean. They provide the methods and disappear.
 
These objects are created invisibly to us and most engines optimize them out, but the specification describes it exactly this way. Methods of these objects also reside in prototypes, available as String.prototype, Number.prototype and Boolean.prototype.

In modern programming, there is only one case where modifying native prototypes is approved. That’s polyfilling.

Borrowing from Prototypes

Some methods of native prototypes are often borrowed.
 
For instance, if we’re making an array-like object, we may want to copy some Array methods to it.
  
let obj = {
  0: "Hello",
  1: "world!",
  length: 2,
};
 
obj.join = Array.prototype.join;
 
alert( obj.join(',') ); // Hello,world!

It works because the internal algorithm of the built-in join method only cares about the correct indexes and the length property. It doesn’t check if the object is indeed an array. Many built-in methods are like that.
 
Another possibility is to inherit by setting obj.__proto__ to Array.prototype, so all Array methods are automatically available in obj.
