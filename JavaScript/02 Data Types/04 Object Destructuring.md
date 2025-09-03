let options = {
  title: "Menu",
  width: 100,
  height: 200
};

let {title, width, height} = options;

The order does not matter.

If we want to assign a property to a variable with another name, for instance, make options.width go into the variable named w, then we can set the variable name using {sourceProperty: targetVariable}

let {width: w, height: h, title} = options;
 
alert(title);  // Menu
alert(w);      // 100
alert(h);      // 200

Rest ...

Similar to  Array Destructuring but creates new object for remaining properties.

let person = {name: 'mike', age: 36, sex: 'male'}
let {name, ...other} = person;

console.log(name); //"mike"
console.log(other); //Object { age: 36, sex: "male" }


Smart Function Parameters

 
function showMenu({ title = "Untitled", width = 200, height = 100, items = [] }) {
  alert( `${title} ${width} ${height}` ); // My Menu 200 100
  alert( items ); // Item1, Item2
}

let options = {
  title: "My menu",
  items: ["Item1", "Item2"]
};

 
showMenu(options);


Default Values

let options = {
  title: "Menu"
};

let {width: w = 100, height: h = 200, title} = options;


Nested Destructuring

const { fri } = restaurant.openingHours;
console.log(fri); //{open: 11, close: 23}

const {
  fri: { open, close },
} = restaurant.openingHours;

console.log(open, close); //11 23


---
 const turns = [
   { square: { row: rowIndex, col: colIndex }, player: currentPlayer },
    ...prevTurns,
  ];

     
...


  for (const turn of turns) {
    const { square, player } = turn;
    const { row, col } = square;

    update(gameBoard[row][col]);
  }
