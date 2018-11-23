
转自：http://blog.51cto.com/lavasoft/1104993 

OpenSSL 1.0.0生成p12、jks、crt等格式证书的命令个过程

此生成的证书可用于浏览器、java、tomcat、c++等。在此备忘！
 
 生成根证书
1.创建根证私钥
openssl genrsa -out root-key.key 1024
2.创建根证书请求文件
openssl req -new -out root-req.csr -key root-key.key -keyform PEM
3.自签根证书
 openssl x509 -req -in root-req.csr -out root-cert.cer -signkey root-key.key -CAcreateserial -days 3650
4.导出p12格式根证书
openssl pkcs12 -export -clcerts -in root-cert.cer -inkey root-key.key -out root.p12
5.生成root.jks文件
keytool -import -v -trustcacerts -storepass 123456 -alias root -file root-cert.cer -keystore root.jks

生成客户端文件：
1.生成客户端key
openssl genrsa -out client-key.key 1024
2.生成客户端请求文件
openssl req -new -out client-req.csr -key client-key.key
3.生成客户端证书（root证书，rootkey，客户端key，客户端请求文件这4个生成客户端证书）
openssl x509 -req -in client-req.csr -out client-cert.cer -signkey client-key.key -CA root-cert.cer
-CAkey root-key.key -CAcreateserial -days 3650
4.生成客户端p12格式根证书
openssl pkcs12 -export -clcerts -in client-cert.cer -inkey client-key.key -out client.p12
 
5.客户端jks：
 keytool -import -v -trustcacerts -storepass 123456 -alias client -file client-cert.cer -keystore client.jks
 
生成服务端文件：
1.生成服务端key
openssl genrsa -out server-key.key 1024
2.生成服务端请求文件
openssl req -new -out server-req.csr -key server-key.key
3.生成服务端证书（root证书，rootkey，客户端key，客户端请求文件这4个生成客户端证书）
openssl x509 -req -in server-req.csr -out server-cert.cer -signkey server-key.key -CA root-cert.cer
-CAkey root-key.key -CAcreateserial -days 3650
4.生成服务端p12格式根证书
openssl pkcs12 -export -clcerts -in server-cert.cer -inkey server-key.key -out server.p12
服务端JKS
 keytool -import -v -trustcacerts -storepass 123456 -alias server -file server-cert.cer -keystore server.jks

无密码key命令：
openssl rsa -in client-key.key -out client-key.key.unsecure