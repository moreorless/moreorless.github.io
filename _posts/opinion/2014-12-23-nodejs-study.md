---
layout: post
title: nodejs学习
description: 
category: opinion
---

## 环境搭建

- Imooc/
  > npm install express
  > npm install body-parser
  > npm install jade
  > npm install mongoose
  > npm install bower -g
  > bower install bootstrap

bower是一个依赖管理工具，使用bower需要安装git

## mongoose

Schema
Model
Documents

	var mongoose = require('mongoose')
	var MovieSchema = require('../schemas/movie')

	var Movie = mongoose.model('Movie', MovieSchema)
	module.exports = Movie


.bowerrc

{
	"directory": "public/libs"
}


## MongoDB安装
安装为系统服务
	mongod --logpath E:\dev\tool\mongodb-2.6.6\logs\MongoDB.log --logappend --dbpath E:\dev\tool\mongodb-2.6.6\data --directoryperdb --rest --serviceName MongoDB --install

	需要使用<code>--rest</code>参数，才会启用http api接口，从(mongo port+1000)端口访问web界面


## 生成配置文件
bower init
npm init
