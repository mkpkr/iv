If you don't want to manage your data in JavaScript, you can manage it in the DOM itself, for example we may get a DOM rendered on a server and sent to us, and we may need data in that.

To associate data with DOM elements use data- tags:

<li 
 data-extra-info="some extra info" 
 data-whatever="more stuff" 
...>

This is then accessed using dataset property of the element, where attributes are converted to camelCase.

el.dastaset.extraInfo
el.dataset.whatever
