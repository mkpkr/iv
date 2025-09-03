We can not only assign handlers, but also generate events from JavaScript, including our own custom events.
We can generate not only completely new events, that we invent for our own purposes, but also built-in ones, such as click, mousedown etc. That may be helpful for automated testing.

Event Constructor

let event = new Event(type[, options]);

	• type – event type, a string like "click" or our own like "my-event".
	• options – the object with two optional properties:
		○ bubbles: true/false – if true, then the event bubbles.
		○ cancelable: true/false – if true, then the “default action” may be prevented. Later we’ll see what it means for custom events.

By default both are false: {bubbles: false, cancelable: false}.

Note for existing events we should actually use the specific constructor e.g. MouseEvent, and for custom events we should use CustomEvent, see below sections.

Dispatch

<script>
  let event = new Event("click");
  elem.dispatchEvent(event);
</script>

Note to handle a custom event, we must use addEventListener, because on<event> only exists for built-in events, document.onhello doesn’t work.

isTrusted

There is a way to tell a “real” user event from a script-generated one.

The property event.isTrusted is true for events that come from real user actions and false for script-generated events.

MouseEvent, KeyboardEvent etc

Here’s a short list of classes for UI Events from the UI Event specification:

	• UIEvent
	• FocusEvent
	• MouseEvent
	• WheelEvent
	• KeyboardEvent
	• …

We should use them instead of new Event if we want to create such events. For instance, new MouseEvent("click").

The right constructor allows to specify standard properties for that type of event.

Like clientX/clientY for a mouse event:

let event = new MouseEvent("click", {
  bubbles: true,
  cancelable: true,
  clientX: 100,
  clientY: 100
});


CustomEvent

For our own, completely new events types like "hello" we should use new CustomEvent. Technically CustomEvent is the same as Event, with one exception.
 
In the second argument (object) we can add an additional property detail for any custom information that we want to pass with the event.

<h1 id="elem">Hello for John!</h1>
 
<script>
  // additional details come with the event to the handler
  elem.addEventListener("hello", function(event) {
    alert(event.detail.name);
  });
 
  elem.dispatchEvent(new CustomEvent("hello", {
    detail: { name: "John" }
  }));
</script>

The detail property can have any data. Technically we could live without, because we can assign any properties into a regular new Event object after its creation. But CustomEvent provides the special detail field for it to evade conflicts with other event properties.

Besides, the event class describes “what kind of event” it is, and if the event is custom, then we should use CustomEvent just to be clear about what it is.

Events-in-events are Synchronous

Usually events are processed in a queue. That is: if the browser is processing onclick and a new event occurs, e.g. mouse moved, then its handling is queued up, corresponding mousemove handlers will be called after onclick processing is finished.

The notable exception is when one event is initiated from within another one, e.g. using dispatchEvent. Such events are processed immediately: the new event handlers are called, and then the current event handling is resumed.
