Navigation: form and elements

Document forms are members of the special collection document.forms.
 
That’s a so-called “named collection”: it’s both named and ordered. We can use both the name or the number in the document to get the form.
 
```
document.forms.my; // the form with name="my"
document.forms[0]; // the first form in the document
document.forms.my.elements.someEelement; //the element (or collection of elements) named "someElem" in the form "my"
document.forms.my.elements['someElement']; //same
```

When we have a form, then any element is available in the named collection form.elements.
 
For instance:
 
```
<form name="my">
  <input name="one" value="1">
  <input name="two" value="2">
</form>

<script>
  // get the form
  let form = document.forms.my; // <form name="my"> element
 
  // get the element
  let elem = form.elements.one; // <input name="one"> element
 
  alert(elem.value); // 1
</script>
```


There may be multiple elements with the same name. This is typical with radio buttons and checkboxes.
 
In that case, form.elements[name] is a collection. For instance:
 
```
<form>
  <input type="radio" name="age" value="10">
  <input type="radio" name="age" value="20">
</form>
 
<script>
  let form = document.forms[0];
 
  let ageElems = form.elements.age;
 
  alert(ageElems[0]); // [object HTMLInputElement]
</script>
```


These navigation properties do not depend on the tag structure. All control elements, no matter how deep they are in the form, are available in form.elements.


Backreference: element.form

For any element, the form is available as element.form. So a form references all elements, and elements reference the form:
![[Pasted image 20250903160721.png]]
```
<form id="form">
  <input type="text" name="login">
</form>
 
<script>
  // form -> element
  let login = form.login;
 
  // element -> form
  alert(login.form); // HTMLFormElement
</script>
```