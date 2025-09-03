We’re going to add even more tests. But before that let’s note that the helper function makeTest and for should be grouped together. We won’t need makeTest in other tests, it’s needed only in for: their common task is to check how pow raises into the given power.

Grouping is done with a nested describe:

```
describe("pow", function() {
 
  describe("raises x to power 3", function() {
 
    function makeTest(x) {
      let expected = x * x * x;
      it(`${x} in the power 3 is ${expected}`, function() {
        assert.equal(pow(x, 3), expected);
      });
    }
 
    for (let x = 1; x <= 5; x++) {
      makeTest(x);
    }
 
  });
});
```

![[Pasted image 20250903161139.png]]