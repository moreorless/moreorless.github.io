---
layout: post
title: hadoop权威指南读书笔记
description: hadoop权威指南读书笔记
category: blog
---

## 初识hadoop
Hadoop是Apache Lucene的创始人Doug Cutting创建的。Hadoop起源于Apache Nutch，一个开源的网络搜索引擎。

MapReduce比数据库更适于处理大规模数据的原因，在于磁盘的一个发展趋势：磁盘寻址时间的提高远远慢于传输速率的提高。如果数据的访问模式中包含大量的磁盘寻址，那么读取大量数据集所花费的时间势必会比流式数据读取模式更长；流式读取主要取决于传输速率。

关系型数据库和MapReduce的比较：
<table class="table">
	<thead>
		<tr>
			<th></th>
			<th>关系型数据库</th>
			<th>MapReduce</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>数据集大小</td>
			<td>GB</td>
			<td>PB</td>
		</tr>
		<tr>
			<td>更新</td>
			<td>多次读写</td>
			<td>一次写入多次读取</td>
		</tr>
		<tr>
			<td>结构</td>
			<td>静态模式</td>
			<td>动态模式</td>
		</tr>
		<tr>
			<td>完整性</td>
			<td>高</td>
			<td>低</td>
		</tr>
		<tr>
			<td>横向扩展</td>
			<td>非线性</td>
			<td>线性</td>
		</tr>
	</tbody>
</table>

