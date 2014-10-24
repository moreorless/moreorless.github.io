---
layout: post
title: spark介绍
description: Spark资料整理
category: blog
---

## Spark发展简介
2009年，Spark诞生于伯克利大学AMPLab，最开初属于伯克利大学的研究性项目。它于2010年正式开源，并于2013年成为了Aparch基金项目，并于2014年成为Aparch基金的顶级项目，整个过程不到五年时间。

Spark建立在统一抽象的RDD之上，使得它可以基于一致的方式应对不同的大数据处理场景，包括MapReduce, Streaming, SQL, Machine Learning以及Graph等。


## Spark on Yarn
从架构和应用角度上看，spark是一个仅包含计算逻辑的开发库（尽管它提供个独立运行的master/slave服务，但考虑到稳定后以及与其他类型作业的继承性，通常不会被采用），而不包含任何资源管理和调度相关的实现，这使得spark可以灵活运行在目前比较主流的资源管理系统上，典型的代表是mesos和yarn，我们称之为“spark on mesos”和“spark on yarn”。

[spark on yarn的技术挑战](http://dongxicheng.org/framework-on-yarn/spark-on-yarn-challenge/)

## RDD
RDD (Resilient Distributed Datasets)，是一个容错的、并行的数据结构，可以让用户显示地将数据存储到磁盘和内存中，并能控制数据的分区。

## Storm VS Spark Streaming

Storm是一个流处理系统，而Spark的使用范围则宽泛的多，涵盖批处理、流处理、SQL查询、图计算、即席查询、机器学习等多种范式。

Spark Streaming相对于传统流处理系统的主要优势在于吞吐和容错。
在吞吐方面，Storm以单条记录为粒度来进行处理和容错，而Spark Streaming的基本思想是将数据切成等时间间隔的小批量任务，吞吐量显著高于Strom。
在容错方面，Spark Streaming借助RDD形成的lineage DAG可以再无须replication的情况下通过并行恢复有效提升故障恢复速度。



[理解Spark的核心RDD](http://www.infoq.com/cn/articles/spark-core-rdd)
[Databricks连城谈Spark的现状](http://www.infoq.com/cn/news/2014/09/spark-status)
[SparkInternals](https://github.com/JerryLead/SparkInternals/tree/master/markdown)
