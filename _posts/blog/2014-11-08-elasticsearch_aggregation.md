---
layout: post
title: ElasticSearch Aggregation
description: 
category: blog
---

[参考文章](https://www.found.no/foundation/elasticsearch-aggregations/)


## Introduction


## What Facets Cannot Do  (Facets的局限)
The illustrations and examples that follow are simplified. They are intended to explain how faceting and aggregation can be used, and not necessarily how they are implemented under the covers. A facet accepts a set of documents (as determined by the query) and produces some sort of summary, such as the how many documents have a certain tag, or how many documents fall in a certain “bucket” in a date histogram. While crunching these numbers, it does not retain information about which documents fall into which buckets: just the tallies. So it isn’t possible to easily say for example “for every store, show me a histogram of sales performance over time” with this model, just “show me sales performance per store” or “show me sales performance over time”.

分面(facet)接收一组文档集合，生成分组统计，例如具有某个标签的文档数量，在直方图上分布在某个统计项上的文档数据。在生成这些数据时，并没有包含哪一个文档对应到哪个统计项的信息，只是记录了数目。

## Aggregations
Aggregations can retain information about which documents go into which bucket. A “bucketing” aggregator takes a set of documents and outputs one or more buckets. These buckets also have their own sets of documents. Therefore, one aggregations’ output can be used as another one’s input! The resulting possibilities are vast. You can now compose aggregations, creating trees of aggregators. 
Aggregation可以获得哪一个文档对应到哪个统计项(bucket)的信息。

一个aggregator从一组文档生成一个或多个buckets。每一个bucket同样拥有他自己的文档集合。因此，一次聚合操作的输出可以作为另一次聚合操作的输入，也就可以进行聚合的嵌套操作。这样可以产生的结果就会非常多样。

对比下图中的左右两个部分

![Facet 和 Aggregation的对比](../../images/elasticsearch/aggregator-vs-facet.svg)

图中，左侧是Facet的过程，右侧是Aggregation的过程。

我们要获取针对tag的文档数量、价格统计。Facet直接通过一步操作获取结果。Aggregation则是通过两次聚合操作完成，首先按照tags进行一次聚合，聚合结果中每个tag都保存了对应的文档记录；然后再针对price进行的二次聚合，获取数据统计信息。

## Bucket vs Metric
The stats-aggregation is an example of what’s called a metric aggregator, and sometimes also a “calculator”. Example metric aggregators are min, max, sum, avg, value_count, stats, and extended_stats. These take a set of documents as input and crunch some numbers for the provided field. The output of metric aggregators does not include any information linked to individual documents, just the statistical data. i.e. metric aggregators can calculate that the minimum price is 10, but will not tell you which documents have that value.

metric聚合，包括像min, max, sum, svg, value_count, stats, entended_stats这样的聚合操作，这些聚合处理输入并生成一些数字结果，输出结果中不包含针对单个文档的信息，只包含统计数据。

![metric aggregator](../../images/elasticsearch/metric-aggregator.svg)

The other kind of aggregator is a bucketing aggregator. These aggregators produce buckets, where a bucket has an associated value and a set of documents.

另一种聚合操作成为bucketing aggregator。这种聚合操作生成统计项(bucket)，每个统计项对应一个数据和一组关联的文档集合。

![buckets aggregator](../../images/elasticsearch/buckets-aggregator.svg)

## Bucket Ordering
By default, the buckets for a terms aggregator are ordered by the number of documents within them, high to low. You can make the order depend on the value of a metrics sub-aggregator as well. For example, you may want to produce a list of products and order them by the revenue generated, and not just the sales count. Similarily, the histogram-aggregation orders buckets by the implied key by default, but this can be changed as well.

聚合后的统计项默认按照包含的文档数量进行排序（升序或者降序）。也可以指定根据子聚合器(sub-aggregator)的结果进行排序。

![buckets ordering](../../images/elasticsearch/bucket-ordering.svg)

## Real World Example
一个使用Stackoverflow网站数据的例子。问题描述如下：
> "What’s the number of questions per tag? And per tag, what’s the average number of comments? Who’s commenting the most, and what’s the highest score of a comment per author?"

> 每个tag下的问题数量？
> 针对每个tag，平均评论数是多少？谁评论的最多？每个作者的最高评论得分是多少？

聚合过程如下：

[stackoverflow-tree.svg](../../images/elasticsearch/stackoverflow-tree.svg)

First, the terms-aggregation on the tags-field will produce one bucket per tag. The documents in each bucket are then fed to an avg-aggregator on the comment_count field for each document in the bucket. The documents are also sent to a nested-aggregator. The nested-aggregator pulls up the comments of each document in the bucket, and these inner comments are passed to a terms-aggregator. That terms-aggregator produces buckets of comments per author. Last, these are passed through a max-aggregator, to find the highest comment score.

