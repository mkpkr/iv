 input, textarea

input.value = "New value";
textarea.value = "New text";
 
input.checked = true; // for a checkbox or radio button

Use textarea.value, not textarea.innerHTML

Please note that even though `<textarea>...</textarea>` holds its value as nested HTML, we should never use textarea.innerHTML to access it.
 
It stores only the HTML that was initially on the page, not the current value.


select, option

A `<select>` element has 3 important properties:
 
	1. select.options – the collection of <option> subelements,
	2. select.value – the value of the currently selected <option>,
	3. select.selectedIndex – the number of the currently selected `<option>`.

They provide three different ways of setting a value for a` <select>`:
 
	1. Find the corresponding `<option>` element (e.g. among select.options) and set its option.selected to true.
	2. If we know a new value: set select.value to the new value.
	3. If we know the new option number: set select.selectedIndex to that number.


Unlike most other controls, `<select>` allows to select multiple options at once if it has multiple attribute. This attribute is rarely used, though.
 
For multiple selected values, use the first way of setting values: add/remove the selected property from `<option>` subelements.
 
Here’s an example of how to get selected values from a multi-select:
 
```
<select id="select" multiple>
  <option value="blues" selected>Blues</option>
  <option value="rock" selected>Rock</option>
  <option value="classic">Classic</option>
</select>
 
<script>
  // get all selected values from multi-select
  let selected = Array.from(select.options)
    .filter(option => option.selected)
    .map(option => option.value);
 
  alert(selected); // blues,rock
</script>
```

