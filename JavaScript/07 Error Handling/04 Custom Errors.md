The Error class is built-in, but here’s its approximate code so we can understand what we’re extending:

// The "pseudocode" for the built-in Error class defined by JavaScript itself
class Error {
  constructor(message) {
    this.message = message;
    this.name = "Error"; // (different names for different built-in error classes)
    this.stack = <call stack>; // non-standard, but most environments support it
  }
}


We could extend this class:

class ValidationError extends Error {
  constructor(message) {
    super(message);
    this.name = "ValidationError";
  }
}

//...

if (!user.age) {
  throw new ValidationError("No field: age");
}