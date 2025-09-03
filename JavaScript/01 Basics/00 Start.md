index.html

/assets
   /images
   /scripts
   /styles


# Script Placement

Note `<script>` requires closing `</script>`, can't use` <script />`

![[Pasted image 20250903104135.png]]

When you place your JavaScript links at the bottom of your HTML body, it gives the HTML time to load before any of the JavaScript loads, which can prevent errors, and speed up website response time:
 
```js
<script src="script.js"></script>
</body>
```

But this means the browser notices the script (and can start downloading it) only after it downloaded the full HTML document. 

For long HTML documents, that may be a noticeable delay. Such things are invisible for people using very fast connections, but many people in the world still have slow internet speeds and use a far-from-perfect mobile internet connection.

defer

Load in background; execute after HTML is parsed.

<script defer src="https://javascript.info/article/script-async-defer/long.js?speed=1"></script>
 
The defer attribute tells the browser not to wait for the script. Instead, the browser will continue to process the HTML, build DOM. 

The script loads “in the background”, and then runs when the DOM is fully built; when you simply put scripts at the end of the HTML file, they don't even begin loading until the HTML is parsed.
  
	• Scripts with defer never block the page.
	• Scripts with defer always execute when the DOM is ready (but before DOMContentLoaded event).
	• Deferred scripts keep their relative order, just like regular scripts.
	• The defer attribute is only for external scripts.
		○ The defer attribute is ignored if the <script> tag has no src.
 
async

Load in background; execute once loaded, regardless of HTML parsing status or other async script status.

The async attribute is somewhat like defer. It also makes the script non-blocking. But it has important differences in the behavior. 

The async attribute means that a script is completely independent:
 
	• The browser doesn’t block on async scripts (like defer).
	• Other scripts don’t wait for async scripts, and async scripts don’t wait for them.
	• DOMContentLoaded and async scripts don’t wait for each other:
		○ DOMContentLoaded may happen both before an async script (if an async script finishes loading after the page is complete)
		○ …or after an async script (if an async script is short or was in HTTP-cache)

In other words, async scripts load in the background and run when ready. The DOM and other scripts don’t wait for them, and they don’t wait for anything.

Async scripts are great when we integrate an independent third-party script into the page: counters, ads and so on, as they don’t depend on our scripts, and our scripts shouldn’t wait for them.


Strict Mode

For a long time, JavaScript evolved without compatibility issues. New features were added to the language while old functionality didn’t change. That had the benefit of never breaking existing code. But the downside was that any mistake or an imperfect decision made by JavaScript’s creators got stuck in the language forever.
 
This was the case until 2009 when ECMAScript 5 (ES5) appeared. It added new features to the language and modified some of the existing ones. 

To keep the old code working, most such modifications are off by default. You need to explicitly enable them with a special directive added to the first line:

'use strict';

	• Forbids using reserved words that may be used in future (private; interface).
	• Creates visible errors instead of failing silently.
	• Strict mode prevents us from assigning a variable that hasn't been declared.
 
Modern JavaScript supports “classes” and “modules” – advanced language structures, that enable use strict automatically. So we don’t need to add the "use strict" directive, if we use them.

So, for now "use strict"; is a welcome guest at the top of your scripts. Later, when your code is all in classes and modules, you may omit it.


Declaring Variables (let and const)

let and const provide block scope.
 
Before ES2015, JavaScript had var which, when declared in a function, would provide function scope (variable accessible within the function even if declared in an if statement or other block within that function).

At global scope when you use var, it creates a variable on window object, let and const do not.
 
var should be avoided.
 
Problems with var are made more clear due to hoisting rules. We may not realise a variable hasn't been declared yet, we can still use it but with var its value us undefined; with let and const we get an error when we use it.
 
Note if you omit let/const/var then the variable is declared at global scope - this should also be avoided.


Data Types

Every value in JavaScript is either an object or a primitive. Primitives can be:

	• Number (floating point)
	• String
	• Boolean
	• Undefined
	• Null
	• Symbol (ES2015+)
	• BigInt (ES2020+) (Not supported in IE yet)

Variables are dynamically typed - values have a type, variables do not.

typeof gives the type of a value stored in a variable.

typeof null will return object because of a bug that was never corrected.


Interaction

alert("Hello");

result = prompt(title, [default]); //In IE: always supply a default like '' otherwise it will display undefined

result = confirm(question);


Conversion / Coercion

Manual Conversion

const year = '1991';
console.log(Number(year));

String(23);

Boolean(''); //see # Truthy/Falsy

We can also use methods like parseInt, parseFloat

Automatic Conversion (Coercion)

	• + 
		○ triggers conversion to string when string involved
	• - * / 
		○ triggers string to int

alert( "6" / "2" ); // 3
'I am ' + 23 + ' years old'; //'I am 23 years old'
'23' + '10' + 3; //'23103'
'23' - '10' - 3; //10
'23' * '2'; //46
2 + 3 + 4 + '5';//95
if('something') //true

Gotcha

alert( 0 == '' ); // true, as '' becomes converted to number 0
alert('0' == '' ); // false, no type conversion, different strings

for

for(let i = 1; i <= 10; i++) {
    console.log(`On repetition ${i}`);
}

Labels to break

outer: for (let i = 0; i < 3; i++) {
 
  for (let j = 0; j < 3; j++) {
 
    let input = prompt(`Value at coords (${i},${j})`, '');
 
    // if an empty string or canceled, then break out of both loops
    if (!input) break outer;
 
    // do something with the value...
  }
}
 
alert('Done!');


while

let rep = 1;
while(rep <=10) {
    //...
    rep++;
}
