---
title:  "25 - Different mode of Cloudera Installation"
date:   2018-03-08 11:30:23
categories: [Hadoop]
tags: [cloudera]
---
There are three main installation paths for creating a new Cloudera Manager deployment:

**Installation Path A** - Automated Installation by Cloudera Manager (Non-Production Mode)

This path is recommended for demonstration and proof-of-concept deployments, but is not recommended for production deployments because it's not intended to scale and may require database migration as your cluster grows.

To use this method, server and cluster hosts must satisfy the following requirements:
– Provide the ability to log in to the Cloudera Manager Server host using a root account or an account that has password-less sudo permission.
– Allow the Cloudera Manager Server host to have uniform SSH access on the same port to all hosts. 
– All hosts must have access to standard package repositories and either archive.cloudera.com or a local repository with the required installation files.

**Installation Path B** - Installation Using Cloudera Manager Parcels or Packages. (Recommended for most cases)

This path requires you to first manually install and configure an external database for the Cloudera Manager Server, Cloudera Management services and any other CDH services that may reside on this database instance (such as Hue, Hive, Oozie etc.). 

In order for Cloudera Manager to automate installation of Cloudera Manager Agent packages or CDH and managed service software, cluster hosts must satisfy the following requirements:
• Allow the Cloudera Manager Server host to have uniform SSH access on the same port to all hosts.
• All hosts must have access to standard package repositories and either archive.cloudera.com or a local repository with the required installation files.

**Installation Path C** - Manual Installation Using Cloudera Manager Tarballs.

In this path, you install the the Oracle JDK, Cloudera Manager server and Agent software using tarballs and use Cloudera Manager to automate installation of CDH and managed service software as parcels.

------

Cloudera manager environment has two basic component :

1. Cloudera manager - Cloudera manager server, Daemons, Agents and Cloudera management services
2. CDH - Hadoop services and roles

The detailed information on installation is found in here

https://www.cloudera.com/documentation/enterprise/latest/PDF/cloudera-installation.pdf

The above document has a table, which may be handy for understanding differnt types of installations:

![phases-of-installation](https://i.imgur.com/vlAFmxy.png)