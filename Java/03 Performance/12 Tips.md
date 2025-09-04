- Don’t allocate an object you don’t need to.

In the following snippet, we’ve added a check to see if the logging object will do anything with a debug log message. This kind of check is called a loggability guard.

If the logging subsystem isn’t set up for debug logs, this code will never construct the log message, saving the cost of the call to currentTimeMillis() and the string concatenation (or construction of the StringBuilder object in newer compilers) used for the log message:

```java
if (log.isDebugEnabled()) {
    log.debug("Useless log at: "+ System.currentTimeMillis());
}
```

- Remove a debug log message if you’ll never need it.

But if the debug log message is truly useless, we can save a couple of processor cycles (the cost of the loggability guard) by removing the code altogether. This cost is trivial and will get lost in the noise of the rest of the performance profile, but if it genuinely isn’t needed, take it out.