---
title:  "17 - Configuring Hadoop cluster(Pseudo-distributed, single machine) for remote accessing using Hiveserver2 and Rhive"
date:   2017-07-21 11:30:23
categories: [Hadoop]
tags: [hive]
reading_time: 5 min
---
Assuming machine "R" wants to connect to machine "H" using Rhive.

Pre-requisites
1. Hadoop and Hive should be configured in machine R
2. RHive is installed and configured in machine R
3. Hadoop should be configured in machine H
4. Hive should be configured in machine H (at least with embedded metastore)

*Hadoop and hive versions should be compatible in both the machines*

Setting up Machine H to allow request from machine R
Machine R will try to access machine H using rhive, which needs hadoop and hive configured and running behind. Machine H has hadoop and hive configured and hiveserver2 running. 

Steps Involved:

1. Configuring Hadoop core-site.xml for allowing remote access for hadoop user
2. Configuring HDFS for RHive
3. Configuring Hive for mysql as remote metastore

Hadoop user configured in Machine H is "hduser". You can have your own hadoop user.

**Changes in core-site.xml**

For remote access to hduser


```xml
<property>
   <name>hadoop.proxyuser.hduser.groups</name>
   <value>*</value>
</property>

<property>
  <name>hadoop.proxyuser.hduser.hosts</name>
  <value>*</value>
</property>
```

Without above, it throws 
"*org.apache.hadoop.ipc.RemoteException: User: hduser is not allowed to impersonate anonymous*"

Also check **fs.default.name** value
configure it to hdfs://&lt;ip-adddress&gt;:54310

Example:

```xml
<property>
  <name>fs.default.name</name>
  <value>hdfs://172.28.2.168:54310</value>
</property>
```

*Namenode is running on 172.28.2.168:54310

After above changes, restart hadoop cluster in machine H

**Configuring HDFS for RHive**

Create a directory called rhive in hdfs and assign all permission to it

```java
hadoop fs -mkdir /rhive
```

```java
hadoop fs -chmod -R 777 /rhive
```

With this much configuration, 


rhive.connect("172.28.2.168", 10000, hiveServer2=TRUE, user="hduser")

results in **successful connection** :)

if it gives error 

"*rhive.connect error - file:///rhive/lib/2.0-0.0/rhive_udf.jar does not exist*"

Then add defaultFS to connect using rhive like below

```java
rhive.connect("172.28.2.168", 10000, hiveServer2=TRUE, user="hduser", defaultFS="hdfs://172.28.2.168:54310")
```


Now machine R wants to read/write Hive tables in Machine H.

For this we have to configure remote metastore with mysql (preferably) in machine H

**Setting up remote metastore using mysql for hive**

1. Install mysql-server, if not installed

      sudo apt-get install mysql-server

2. Install the MySQL Java Connector

      sudo apt-get install libmysql-java

3. Create soft link for connector in Hive lib directory  or copy connector jar to lib folder 

      ln -s /usr/share/java/mysql-connector-java.jar $HIVE_HOME/lib/mysql-connector-java.jar

4. Create the Initial database schema using the hive-schema-&lt;version&gt;mysql.sql file located in the $HIVE_HOME/scripts/metastore/upgrade/mysql directory.

   ```java
    $ mysql -u root -p
    Enter password:
    mysql> CREATE DATABASE metastore;
    mysql> USE metastore;
    mysql> SOURCE $HIVE_HOME/scripts/metastore/upgrade/mysql/hive-schema-<version>.mysql.sql;
   ```

5. Create a MySQL user account for Hive to use to access the metastore.

      mysql> CREATE USER 'hduser'@'%' IDENTIFIED BY 'hivepassword'; 
      ```java
      mysql> GRANT all on *.* to 'hduser'@localhost identified by 'hivepassword';
      mysql>  flush privileges;
      ```

*For simplicity keep hivepassword same as your mysql password

**Note** : –  hiveuser is the ConnectionUserName in hive-site.xml ( As explained next)

6. Create hive-site.xml ( If not already present) in $HIVE_HOME/conf folder with the configuration below

      <configuration>
      ```xml
         <property>
            <name>javax.jdo.option.ConnectionURL</name>
            <value>jdbc:mysql://localhost/metastore?createDatabaseIfNotExist=true</value>
            <description>metadata is stored in a MySQL server</description>
         </property>
         <property>
            <name>javax.jdo.option.ConnectionDriverName</name>
            <value>com.mysql.jdbc.Driver</value>
            <description>MySQL JDBC driver class</description>
         </property>
         <property>
            <name>javax.jdo.option.ConnectionUserName</name>
            <value>hduser</value>
            <description>user name for connecting to mysql server</description>
         </property>
         <property>
            <name>javax.jdo.option.ConnectionPassword</name>
            <value>hivepassword</value>
            <description>password for connecting to mysql server</description>
         </property>
      </configuration>
      ```


7. Start hive session

      $ hive


Now we are done with configuration in machine H

Let's create a hive table

```sql
hive> create table hduser_test(id int, name string);
```


In MySQL


```sql
mysql -u root -p
Enter password:                                                             
mysql> use metastore;
```

Query the metastore schema in your MySQL database.

```sql
select * from TBLS;
```


On your mysql, you will see something like below

![hive-mysql](https://i.imgur.com/LhaTIuh.png  "hive-msql")

Exit from previous hive session and got $HIVE_HOME/bin and run hiveserver2


```java
$ hiveserver2
```


This is it for machine H configuration. Now from machine R will be able to access hive tables present in machine H.
