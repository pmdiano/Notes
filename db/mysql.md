## 基本的SQL语句
```sql
# SELECT
SELECT prod_id FROM Products;
SELECT prod_id, prod_name, prod_price FROM Products;
SELECT * FROM Products;
SELECT DISTINCT vend_id FROM Products;
SELECT prod_name FROM Products LIMIT 5 OFFSET 5;
SELECT prod_name FROM Products LIMIT 5,5;   -- same as above

# ORDER BY
SELECT prod_name FROM Products ORDER BY prod_name;
    /* ORDER BY子句应当保证是SELECT语句中最后一条字句 */
SELECT prod_id, prod_price, prod_name FROM Products ORDER BY prod_price, prod_name;
SELECT prod_id, prod_price, prod_name FROM Products ORDER BY 2,3;
SELECT prod_id, prod_price, prod_name FROM Products ORDER BY prod_price DESC;
SELECT prod_id, prod_price, prod_name FROM Products ORDER BY prod_price DESC, prod_name;

# WHERE子句
SELECT prod_name, prod_price FROM Products WHERE prod_price = 3.49;
SELECT prod_name, prod_price FROM Products WHERE prod_price < 10;
SELECT vend_id, prod_name FROM Products WHERE vend_id <> 'DLL01';
SELECT prod_name, prod_price FROM Products WHERE prod_price BETWEEN 5 AND 10;
SELECT prod_name FROM Products WHERE prod_price IS NULL;

# 组合WHERE子句
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' AND prod_price <= 4;

SELECT prod_name, prod_price
FROM Products
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01';

SELECT prod_name, prod_price
FROM Products
WHERE (vend_id = 'DLL01' OR vend_id = 'BRS01') AND prod_price >= 10;

# IN操作符
SELECT prod_name, prod_price
FROM Products
WHERE vend_id IN ('DLL01', 'BRS01')
ORDER BY prod_name;

# NOT操作符
SELECT prod_name
FROM Products
WHERE NOT vend_id = 'DLL01'
ORDER BY prod_name;

# LIKE操作符
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE 'Fish%';   -- %匹配任意字符出现任意多次

SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE '__ inch teddy bear';  -- _匹配单个字符

# 计算字段
SELECT CONCAT(RTRIM(vend_name), ' (', RTRIM(vend_country), ')')
FROM Vendors
ORDER BY vend_name;

SELECT CONCAT(RTRIM(vend_name), ' (', RTRIM(vend_country), ')')
    AS vend_title
FROM Vendors
ORDER BY vend_name;

SELECT prod_id,
       quantity,
       item_price,
       quantity*item_price AS expanded_price
FROM OrderItems
WHERE order_num = 20008;

# 函数
SELECT vend_name, UPPER(vend_name) AS vend_name_upcase FROM Vendors ORDER BY vend_name;

SELECT cust_name, cust_contact
FROM Customers
WHERE SOUNDEX(cust_contact) = SOUNDEX('Michael Green');

SELECT order_num FROM Orders WHERE YEAR(order_date) = 2012;

# 聚集函数
SELECT COUNT(*) FROM Customers;
SELECT COUNT(cust_email) AS num_cust FROM Customers;  -- 忽略NULL值
SELECT AVG(prod_price) AS avg_price FROM Products;
SELECT AVG(prod_price) AS avg_price FROM Products WHERE vend_id = 'DLL01';
SELECT MAX(prod_price) AS max_price FROM Products;
SELECT SUM(quantity) AS items_ordered FROM OrderItems WHERE order_num = 20005;
SELECT SUM(item_price*quantity) AS total_price FROM OrderItems WHERE order_num = 20005;
SELECT AVG(DISTINCT prod_price) AS avg_price FROM Products WHERE vend_id = 'DLL01';

SELECT COUNT(*) AS num_items,
       MIN(prod_price) AS price_min,
       MAX(prod_price) AS price_max,
       AVG(prod_price) AS price_avg
FROM Products;

# 分组数据：GROUP BY语句和HAVING语句
SELECT vend_id, COUNT(*) AS num_prods FROM Products GROUP BY vend_id;
SELECT cust_id, COUNT(*) AS orders FROM Orders GROUP BY cust_id HAVING COUNT(*) >= 2;

-- WHERE过滤行，HAVING过滤分组；WHERE在数据分组前过滤，HAVING在数据分组后过滤
SELECT vend_id, COUNT(*) AS num_prods
FROM Products
WHERE prod_price >= 4
GROUP BY vend_id
HAVING COUNT(*) >= 2;

SELECT order_num, COUNT(*) AS items
FROM OrderItems
GROUP BY order_num
HAVING COUNT(*) >= 3
ORDER BY items, order_num;

# 子查询
SELECT cust_name, cust_contact
FROM Customers
WHERE cust_id IN (SELECT cust_id
                  FROM Orders
                  WHERE order_num IN (SELECT order_num
                                      FROM OrderItems
                                      WHERE prod_id = 'RGAN01'));

SELECT cust_name,
       cust_state,
       (SELECT COUNT(*)
        FROM Orders
        WHERE Orders.cust_id = Customers.cust_id) AS orders
FROM Customers
ORDER BY cust_name;

# 联结表（JOIN）
SELECT vend_name, prod_name, prod_price
FROM Vendors, Products
WHERE Vendors.vend_id = Products.vend_id;

-- 内联结（equijoin），与上相同
SELECT vend_name, prod_name, prod_price
FROM Vendors INNER JOIN Products
ON Vendors.vend_id = Products.vend_id;

-- 联结多个表
SELECT prod_name, vend_name, prod_price, quantity
FROM OrderItems, Products, Vendors
WHERE Products.vend_id = Vendors.vend_id
AND OrderItems.prod_id = Products.prod_id
AND order_num = 20007;

-- 与子查询中第一个例子一样
SELECT cust_name, cust_contact
FROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id
AND OrderItems.order_num = Orders.order_num
AND prod_id = 'RGAN01';

# 自联结
-- 使用子查询：
SELECT cust_id, cust_name, cust_contact
FROM Customers
WHERE cust_name = (SELECT cust_name
                   FROM Customers
                   WHERE cust_contact = 'Jim Jones');

-- 自联结：
SELECT c1.cust_id, c1.cust_name, c1.cust_contact
FROM Customers AS c1, Customers AS c2
WHERE c1.cust_name = c2.cust_name
AND c2.cust_contact = 'Jim Jones';

SELECT C.*, O.order_num, O.order_date,
       OI.prod_id, OI.quantity, OI.item_price
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND OI.order_num = O.order_num
AND prod_id = 'RGAN01';

# 内联结
SELECT Customers.cust_id, Orders.order_num
FROM Customers INNER JOIN Orders
ON Customers.cust_id = Orders.cust_id;

# 外联结
SELECT Customers.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;

# UNION去除重复行，而UNION ALL不去除重复行
# 还有EXCEPT（MINUS）及INTERSECT，但不常用

# 插入数据
INSERT INTO Customers(cust_id,
                      cust_name,
                      cust_address,
                      cust_city,
                      cust_state,
                      cust_zip,
                      cust_country,
                      cust_contact,
                      cust_email)
VALUES('1000000006',
       'Toy Land',
       '123 Any Street',
       'New York',
       'NY',
       '11111',
       'USA',
       NULL,
       NULL);
# 还有INSERT SELECT

# 从一个表复制到另一个表
CREATE TABLE CustCopy AS
SELECT * FROM Customers;

# 更新数据
UPDATE Customers
SET cust_contact = 'Sam Roberts',
    cust_email = 'sam@toyland.com'
WHERE cust_id = '1000000006';

# 删除数据
DELETE FROM Customers
WHERE cust_id = '1000000006';

# 创建表
-- 默认时间：DEFAULT CURRENT_DATE()

# 更改表
ALTER TABLE Vendors
ADD vend_phone CHAR(20);

ALTER TABLE Vendors
DROP COLUMN vend_phone;

# 删除表
DROP TABLE CustCopy;

# 视图
# 创建视图：CREATE VIEW viewname;
# 删除视图：DROP VIEW viewname;
CREATE VIEW ProductCustomers AS
SELECT cust_name, cust_contact, prod_id
FROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id
AND OrderItems.order_num = Orders.order_num;

# 执行存储过程
EXECUTE AddNewProduct('Foo', 'Bar', 6.49)

# 事务处理
-- 事务处理可以用来管理INSERT，UPDATE和DELETE语句。不能回退SELECT语句，也不能回退CREATE或DROP操作。
START TRANSACTION
SAVEPOINT name;
ROLLBACK TO name;
COMMIT;

# 游标
-- 创建游标
DECLARE CustCursor CURSOR
FOR
SELECT * FROM Customers
WHERE cust_email IS NULL;

-- 使用游标
OPEN CURSOR CustCursor;

# 创建索引
CREATE INDEX prod_name_ind
ON Products(prod_name);
```

## 基本的MySQL
启动和登录MySQL：
```bash
brew info mysql     # 显示相关信息
mysql.server start
mysql -u root -p
```

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

MySQL的主从复制是依据主服务器的二进制日志进行的。这种复制是异步进行的，主服务器上对于所有更新操作记录二进制文件，这部分额外开销对性能大概有1%的影响。读写分离：对于所有的更新操作，我们必须将它作用于主服务器上。

数据库返反向代理：MySQL可以用MySQL Proxy，它工作在应用程序和MySQL服务器之间，负责所有请求和相应数据的转发。MySQL Proxy通过Lua脚本来描述转发规则。MySQL Proxy默认监听在4040端口，我们可以在启动时通过选项参数来指定后端的MySQL服务器：
```
mysql-proxy \
--proxy-read-only-backend-addresses=10.0.1.202:3306 \
--proxy-backend-addresses=10.0.1.201:3306 \
--proxy-lua-script=/etc/mysql-proxy/rw-splitting.lua &
```

分区反向代理：Spock Proxy，基于MySQL Proxy。

[登录](http://www.cnblogs.com/mr-wid/archive/2013/05/09/3068229.html)：`mysql -h 主机名 -u 用户名 -p`

显示数据库：`show databases;`

创建数据库：`create database 数据库名 [其它选项];`

选择所要操作的数据库：一是在登录数据库时指定，`mysql -D 所选择的数据库名 -h 主机名 -u 用户名 -p`，二是在登录后使用 `use 数据库名;`

显示表：`show tables;`

创建表：`create table 表名称(列声明);`，如下例子：
```sql
mysql> create table students
    -> (
    -> id int unsigned not null auto_increment primary key,
    -> name char(8) not null,
    -> sex char(4) not null,
    -> age tinyint unsigned not null,
    -> tel char(13) null default "-"
    -> );
Query OK, 0 rows affected (0.03 sec)
```
也可以将语句保存为sql文件：`mysql -D 数据库名 -u root -p < createtable.sql`

查看已创建的表的详细信息：`describe 表名;`:
```
mysql> describe students;
+-------+---------------------+------+-----+---------+----------------+
| Field | Type                | Null | Key | Default | Extra          |
+-------+---------------------+------+-----+---------+----------------+
| id    | int(10) unsigned    | NO   | PRI | NULL    | auto_increment |
| name  | char(8)             | NO   |     | NULL    |                |
| sex   | char(4)             | NO   |     | NULL    |                |
| age   | tinyint(3) unsigned | NO   |     | NULL    |                |
| tel   | char(13)            | YES  |     | -       |                |
+-------+---------------------+------+-----+---------+----------------+
5 rows in set (0.02 sec)
```

向表中插入数据：`insert [into] 表名 [(列名1, 列名2, 列名3, ...)] values (值1, 值2, 值3, ...);`，如：
```
mysql> insert into students values(NULL, "王刚", "男", 20, "13811371377");
Query OK, 1 row affected (0.01 sec)

mysql> insert into students (name, sex, age) values("孙丽华", "女", 21);
Query OK, 1 row affected (0.00 sec)
```

查询表中的数据：`select 列名称 from 表名称 [查询条件];`
```
mysql> select name, age from students;
+-----------+-----+
| name      | age |
+-----------+-----+
| 王刚      |  20 |
| 孙丽华    |  21 |
+-----------+-----+
2 rows in set (0.00 sec)

mysql> select * from students where age > 21;
Empty set (0.00 sec)

mysql> select * from students where name like "%王%";
+----+--------+-----+-----+-------------+
| id | name   | sex | age | tel         |
+----+--------+-----+-----+-------------+
|  1 | 王刚   | 男  |  20 | 13811371377 |
+----+--------+-----+-----+-------------+
1 row in set (0.00 sec)

mysql> select * from students where id<5 and age>20;
+----+-----------+-----+-----+------+
| id | name      | sex | age | tel  |
+----+-----------+-----+-----+------+
|  2 | 孙丽华    | 女  |  21 | -    |
+----+-----------+-----+-----+------+
1 row in set (0.00 sec)
```

更改表中的数据：`update 表名称 set 列名称=新值 where 更改条件;`，如：
```sql
update students set tel=default where id=5;
update students set age=age+1;
update students set name="张伟鹏", age=19 where tel="13288957398";
```

删除表中的数据：`delete from 表名称 where 删除条件;`，如：
```sql
delete from students where id=2;
delete from students where age<20;
delete from students; # 删除表中所有数据
```

创建后表的修改，添加列：`alter table 表名 add 列名 列数据类型 [after 插入位置];`，如：
```sql
alter table students add address char(60);          # 在表的最后追加列
alter table students add birthday date after age;   # 在名为age的列后插入列birthday
```

修改列：`alter table 表名 change 列名称 列新名称 新数据类型;`
```sql
alter table students change tel telephone char(13) default "-"; # 将表tel列改名为telephone
alter table students change name name char(16) not null;        # 将name列的数据类型改为char(16)
```

删除列：`alter table 表名 drop 列名称;`
```sql
alter table students drop birthday; # 删除birthday列
```

重命名表：`alter table 表名 rename 新表名字;`
```sql
alter table students rename workmates; # 重命名studetns表为workmates
```

删除整张表：`drop table 表名;`

删除整个数据库：`drop database 数据库名;`

修改root用户密码：`mysqladmin -u root -p password 新密码`

可视化管理工具：MySQL Workbench
