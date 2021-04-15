```python
https双向认证原理：



一.生成自签名根证书

创建根证书私钥：

openssl genrsa -out root.key 1024
创建根证书请求文件：

openssl req -new -out root.csr -key root.key
 
############注意##############
根证书的Common Name填写root就可以，根证书的这个字段和客户端证书、服务器端证书不能一样
其他字段必须与根证书一致，为了不容易出错，其他所有字段输入.
输入密码也输入.


创建根证书：

openssl x509 -req -in root.csr -out root.crt -signkey root.key -CAcreateserial -days 3650
这里我们就得到效期为10年的根证书root.crt，我们现在已经有3个文件：



 

二.生成自签名客户端证书

生成客户端证书秘钥：

openssl genrsa -out client.key 1024
生成客户端证书请求文件：

openssl req -new -out client.csr -key client.key
 
#############注意##############
客户端证书Common Name必须填写访问的域名，该域名与nginx配置的域名一致，可以设置*.xx.com匹配所有二级域名
其他字段必须与根证书字段信息一致，所以其他字段全部输入.
密码输入.


生客户端证书：

openssl x509 -req -in client.csr -out client.crt -signkey client.key -CA root.crt -CAkey root.key -CAcreateserial -days 3650
客户端证书生成完毕，我们的文件有：



校验客户端证书与根证书是否创建正确：

openssl verify -CAfile root.crt client.crt


生客户端p12格式证书：

openssl pkcs12 -export -clcerts -in client.crt -inkey client.key -out client.p12
 
# 密码输入.
 

三.Nginx配置

ssl_certificate     /opt/ssl_test/root.crt;
ssl_certificate_key /opt/ssl_test/root.key;
ssl_client_certificate /opt/ssl_test/root.crt;
ssl_verify_client on;
ssl_session_timeout 5m;
ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
ssl_prefer_server_ciphers on;
 

```

