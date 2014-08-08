---
layout: post
title: 使用Jekyll和Github搭建blog
category: blog
description: 
---
## 缘起
偶然间发现了[Jekyll](http://jekyllcn.com/)，完美实现了搭建个人blog的需求。

* 静态发布，不需要数据库，支持markdown，实现了[简书](http://jianshu.io/)风格的写作
* 与[Github](https://github.com/)完美结合，解决了网站托管的问题。事实上github page就是基于Jekyll实现的
* 使用github的版本管理功能，保留文档修改历史记录

## 动手
主要参考了两篇文章：

* 中文站点有详细的安装指南 [http://jekyllcn.com/docs/installation](http://jekyllcn.com/docs/installation/)
* [http://beiyuu.com/github-pages](http://beiyuu.com/github-pages/)

推荐两个模板参考：

* [https://github.com/mojombo/mojombo.github.io](https://github.com/mojombo/mojombo.github.io)
* [https://github.com/beiyuu/beiyuu.github.com](https://github.com/beiyuu/beiyuu.github.com)

安装及使用的步骤:

1. 首先，安装Ruby, RubyDevKit。
2. 接下来，就是了解Jekyll的目录结构和配置文件。看一下文档，再结合两个模板的文件比较一下就清楚了。
3. 最后，就是学习[markdown语法](http://wowubuntu.com/markdown/basic.html)，开始写文章:)

基本用法

安装了 Jekyll 的 Gem 包之后，就可以在命令行中使用 Jekyll 命令了。有以下一些用法

    $jekyll build
    # => 当前文件夹中的内容将会生成到 ./_site 文件夹中。
    jekyll build --destination <destination>
    # => 当前文件夹中的内容将会生成到目标文件夹<destination>中。

    $ jekyll build --source <source> --destination <destination>
    # => 指定源文件夹<source>中的内容将会生成到目标文件夹<destination>中。

    $ jekyll build --watch
    # => 当前文件夹中的内容将会生成到 ./_site 文件夹中，
    #    查看改变，并且自动再生成。

Jekyll 同时也集成了一个开发用的服务器，可以让你使用浏览器在本地进行预览。
    
    $jekyll serve
    # => 一个开发服务器将会运行在 http://localhost:4000/



基本的Jekyll结构如下：

    |-- _config.yml
    |-- _includes
    |-- _layouts
    |   |-- default.html
    |   -- post.html
    |-- _posts
    |   |-- 2007-10-29-why-every-programmer-should-play-nethack.textile
    |   -- 2009-04-26-barcamp-boston-4-roundup.textile
    |-- _site
    -- index.html

## 问题整理
### rdiscount安装问题
执行<code>gem install rdiscount</code>后，报错：<code>duplicate 'unsigned'</code>

这是rdiscount最新版本的问题，windows系统的头文件定义与discount中的代码冲突。安装之前的一个版本，可以解决。参考[这里](http://stackoverflow.com/questions/15021795/ruby-devkit-compile-issues)
    gem install rdiscount -v=1.6.8 --platform=ruby

### 中文编码问题
<blockquote><p>需要注意所有文件都需要使用UTF-8无BOM格式。</p></blockquote>
在Windows系统下，<code>jekyll --server</code>启动时，报错<code>... invalid byte sequence in GBK  ...</code>
这是中文兼容的问题，修改文件<code>C:/Ruby193/lib/ruby/gems/1.9.1/gems/jekyll-0.11.2/lib/jekyll/convertible.rb</code>

    self.content = File.read(File.join(base, name)
改为
    self.content = File.read(File.join(base, name), :encoding => "utf-8")

### YAML格式的注意事项
* YAML文件头必须写在三个短横线内，且短线前不能有空格
* “名: 值”对的冒号后需要有空格，否则不能正确识别为键值对；如果报key找不到的错误，可能就是这个原因
* 不能使用Tab符
* 表示数组成员的-后面要有空格
