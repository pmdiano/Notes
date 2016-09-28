Nginx作为反向代理服务器（Reverse Proxy），需要配置proxy_pass指令，同时打开mod_proxy模块。

需要进行以下配置才能转发用户的IP地址：
```
location / {
    proxy_pass          http://localhost:8001;
    proxy_set_header    X-Real-IP $remote_addr;
}
```

Nginx作为反向代理服务器，也就是负载均衡调度器，并为它配置两个后端服务器（仅关键部分）:
```
upstream backend {
    server 10.0.1.200:80;
    server 10.0.1.201:80;
}
```
改变权重分配：
```
upstream backend {
    server 10.0.1.200:80 weight=2;
    server 10.0.1.201:80 weight=1;
}
```
HAProxy也是一款主流的反向代理服务器。