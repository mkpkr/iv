Object Constructor

let user = new Object(); // "object constructor" syntax 
let user = {}; // "object literal" syntax

Object Literal

const mike = {
    firstName: 'Mike',
    lastName: 'Parker,
    age: 32,
    friends: ['Jordan', 'Warren']
}
 
console.log(mike.lastName);
console.log(mike['lastName']);
 
//add/change property
mike.location = 'USA';
mike['location'] = 'USA';


Shorthand Properties


const addMovieHandler = () => {
  const title = document.getElementById('title').value;
  ...

  const newMovie = {
    info: {
      title, //if property name is same as an existing variable, don't need to say title: title
      ...
    },

    ...
  };
};


Dynamic Properties

const addMovieHandler = () => {
  const title = document.getElementById('title').value;
  const extraName = document.getElementById('extra-name').value;
  const extraValue = document.getElementById('extra-value').value;

  const newMovie = {
    info: {
      title,
      [extraName]: extraValue
    },
    ...
  };

  ...
};


Methods

const mike = {
    firstName: 'Mike',
    lastName: 'Parker,
    birthYear: 1988,
    friends: ['Jordan', 'Warren']
 
    calcAge: function() {
        return 2020 - this.birthYear;
    }
}

Computed Properties

let fruit = 'apple';
let bag = {
  [fruit + 'Computers']: 5 // bag.appleComputers = 5
};


Using Function Arguments

function makeUser(name, age) {
  return {
    name: name,
    age: age,
    // ...other properties
  };
}

in

let user = { name: "John", age: 30 };
alert( "age" in user ); // true, user.age exists

On the left side of in there must be a property name. That’s usually a quoted string.
 
If we omit quotes, that means a variable should contain the actual name to be tested. For instance:

let user = { age: 30 };
 
let key = "age";
alert( key in user ); // true, property "age" exists

We could also compare the key against undefined. There is one situation they will give different results, which is if you have the property but it is explicitly set to undefined. This is very rare though so both in and compare to undefined work.

Iterate Keys: for..in

for (key in user) {
  alert( key );
  alert( user[key] );
}

Property Order

Are objects ordered? Integer properties are sorted, others appear in creation order.  
 
let codes = {
  49: "Germany",
  41: "Switzerland",
  44: "Great Britain",
  1: "USA"
};
 
for (let code in codes) {
  alert(code); // 1, 41, 44, 49
}
 
If we want to ensure creation order when looping we would have to make them non integer:
 
let codes = {
  "+49": "Germany",
  "+41": "Switzerland",
  "+44": "Great Britain",
  "+1": "USA"
};
 
for (let code in codes) {
  alert( +code ); // 49, 41, 44, 1
}

I think this works because + converts string to number:
The following prints 1:

let a = "+1"
alert(+a)


Object.assign()

Note we can also just use shorter spread syntax: Spread

Clone Object
 
let user = {
  name: "John",
  age: 30
};
 
let clone = Object.assign({}, user);
 
alert(clone.name); // John
alert(clone.age); // 30


Copy Properties to Object
 
let user = { name: "John" };
 
let permissions1 = { canView: true };
let permissions2 = { canEdit: true };
 
// copies all properties from permissions1 and permissions2 into user
Object.assign(user, permissions1, permissions2);
 
// now user = { name: "John", canView: true, canEdit: true }
alert(user.name); // John
alert(user.canView); // true
alert(user.canEdit); // true


If the copied property name already exists, it gets overwritten.


Deep Clone: structuredClone

Object.assign will do shallow clone – references will be copied for objects so fields will point to same value as other object's fields.

structuredClone(object) clones the object with all nested properties.

let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};
 
let clone = structuredClone(user);
 
alert( user.sizes === clone.sizes ); // false, different objects
 
// user and clone are totally unrelated now
user.sizes.width = 60;    // change a property from one place
alert(clone.sizes.width); // 50, not related


Note: Function properties aren’t supported.
