---
layout: post
title: impala jdbc连接
description: 
category: blog
---

## impala jdbc连接配置
### 配置jdbc端口
默认的jdbc2.0端口是21050，impala默认使用该端口。如需要修改可在启动impalad时使用<code>--hs2_port</code>参数进行配置。

### jdbc需要的jar包

	commons-logging-X.X.X.jar
	hadoop-common.jar
	hive-common-X.XX.X-cdhX.X.X.jar
	hive-jdbc-X.XX.X-cdhX.X.X.jar
	hive-metastore-X.XX.X-cdhX.X.X.jar
	hive-service-X.XX.X-cdhX.X.X.jar
	libfb303-X.X.X.jar
	libthrift-X.X.X.jar
	log4j-X.X.XX.jar
	slf4j-api-X.X.X.jar
	slf4j-logXjXX-X.X.X.jar


### 建立jdbc连接
不使用Kerberos认证的连接字符串

```
jdbc:hive2://host:port/;auth=noSasl
jdbc:hive2://myhost.example.com:21050/;auth=noSasl
```

使用kerberos认证的连接

```
jdbc:hive2://host:port/;principal=principal_name
```

使用LDAP认证的连接

```
jdbc:hive2://host:port/db_name;user=ldap_userid;password=ldap_password
```


