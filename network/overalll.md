在规范情况下，对磁盘文件调用`read()`将阻塞进程，一直到数据被复制到进程用户态内存空间。`write()`则不同，它会在数据被复制到内核缓冲区后立即返回。如果使用`O_SYNC`标志打开文件，则对写文件操作产生影响，它是的`write()`必须等待数据真正写入磁盘后才返回。

同步非阻塞I/O调用不会等待数据的就绪，如果数据不可读或者不可写，它会立即告诉进程。非阻塞I/O一般只针对网络I/O有效，我们只要在socket的选项设置中使用`O_NONBLOCK`即可，这样对于该socket的`send()`或`recv()`便采用非阻塞方式值得注意的是，对于磁盘I/O，非阻塞I/O并不产生效果。

多路I/O就绪通知可以帮助我们快速获得就绪的文件描述符，当得知数据就绪后，就访问数据本身而言，仍然需要选择阻塞或非阻塞的访问方式。

TCP/IP诞生于4.1BSD，select诞生于4.2BSD。4.4BSD Lite很多现在自由版UNIX的基础。BSD在发展中衍生出3个主要的分支：FreeBSD、OpenBSD和NetBSD。

select系统调用诞生于1983年的4.2BSD。输入一个包含多个文件描述符的数组，当返回后，该数组中就绪的文件描述符便会被内核修改标志位，使得进程可以获得这些文件描述符从而进行后续的读写操作。它的一个缺点在于单个进程能够监视的文件描述符数量存在最大限制，Linux上一般为1024。同时其会对所有socket进行一次线性扫描，这也浪费了一定的开销。

poll诞生于1986年的System V Release 3，它和select在本质上没有多大差别，但是poll没有最大文件描述符数量的限制。select和poll将就绪的文件描述符告诉进程后，如果进程没有对其进行I/O操作，那么下次调用select或poll的时候将再次报告这些文件描述符，所以它们一般不会丢失。这种方式称为水平触发（Level Triggered）。

SIGIO提供于Linux 2.4，它通过实时信号来实现select/poll的通知方法，但SIGIO告诉我们哪些FD刚刚变成就绪状态，它只说一遍。这种方式称为边缘触发（Edge Triggered）。

/dev/poll和/dev/epoll见于Solaris和Linux2.4的一个补丁，通过ioctl来等待事件通知。没有提供直接的内核支持。

epoll出现于Linux2.6，公认为Linux2.6下性能最好的多路I/O就绪通知方法。默认水平触发，边缘触发则需要在时间注册时增加EPOLLET选项。

kqueue实现于FreeBSD中，像epoll一样可以设置水平触发或边缘触发，但他的API在很多平台都不支持。

内存映射可以提高磁盘I/O的性能，它无需使用read或者write等系统调用来访问文件，而是通过mmap系统调用来建立内存和磁盘文件的关联，然后像访问内存一样自由地访问文件。

open系统调用中增加参数选项O_DIRECT，用它打开的文件便可以绕过内核缓冲区直接访问，这样便避免了CPU和内存的多余时间开销。MySQL中，对于Innodb存储引擎，在my.cnf配置中，可以在分配Innodb数据空间文件的时候，通过使用raw分区跳过内核缓冲区，实现直接I/O。另外还提供了`innodb_flush_method = O_DIRECT`选项。O_SYNC只对写数据有效，将写入内核缓冲区的数据立即写入磁盘，但是仍然要经过内核缓冲区。

sendfile系统调用可以将磁盘文件的特定部分直接传送到代表客户端的socket描述符，而不是先从磁盘建经过内核缓冲区到达用户内存空间，然后又被送到网卡对应的内核缓冲区，接着再被送入网卡进行发送。Linux2.4引入了khttpd的内核级web服务程序，只处理静态文件的请求。在OpenBSD和NetBSD中没有提供对sendfile的请求。

异步I/O：`aio_read()`, `aio_write()`, `aio_error()`。然而Linux2.6.16中AIO的实现方式是基于LinuxThreads内核级线程库，性能大打折扣。

服务器并发策略：
+ 一个进程处理一个连接，非阻塞I/O，如Apache
+ 一个线程处理一个连接，非阻塞I/O，Apache的worker多路处理模块便采用这种方法。实际测试中，这种方式的表现并不比prefork有太大的优势。人们几乎很少使用它。
+ 一个进程处理多个连接，非阻塞I/O，使用select/poll/epoll
+ 一个线程处理多个连接，异步I/O，目前很少有web服务器支持这种真正意义上的异步I/O。

HTTP响应头中同时含有Expires和Cache-Control时，浏览器会优先考虑Cache-Control。对于没有Cache-Control的情况，浏览器则会服从Expires的指示。

Varnish：反向代理（Caching HTTP reverse proxy）。varnishstat：命令行的状态监控程序。

Cacti：监控平台。

ESI：Edge Side Includes，反向代理服务器可以做到这一点，可以像SSI（Server Side Include）一样在网页中嵌入子页面。不同的是，SSI是在Web服务器端组装内容，而ESI则是在HTTP代理服务器上组装内容，包括反向代理。