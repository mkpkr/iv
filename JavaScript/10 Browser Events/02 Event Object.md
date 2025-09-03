To properly handle an event we’d want to know more about what’s happened. 

Not just a “click” or a “keydown”, but what were the pointer coordinates? Which key was pressed? And so on.

When an event happens, the browser creates an event object, puts details into it and passes it as an argument to the handler.

<input type="button" value="Click me" id="elem">
 
<script>
  elem.onclick = function(event) {
    // show event type, element and coordinates of the click
    alert(event.type + " at " + event.currentTarget);
    alert("Coordinates: " + event.clientX + ":" + event.clientY);
  };
</script>

Some properties of event object:
 
	• event.type
		○ Event type, here it’s "click".
	• event.currentTarget
		○ Element that handled the event. 
		○ That’s exactly the same as this, unless the handler is an arrow function, or its this is bound to something else, then we can get the element from event.currentTarget.
	• event.target
		○ the element that caused the event (could be a nested element), covered in Bubbling and Capturing  
	• event.clientX / event.clientY
		○ Window-relative coordinates of the cursor, for pointer events.

There are more properties. Many of them depend on the event type.


Object Handlers: handleEvent

We can assign not just a function, but an object as an event handler using addEventListener. When an event occurs, its handleEvent method is called.

<button id="elem">Click me</button>
 
<script>
  let obj = {
    handleEvent(event) {
      alert(event.type + " at " + event.currentTarget);
    }
  };
 
  elem.addEventListener('click', obj);
</script>



We could also use objects of a custom class, like this:
 
<button id="elem">Click me</button>
 
<script>
  class Menu {
    handleEvent(event) {
      switch(event.type) {
        case 'mousedown':
          elem.innerHTML = "Mouse button pressed";
          break;
        case 'mouseup':
          elem.innerHTML += "...and released.";
          break;
      }
    }
  }
 
  let menu = new Menu();
 
  elem.addEventListener('mousedown', menu);
  elem.addEventListener('mouseup', menu);
</script>

