Note protected properties are usually prefixed with an underscore _.

That is not enforced on the language level, but there’s a well-known convention between programmers that such properties and methods should not be accessed from the outside.

For language enforced privates, see Private Fields  Methods

Get/Set Methods

This is typically preferred.

class CoffeeMachine {
  _waterAmount = 0; //need new browser or polyfill for Class Fields
 
  setWaterAmount(value) {
    if (value < 0) value = 0;
    this._waterAmount = value;
  }

  getWaterAmount() {
    return this._waterAmount;
  }
}

new CoffeeMachine().setWaterAmount(100);


Getters/Setters (Language Feature)

obj.variable; //this will call the getter
obj.variable = value; //this will call the setter

Example:

class User {
 
  constructor(name) {
    // invokes the setter
    this.name = name;
  }
 
  get name() {
    return this._name;
  }
 
  set name(value) {
    if (value.length < 4) {
      alert("Name is too short.");
      return;
    }
    this._name = value;
  }
 
}
 
let user = new User("John");
alert(user.name); // John
 
user = new User(""); // Name is too short.
