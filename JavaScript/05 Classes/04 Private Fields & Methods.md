Old browsers may need a polyfill: Privates are a recent addition to the language.

class CoffeeMachine {
  #waterLimit = 200;
 
  #fixWaterAmount(value) {
    if (value < 0) return 0;
    if (value > this.#waterLimit) return this.#waterLimit;
  }
 
  setWaterAmount(value) {
    this.#waterLimit = this.#fixWaterAmount(value);
  }
 
}
 
let coffeeMachine = new CoffeeMachine();
 
// can't access privates from outside of the class
coffeeMachine.#fixWaterAmount(123); // Error
coffeeMachine.#waterLimit = 1000; // Error

Private fields do not conflict with public ones. We can have both private #waterAmount and public waterAmount fields at the same time.



Legacy Code - Pseudo Privates

The addition of private fields and properties is relatively new - in the past, such a feature was not part of JavaScript.

Hence you might find many scripts that use a concept which you could describe as "pseudo-private" properties.
It would look like this:

class User {
    constructor() {
        this._role = 'admin';
    }
}
 
// or directly in an object
 
const product = {
    _internalId: 'abc1'
};

It's a quite common convention to prefix private properties with an underscore (_) to signal that they should not be accessed from outside of the object.
Important: It's just a convention that should signal something! It does NOT technically prevent access. 
