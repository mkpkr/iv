Note – use UI Events Pointer Events - but be aware of the rules in this page.

	• mouseover
	• mouseout
	• mouseenter
	• mouseleave

These events are special, because they have property relatedTarget. This property complements target. When a mouse leaves one element for another, one of them becomes target, and the other one – relatedTarget.

For mouseover:
	• event.target – is the element where the mouse came over.
	• event.relatedTarget – is the element from which the mouse came (relatedTarget → target).

For mouseout the reverse:
	• event.target – is the element that the mouse left.
	• event.relatedTarget – is the new under-the-pointer element, that mouse left for (target → relatedTarget).

The relatedTarget property can be null.

That’s normal and just means that the mouse came not from another element, but from out of the window. Or that it left the window.

We should keep that possibility in mind when using event.relatedTarget in our code. If we access event.relatedTarget.tagName, then there will be an error.

Skipping Elements

The mousemove event triggers when the mouse moves. But that doesn’t mean that every pixel leads to an event.

The browser checks the mouse position from time to time. And if it notices changes then triggers the events.

That means that if the visitor is moving the mouse very fast then some DOM-elements may be skipped:

![[Pasted image 20250903160429.png]]
We should keep in mind that the mouse pointer doesn’t “visit” all elements along the way. It can “jump”.

In particular, it’s possible that the pointer jumps right inside the middle of the page from out of the window. In that case relatedTarget is null, because it came from “nowhere”.

If mouseover Triggered, There Must be mouseout

In case of fast mouse movements, intermediate elements may be ignored, but one thing we know for sure: if the pointer “officially” entered an element (mouseover event generated), then upon leaving it we always get mouseout.



Mouseout When Leaving for a Child

(Note this section doesn't apply for mouseenter/mouseleave (see below)).


An important feature of mouseout – it triggers, when the pointer moves from an element to its descendant, e.g. from #parent to #child:

![[Pasted image 20250903160448.png]]
According to the browser logic, the mouse cursor may be only over a single element at any time – the most nested one and top by z-index.

So if it goes to another element (even a descendant), then it leaves the previous one.

Please note another important detail of event processing.

The mouseover event on a descendant bubbles up. So, if #parent has mouseover handler, it triggers:

![[Pasted image 20250903160505.png]]parent.onmouseout = function(event) {
  /* event.target: parent element */
};

parent.onmouseover = function(event) {
  /* event.target: child element (bubbled) */
};

If we don’t examine event.target inside the handlers, then it may seem that the mouse pointer left parent element, and then immediately came back over it.

Alternatively we can use other events: mouseenter and mouseleave, that we’ll be covering now, as they don’t have such problems.

mouseenter / mouseleave

Events mouseenter/mouseleave are like mouseover/mouseout. They trigger when the mouse pointer enters/leaves the element.

But there are two important differences:

	1. Transitions inside the element, to/from descendants, are not counted.
	2. Events mouseenter/mouseleave do not bubble.

These events are extremely simple.

Event Delegation

Events mouseenter/leave are very simple and easy to use. But they do not bubble. So we can’t use Event Delegation with them.

