---
title: SkyWalking 9 安装
date: 2023-03-14 10:37:04.567
updated: 2023-12-15 17:53:24.652
url: /archives/skywalking9安装
categories: 
tags: 
---

## oap

```
docker run --name oap  --restart always -d \
-e SW_STORAGE=elasticsearch \
-e SW_STORAGE_ES_CLUSTER_NODES=10.0.0.2:9200 \
-e SW_ES_USER=elastic -e SW_ES_PASSWORD=密码 \
-e SW_CORE_GRPC_SSL_ENABLED=true \
-e SW_CORE_GRPC_SSL_KEY_PATH=/skywalking/config/tls/server.pem \
-e SW_CORE_GRPC_SSL_CERT_CHAIN_PATH=/skywalking/config/tls/server.crt \
-e SW_CORE_GRPC_SSL_TRUSTED_CA_PATH=/skywalking/config/tls/ca.crt \
-e JAVA_OPTS="-Xms2048m -Xmx2048m" \
-v /opt/oap/tls/:/skywalking/config/tls/ \
-p 11800:11800 -p 12800:12800 \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
apache/skywalking-oap-server:9.7.0-java17
```

agent.instance_uuid

## ui
```
docker run --name oap-ui  --restart always -d  \
-p 8380:8080 \
-e SW_OAP_ADDRESS=http://10.0.0.2:12800 \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
apache/skywalking-ui:9.7.0-java17
```