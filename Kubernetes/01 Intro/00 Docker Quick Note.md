Earlier versions of Java have not been very good at honoring the quotas specified for a Docker container using Linux cgroups; they simply ignored these settings. So, instead of allocating memory inside the JVM in relation to the memory available in the container, Java allocated memory as if it had access to all the memory in the Docker host. When trying to allocate more memory than allowed, the Java container was killed by the host with an "out of memory" error message. In the same way, Java allocated CPU-related resources such as thread pools in relation to the total number of available CPU cores in the Docker host, instead of the number of CPU cores that were made available for the container JVM was running in.

In Java SE 9, initial support for container-based CPU and memory constraints was provided, much improved in Java SE 10.

Let's look at how Java SE 16 responds to limits we set on a container it runs in!

# jshell

In the following tests, we will run the Docker engine inside a virtual machine on a MacBook Pro, acting as the Docker host. The Docker host is configured to use 12 CPU cores and 16 GB of memory.

Let's start by finding out how many available processors, that is CPU cores, Java sees without applying any constraints.

echo 'Runtime.getRuntime().availableProcessors()' | docker run --rm -i adoptopenjdk:16 jshell –q

Let's move on and restrict the Docker container to only be allowed to use three CPU cores using the --cpus 3 Docker option, then ask the JVM about how many available processors it sees:

echo 'Runtime.getRuntime().availableProcessors()' | docker run --rm -i --cpus=3 adoptopenjdk:16 jshell –q

In terms of the amount of available memory, let's ask the JVM for the maximum size that it thinks it can allocate for the heap. We can achieve this by asking the JVM for extra runtime information using the -XX:+PrintFlagsFinal Java option and then using the grep command to filter out the MaxHeapSize parameter, like so:

docker run -it --rm adoptopenjdk:16 java -XX:+PrintFlagsFinal | grep "size_t MaxHeapSize"

With no JVM memory constraints, that is not using the JVM parameter -Xmx, Java will allocate one-quarter of the memory available to the container for its heap. So, we expect it to allocate up to 4 GB to its heap.

If we constrain the Docker container to only use up to 1 GB of memory using the Docker option -m=1024M, we expect to see a lower max memory allocation. Running the command:

docker run -it --rm -m=1024M adoptopenjdk:16 java -XX:+PrintFlagsFinal | grep "size_t MaxHeapSize"

Will result in the response 268,435,456 bytes, which equals 268,435,456 / 10242 = 256 MB. 256 MB is one-quarter of 1 GB, so again, this is as expected.

We can, as usual, set the max heap size on the JVM ourselves. For example, if we want to allow the JVM to use 600 MB of the total 1 GB we have for its heap, we can specify that using the JVM option -Xmx600m like:

docker run -it --rm -m=1024M adoptopenjdk:16 java -Xmx600m -XX:+PrintFlagsFinal -version | grep "size_t MaxHeapSize"

The JVM will respond with 629,145,600 bytes = 600 * 10242 = 600 MB, again as expected.

Let's conclude with an "out of memory" test to ensure that this really works!

We'll allocate some memory using jshell in a JVM that runs in a container that has been given 1 GB of memory; that is, it has a max heap size of 256 MB.

First, try to allocate a byte array of 100 MB:

echo 'new byte[100_000_000]' | docker run -i --rm -m=1024M adoptopenjdk:16 jshell -q  

The command will respond with $1 ==>, meaning that it worked fine!

Normally, jshell will print out the value resulting from the command, but 100 MB of bytes all set to zero is a bit too much to print, and so we get nothing.

Now, let's try to allocate a byte array that is larger than the max heap size, for example, 500 MB:

echo 'new byte[500_000_000]' | docker run -i --rm -m=1024M adoptopenjdk:16 jshell -q  

The JVM sees that it can't perform the action since it honors the container settings of max memory and responds immediately with Exception java.lang.OutOfMemoryError: Java heap space. Great!