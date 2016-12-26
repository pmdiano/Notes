## 服务器

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

## Linux系统中的目录
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

## 一般命令
- `ls`命令加上`-i`选项显示文件索引节点的信息，可以用来判断几个文件是否是同一文件的硬链接。`ls -l`显示的那个数字是该文件的硬链接数目。硬链接有两个缺点：不能跨越物理设备；不能关联目录，只能是文件。符号链接是文件的特殊类型，它包含一个指向目标文件或目录的文本指针。ls的选项：-F (classify), -t (按照修改时间排序)，-d (directory，列出目录的详细信息而不是目录中的内容)，-S（按照文件大小排序）
- `df`: 查看磁盘剩余空间的数量
- `free`: 显示空闲内存的数量
- `file`：打印文件类型
- `cp`：`cp item1 item2`，或者 `cp item... directory` （三个圆点在一个参数后面，表示那个参数可以重复）
- `ln`：创建硬链接，`ln file link`。创建符号链接：`ln -s item link`
- `less`：打印文件内容：
    + `e`：向前移动一行
    + `y`：向后移动一行
    + `d`：向前移动半页
    + `u`：向后移动半页
    + `f`：向前移动一页
    + `b`：向后移动一页
    + `G`：移动到最后一行
    + `g`：移动到第一行
    + `/characters`：向前查找指定的字符串
    + `n`：向前查找下一个出现的字符串，这个字符串是之前所指定查找的

命令可以是以下四种形式之一：

- 可执行程序，或者由shell，python等写成的脚本程序
- 内建于shell自身的命令
- shell函数，小规模的shell脚本，混合到环境变量中
- 命令别名

`type <command>`可以用来显示命令的类别。`type -a python`

`which`可以确定所给定的可执行程序的准确位置，不包括内部命令和命令别名。

`apropos`, `whatis`

- `cat`：连接文件
- `sort`：排序文本行
- `uniq`：报道或省略重复行
- `grep`：打印匹配行
- `wc`：打印文件中换行符，字，和字节个数
- `head`：输出文件第一部分
- `tail`：输出文件最后一部分

## 重定向
重定向标准错误到一个文件：`ls -l /bin/usr 2> ls-error.txt`。文件描述符0，1，2分别为标准输入，标准输出，标准错误。

重定向标准输出和错误到同一个文件：`ls -l /bin/usr > ls-output.txt 2>&1`，或者`ls -l /bin/usr &> ls-output.txt`。

处理不需要额输出：`ls -l /bin/usr 2> /dev/null`

`grep`选项：`-i`忽略大小写，`-v`只打印不匹配的行。

`head`打印文件的前十行，`tail`打印文件的后十行。`-n`选项可以调整命令打印的行数。`tail`有一个选项允许你实时的浏览文件：`tail -f /var/log/messages`。

`tee`：`ls /usr/bin | tee ls.txt | grep zip`

`echo [[:upper:]]*`

`echo $((2+2))`：算数表达式展开，`$((expression))`

## 花括号展开
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

## 用户和权限
- `id`：显示用户身份号
- `chmod`：更改文件模式
- `umask`：设置默认的文件权限
- `su`：以另一个用户的身份来运行shell
- `sudo`：以另一个用户的身份来执行命令
- `chown`：更改文件所有者
- `chgrp`：更改文件组所有权
- `passwd`：更改用户密码

查看`sudo`命令可以授予哪些权限：`sudo -l`

## 进程
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

## 存储媒介
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

## 关于网络系统的一些命令
- `ping`：发动ICMP ECHO\_REQUEST软件包到网络主机
- `traceroute`：打印到一台网络主机路由数据包
- `netstat`：打印网络连接，路由表，接口统计数据，伪装连接，和多路广播成员
- `ftp`：因特网文件传输程序
- `wget`：非交互式网络下载器
- `ssh`：OpenSSH SSH客户端

## 查找文件
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

## 压缩，归档和同步程序
- `gzip`：压缩文件，使用了Lempel-Ziv算法（L777)。对应的解压缩命令为`gunzip`。
- `bzip2`：块排序文件压缩器，使用了Burrows-Wheeler块排序算法以及Huffman编码
- `tar`：归档（tape archive）
- `zip`：打包和压缩文件
- `rsync`：同步远端文件和目录，类似于`rcp`(remote file copy)

```bash
gzip foo.txt                # 将foot.txt压缩为foo.txt.gz，foo.txt消失
gzip -tv foo.txt.gz         # -t:测试压缩文件的完整性 -v:显示压缩过程中的信息
gunzip foo.txt              # 假定了扩展名为gz，没必要指定
gunzip -c foo.txt | less    # 只浏览压缩文本文件的内容
```
`zcat`等同于`gunzip -c`，`zless`等同于带`less`管道的`zcat`。

`tar`的模式：

- `c`：为文件和/或目录列表创建归档文件
- `x`：抽取归档文件
- `r`：追加具体的路径到归档文件的末尾
- `t`：列出归档文件的内容

```bash
tar cf playground.tar playground    # 创建playround目录的tar包
tar tf playground.tar               # 列出tar包的内容
tar tvf playground.tar              # 列出tar包的详细内容

mkdir foo
cd foo
tar xf ../playground.tar            # 解压缩tar包
```
`tar xf`也可以通过给命令添加末尾的路径名就只会恢复指定的文件，通过`--wildcards`选项可以支持通配符。`tar`经常和`find`命令合用：
```bash
find playground -name 'file-A' -exec tar rf playground.tar '{}' '+'
```
`tar`也可以直接压缩：
```bash
find playground -name 'file-A' | tar czf playground.tgz -T -    # '-' 表示标准输入输出
find playground -name 'file-A' | tar cjf playground.tbz -T -
```
与`ssh`合用：
```bash
mkdir remote-stuff
cd remote-stuff
ssh remote-sys 'tar cf - Documents' | tar xf -
```

`zip`最基本的使用：`zip options zipfile file...`。如：`zip -r playground.zip playground`。使用`unzip`来抽取一个zip文件的内容：`cd foo; unzip ../playground.zip`。`unzip -l file`则只列出文件包中的内容而没有抽取文件。`zip`的管道：
```bash
find playground -name "file-A" | zip -@ file-A.zip
ls -l /etc/ | zip ls-etc.zip -
unzip -p ls-etc.zip | less
```
`rsync`的使用：
```bash
rsync -av playground foo    # 同步playground到foo
sudo rsync -av --delete /etc /home /usr/local /media/BigDisk/backup
                            # --delete选项删除可能在备份设备中已经存在但却不再
                            # 存在于源设备中的文件
sudo rsync -av --delete --rsh=ssh /etc /usr/local remote-sys:/backup
```
`rsync`用来在网络间同步文件的第二种方式是通过使用`rsync`服务器。`rsync`可以被配置为一个守护进程，监听即将到来的同步请求。
```bash
mkdir fedora-devel
rsync -av --delete rsync://rsync.gtlib.gatech.edu/fedora-linux-core/development/i386/os fedora-devel
```

## 正则表达式
`grep`即为`global regular expression print`。`grep`的用法：`grep [options] regex [file...]`。常用的`grep`选项列表：

- `-i`：忽略大小写
- `-v`：不匹配
- `-c`：打印匹配的数量，而不是文本行本身
- `-l`：打印包含匹配项的文件名，而不是文本行本身
- `-L`：类似于`-l`选项，但是只是打印不包含匹配项的文件名
- `-n`：打印行号
- `-h`：应用于多文件搜索，不输出文件名

正则表达式元字符：`^ $ . [ ] { } - ? * + ( ) | \`。其它所有字符都被认为是原义字符。

- `.`：匹配任意字符
- `^`：锚（定位点），匹配文本行的开头
- `$`：锚（定位点），匹配文本行的末尾

查字典：
```bash
± grep -i '^..j.r$' /usr/share/dict/words
Gujar
Kajar
Major
major
```
中括号表达式能够从一个指定的字符集合中匹配一个单个的字符：`grep -h '[bg]zip' dirlist*.txt`。元字符被放置到中括号后会失去了它们的特殊含义，然而在两种情况下会在中括号表达式中使用元字符。第一个是插入字符`^`，用来表示否定，且只有插入字符是中括号表达式中的第一个字符的时候才会唤醒否定功能，否则仍是普通字符；第二个是连字符字符`-`，用来表示一个字符区域，如`grep -h '^[A-Z]' dirlist*.txt`，`grep -h '^[A-Za-z0-9]' dirlist*.txt`。

`ls /usr/sbin/[[:upper:]]*`

POSIX把正则表达式的实现分成了两类：基本正则表达式（BRE）和扩展的正则表达式（ERE）。BRE可以辨别以下元字符：`^ $ . [ ] *`，ERE添加了以下元字符：`( ) { } ? + |`。`egrep`支持ERE，`grep -E`也一样。
```bash
± echo "AAA" | grep 'AAA|BBB'

± echo "AAA" | grep -E 'AAA|BBB'        # ERE的alteration，用`|`来表示
AAA

± echo "BBB" | grep -E 'AAA|BBB'
BBB

± grep -Eh '^(bz|gz|zip)' dirlist*.txt  # ()来分离alteration
bzcat
bzcmp
...
```

限定符:

- `?`：匹配一个元素零次或一次
- `*`：匹配任意多次
- `+`：匹配至少一次
- `{}`：匹配一个元素特定的次数
    + `{n}`：匹配前面的元素，确切出现了n次
    + `{n,m}`：匹配前面的元素，至少出现了n次，但不多于m次
    + `{n,}`：匹配前面的元素，出现了n次或多于n次
    + `{,m}`：匹配前面的元素，出现的次数不多于m次

如：
```bash
echo "(555) 123-4567" | grep -E '^\(?[0-9][0-9][0-9]\)? [0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]$'
    # 电话号码的括号是可选的
echo "555 123-4567" | grep -E '^\(?[0-9]{3}\)? [0-9]{3}-[0-9]{4}$'
    # 跟上面的一样
echo "This works." | grep -E '[[:upper:]][[:upper:][:lower:] ]*'
    # 匹配一个句子，以大写字母开头，后面是任意的字母和空格。
echo "This that" | grep -E '^([[:alpha:]]+ ?)+$'
    # 匹配那些由一个或多个字母字符组构成的文本行，字母字符之间由单个空格分开
    # “This that"匹配，”a b c"匹配，"a b 9"不匹配，"abc   d"不匹配
```

一些例子：
```bash
# 生成并验证一个电话簿
for i in {1..10}; do echo "(${RANDOM:0:3}) ${RANDOM:0:3}-${RANDOM:0:4}" >> phonelist.txt; done
grep -Ev '^\([0-9]{3}\) \d{3}-\d{4}'

# find命令要求路径名精确地匹配这个正则表达式。
# 查找丑陋的路径名，其包含的任意字符都不是以下字符集中的一员（？）
find . -regex '.*[^-\_./0-9a-zA-Z].*'

# locate程序支持基本的（--regexp）和扩展的（--regex）正则表达式
locate --regex 'bin/(bz|gz|zip)'
```

## 文本处理
### `sort`
`sort`的选项：

- `-b`: `--ignore-leading-blankds`
- `-f`: `--ignore-case`
- `-n`: `--numeric-sort`
- `-r`: `--reverse`
- `-k`: `--key=field1,[field2]`
- `-m`: `--merge`
- `-o`: `--output=file`
- `-t`: `--field-separate=char`，默认情况下，域由空格或制表符分隔。

```bash
du -s /usr/share/* | head
du -s /usr/share/* | sort -nr | head

ls -l /usr/bin | head
ls -l /usr/bin | sort -nr -k 5 | head

sort -k 1,1 -k 2n distros.txt
sort -k 3.7nbr -k 3.1nbr -k 3.4nbr distros.txt #偏移量，根据日期排序，11/25/2008

sort -t ':' -k 7 /etc/passwd | head
```

### `uniq`
`uniq`的选项：

- `-c`：每行开头显示重复的次数
- `-d`：只输出重复行
- `-f n`：忽略每行开头的n个字段，字段之间由空格分隔
- `-i`：在比较文本行的时候忽略大小写
- `-s n`：跳过（忽略）每行开头的n个字符
- `-u`：只是输出独有的文本行，这是默认的

### `cut`
从文本行中抽取文本，选项：

- `-c char_list`
- `-f field_list`
- `-d delim_char`，默认情况下有单个tab字符分隔开
- `--complement`

```bash
cut -f 3 distro.txt | cut -c 7-10
```
`expand`命令可以将tab展开为空格，`unexpand`可以用tab代替空格。

### `diff`，`patch`
`diff`有上下文模式（`-c`），统一模式（`-u`）。
```bash
diff -Naur old_file new_file > diff_file
patch < diff_file
```

### `tr`：translate character
```bash
echo "lowercase letters" | tr a-z A-Z
echo "lowercase letters" | tr [:lower:] A
tr -d '\r' <dos_file> <unix_file>
echo "secret text" | tr a-zA-Z n-za-mN-ZA-M     # ROT13文本编码
echo "aaabbbccc" | tr -s ab                     # abccc
echo "abcabcabc" | tr -s ab                     # abcabcabc
```

### `sed`：stream editor
```bash
echo "front" | sed '1s/front/back/' # s是替换的意思
sed -n '1,5p' distros.txt           # p是打印，-n选项是不自动打印选项
sed -n '/SUSE/p' distros.txt        # 正则表达式，打印所有包含SUSE的行
sed -n '/SUSE/!p' distros.txt       # 打印所有不包含SUSE的行
sed 's/\([0-9]\{2\}\)\/\([0-9]\{2\}\)\/\([0-9]\{4\}\)$/\3-\1-\2/' distros.txt
sed -f distros.sed distros.txt
echo "aaabbbccc" | sed 's/b/B/'     # aaaBbbccc
echo "aaabbbccc" | sed 's/b/B/g'    # aaaBBBccc
```
`sed`地址：

| 地址          | 说明              |
| ------------- | ----------------- |
| `n`           | 行号，n是一个正整数 |
| `$`           | 最后一行 |
| `/regexp/`    | 所有匹配一个POSIX基本正则表达式的文本行 |
| `addr1,addr2` | 从addr1到addr2范围内的文本行，地址可能是上述任意单独的地址形式 |
| `first~step`  | 初始+步长 |
| `addr1,+n`    | 匹配地址addr1和随后的n个文本行 |
| `addr!`       | 匹配所有的文本行，除了addr之外 |

`sed`命令：

| 地址          | 说明              |
| ------------- | ----------------- |
| `=`           | 输出当前的行号 |
| `a`           | 在当前行之后追加文本 |
| `d`           | 删除当前行 |
| `i`           | 在当前行之前插入文本 |
| `p`           | 打印当前行 |
| `q`           | 退出sed。如果不指定`-n`选项，输出当前行 |
| `Q`           | 退出sed |
| `s/regexp/replacement/` | 替换 |
| `y/set1/set2` | 执行字符转写操作，要求两个字符集合具有相同的长度 |

### `aspell`：一款交互式的拼写检查器
`aspell check textfile`

### 别的命令
`split`：把文件分割成碎片；`csplit`：基于上下文把文件分隔成碎片；`sdiff`：并排合并文件差异

## 格式化输出
- `nl`：添加行号
- `fold`：限制文件列宽
- `fmt`：一个简单的文本格式转换器
- `pr`：让文本为打印做好准备
- `lpr`, `lp`：打印
- `lpstat`：显示打印系统状态
- `lpq`：显示打印机队列状态
- `lprm`和`cancel`：取消打印任务（打印的程序都在CUPS包）
- `printf`：格式化数据并打印出来
- `groff`：一个文件格式系统

### `nl`
TL,DR: sed脚本

### `a2ps`
```bash
ls /usr/bin | pr -3 -t | a2ps -o ~/Desktop/ls.ps -L 66
```

## Shell脚本
脚本的第一行：`#!/bin/bash`，`#!`字符序列叫做shebang，告诉操作系统执行此脚本所用的解释器的名字。给变量赋值：
```bash
a=z
b="a string"
c="a string and $b"
d=$(ls -l foo.txt)
e=$((5 * 7))
f="\t\ta string \n"

# 花括号展开
filename="myfile"
touch $filename
mv $filename $filename1     # 失败
mv $filename ${filename}1   # 成功

# here document：
#!/bin/bash
# Script to retrieve a file via FTP
# for <<, should be no space for following lines
# for <<-, must it be tab?
FTP_SERVER=ftp.nl.debian.org
FTP_PATH=/debian/dists/lenny/main/installer-i386/current/images/cdrom
REMOTE_FILE=debian-cd_info.tar.gz
ftp -n <<- _EOF_
    open $FTP_SERVER
    user anonymous me@linuxbox
    cd $FTP_PATH
    hash
    get $REMOTE_FILE
    bye
_EOF_
ls -l $REMOTE_FILE
```

### Shell函数
Shell函数有两种语法形式，它们是等价的：
```bash
function name {
    commands
    return
}

name () {
    commands
    return
}
```

### 局部变量
```bash
#!/bin/bash
foo=0 # global variable
funct_1 () {
    local foo
    foo=1
    echo "funct_1: foo = $foo"
}
funct_2 () {
    local foo
    foo=2
    echo "funct_2: foo = $foo"
}
echo "global: foo = $foo"
funct_1
echo "global: foo = $foo"
funct_2
echo "global: foo = $foo"
```

### `if`语句和测试条件
```bash
x=5
if [ $x = 5 ]; then
    echo "x equals 5."
else
    echo "x does not equal 5."
fi
```
退出状态：`$?`。

文件表达式：

| 表达式             | 如果为真           |
| ----------------- | ----------------- |
| `file1 -ef file2` | file1 和 file2 拥有相同的索引号（通过硬链接两个文件名指向相同的文件）。|
| `file1 -nt file2` | file1新于 file2。 |
| `file1 -ot file2` | file1早于 file2。 |
| `-b file`         | file 存在并且是一个块（设备）文件。|
| `-c file`         | file 存在并且是一个字符（设备）文件。 |
| `-d file`         | file 存在并且是一个目录。 |
| `-e file`         | file 存在。 |
| `-f file`         | file 存在并且是一个普通文件。|
| `-g file`         | file 存在并且设置了组 ID。|
| `-G file`         | file 存在并且由有效组 ID 拥有。|
| `-k file`         | file 存在并且设置了它的“sticky bit”。|
| `-L file`         | file 存在并且是一个符号链接。|
| `-O file`         | file 存在并且由有效用户 ID 拥有。|
| `-p file`         | file 存在并且是一个命名管道。|
| `-r file`         | file 存在并且可读（有效用户有可读权限。|
| `-s file`         | file 存在且其长度大于零。|
| `-S file`         | file 存在且是一个网络 socket。|
| `-t fd`           | fd是一个定向到终端／从终端定向的文件描述符。这可以被用来决定是否重定向了标准输入／输出错误。|
| `-u file`         | file 存在并且设置了 setuid 位。|
| `-w file`         | file 存在并且可写（有效用户拥有可写权限）。|
| `-x file`         | file 存在并且可执行（有效用户有执行／搜索权限）。|

字符串表达式：

| 表达式             | 如果为真           |
| ----------------- | ----------------- |
| `string` | string不为null |
| `-n string` | 字符串string的长度大于零 |
| `-z string` | 字符串string的长度等于零 |
| `string1 = string2` | string1和string2相同 |
| `string1 == string2` | 同上 |
| `string1 != string2` | string1和string2不相同 |
| `string1 '>' string2` | string1排列在string2之后 |
| `string1 '<' string2` | string1排列在string2之前 |

整型表达式：

| 表达式             | 如果为真           |
| ----------------- | ----------------- |
| `integer1 -eq integer2` | equal |
| `integer1 -ne integer2` | not equal |
| `integer1 -le integer2` | less than or equal |
| `integer1 -lt integer2` | less than |
| `integer1 -ge integer2` | greater than or equal |
| `integer1 -gt integer2` | greater than |

`[[ expression ]]`相似于test命令，但是增加了一个重要的新的字符串表达式：`string1 =~ regex`。`[[ ]]`添加的另一个功能是`==`操作符支持类型匹配。
```bash
#!/bin/bash
# test-integer2: evaluate the value of an integer.
INT=-5
if [[ "$INT" =~ ^-?[0-9]+$ ]]; then
    if [ $INT -eq 0 ]; then
        echo "INT is zero."
    else
        if [ $INT -lt 0 ]; then
            echo "INT is negative."
        else
            echo "INT is positive."
        fi
        if [ $((INT % 2)) -eq 0 ]; then
            echo "INT is even."
        else
            echo "INT is odd."
        fi
    fi
else
    echo "INT is not an integer." >&2
    exit 1
fi
```
`(( ))`被用来执行整数算数计算，它能够通过名字识别出变量，而不需要执行展开操作。判断奇偶：
```bash
if [ $((INT % 2)) -eq 0 ]; then
    echo "INT is even."
else
    echo "INT is odd."
fi

if (( ((INT % 2)) == 0)); then
    echo "INT is even."
else
    echo "INT is odd."
fi
```

结合表达式：

| 操作符 | 测试 | `[[ ]]`和`(( ))` |
| ------ | ---- | ------- |
| AND    | `-a` | `&&` |
| OR     | `-o` | `||` |
| NOT    | `!`  | `!`  |

注意在测试表达式中（test，即`[ ]`）操作符都被shell看作是命令参数，对于bash有特殊含义的字符，比如`<`，`>`，`(`和`)`，必须引起来或者是转义：
```bash
if [ ! \( $INT -ge $MIN_VAL -a $INT -le $MAX_VAL \) ]; then
    echo "$INT is outside $MIN_VAL to $MAX_VAL."
else
    echo "$INT is in range."
fi
```

test更传统（是POSIX的一部分），然而`[[ ]]`特定于bash。`[[ ]]`更有助于编码。

控制操作符：
```bash
mkdir temp && cd temp
[ -d temp ] || mkdir temp
```

## 读取键盘输入
### `read` - 从标准输入读取数值
`read [-options] [variable...]`
如果没有提供变量名，shell变量`REPLY`会包含数据行。
```bash
#!/bin/bash
# read-secrect: input a secret pass phrase
if read -t 10 -sp "Enter secret pass phrase > " secret_pass; then
    echo -e "\nSecret pass phrase = '$secret_pass'"
else
    echo -e "\nInput timed out" >&2
    exit 1
if
```
调整IFS（内部字符分隔符）的值来控制输入字段的分离：
```bash
#!/bin/bash
# read-ifs: read fields from a file
FILE=/etc/passwd
read -p "Enter a user name > " user_name
file_info=$(grep "^$user_name:" $FILE)
if [ -n "$file_info" ]; then
    IFS=":" read user pw uid gid name home shell <<< "$file_info"
    echo "User = '$user'"
    echo "UID = '$uid'"
    echo "GID = '$gid'"
    echo "Full Name = '$name'"
    echo "Home Dir. = '$home'"
    echo "Shell = '$shell'"
else
    echo "No such user '$user_name'" >&2
    exit 1
fi
```
校验各种输入的示例程序：
```bash
#!/bin/bash
# read-validate: validate input
invalid_input () {
    echo "Invalid input '$REPLY'" >$ 2
    exit 1
}
read -p "Enter a single item > "
# input is empty (invalid)
[[ -z $REPLY ]] && invalid_input
# input is multiple items (invalid)
(( $(echo $REPLY | wc -w) > 1)) && invalid_input
# is input a valid filename?
if [[ $REPLY =~ ^[-[:alnum:]\._]+$ ]]; then
    echo "'$REPLY' is a valid filename."
    if [[ -e $REPLY ]]; then
        echo "And file '$REPLY' exists."
    else
        echo "However, file '$REPLY' does not exist."
    fi
    # is input a floating point number?
    if [[ $REPLY =~ ^-?[[:digit:]]*\.[[:digit:]]+$ ]]; then
        echo "'$REPLY' is a floating point number."
    else
        echo "'$REPLY' is not a floating point number."
    fi
    # is input an integer?
    if [[ $REPLY =~ ^-?[[:digit:]]+$ ]]; then
        echo "'$REPLY' is an integer."
    else
        echo "'$REPLY' is not an integer."
    fi
else
    echo "The string '$REPLY' is not a valid filename."
fi
```

## 循环
```bash
#!/bin/bash
# while-count: display a series of numbers
count=1
while [ $count -le 5 ]; do
    echo $count
    count=$((count + 1))
done
echo "Finished."
```

```bash
#!/bin/bash
# until-count: display a series of numbers
count=1
until [ $count -gt 5 ]; do
    echo $count
    count=$((count + 1))
done
echo "Finished."
```

```bash
#!/bin/bash
# while-read: read lines from a file
while read distro version release; do
    printf "Distro: %s\tVersion: %s\tReleased: %s\n" \
        $distro \
        $version \
        $release
done < distros.txt
```

```bash
#!/bin/bash
# while-read2: read lines from a file
sort -k 1,1 -k 2n distros.txt | while read distro version release; do
    printf "Distro: %s\tVersion: %s\tReleased: %s\n" \
        $distro \
        $version \
        $release
done
```

## 调试
加上`-x`可以打印每一条执行的语句：`#!/bin/bash -x`。加号是追踪输出的默认字符，它包含在`PS4`Shell变量中。使得追踪输出加上行号：`export PS4='$LINENO + '`。`set -x`打开追踪，`set +x`关闭追踪。

## 选择
```bash
#!/bin/bash
read -p "enter word > "
case $REPLY in
    [[:alpha:]])    echo "is a single alphabetic character."
                    ;;
    [ABC][0-9])     echo "is A, B, or C followed by a digit."
                    ;;
    ???)            echo "is three characters long."
                    ;;
    *.txt)          echo "is a word ending in '.txt'"
                    ;;
    *)              echo "is something else."
                    ;;
esac
```
一般语法规则为：
```bash
case word in
    [pattern [| pattern]...) commands ;;]...
esac
```
`;;&`允许`case`语句继续执行下一条测试，而不是简单地终止运行。

## 位置参数
```bash
#!/bin/bash
# post-param: script to view command line parameters
echo "
Number of arguments: $#
\$0 = $0
\$1 = $1
\$2 = $2
\$3 = $3
\$4 = $4
\$5 = $5
\$6 = $6
\$7 = $7
\$8 = $8
\$9 = $9
"
```

```bash
#!/bin/bash
# post-param2: script to display all arguments
count=1
while [[ $# -gt 0 ]]; do
    echo "Argument $count = $1"
    count=$((count + 1))
    shift
done
```

## 处理集体位置参数：$*, $@
```bash
#!/bin/bash
# post-params3: script to demonstrate $* and $@
print_params () {
  echo "\$1 = $1"
  echo "\$2 = $2"
  echo "\$3 = $3"
  echo "\$4 = $4"
}
pass_params () {
  echo -e "\n" '$* :';    print_params $*
  echo -e "\n" '"$*" :';  print_params "$*"
  echo -e "\n" '$@ :';    print_params $@
  echo -e "\n" '"$@" :';  print_params "$@"
}
pass_params "word" "words with spaces"
```
此外，`bash`还包括一个叫做`getopts`的内部命令，此命令可以用来处理行参数。

## for 循环
```bash
for variable [in words]; do
    commands
done
```
```bash
for i in A B C D; do echo $i; done
for i in {A..D}; do echo $i; done
for i in distros*.txt; do echo $i; done
```
`for`命令默认会处理位置参数：
```bash
#!/bin/bash
# longest-word: find longest word in a file
for i; do
  if [[ -r $i ]]; then
    max_word=
    max_len=0
    for j in $(strings $i); do
      len=$(echo $j | wc -c)
      if (( len > max_len )); then
        max_len=$len
        max_word=$j
      fi
    done
    echo "$i: '$max_word' ($max_len characters)"
  fi
done
```
C-style for loop:
```bash
#!/bin/bash
# simple_counter: demo of C style for command
for (( i=0; i<5; i=i+1 )); do
    echo $i;
done
```

## 字符串和数字

### 展开
`${parameter:-word}`：若parameter没有设置或者为空，展开结果是word的值。若parameter不为空，则展开结果是parameter的值。

`${parameter:=word}`：若parameter没有设置或者为空，展开结果是word的值，另外，word的值回赋给parameter。若parameter不为空，展开结果是parameter的值。

`${parameter:?word}`：若parameter没有设置或为空，这种展开导致脚本带有错误退出，并且word的内容会发送到标准错误。若parameter不为空，展开结果是parameter的值。

`${parameter:+word}`：若parameter没有设置或为空，展开结果为空。若parameter不为空，展开结果是word的值（parameter的值不变）。

`${!prefix*}`，`${!prefix@}`展开会返回以prefix开头的已有环境变量名（在Mac下并不work）

`${#parameter}`：展开成由parameter所包含的字符串长度。如果parameter是@或*的话，则展开成位置参数的个数。

`${parameter:offset}`，`${parameter:offset:length}`：展开成parameter的substring。如果offset是负数，则从字符串尾部开始计算，但负号前面要有空格，以免与上面第一条混淆。如果parameter是@，展开结果是length个位置参数，从第offset个位置参数开始。

`${parameter#pattern}`，`${parameter##pattern}`：这些展开从parameter所包含的字符串中清除一部分文字，要匹配定义的pattern，pattern是通配符模式。#清除最短的匹配结果，而##清除最长的匹配结果。
```bash
bin[master] % foo=file.txt.zip
bin[master] % echo ${foo#*.}
txt.zip
bin[master] % echo ${foo##*.}
zip
```

`${parameter%pattern}`，`${parameter%%pattern}`：和上一条一样，但它们清除的文本从parameter末尾开始。

`${parameter/pattern/string}`，`${parameter//pattern/string}`，`${parameter/#pattern/string}`，`${parameter/%pattern/string}`：对parameter的内容执行查找和替换操作。`//`会替换所有的匹配项，`/#`要求匹配项出现在字符串的开头，而`/%`要求匹配项出现在字符串的末尾。

### 大小写转换
```bash
#!/bin/bash
# ul-declare: demonstrate case conversion via declare
# does not work well on Mac (needs Bash 4+)
declare -u upper
declare -l lower
if [[ $1 ]]; then
    upper="$1"
    lower="$1"
    echo $upper
    echo $lower
fi
```
大小写转换：（need Bash 4+）：

- `${parameter,,}`：把parameter的值全部展开为小写字母
- `${parameter,}`：仅把parameter的第一个字符展开为小写字母
- `${parameter^^}`：把parameter的值全部展开为大写字母
- `${parameter^}`：仅把parameter的第一个字符展开为大写字母

### 算数求值和展开
shell算数只操作整型。算数展开的基本格式：`$((expression))`。

数基：

- `number`：十进制数
- `0number`：八进制数
- `0xnumber`：十六进制数
- `base#number`：base进制数

```bash
#!/bin/bash
# demonstrate bash arithmetics
for ((i = 0; i <= 20; ++i )); do
  if (( (i % 5) == 0 )); then
    printf "<%d> " $i
  else
    printf "%d " $i
  fi
done
printf "\n"
```

```bash
#!/bin/bash
# arith-loop: script to demonstrate arithmetic operators
finished=0
a=0
printf "a\ta**2\ta**3\n"
printf "=\t====\t====\n"
until ((finished)); do
  b=$((a**2))
  c=$((a**3))
  printf "%d\t%d\t%d\n" $a $b $c
  ((a<10?++a:(finished=1)))
done
```
