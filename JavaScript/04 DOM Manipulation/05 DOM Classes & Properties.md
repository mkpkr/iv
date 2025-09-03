![[Pasted image 20250903145619.png]]


	• EventTarget – is the root abstract class for everything.
		○ Objects of that class are never created. It serves as a base, so that all DOM nodes support events.
	• Node – is also an abstract class, serving as a base for DOM nodes.
		○ It provides the core tree functionality: parentNode, nextSibling, childNodes and so on (they are getters).
	• Document, for historical reasons often inherited by HTMLDocument (though the latest spec doesn’t dictate it) – is a document as a whole.
		○ The document global object belongs exactly to this class. It serves as an entry point to the DOM.
	• CharacterData – an “abstract” class, inherited by:
		○ Text – the class corresponding to a text inside elements.
		○ Comment – the class for comments. They are not shown, but each comment becomes a member of DOM.
	• Element – is the base class for DOM elements.
		○ It provides element-level navigation like nextElementSibling, children and searching methods like getElementsByTagName, querySelector.
		○ A browser supports not only HTML, but also XML and SVG. So the Element class serves as a base for more specific classes: SVGElement, XMLElement (we don’t need them here) and HTMLElement.
	• HTMLElement is the basic class for all HTML elements. We’ll work with it most of the time. It is inherited by concrete HTML elements:
		○ HTMLInputElement – the class for <input> elements,
		○ HTMLBodyElement – the class for <body> elements,
		○ HTMLAnchorElement – the class for <a> elements,
		○ …and so on.

There are many other tags with their own classes that may have specific properties and methods, while some elements, such as <span>, <section>, <article> do not have any specific properties, so they are instances of HTMLElement class.
 
So, the full set of properties and methods of a given node comes as the result of the chain of inheritance.

To see the DOM node class name, we can recall that an object usually has the constructor property. It references the class constructor, and constructor.name is its name:
 
alert( document.body.constructor.name ); // HTMLBodyElement

…Or we can just toString it:
 
alert( document.body ); // [object HTMLBodyElement]

We also can use instanceof to check the inheritance:
 
alert( document.body instanceof HTMLBodyElement ); // true
alert( document.body instanceof HTMLElement ); // true
alert( document.body instanceof Element ); // true
alert( document.body instanceof Node ); // true
alert( document.body instanceof EventTarget ); // true

Properties/Getters/Setters

innerHTML

The innerHTML property allows to get the HTML inside the element as a string.

We can also modify it. So it’s one of the most powerful ways to change the page.

outerHTML

The outerHTML property contains the full HTML of the element. That’s like innerHTML plus the element itself.

Beware: unlike innerHTML, writing to outerHTML does not change the element. Instead, it replaces it in the DOM.

<div>Hello, world!</div>
 
<script>
  let div = document.querySelector('div');
 
  // replace div.outerHTML with <p>...</p>
  div.outerHTML = '<p>A new element</p>'; // (*)
 
  // 'div' is still the same!
  alert(div.outerHTML); // <div>Hello, world!</div>
</script>


The element div is still the same, but that no longer exists in the dom – if we did a new querySelector we would get the newly written data.

nodeValue / data

Both nodeValue and data provide content from text nodes and comments

textContent

The textContent provides access to the text inside the element: only text, minus all <tags>.
 
For instance:
 
<div id="news">
  <h1>Headline!</h1>
  <p>Martians attack people!</p>
</div>
 
<script>
  // Headline! Martians attack people!
  alert(news.textContent);
</script>

As we can see, only text is returned, as if all <tags> were cut out, but the text in them remained.
 
In practice, reading such text is rarely needed.
 
Writing to textContent is much more useful, because it allows to write text the “safe way”.
 
Let’s say we have an arbitrary string, for instance entered by a user, and want to show it.
 
	• With innerHTML we’ll have it inserted “as HTML”, with all HTML tags.
	• With textContent we’ll have it inserted “as text”, all symbols are treated literally.


hidden

The hidden attribute and the DOM property specifies whether the element is visible or not.
 
We can use it in HTML or assign it using JavaScript, like this:
 
<div>Both divs below are hidden</div>

<div hidden>With the attribute "hidden"</div>
 
<div id="elem">JavaScript assigned the property "hidden"</div>
 
<script>
  elem.hidden = true;
</script>

Technically, hidden works the same as style="display:none". But it’s shorter to write.
 
Here’s a blinking element:

<div id="elem">A blinking element</div>
 
<script>
  setInterval(() => elem.hidden = !elem.hidden, 1000);
</script>

More Properties

DOM elements also have additional properties, in particular those that depend on the class:
 
	• value – the value for <input>, <select> and <textarea> (HTMLInputElement, HTMLSelectElement…).
	• href – the “href” for <a href="..."> (HTMLAnchorElement).
	• id – the value of “id” attribute, for all elements (HTMLElement).
	• …and much more…

Most standard HTML attributes have the corresponding DOM property, and we can access it like that.
