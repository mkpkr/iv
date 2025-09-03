getElementById

	• document.getElementById('some-id');

Note that an element's id is also a global variable – (although in the case above there is a hyphen so it can't be a variable name but can be accessed using window['some-id'])
It is highly recommended that you use getElementById and don't access the variable directly, which could result in conflicts with other variable names. 


querySelector / querySelectorAll

	• document.querySelector(css)
	• document.querySelectorAll(css)
		○ returns a static collection (not live)

querySelector gets the first matching element

//get element
document.querySelector('.message'); //<p class="message">Start guessing...</p>

//get text
document.querySelector('.message').textContent; //Start guessing...

//set text
document.querySelector('.message').textContent = 'Correct Number';
 
//get value from input
document.querySelector('.guess').value;

querySelectorAll gets all matching elements

//select all of same class
document.querySelectorAll('.some-class');


Any CSS Expression can be used

document.querySelectorAll('ul > li:last-child');

It's Not Just document

const myElem = document.getElementById('some-id');
myElem.querySelectorAll('ul > li');

matches

	• element.matches(css)
		○ Checks if element matches the given CSS-selector. It returns true or false

closest

	• element.closest(css)
		○ Looks for the nearest ancestor that matches the CSS-selector. 
		○ The element itself is also included in the search.
 
getElementsBy* - old methods
 
Today, they are mostly history, as querySelector is more powerful and shorter to write.
 
However you can still find them in the old scripts.
 
	• elem.getElementsByTagName(tag) 
		○ looks for elements with the given tag and returns the collection of them. The tag parameter can also be a star "*" for “any tags”.
	• elem.getElementsByClassName(className) 
		○ returns elements that have the given CSS class.
	• document.getElementsByName(name)
		○ returns elements with the given name attribute, document-wide. 
		○ Very rarely used.

All methods "getElementsBy*" return a live collection. Such collections always reflect the current state of the document and “auto-update” when it changes.

In contrast, querySelectorAll returns a static collection. It’s like a fixed array of elements.

