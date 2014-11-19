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


这些jar包可以从[impala官网](http://www.cloudera.com/content/cloudera/en/downloads/connectors/impala/jdbc/impala-jdbc-v2-5-5.html)下载

### 建立jdbc连接
不需要认证方式

```
jdbc:impala://host:port/;AuthMech=0
jdbc:impala://myhost.example.com:21050;AuthMech=0
```

使用kerberos认证的连接

```
jdbc:impala://localhost:21050/;AuthMech=1;KrbRealm=EXAMPLE.COM;KrbHostFQDN=impala.example.com;KrbServiceName=impala
```

使用用户名、密码认证的连接

```
jdbc:impala://localhost:21050/;AuthMech=2;UID=impala
jdbc:impala://localhost:21050/;AuthMech=2;UID=impala;PWD=xxxx
```

Driver Class Name <code>com.cloudera.impala.jdbc4.Driver</code>

### 支持的数据类型
* TINYINT
* SMALLINT
* INT
* BIGINT
* FLOAT
* DOUBLE
* BOOLEAN
* STRING
* TIMESTAMP

### 代码示例

```
import java.sql.*;

public class JdbcTest {

	static String JDBCDriver = "com.cloudera.impala.jdbc4.Driver";
	static String ConnectionURL = "jdbc:impala://192.168.55.246:21050";
	
	public static void main(String[] args){
		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;
		
		String query = "select * from soc_netflow where year='2014' and month='11' and day='18' limit 100";
		
		try{
			// Register the driver using the class name
			Class.forName(JDBCDriver);
			
			// Establish a connection using the connection URL
			conn = DriverManager.getConnection(ConnectionURL);
			// Create a Statement object for sending SQL
			// statements to the database
			stmt = conn.createStatement();
			// Execute the SQL statement
			rs = stmt.executeQuery(query);
			
			
			System.out.printf("%20s%20s\r\n", "src_addr", "dst_addr");
			// Step through each row in the result set returned
			// from the database
			while(rs.next()) {
				// Retrieve values from the row where the cursor is currently positioned using column names
				String srcaddr = rs.getString("srcaddr");
				String dstaddr = rs.getString ("dstaddr");
				// Display values in columns 20 characters wide in the Console View using the Formatter
				System.out.printf("%20s%20s\r\n", srcaddr, dstaddr);
			}
		}catch (SQLException se) {
			// Handle errors encountered during interaction
			// with the data source
			se.printStackTrace();
		} catch (Exception e) {
			// Handle other errors
			e.printStackTrace();
		} finally {
			// Perform clean up
			try {
				if (rs != null) {
					rs.close();
				}
			} catch (SQLException se1) {
				// Log this
			}
			try {
				if (stmt!=null) {
					stmt.close();
				}
			} catch (SQLException se2) {
				// Log this
			}
			try {
				if (conn!=null) {
					conn.close();
				}
			} catch (SQLException se3) {
				// Log this
				se3.printStackTrace();
			} // End try
		} // End try
	}
}
```