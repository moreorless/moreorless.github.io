---
layout: post
title: pdf文件生成方案
description: 
category: opinion
---

## nodejs方案 (首选方案)

1. 安装nodejs, http://nodejs.org/

2. 使用markdown-pdf, https://github.com/alanshaw/markdown-pdf

> 先转换成html，然后调用phandomjs，生成pdf

> 可以通过重写css文件控制页面样式。

## pandoc方案 (备选方案)

下载安装pandoc, http://johnmacfarlane.net/pandoc/README.html

windows系统下，需要安装MiKTeX
