HTML tags are element nodes (or just elements) and form the tree structure: `<html>` is at the root, then `<head>` and `<body>` are its children, etc.


![[Pasted image 20250903145246.png]]


Please note the special characters in text nodes:
 
	• a newline: ↵ (in JavaScript known as \n)
	• a space: ␣

Spaces and newlines are totally valid characters, like letters and digits. They form text nodes and become a part of the DOM.  Note some `#text` nodes only contain a new line or spaces.

So if you put new lines between your HTML elements, they become text nodes.

From another course - look howthe new line + spacebetween HTML and HEAD is its own element in the DOM:

![[Pasted image 20250903145344.png]]

Everything in HTML, even comments, becomes a part of the DOM.
 
The document object that represents the whole document is, formally, a DOM node as well.

There are 12 node types. In practice we usually work with 4 of them:
 
	• document – the “entry point” into DOM.
	• element nodes – HTML-tags, the tree building blocks.
	• text nodes – contain text.
comments – sometimes we can put information there, it won’t be shown, but JS can read it from the DOM.