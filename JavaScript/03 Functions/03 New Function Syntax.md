let func = new Function ([arg1, arg2, ...argN], functionBody);

e.g.

let sum = new Function('a', 'b', 'return a + b');

new Function allows to turn any string into a function; for example, we can receive a new function from a server and then execute it.

Closures

Usually, a function remembers where it was born in the special property [[Environment]]. It references the Lexical Environment from where it’s created..
 
But when a function is created using new Function, its [[Environment]] is set to reference not the current Lexical Environment, but the global one.
 
So, such function doesn’t have access to outer variables, only to the global ones.