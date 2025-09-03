	• new Map()
	• map.set(key, value)
	• map.get(key)
	• map.has(key)
	• map.delete(key)
	• map.clear()
	• map.size

Do not use map[key]: although map[key] also works, e.g. we can set map[key] = 2, this is treating map as a plain JavaScript object, so it implies all corresponding limitations (only string/symbol keys and so on).

So we should use map methods: set, get and so on.

Iteration

	• map.keys() – returns an iterable for keys
		○ for (let key of recipeMap.keys()) {
	
	• map.values() – returns an iterable for values
		○ for (let amount of recipeMap.values()) {
	
	• map.entries() – returns an iterable for entries [key, value]
		○ used by default in for..of
		○ for (let entry of recipeMap) {
	
	• Map.forEach
		○ recipeMap.forEach( (value, key, map) => { ... };

The iteration goes in the same order as the values were inserted.

Objects as Keys

Using objects as keys is one of the most notable and important Map features. The same does not count for Object. 

String as a key in Object is fine, but we can’t use another Object as a key in Object.

let john = { name: "John" };
let visitsCountMap = new Map();
visitsCountMap.set(john, 123); // john is the key for the map
alert( visitsCountMap.get(john) ); // 123


Key Comparison

To test keys for equivalence, Map uses the algorithm SameValueZero. It is roughly the same as strict equality `===`, but the difference is that NaN is considered equal to NaN. So NaN can be used as the key as well.

This algorithm can’t be changed or customized.


Convert Between Object and Map

	• Object.entries()
		○ Map from Object
	• Object.fromEntries()
		○ Object from Map
