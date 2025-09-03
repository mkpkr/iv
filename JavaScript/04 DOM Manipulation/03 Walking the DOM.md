The topmost tree nodes are available directly as document properties:
 
	• <html> = document.documentElement
	• <body> = document.body
	• <head> = document.head

There’s a catch: document.body can be null

A script cannot access an element that doesn’t exist at the moment of running. In particular, if a script is inside `<head>`, then document.body is unavailable, because the browser did not read it yet.


![[Pasted image 20250903145438.png]]

Siblings are nodes that are children of the same parent.

	• document.head.nextSibling
	• document.body.previousSibling
	• document.body.parentNode === document.documentElement //true
	
	• document.body.childNodes.length
	• elem.hasChildNodes()
	• elem.childNodes[0] === elem.firstChild //true
	• elem.childNodes[elem.childNodes.length - 1] === elem.lastChild //true

Note that childNodes is a collection, not array. So you can iterate over it but not use array elements, however we can use Array.from to create a “real” array from the collection, if we want array methods:

alert( Array.from(document.body.childNodes).filter ); // function


DOM collections are read-only

We can’t replace a child by something else by assigning childNodes[i] = ....
 
Changing DOM needs other methods. We will see them in the next chapter.

Don’t use for..in to loop over collections

Collections are iterable using for..of. Sometimes people try to use for..in for that.
 
Please, don’t. The for..in loop iterates over all enumerable properties. And collections have some “extra” rarely used properties that we usually do not want to get.

Element Only Navigation

For many tasks we don’t want text or comment nodes. We want to manipulate element nodes that represent tags and form the structure of the page:

![[Pasted image 20250903145528.png]]

The links are similar to those given above, just with Element word inside:
 
	• children – only those children that are element nodes.
	• firstElementChild, lastElementChild – first and last element children.
	• previousElementSibling, nextElementSibling – neighbor elements.
	• parentElement – parent element.

Why parentElement? Can the parent be not an element?
The parentElement property returns the “element” parent, while parentNode returns “any node” parent. These properties are usually the same: they both get the parent.
 
With the one exception of document.documentElement:
 
alert( document.documentElement.parentNode ); // document
alert( document.documentElement.parentElement ); // null

The reason is that the root node document.documentElement (<html>) has document as its parent. But document is not an element node, so parentNode returns it and parentElement does not.

Tables

let td = table.rows[0].cells[1];

<table> supports (in addition to the given above) these properties:
 
	• table.rows – the collection of <tr> elements of the table.
	• table.caption/tHead/tFoot – references to elements <caption>, <thead>, <tfoot>.
	• table.tBodies – the collection of <tbody> elements (can be many according to the standard, but there will always be at least one – even if it is not in the source HTML, the browser will put it in the DOM).

<thead>, <tfoot>, <tbody> elements provide the rows property:
 
	• tbody.rows – the collection of <tr> inside.

<tr>:
 
	• tr.cells – the collection of <td> and <th> cells inside the given <tr>.
	• tr.sectionRowIndex – the position (index) of the given <tr> inside the enclosing <thead>/<tbody>/<tfoot>.
	• tr.rowIndex – the number of the <tr> in the table as a whole (including all table rows).

<td> and <th>:
 
td.cellIndex – the number of the cell inside the enclosing <tr>.