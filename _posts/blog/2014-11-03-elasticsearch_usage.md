---
layout: post
title: ElasticSearch
description: 
category: blog
---

## 节点管理
查看所有节点

```
curl 'localhost:9200/_cat/nodes?v'
```

查看节点健康状况

```
curl 'localhost:9200/_cat/health?v'
```

## 索引管理
### 列出所有索引

```
curl 'localhost:9200/_cat/indices?v'
```

### 创建索引

索引的访问模式

```
curl -<REST Verb> <Node>:<Port>/<Index>/<Type>/<ID>
```

创建一个索引customer

```
curl -XPUT 'localhost:9200/customer?pretty'
```


下面的请求添加或更新一条数据索引，对应信息如下：

* index : customer
* type  : external
* ID    : 1

```
curl -XPUT 'localhost:9200/customer/external/1?pretty' -d '
{
  "name": "John Doe"
}'
```

ID可以省略

```
curl -XPOST 'localhost:9200/customer/external?pretty' -d '
{
  "name": "Jane Doe"
}'
```

### 删除索引

```
curl -XDELETE 'localhost:9200/customer?pretty'
```

### 更新文档 updating documents

使用_update命令

```
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "doc": { "name": "Jane Doe" }
}'
```

使用<code>script</code>让age加5

```
curl -XPOST 'localhost:9200/customer/external/1/_update?pretty' -d '
{
  "script" : "ctx._source.age += 5"
}'
```

### 删除文档 delete documents

```
curl -XDELETE 'localhost:9200/customer/external/2?pretty'

curl -XDELETE 'localhost:9200/customer/external/_query?pretty' -d '
{
  "query": { "match": { "name": "John" } }
}'
```

### 批量处理 batch processing

先后索引ID=1，ID=2的两个文档

```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'
```

先更细ID=1的记录，再删除ID=2的记录

```
curl -XPOST 'localhost:9200/customer/external/_bulk?pretty' -d '
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'
```


## REST查询
http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/_the_search_api.html

### Request URI
[REST Request URI](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-uri-request.html)

在url中包含请求参数，类似http的GET请求

```
$ curl -XGET 'http://localhost:9200/twitter/tweet/_search?q=user:kimchy'
```

```
curl 'localhost:9200/bank/_search?q=*&pretty'
```
<code>bank/_search</code> 表示从bank索引中查询
<code>q=*</code> 返回所有的文档记录
<code>pretty</code> 表示返回格式化打印的文档结果

对应的request body请求方式为：

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} }
}'
```

返回结果示例:

```
{
    "_shards":{
        "total" : 5,
        "successful" : 5,
        "failed" : 0
    },
    "hits":{
        "total" : 1,
        "hits" : [
            {
                "_index" : "twitter",
                "_type" : "tweet",
                "_id" : "1",
                "_source" : {
                    "user" : "kimchy",
                    "postDate" : "2009-11-15T14:12:12",
                    "message" : "trying out Elasticsearch"
                }
            }
        ]
    }
}
```

### Request body
[REST Request body](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-request-body.html)

#### 查询第一条记录
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "size": 1
}'
```

#### 查询第11到第20条记录
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "from": 10,
  "size": 10
}'
```

<code>from</code> 标记索引开始的记录数（从0开始计数）
<code>size</code> 返回的记录数，默认为10

#### 查询所有记录，并按balance降序排列

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}'
```

#### _source
查询默认返回的是JSON文档的全部内容，如果只想获取文档中的指定字段，可以通过_source进行控制。

下面的示例返回<code>account_number</code>、<code>balance</code>两个字段

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "_source": ["account_number", "balance"]
}'
```

#### match query
查询account_number等于20的记录

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "account_number": 20 } }
}'
```

返回adress中包含mill的记录

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill" } }
}'
```

返回adress中包含mill或者lane的记录

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "address": "mill lane" } }
}'
```

match_phrase 返回包含词组"mill lane"的记录

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_phrase": { "address": "mill lane" } }
}'
```

#### 逻辑条件

##### must
相当于AND，包含mill，并且包含lane

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```
##### should
相当于OR,包含mill，或者包含lane

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "should": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

##### must not
既不包含mill，也不包含lane

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "bool": {
      "must_not": [
        { "match": { "address": "mill" } },
        { "match": { "address": "lane" } }
      ]
    }
  }
}'
```

### Filter
执行filter查询时，不需要计算文档的关联分数。(The score is a numeric value that is a relative measure of how well the document matches the search query that we specified. )

所以

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": {
    "filtered": {
      "query": { "match_all": {} },
      "filter": {
        "range": {
          "balance": {
            "gte": 20000,
            "lte": 30000
          }
        }
      }
    }
  }
}'
```

### 聚合(Aggregation)
对应于SQL的Group by和聚合函数。

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state"
      }
    }
  }
}'
```

相当于

```
SELECT COUNT(*) from bank GROUP BY state ORDER BY COUNT(*) DESC
```

Note that we set size=0 to not show search hits because we only want to see the aggregation results in the response.

先按赵state字段做group by，然后对合并结果的balance字段取平均值，结果按照平均值降序输出。

```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "size": 0,
  "aggs": {
    "group_by_state": {
      "terms": {
        "field": "state",
        "order": {
          "average_balance": "desc"
        }
      },
      "aggs": {
        "average_balance": {
          "avg": {
            "field": "balance"
          }
        }
      }
    }
  }
}'
```

**需要注意的是，从elasticsearch完成一次查询后，elasticsearch不会在服务端保存任何资源或者游标。
这是与传统关系型数据库很不同的一点。**

## JAVA API

