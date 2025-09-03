19:30

Template String

We can use backticks:

const name = "Mike";
const year = 2020;
const birthYear = 1988;

const output = `Hello, I'm ${name} and I'm ${year - birthYear} years old!`;

Expressions

Backticks, allow us to embed any expression:

function sum(a, b) {
  return a + b;
}
 
alert(`1 + 2 = ${sum(1, 2)}.`); // 1 + 2 = 3.

Multiline Strings

Backticks (template strings) allow us to capture new lines in our strings:

console.log(`line 1
line 2
line 3`);

This will output three separate lines and is useful when we're generating HTML from JS.

Length

str.length; //property

Accessing Characters

// the first character
alert( str[0] );
alert( str.at(0) );
 
// the last character
alert( str[str.length - 1] );
alert( str.at(-1) );

//iterate
for (let char of "Hello") {
  alert(char);
}

Other

	• slice()
	• substring()
		○ Almost the same as slice, but it allows start to be greater than end (in this case it simply swaps start and end values).
		○ Negatives are not supported in substring
	• substr()
		○ may not be supported in some non-browser environments

Best to just stick with slice

![[Pasted image 20250903142209.png]]

	• indexOf()
	• lastIndexOf()
	• toUpperCase()
	• toLowerCase()
	• includes()
	• startsWith()
- endsWith()