Extends

![[Pasted image 20250903152337.png]]

class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  run(speed) {
    this.speed = speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }

  stop() {
    this.speed = 0;
    alert(`${this.name} stands still.`);
  }
}


class Rabbit extends Animal {
  hide() {
    alert(`${this.name} hides!`);
  }
}

let rabbit = new Rabbit("White Rabbit");



Note: see Static -> Inheritance section for an extended version of this diagram which shows an extra prototypal inheritance.


Any expression is allowed after extends

Class syntax allows to specify not just a class, but any expression after extends.
 
For instance, a function call that generates the parent class:
 
function f(phrase) {
  return class {
    sayHi() { alert(phrase); }
  };
}
 
class User extends f("Hello") {}

new User().sayHi(); // Hello

