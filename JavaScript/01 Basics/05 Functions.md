Default Values

If value not provided, or value is undefined, use default

function showMessage(from, text = "no text given") {

function showMessage(from, text = anotherFunction()) { //anotherFunction() only executed if no text given

If we want to assign a default value later in the function execution:

if (text === undefined) { // if the parameter is missing
  text = 'empty message';
}

Note that we can have default values at any position, they do not have to be the last parameters. For example parameter 1 can have a default even if parameter 2 doesn't. If you pass a single parameter it will go to the 1st (even though there is a default) and the 2nd will be undefined.

Function Declarations vs Expressions

//declaration
function calcAge(birthYear) {
    
}

//expression
const calcAge = function(birthYear) {
    
}; //note the semi colon at the end

	• A Function Expression is created when the execution reaches it and is usable only from that moment.
	• A Function Declaration can be called earlier than it is defined due to hoisting.
	• In strict mode, when a Function Declaration is within a code block, it’s visible everywhere inside that block. But not outside of it.

As a rule of thumb, when we need to declare a function, the first thing to consider is Function Declaration syntax. It gives more freedom in how to organize our code, because we can call such functions before they are declared.
 
That’s also better for readability, as it’s easier to look up function f(…) {…} in the code than let f = function(…) {…};. Function Declarations are more “eye-catching”.

But if a Function Declaration does not suit us for some reason, or we need a conditional declaration, then Function Expression should be used.


Arrow Function

//one line arrow function
const calcAge = birthYear => currentYear - birthYear;
let sum = (a, b) => a + b;
let sayHi = () => alert("Hello");

//multi line arrow function
const yearsUntilRetirement = birthYear => {
    const age = currentYear - birthYear; 
    const retiurementAge = 65 - age;
    return retirement;
}

Arrow functions do not get their own this keyword. See this for more info. This allows us to retain the current context which is typically what we want with an arrow function.

arguments

We are free to call a function with as many arguments as we want, not just those delcared as parameters - although this is not recommented
 
arguments keyword is available to get all arguments passed, but only available in regular functions (expressions and declarations), not arrow functions
 
const addExpr = function (a, b) {
  console.log(arguments);    //prints array of 2,5,8,12
  return a + b;
};
addExpr(2, 5, 8, 12);


var addArrow = (a, b) => {
  console.log(arguments);    //error - arguments is not defined
  return a + b;
};
addArrow(2, 5, 8);


Hoisting

![[Pasted image 20250903105038.png]]