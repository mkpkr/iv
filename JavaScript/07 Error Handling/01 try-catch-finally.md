What happens if you call JSON.parse(json) and the JSON is malformed?

try {
  lalala; // error, variable is not defined!
} catch (err) {

  if (err instanceof ReferenceError) {
    alert(err.name); // ReferenceError
    alert(err.message); // lalala is not defined
    alert(err.stack); // ReferenceError: lalala is not defined at (...call stack)
 
    // Can also show an error as a whole the error is converted to string as "name: message"
    alert(err); // ReferenceError: lalala is not defined
  } else {
    
  }
} finally {

}

catch is optional if using finally, and vice versa


Optional catch Binding

This is a recent addition to the language. Old browsers may need polyfills.

If we don’t need error details, catch may omit it:
 
try {
  // ...
} catch { // <-- without (err)
  // ...
}

