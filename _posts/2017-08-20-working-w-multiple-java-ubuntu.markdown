---
title:  "18 - Working with multiple versions of Java in ubuntu 16.04"
date:   2017-09-19 11:30:23
categories: [Development]
tags: [java]
reading_time: 2 min
---
Current Java version: 

```shell
jrp@iamjrp:~$ java -version

java version "1.7.0_95"

OpenJDK Runtime Environment (IcedTea 2.6.4) (7u95-2.6.4-3)

OpenJDK 64-Bit Server VM (build 24.95-b01, mixed mode)

```

I need Java 8 for another project, and want to switch between Java 7 & 8 whenever needed.

Q. If I install Java 8, will it overwrite my current installation?

Ans. No. Apt-get won't overwrite the existing java versions.

Cool, Then let's install Java 8

```shell
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
```

To List all Java Versions (A)

```shell
jrp@iamjrp:~$ update-java-alternatives --list
java-1.7.0-openjdk-amd64       1071       /usr/lib/jvm/java-1.7.0-openjdk-amd64
java-8-oracle                  1081       /usr/lib/jvm/java-8-oracle
```

Q. Though I have installed Java 8, while using Java 8, JAVA_HOME will point to Java 7. How can I manage it easily?

Ans. Add this to .bashrc file

```shell
export JAVA_HOME="$(jrunscript -e 'java.lang.System.out.println(java.lang.System.getProperty("java.home"));')"
```

Also add these aliases

```shell
alias useJava8='yes | sudo update-java-alternatives --set /usr/lib/jvm/java-8-oracle && source ~/.bashrc'
alias useJava7='yes | sudo update-java-alternatives --set /usr/lib/jvm/java-1.7.0-openjdk-amd64 && source ~/.bashrc'
```

Note : The argument for --set comes from Path of (A) above

After saving .bashrc file, do

`source .bashrc`

Now let's check

```shell
jrp@iamjrp:~$ java -version
java version "1.8.0_121"
Java(TM) SE Runtime Environment (build 1.8.0_121-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.121-b13, mixed mode)
jrp@iamjrp:~$ echo $JAVA_HOME
/usr/lib/jvm/java-8-oracle/jre
jrp@iamjrp:~$ useJava7
update-java-alternatives: plugin alternative does not exist: /usr/lib/jvm/java-7-openjdk-amd64/jre/lib/amd64/IcedTeaPlugin.so
jrp@iamjrp:~$ java -version
java version "1.7.0_95"
OpenJDK Runtime Environment (IcedTea 2.6.4) (7u95-2.6.4-3)
OpenJDK 64-Bit Server VM (build 24.95-b01, mixed mode)
jrp@iamjrp:~$ echo $JAVA_HOME
/usr/lib/jvm/java-7-openjdk-amd64/jre
jrp@iamjrp:~$ useJava8
jrp@iamjrp:~$ java -version
java version "1.8.0_121"
Java(TM) SE Runtime Environment (build 1.8.0_121-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.121-b13, mixed mode)
jrp@iamjrp:~$ echo $JAVA_HOME
/usr/lib/jvm/java-8-oracle/jre

```

Enjoy :)
