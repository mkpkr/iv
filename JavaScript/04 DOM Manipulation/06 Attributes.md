Attributes & Properties
Standard attributes get a property created, non-standard attrbitutes do not:

<body id="test" something="non-standard">
  <script>
    alert(document.body.id); // test
    // non-standard attribute does not yield a property
    alert(document.body.something); // undefined
  </script>
</body>

However, either way all attributes can be retrieved and set using the following:

	• elem.hasAttribute(name) – checks for existence.
	• elem.getAttribute(name) – gets the value.
	• elem.setAttribute(name, value) – sets the value.
	• elem.removeAttribute(name) – removes the attribute.
	• Elem.attributes - a collection of objects that belong to a built-in Attr class, with name and value properties.


Property-attribute Synchronization
 
When a standard attribute changes, the corresponding property is auto-updated, and (with some exceptions) vice versa.
 
But there are exclusions, for instance input.value synchronizes only from attribute → property, but not back:
 
That “feature” may actually come in handy, because the user actions may lead to value changes, and then after them, if we want to recover the “original” value from HTML, it’s in the attribute.

Custom Attributes

It is legal to use custom attributes but may cause issues if they are later added to the standard.

Instead it is recommended if you want to pass data with a tag, to use attributes named data-

All attributes starting with “data-” are reserved for programmers’ use. They are available in the dataset property.

<body data-about="Elephants">
<script>
  alert(document.body.dataset.about); // Elephants
</script>

Multiword attributes like data-order-state become camel-cased: dataset.orderState