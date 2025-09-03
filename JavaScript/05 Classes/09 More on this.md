Remember that this is bound to whatever the method was called on, and in the case of an event listener it is the DOM element bound to this.

Which means if you want to register an event handler within a class method and call some other method as a result of the event, the following will not work:


class ShoppingCart {
  items = [];

  listItems() {
    console.log(this.items);   // within listItems, this now refers to the button that was clicked, not the ShoppingCart instance
  }

  blah() {
    listButton = document.getElementById("listBtn");
    listButton.addEventListener('click', this.listItems);
  }
}

To get around that, we can either bind:

  something() {
    listButton = document.getElementById("listBtn");
    listButton.addEventListener("click", this.listItems.bind(this));
  }


Or use an arrow function, remember that arrow functions use the this of the surrounding function:


  something() {
    listButton = document.getElementById("listBtn");
    listButton.addEventListener("click", () => this.listItems());   // within the anonymouse function, this refers to the the ShoppingCart instance
  }


We could also choose use the arrow function in listItems:

  listItems = () => {       // within the anonymouse function, this refers to the the ShoppingCart instance
    console.log(this.items);
  }

  something() {
    listButton = document.getElementById("listBtn");
    listButton.addEventListener("click", this.listItems);
  }



