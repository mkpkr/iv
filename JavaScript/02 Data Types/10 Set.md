
	• new Set([iterable]) 
	• set.add(value)
	• set.delete(value)
	• set.has(value)
	• set.clear()
	• set.size

Iteration

	• for..of
		○ for (let value of set)
	• forEach()
		○ set.forEach((value, valueAgain, set) => {

The iteration goes in the same order as the values were inserted.

Map Compatibility

Note the funny thing. The callback function passed in forEach has 3 arguments: a value, then the same value valueAgain, and then the target object. Indeed, the same value appears in the arguments twice.
 
That’s for compatibility with Map where the callback passed forEach has three arguments. Looks a bit strange, for sure. But this may help to replace Map with Set in certain cases with ease, and vice versa.

The same methods Map has for iterators are also supported:
 
	• set.keys()
		○ returns an iterable object for values
	• set.values()
		○ same as set.keys(), for compatibility with Map
	• set.entries()
		○ returns an iterable object: [value, value], double entry exists for compatibility with Map
 

