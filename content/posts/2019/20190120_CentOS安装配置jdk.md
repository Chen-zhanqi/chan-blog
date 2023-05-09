---
title: CentOS安装配置jdk
date: 2019-01-20
tags:
 - linux
categories: 
 - 2019
 - 技术
---


**下载所需JDK包**

[官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载

[JDK包下载：](https://pan.baidu.com/s/1jOly6mTIXVCREKXhGj7CwA) 提取码：2knq

**创建存放JDK包文件夹**

通过xftp上传至服务器指定路径

``` shell
[root@ ~]# mkdir /usr/local/java
[root@ ~]# cd /usr/local/java
[root@ ~]# tar -zxvf  ...
```

**配置环境变量**
```shell
[root@ ~]# vim /etc/profile
```

``` shell
JAVAHOME=/usr/local/java/解压后名字
PATH=$PATH:$JAVAHOME/bin:/usr/bin:/usr/sbin:/bin:/sbin
CLASSPATH=.:$JAVAHOME/lib/tools.jar:$JAVAHOME/lib/dt.jar
export  JAVAHOME  PATH  CLASSPATH 
```

生效配置
``` shell
[root@ ~]# source /etc/profile
```

**验证**
``` shell
[root@ ~]# java -version
```
