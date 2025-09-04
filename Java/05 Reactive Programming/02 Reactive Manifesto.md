![[Pasted image 20250904233103.png]]

The main feature of reactive programming for application-level components allows tasks to be executed asynchronously. Processing streams of events in an asynchronous and nonblocking way is essential for maximizing the use rate of modern multicore CPUs and, more precisely, of the threads competing for their use.

Reactive programming techniques not only have the benefit of being cheaper than threads, but also have a major advantage from developers’ point of view: **they raise the level of abstraction of implementing concurrent and asynchronous applications**, allowing developers to concentrate on the business requirements instead of dealing with typical problems of low-level multithreading issues such as synchronization, race conditions, and deadlocks.

The most important thing is to **never perform blocking operations inside the main event loop**, (I/O-bound operations such as accessing a database or the file system or calling a remote service that may take a long or unpredictable time to complete.).