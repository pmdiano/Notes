Nginx作为反向代理服务器（Reverse Proxy），需要配置proxy_pass指令，同时打开mod_proxy模块。

需要进行以下配置才能转发用户的IP地址：
```
location / {
    proxy_pass          http://localhost:8001;
    proxy_set_header    X-Real-IP $remote_addr;
}
```