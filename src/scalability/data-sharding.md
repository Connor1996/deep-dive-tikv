# Data sharding

## What is the partition

For fault tolerance, TiKV replicates multiple copies of the same data on different nodes based on Raft. But for that large dataset or high query throughput, all data is managed by one Raft state machine is not efficient and flexible, so we need to break the data up into partitions, also known as `sharding`. And we call a partition here is called a `Region` in TiKV.

With partitioning, we can move partitions to balance the load among all nodes of a cluster. For how to decide which partitions to store on which nodes isn't covered here. It will be discussed later in chapter related to PD.

## Partitioning

Different partitions can be placed on different nodes, thus a large dataset can be distributed across many disks, and the query load can be distributed across many processors. But partitioning suffers from hot spot issue that high load may end up on one partition which makes only one node busy but other nodes idle. To avoid that, we can simply assign records to nodes randomly, but when reading a particular item there is no fast way of knowing which node is on. 

TiKV uses simple key-value data model, here are two general ways to map keys to partitions:

### Partitioning by hash of key

One way of partitioning is to assign a continuous range of hashes of keys to each partition. The partition boundaries can be evenly spaced, or they can be chosen by some kind of algorithm like [consistent hashing](https://en.wikipedia.org/wiki/Consistent_hashing). Due to hash, it can distribute keys fairly among the partitions which effectively reduces hot spots though not totally avoids. Whereas, it is unable to do efficient range queries comparing with partitioning by key range.

### Partitioning by key range

The other way of partitioning is to assign a continuous range of keys to each partition. It needs to record the boundaries between the ranges to determine which partition contains a given key, then requests can be sent to the appropriate node. One key feature of key range partition is friendly to range scans. However, the downside of the sorted order is that sequential reads and writes can lead to hot spots. 

And it is the partitioning approach that TiKV uses. The main reason to choose it is scan-friendly, meanwhile, for the split or merge of Region, TiKV only needs to change meta-information about the range of the Region which avoids moving actual data in a large extent.
