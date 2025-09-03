	• The prototype property of a constructor function has worked since very ancient times. It’s the oldest way to create objects with a given prototype.
	
	• In 2012, Object.create appeared in the standard. It gave the ability to create objects with a given prototype, but did not provide the ability to get/set it. Some browsers implemented the non-standard __proto__ accessor that allowed the user to get/set a prototype at any time, to give more flexibility to developers.
	
	• In 2015, Object.setPrototypeOf and Object.getPrototypeOf were added to the standard, to perform the same functionality as __proto__. As __proto__ was de-facto implemented everywhere, it was kind-of deprecated and made its way to the Annex B of the standard, that is: optional for non-browser environments.
	
	• In 2022, it was officially allowed to use __proto__ in object literals {...} (moved out of Annex B), but not as a getter/setter obj.__proto__ (still in Annex B).

For more information on these decisions, see: https://javascript.info/prototype-methods#brief-history
