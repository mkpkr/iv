 **

Exponent:

2 ** 3 //2^3

== vs ===

	• === is strict 
	• == is loose and performs type coercion (avoid)
		○ 18 == '18'
 
We should use strict.

Note that switch statements are equivalent to using ===

!= vs !==

Similar to above, use strict.

23 != '23'
//false
 
23 !== '23'
//true

Optional Chaining: ?.

The optional chaining ?. stops the evaluation if the value before ?. is undefined or null and returns undefined.

let user = {};
alert( user?.address?.street ); // undefined (no error)

The variable before ?. must be declared. If there’s no variable user at all, then user?.anything triggers an error.

Other variants: ?.() and ?.[]

Call method if it exists:

object.method?.();

Get key on object if object is defined/not null:

object?.[key] );


Nullish Coalescing: ??

a ?? b
	• If a is defined and not null, then a
	• If a is undefined or null, then b

result = (a !== null && a !== undefined) ? a : b;
//can be rewritten as
result = a ?? b;

//provide default value
alert(user ?? "Anonymous");
 
// show the first defined & not null value
alert(firstName ?? lastName ?? nickName ?? "Anonymous");

	• || returns the first truthy value
	• ?? returns the first defined value.

Boolean Coercion

!! converts to boolean:

!!1

!!""