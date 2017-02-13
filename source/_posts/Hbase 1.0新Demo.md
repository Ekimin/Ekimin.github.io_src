title: Hbase新APIDemo
date: 2016-12-22 22:34:08
tags: [大数据, hbase, code]
categories: 大数据
layout: post
updated: 
comments: 
permalink: 
---

HBase 1.0版是一个稳定版本，可以用于生产环境，而对于0.98.x系列版本，HBase开发团队仍然会继续维护和开发。

<!--more-->

```java
	// 创建一个hbase用户
	UserGroupInformation userGroupInformation = UserGroupInformation.createRemoteUser("hbase");
	User user = User.create(userGroupInformation);
	// 创建连接

	Connection connection = null;
	Table table = null;
	//Admin admin = null;
	try {
		connection = ConnectionFactory.createConnection(conf, user);
		table = connection.getTable(TableName.valueOf(tableName));
//		admin = connection.getAdmin();

		ResultScanner resultScanner = HbaseCommons.scanTable(table);
		for (Result result : resultScanner) {
			HbaseCommons.printResult(result);
		}
```