Transpilers

A transpiler is a special piece of software that translates source code to another source code. It can parse (“read and understand”) modern code and rewrite it using older syntax constructs, so that it’ll also work in outdated engines.

	• Babel is one of the most prominent transpilers out there.

Polyfills

New language features may include not only syntax constructs and operators, but also built-in functions.
 
For example, Math.trunc(n) is a function that “cuts off” the decimal part of a number, e.g Math.trunc(1.23) returns 1.

In some (very outdated) JavaScript engines, there’s no Math.trunc, so such code will fail.

As we’re talking about new functions, not syntax changes, there’s no need to transpile anything here. We just need to declare the missing function.

A script that updates/adds new functions is called “polyfill”. It “fills in” the gap and adds missing implementations.

Two interesting polyfill libraries are:

	• core js that supports a lot, allows to include only needed features.
	• polyfill.io service that provides a script with polyfills, depending on the features and user’s browser.

