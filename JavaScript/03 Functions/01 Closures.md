We already know that a function can access variables outside of it (“outer” variables).

But what happens if outer variables change since a function is created? Will the function get newer values or the old ones?

function makeCounter() {
  let count = 0;
 
  return function() {
    return count++;
  };
}
 
let counter = makeCounter();
 
alert( counter() ); // 0
alert( counter() ); // 1
alert( counter() ); // 2


Lexical Environment

In JavaScript, every running function, code block {...}, and the script as a whole have an internal (hidden) associated object known as the Lexical Environment.
 
The Lexical Environment object consists of two parts:
 
	1. Environment Record – an object that stores all local variables as its properties (and some other information like the value of this).
	2. A reference to the outer lexical environment, the one associated with the outer code.

A “variable” is just a property of the special internal object, Environment Record. “To get or change a variable” means “to get or change a property of that object”.

In this simple code without functions, there is only one Lexical Environment:

This is the so-called global Lexical Environment, associated with the whole script.

![[Pasted image 20250903144502.png]]
Note that any function declared as function declaration, will be available in the Lexical Enviornment at the start of the script, function expressions (let/const) will only be available as they are reached.

During the execution of a function, we have an outer Lexical Environment:

![[Pasted image 20250903144510.png]]
When the code wants to access a variable – the inner Lexical Environment is searched first, then the outer one, then the more outer one and so on until the global one.

If a variable is not found anywhere, that’s an error in strict mode (without use strict, an assignment to a non-existing variable creates a new global variable, for compatibility with old code).

Let’s return to the makeCounter example:

![[Pasted image 20250903144517.png]]
What’s different is that, during the execution of makeCounter(), a tiny nested function is created of only one line: return count++. We don’t run it yet, only create.

All functions remember the Lexical Environment in which they were made. Technically, there’s no magic here: all functions have the hidden property named `[[Environment]]`, that keeps the reference to the Lexical Environment where the function was created:

So, counter.`[[Environment]]` has the reference to {count: 0} Lexical Environment. 

That’s how the function remembers where it was created, no matter where it’s called. The `[[Environment]]` reference is set once and forever at function creation time.

Later, when counter() is called, a new Lexical Environment is created for the call, and its outer Lexical Environment reference is taken from counter.`[[Environment]]`:

![[Pasted image 20250903144613.png]]

Now when the code inside counter() looks for count variable, it first searches its own Lexical Environment (empty, as there are no local variables there), then the Lexical Environment of the outer makeCounter() call, where it finds and changes it.

A variable is updated in the Lexical Environment where it lives.

![[Pasted image 20250903144806.png]]

If we call counter() multiple times, the count variable will be increased to 2, 3 and so on, at the same place.

This is known as a closure. A closure is a function that remembers its outer variables and can access them. In some languages, that’s not possible, or a function should be written in a special way to make it happen. But as explained above, in JavaScript, all functions are naturally closures (there is only one exception, to be covered in new Function Syntax).

Optimizations

As we’ve seen, in theory while a function is alive, all outer variables are also retained.
 
But in practice, JavaScript engines try to optimize that. They analyze variable usage and if it’s obvious from the code that an outer variable is not used – it is removed.
 
An important side effect in V8 (Chrome, Edge, Opera) is that such variable will become unavailable in debugging.

