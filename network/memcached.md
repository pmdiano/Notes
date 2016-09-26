启动memcached的时候，需要指定分配给缓存区的内存大小。比如我们分配了4GB的内存作为缓冲区：
```
memcached -d -m 4086 -l 10.0.1.12 -p 11711
```
memcached使用key-value的方式来存储数据。这是一种单索引的结构化数据组织形式。所有数据项之间彼此独立，每个数据项都以key作为唯一索引。数据项查询的时间复杂度是O(1)。memcached使用libevent函数库来实现网络并发模型。

memcached提供了原子递增操作。