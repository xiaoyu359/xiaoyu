## kafka配置ssl通信方式

> kafka版本: 2.11-2.0.0, zookeeper版本：3.4.12


#### ssl key生成脚本
注意：host需要填写对应的主机名，而且需要在hosts文件中添加上127.0.0.1 host
```
#!/bin/bash
#Step 1
keytool -keystore server.keystore.jks -alias mask -validity 365 -genkey
#Step 2
openssl req -new -x509 -keyout ca-key -out ca-cert -days 365
keytool -keystore server.truststore.jks -alias CARoot -import -file ca-cert
keytool -keystore client.truststore.jks -alias CARoot -import -file ca-cert
#Step 3
keytool -keystore server.keystore.jks -alias mask -certreq -file cert-file
openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:xxx
keytool -keystore server.keystore.jks -alias CARoot -import -file ca-cert
keytool -keystore server.keystore.jks -alias mask -import -file cert-signed
```

#### 服务端配置
记得host要与上面同名
```
listeners=PLAINTEXT://host:19092,SSL://host:19093
ssl.client.auth=required
ssl.keystore.location=/opt/kafka_key/server.keystore.jks
ssl.keystore.password=xxx
ssl.key.password=xxx
ssl.truststore.location=/opt/kafka_key/server.truststore.jks
ssl.truststore.password=xxx
ssl.enabled.protocols=TLSv1.2,TLSv1.1,TLSv1
ssl.keystore.type=JKS
ssl.truststore.type=JKS
ssl.endpoint.identification.algorithm=HTTPS
```

#### java kafka Connector consumer端配置
```
bootstrap.servers=host:19093

security.protocol=SSL
ssl.truststore.location=/opt/kafka_key/client.truststore.jks
ssl.truststore.password=xxx
ssl.keystore.location=/opt/kafka_key/server.keystore.jks
ssl.keystore.password=xxx
ssl.key.password=xxx

key.converter.schemas.enable=false
value.converter.schemas.enable=false

offset.storage.file.filename=/tmp/connect.offsets


consumer.bootstrap.servers=host:19093
consumer.security.protocol=SSL
consumer.ssl.truststore.location=/opt/kafka_ke/client.truststore.jks
consumer.ssl.truststore.password=xxx
consumer.ssl.keystore.location=/opt/kafka_ke/server.keystore.jks
consumer.ssl.keystore.password=xxx
consumer.ssl.key.password=xxx

```



#### 参考网址
[Kafka配置SSL加密传输](http://cxy7.com/articles/2018/06/28/1530183007850.html)
