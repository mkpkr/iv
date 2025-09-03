Internally var is a very different beast to let and const, that originates from very old times. It’s generally not used in modern scripts, but still lurks in the old ones.
 
“var” has no block scope

Variables declared with var are either function-scoped or global-scoped. 

Global Scope

if (true) {
  var test = true;
}
 
alert(test); // true, the variable lives after if

The same thing for loops: var cannot be block- or loop-local:
 
for (var i = 0; i < 10; i++) {
  var one = 1;
  // ...
}
 
alert(i);   // 10, "i" is visible after loop, it's a global variable
alert(one); // 1, "one" is visible after loop, it's a global variable

Function Scope

If a code block is inside a function, then var becomes a function-level variable:
 
function sayHi() {
  if (true) {
    var phrase = "Hello";
  }
 
  alert(phrase); // works
}
 
sayHi();
alert(phrase); // ReferenceError: phrase is not defined
 

“var” tolerates redeclarations

var user = "Pete";
var user = "John";  //not an error like with let or const
 
alert(user); // John


“var” variables can be declared below their use

All var are “hoisted” (raised) to the top of the function or script for globals.

So this code works:
 
function sayHi() {
  phrase = "Hello";
 
  alert(phrase);
 
  var phrase;
}

And even this (remember, code blocks are ignored):
 
function sayHi() {
  phrase = "Hello";
 
  if (false) {
    var phrase;
  }
 
  alert(phrase);
}
sayHi();

 
if (false) branch never executes, but that doesn’t matter. 
The var inside it is processed and exists in the beginning of the function.
 
Declarations are hoisted, but assignments are not.

Because all var declarations are processed at the function start, we can reference them at any place. But variables are undefined until the assignments.

function sayHi() {
  alert(phrase); //undefined
 
  var phrase = "Hello";
}
 
sayHi();

The declaration is processed at the start of function execution (“hoisted”), but the assignment always works at the place where it appears. 

alert runs without an error, because the variable phrase exists. But its value is not yet assigned, so it shows undefined.
 
IIFE - Immediately-Invoked Function Expressions

In the past, as there was only var, and it has no block-level visibility, programmers invented a way to emulate it. What they did was called “immediately-invoked function expressions”.
 
That’s not something we should use nowadays, but you can find them in old scripts.
 
(function() {
 
  var message = "Hello";
 
  alert(message);
 
})();

Here, a Function Expression is created and immediately called. 

So the code executes right away and has its own private variables.
 
The Function Expression is wrapped with parenthesis (function {...}), because when JavaScript engine encounters "function" in the main code, it understands it as the start of a Function Declaration, so, the parentheses around the function is a trick to show JavaScript that the function is created in the context of another expression, and hence it’s a Function Expression: it needs no name and can be called immediately.
 
There exist other ways besides parentheses to tell JavaScript that we mean a Function Expression...
 
Ways to Create IIFE
 
(function() {
  alert("Parentheses around the function");
})();
 
(function() {
  alert("Parentheses around the whole thing");
}());
 
!function() {
  alert("Bitwise NOT operator starts the expression");
}();
 
+function() {
  alert("Unary plus starts the expression");
}();
