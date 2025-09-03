Note – use UI Events Pointer Events - but be aware of the rules in this page.

	• mousedown/mouseup
		○ Mouse button is clicked/released over an element.
	• mouseover/mouseout
		○ Mouse pointer comes over/out from an element.
	• mousemove
		○ Every mouse move over an element triggers that event.
	• click
		○ Triggers after mousedown and then mouseup over the same element if the left mouse button was used.
	• dblclick
		○ Triggers after two clicks on the same element within a short timeframe. Rarely used nowadays.
	• contextmenu
		○ Triggers when the right mouse button is pressed. There are other ways to open a context menu, e.g. using a special keyboard key, it triggers in that case also, so it’s not exactly the mouse event.

Left click generates: 

	1. mousedown
	2. mouseup
	3. click

Right click generates:

	1. mousedown
	2. mouseup
	3. contextmenu

Button

Click-related events always have the button property, which allows to get the exact mouse button.

|   |   |
|---|---|
|Button|event.button|
|Left button (primary)|0|
|Middle button (auxiliary)|1|
|Right button (secondary)|2|
|X1 button (back)|3|
|X2 button (forward)|4|

event.which is deprecated.

Modifiers

	• shiftKey: Shift
	• altKey: Alt (or Opt for Mac)
	• ctrlKey: Ctrl
	• metaKey: Cmd for Mac

Coordinates

All mouse events provide coordinates in two flavours:

	1. Window-relative: clientX and clientY.
	2. Document-relative: pageX and pageY.


Preventing selection on mousedown

<b ondblclick="alert('Click!')" onmousedown="return false"> 
Double-click me 
</b>
