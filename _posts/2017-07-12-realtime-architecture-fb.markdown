---
title:  "Realtime architecture of Facebook"
date:   2017-07-12 11:30:23
categories: [Architecture]
tags: [others]
---
![image](http://78.media.tumblr.com/6c137a2cd0011fc190019e869dfd712d/tumblr_inline_oa7oikev7o1uq2h70_540.png)

Scribe (In house):** For data transport. It provides a persistent, distributed messaging system for collecting, aggregating and delivering high volumes of log data with a few seconds of latency and high throughput. Alternative : Kafka

The realtime stream processing systems Puma, Stylus, and Swift read data from Scribe and also write to Scribe. Laser, Scuba, and Hive are data stores that use Scribe for ingestion and serve different types of queries. Laser can also provide data to the products and streaming systems, as shown by the dashed (blue) arrows. 

**Puma:** it is a stream processing system whose applications (apps) are written in a SQL-like language with UDFs (user-defined functions) written in Java.

Unlike traditional relational databases, Puma is optimized for compiled queries, not for ad-hoc analysis.

The output of these stateless Puma apps is another Scribe stream, which can then be the input to another Puma app, any other realtime stream processor, or a data store.

**Swift:** It is a basic stream processing engine which provides checkpointing functionalities for Scribe

**Stylus**: It is a low-level stream processing framework written in C++

**Laser:** It is a high query throughput, low (millisecond) latency, key-value storage service built on top of RocksDB

**Scuba**: It is Facebook’s fast slice-and-dice analysis data store, most commonly used for trouble-shooting of problems as they happen.

**Hive:** It is Facebook’s exabyte-scale data warehouse.

Most event tables in Hive are partitioned by day.

Original post is [here](http://t.umblr.com/redirect?z=http%3A%2F%2Fmuratbuffalo.blogspot.in%2F2016%2F07%2Frealtime-data-processing-at-facebook.html&t=MDAzYmExNTk2MTU3ODM3MmRiNDA4OTYzZDA1NGVmMDMxMGJhZWMxYSxQMFRJSkpTbQ%3D%3D&b=t%3AWHEoVRTNb2bin3CIZh4A_w&p=http%3A%2F%2Fmini.iamjrp.com%2Fpost%2F147297010511%2Freal-time-processing-architecture-facebook&m=0)
