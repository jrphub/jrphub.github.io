---
title:  "11 - MSSQL on Linux (Ubuntu)"
date:   2017-04-04 11:30:23
categories: [Development]
tags: [sql]
reading_time: 1 min
---

[Sql-Cli](https://www.npmjs.com/package/sql-cli) is command line interface for SQL Server.

To install sql-cli using npm,

    npm install -g sql-cli

if you don't have npm installed, install npm by

    sudo apt-get install npm

After sql-cli installation, run

    mssql -h

for successful run, it will show usage of mssql command.
Else, it may throw this error:

```shell
root@SI-VM-207:~# mssql -h
/usr/bin/env: node: No such file or directory
```

To fix this error , do:

    ln -s /usr/bin/nodejs /usr/bin/node

Now, run "mssql -h" command again, it will work fine.

To connect to a database:

```sql
mssql -s hostname -u username -p password -o port -d databaseName
```
