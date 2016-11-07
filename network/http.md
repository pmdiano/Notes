HTTP重定向：Web服务器通过HTTP响应头信息中的Location标记来返回一个新的URL。

DNS中A记录用来指定域名对应的IP地址。dig命令可以查看站点域名的A记录设置。

简易HTTP服务器：
+ Python: `python –m SimpleHTTPServer 8888`
+ Node.js: `npm install http-server –g`, `http-server .` (默认端口8080，也可以`-p`指定)
