Deadlocks occur when two threads aquire the same locks in a different order.

```java
public boolean naiveTransferTo(Account other, int amount) {
    // Check to see amount > 0, throw if not
    synchronized (this) {
        if (balance >= amount) {
            balance = balance - amount;
            synchronized (other) {
                other.rawDeposit(amount);
            }
            return true;
        }
    }
    return false;
}
```

What if 2 accounts both attempt to deposit money into the other's?

	• Thread 1 locks account A and then account B
	• Thread 2 locks account B and then account A

Which could look like this:

	• Thread 1 locks account A 
	• Thread 2 locks account B 
	• Thread 1 attempts to lock account B and blocks
	• Thread 2 attempts to lock account A and blocks

To avoid this, every thread must acquire locks in the same order.

One way to do this is by creating an ordering on the threads—say, by introducing a unique account number and implementing the rule: “acquire the lock corresponding to the lowest account ID first.”
 
Note: For objects that don’t have numeric IDs, we will need to do something different, but the general principle of using an unambiguous total order still applies.

This can be done with synchronized, as shown below but it involves two almost identical blocks, the first if this account id is lower: synchonized this then other, the second if other account id is lower: syncrhonized other then this.

See *Lock & Condition* for an example of this solved with Lock object (and only one block required). See *Blocking Queue* for a lock-free implementation.

```java
public boolean safeTransferTo(final Account other, final int amount) {
	//error checks omitted

	if (accountId < other.getAccountId()) {
		synchronized (this) {
			if (balance >= amount) {
				balance = balance - amount;
				synchronized (other) {
					other.rawDeposit(amount);
				}
				return true;
			}
		}
		return false;
	} else {
		synchronized (other) {
			synchronized (this) {
				if (balance >= amount) {
					balance = balance - amount;
					other.rawDeposit(amount);
					return true;
				}
			}
		}
		return false;
	}
}
```
