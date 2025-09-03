IMPORTANT: this is not bound, value of this is evaluated during the run-time, depending on the context.

![[Pasted image 20250903105437.png]]
Global Functions

const arrow = () => {
  console.log(this);        //1
};
 
const expr = function () {
  console.log(this);       //2
};
 
arrow();
expr();
	
	1. this refers to window object (arrow functions inherit this from the parent scope)
	2. in strict mode this is undefined; in non-strict it is the window object


Methods & Nested Functions

const mike = {
  method: function () {
    console.log(this);              //1
 
    const nested = function () {
      console.log(this);            //2
    };
 
    nested();
  },
 
  arrowMethod: () => {
    console.log(this);              //3
  },
};

When we call mike.method(); and mike.arrowMethod();

	1. this refers to mike object
	2. this is undefined - nested() is just a regular function call
		○ remember this is not about where it is referenced but on what object the function was called
	3. this refers to window object - arrow functions inherit this from the parent scope (aka mike)

Because of 3, it is best practice not to use arrow functions for methods.
 
How do we fix 2?
 
Solution 1: store a refernece to this as self before calling nested()

Solution 2: use an arrow function - it will inherit this from the parent scope (aka mike)
 
const mike = {
  method: function () {
    console.log(this);
 
    // Solution 1 - keep a reference to this
    const self = this; // self or that
    const nested1 = function () {
      console.log(self);
    };
 
    // Solution 2 - use an arrow function
    const nested2 = () => {
      console.log(this);
    };
 
    nested1();
    nested2();
  },


bind, call, apply

We can specify what this should refer to using call(), bind(), apply()
