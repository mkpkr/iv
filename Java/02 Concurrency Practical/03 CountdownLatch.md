The `CountDownLatch` is a simple concurrency primitive that provides a consensus barrier—it allows for multiple threads to reach a coordination point and wait until the barrier is released.

This is achieved by providing an int value (the count) when constructing a new instance of `CountDownLatch`.

After that point, two methods are used to control the latch:

- `countDown()`
	- reduces the count by 1
- `await()`
	- causes the calling thread to block until the count reaches 0
	- **it does nothing if the count is already 0 or less**

Consider an application that needs to prepopulate several caches with reference data before the server is ready to receive incoming requests. We can easily achieve this by using a shared latch, a reference to which is held by each cache population thread.

When each cache finishes loading, the Runnable populating it counts down the latch and exits. When all the caches are loaded, the main thread (which has been awaiting the latch opening) can proceed and is ready to mark the service as up and begin handling requests.