---
title:  "27 - Designing Infrastructure Config for Spark Application"
date:   2018-05-14 11:30:23
categories: [Hadoop]
tags: [spark]
reading_time: 5 min
---
When we get hardware for our Spark project, we always wonder, what should be the ideal configuration as per the hardware provided. To have better performance, there are two most important factor, we should consider.

1. CPU/Cores
2. Memory

Let's discuss a usecase and find out what should be the value of

1. No. of executor for each node
2. Total No. of executor instances i.e. -\-num-executors or spark.executor.instances
3. No. of Cores for each executor i.e. -\-executor-cores or spark.executor.cores
4. Memory for each executor i.e. -\-executor-memory or spark.executor.memory - default is 1g

To visualize the above terms, here are two figures

*Relation between Node, executor, Core, Task*

![Node-executor-core-task](https://i.imgur.com/oycY37f.png)

*Memory* 

![memory](https://i.imgur.com/Eg3zulc.png)

Now, let's assume , we got hardware of below configuration

1. No. of nodes = 6
2. No. of cores in each Node = 16 Core
3. RAM in each Node = 64 GB

![hardware](https://i.imgur.com/uCQddoH.png)

Now let's calculate what should be the ideal configuration

**<u>Point to remember</u>:**

1. **YARN Application master (AM) needs one executor**
2. **Ideally, No. of core per executor should be 5**
3. **From each node, 1 GB RAM will be dedicated for system related process**

So, from above,

There should be 5 cores per executor i.e. -\-executor-cores or spark.executor.cores = 5  **(Ans. of 3)**

Cluster has 15x6 = 90 cores

That means, No. of executor in the cluster = 90/5 = 18

Among 18, one executor for AM i.e. Actual No. of executors = 18 - 1 = 17  

  -\-num-executors or spark.executor.instances = 17 **(Ans. of 2)**

Then, each node can have 17/6 ~= 3 executors **(Ans. of 1)**

Let's calculate memory

Among 64GB RAM from each node, 1 GB RAM will be for system related tasks

So, left out RAM per each node = 64-1 = 63 GB

There are 3 executor per each node, So memory per executor = 63/3 = 21 GB RAM

But, there will be 7% overhead, hence, the memory left = 21 x (1 - 0.7) =~= 19GB RAM 

-\-executor-memory or spark.executor.memory = 19GB **(Ans. of 4)**

Hope it helps :)

