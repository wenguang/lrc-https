参考：https://lamjack.github.io/2018/05/17/openssl-localhost-https/

生成域SSL证书、nginx配置访问

本节用到openssl-root-cer-macos中生成的根证书

1、创建一个v3.ext文件，以创建一个X509 v3证书(v3.ext文件的作用待研究)
2、创建证书密钥与请求
openssl req -new -sha256 -nodes -out server.csr -newkey rsa:2048 -keyout server.key
3、签发域证书, (证书签名请求通过我们之前创建的根SSL证书颁发，创建出一个 *.hello.com 的域名证书。输出是一个名为的证书文件server.crt)
openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 500 -sha256 -extfile v3.ext


Nginx配置后https访问未成功，待续...
nginx.conf的https配置如下（server.crt、server.key已拷贝在nginx目录下）：
```
server {
        listen       80;
        listen       443 ssl;
        server_name  hello.com;

        ssl_certificate      server.crt;
        ssl_certificate_key  server.key;

        ssl_protocols        TLSv1 TLSv1.1 TLSv1.2;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;

        location / {
            root   html;
            index  index.html index.htm;
        }
    }
```

