SSH对传输的数据进行了压缩，以加快传输速度。通过SCP来进行文件分发，需要在SSH服务器端，也就是分发文件的目标服务器上，对SSH的服务器端配置选项进行一些修改，通常配置文件为`/etc/ssh/sshd_config`，修改以下内容：
```
UseDNS no
PasswordAuthentication yes
```
一来可以减少SSH服务器进行DNS解析的时间，并且允许我们在Web应用程序中通过密码验证的方式来登录SSH服务器。

SFTP能断点续传，SCP则不能。