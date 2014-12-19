---
layout: post
title: mysql性能调优
description: 
category: blog
---

[http://www.imooc.com/learn/194](http://www.imooc.com/learn/194)

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

