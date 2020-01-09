---
title:  "30 - Offset Management by Kafka"
date:   2020-01-09 10:01
categories: [Hadoop]
tags: [kafka]
reading_time: 2 min
---

In this post we will discuss about, 

From which offsets Kafka consumer starts consuming, when it starts?

This is decided by the configuration called "**auto.offset.reset**".



![kafka-consumer-offset](https://i.imgur.com/RIdNfbf.png)When the consumer is new and doesn't have any offset, it will start reading only new messages that will come to a topic-partition. This is default behaviour i.e. **latest**

That means, from diagram above, it will start reading from m7 i.e. offset 06

If you have set it to "**earliest**", then the consumer will read from m1 i.e offset 00 in the above diagram.

If you have set it to "**None**", then it will wait for your input, from which offset it should start reading. In this case, the offset management is at the application side.

Kafka holds the message in the topic-partition for a certain retention period (default : 7 days)

Now let's discuss a scenario...

Suppose, in 7 days, there are 1000 messages stored in a topic partition. Now, after 7 days, the starting offset will be 1001. 

*In case of "latest" configuration*

If the consumer is continously running, then it will start reading from 1001 

If you start a new consumer, it will start consuming message after the consumer starts. That means, in between consumer starts, if there is any messages in the topic-partition, it won't read those messages. 

In case of "earliest" configuration

If the consumer is continously running, then it will continously consuming from 1001.

If you start a new consumer, then also, it will start consuming from 1001 as that is the beginning offset of the topic partition after retention period.

In case, of earliest and latest configuration, Kafka stores the offset information in the built-in topic called "__consumer_offsets" (after v0.9)

The old kafka versions were storing this information in Zookeeper.

When consumer receives the message, it sends "commit" signal and Kafka updates this __consumer_offsets topic with this information. So, if you restart a consumer, the consumer reads from the position where it left in case of "latest" configuration.

In case of "earliest" configuration, it will start reading from beginning offsets of the topic anyways.

Thank you :)



