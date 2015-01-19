---
layout: post
title: ant命令
category: project
description: 
---

## 调用target
* 调用用一个build.xml中的其他target
	antcall

* 调用另一个build.xml中的target
	ant

	```
	<ant antfile="${installer.source.path}/build.xml" target="deploy-release" dir="${installer.source.path}" inheritAll="false" />
	```
## 字符串替换
```
<propertyregex override="yes" property="installer.dist.path_trans" input="${installer.dist.path}" regexp="\\" replace="/" />
```

## 提取文件内容
```
<loadfile srcfile="${installer.dist.path}/webapp/oem/i18n/oem.properties" property="oem.file">
	<filterchain>
		<tokenfilter>
		    <containsregex pattern="compile.time=" flags="i"/>
		</tokenfilter>
		<striplinebreaks />  <!-- 过滤换行符 -->
	</filterchain>
</loadfile>
```

## 调用外部程序
```
<exec dir="${installer.source.path}" executable="${svn.install.path}/bin/svn.exe">
	<arg line="checkout" />
	<arg line="${svn.src.path}" />
	<arg line="${installer.source.path}" />
	<arg line="\-\-quiet" />
</exec>
```

## Properties文件修改
```
<propertyfile file="${installer.source.path}/build.properties">
	<entry key="deploy.version" value="${deploy.version}"/>
	<entry key="deploy.modules" value="${deploy.modules}"/>
	<entry key="deploy.lang" value="${deploy.lang}"/>
	<entry key="db.userid" value="${db.userid}"/>
	<entry key="db.password" value="${db.password}"/>
	<entry key="deploy.release.path" value="${installer.dist.path_trans}"/>
	<entry key="db.path" value="${installer.dist.path_trans}/database"/>
</propertyfile>
```
