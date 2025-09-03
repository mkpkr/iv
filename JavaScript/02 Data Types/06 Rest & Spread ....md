Rest ...

Javascript can accept any number of arguments to a function, which can be accessed with "arguments":

function showName() {
  alert( arguments.length );
  alert( arguments[0] );
  alert( arguments[1] );
}

Although arguments is both array-like and iterable, it’s not an array. It does not support array methods. 

Also note Arrow functions do not have their own "arguments".


We should use rest paramters (varargs):

function sumAll(arg1, ...args) {

Rest parameter must be the last parameter and must be maximum of 1 rest parameter.

Spread …

Spread does the reverse of rest, turning an array into an argument list:

let arr = [3, 5, 1];

alert( Math.max(arr) ); // NaN
alert( Math.max(...arr) ); // 5


We also can pass multiple iterables this way:
 
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];
 
alert( Math.max(...arr1, ...arr2) ); // 8


We can even combine the spread syntax with normal values:
 
let arr1 = [1, -2, 3, 4];
let arr2 = [8, 3, -8, 1];
 
alert( Math.max(1, ...arr1, 2, ...arr2, 25) ); // 25


Also, the spread syntax can be used to merge arrays:
 
let arr = [3, 5, 1];
let arr2 = [8, 9, 15];
 
let merged = [0, ...arr, 2, ...arr2];
 
alert(merged); // 0,3,5,1,2,8,9,15

Other Iterables

In the examples above we used an array to demonstrate the spread syntax, but any iterable will do.
 
let str = "Hello";

alert( [...str] ); // H,e,l,l,o


Although for this we could use Array.from, because it converts an iterable (like a string) into an array:

alert( Array.from(str) ); // H,e,l,l,o

	• Array.from operates on both array-likes and iterables so it is more universal
	• The spread syntax works only with iterables.

Copy Array/Object

Instead of Objects.assign we can use spread syntax, which is more concise:

let arr = [1, 2, 3];
let arrCopy = [...arr];

alert(JSON.stringify(arr) === JSON.stringify(arrCopy)); // true
alert(arr === arrCopy); // false (not same reference)


let obj = { a: 1, b: 2, c: 3 };
let objCopy = { ...obj };

alert(JSON.stringify(obj) === JSON.stringify(objCopy)); // true
alert(obj === objCopy); // false (not same reference)


Note this is a shallow copy. For deep copy we would need to use map:

const copiedPersons = persons.map(person => ({
  name: person.name,
  age: person.age,
  address: person.address
  purchases: [...person.purchases]
}));

Why does he map on the object and not do a spread/map like in Deep Copy Array which was taken from React course.
