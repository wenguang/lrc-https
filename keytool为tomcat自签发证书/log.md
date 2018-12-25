参考：https://tomcat.apache.org/tomcat-8.5-doc/ssl-howto.html 

1、用keytool生成带自签名证书的keystore，指定keystore文件在tomcat的myssl目录下：
```
keytool -genkey -alias tomcat -genalg RSA -keystore ~/Library/apache-tomcat-8.5.31/myssl/keystore.jks
```

2、在tomcat的server.xml中配置：
```
<Connector
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           port="8443" maxThreads="200"
           scheme="https" secure="true" SSLEnabled="true"
           keystoreFile="myssl/keystore.jks" keystorePass="123456"
           clientAuth="false" sslProtocol="TLS"/>
```

重启tomcat后，访问https://localhost:8443就得访问截图的效果了
截图中可看到证书是安全，它也不是根证书，也无法导入到keychain中