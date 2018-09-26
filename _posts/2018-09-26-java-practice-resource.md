---
layout:     post
title:      "Java开发实战资源配置参考"
subtitle:   ""
date:       2018-09-26 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - java
---


## Log4j 2使用教程

![log4j-logo](https://logging.apache.org/log4j/2.x/images/logo.png)

Log4j 目前最新的版本是 apache-log4j-2.11.1 (2018-09-26), 在尝试使用Log4j 2版本的时候，还是按照Log4j 1的思路来配置，发现行不通，2和1的变化还是很大的。 log4j2相对于log4j 1.x有了脱胎换骨的变化，其官网宣称的优势有多线程下10几倍于log4j 1.x和logback的高吞吐量、可配置的审计型日志、基于插件架构的各种灵活配置等。如果已经掌握log4j 1.x，使用log4j2还是非常简单的。

引入的包也发生了变化，Apache官方的介绍引入jar包如下
#### Using Log4j on your classpath
To use Log4j 2 in your application make sure that both the API and Core jars are in the application's classpath. Add the dependencies listed below to your classpath.
```
log4j-api-2.11.1.jar
log4j-core-2.11.1.jar
```
You can do this from the command line or a manifest file.

具体的使用教程可以参考  
[Log4j 2使用教程][log4j-2]




 [log4j-2]:https://www.cnblogs.com/leo-lsw/p/log4j2tutorial.html

