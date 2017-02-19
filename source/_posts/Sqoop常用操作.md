----
title: Sqoop抽数
date: 2016-07-06 16:45:22
tags: [大数据, Sqoop]
----

## 从Oracle抽数到Hbase

```Shell
sudo -u hdfs sqoop import -connect jdbc:oracle:thin:@192.168.61.27:1521:orcl  -username pira -password pira -table DT_CLEAR_DATA --hbase-table dt_clear_data --column-family info --hbase-row-key ID --hbase-create-table -columns ID,TEXT
```

## 从Oracle抽数到Hive
```Shell
sudo -u hdfs sqoop import --driver oracle.jdbc.driver.OracleDriver --connect jdbc:oracle:thin:@192.168.1.104:1521:ora11g --username crpiradev --password crpiradev --append --query "SELECT * FROM DT_CLEAR_DATA WHERE 1=1 AND \$CONDITIONS" --target-dir /usr/local/tmp/hivebackup --split-by DT_CLEAR_DATA.ID
```

## 从Oracle抽数到Hive
* 根据Oracle中某表，在HIVE中建立相同的表
```Shell
sqoop create-hive-table -connect jdbc:oracle:thin:@192.168.1.51:1521:orcl -username pira -password 'pira!51' -table DT_MANAGE --hive-table fall_in_db.DT_MANAGE
```
* 抽数
```HiveSql
LOAD DATA INPATH '/amarsoft/test/hivebackup/dt_clear_data/part-m-*' OVERWRITE INTO TABLE dt_clear_data 
```