Setting or reading the prototype with obj.__proto__ is considered outdated and somewhat deprecated (moved to the so-called “Annex B” of the JavaScript standard, meant for browsers only).
 
The modern methods to get/set a prototype are:
 
	• Object.getPrototypeOf(obj) – returns the [[Prototype]] of obj.
	• Object.setPrototypeOf(obj, proto) – sets the [[Prototype]] of obj to proto.

The only usage of __proto__, that’s not frowned upon, is as a property when creating a new object: { __proto__: ... }.

Although, there’s a special method for this too:

	• Object.create(proto, [descriptors]) – creates an empty object with given proto as [[Prototype]] and optional property descriptors.

Object.create has an optional second argument: property descriptors.

let animal = {
  eats: true
};
 
let rabbit = Object.create(animal, {
  jumps: {
    value: true
  }
});
 
alert(rabbit.jumps); // true

The descriptors are in the same format as described in the Property flags and descriptors.

We can use Object.create to perform an object cloning more powerful than copying properties in for..in:
 
let clone = Object.create(
  Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj)
);
