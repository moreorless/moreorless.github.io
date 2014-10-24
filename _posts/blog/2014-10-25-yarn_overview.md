---
layout: post
title: YARN介绍
description: YARN资料整理
category: blog
---

YARN（Yet Another Resource Negotiator）是下一代Hadoop的一个分支（注意：目前YARN尚不成熟稳定，各大公司普遍使用的还是Hadoop 1.0，但YARN是未来发展趋势，可以提前了解和学习它），它是一个资源管理系统，其上可以运行各种计算框架和应用程序。

YARN是未来的一个趋势，YARN本身已经变成了一个云操作系统，很多新的计算框架或者应用程序不再基于传统的操作系统开发（比如Linux）,而是基于YARN这个云操作系统，YARN提供了资源管理和资源调度等机制，这意味着，很多新的计算框架或者应用程序脱离了YARN将不再可以单独运行，典型的代表是DAG计算框架Tez和Spark（Spark也可以运行在另一个与YARN类似的资源管理系统Mesos上）。

随着开源界的发展和推进，最终，YARN之上可以运行各种应用类型的计算框架，包括离线计算框架MapReduce，实时计算框架Storm，DAG计算框架Tez等，真正实现一个集群多用途，这样的集群或者系统，我们通常称为轻量级弹性计算平台，说它轻量级，是因为YARN采用了cgroups轻量级隔离方案，说它弹性，是因为YARN能根据各种计算框架或者应用的负载或者需求调整它们各自占用的资源，实现集群资源共享，资源弹性收缩。



[运行在YARN上的计算框架](http://dongxicheng.org/framework-on-yarn/distributed-computing-framework-on-yarn/)