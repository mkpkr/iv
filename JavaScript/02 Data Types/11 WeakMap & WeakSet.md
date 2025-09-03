WeakMap

The first difference between Map and WeakMap is that keys must be objects, not primitive values.

Now, if we use an object as the key in it, and there are no other references to that object – it will be removed from memory (and from the map) automatically.

let john = { name: "John" };
 
let weakMap = new WeakMap();
weakMap.set(john, "...");

john = null; // overwrite the reference
// john is removed from memory!

WeakMap has only the following methods:

	• weakMap.set(key, value)
	• weakMap.get(key)
	• weakMap.delete(key)
	• weakMap.has(key)

The main area of application for WeakMap is an additional data storage.

If we’re working with an object that “belongs” to another code, maybe even a third-party library, and would like to store some data associated with it that should only exist while the object is alive – then WeakMap is exactly what’s needed.

Another use case is a cache that we do not need to clean when the key is no longer in use.

WeakSet

Behaves similarly to WeakMap, being “weak” it also serves as additional storage. But not for arbitrary data, rather for “yes/no” facts. 

A membership in WeakSet may mean something about the object.

