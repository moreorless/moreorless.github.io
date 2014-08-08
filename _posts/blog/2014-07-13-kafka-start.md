---
layout: post
title: Kafka安装和使用初步
category: blog
description: Windows系统下安装和使用Kafka
---
## 简介
Kafka依赖于Zookeeper。使用Kafka之前，最好对zookeeper有一定的了解。

## 安装配置
### Step 1 下载
下载[Kafka](http://kafka.apache.org/downloads.html)，解压。

### Step 2 更新bat文件
0.8.0版本的kafka的windows脚本文件中有很多错误，从[这里](https://github.com/HCanber/kafka/releases)下载更新。

### Step 3 更新配置
1. 修改<code>config\server.properties</code>
    
    log.dirs=c:\\kafka\\kafka-logs
2. 修改<code>config\zookeeper.properties</code>
    
    dataDir=c:\\kafka\\zookeeper-data

<blockquote>注意windows系统下，目录分隔符要使用双反斜杠</blockquote>
<blockquote>如果遇到问题，注意查看<code>kafka_2.8.0-0.8.0\logs</code>目录下的日志文件。</blockquote>

### Step 4 启动服务
    cd c:\kafka

启动zookeeper服务
    .\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties

在另一个命令行窗口启动kafka服务
    .\bin\windows\kafka-server-start.bat .\config\server.properties

### Step 5 创建一个主题
创建一个主题
    .\bin\windows\kafka-create-topic.bat --zookeeper localhost:2181 --replica 1 --partition 1 --topic test

查看主题
    .\bin\windows\kafka-list-topic.bat --zookeeper localhost:2181

### Step 6 启动生产者、消费者
在命令行窗口启动生产者
    .\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test
控制台进行一些输入
    This is a message 
    This is another message
启动一个消费者
    .\bin\windows\kafka-console-consumer.bat --zookeeper localhost:2181 --topic test --from-beginning