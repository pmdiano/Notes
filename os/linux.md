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
[qma@qxbox notes (master)]$ ls -l `which cp`
-rwxr-xr-x  1 root  wheel  28832 Jan 13  2016 /bin/cp
[qma@qxbox notes (master)]$ file `which cp`
/bin/cp: Mach-O 64-bit executable x86_64
```

双引号中，参数展开，算数表达式展开，和命令替换仍然有效。如果需要禁止所有的展开，我们使用单引号。若我们只想应用单个字符，可以在字符前面加上一个反斜杠，在这个上下文中叫做转义字符。在单引号中，反斜杠失去它的特殊含义，被看作普通字符。

`echo`命令加上`-e`选项，能够解释转义序列。你可以把转义序列放在`$''`里面：
```bash
sleep 10; echo -e "Time's up\a"
sleep 10; echo "Time's up" $'\a'
```

历史命令展开：`!88`

`Ctr-r`：递增反向搜索历史命令

一些与用户和权限有关的命令：

- `id`：显示用户身份号
- `chmod`：更改文件模式
- `umask`：设置默认的文件权限
- `su`：以另一个用户的身份来运行shell
- `sudo`：以另一个用户的身份来执行命令
- `chown`：更改文件所有者
- `chgrp`：更改文件组所有权
- `passwd`：更改用户密码

查看`sudo`命令可以授予哪些权限：`sudo -l`

关于进程的命令：

- `ps`：报告当前进程快照
- `top`：显示任务
- `jobs`：列出活跃的任务
- `bg`：把一个任务放到后台执行
- `fg`：把一个任务放到前台执行
- `kill`：给一个进程发送信号
- `killall`：杀死指定名字的进程
- `shutdown`：关机或重启系统

当系统启动的时候，内核先把一些它自己的程序初始化为进程，然后运行一个叫做`init`的进程。`init`，依次再运行一系列称为`init`脚本的shell脚本（位于`/etc`），它们可以启动所有的系统服务。`init`进程的PID总是1。

`ps x`：展示所有进程。`ps aux`会显示更多信息。

`ps`只能提供命令执行时刻的机器状态快照。为了看到更多动态的信息，我们可用`top`命令。

启动一个程序，让它立刻在后台运行，我们在程序命令之后加上`&`字符：`xlogo &`。`fg`移动到前台，`Ctrl-z`可以停止一个前台进程，`bg`命令把程序移到后台。
```bash
qirong@qirong-VirtualBox:~$ xlogo &
[1] 3432
qirong@qirong-VirtualBox:~$ jobs
[1]+  Running                 xlogo &
qirong@qirong-VirtualBox:~$ fg %1
xlogo
^Z
[1]+  Stopped                 xlogo
qirong@qirong-VirtualBox:~$ bg %1
[1]+ xlogo &
qirong@qirong-VirtualBox:~$ kill 3432
qirong@qirong-VirtualBox:~$
```

注意`kill`命令只是给程序发送信号。`Ctrl-c`发送一个INT（中断）信号，`Ctrl-z`发送一个TSTP（终端停止）的信号。完整的信号列表：`kill -l`。`killall`给多个进程发送信号：`killall [-u user] [-signal] name...`

其他与进程监测相关的命令：

- `pstree`：输出一个树形结构的进程列表
- `vmstat`：输出一个系统资源使用快照，包括内存、交换分区和磁盘I/O
- `xload`：一个图形界面程序，可以画出系统负载的图形
- `tload`：与`xload`程序相似，但是在终端中画出图形

关于shell环境的一些命令：

- `printenv`：打印部分或者所有的环境变量
- `set`：设置shell选项
- `export`：导出环境变量，让随后执行的程序知道
- `alias`：创建命令别名

shell在环境中存储了两种不同的数据：环境变量和shell变量。`set`命令可以显示shell和环境变量两者，而`printenv`只是显示环境变量。如果shell环境中的一个成员即不可用`set`命令也不可用`printenv`命令显示，则这个变量是别名，可以用`alias`命令来查看它们。

包管理工具：

| 发行版 | 底层工具 | 上层工具 |  
| ---- | ----------------- | -------------------|  
| Debian-Style | dpkg | apt-get, aptitude |  
| Fedora, Red Hat, CentOS | rpm | yum |  

关于存储媒介的命令：

- `mount`：挂载一个文件系统
- `unmount`：卸载一个文件系统
- `fsck`：检查和修复一个文件系统：`sudo fsck /dev/sdb1`
- `fdisk`：分区表控制器
- `mkfs`：创建文件系统：`sudo mkfs -t ext3 /dev/sdb1`; `sudo mkfs -t vfat /dev/sdb1`
- `fdformat`：格式化一张软盘
- `dd`：把面向块的数据直接写入设备，也可创建CD或DVD映像文件，但不能用于音频CD（用`cdrdao`命令）
- `df`：report file system disk space usage
- `genisoimage (mkisofs)`：创建一个ISO 9660 的映像文件：`genisoimage -o cd-rom.iso -R -J ~/cd-rom-files
- `md5sum`：计算MD5检验码

有一个叫做`/etc/fstab`的文件可以列出系统启动时要挂载的设备。

挂载ISO：
```bash
mkdir /mnt/iso_image
mount -t iso9660 -o loop image.iso /mnt/iso_image
```

`wodim`命令可以清空CD-ROM：`wodim dev=/dev/cdrw blank=fast`；写入一个映像文件：`wodim dev=/dev/cdrw image.iso`

关于网络系统的一些命令：

- `ping`：发动ICMP ECHO\_REQUEST软件包到网络主机
- `traceroute`：打印到一台网络主机路由数据包
- `netstat`：打印网络连接，路由表，接口统计数据，伪装连接，和多路广播成员
- `ftp`：因特网文件传输程序
- `wget`：非交互式网络下载器
- `ssh`：OpenSSH SSH客户端

查找文件的命令：

- `locate`：通过名字来查找文件，`updatedb`命令来更新数据库
- `find`：在目录层次结构中搜索文件
- `xargs`：从标准输入生成和执行命令行
- `stat`：显示文件或文件系统状态

`locate`程序只能依据文件名来查找文件，而`find`程序能基于各种各样的属性，搜索一个给定目录（以及它的子目录），来查找文件。`find ~`将输出主目录下的所有文件（包括目录）。只搜索目录：`find ~ -type d | wc -l`。只搜索普通文件：`find ~ -type f | wc -l`。`b`为块设备文件，`c`为字符设备文件，`l`为符号链接。所有文件名匹配通配符模式`"*.jpg"`和文件大小大于1M的文件：`find ~ -type f -name "\*.jpg" -size +1M | wc -l`。`find`也可以使用逻辑操作符来创建更复杂的逻辑关系：`find ~ \(-type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)`。

`find`预定义的操作：

- `-delete`
- `-ls`
- `-print`
- `-quit`

如：`find ~ -type f -name '*.BAK' -delete`，`find ~ -type f -and -name '*.BAK' -and -print`。除了预定义的行为，也可以使用随意的命令：`-exec command {};`。如：`find . -exec ls -l '{}' ';'`，`find . -ok ls -l '{}' ';'`。当`-exec`行为被使用的时候，若每次找到一个匹配的文件，它会启动一个新的指定命令的实例。我们可能想要把所有的搜索结果结合起来，再运行一个命令的实例。有两种方法可以这样做，传统方法是使用外部命令`xargs`，第二种方法是使用`find`命令自己的一个新功能。通过把末尾的分号改为加号，就激活了`find`命令的一个功能，把搜索结果结合为一个参数列表，然后执行一次所期望的命令，如：` find . -type f -exec ls -l '{}' +`。`xargs`命令从标准输入接受输入，并把输入转换为一个特定命令的参数列表，如：`find . -type f | xargs ls -l`。对于可能存在的包含空格的文件名，可以这样：`find . -type f -print0 | xargs --null ls -l`。

一些命令：
```bash
mkdir -p playground/dir-{00{1..9},0{10..99},100}
touch playground/dir-{00{1..9},0{10..99},100}/file-{A..Z}
touch playgournd/timestamp
find playgound -type f -name 'file-B' -exec touch '{}' ';'
find playground -type f -newer playground/timestamp
find playground \( -type f -not -perm 0600 \) -or \( -type d -not -perm 0700 \)
find playground \( -type f -not -perm 0600 -exec chmod 0600 '{}' ';' \) -or \( -type d -not -perm 0711 -exec chmod 0700 '{}' ';' \)
```

`find`命令的其他一些选项：

- `-depth`：先处理目录中的文件，再处理目录本身。当指定`-delete`行为时，会自动应用这个选项。
- `-maxdepth levels`：当执行测试条件和行为的时候，设置find程序陷入目录树的最大级别数
- `-mindepth levels`：设置find程序陷入目录树的最小级别数
- `-mount`：不要搜索挂载到其他文件系统上的目录
- `-noleaf`：不要基于搜索类似于Unix的文件系统做出的假设，来优化它的搜索。
