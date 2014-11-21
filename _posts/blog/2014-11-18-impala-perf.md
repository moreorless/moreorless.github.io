---
layout: post
title: impala
description: 
category: blog
---

## 基本操作

```
# 创建数据库
create database <db_name>
# 显示数据库表
show databases;
show tables;

# 创建数据表
create table t1 (x int, y int, s string);
# 文本格式的表 (Text data files)
create table my_table(id int, s string, n int, t timestamp, b boolean);
# 指定表列间的分隔符
create table csv(id int, s string, n int, t timestamp, b boolean) row format delimited fields terminated by ',';
create table tsv(id int, s string, n int, t timestamp, b boolean) row format delimited fields terminated by '\t';
create table pipe_separated(id int, s string, n int, t timestamp, b boolean) row format delimited fields terminated by '|' stored as textfile;
# 使用parquet格式存储的表
create table parquet_table_name (x INT, y STRING) STORED AS PARQUET;

# 使用分区()
[localhost:21000] > drop table census;
[localhost:21000] > create table census (name string, census_year int) partitioned by
(year int);
[localhost:21000] > insert into census partition (year=2010) values
('Smith',2010),('Jones',2010);
[localhost:21000] > insert into census partition (year=2011) values
('Smith',2020),('Jones',2020),('Doe',2020);
[localhost:21000] > insert into census partition (year=2012) values
('Smith',2020),('Doe',2020);
[localhost:21000] > select name from census where year = census_year and
census_year=2010;
+-------+
| name |
+-------+
| Smith |
| Jones |
+-------+

# 查看表结构
describe <tablename>
# 查看详细的表结构，分区信息，存储信息等
describe formatted <tablename>

```

* 使用文本格式时，文本数据中不能包含分隔符使用的字符，如果需要包含，需要在CREATE TABLE时使用ESCAPED BY语句指定转义字符。 *

## 一些实用信息
impala的web管理端口
http://<ip>:25000



## 分区
表的所有数据文件默认放在一个目录下。分区是一项基于一个或多个列上的值，在载入时物理拆分数据的技术。
例如，对于根据 year 列分区的 school_records 表来说，对于每一个不同的年份都有一个单独的数据目录，并且这一年的所有数据都存放在这个目录下的数据文件中。一个包含 WHERE 条件如 YEAR=1966, YEAR IN (1989,1999), YEAR BETWEEN 1984 AND 1989 的查询，可以只从对应的一个或多个目录下检索数据文件，极大的减少了读取和测试的数据的数量。


对于 Parquet 表，块大小 (数据文件理想大小) 是 1GB。

按照每天接收30G - 50G的数据计算，分区键列划分至小时。

### 分区键列
* 对于基于时间的数据，拆分出其中的各个部分到单独的列，因为 Impala 不能基于 TIMESTAMP 列进行分区

### Impala Parquet 表的查询性能
Parquet 表的数据是划分成一个个的 1GB 的数据文件的("行组(row groups)")，查询时读取以压缩格式存储的每一列的数据，并消耗 CPU 解压数据。因此 Parquet 表的查询性能依赖于查询中 SELECT 列表和 WHERE 子句中需要处理的列的个数，以及是否可以跳过较多的数据文件(分区表)，降低 I/O 并减少 CPU 负载(Query performance for Parquet tables depends on the number of columns needed to process the SELECT list and WHERE clauses of the query, the way data is divided into 1GB data files ("row groups"), the reduction in I/O by reading the data for each column in compressed format, which data files can be skipped (for partitioned tables), and the CPU overhead of decompressing the data for each column)。

