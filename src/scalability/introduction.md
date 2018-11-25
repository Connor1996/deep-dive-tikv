# Introduction

In the database field, scalability is the term we use to describe the capability of a system to handle a growing amount of work. For example, even if a system is working reliably and fast today, it doesn't mean it will necessarily work well in the future. One common reason for degradation is the increased load which exceeds the system can process. Nowadays, the speed of data growth far beyond our imagination, so scalability is a substantial part for the design of a database.

A system whose performance improves after adding hardware, proportionally to the capacity added, is said to be a scalable system. TiKV is a highly scalable key-value store, especially comparing with other stand-alone key-value stores like RocksDB and LevelDB. This chapters we will talk about the two main ways of scaling, horizontal and vertical, and how TiKV provide strong scalability based on Raft.
