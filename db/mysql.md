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

Using filesort和Using temporary非常不受欢迎，它们越少越好。组合索引可能产生副作用（normal_key）。

将数据表转为MyISAM类型：
```
alter table key_t type=myisam;
```