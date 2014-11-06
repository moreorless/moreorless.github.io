---
layout: post
title: ElasticSearch查询api
description: 
category: blog
---

## REST查询
http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/_the_search_api.html

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

[REST Request body](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-request-body.html)

查询第一条记录
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "size": 1
}'
```
查询第11到第20条记录
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

查询所有记录，并按balance降序排列
```
curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match_all": {} },
  "sort": { "balance": { "order": "desc" } }
}'
```

**需要注意的是，从elasticsearch完成一次查询后，elasticsearch不会在服务端保存任何资源或者游标。
这是与传统关系型数据库很不同的一点。**

## JAVA API

