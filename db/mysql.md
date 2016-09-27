通过以下指令来获取当前数据库的实时状态：
```
show status;
show innodb status;
```
mysqlreport是一个第三方的状态报告工具。

创建数据表：
```sql
CREATE TABLE `test` (
  `id` int(11) NOT NULL auto_increment,
  PRIMARY KEY (`id`)
)
```

用explain分析基于主键的条件查找：`explain select * from test where id=1`。

为name字段添加索引：`alter table test add key name(name);`。对于没有使用主键索引或者唯一索引的条件查询，查询结果可能会有多个匹配行，MySQL为这种情况定义的type为ref。

组合索引：
```sql
CREATE TABLE `key_t` (
  `id` int(11) NOT NULL auto_increment,
  `key1` int(11) NOT NULL default '0',
  `key2` int(11) NOT NULL default '0',
  `key3` int(11) NOT NULL default '0',
  PRIMARY KEY (`id`),
  KEY `normal_key` (`key1`,`key2`,`key3`)
) ENGINE=InnoDB
```

一些命令：
```sql
select count(id) from key_t;
select * from key_t where key1=1
select * from key_t where key1=1 and key2=2
select * from key_t where key1=1 and key2=2 and key3=3
```

Using filesort和Using temporary非常不受欢迎，它们越少越好。组合索引可能产生副作用。

将数据表转为MyISAM类型：
```sql
alter table key_t type=myisam;
```

在MySQL中开启慢查询日志：在my.cnf中增加以下配置选项：
```
long_query_time = 1
log-slow-queries = /data/var/mysql_slow.log
```
以上将记录查询时间超过1秒的查询。`log-queries-not-using-indexes`将所有没有使用索引的查询记录下来。MySQL提供了mysqldumpslow，还有第三方工具mysqlsla：
```
mysqlsla -lt slow /data/var/mysql_slow.log
```
大多数的慢查询都是因为索引使用不当，其他的原因包括查询语句过于复杂（比如联合查询）或者数据表记录数过多，通过反范式化设计和数据分区可以有效改善这一状况。

任何一个MyISAM类型的表包括以下三个文件：
+ frm：存储了表的结构信息
+ MYD：存储了表中的数据
+ MYI：存储了表中的索引

对于索引写缓存机制，Innodb采用预写日志方式（Write-Ahead Logging，WAL）来实现事物，也就是只有当事物日志写入磁盘后才更新数据和索引，这样即使数据库崩溃，也可以通过事物日志来恢复数据和索引。

查看所有线程的状态：
```
show processlist\G;
```
MySQL在Innodb类型表中提供了行锁的支持。（Innodb一定是行锁定，MyISAM一定是表锁定吗？）Innodb除了支持行锁定外，还支持事务。当有事务提交时，Innodb首先将它写到内存中的事务日志缓冲区，随后当事务日志写入磁盘时，Innodb才更新实际数据和索引。关键点事事务日志何时写入磁盘，MySQL的配置选项有三个可选额值：
+ innodb_flush_log_at_trx_commit = 1：事务提交时事务日志立即写入磁盘
+ innodb_flush_log_at_trx_commit = 0：事务日志每隔1秒写入磁盘并刷新磁盘
+ innodb_flush_log_at_trx_commit = 2：事务日志立即写入磁盘，每隔1秒刷新到磁盘

直接I/O： `innodb_flush_method = O_DIRECT`

MySQL默认是没有开启查询缓存的，以下配置开启256MB查询缓存空间：
```
query_cache_size = 268435456
query_cache_type = 1
query_cache_limit = 1048576
```

tmp_table_size选项可以设置用来存储临时表的内存空间大小。

线程池可以在my.conf中设置以下选项：`thread_cache_size = 100`。

范式是对关系数据库中关系的一定要求，不同程度的要求为不同的范式，满足最低要求的称为第一范式（1NF），在此基础上满足进一步要求的称为第二范式（2NF）。在实际的关系模式设计中，我们通常遵循第三范式（3NF），要求在一个数据表中，非主键字段之间不能存在依赖关系。