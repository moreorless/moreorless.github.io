---
layout: post
title: python学习
description: 一些python的学习笔记
category: blog
---

## 模块
每一个python脚本都可以被当作是一个模块。模块以磁盘文件的形式存在。
模块里的代码可以是一段直接执行的脚本，也可以是一堆类似库函数的代码，从而可以被别的模块导入调用。

## 语法
python不支持类型x++或++x这样的前置、后置自增/自减运算符，可以写成x += 1

## 类

	class FooClass(object):
	"""my very first class : FooClass"""
	version = 0.1

	def __init__(self, nm="John Doe"):
		"""constructor"""
		self.name = nm
		print "create a class instance for", nm

	def showname(self):
		print "your name is ", self.name
		print "my name is ", self.__class__.__name__

	def showversion(self):
		print self.version

	def addMe2Me(slef, x):
		return x+x


	fool = FooClass()
	fool.showname()
	fool.showversion()
	fool.addMe2Me(3)
	fool.addMe2Me('xyz')

	foo2 = FooClass('Jane Smith')
	foo2.showname()