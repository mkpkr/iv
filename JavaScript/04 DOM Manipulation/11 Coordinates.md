To move elements around we should be familiar with coordinates.

Any point on the page has coordinates:

- Relative to the window – .
- Relative to the document – .

Most JavaScript methods deal with one of two coordinate systems:

1. Relative to the window – elem.getBoundingClientRect()

- similar to position:fixed, calculated from the window top/left edge.
- we’ll denote these coordinates as clientX/clientY

1. Relative to the document – elem.getBoundingClientRect() plus the current page scroll

- similar to position:absolute in the document root, calculated from the document top/left edge.
- we’ll denote them pageX/pageY

When the page is scrolled to the very beginning, so that the top/left corner of the window is exactly the document top/left corner, these coordinates equal each other. But after the document shifts, window-relative coordinates of elements change, as elements move across the window, while document-relative coordinates remain the same.

On this picture we take a point in the document and demonstrate its coordinates before the scroll (left) and after it (right):

# Get Element Coordinates – getBoundingClientRect()

The method elem.getBoundingClientRect() returns window coordinates for a minimal rectangle that encloses elem as an object of built-in DOMRect class.

Main DOMRect properties:

- x/y – X/Y-coordinates of the rectangle origin relative to window,
- width/height – width/height of the rectangle (can be negative but not in the context of getBoundingClientRect()).

Additionally, there are derived properties:

- top/bottom – Y-coordinate for the top/bottom rectangle edge,
- left/right – X-coordinate for the left/right rectangle edge.
- 
![[Pasted image 20250903150418.png]]


- Coordinates may be decimal fractions, such as 10.5. That’s normal, internally browser uses fractions in calculations.

- We don’t have to round them when setting to style.left/top.

- Coordinates may be negative. For instance, if the page is scrolled so that elem is now above the window, then elem.getBoundingClientRect().top is negative.

- Internet Explorer doesn’t support x/y properties for historical reasons.

- So we can either make a polyfill (add getters in DomRect.prototype) or just use top/left, as they are always the same as x/y for getBoundingClientRect()

- Coordinates right/bottom are different from CSS position properties

- There are obvious similarities between window-relative coordinates and CSS position:fixed.
- But in CSS positioning, right property means the distance from the right edge, and bottom property means the distance from the bottom edge.
- In JavaScript, all window coordinates are counted from the top-left corner, including these ones.

# elementFromPoint(x, y)
 
The call to document.elementFromPoint(x, y) returns the most nested element at window coordinates (x, y).

The following alerts the tag name of the most nested element in the center of the window, and highlights the element red:

let centerX = document.documentElement.clientWidth / 2;
let centerY = document.documentElement.clientHeight / 2;
 
let elem = document.elementFromPoint(centerX, centerY);
 
elem.style.background = "red";
alert(elem.tagName);

The method document.elementFromPoint(x,y) only works if (x,y) are inside the visible area.

For out-of-window coordinates the elementFromPoint returns null.


# Place an Element Near Another (Fixed Coordinates)

To show something near an element, we can use getBoundingClientRect to get its coordinates, and then CSS position together with left/top (or right/bottom).

Example - place a message under a button:

let elem = document.getElementById("my-button");
 
function createMessageUnder(elem, html) {
  // create message element
  let message = document.createElement('div');
  // better to use a css class for the style here
  message.style.cssText = "position:fixed; color: red";
 
  // assign coordinates, don't forget "px"!
  let coords = elem.getBoundingClientRect();
 
  message.style.left = coords.left + "px";
  message.style.top = coords.bottom + "px";
 
  message.innerHTML = html;
 
  return message;
}
 
// Usage:
// add it for 5 seconds in the document
let message = createMessageUnder(elem, 'Hello, world!');
document.body.append(message);
setTimeout(() => message.remove(), 5000);


Note the highlighted red (position:fixed) - this means that when we scroll away, the element stays at the same place on the window – not good in this scenario. To change that, we need to use document-based coordinates and position:absolute (next section).


# Document Coordinates

Document-relative coordinates start from the upper-left corner of the document, not the window.

We can use position:absolute and top/left to put something at a certain place of the document, so that it remains there during a page scroll. But we need the right coordinates first.
 
There’s no standard method to get the document coordinates of an element. But it’s easy to write it.
 
The two coordinate systems are connected by the formula:
 
	• pageY = clientY + height of the scrolled-out vertical part of the document.
	• pageX = clientX + width of the scrolled-out horizontal part of the document.

See "Get the Current Scroll" in Window Size  Scrolling for why we use pageYOffset/pageXOffset:

The function below will take window coordinates from elem.getBoundingClientRect() and add the current scroll to them:

// get document coordinates of the element
function getCoords(elem) {
  let box = elem.getBoundingClientRect();
 
  return {
    top: box.top + window.pageYOffset,
    right: box.right + window.pageXOffset,
    bottom: box.bottom + window.pageYOffset,
    left: box.left + window.pageXOffset
  };
}

Now we can rewrite the place element example using absolute (document) coordinates:

function createMessageUnder(elem, html) {
  let message = document.createElement('div');
  message.style.cssText = "position:absolute; color: red";
 
  let coords = getCoords(elem);
 
  message.style.left = coords.left + "px";
  message.style.top = coords.bottom + "px";
 
  message.innerHTML = html;
 
  return message;
}


