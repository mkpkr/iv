

Classes use prototypal inheritance behind the scenes.

Historically prototyal inheritcance was used by developers to mock OOP concepts, now we have classes so probably won't see much like this in any modern code.

Everything is still based opn prototypes, so you should understand how it works - but you will probably never see or need code that manipulates prototypes.


Constructor Function

Class and constructor are just syntactic sugar, we can create something similar like this:

function Person() {   // constructor function, can call new Person()
  this.age = 30;
  this.name = 'Max';

  this.greet = function() { // method on Person - when using class, this would actually be set on Person.prototype - see Methods
    console.log(
      'Hi, I am ' + this.name + ' and I am ' + this.age + ' years old.'
    );
  };

  //no return value, new keyword will handle that
}


//setting prototype property is similar to what JS does when you use extends keyword; 
//for example we could create a Parrot constructor Function and set the same prototype
//note that Person.protoype is not Person's prototype (badly named similar terms); 
//Person's prototype could be accessed through Person.__proto__ (every object has __proto__ to get its own prototype and functions are objects)
//Person.prototype will actually be the prototype of objects created with new Person(), and those object's __proto__ will be Person.prototype
Person.prototype = {   
  
  //by default the prototype will create a constructor property that will point to Person function, see below

  sayHello() {   // method on the prototype of any object created from new Person()
                 // methods should be created on an objects prototype to save memory, which is what JS does, see Methods
    console.log('Hi, I am ' +  this.name);
  }
};

// a better way to do this, instead of overwriting Person.prototype, we could add a property
// in this case (if we hadn't overridden Person.prototype) the constructor property would be left in tact
Person.prototype.printAge = function() {
  console.log(this.age);
}



const p = new Person();
p.greet();
p.sayHello();
p.printAge();

console.log(p.__proto__); // p.__proto__ is derived from Person.prototype
console.log(p.__proto__.__proto__); // prototypes have their own prototypes



prototype object is the fallback object, javascript will search the prototype if it can't find the property on the object it is searching

![[Pasted image 20250903153212.png]]

![[Pasted image 20250903153227.png]]