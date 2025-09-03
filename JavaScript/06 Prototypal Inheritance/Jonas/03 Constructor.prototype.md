In the old times, there was no direct access to it. The only thing that worked reliably was a "prototype" property of the constructor function, described in this chapter. So there are many scripts that still use it.

let animal = {
  eats: true
};
 
function Rabbit(name) {
  this.name = name;
}
 
Rabbit.prototype = animal;
let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal
 
alert( rabbit.eats ); // true

![[Pasted image 20250903153720.png]]

Constructor.prototype property is only used when new Constructor is called, it assigns [[Prototype]] of the new object.

If, after the creation, F.prototype property changes (`F.prototype = <another object>`), then new objects created by new F will have another object as [[Prototype]], but already existing objects keep the old one.

Default prototype & constructor Property

Every function has the "prototype" property even if we don’t supply it.

The default "prototype" is an object with the only property constructor that points back to the function itself.
 
Like this:
 
function Rabbit() {} //default prototype: Rabbit.prototype = { constructor: Rabbit };

alert( Rabbit.prototype.constructor == Rabbit ); // true

![[Pasted image 20250903153738.png]]

let rabbit = new Rabbit(); // inherits from {constructor: Rabbit}

alert(rabbit.constructor == Rabbit); // true (from prototype)

![[Pasted image 20250903153807.png]]

We can use constructor property to create a new object using the same constructor as the existing one:

function Rabbit(name) {
  this.name = name;
  alert(name);
}
 
let rabbit = new Rabbit("White Rabbit");
 
let rabbit2 = new rabbit.constructor("Black Rabbit");


JavaScript itself does not ensure the right "constructor" value.

Yes, it exists in the default "prototype" for functions, but that’s all. What happens with it later – is totally on us.
 
In particular, if we replace the default prototype as a whole, then there will be no "constructor" in it.
 
function Rabbit() {}
Rabbit.prototype = {
  jumps: true
};
 
let rabbit = new Rabbit();
alert(rabbit.constructor === Rabbit); // false

So, to keep the right "constructor" we can choose to add/remove properties to the default "prototype" instead of overwriting it as a whole:
 
function Rabbit() {}
 
// Not overwrite Rabbit.prototype totally just add to it
Rabbit.prototype.jumps = true
// the default Rabbit.prototype.constructor is preserved


Or, alternatively, recreate the constructor property manually:
 
Rabbit.prototype = {
  jumps: true,
  constructor: Rabbit
};
 
// now constructor is also correct, because we added it

