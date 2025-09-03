Instead of needing to lock the whole structure when making a change, it’s only necessary to lock the hash chain (aka bucket) that’s being altered or read.

This technique is known as lock striping, and it enables multiple threads to access the map, provided they are operating on different chains (buckets).

It does use considerably more resources than a plain HashMap and will have worse throughput due to the synchronization of some operations. As we’ll discuss in chapter 7, however, those inconveniences are nothing when compared to the possibility of a race condition leading to Lost Update or an infinite loop.