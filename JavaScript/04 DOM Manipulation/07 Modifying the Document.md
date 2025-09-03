DOM modification is the key to creating “live” pages.

Create
 
	• document.createElement(tag)
	• document.createTextNode(text)
 
Edit
 
let div = document.createElement('div');
div.className = "alert";
div.innerHTML = "<strong>Hi there!</strong> You've read an important message.";
 
Insert
 
document.body.append(div);
 
Here are more insertion methods, they specify different places where to insert:
 
	• node.append(...nodes or strings) – append nodes or strings at the end of node,
	• node.prepend(...nodes or strings) – insert nodes or strings at the beginning of node,
	• node.before(...nodes or strings) – insert nodes or strings before node,
	• node.after(...nodes or strings) – insert nodes or strings after node,
	• node.replaceWith(...nodes or strings) – replaces node with the given nodes or strings.

Insert (HTML)
 
We can insert an HTML string “as html”
 
	• insertAdjacentHTML(where, html)
 
The first parameter is a code word, specifying where to insert relative to elem. Must be one of the following:
 
	• "beforebegin" – insert html immediately before elem,
	• "afterbegin" – insert html into elem, at the beginning,
	• "beforeend" – insert html into elem, at the end,
	• "afterend" – insert html immediately after elem.
 
div.insertAdjacentHTML('beforebegin', '<p>Hello</p>');
div.insertAdjacentHTML('afterend', '<p>Bye</p>');
 
There also exists elem.insertAdjacentText(where, text) and elem.insertAdjacentElement(where, elem) - however they exist mainly to make the syntax “uniform”. In practice, only insertAdjacentHTML is used most of the time. Because for elements and text, we have methods append/prepend/before/after – they are shorter to write and can insert nodes/text pieces.
 
Remove
 
	• node.remove()
 
Please note: if we want to move an element to another place – there’s no need to remove it from the old one.
 
All insertion methods automatically remove the node from the old place.


Clone
 
	• elem.cloneNode(true) - creates a “deep” clone of the element – with all attributes and subelements. 
	• elem.cloneNode(false) -  the clone is made without child elements.


DocumentFragment

DocumentFragment is a special DOM node that serves as a wrapper to pass around lists of nodes.
 
We can append other nodes to it, but when we insert it somewhere, then its content is inserted instead.
 
For example, getListContent below generates a fragment with <li> items, that are later inserted into <ul>:
 
<ul id="ul"></ul>

<script>
	function getListContent() {
	  let fragment = new DocumentFragment();
	 
	  for(let i=1; i<=3; i++) {
	    let li = document.createElement('li');
	    li.append(i);
	    fragment.append(li);
	  }
	 
	  return fragment;
	}
	 
	ul.append(getListContent());
</script>


Old Methods - Deprecated
 
Modern methods listed in this document are more flexible, but you may see in old scripts:
 
	• parentElem.appendChild(node)
	• parentElem.insertBefore(node, nextSibling)
	• parentElem.replaceChild(node, oldChild)
	• parentElem.removeChild(node)
 
There’s one more, very ancient method of adding something to a web-page: 

	• document.write
 
The call to document.write(html) writes the html into page “right here and now”. The html string can be dynamically generated, so it’s kind of flexible. We can use JavaScript to create a full-fledged webpage and write it.
 
The method comes from times when there was no DOM, no standards… Really old times. It still lives, because there are scripts using it.
 
In modern scripts we can rarely see it, because of the following important limitation:
 
The call to document.write only works while the page is loading.
