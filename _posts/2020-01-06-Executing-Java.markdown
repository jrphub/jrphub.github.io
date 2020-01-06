---
title:  "29 - Executing Java Program"
date:   2019-01-06 09:11:53
categories: [Java]
tags: [java]
---

While running Java program using command line/server, I always got confused about using -jar option and -cp option. We bundle the application in jar file using maven/gradle or any building tool. Now there may some resource files which should be provided from outside of the application. This is very common and ideal practice.

Now if you use

{% highlight shell %}
java -cp /path/to/resources -jar app.jar
{% endhighlight %}

Java won't read classpath resources and will look for classpath mentioned in app.jar manifest file.

So, in such case, 

{% highlight shell %}
java -cp /path/to/resources:/path/to/jar package.main-classname
{% endhighlight %}

Now, Java will search for the jar file and resources in classpath (separated by ; in windows and : in unix).

if you are not providing any -cp option, java checks System's CLASSPATH and uses the directories.

If there is no envrionment variable CLASSPATH set, then it checks the current directory

{% highlight shell %}
java -cp . package.main-classname
{% endhighlight %}

Any arguments provided after the main class name will be considered as program arguments

{% highlight shell %}
java -cp /path/to/all/resources package.main-classname arg1 arg2 arg3
{% endhighlight %}

You can pass JVM arguments before the main class and after -cp option with -D as prefix

{% highlight shell %}
java -cp /path/to/resources -Dkey=value package.main-classname arg1 arg2
{% endhighlight %}



Have a nice day :)