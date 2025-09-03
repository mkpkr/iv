In JavaScript, objects have a special hidden property `[[Prototype]]`, that is either null or references another object. That object is called “a prototype”:

![[Pasted image 20250903153424.png]]

When we read a property from object, and it’s missing, JavaScript automatically takes it from the prototype.

The property `[[Prototype]]` is internal and hidden, but there are many ways to set it.

Note on for…in loop & Iteration

	• The for..in loop iterates over inherited properties too.
	• Almost all other key/value-getting methods, such as Object.keys, Object.values and so on ignore inherited properties.
They only operate on the object itself. Properties from the prototype are not taken into account.

![[Pasted image 20250903153439.png]]