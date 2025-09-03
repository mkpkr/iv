Named Function Expression, or NFE, is a term for Function Expressions that have a name.

let sayHi = function func(who) {

This name func:

	1. It allows the function to reference itself internally.
	2. It is not visible outside of the function.

If we write a recursive function, the problem with that code is that sayHi may change in the outer code. If the function gets assigned to another variable instead, the code will start to give errors.

That is not true of the name func:

let sayHi = function func(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
    func("Guest"); //if we used sayHi, we get "Error: sayHi is not a function"
  }
};
 
let welcome = sayHi;
sayHi = null;
 
welcome(); // Hello, Guest (nested call works)
