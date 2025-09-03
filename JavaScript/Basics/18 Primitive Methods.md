Primitives allow you to call methods by first converting to an object wrapper (String, Number, Boolean, Symbol and BigInt) which is then disposed.

The JavaScript engine highly optimizes this process. It may even skip the creation of the extra object at all. But it must still adhere to the specification and behave as if it creates one.

let str = "Hello";
alert( str.toUpperCase() ); // HELLO

let n = 1.23456;
alert( n.toFixed(2) ); // 1.23

Do not use the wrapper constrcutors directly – these are meant for internal use only, but for historic reasons they are still available to use.
