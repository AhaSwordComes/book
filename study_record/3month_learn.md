背景

解决了什么问题

与其他相比优缺点

各自适合的场景

# MySQL

## 调优

取自  [酷壳](https://coolshell.cn/articles/1846.html)

### explain

执行计划怎么看？

key rows extra filter ref

explain analyze 看执行过程和预计耗时 [mysqlblog](https://dev.mysql.com/blog-archive/mysql-explain-analyze/#:~:text=EXPLAIN%20ANALYZE%20is%20a%20profiling,points%20in%20the%20execution%20plan.)

### 缓存

已被取消，why?	效果不明显，且耗费性能，分布式等问题，不如使用外部缓存redis之类（存疑）

### 只要一行时limit 1

### 为搜索字段建索引

索引有几种，原理（为什么能加速），各自特点，各自场景

不同引擎的索引有区别吗

最左前缀：index (column1,column2,column3), where column1 = cxx，column2=xxx,可用到索引，column2=xx,column1=xxx 用不到索引

### join表时使用字段类型应该一致，并建索引

### 勿用order by rand()

### 避免select*

原因？1、浪费io资源 2、不便于理解，（有人说表加字段了不用改代码也不会报错，然后当某天数据库性能瓶颈了想优化这里，却找不到原来是想要哪些字段）3、查询慢

### 每个表设置个id

最好unsigned int,自增

varchar做主键降低性能,why?'

分区集群时需要使用主键,why?

### 使用enum 而非varchar

enum实际保存的tinyint，快而紧凑？why?

###  PROCEDURE ANALYSE()取得建议

mysql 自带的分析优化，但5.7 decrepted,8.0 removed

替代？

### 尽可能使用not null

你知道吗？在 Oracle 里，NULL 和 Empty 的字符串是一样的

null亦占用空间（细说？）



### prepared statements

是什么，为什么能快

防sql注入攻击,ORM框架很多都有（mybatis是怎么做的？）

### unbuffered_query()

细说

### ip 存成unsigned int 

unsigned int 是个啥，和int difference

### 固定长度的表更快



剩下的分批做



## 引擎





## 生成测试数据

来源[知乎文章](https://zhuanlan.zhihu.com/p/85417466)

创建数据表

```sql
CREATE TABLE `vote_record` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` varchar(20) NOT NULL,
  `vote_id` int(11) NOT NULL,
  `group_id` int(11) NOT NULL,
  `create_time` datetime NOT NULL,
  PRIMARY KEY (`id`),
  KEY `index_user_id` (`user_id`)
) ENGINE = InnoDB DEFAULT CHARSET = utf8mb4;
```

创建内存表

```mysql
CREATE TABLE `vote_record_memory` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` varchar(20) NOT NULL,
  `vote_id` int(11) NOT NULL,
  `group_id` int(11) NOT NULL,
  `create_time` datetime NOT NULL,
  PRIMARY KEY (`id`),
  KEY `index_user_id` (`user_id`)
) ENGINE = InnoDB DEFAULT CHARSET = utf8mb4;
```

创建存储过程

```mysql
CREATE DEFINER=`root`@`%` PROCEDURE `add_vote_memory`(IN n int)
BEGIN
    DECLARE i INT DEFAULT 1;
    WHILE (i <= n) DO
        INSERT INTO vote_record_memory (user_id, vote_id, group_id, create_time) VALUES (substring(MD5(RAND()),1,20), FLOOR(RAND() * 1000), FLOOR(RAND() * 100), NOW());
        SET i = i + 1;
    END WHILE;
END
```

执行前需执行

```mysql
DELIMITER $$ #将分号;暂时注销原先功能，要不然存储过程中间的分号就会报错
```

调用存储过程

```mysql
CALL add_vote_memory(1000000) 
```



### 用到的命令

```mysql
show processlist; 
kill 'processId';
delimiter;
```

## 疑问

视图是啥？
