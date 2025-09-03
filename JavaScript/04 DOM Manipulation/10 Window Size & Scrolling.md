How do we find the width and height of the browser window? How do we get the full width and height of the document, including the scrolled out part? How do we scroll the page using JavaScript?

For this type of information, we can use the root document element document.documentElement, that corresponds to the `<html>` tag.

But there are additional methods and peculiarities to consider.

# Width/Height of the Window

To get window width and height, we can use the clientWidth/clientHeight of document.documentElement:

![[Pasted image 20250903150319.png]]

Don't use window.innerWidth/innerHeight

If there exists a scrollbar, and it occupies some space, clientWidth/clientHeight provide the width/height without it (subtract it). In other words, they return the width/height of the visible part of the document, available for the content.

window.innerWidth/innerHeight includes the scrollbar.

In most cases, we need the available window width in order to draw or position something within scrollbars (if there are any).

Please note: top-level geometry properties may work a little bit differently when there’s no <!DOCTYPE HTML> in HTML. Odd things are possible.

In modern HTML we should always write DOCTYPE.

# Get the Current Scroll

DOM elements have their current scroll state in their scrollLeft/scrollTop properties.

For document scroll, document.documentElement.scrollLeft/scrollTop works in most browsers, except older WebKit-based ones, like Safari (bug 5991), where we should use document.body instead of document.documentElement.

Luckily, we don’t have to remember these peculiarities at all, because the scroll is available in the special properties, window.pageXOffset/pageYOffset.

alert('Current scroll from the top: ' + window.pageYOffset);

alert('Current scroll from the left: ' + window.pageXOffset);

Also available as window properties scrollX and scrollY (for historical reasons).

# scrollTo, scrollBy

To scroll the page with JavaScript, its DOM must be fully built.

For instance, if we try to scroll the page with a script in `<head>`, it won’t work.

Regular elements can be scrolled by changing scrollTop/scrollLeft.

We can do the same for the page using document.documentElement.scrollTop/scrollLeft (except Safari, where document.body.scrollTop/Left should be used instead).

Alternatively, there’s a simpler, universal solution: special methods window.scrollBy(x,y) and window.scrollTo(pageX,pageY).

- scrollBy(x,y) scrolls the page relative to its current position.

- For instance, scrollBy(0,10) scrolls the page 10px down.

- scrollTo(pageX,pageY) scrolls the page to absolute coordinates, so that the top-left corner of the visible part has coordinates (pageX, pageY) relative to the document’s top-left corner.

# scrollIntoView

The call to elem.scrollIntoView(top) scrolls the page to make elem visible. It has one argument:

- scrollIntoView(true) - default - the page will be scrolled to make element appear on the top of the window. The upper edge of the element will be aligned with the window top.
- scrollIntoView(false) -  then the page scrolls to make elem appear at the bottom. The bottom edge of the element will be aligned with the window bottom.

# Prevent Scrolling

Sometimes we need to make the document “unscrollable”. For instance, when we need to cover the page with a large message requiring immediate attention, and we want the visitor to interact with that message, not with the document.

- document.body.style.overflow = "hidden" - prevent scrolling

- The page will “freeze” at its current scroll position.

- document.body.style.overflow = '' - allow scrolling

We can use the same technique to freeze the scroll for other elements, not just for document.body.

The drawback of the method is that the scrollbar disappears. If it occupied some space, then that space is now free and the content “jumps” to fill it.

That looks a bit odd, but can be worked around if we compare clientWidth before and after the freeze. If it increased (the scrollbar disappeared), then add padding to document.body in place of the scrollbar to keep the content width the same.