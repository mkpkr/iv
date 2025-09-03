There’s no conversion to boolean. All objects are true in a boolean context, as simple as that. 

There exist only numeric and string conversions. The result of an operation is always a primitive, not another object.

	• The numeric conversion happens when we subtract objects or apply mathematical functions. For instance, Date objects (to be covered in the chapter Date and time) can be subtracted, and the result of date1 - date2 is the time difference between two dates.
	• The string conversion usually happens when we output an object with alert(obj) and in similar contexts.

How does JavaScript decide which conversion to apply?

There are three variants of type conversion, that happen in various situations. They’re called “hints”:

	• "string"
		○ For an object-to-string conversion, when we’re doing an operation on an object that expects a string, like alert.
	• "number"
		○ For an object-to-number conversion, like when we’re doing maths.
	• "default"
		○ Occurs in rare cases when the operator is “not sure” what type to expect.
		○ For instance, binary plus + can work both with strings (concatenates them) and numbers (adds them). 

To do the conversion, JavaScript tries to find and call three object methods:
 
	1. Call obj[Symbol.toPrimitive](hint) – the method with the symbolic key Symbol.toPrimitive (system symbol), if such method exists,
	2. Otherwise if hint is "string"
		○ try calling obj.toString() or obj.valueOf(), whatever exists.
	3. Otherwise if hint is "number" or "default"
		○ try calling obj.valueOf() or obj.toString(), whatever exists.


Symbol.toPrimitive

There’s a built-in symbol named Symbol.toPrimitive that should be used to name the conversion method:
 
obj[Symbol.toPrimitive] = function(hint) {
  // here goes the code to convert this object to a primitive
  // it must return a primitive value
  // hint = one of "string", "number", "default"
};

A conversion can contain any primitive type, but it must be a primitive.

let user = {
  name: "John",
  money: 1000,
 
  // for hint="string"
  toString() {
    return `{name: "${this.name}"}`;
  },
 
  // for hint="number" or "default"
  valueOf() {
    return this.money;
  }
 
};



