Capturing and bubbling allow us to implement one of the most powerful event handling patterns called event delegation.

The idea is that if we have a lot of elements handled in a similar way, then instead of assigning a handler to each of them – we put a single handler on their common ancestor.

In the handler we get event.target to see where the event actually happened and handle it.

Imagine a table and we want to highlight a clicked cell – instead of adding an event handler to every `<td>` (could be many), we can add an event handle to table and use event.target:

let selectedTd;

table.onclick = function(event) {
  let target = event.target; // where was the click?

  if (target.tagName != 'TD') return; // not on TD? Then we're not interested
 
  highlight(target); // highlight it
};
 
function highlight(td) {
  if (selectedTd) { // remove the existing highlight if any
    selectedTd.classList.remove('highlight');
  }
  selectedTd = td;
  selectedTd.classList.add('highlight'); // highlight the new td
}

Still, there’s a drawback: the click may occur not on the `<td>`, but inside it.
 
In our case if we take a look inside the HTML, we can see nested tags inside `<td>`, like `<strong>`:
 
```
<td>
  <strong>Northwest</strong>
  ...
</td>
```

Naturally, if a click happens on that `<strong>` then it becomes the value of event.target.

![[Pasted image 20250903155945.png]]
Here is the improved code:

table.onclick = function(event) {
  let td = event.target.closest('td'); // returns the nearest ancestor that matches
 
  if (!td) return; // if event.target is not inside any <td>, then the call returns immediately, as there’s nothing to do
 
  if (!table.contains(td)) return; // If nested tables, event.target may be a <td>, but outside of the current table. So we check it's our table’s <td>
 
  highlight(td);
};


Example – Actions in Markup

Let’s say, we want to make a menu with buttons “Save”, “Load”, “Search” and so on. And there’s an object with methods save, load, search… How to match them?

The first idea may be to assign a separate handler to each button. But there’s a more elegant solution. 

We can add a handler for the whole menu and use custom data attributes as explained in Attributes (we will call them "data-action"):

<div id="menu">
  <button data-action="save">Save</button>
  <button data-action="load">Load</button>
  <button data-action="search">Search</button>
</div>


<script>
  class Menu {
    constructor(elem) {
      this._elem = elem;
      elem.onclick = this.onClick.bind(this); // (*)
    }
 
    save() {
      alert('saving');
    }
 
    load() {
      alert('loading');
    }
 
    search() {
      alert('searching');
    }
 
    onClick(event) {
      let action = event.target.dataset.action;
      if (action) {
        this[action]();
      }
    };
  }
 
  new Menu(menu); //pass element id="menu"
</script>

(*) Note that this.onClick is bound to this. That’s important, because otherwise this inside it would reference the DOM element (elem), not the Menu object, and this[action] would not be what we need.


