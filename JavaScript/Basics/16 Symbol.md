By specification, only two primitive types may serve as object property keys:
	• string type
	• symbol type

Otherwise, if one uses another type, such as number, it’s autoconverted to string. So that obj[1] is the same as obj["1"], and obj[true] is the same as obj["true"].

A symbol is a “primitive unique value” with an optional description
It represents a unique identifier.
 
A value of this type can be created using Symbol():
 
let id = Symbol();
 
Upon creation, we can give symbols a description (also called a symbol name), mostly useful for debugging purposes:

let id = Symbol("id");
 
Symbols don’t auto-convert to a string, need to use toString().

Hidden Properties
 
Symbols allow us to create “hidden” properties of an object, that no other part of code can accidentally access or overwrite.

Third-party code won’t be aware of newly defined symbols, so it’s safe to add symbols to the user objects. Symbols are skipped by for…in.
 
let user = { // belongs to another code
  name: "John"
};

let id = Symbol("id");
 
user[id] = 1;
alert( user[id] ); // we can access the data using the symbol as the key
 
Imagine that another script wants to have its own identifier inside user, for its own purposes.
 
Then that script can create its own Symbol("id"), like this:
 
// ...
let id = Symbol("id");
user[id] = "Their id value";
 
There will be no conflict between our and their identifiers, because symbols are always different, even if they have the same name.


Symbols in an object literal

If we want to use a symbol in an object literal {...}, we need square brackets around it.
 
let id = Symbol("id");
 
let user = {
  name: "John",
  [id]: 123 // not "id": 123
};


Global Symbols

As we’ve seen, usually all symbols are different, even if they have the same name. But sometimes we want same-named symbols to be same entities. For instance, different parts of our application want to access symbol "id" meaning exactly the same property.
 
// read from the global registry
let id = Symbol.for("id"); // if the symbol did not exist, it is created
 
// read it again (maybe from another part of the code)
let idAgain = Symbol.for("id");
 
// the same symbol
alert( id === idAgain ); // true




