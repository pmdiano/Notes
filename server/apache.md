mod_status： 查看状态

压力测试工具: ab; LoadRunner; Jmeter

prefork模式：父进程预先创建一定量的子进程，所有子进程竞争accept用户请求。

查看某时刻的httpd进程数： `ps afx | grep httpd | wc -l`