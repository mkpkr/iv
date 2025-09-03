let animal = {
  eats: true
};

let rabbit = {
  jumps: true
  __proto__: animal; // sets rabbit.[[Prototype]] = animal
};

// we can find both properties in rabbit now: 
alert( rabbit.eats ); // true
alert( rabbit.jumps ); // true


![[Pasted image 20250903153533.png]]

Please note that __proto__ is not the same as the internal `[[Prototype]]` property. It’s a getter/setter for `[[Prototype]]`.