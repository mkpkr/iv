
- Cassandra
	- Works well when writes exceed reads
	- SSTables are append only, does not need any seeks to write data
	- Does not work well with many deletes – it stores them as tombstones (until compaction) that affects read performance