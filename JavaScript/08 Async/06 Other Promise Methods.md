There are 5 other static methods other than Promise.all

	• Promise.allSettled
		○ This is a recent addition to the language. Old browsers may need polyfills.
		○ Promise.all rejects as a whole if any promise rejects. That’s good for “all or nothing” cases, when we need all results successful to proceed.
		○ Promise.allSettled just waits for all promises to settle, regardless of the result. The resulting array has:
			▪ {status:"fulfilled", value:result} for successful responses,
			▪ {status:"rejected", reason:error} for errors.
 
	• Promise.race
		○ Similar to Promise.all, but waits only for the first settled promise and gets its result (or error).
 
	• Promise.any
		○ Similar to Promise.race, but waits only for the first fulfilled promise and gets its result. If all of the given promises are rejected, then the returned promise is rejected with AggregateError – a special error object that stores all promise errors in its errors property.
 
	• Promise.resolve/reject
		○ Rarely needed in modern code, because Async/await syntax  makes them somewhat obsolete.
		○ We cover them here for completeness and for those who can’t use async/await for some reason.
		○ Promise.resolve(value) creates a resolved promise with the result value.
		○ Promise.reject(error) creates a rejected promise with error.
