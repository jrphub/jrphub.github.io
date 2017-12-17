---
title:  "Running Scala Project in Eclipse IDE"
date:   2017-09-19 11:30:23
categories: [Scala]
tags: [eclipse]
---
Two ways

1. Using scala IDE (needs Java 8, wrapper of Eclipse Neon)
2. Using scala IDE as plugin in Existing eclipse IDE (Eclipse Luna onwards)

### IDE Setup

#### Using Scala IDE :

Download from here (http://scala-ide.org/download/sdk.html)

#### Using Scala IDE as plugin in Existing Eclipse IDE

Open eclipse ide -> Go to Help -> Eclipse Market Place -> Search "scala ide" as shown in image below

![img](http://i.imgur.com/GV7S5Fj.png)

Click on "install" corresponding "Scala IDE" in search result and install the plugin.

Restart the eclipse.

Now you can create Scala Project inside Eclipse IDE

### Installing SBT

#### On Windows

Download **zip file**  and **msi installer** mentioned here() http://www.scala-sbt.org/0.13/docs/Installing-sbt-on-Windows.html)

Using msi installer, install SBT on windows

#### On Linux

Follow the instruction mentioned here() http://www.scala-sbt.org/0.13/docs/Installing-sbt-on-Linux.html)

For ubuntu, 

```shell
echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823
sudo apt-get update
sudo apt-get install sbt
```

### Creating Scala Project 

1. We are creating "SimpleApp" Project. To do so, run below command

   ```shell
   jrp@iamjrp:~$ mkdir /home/jrp/workspace_1/SimpleApp
   jrp@iamjrp:~$ cd /home/jrp/workspace_1/SimpleApp
   jrp@iamjrp:~/workspace_1/SimpleApp$ mkdir -p src/{main,test}/{java,resources,scala}
   jrp@iamjrp:~/workspace_1/SimpleApp$ ls
   bin  src
   jrp@iamjrp:~/workspace_1/SimpleApp$ mkdir lib project target
   jrp@iamjrp:~/workspace_1/SimpleApp$ ls
   bin  lib  project  src  target
   jrp@iamjrp:~/workspace_1/SimpleApp$ touch build.sbt
   jrp@iamjrp:~/workspace_1/SimpleApp$ tree
   .
   ├── bin
   ├── build.sbt
   ├── lib
   ├── project
   ├── src
   │   ├── main
   │   │   ├── java
   │   │   ├── resources
   │   │   └── scala
   │   └── test
   │       ├── java
   │       ├── resources
   │       └── scala
   └── target

   13 directories, 1 file
   jrp@iamjrp:~/workspace_1/SimpleApp$ 
   ```

   **For windows, you can run these command using Cygwin terminal or simply "md" command in command prompt for creating directories. The "tree" command is just to show directory structure so far. Also create build.sbt file

2. Open build.sbt file and paste below content

   ```scala
   name := "SimpleApp"

   version := "1.0"

   scalaVersion := "2.11.7"

   libraryDependencies += "org.apache.spark" %% "spark-core" % "2.1.0"
   ```

   Here we are using , spark version : 2.1.0 and scala version "2.11". You can change the version as you like.

3. Go to HOME directory and run below commands

   ```shell
   jrp@iamjrp:~$ cd
   jrp@iamjrp:~$ cd .sbt
   jrp@iamjrp:~/.sbt$ ls
   0.13  boot
   jrp@iamjrp:~/.sbt$ cd 0.13/
   jrp@iamjrp:~/.sbt/0.13$ mkdir plugins
   jrp@iamjrp:~/.sbt/0.13$ cd plugins/
   jrp@iamjrp:~/.sbt/0.13/plugins$ vi plugins.sbt
   #Add below line in plugins.sbt and save
   addSbtPlugin("com.typesafe.sbteclipse" % "sbteclipse-plugin" % "4.0.0")

   # Go to project directory
   jrp@iamjrp:~/.sbt/0.13/plugins$ cd /home/jrp/workspace_1/SimpleApp

   #run below command
   jrp@iamjrp:~/workspace_1/SimpleApp$ sbt eclipse

   jrp@iamjrp:~/workspace_1/SimpleApp$ ls -la
   total 80
   drwxrwxr-x  8 jrp jrp  4096 Jul  2 17:40 .
   drwxrwxr-x 35 jrp jrp  4096 Jul  2 12:58 ..
   drwxrwxr-x  3 jrp jrp  4096 Jul  2 17:40 bin
   -rw-rw-r--  1 jrp jrp   132 Jul  2 17:08 build.sbt
   -rw-rw-r--  1 jrp jrp 21482 Jul  2 17:40 .cache-main
   -rw-rw-r--  1 jrp jrp 15033 Jul  2 17:35 .classpath
   drwxrwxr-x  2 jrp jrp  4096 Jul  2 17:07 lib
   drwxrwxr-x  3 jrp jrp  4096 Jul  2 17:08 project
   -rw-rw-r--  1 jrp jrp   363 Jul  2 17:35 .project
   drwxrwxr-x  2 jrp jrp  4096 Jul  2 17:35 .settings
   drwxrwxr-x  4 jrp jrp  4096 Jul  2 16:59 src
   drwxrwxr-x  5 jrp jrp  4096 Jul  2 17:09 target
   ```

4. Now Go to eclipse, and import the project

   File -> import -> General (Existing Project  into workspace) -> Browse to SimpleApp Folder -> Next -> Finish

5. Right click on src/main/scala -> new -> scala object

   ![img](http://i.imgur.com/sZSgmjI.png)

6. Create input-dirdirectory in the root of project directory in eclipse  and put one text file 

7. Paste the below content in MainApp.scala and save the file

   ```scala
   package com.spark.practice

   import org.apache.spark.SparkContext
   import org.apache.spark.SparkContext._
   import org.apache.spark.SparkConf

   object MainApp {

     def main(args: Array[String]) {
       val logFile = "file:///home/jrp/workspace_1/SimpleApp/input-dir/wordcount.txt" // Should be some file on your system
       val conf = new SparkConf().setAppName("Simple App").setMaster("local[*]")
       val sc = new SparkContext(conf)
       val logData = sc.textFile(logFile, 2).cache()
       val numAs = logData.filter(line => line.contains("a")).count()
       val numBs = logData.filter(line => line.contains("b")).count()
       println(s"Lines with a: $numAs, Lines with b: $numBs") //Lines with a: 44, Lines with b: 29
       sc.stop()
     }

   }
   ```

8. Right click on the file and run as -> scala application 

#### To run through terminal

1. Go to project directory in terminal and run "sbt" like this

   ```shell
   jrp@iamjrp:~/workspace_1/SimpleApp$ sbt package
   [warn] Executing in batch mode.
   [warn]   For better performance, hit [ENTER] to switch to interactive mode, or
   [warn]   consider launching sbt without any commands, or explicitly passing 'shell'
   [info] Loading global plugins from /home/jrp/.sbt/0.13/plugins
   [info] Loading project definition from /home/jrp/workspace_1/SimpleApp/project
   [info] Set current project to SimpleApp (in build file:/home/jrp/workspace_1/SimpleApp/)
   [success] Total time: 0 s, completed 2 Jul, 2017 6:13:31 PM
   ```

2. let's run the application

   ```shell
   jrp@iamjrp:~/workspace_1/SimpleApp$ sbt run
   [warn] Executing in batch mode.
   [warn]   For better performance, hit [ENTER] to switch to interactive mode, or
   [warn]   consider launching sbt without any commands, or explicitly passing 'shell'
   [info] Loading global plugins from /home/jrp/.sbt/0.13/plugins

   .....
   Lines with a: 44, Lines with b: 29
      17/07/02 18:17:00 INFO SparkUI: Stopped Spark web UI at http://192.168.0.104:4040
      17/07/02 18:17:00 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!
      17/07/02 18:17:00 INFO MemoryStore: MemoryStore cleared
      17/07/02 18:17:00 INFO BlockManager: BlockManager stopped
      17/07/02 18:17:00 INFO BlockManagerMaster: BlockManagerMaster stopped
      17/07/02 18:17:00 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!
      17/07/02 18:17:00 INFO SparkContext: Successfully stopped SparkContext
      [success] Total time: 4 s, completed 2 Jul, 2017 6:17:00 PM
      17/07/02 18:17:00 INFO ShutdownHookManager: Shutdown hook called
      17/07/02 18:17:00 INFO ShutdownHookManager: Deleting directory /tmp/spark-9f5ddc73-1c8a-4ae0-a05a-fa43154bc24e
   ```


####  To submit the application


   ```

cd $SPARK_HOME/bin
./spark-submit --class "com.spark.practice.MainApp" --master local[4] /home/jrp/workspace_1/SimpleApp/target/scala-2.11/simpleapp_2.11-1.0.jar
   ```
