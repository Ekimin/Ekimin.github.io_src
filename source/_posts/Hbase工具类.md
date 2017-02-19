title: Hbase工具类
date: 2016-07-29 17:27:45
tags: [大数据, hbase]
categories: 大数据
layout: post
updated: 2016-07-29 17:28:33
comments: 
permalink: 
---

Hbase工具类，包含若干常用方法。


<!--more-->

```java
package com.wentuotuo.utils;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.ClusterStatus;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.MasterNotRunningException;
import org.apache.hadoop.hbase.ServerName;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.ZooKeeperConnectionException;
import org.apache.hadoop.hbase.client.Delete;
import org.apache.hadoop.hbase.client.Get;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.filter.CompareFilter;
import org.apache.hadoop.hbase.filter.Filter;
import org.apache.hadoop.hbase.filter.FilterList;
import org.apache.hadoop.hbase.filter.PageFilter;
import org.apache.hadoop.hbase.filter.RowFilter;
import org.apache.hadoop.hbase.filter.SubstringComparator;
import org.apache.hadoop.hbase.util.Bytes;

/**
 * Hbase工具类
 * 
 **/
public class HbaseCommons {

	// 设置hbase的配置文件，hbase会自动从xml中读取信息
	static Configuration conf = HBaseConfiguration.create();

	/**
	 * 创建一个表
	 * 
	 * @param tableName
	 *            表名字
	 * @param columnFamilys
	 *            列簇
	 * 
	 **/
	public static void createTable(String tableName, String[] columnFamilys) throws Exception {
		// 创建表（创建表、删除表使用HBaseAdmin）
		HBaseAdmin admin = new HBaseAdmin(conf);
		if (admin.tableExists(tableName)) {
			System.out.println("此表，已存在！");
		} else {
			// 旧的写法
			// HTableDescriptor tableDesc=new HTableDescriptor(tableName);
			// 新的api
			HTableDescriptor tableDesc = new HTableDescriptor(TableName.valueOf(tableName));

			for (String columnFamily : columnFamilys) {
				tableDesc.addFamily(new HColumnDescriptor(columnFamily));
			}

			admin.createTable(tableDesc);
			System.out.println("建表成功!");
		}
		admin.close();// 关闭释放资源

	}

	/**
	 * 删除一个表
	 * 
	 * @param tableName
	 *            删除的表名
	 */
	public static void deleteTable(String tableName) throws Exception {
		HBaseAdmin admin = new HBaseAdmin(conf);
		if (admin.tableExists(tableName)) {
			admin.disableTable(tableName);// 禁用表
			admin.deleteTable(tableName);// 删除表
			System.out.println("删除表成功!");
		} else {
			System.out.println("删除的表不存在！");
		}
		admin.close();
	}

	/**
	 * 插入一条数据
	 * 
	 * @param tableName
	 *            表名
	 * @param columnFamily
	 *            列簇
	 * @param column
	 *            列
	 * @param value
	 *            值
	 ***/
	public static void insertOneRow(String tableName, String rowkey, String columnFamily, String column, String value)
			throws Exception {

		HTable table = new HTable(conf, tableName);
		Put put = new Put(Bytes.toBytes(rowkey));
		put.add(Bytes.toBytes(columnFamily), Bytes.toBytes(column), Bytes.toBytes(value));
		table.put(put);// 放入表
		table.close();// 释放资源
	}

	/**
	 * 批量添加数据
	 * 
	 * @param tableName
	 *            表名字
	 * @param rows
	 *            rows的数据格式："rowskey,columnFamily,column,value"
	 * 
	 **/
	public static void insertList(String tableName, List<String> rows) throws Exception {
		HTable table = new HTable(conf, tableName);
		List<Put> list = new ArrayList<Put>();
		for (String r : rows) {
			String[] splited = r.split(",");
			// 添加rowkey
			Put p = new Put(Bytes.toBytes(splited[0]));
			// 添加其他信息
			p.add(Bytes.toBytes(splited[1]), Bytes.toBytes(splited[2]), Bytes.toBytes(splited[3]));
			list.add(p);
		}
		table.put(list);// 批量添加
		table.close();// 释放资源
	}

	/**
	 * 删除一条数据
	 * 
	 * @param tableName
	 *            表名
	 * @param row
	 *            rowkey行键
	 * 
	 */
	public static void deleteOneRow(String tableName, String row) throws Exception {

		HTable table = new HTable(conf, tableName);
		Delete delete = new Delete(Bytes.toBytes(row));
		table.delete(delete);
		table.close();
	}

	/**
	 * 删除多条数据
	 * 
	 * @param tableName
	 *            表名
	 * @param rows
	 *            行健集合
	 * 
	 **/
	public static void deleteList(String tableName, String rows[]) throws Exception {
		HTable table = new HTable(conf, tableName);
		List<Delete> list = new ArrayList<Delete>();
		for (String row : rows) {
			Delete del = new Delete(Bytes.toBytes(row));
			list.add(del);
		}
		table.delete(list);
		table.close();// 释放资源
	}

	/**
	 * rowkey模糊条件查询
	 * 
	 * @param key
	 *            模糊rowkey内容
	 * @throws Exception
	 */
	public static ResultScanner fuzzyScan(String tableName, String startKey, String endKey) throws Exception {
		HTable table = new HTable(conf, tableName);
		Scan s = new Scan();
		// 可以通过时间限定查询范围
		// s.setStartRow(startKey.getBytes());
		// s.setStopRow(endKey.getBytes());
		return table.getScanner(s);
	}

	/**
	 * rowkey模糊条件查询
	 * 
	 * @param key
	 *            模糊rowkey内容
	 * @param columnFalimy
	 *            指定列簇
	 * @throws Exception
	 */
	public static void fuzzySearchFamily(String tableName, String key, String columnFalimy) throws Exception {
		HTable table = new HTable(conf, tableName);
		Scan s = new Scan();
		RowFilter filter = new RowFilter(CompareFilter.CompareOp.EQUAL, new SubstringComparator(key));
		s.setFilter(filter);
		s.addFamily(columnFalimy.getBytes());
		ResultScanner rs = table.getScanner(s);
		for (Result r : rs) {
			printRecoder(r);// 打印记录
		}
		table.close();// 释放资源
	}

	/**
	 * 获取一条数据，根据rowkey
	 * 
	 * @param tableName
	 *            表名
	 * @param row
	 *            行健
	 * 
	 **/
	public static Result getOneRow(String tableName, String row) throws Exception {
		HTable table = new HTable(conf, tableName);
		Get get = new Get(Bytes.toBytes(row));
		Result result = table.get(get);
		printRecoder(result);// 打印记录
		table.close();// 释放资源
		return result;
	}

	/**
	 * 查看某个表下的所有数据
	 * 
	 * @param tableName
	 *            表名
	 */
	public static void showAll(String tableName) throws Exception {
		HTable table = new HTable(conf, tableName);
		Scan scan = new Scan();
		scan.setStartRow(Bytes.toBytes(""));
		scan.setStopRow(Bytes.toBytes(""));
		ResultScanner rs = table.getScanner(scan);
		for (Result r : rs) {
			printRecoder(r);// 打印记录
		}
		table.close();// 释放资源
	}

	/**
	 * 具体查询row中的列簇的数据
	 * 
	 * @param tableName
	 *            表名
	 * @param rowKey
	 *            行健
	 * @param columnFalimy
	 *            列簇
	 */
	public static void getFamily(String tableName, String rowKey, String columnFalimy) throws Exception {
		HTable table = new HTable(conf, tableName);
		Get get = new Get(Bytes.toBytes(rowKey));
		get.addFamily(Bytes.toBytes(columnFalimy));
		Result r = table.get(get);
		printRecoder(r);
		table.close();// 释放资源
	}

	/**
	 * 具体查询row中的一条数据
	 * 
	 * @param tableName
	 *            表名
	 * @param rowKey
	 *            行健
	 * @param columnFalimy
	 *            列簇
	 * @param column
	 *            值
	 */
	public static void getColumn(String tableName, String rowKey, String columnFalimy, String column) throws Exception {

		HTable table = new HTable(conf, tableName);
		Get get = new Get(Bytes.toBytes(rowKey));
		get.addColumn(Bytes.toBytes(columnFalimy), Bytes.toBytes(column));
		Result r = table.get(get);
		printRecoder(r);
		table.close();// 释放资源
	}

	/**
	 * 打印一条记录的详情
	 * 
	 */
	public static void printRecoder(Result result) throws Exception {
		for (Cell cell : result.rawCells()) {
			System.out.print("行健: " + new String(CellUtil.cloneRow(cell)));
			System.out.print(" 列簇: " + new String(CellUtil.cloneFamily(cell)));
			System.out.print(" 列: " + new String(CellUtil.cloneQualifier(cell)));
			System.out.print(" 值: " + new String(CellUtil.cloneValue(cell)));
			System.out.println(" 时间戳: " + cell.getTimestamp());
		}
	}

	public static void getRecoder(Result result) {
		for (Cell cell : result.rawCells()) {
			System.out.print("行健: " + new String(CellUtil.cloneRow(cell)));
			System.out.print(" 列簇: " + new String(CellUtil.cloneFamily(cell)));
			System.out.print(" 列: " + new String(CellUtil.cloneQualifier(cell)));
			System.out.print(" 值: " + new String(CellUtil.cloneValue(cell)));
			System.out.println(" 时间戳: " + cell.getTimestamp());
		}
	}

	public static void main(String[] args) throws Exception {

		List<String> list = new ArrayList<String>();

		for (int i = 1; i < 101; i++) {
			String s = i+",data,info,"+System.currentTimeMillis()+"#info#"+i;
			String ss = i+",data,other,"+System.currentTimeMillis()+"#other#"+i;
			list.add(s);
			list.add(ss);
		}

		HbaseCommons.insertList("record", list);
	}
}
```