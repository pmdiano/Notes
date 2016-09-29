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
