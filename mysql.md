## mysqldump

#### 不指定表名：
```sql
mysqldump -n -t sakiladb -uroot -p"123456" -P 3306 --default-character-set=utf8 --where="OccurredTime>'2016-12-01 00:00:00'" > /root/data/sakiladb.sql
```

- -t：只导数据
#### 指定表名：
```sql
mysqldump -n -t sakiladb -uroot -p"123456" -P 3306 --tables table_name1 table_name2 --default-character-set=utf8 --where="OccurredTime>'2016-12-01 00:00:00'" > /root/data/sakiladb.sql
```

#### 导出表结构：
```sql
mysqldump -d sakiladb -uroot -p"123456" -P 3306 --default-character-set=utf8 > /root/data/sakiladb.sql
```


## 从select中创建表：
```sql
create table film20240809 (select * from sakiladb.film)
```

## mysql命令连接时指定编码：
```sh
 ./mysql -uroot -p123456 -D sakia --default-character-set=utf8 
```

## 查询条件使用正则
```sql
select * from kbv2_community where boundary REGEXP '.*(\\|)+.*' limit 10
```

## 对分组结果过滤

我们知道WHERE 子句用于过滤结果，但是对于分组的过滤WHERE子句不行。

因为WHERE子句，是针对行的过滤。要对分组结果进行过滤，必须使用HAVING子句，HAVING子句能针对分组的结果进行过滤。

举例：在订单表中，检索出具有两个以上订单的客户id以及订单数量。
```sql
SELECT cust_id,COUNT(*) AS orders FROM orders GROUP BY cust_id HAVING orders>=2;
```

显示每个地区的总人口数和总面积，仅显示那些面积超过1000000的地区。
```sql
SELECT region, SUM(population), SUM(area)
FROM bbc
GROUP BY region
HAVING SUM(area)>1000000
```

## exists

EXISTS用于检查子查询是否至少会返回一行数据，该子查询实际上并不返回任何数据，而是返回值True或False。

```sql
SELECT c.CustomerId, CompanyName  
	FROM Customers c  
	WHERE EXISTS(  
	    SELECT OrderID FROM Orders o  
	    WHERE o.CustomerID = cu.CustomerID)  
```


##  mysql IPV6:
```
[mysqld]
bind-address=::
```

## GRANT命令：
```sql
GRANT  ALL ON *.* to admin@'::1' IDENTIFIED BY '123456';

GRANT  ALL ON *.* to admin@'fe80::20c:29ff:fec4:ae9c' IDENTIFIED BY '123456';

GRANT  ALL ON *.* to admin@'%' IDENTIFIED BY '123456';
```

## 导入文件到数据库
```sql
load data infile 'C:/Users/Administrator/Desktop/films.csv'   
into table films    
fields terminated by ','  optionally enclosed by '"' escaped by '"'   
lines terminated by '\r\n';
```
## 修改字段类型
```sql
alter table address modify column city varchar(50);
```

## 修改字段名称和类型
```sql
 ALTER TABLE table_name CHANGE old_field_name new_field_name field_type;
```

## 增加字段：
```sql
ALTER TABLE table_name ADD field_name field_type;
```

## 删除字段
```sql
MySQL ALTER TABLE table_name DROP field_name;
```

## 添加索引

- 添加唯一索引
```sql
alter table employee add unique index emp_name2(cardnumber);
```
employee：表名；

emp_name2：索引名称；

cardnumber：字段名；

- 添加普通索引
```sql
alter table employee add index emp_name2(cardnumber);
```


## 删除索引

```sql
alter table employee drop index emp_name;
```
emp_name是索引名称

## profiling
```
set profiling=1;
show profiles;
show profile for query 1; 
```

## 修改索引名称
```sql
ALTER TABLE t1 RENAME INDEX col1 TO c1;
```

## general_log

打开日志:
```sql
SET GLOBAL general_log = 'ON';
```

关闭日志
```sql
SET GLOBAL general_log = 'OFF';
```

配置文件设置General Log的开关：
```
[mysqld]
general_log = 1
general_log_file = 文件路径
```

## 查看mysql目录
查看mysql安装目录
```sql
select @@basedir;
```

查看mysql数据文件存放目录
```sql
select @@datadir;
```





