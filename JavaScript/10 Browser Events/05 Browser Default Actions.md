	• A click on a link – initiates navigation to its URL.
	• A click on a form submit button – initiates its submission to the server.
	• Pressing a mouse button over a text and moving it – selects the text.


If we handle an event in JavaScript, we may not want the corresponding browser action to happen, and want to implement another behavior instead.

Preventing Browser Actions

There are two ways to tell the browser we don’t want it to act:
 
	• The main way is to use the event object. There’s a method event.preventDefault().
	• If the handler is assigned using on<event> (not by addEventListener), then returning false also works the same.
		○ note that all other return values are ignored

<a href="/" onclick="return false">Click here</a>
<a href="/" onclick="event.preventDefault()">Or here</a>

Example: Menu

Menu items are implemented as HTML-links <a>, not buttons <button>. There are several reasons to do so, for instance:
 
	• Many people like to use “right click” – “open in a new window”. If we use <button> or <span>, that doesn’t work.
	• Search engines follow <a href="..."> links while indexing.

So we use <a> in the markup. But normally we intend to handle clicks in JavaScript. So we should prevent the default browser action:

menu.onclick = function(event) {
  if (event.target.nodeName != 'A') return;
 
  let href = event.target.getAttribute('href');
  alert( href ); // ...can be loading from the server, UI generation etc
 
  return false; // prevent browser action (don't go to the URL)
};

The “passive” Handler Option

The optional passive: true option of addEventListener signals the browser that the handler is not going to call preventDefault().
 
Why might that be needed?
 
There are some events like touchmove on mobile devices (when the user moves their finger across the screen), that cause scrolling by default, but that scrolling can be prevented using preventDefault() in the handler.
 
So when the browser detects such event, it has first to process all handlers, and then if preventDefault is not called anywhere, it can proceed with scrolling. That may cause unnecessary delays and “jitters” in the UI.
 
The passive: true options tells the browser that the handler is not going to cancel scrolling. Then browser scrolls immediately providing a maximally fluent experience, and the event is handled by the way.
 
For some browsers (Firefox, Chrome), passive is true by default for touchstart and touchmove events.
