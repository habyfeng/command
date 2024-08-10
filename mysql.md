## mysqldump
```sql
mysqldump  -n -t sakiladb -uroot -p"123456" -P 3306 --where="OccurredTime>'2016-12-01 00:00:00'">/root/spider/epgdb.sql
```

## 从select中创建表：
```sql
create table film20240809 (select * from sakiladb.film)
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

```sql
alter table employee add unique emp_name2(cardnumber);
```
employee：表名；

emp_name2：索引名称；

cardnumber：字段名；


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



