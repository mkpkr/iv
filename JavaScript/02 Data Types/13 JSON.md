	• JSON.stringify -convert objects into JSON.
		○ let json = JSON.stringify(value[, replacer, space])
			▪ replacer - Array of properties to encode or a mapping function(key, value).
			▪ space - Amount of space to use for formatting
			
	• JSON.parse - convert JSON back into an object.
		○ let value = JSON.parse(str, [reviver]);
			▪ reviver -  function(key,value) that will be called for each (key, value) pair and can transform the value.

Like toString() for string conversion, an object may provide method toJSON() for to-JSON conversion. 

JSON.stringify automatically calls it if available.

JSON supports following data types:
 
	• Objects { ... }
	• Arrays [ ... ]
	• Primitives:
		○ strings
		○ numbers
		○ true/false
		○ null

Some JavaScript-specific object properties are skipped by JSON.stringify.
 
	• Function properties (methods)
	• Symbolic keys and values
	• Properties that store undefined
