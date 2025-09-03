Note this is an example from React (setUserInput is changing state) but uses JavaScript dynamic property syntax.

We hada state array with a field for each input, and multiple handlers for each input field, so whenever a user entered text, the specific handler was called for the input field they edited, and that field in the array was called.

But instead of separate functions for each input that may change, we instead can use a shared handleChange(), which accepts an identifier to idenify which input field changed, i.e. what array item should be updated.

  function handleChange(inputIdentifier, newValue) {
    setUserInput((prevUserInput) => {
      return {
        ...prevUserInput, //take existing object
        [inputIdentifier]: newValue, //and apply new value only the property name provided
      };
    });
  }