should

```
chai.should();
 
foo.should.be.a('string');
foo.should.equal('bar');
foo.should.have.lengthOf(3);
tea.should.have.property('flavors').with.lengthOf(3);
```

expect

```
let expect = chai.expect;
 
expect(foo).to.be.a('string');
expect(foo).to.equal('bar');
expect(foo).to.have.lengthOf(3);
expect(tea).to.have.property('flavors').with.lengthOf(3);
```

assert

```
let assert = chai.assert;
 
assert.typeOf(foo, 'string');
assert.equal(foo, 'bar');
assert.strictEqual(value1, value2) – checks the strict equality value1 === value2.
assert.notEqual, assert.notStrictEqual – inverse checks to the ones above.
assert.isTrue(value) – checks that value === true
assert.isFalse(value) – checks that value === false
assert.lengthOf(foo, 3)
assert.property(tea, 'flavors');
assert.lengthOf(tea.flavors, 3);
assert.isNaN(doSomething(2, -1));
```