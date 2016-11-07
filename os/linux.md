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