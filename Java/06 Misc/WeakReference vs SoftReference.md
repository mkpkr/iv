A `**WeakReference**` is a reference that **does not protect a referenced object from collection by GC**. i.e. garbage collects object when there are no Strong or Soft referencess.

 If GC finds that an object is weakly reachable (reachable only through weak references), it'll clear the weak references to that object immediately. As such, they're good for keeping a reference to an object for which your program also keeps (strongly referenced) "associated information" somewere, like a wrapper for an object or some metadata etc. Anything that makes no sense to keep after the object it is associated with is GC-ed.

`WeakReference` objects can be used to create pointers to objects in your system while still allowing those objects to be reclaimed by the garbage-collector once they pass out of scope. They are good for canonical mapping (mapping via proxy object, which is responsible for giving back the object or null).

**`SoftReference`s** on the other hand are eligible for collection by garbage collector, but probably won't be collected until its memory is needed. i.e. garbage collects before OutOfMemoryError.

They are good for **caching external, re-creatable resources** as the GC typically delays clearing them. It is guaranteed though that all `SoftReference`s will get cleared before OutOfMemoryError is thrown, so they theoretically can't cause an OOME.

Typical use case example is keeping a parsed form of a contents from a file. You'd implement a system where you'd **load a file, parse it, and keep a `SoftReference`** to the root object of the parsed representation. Next time you need the file, you'll try to retrieve it through the `SoftReference`.