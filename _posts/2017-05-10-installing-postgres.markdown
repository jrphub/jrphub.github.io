---
title:  "12 - Installing and configuring Postgres"
date:   2017-05-10 11:30:23
categories: [Development]
tags: [sql]
---
To install postgres on linux machine,

    sudo apt-get install postgresql postgresql-contrib

After successful installation, check for 

    netstat -na | grep ^tcp

This should include below line 

    tcp 0 0 127.0.0.1:5432 0.0.0.0:* LISTEN

if it is not showing, then fix it first. Most probably postgres is not installed.

You can login as postgres like this

    sudo -i -u postgres

To open postgres console

    psql

To check the **port** it is using and other connection info

    postgres=# \conninfo
    You are connected to database "postgres" as user "postgres" via socket in "/var/run/postgresql" at port "5432".

To quit

    \q


Now, lets say we have two machines

machine-S : 172.30.10.98 (server)
machine-C : 172.28.2.168 (client)

In both the machine, postgres is installed, but machine-C wants to connect to machine-S, where postgres database is present.

If we try to connect server machine, from client using below command

    psql -h 172.30.10.98 -p 5432 -U postgres

It will throw error like this

    psql: could not connect to server: Connection refused
            Is the server running on host "172.30.10.98" and accepting
            TCP/IP connections on port 5432?


So, to set up the connection between two, we need to follow two steps:
1. Modify pg_hba.conf to add Client Authentication Record

As we are using host based authentication, we need to change pg_hba.conf file like below

    sudo vi /etc/postgresql/9.3/main/pg_hba.conf

append below line

    host all all 172.28.2.168/24 trust

where "172.28.2.168" is the ipv4 address of client machine

change the line for localhost like below

    host all all 127.0.0.1/32 trust


2. Change the Listen Address in postgresql.conf of server machine

a. open postgresql.conf 

    sudo vi /etc/postgresql/9.3/main/postgresql.conf


b. uncomment **listen_address** and change the value from 'localhost' to '*'

Now restart the server

    sudo /etc/init.d/postgresql restart


Now client machine can connect to postgres database

    root@172.28.2.168:~# psql -h 172.30.10.98 -p 5432 -U postgres
    psql (9.3.14, server 9.3.13)
    SSL connection (cipher: DHE-RSA-AES256-GCM-SHA384, bits: 256)
    Type "help" for help.
    
    postgres=# 

Cheers :)
