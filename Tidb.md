### tiup cluster display
```
tiup cluster display tidb-xxx
```

说明：
- tidb-xxx 集群名称

### insert ignore

1. INSERT IGNORE 语句用于在插入数据时，若遇到主键或唯一索引冲突，会静默地忽略（放弃）插入冲突的行，而不会返回错误，并继续处理其他行的插入，最后可以通过 SHOW WARNINGS 查看被忽略的行数。
> 注意
2. 经过测试，如果插入的数据字段超长，INSERT IGNORE会截取超长的字符串，并插入成功。 初步确定ignore 忽略的是所有错误。

### 悲观事务模式

悲观事务的行为和 MySQL 基本一致。

悲观事务中引入快照读和当前读的概念：

- 快照读是一种不加锁读，读的是该事务开始时刻前已提交的版本。SELECT 语句中的读是快照读。
- 当前读是一种加锁读，读取的是最新已提交的版本，UPDATE、DELETE 、INSERT、SELECT FOR UPDATE 语句中的读是当前读。

- 如果 Point Get 和 Batch Point Get 算子没有读到数据，依然会对给定的主键或者唯一键加锁，阻塞其他事务对相同主键唯一键加锁或者进行写入操作。
  > 注意：经过测试，SELECT FOR UPDATE如果没有读到数据，不会对给定的主键或者唯一键加锁，不会阻塞其他事务对相同主键唯一键加锁或者进行写入操作。这里说的Point Get 和 Batch Point Get 算子可能和SELECT FOR UPDATE不是一回事。

### LOAD DATA LOCAL INFILE

```sql
LOAD DATA LOCAL INFILE '/path/to/users.csv'
INTO TABLE users
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
(user_id, username, @status)
SET status = NULLIF(@status, ''); -- 将空字符串转换成NULL
```

- NULLIF(expr1, expr2): MySQL函数，如果expr1等于expr2，则返回NULL，否则返回expr1