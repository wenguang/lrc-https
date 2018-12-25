参考：https://lamjack.github.io/2018/05/17/openssl-localhost-https/

MacOS下 Openssl自己创建根证书、导入keychain

1、创建 root key
openssl genrsa -des3 -out rootCA.key 2048
2、使用生成的密钥来创建新的根SSL证书
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 3650 -out rootCA.pem

3、导入根证书到keychain、设置信任见截图