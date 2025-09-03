19:00

Create Array

let myArr = [];
let friends = ['Michael', 'Steven', 'Peter'];
 
//alt syntax - best to just use []
let years = new Array(1991, 1992, 1993); //be careful with passing a single number - see "Create an Array with Given Length" below
let years = Array.of(1991, 1992, 1993);

//accessing elements:
console.log(friends[0]);
console.log(friends.length);

Arrays do not need to contain the same type:
 
const someArray = ['Mike', 184, someOtherArray];
let arr = [ 'Apple', { name: 'John' }, true, function() { alert('hello'); } ];

Note if you set an element higher than the size, it will be set, and the in between elements will be undefined.

const vals = ["s", "c"];
vals[70] = "r";
console.log(vals); // (71) ['s', 'c', empty × 68, 'r']

Create Array with Given Length

let arr = new Array(2); //does not create array with one element (2), creates empty array with length 2

Clone an Array

Use Spread


From

const split = Array.from('Hi!''); //["H", "i", "!"]

This becomes more useful when you have an array-like object like a NodeList and need a real array (array methods don't exist on array-like objects):

const listItems = document.querySelectorAll('li');
const listItemsArr = Array.from(listItems);

Iterating

Do not use for..in - some "array-like" objects also have extra properties that will be picked up with for..in

Use for..of for iterables like arrays.

for (const fruit of fruits) {

Can also use the classic style:

for (let i = 0; i < arr.length; i++) {

And forEach:

arr.forEach(function(item, index, array) { 
};

//can also use an arrow function
arr.forEach((item, index, array) => { ... };

Remember with JS lambdas we can omit arguments e.g forEach((item) => { … }); or rename forEach((el, i) => { … });

Deleting Elements - do not use delete

let arr = ["I", "go", "home"];
delete arr[1]; // remove "go"
alert( arr[1] ); // undefined

// now arr = ["I",  , "home"];
alert( arr.length ); // 3

The element was removed, but the array still has 3 elements, we can see that arr.length == 3.

delete obj.key removes a value by the key. It’s all it does. Fine for objects. But for arrays we usually want the rest of elements to shift and occupy the freed place. We expect to have a shorter array now.

So, special methods should be used. See "Splice"...

Array Methods

https://javascript.info/array-methods

Note that most (all?) methods allow specifying negative values so -3 would specify 3 elements from the end of the array.

	• end of array
		○ push
		○ pop
	• beginning of array – BAD PERFORMANCE (see below)
		○ unshift (add element)
		○ shift (remove element)
	• splice
	• slice
	• forEach
	• indexOf/lastIndexOf     // useful for primitives, use find/findIndex/findLastIndex() for complex objects
	• includes                // same - use for primitives 
	• find                    // find((item, index, array) => { … });   // remember we can omit arguments e.g find((item) => { … });
	• findIndex/findLastIndex
	• filter
	• map
	• reduce                 // reduce((accumulator, item, index, array) => {…}, [initial]);
	• sort
	• concat

push/pop/shift/unshift

const friends = ['Michael', 'Steven', 'Peter'];
 
//add to the end
friends.push('Jay');
 
//add to the beginning
friends.unshift('John');
 
//remove last
const removedElement = friends.pop();
 
//remove first
const removedElement = friends.shift();
 
//get index of (int)
friends.indexOf('Steven');
 
//includes (bool)
friends.includes('Steven');


splice

The arr.splice method is a swiss army knife for arrays. It can do everything: insert, remove and replace elements.

arr.splice(start[, deleteCount, elem1, ..., elemN])

	• It modifies arr starting from the index start
	• removes deleteCount elements
	•  then inserts elem1, ..., elemN at their place. 
	• It returns the array of removed elements.

Delete

let arr = ["I", "study", "JavaScript"];

arr.splice(1, 1); // from index 1 remove 1 element

alert( arr ); // ["I", "JavaScript"]

Replace

let arr = ["I", "study", "JavaScript", "right", "now"];

// remove 3 first elements and replace them with another 2
arr.splice(0, 3, "Let's", "dance");

alert( arr ) // now ["Let's", "dance", "right", "now"]

Insert

let arr = ["I", "study", "JavaScript"];
 
// from index 2
// delete 0 elements
// then insert "complex" and "language"
arr.splice(2, 0, "complex", "language");
 
alert( arr ); // "I", "study", "complex", "language", "JavaScript"

Negative Indexes

Here and in other array methods, negative indexes are allowed. They specify the position from the end of the array:

let arr = [1, 2, 5];
 
// from index -1 (one step from the end)
// delete 0 elements,
// then insert 3 and 4
arr.splice(-1, 0, 3, 4);
 
alert( arr ); // 1,2,3,4,5



slice

Select a range:

arr.slice([start inclusive], [end exclusive]) //negatives allowed to specify backwards from end e.g. (-3, -1)

Copy an array

const arrClone = arr.slice()

filter

let results = arr.filter(function(item, index, array) {
  // if true item is pushed to results and the iteration continues
  // returns empty array if nothing found
});

let someUsers = users.filter(item => item.id < 3);

map

let result = arr.map(function(item, index, array) {
  // returns the new value instead of item
});
 
let lengths = ["Bilbo", "Gandalf", "Nazgul"]
             .map(item => item.length);
alert(lengths); // 5,7,6

sort

arr.sort();

arr.sort(function (a, b) {
   //if (a > b) return 1;
   //if (a == b) return 0;
   //if (a < b) return -1;
});
 
arr.sort( (a, b) => a - b );

reduce

const sum = prices.reduce((accumulatedValue, curValue) => accumulatedValue + curValue, 0);

map + reduce

const sum = originalArray
               .map(obj => obj.price)
               .reduce((sumVal, curVal) => sumVal + curVal, 0); // => 46.97

# Array as Stack

![[Pasted image 20250903142527.png]]

# Array as Queue

Don't do this – implement a querue as linked list instead

![[Pasted image 20250903142544.png]]
# Performance

Methods push/pop run fast, while shift/unshift are slow.

![[Pasted image 20250903142629.png]]