---
layout: post
title: mysql性能调优
description: 
category: blog
---

[http://www.imooc.com/learn/194](http://www.imooc.com/learn/194)

## 慢查询分析
### 开启慢查询日志

show variables like 'slow_query_log'
set global slow_query_log_file='/home/mysql/sql_log/mysql-slow.log'
set global log_queries_not_using_indexes=on;
set global long_query_time=1
set global slow_query_log=on;

### 慢查询日志分析工具

mysqldumpslow

pt-query-digest 

### 从慢查日志发现有问题的SQL
1. 查询次数多切每次查询占用时间长的SQL
2. IO大的SQL
3. 未命中索引的SQL  (Rows examine 远远大于 Rows Send)

### 如何分析SQL查询

使用explain查询SQL的执行计划

explain返回各列的含义
* table : 显示这一行的数据是关于哪张表的
* type : 这是重要的列，显示连接使用了何种类型。从最好到最差的连接类型为const,eq_reg, ref, range, index, ALL
* possible_keys : 显示可能应用在这张表中的索引。如果为空，没有可能的索引。
* key : 实际使用的索引。如果为NULL，则没有使用索引。
* key_len : 使用的索引的长度。
* ref : 显示索引的哪一列被使用了
* rows : MySQL认为必须检查的用来返回请求数据的行数
* extra : 需要注意的返回值，可能为Using filesort, Using temporary，看到这个都表示查询需要优化。

重复索引检测工具 pt-duplicate-key-checker

## 数据库结构优化
### 选择合适的数据类型

1. 使用简单的数据类型。Int要比Varchar类型在处理上简单
2. 尽可能的使用not null定义字段
3. 尽量少用text类型

时间存储，使用int存储，利用<code>FROM_UNIXTIME()</code>, <code>UNIX_TIMESTAMP()</code>进行转换

IP地址存储，使用bigint，利用<code>INET_ATON()</code>, <code>INET_NTOA()</code>进行转换

## 系统配置优化
### 操作系统配置优化

网络方面的配置，修改/etc/sysctl.conf文件

	# 增加tcp支持的队列数
	net.ipv4.tcp_max_syn_backlog = 65535
	# 减少断开连接式，资源回收
	net.ipv4.tcp_max_tw_buckets = 8000
	net.ipv4.tcp_tw_reuse = 1
	net.ipv4.tcp_tw_recycle = 1
	net.ipv4.tcp_fin_timeout = 10

打开文件数的限制，可以修改 /etc/security/limits.conf
	* soft nofile 65535
	* hard nofile 65535

在MySQL服务器上关闭iptables, selinux等防火墙软件

### MySQL配置文件 -- 常用参数

<code>innodb_buffer_pool_size</code>

非常重要的一个参数，用于配置Innodb的缓冲池。如果数据库中只有Innodb表，则推荐配置为总内存的75%.

<code>innodb_buffer_pool_instances</code>

控制缓冲池的个数，默认情况下只有一个缓冲池

<code>innodb_log_buffer_size</code>

innodb log缓冲的大小

<code>innodb_flush_log_at_trx_commit</code>

关键参数，对innodb的IO效率影响很大。默认值为1,可以取0,1,2三个值，一般建议设置为2，但如果数据安全性要求比较高，则使用默认值1.

* <code>innodb_read_io_threads</code>
* <code>innodb_write_io_threads</code>

Innodb读写的IO线程数，默认为4

<code>innodb_file_per_table</code>

*关键参数*，控制Innodb每一个表使用独立的表空间，默认为OFF，也就是所有表都会建立在共享表空间中。建议设置为ON。

<code>innodb_stats_on_metadata</code>

决定了MySQL在什么情况下会刷新innodb表的统计信息。

### 第三方配置工具

Percon Configuration Wizard

