---
title:  "07 - Concepts of HBase"
date:   2017-01-19 11:30:23
categories: [Hadoop]
tags: [hbase]
reading_time: 15 min
---

**Introduction**

HBase is Open Source, Distributed, Versioned, Non-Relational, column-oriented Database, built on top of Hadoop File System, Horizontally Scalable, fault-tolerant and has easy Java API for client access.

It has below basic components. We will discuss one by one.

![img](https://i.imgur.com/FBQgDWD.png)

Region assignment, DDL (create, delete tables) operations are handled by the HBase Master process (HMaster). 

![img](https://i.imgur.com/Fi2PG25.png)

Region servers serve data for reads and writes. When accessing data, clients communicate with HBase RegionServers directly.

HBase Tables are divided horizontally by row key range into “Regions.” A region contains all rows in the table between the region’s start key and end key. Regions are assigned to the nodes in the cluster, called “Region Servers,” and these serve data for reads and writes. A region server can serve about 1,000 regions.

![img](https://i.imgur.com/WhZ05sx.png)

Zookeeper, which is part of HDFS, maintains a live cluster state and provides server failure notification. 

The ZooKeeper maintains ephemeral nodes for active sessions via heartbeats.

![img](https://i.imgur.com/g321t7S.png)

Each Region Server creates an ephemeral node. The HMaster monitors these nodes to discover available region servers, and it also monitors these nodes for server failures. HMasters compete with each other to create an ephemeral node. Zookeeper determines the first one and uses it to make sure that only one master is active. The active HMaster sends heartbeats to Zookeeper, and the inactive HMaster listens for notifications of the active HMaster failure.

If a region server or the active HMaster fails to send a heartbeat, the session is expired and the corresponding ephemeral node is deleted. Listeners for updates will be notified of the deleted nodes. The active HMaster listens for region servers, and will recover region servers on failure. The Inactive HMaster listens for active HMaster failure, and if an active HMaster fails, the inactive HMaster becomes active.

![img](https://i.imgur.com/Vgx06G2.png)

There is a special HBase Catalog table called the META table, which holds the location of the regions in the cluster. ZooKeeper stores the location of the .META. table.

This is what happens the first time a client reads or writes to HBase:

1. The client gets the Region server that hosts the META table from ZooKeeper.
2. The client will query the .META. server to get the region server corresponding to the row key it wants to access. The client caches this information along with the .META. table location.
3. It will get the Row from the corresponding Region Server.

For future reads, the client uses the cache to retrieve the META location and previously read row keys. Over time, it does not need to query the META table, unless there is a miss because a region has moved; then it will re-query and update the cache.

![img](https://i.imgur.com/9dWK8mR.png)

**HBase Meta Table**

- This META table is an HBase table that keeps a list of all regions in the system.

- The .META. table is like a b tree.

- The META table structure is as follows:

  \- Key: region start key,region id

  \- Values: RegionServer

  ![img](https://i.imgur.com/cha9Nde.png)

**Region Server Components**

A Region Server runs on an HDFS data node and has the following components:

- WAL: Write Ahead Log is a file on the distributed file system. The WAL is used to store new data that hasn't yet been persisted to permanent storage; it is used for recovery in the case of failure.
- BlockCache: is the read cache. It stores frequently read data in memory. Least Recently Used data is evicted when full.
- MemStore: is the write cache. It stores new data which has not yet been written to disk. It is sorted by key before writing to disk. There is one MemStore per column family per region.
- Hfiles store the rows as sorted KeyValues on disk.

![img](https://i.imgur.com/s49B2eg.png)

**HBase Write**

When the client issues a Put request

1. write the data to the write-ahead log, Edits are appended to the end of the WAL file that is stored on disk
2. Then it is placed in the MemStore and the put request acknowledgement returns to the client.

    ![img](https://i.imgur.com/mRjhNqP.png)

**HBase Region Flush**

When the MemStore accumulates enough data, the entire sorted set is written to a new HFile in HDFS. This is a sequential write. It is very fast, as it avoids moving the disk drive head. HBase uses multiple HFiles per column family, which contain the actual cells, or KeyValue instances. These files are created over time as KeyValue edits sorted in the MemStores are flushed as files to disk.

Note that this is one reason why there is a limit to the number of column families in HBase. There is one MemStore per CF per region; when one is full, they all flush. It also saves the last written sequence number so the system knows what was persisted so far.

The highest sequence number is stored as a meta field in each HFile, to reflect where persisting has ended and where to continue. On region startup, the sequence number is read, and the highest is used as the sequence number for new edits.

![img](https://i.imgur.com/h6LvZd6.png)

**HBase HFile Structure**

An HFile contains a multi-layered index which allows HBase to seek to the data without having to read the whole file. The multi-level index is like a b+tree:

- Key value pairs are stored in increasing order
- Indexes point by row key to the key value data in 64KB “blocks”
- Each block has its own leaf-index
- The last key of each block is put in the intermediate index
- The root index points to the intermediate index

The trailer points to the meta blocks, and is written at the end of persisting the data to the file. The trailer also has information like bloom filters and time range info. **Bloom filters** help to skip files that do not contain a certain row key. The time range info is useful for skipping the file if it is not in the time range the read is looking for.

![img](https://i.imgur.com/FgT0Gd6.png)

**HFile Index**

![img](https://i.imgur.com/Uxn0D2n.png)

**HBase Read Merge**

We have seen that the KeyValue cells corresponding to one row can be in multiple places, row cells already persisted are in Hfiles, recently updated cells are in the MemStore, and recently read cells are in the Block cache. So when you read a row, how does the system get the corresponding cells to return? 

![img](https://i.imgur.com/xdMRgj7.png)

As discussed earlier, there may be many HFiles per MemStore, which means for a read, multiple files may have to be examined, which can affect the performance. This is called read amplification.

Minor compaction reduces the number of storage files by rewriting smaller files into fewer but larger ones, performing a merge sort.

Major compaction merges and rewrites all the HFiles in a region to one HFile per column family, and in the process, drops deleted or expired cells. This improves read performance; however, since major compaction rewrites all of the files, lots of disk I/O and network traffic might occur during the process. This is called write amplification.

Major compactions can be scheduled to run automatically. Due to write amplification, major compactions are usually scheduled for weekends or evenings.

**Regions**

![img](https://i.imgur.com/4fzYvoR.png)

Initially there is one region per table. When a region grows too large, it splits into two child regions. 

Then the split is reported to the HMaster. For load balancing reasons, the HMaster may schedule for new regions to be moved off to other servers.

![img](https://i.imgur.com/pNrHggk.png)

All writes and Reads are to/from the primary node. HDFS replicates the WAL and HFile blocks. HFile block replication happens automatically. HBase relies on HDFS to provide the data safety as it stores its files

![img](https://i.imgur.com/sDDEipd.png)

So how does HBase recover the MemStore updates not persisted to HFiles?

![img](https://i.imgur.com/tGXhSth.png)

**Problems in HBase**

1. WAL replay slow
2. Slow complex crash recovery
3. Major Compaction I/O storms

Basic Commands

```java
$ ./bin/hbase shell
hbase(main):001:0> create 'test', 'cf'
hbase(main):002:0> list 'test'
hbase(main):003:0> put 'test', 'row1', 'cf:a', 'value1'
hbase(main):004:0> put 'test', 'row2', 'cf:b', 'value2'
hbase(main):005:0> put 'test', 'row3', 'cf:c', 'value3'
hbase(main):006:0> scan 'test'
ROW                                      COLUMN+CELL
 row1                                    column=cf:a, timestamp=1421762485768, value=value1
 row2                                    column=cf:b, timestamp=1421762491785, value=value2
 row3                                    column=cf:c, timestamp=1421762496210, value=value3
3 row(s) in 0.0230 seconds
hbase(main):007:0> get 'test', 'row1'
hbase(main):008:0> disable 'test'
hbase(main):011:0> drop 'test'
hbase(main):011:0> quit
```

Pseudo-distributed mode means that HBase still runs completely on a single host, but each HBase daemon (HMaster, HRegionServer, and ZooKeeper) runs as a separate process. 

The **HMaster** server controls the HBase cluster. You can start up to 9 backup HMaster servers, which makes 10 total HMasters, counting the primary.

The **HRegionServer** manages the data in its StoreFiles as directed by the HMaster. Generally, one HRegionServer runs per node in the cluster.You can run 99 additional RegionServers that are not a HMaster or backup HMaster, on a server

Distributed Cluster Demo Architecture:

| Node Name          | Master | ZooKeeper | RegionServer |
| ------------------ | ------ | --------- | ------------ |
| node-a.example.com | yes    | yes       | no           |
| node-b.example.com | backup | yes       | yes          |
| node-c.example.com | no     | yes       | yes          |

node-b

```java
$ jps
15930 HRegionServer
16194 Jps
15838 HQuorumPeer
16010 HMaster
```

**ZooKeeper Process Name**

The `HQuorumPeer` process is a ZooKeeper instance which is controlled and started by HBase. If you use ZooKeeper this way, it is limited to one instance per cluster node, , and is appropriate for testing only. If ZooKeeper is run outside of HBase, the process is called `QuorumPeer`

**Keep Configuration In Sync Across the Cluster**

When running in distributed mode, after you make an edit to an HBase configuration, make sure you copy the content of the *conf/* directory to all nodes of the cluster. HBase will not do this for you.

Limits on Number of Files and Processes (ulimit -n) : 1024, can be raised more, like 10240

Each ColumnFamily has at least one StoreFile, and possibly more than six StoreFiles if the region is under load.

```java
Potential Number of Open Files = (StoreFiles per ColumnFamily) x (regions per RegionServer)
```

HBase has two run modes: standalone and distributed

In standalone mode (default), HBase does not use HDFS — it uses the local filesystem instead — and it runs all HBase daemons and a local ZooKeeper all up in the same JVM.

Distributed mode can be subdivided into Pseudo distributed (all daemons runs in a single node, can run against the local filesystem or it can run against an instance of HDFS) and Fully distributed (daemons are spread across all nodes in the cluster)

HBase uses WAL to recover the memstore data that has not been flushed to disk in case of an RS failure. These WAL files should be configured to be slightly smaller than HDFS block (by default a HDFS block is 64Mb and a WAL file is ~60Mb).

HBase also has a limit on the number of WAL files, designed to ensure there’s never too much data that needs to be replayed during recovery.For example, with 16Gb RS heap, default memstore settings (0.4), and default WAL file size (~60Mb), 16Gb*0.4/60, the starting point for WAL file count is ~109.

A row in HBase consists of a row key and one or more columns with values associated with them. **Rows are sorted alphabetically by the row key as they are stored**. For this reason, the design of the row key is very important. **The goal is to store data in such a way that related rows are near each other.**

A new column qualifier (column_family:column_qualifier) can be added to an existing column family at any time.

A namespace is a logical grouping of tables analogous to a database in relation database systems. A namespace can be created, removed or altered.

```java
#Create a namespace
create_namespace 'my_ns'
```

```java
#create my_table in my_ns namespace
create 'my_ns:my_table', 'fam'
#drop namespace
drop_namespace 'my_ns'
#alter namespace
alter_namespace 'my_ns', {METHOD => 'set', 'PROPERTY_NAME' => 'PROPERTY_VALUE'}
```

There are two predefined special namespaces:

- hbase - system namespace, used to contain HBase internal tables
- default - tables with no explicit specified namespace will automatically fall into this namespace

```java
#namespace=foo and table qualifier=bar
create 'foo:bar', 'fam'

#namespace=default and table qualifier=bar
create 'bar', 'fam'
```

HBase does not modify data in place, and so deletes are handled by creating new markers called *tombstones*. These tombstones, along with the dead values, are cleaned up on major compactions.

