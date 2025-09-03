function hash() {
  return arguments.join();
}
 
This does not work because join() is an array method - we are calling hash(arguments), and arguments object is both iterable and array-like, but not a real array.
 
The trick is to borrow a method from Array:
 
function hash() {
  alert( [].join.call(arguments) ); // 1,2
}
 
hash(1, 2);
 
 
 
Another Example
 
function findS() {
  return Array.prototype.slice.call(arguments).filter( arg => arg.includes('s'))
}

console.log(findS("Tesla", "Mitsubishi", "Ford"))
