The scroll event allows reacting to a page or element scrolling. There are quite a few good things we can do here.

For instance:
	• Show/hide additional controls or information depending on where in the document the user is.
	• Load more data when the user scrolls down till the end of the page.

Here’s a small function to show the current scroll:

window.addEventListener('scroll', function() {

  document.getElementById('showScroll').innerHTML = window.pageYOffset + 'px';

});

The scroll event works both on the window and on scrollable elements.


PreventScrolling

How do we make something unscrollable?

We can’t prevent scrolling by using event.preventDefault() in onscroll listener, because it triggers after the scroll has already happened.

But we can prevent scrolling by event.preventDefault() on an event that causes the scroll, for instance keydown event for pageUp and pageDown.

If we add an event handler to these events and event.preventDefault() in it, then the scroll won’t start.

There are many ways to initiate a scroll, so it’s more reliable to use CSS, overflow property.

Examples

Some scroll examples can be found here: https://javascript.info/onscroll#tasks

