缓存：Smarty，Xcache

opcode缓存：在APC（Alternative PHP Cache）下是在php.ini中打开opcode cache的开关：`apc.cache_by_default = on`

Xdebug是一个PHP的PECL扩展，它提供了一一组用于代码跟踪和调试的API。`xdebug_time_index()`方法返回从脚本开始处执行到该位置所花费的时间。
一些API：
```
xdebug_call_line()
xdebug_call_funtion()
xdebug_start_code_coverage()
xdebug_get_code_coverage()
```
函数跟踪：要在php.ini中设置记录文件的存储目录和文件名前缀：
```
xdebug.trace_output_dir = /tmp/xdebug
xdebug.trace_output_name = trace.%c
```
Profiler：在脚本程序运行的时候自动将性能记录文件写入我们指定的目录中：
```
xdebug.profiler_output_dir = /tmp/xdebug
xdebug.profiler_output_name = cachegrind.output.%p // "%p"是运行时PHP解释器所在的进程PID
```
KCacheGrind是一个GUI分析性能记录文件的工具。

PHP编写的用于分发文件的例子：
```php
<?php
$conn = ssh2_connect("10.0.1.201", 22);
ssh2_auth_password($conn, "user", "pwd");
ssh2_scp_send($conn, "/home/user/list.htm", "/home/user/list.htm", 0644);
?>
```

使用SFTP的例子：
```php
<?php
$conn = ssh2_connect("10.0.1.201", 22);
ssh2_auth_password($conn, "user", "pwd");
$sftp = ssh2_sftp($conn);
ssh2_sftp_mkdir($sftp, "/home/user/newdir", 0666);
?>
```