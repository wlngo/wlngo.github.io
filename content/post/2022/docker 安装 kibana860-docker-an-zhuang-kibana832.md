---
title: docker 安装 kibana:8.6.0
date: 2022-07-28 20:19:12.545
updated: 2023-08-08 11:04:49.766
url: /archives/docker-an-zhuang-kibana832
categories: 
- Dcoker
tags: 
---

#### 下载镜像
```
docker pull kibana:8.6.0
```

#### 创建docke网络
```
docker  network create elknetwork
```

#### 运行镜像
```
docker run -d --name kibana  --restart=always  -p 5601:5601 \
-v /opt/elk/kibana/config/:/usr/share/kibana/config/  \
-v /opt/elk/kibana/data/:/usr/share/kibana/data/  \
-v /opt/elk/kibana/plugins/:/usr/share/kibana/plugins/  \
-v /opt/elk/kibana/logs/:/usr/share/kibana/logs/  \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
kibana:8.6.0
```
#### 先使用默认的Elasticsearch https 的设置 不做修改 后面改为http 不然 kibana 无法启动
#### 进入elasticsearch 镜像生产token
```
docker exec -it elasticsearch /bin/bash
```

```
sh bin/elasticsearch-create-enrollment-token --scope kibana
```

#### kibana配置文件必须为默认的 才能设置(物理机选择账号密码安装)

```
# Default Kibana configuration for docker target
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
```

#### 进入kibana 镜像获取验证码
```
docker exec -it kibana /bin/bash
```

```
sh bin/kibana-verification-code
```
#### 修改Elasticsearch 密码
```
docker exec -it elasticsearch /bin/bash
```

```
sh bin/elasticsearch-reset-password --username elastic -i
```
#### 生成插件加密密钥并 添加到 kibana.yml
```
docker exec -it kibana /bin/bash
```

```
 sh bin/kibana-encryption-keys generate
```

#### 修改 kibana 为中文  和修改es地址（否则导致重启后无法成功启动 修改为http协议）
```
docker cp kibana:/usr/share/kibana/config/kibana.yml  ~
```

##### 修改ip为容器域名 修改为http协议

```
#
# ** THIS IS AN AUTO-GENERATED FILE **
#

# Default Kibana configuration for docker target
#server.host: "0.0.0.0"
#server.shutdownTimeout: "5s"
#elasticsearch.hosts: [ "http://elasticsearch:9200" ]
#monitoring.ui.container.elasticsearch.enabled: true
### >>>>>>> BACKUP END: Kibana interactive setup (2022-07-30T02:22:45.284Z)

# This section was automatically generated during setup.
server.host: 0.0.0.0
server.shutdownTimeout: 5s
elasticsearch.hosts: ['http://elasticsearch:9200']
monitoring.ui.container.elasticsearch.enabled: true
elasticsearch.serviceAccountToken: AAEAAWVsYXN0aWMva2liYW5hL2Vucm9sbC1wcm9jZXNzLXRva2VuLTE2NTkxNDc3NjQ1NTc6NTdaNWtlOHJULXlSWkFfaVg2WWdSUQ
elasticsearch.ssl.certificateAuthorities: [/usr/share/kibana/data/ca_1659147765281.crt]
xpack.fleet.outputs: [{id: fleet-default-output, name: default, is_default: true, is_default_monitoring: true, type: elasticsearch, hosts: ['http://elasticsearch:9200'], ca_trusted_fingerprint: 9983b684a10b3cbc24e1c5816d911bdcdb323545fb36bb5fc3f685f3214efae9}]
#中文
i18n.locale: "zh-CN"
xpack.encryptedSavedObjects.encryptionKey: b363ba8a4cba829a212a9b1202ea2bb7
xpack.reporting.encryptionKey: b98e56b7304314427f71ec57d1a9d53d
xpack.security.encryptionKey: dc7505bc23e41561e586a025e4d83a45
```

```
docker cp kibana.yml kibana:/usr/share/kibana/config/kibana.yml  
```
#### 修改Elasticsearch 为http 协议
```
docker exec -it elasticsearch /bin/bash
```
```
docker cp elasticsearch:/usr/share/elasticsearch/config/elasticsearch.yml  ~
```
```

cluster.name: "docker-cluster"
network.host: 0.0.0.0

#----------------------- BEGIN SECURITY AUTO CONFIGURATION -----------------------
#
# The following settings, TLS certificates, and keys have been automatically
# generated to configure Elasticsearch security features on 30-07-2022 02:12:38
#
# --------------------------------------------------------------------------------

# Enable security features
xpack.security.enabled: true

xpack.security.enrollment.enabled: true

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false
  keystore.path: certs/http.p12

# Enable encryption and mutual authentication between cluster nodes
xpack.security.transport.ssl:
  enabled: true
  verification_mode: certificate
  keystore.path: certs/transport.p12
  truststore.path: certs/transport.p12
#----------------------- END SECURITY AUTO CONFIGURATION -------------------------
```

```
docker cp elasticsearch.yml elasticsearch:/usr/share/elasticsearch/config/elasticsearch.yml  
```

#### 重启容器
