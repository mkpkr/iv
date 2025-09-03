09:40

	• Mocha – the core framework: it provides common testing functions including describe and it and the main function that runs tests.
	• Chai – the library with many assertions e.g. assert.equal.
	• Sinon – a library to spy over functions, emulate built-in functions and more.


Imagine we want to write a pow function that just does ** exponent operator.

test.js:
```
describe("pow", function() {
 
  it("2 raised to power 3 is 8", function() {
    assert.equal(pow(2, 3), 8);
  });
 
  it("3 raised to power 4 is 81", function() {
    assert.equal(pow(3, 4), 81);
  });
 
});
```

test.html:
```
<!DOCTYPE html>
<html>
<head>
  <!-- add mocha css, to show results -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/mocha/3.2.0/mocha.css">
  
  <!-- add mocha framework code -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/mocha/3.2.0/mocha.js"></script>
  <script>
    mocha.setup('bdd'); // minimal setup
  </script>
  
  <!-- add chai -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/chai/3.5.0/chai.js"></script>
  <script>
    // chai has a lot of stuff, let's make assert global
    let assert = chai.assert;
  </script>
</head>

<body>

  <script>
    function pow(x, n) {
      /* function code is to be written, empty now */
    }
  </script>
 
  <!-- the script with tests (describe, it...) -->
  <script src="test.js"></script>
 
  <!-- the element with id="mocha" will contain test results -->
  <div id="mocha"></div>
 
  <!-- run tests! -->
  <script>
    mocha.run();
  </script>

</body>
</html>
```

Here is the output if we hardcoded pow to return 8:

![[Pasted image 20250903161037.png]]