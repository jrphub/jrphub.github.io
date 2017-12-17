---
title:  "Remote Connection : To reconnect existing session using xrdp"
date:   2017-06-06 11:30:23
categories: [Others]
tags: [others]
---
The main drawback with the standard package is that each time you perform a remote desktop to your xrdp server; a new session was created.

You can check xrdp configuration file /etc/xrdp/xrdp.ini

```shell
[xrdp1] name=sesman-Xvnc

lib=libvnc.so

username=ask

password=ask

ip=127.0.0.1

port=-1
```

Port “-1″ says, connect to new session every time.

When you connect it for the first time, you can see the port referred in **5910** which is by default. See below image

![image](http://78.media.tumblr.com/15b0dc077b81b07fe5ff13605a341b7a/tumblr_inline_oa72v65tsI1uq2h70_540.png)

So, to connect to the existing session you have two options

Change the xrdp.ini file as follows

```shell
[xrdp1] name=sesman-Xvnc

lib=libvnc.so

username=ask

password=ask

ip=127.0.0.1

port=5910
```

Or

```shell
[xrdp1] name=sesman-Xvnc

lib=libvnc.so

username=ask

password=ask

ip=127.0.0.1

port=ask
```

In later case, while connecting, it will ask for port, along with username and password.

You can provide 5910 as port no. or -1 (if you want to create a new session)

Now disconnect he connection and connect again.

Enjoy!
