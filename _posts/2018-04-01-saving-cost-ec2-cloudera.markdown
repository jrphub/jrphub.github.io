---
title:  "26 - Saving cost while working on EC2 and Cloudera Manager"
date:   2018-04-01 11:30:23
categories: [AWS]
tags: [cloudera]
reading_time: 1 min
---
I have set up one AWS EC2 cluster of 5 instances (us-east-2 region, EBS Storage Type) of m4.xlarge type i.e. with 4 CPU and 16GB RAM each. These are Ubuntu 16.04 LTS Xenial instances which comes with free-tier usage.

As per this link :

https://aws.amazon.com/ec2/pricing/on-demand/

The cost would be 0.2x5=1$ per hour

To save a bit, I don't want to keep my EC2 instances running unnecessarily while I am not using the AWS cluster. Also, I have installed Cloudera manager in this 5 instance cluster. I don't want to corrupt cloudera set up as well.

So, here are the steps I follow to maintain the cluster , cloudera manager, CDH to save a bit from my pocket.

**Stop** :

1. Stop the cluster in Cloudera Manager
2. Stop the Cloudera Management Service in Cloudera manager
3. Stop all Cluster EC2 instances, including Cloudera Manager host in AWS console

**Start** :

1. Start all cluster EC2 instances in AWS console
2. Open Cloudera manager url and wait a bit to load the page. Then start the Cloudera Management Service
3. Start the cluster in Cloudera Manager

Cloudera Cluster should be healthy after restart. Check the logs of respective service, if any service has some issue. 

EC2 instances retain their internal IP address and hostname for their lifetime, so no reconfiguration of CDH is required after restart. The public IP and DNS hostnames, however, will be different. Elastic IPs can be configured to remain associated with a stopped instance at additional cost, but it isn’t necessary to maintain proper cluster operation.