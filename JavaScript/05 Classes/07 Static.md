17:11

Static Methods

The value of this in User.staticMethod() call is the class constructor User itself.

class User {

  static someStaticMethod() {
    alert(this === User);
  }
}

User.someStaticMethod(); // true


//alter

class User { }
 
User.someStaticMethod= function() {
  alert(this === User);
};

User.someStaticMethod(); // true


Static methods are callable on classes, not on individual objects.

obj.staticMethod(); // Error: obj.staticMethodis not a function


Static Properties

This is a recent addition to the language. Examples work in the recent Chrome.

class Article {
  static publisher = "Ilya Kantor";
}
 
alert( Article.publisher );


Other Example of Static Property and Methods

class App {
  static cart;

  static init() {
    const shop = new Shop();
    shop.render();
    this.cart = shop.cart;
  }

  static addProductToCart(product) {
    this.cart.addProduct(product);
  }
}

App.init();


Inheritance

Static properties and methods are inherited.

Animal.staticSomething() will be accessible as Rabbit.staticSomething()

extends gives Rabbit the [[Prototype]] reference to Animal.
 
So, Rabbit extends Animal creates two [[Prototype]] references:
 
	1. Rabbit function prototypally inherits from Animal function.
	2. Rabbit.prototype prototypally inherits from Animal.prototype.

![[Pasted image 20250903152618.png]]

class Animal {}
class Rabbit extends Animal {}
 
// for statics
alert(Rabbit.__proto__ === Animal); // true
 
// for regular methods
alert(Rabbit.prototype.__proto__ === Animal.prototype); // true

But I thought setting Constructor.prototype would set the [[Prototype]] property; or is it different rules when using extends vs using regular functions and objects described in Constructor.prototype ?

No Static Inheritance in Built-ins

Built-in objects have their own static methods, for instance Object.keys, Array.isArray etc.
 
And as we already know, native classes extend each other. For instance, Array extends Object.
 
Normally, when one class extends another, both static and non-static methods are inherited.

But built-in classes are an exception. They don’t inherit statics from each other.
 
For example, both Array and Date inherit from Object, so their instances have methods from Object.prototype. 

But Array.[[Prototype]] does not reference Object like is shown in the diagram in previous heading, so there’s no, for instance, Array.keys() (or Date.keys()) static method.
 
Here’s the picture structure for Date and Object:


![[Pasted image 20250903152640.png]]

As you can see, there’s no link between Date and Object like we get with extends (as explained in previous heading).

With built-ins, only e.g. Date.prototype inherits from Object.prototype.

That’s an important difference of inheritance between built-in objects compared to what we get with extends.