Unfortunately, the linear nature of a list is not helpful when making a concurrent implementation.

Even in the case of a linked list, multiple threads attempting to modify the list raise the possibility of contention, for example, in workloads that have a large percentage of append operations.

CopyOnWriteArrayList class. As the name suggests, this type is a replacement for the standard ArrayList class that has been made thread-safe by the addition of copy-on-write semantics.

This means that **any operations that mutate the list will create a new copy of the array backing the list .**

This also means that **any iterators created don’t have to worry about any modifications that they didn’t expect.**

The iterators are guaranteed not to throw ConcurrentModificationException and will not reflect any additions, removals, or changes to the list since the iterator was created—except, of course, that (as usual in Java) the list elements can still mutate. It is only the list that cannot.

This implementation is **usually too expensive for general use** (not just synchronization of mutators – while `ArrayList` will only allocate memory when a resize of the underlying array is required; `CopyOnWriteArrayList` allocates and copies on every mutation)

However this may be a **good option when traversal operations vastly outnumber mutations, and when the programmer does not want the headache of synchronization, yet wants to rule out the possibility of threads interfering with each other.**