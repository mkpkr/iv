There are generally two ways to style an element:
 
	• Create a class in CSS and add it: <div class="...">
	• Write properties directly into style: <div style="...">.

JavaScript can modify both classes and style properties.
 
We should always prefer CSS classes to style. 

className

Changing a class is one of the most often used actions in scripts.
 
In the ancient time, there was a limitation in JavaScript: a reserved word like "class" could not be an object property. That limitation does not exist now, but at that time it was impossible to have a "class" property, like elem.class.
 
So for classes the similar-looking property "className" was introduced: the elem.className corresponds to the "class" attribute.
 
<body class="main page">
  <script>
    alert(document.body.className); // main page
  </script>
</body>
 
If we assign something to elem.className, it replaces the whole string of classes. Sometimes that’s what we need, but often we want to add/remove a single class.

classList 

There’s another property for that: elem.classList
 
The elem.classList is a special object with methods to add/remove/toggle a single class.
 
<body class="main page">
  <script>
    // add a class
    document.body.classList.add('article');
 
    alert(document.body.className); // main page article
  </script>
</body>

Methods of classList:

	• elem.classList.add/remove("class") – adds/removes the class.
	• elem.classList.toggle("class") – adds the class if it doesn’t exist, otherwise removes it.
	• elem.classList.contains("class") – checks for the given class, returns true/false.

Besides, classList is iterable, so we can list all classes with for..of, like this:
 
<body class="main page">
  <script>
    for (let name of document.body.classList) {
      alert(name); // main, and then page
    }
  </script>
</body>


getComputedStyle

Note when using the style property of an element to get or set its style:

The style property operates only on the value of the "style" attribute, without any CSS cascade.

So we can’t read anything that comes from CSS classes using elem.style, we must use:

getComputedStyle(element, [pseudo])
	
	• element
		○ Element to read the value for.
	• pseudo
		○ A pseudo-element if required, for instance ::before. 
		○ An empty string or no argument means the element itself.

Computed and Resolved Values

There are two concepts in CSS:
 
	• A computed style value is the value after all CSS rules and CSS inheritance is applied, as the result of the CSS cascade. It can look like height:1em or font-size:125%.
	• A resolved style value is the one finally applied to the element. Values like 1em or 125% are relative. The browser takes the computed value and makes all units fixed and absolute, for instance: height:20px or font-size:16px. For geometry properties resolved values may have a floating point, like width:50.5px.

A long time ago getComputedStyle was created to get computed values, but it turned out that resolved values are much more convenient, and the standard changed.
 
So nowadays getComputedStyle actually returns the resolved value of the property, usually in px for geometry.

Styles applied to :visited links are hidden!

Visited links may be colored using :visited CSS pseudoclass.
 
But getComputedStyle does not give access to that color, because otherwise an arbitrary page could find out whether the user visited a link by creating it on the page and checking the styles.
 
JavaScript may not see the styles applied by :visited. 
