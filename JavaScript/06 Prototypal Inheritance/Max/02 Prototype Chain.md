If a method does not exist, the prototype will be searched, and its prototype, until we reach the global Object.prototype.

Note that this doesn't mean all properties/functions on Object are available on all objects - Object is not the last in the chain, it is Object.prototype.

This means that any properties/functions on Object are not inherited, only ones on Object.prototype.

![[Pasted image 20250903153247.png]]