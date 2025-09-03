let a = 0b11111111; // binary form of 255
let b = 0o377; // octal form of 255
let c = 0xff; // hex 255 (case insensitive

let billion = 1000000000;
let billion = 1_000_000_000;

let billion = 1e9;  // 1 billion, literally: 1 and 9 zeroes

In other words, e multiplies the number by 1 with the given zeroes count.
 
1e3 === 1 * 1000; // e3 means *1000
1.23e6 === 1.23 * 1000000; // e6 means *1000000
 
let mсs = 0.000001;

Just like before, using "e" can help. If we’d like to avoid writing the zeroes explicitly, we could write the same as:

let mcs = 1e-6; // five zeroes to the left from 1
 
In other words, a negative number after "e" means a division by 1 with the given number of zeroes:
 
// -3 divides by 1 with 3 zeroes
1e-3 === 1 / 1000; // 0.001
 
// -6 divides by 1 with 6 zeroes
1.23e-6 === 1.23 / 1000000; // 0.00000123
 
// an example with a bigger number
1234e-2 === 1234 / 100; // 12.34, decimal point moves 2 times


toString(base)
 
let num = 255;
 
alert( num.toString(16) );  // ff
alert( num.toString(2) );   // 11111111


Rounding
 
	• Math.floor
		○ Rounds down
		○ 3.1 becomes 3, and -1.1 becomes -2.
	• Math.ceil
		○ Rounds up
		○ 3.1 becomes 4, and -1.1 becomes -1.
	• Math.round
		○ Rounds to the nearest integer
		○ 3.1 becomes 3, 3.6 becomes 4
		○  3.5 rounds up to 4 too.
	• Math.trunc (not supported by Internet Explorer)
		○ Removes anything after the decimal point without rounding
		○ 3.1 becomes 3, -1.1 becomes -1.


Other Math Functions

	• Math.random()
	• Math.max(a, b, c...) and Math.min(a, b, c...)
	• Math.pow(n, power)


Imprecise Calculations

There’s just no way to store exactly 0.1 or exactly 0.2 using the binary system, just like there is no way to store one-third as a decimal fraction.

alert( 0.1.toFixed(20) ); // 0.10000000000000000555

The safest way is to not use decimals at all, for example if using money then count using cents not dollars.

Apart from that, the most reliable method is to round the result with the help of a method toFixed(n):
 
let sum = 0.1 + 0.2; // 0.30000000000000004
alert( sum.toFixed(2) ); // "0.30"

toFixed always returns a string. It ensures that it has 2 digits after the decimal point. That’s actually convenient if we have an e-shopping and need to show $0.30. 
For other cases, we can use the unary plus to coerce it into a number:
 
let sum = 0.1 + 0.2;
alert( +sum.toFixed(2) ); // 0.3


parseInt and parseFloat

Numeric conversion using a plus + or Number() is strict. If a value is not exactly a number, it fails:

alert( +"100px" ); // NaN

For this we can use parseInt and parseFloat:

alert( parseInt('100px') ); // 100
alert( parseFloat('12.5em') ); // 12.5
alert( parseInt('12.3') ); // 12, only the integer part is returned
alert( parseFloat('12.3.4') ); // 12.3, the second point stops the reading

There are situations when parseInt/parseFloat will return NaN. It happens when no digits could be read:
 
alert( parseInt('a123') ); // NaN, the first symbol stops the process

