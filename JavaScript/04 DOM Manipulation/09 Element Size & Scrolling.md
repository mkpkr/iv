![[Pasted image 20250903145759.png]]

The picture above demonstrates the most complex case when the element has a scrollbar. Some browsers (not all) reserve the space for it by taking it from the content (labelled as “content width” above).

So, without scrollbar the content width would be 300px, but if the scrollbar is 16px wide (the width may vary between devices and browsers) then only 300 - 16 = 284px remains, and we should take it into account.

# Geometry Properties

As discussed in [Styles  Classes](onenote:#Styles%20%20Classes&section-id={d18ccded-9a0d-486d-b537-9ef18671bff8}&page-id={088fdb4f-54ce-4e4e-a854-e2968e545136}&end) , we can read CSS-height and width using getComputedStyle, but there are several reasons we should use geometry properties instead:

1. CSS width/height depend on another property: box-sizing that defines “what is” CSS width and height. A change in box-sizing for CSS purposes may break such JavaScript.
2. CSS width/height may be auto. From the CSS standpoint, width:auto is perfectly normal, but in JavaScript we need an exact size in px that we can use in calculations. So here CSS width is useless.
3. A scrollbar. Sometimes the code that works fine without a scrollbar becomes buggy with it, because a scrollbar takes the space from the content in some browsers. So the real width available for the content is less than CSS width. And geometry properties clientWidth/clientHeight take that into account.

- With getComputedStyle(elem).width the situation is different. Some browsers (e.g. Chrome) return the real inner width, minus the scrollbar, and some of them (e.g. Firefox) – CSS width (ignore the scrollbar). Such cross-browser differences is the reason not to use getComputedStyle, but rather rely on geometry properties.

## Summary

- offsetParent – is the nearest positioned ancestor or td, th, table, body.
- offsetLeft/offsetTop – coordinates relative to the upper-left edge of offsetParent.
- offsetWidth/offsetHeight – “outer” width/height of an element including borders.
- clientLeft/clientTop – the distances from the upper-left outer corner to the upper-left inner (content + padding) corner. For left-to-right OS they are always the widths of left/top borders. For right-to-left OS the vertical scrollbar is on the left so clientLeft includes its width too.
- clientWidth/clientHeight – the width/height of the content including paddings, but without the scrollbar.
- scrollWidth/scrollHeight – the width/height of the content, just like clientWidth/clientHeight, but also include scrolled-out, invisible part of the element.
- scrollLeft/scrollTop – width/height of the scrolled out upper part of the element, starting from its upper-left corner.
![[Pasted image 20250903145830.png]]

Values of these properties are technically numbers, but these numbers are “of pixels”, so these are pixel measurements.

# offsetParent, offsetLeft/Top

- offsetParent

- nearest ancestor that the browser uses for calculating coordinates during rendering, meaning the nearest ancestor that is one of the following:

1. CSS-positioned (position is absolute, relative, fixed or sticky), or
2. `<td>`, `<th>`, or `<table>`, or
3. `<body>`.

- rarely needed

- offsetLeft/Top

- provide x/y coordinates relative to offsetParent upper-left corner
- rarely needed
- 
```html
<main style="position: relative" id="main">
   <article>
     <div id="example" style="position: absolute; left: 180px; top: 180px">...</div>
   </article>
</main>
<script>
   alert(example.offsetParent.id); // main
   alert(example.offsetLeft); // 180 (note: a number, not a string "180px")
   alert(example.offsetTop); // 180
</script>
```

![[Pasted image 20250903145953.png]]

There are several occasions when offsetParent is null:

1. For not shown elements (display:none or not in the document).
2. For `<body>` and `<html>`.
3. For elements with position:fixed.

# offsetWidth/Height

These two properties are the simplest ones. They provide the “outer” width/height of the element. Or, in other words, its full size including borders.

![[Pasted image 20250903150038.png]]

- offsetWidth = 390 : the outer width (inner CSS-width (300px) plus paddings (2 * 20px) and borders (2 * 25px))
- offsetHeight = 290 : the outer height

offsetWidth & offsetHeight are 0 when we created an element, but haven’t inserted it into the document yet, or it (or its ancestor) has display:none

# clientTop/Left

Inside the element we have the borders.

To measure them, there are properties clientTop and clientLeft.

![[Pasted image 20250903150116.png]]

…But to be precise – these properties are not border width/height, but rather relative coordinates of the inner side from the outer side.

What’s the difference?

It becomes visible when the document is right-to-left (the operating system is in Arabic or Hebrew languages). The scrollbar is then not on the right, but on the left, and then clientLeft also includes the scrollbar width.

In that case, clientLeft would be not 25, but with the scrollbar width 25 + 16 = 41.

![[Pasted image 20250903150125.png]]

# clientWidth/Height

These properties provide the size of the area inside the element borders.

They include the content width together with paddings, but without the scrollbar:

![[Pasted image 20250903150144.png]]

On the picture above let’s first consider clientHeight.

There’s no horizontal scrollbar, so it’s exactly the sum of what’s inside the borders: CSS-height 200px plus top and bottom paddings (2 * 20px) total 240px.

Now clientWidth – here the content width is not 300px, but 284px, because 16px are occupied by the scrollbar. So the sum is 284px plus left and right paddings, total 324px.

If there are no paddings, then clientWidth/Height is exactly the content area, inside the borders and the scrollbar (if any).

# scrollWidth/Height

These properties are like clientWidth/clientHeight, but they also include the scrolled out (hidden) parts:

![[Pasted image 20250903150207.png]]

We can use these properties to expand the element wide to its full width/height:

`element.style.height = ${element.scrollHeight}px`;

# scrollLeft/scrollTop

Properties scrollLeft/scrollTop are the width/height of the hidden, scrolled out part of the element.

In other words, scrollTop is “how much is scrolled up”.

On the picture below we can see scrollHeight and scrollTop for a block with a vertical scroll.

![[Pasted image 20250903150239.png]]

Most of the geometry properties here are read-only, but scrollLeft/scrollTop can be changed, and the browser will scroll the element.

If you click the element below, the code elem.scrollTop += 10 executes. That makes the element content scroll 10px down.

