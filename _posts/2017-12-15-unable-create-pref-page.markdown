---
title:  "22 - Eclipse Error: Unable to create selected Preference Page"
date:   2017-12-15 11:30:23
categories: [Development]
tags: [eclipse]
reading_time: 1 min
---
**Ubuntu** : 16.04 LTS

**Eclipse**: Luna Service Release 2 (4.4.2)

**Java Version**: 1.7.0_95

I was not able to run simple java program also. While changing preference, it alerts error, Unable to create selected preference page.

Checked .log file in workspace .metadata directory.

After a bit scrolling down, the actual error was this

```shell
Caused by: java.lang.NoClassDefFoundError: Could not initialize 
class com.ibm.icu.text.SimpleDateFormat
at org.eclipse.jdt.internal.debug.core.JDIDebugOptions.<clinit>
(JDIDebugOptions.java:58)
at org.eclipse.jdt.internal.debug.core.JDIDebugPlugin.start
(JDIDebugPlugin.java:274)
at org.eclipse.osgi.internal.framework.BundleContextImpl$3.run
(BundleContextImpl.java:771)
at org.eclipse.osgi.internal.framework.BundleContextImpl$3.run
(BundleContextImpl.java:1)
at java.security.AccessController.doPrivileged(Native Method)
at org.eclipse.osgi.internal.framework.BundleContextImpl.
startActivator(BundleContextImpl.java:764)
```

**Reason:**

`tzdata-java` was removed in Xenial 16.04,  because OpenJDK 8 does not provide the necessary files to build it. Quoting the [Debian bug report](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=814073) which prompted its removal:

> The problem is that the default java has recently been switched to openjdk-8, which doesn't provide javazic.jar. As such we can't build tzdata-java anymore.

**Solution**

```shell
sudo add-apt-repository ppa:justinludwig/tzdata
sudo apt-get update
```

Also, add below line in eclipse.ini

`-Dcom.ibm.icu.util.TimeZone.DefaultTimeZoneType=ICU`

Restart your eclipse, Solved :)
