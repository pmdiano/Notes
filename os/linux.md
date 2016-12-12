查看/proc/loadavg，可以了解到运行队列的情况： `cat /proc/loadavg`。也可用top，w等工具，从实现方法上看，这些工具获得的系统负载都是来源于/proc/loadavg。

Nmon工具可以监视服务器每秒上下文切换次数。

top查看进程状态时，RES值表示其占用的物理内存空间，SWAP值表示其使用的虚拟内存空间，VIRT值则等于RES和SWAP的总和，PR是优先级。IOWait是指CPU空闲并且等待I/O操作完成的时间比例。

strace: 查看进程的系统调用。

改变最大文件描述符数量： `ulimit -n 30000`（需在root身份下）

epoll监视的描述符数量不受限制，它所支持的FD上限是最大可以打开文件的数目，这个数字一般远大于2048,举个例子,在1GB内存的机器上大约是10万左 右，具体数目可以`cat /proc/sys/fs/file-max`察看。

uptime: 查看系统已运行时间。

`/etc/hosts`

vmstat系统工具：可以观察物理内存和swap的使用情况。

iptables命令

添加默认网关：`route add default gw 10.0.1.50`

查看内核中是否已经安装IPVS模块：`modprobe -l | grep ipvs`。IPVS的管理工具是ipvsadm，提供了基于命令行的配置界面，可以通过它快速实现负载均衡系统，也成为LVS（Linux Virtual Server）。

ifconfig命令

arp命令

查看Bonding：`cat /proc/net/bonding/bond0`

将10.0.1.200这台服务器的/data目录共享给10.0.1.201这台服务器，需要再10.0.1.200上声明如下：`vi /etc/exports /data 10.0.1.201(rw,sync)`。然后启动NFS服务器端：`/etc/init.d/nfsserver start`。然后我们需要在10.0.1.201这台服务器上执行mount操作，将10.0.1.200共享的目录绑定到自己的文件系统中：`mount -t nfs 10.0.1.200:/data /mnt/data`。

rpcinfo：查看所有基于RPC的服务。

nfsstat：NFS的I/O统计界面。

time：统计时间。

查看内核版本：uname -a

系统实时监控：Nmon，Nmon Analyzer。

查看详细的内存使用情况：`cat /proc/meminfo`。

查看系统负载和进程队列状态：`cat /proc/loadavg`。

SNMP服务器端本身便是一个出色的监控处理程序。通过SNMP来获取另一台服务器的所有设备状态：`snmpwalk -c public -v 2c 10.0.1.201`。包括MRTG、Cacti以及Nagios在内的很多监控工具都利用SNMP来监控远程服务器，而你只需要在被监控的服务器上开启SNMP服务，同时对监控来源进行授权配置即可。对于没有提供SNMP支持的，比如，Nginx提供了必要的HTTP监控接口。

系统监控：通常我们通过SNMP便可以很容易地对服务器进行一些常规的系统监控，这包括CPU使用率、系统负载、内存使用率、网络I/O、磁盘I/O、磁盘使用率等。Cacti完全支持这些系统监控，并且绘制出相应的图表📈。Cacti采用RRDtool作为监控数据的存储引擎，它是一种专门针对绘制坐标图而设计的存储格式。Cacti中支持插件机制，可以通过一些第三方模板来监控应用层服务的状态。

查看网络流量：`ifstat -bi eth2`

type -a python

sysctl: get or set kernel state. `sysctl -a | grep pid_max` [link](http://blog.chinaunix.net/uid-20662820-id-5690021.html)

+ `/proc/sys/kernel/pid_max` #操作系统进程数限制（or线程数限制？）
+ `/proc/sys/kernel/thread-max` #操作系统线程数
+ `max_user_process (ulimit -u)` #系统限制某用户下最多可以运行多少进程或线程
+ `/proc/sys/vm/max_map_count` #单进程mmap的限制会影响单个进程可创建的线程数
+ `/sys/fs/cgroup/memory/${cgroup}/memory.kmem` #单个docker内核内存的限制，可以影响task_struct等slab节点的申请，间接影响可创建的线程数

ping: ping命令作为检测网络连通性最常用的工具，其适用的传输协议既不是TCP，也不是UDP，而是ICMP。[link](http://segmentfault.com/a/1190000007350218)

netstat：查看Socket信息的老牌命令，常用的选择有：

+ -t 只显示TCP连接
+ -u 只显示UDP连接
+ -n 不用解析hostname，用IP显示主机，可以加快执行速度
+ -p 查看连接的进程信息
+ -l 只显示监听的连接

ss是新兴的命令，其选项和netstat差不多，主要区别是能够进行过滤（通过state与exclude关键字）。

df: 查看磁盘剩余空间的数量

free: 显示空闲内存的数量

ls的选项：-F (classify), -t (按照修改时间排序)，-d (directory，列出目录的详细信息而不是目录中的内容)，-S（按照文件大小排序）

file：打印文件类型

less：打印文件内容：

+ e：向前移动一行
+ y：向后移动一行
+ d：向前移动半页
+ u：向后移动半页
+ f：向前移动一页
+ b：向后移动一页
+ G：移动到最后一行
+ g：移动到第一行
+ /characters：向前查找指定的字符串
+ n：向前查找下一个出现的字符串，这个字符串是之前所指定查找的

Linux系统中的目录：

+ `/bin`：包含系统启动和运行所必须的二进制程序
+ `/boot`：包含Linux内核，最初的RMA磁盘映像，和启动加载程序
    - `/boot/grub/grub/conf` or `menu.lst`：用来配置启动加载程序
    - `/boot/vmlinuz`：Linux内核
+ `/dev`：设备节点
+ `/etc`：包含系统层面的配置文件，也包含一系列的shell脚本。这个目录中的任何文件应该是可读的文本文件。
    - `/etc/crontab`：定义自动运行的任务
    - `/etc/fstab`：包含存储设备的列表，以及与他们相关的挂载点
    - `/etc/passwd`：包含用户账号列表
+ `/home`：在其下系统给每个用户分配一个目录
+ `/lib`：包含核心系统程序所需的库文件
+ `/lost-found`
+ `/media`：包含可移除媒体设备的挂载点
+ `/mnt`：在早些的Linux系统中包含可移除设备的挂载点
+ `/opt`：可用来安装“可选的”软件
+ `/proc`：一个由内核维护的虚拟文件系统，它所包含的文件是内核的窥视孔。这些文件是可读的，它们会告诉你内核是怎样监管计算机的。
+ `/root`：root账号的主目录
+ `/sbin`：包含“系统”二进制文件，它们是完成重大系统任务的程序，通常为超级用户保留
+ `/tmp`：用来存储各种程序创建的临时文件的地方。一些配置，导致系统每次重启时，都会清空这个目录
+ `/usr`：可能是最大的一个目录。包含普通用户所需要的所有程序和文件
+ `/usr/bin`：系统安装的可执行程序
+ `/usr/lib`：包含由`/usr/bin`目录中的程序所用的共享库
+ `/usr/local`：非系统发行版自带，却打算让系统使用的程序的安装目录
+ `/usr/sbin`：包含许多系统管理程序
+ `/usr/share`：包含许多由`/usr/bin`目录中的程序使用的共享数据，包括默认的配置文件，图标等
+ `/usr/share/doc`：文档
+ `/var`：除了`/tmp`和`home`目录之外，相对来说，其他的目录是静态的。`/var`目录是可能需要改动的文件存储的地方
+ `/var/log`：日志文件。其中最重要的一个文件是`/var/log/messages`。

通配符：

+ `[characters]`：匹配任意一个属于字符集中的字符
+ `[!characters]`：匹配任意一个不是字符集中的字符
+ `[[:class:]]`：匹配任意一个属于指定字符类中的字符
    - `[:alnum:]`：匹配任意一个字母或数字
    - `[:alpha:]`：匹配任意一个字母
    - `[:digit:]`：匹配任意一个数字
    - `[:lower:]`：匹配任意一个小写字母
    - `[:upper:]`：匹配任意一个大写字母

cp：`cp item1 item2`，或者 `cp item... directory` （三个圆点在一个参数后面，表示那个参数可以重复）

ln：创建硬链接，`ln file link`。创建符号链接：`ln -s item link`

ls命令加上`-i`选项显示文件索引节点的信息，可以用来判断几个文件是否是同一文件的硬链接。`ls -l`显示的那个数字是该文件的硬链接数目。硬链接有两个缺点：不能跨越物理设备；不能关联目录，只能是文件。符号链接是文件的特殊类型，它包含一个指向目标文件或目录的文本指针。

命令可以是以下四种形式之一：

- 可执行程序，或者由shell，python等写成的脚本程序
- 内建于shell自身的命令
- shell函数，小规模的shell脚本，混合到环境变量中
- 命令别名

`type <command>`可以用来显示命令的类别。

`which`可以确定所给定的可执行程序的准确位置，不包括内部命令和命令别名。

`apropos`, `whatis`

- `cat`：连接文件
- `sort`：排序文本行
- `uniq`：报道或省略重复行
- `grep`：打印匹配行
- `wc`：打印文件中换行符，字，和字节个数
- `head`：输出文件第一部分
- `tail`：输出文件最后一部分

重定向标准错误到一个文件：`ls -l /bin/usr 2> ls-error.txt`。文件描述符0，1，2分别为标准输入，标准输出，标准错误。

重定向标准输出和错误到同一个文件：`ls -l /bin/usr > ls-output.txt 2>&1`，或者`ls -l /bin/usr &> ls-output.txt`。

处理不需要额输出：`ls -l /bin/usr 2> /dev/null`

`grep`选项：`-i`忽略大小写，`-v`只打印不匹配的行。

`head`打印文件的前十行，`tail`打印文件的后十行。`-n`选项可以调整命令打印的行数。`tail`有一个选项允许你实时的浏览文件：`tail -f /var/log/messages`。

`tee`：`ls /usr/bin | tee ls.txt | grep zip`

`echo [[:upper:]]*`

`echo $((2+2))`：算数表达式展开，`$((expression))`

花括号展开：
```bash
[qma@qxbox ~ ]$ echo Front-{A,B,C}-Back
Front-A-Back Front-B-Back Front-C-Back
[qma@qxbox ~ ]$ echo Number_{1..5}
Number_1 Number_2 Number_3 Number_4 Number_5
[qma@qxbox ~ ]$ echo {Z..A}
Z Y X W V U T S R Q P O N M L K J I H G F E D C B A
[qma@qxbox ~ ]$ echo a{A{1,2},B{3,4}}b
aA1b aA2b aB3b aB4b
```

`echo $USER`, `printenv | less`

命令替换：把一个命令的输出作为另一个展开模式来使用。
```bash
[qma@qxbox ~ ]$ ls -l $(which cp)
-rwxr-xr-x  1 root  wheel  28832 Jan 13  2016 /bin/cp
```
