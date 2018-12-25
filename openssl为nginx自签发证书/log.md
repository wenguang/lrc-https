命令openssl req 的-subj选项后面应该跟什么参数？
以gencert.sh为例（该脚本来自：https://github.com/michaelliao/itranswarp.js/blob/master/conf/ssl/gencert.sh）
```
SUBJECT="/C=US/ST=Mars/L=iTranswarp/O=iTranswarp/OU=iTranswarp/CN=$DOMAIN"
openssl req -new -subj $SUBJECT -key $DOMAIN.key -out $DOMAIN.csr
```

注意：为了签发的证书能让浏览器验证通过，$DOMAIN的值，也就是CN的值必须与域名一致。（见：https://www.liaoxuefeng.com/article/0014189023237367e8d42829de24b6eaf893ca47df4fb5e000）

为了了解SUBJECT后面参数的含义，下面新生成一个key:
```
openssl genrsa -des3 -out mj.key 1024
```
再进行一次证书请求命令，这次不带-subj选项
```
wenguangdeMacBook-Pro:openssl wenguangpan$ openssl req -new -key mj.key -out mj.csr
Enter pass phrase for mj.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) []:CHN
string is too long, it needs to be less than  2 bytes long
Country Name (2 letter code) []:CN
State or Province Name (full name) []:GD
Locality Name (eg, city) []:GZ
Organization Name (eg, company) []:dvs
Organizational Unit Name (eg, section) []:dvs.learn
Common Name (eg, fully qualified host name) []:mj
Email Address []:mj

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:123456
```
这样就可以看出SUBJECT中的C对应Country Name, ST对应State or Province Name, L对应Locality Name, O对应Organization Name, OU对应Organizational Unit Name, CN对应Common Name, Email Address可以不列出，了解了这些有助于编写脚本.


gencert.sh脚本有一段是把key文件的密码删除掉，作法是：
```
mv $DOMAIN.key $DOMAIN.origin.key
openssl rsa -in $DOMAIN.origin.key -out $DOMAIN.key
```
我试过用删除掉密码的key文件去生成证书请求，是可以不用输入密码，但openssl rsa还是有点让我费解，用openssl rsa -help也没看出有这个删除密码的功能.

总结自签发证书的步骤：
1、openssl genrsa命令 用des3算法生成一个带密码key文件xx.key
2、openssl req命令 用步骤1的key生成一个证书请求文件xx.csr
3、openssl rsa命令 删除key中的密码，得到一个不带密码的key文件
4、openssl x509命令 用证书请求文件和不带密码的key文件签发证书得到xx.crt
我不确定步骤3是否为必要的


把生成的localhost.crt和localhost.key拷贝到/usr/local/nginx/ssl下，且设计了nginx.conf
```
server {
        listen       443 ssl;
        server_name  localhost;

        ssl_certificate      /usr/local/nginx/ssl/localhost.crt;
        ssl_certificate_key  /usr/local/nginx/ssl/localhost.key;

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

这时https://localhost就可以访问了，且把localhost.crt导入了keychain，但浏览器的https还是被红线划过的，它不被根证书认可，这也很正常



