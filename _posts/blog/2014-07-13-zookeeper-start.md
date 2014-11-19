---
layout: post
title: Zookeeper安装和使用初步
category: blog
description: Zookeeper安装和使用初步
---

Zookeeper 分布式服务框架是 Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。

zookeeper用于存储协调数据，如状态、配置、位置等信息，每个节点存储的数据量很小，KB级别。

## 安装


### Node


### Watches
Zookeeper对Node的增、删、改、查都可以触发监听。


参考链接
http://www.ibm.com/developerworks/cn/opensource/os-cn-zookeeper/

[zookeeper技术浅析](http://www.cnblogs.com/sharpxiajun/archive/2013/06/02/3113923.html)

Zookeeper就是解决分布式系统“部分失败”的框架。Zookeeper不是让分布式系统避免“部分失败”问题，而是让分布式系统当碰到部分失败时候，可以正确的处理此类的问题，让分布式系统能正常的运行。

开发分布式系统是件很困难的事情，其中的困难主要体现在分布式系统的“部分失败”。“部分失败”是指信息在网络的两个节点之间传送时候，如果网络出了故障，发送者无法知道接收者是否收到了这个信息，而且这种故障的原因很复杂，接收者可能在出现网络错误之前已经收到了信息，也可能没有收到，又或接收者的进程死掉了。发送者能够获得真实情况的唯一办法就是重新连接到接收者，询问接收者错误的原因，这就是分布式系统开发里的“部分失败”问题。
　　
	