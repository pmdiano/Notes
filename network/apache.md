mod_status： 查看状态

压力测试工具: ab; LoadRunner; Jmeter

prefork模式：父进程预先创建一定量的子进程，所有子进程竞争accept用户请求。

查看某时刻的httpd进程数： `ps afx | grep httpd | wc -l`

XBitHack

mod_cache：服务器缓存。有mod_disk_cache和mod_mem_cache两个扩展，为mod_cache提供缓存引擎。官方推荐使用mod_disk_cache。Lighttpd提供了mod_trigger_b4_dl模块。在Apache中开启磁盘缓存时必须在Apache编译时为configure追加必要的模块：
```
--enable-cache=shared --enable-disk-cache=shared --enable-so
```
还有一些需要增加的配置，详见原书。mod_file_cache模块缓存文件描述符。