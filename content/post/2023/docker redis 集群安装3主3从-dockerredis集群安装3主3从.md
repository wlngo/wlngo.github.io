---
title: docker redis 集群安装3主3从
date: 2023-07-11 09:25:06.912
updated: 2023-10-23 17:06:35.629
url: /archives/dockerredis集群安装3主3从
categories: 
tags: 
---

1主
```
docker run -d --name redis-master-1 --network=elknetwork --restart=always \
-p 6429:6429  -p 16429:16429 \
-v /opt/redis-cluster/master-1/log:/var/log/redis/ \
-v /opt/redis-cluster/master-1/conf:/usr/local/etc/redis/ \
-v /opt/redis-cluster/master-1/data/:/var/lib/redis/ \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
redis \
redis-server /usr/local/etc/redis/redis.conf
```
2主
```
docker run -d --name redis-master-2 --network=elknetwork --restart=always \
-p 6449:6449  -p 16449:16449 \
-v /opt/redis-cluster/master-2/log:/var/log/redis/ \
-v /opt/redis-cluster/master-2/conf:/usr/local/etc/redis/ \
-v /opt/redis-cluster/master-2/data/:/var/lib/redis/ \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
redis \
redis-server /usr/local/etc/redis/redis.conf
```
3主
```
docker run -d --name redis-master-3 --network=elknetwork --restart=always \
-p 6469:6469   -p 16469:16469 \
-v /opt/redis-cluster/master-3/log:/var/log/redis/ \
-v /opt/redis-cluster/master-3/conf:/usr/local/etc/redis/ \
-v /opt/redis-cluster/master-3/data/:/var/lib/redis/ \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
redis \
redis-server /usr/local/etc/redis/redis.conf
```
从1
```
docker run -d --name redis-slave-1 --network=elknetwork --restart=always \
-p 6439:6439  -p 16439:16439 \
-v /opt/redis-cluster/slave-1/log:/var/log/redis/ \
-v /opt/redis-cluster/slave-1/conf:/usr/local/etc/redis/ \
-v /opt/redis-cluster/slave-1/data/:/var/lib/redis/ \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
redis \
redis-server /usr/local/etc/redis/redis.conf
```
从2
```
docker run -d --name redis-slave-2 --network=elknetwork --restart=always \
-p 6459:6459  -p 16459:16459 \
-v /opt/redis-cluster/slave-2/log:/var/log/redis/ \
-v /opt/redis-cluster/slave-2/conf:/usr/local/etc/redis/ \
-v /opt/redis-cluster/slave-2/data/:/var/lib/redis/ \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
redis \
redis-server /usr/local/etc/redis/redis.conf
```
从3
```
docker run -d --name redis-slave-3 --network=elknetwork --restart=always \
-p 6479:6479  -p 16479:16479 \
-v /opt/redis-cluster/slave-3/log:/var/log/redis/ \
-v /opt/redis-cluster/slave-3/conf:/usr/local/etc/redis/ \
-v /opt/redis-cluster/slave-3/data/:/var/lib/redis/ \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
redis \
redis-server /usr/local/etc/redis/redis.conf
```

默认参数
```
docker run -d --name redis-cluster --restart=always -p 6379:6379  -p 16379:16379 \
-e TZ=Asia/Shanghai \
-v /opt/redis-cluster/log:/var/log/redis/ \
-v /opt/redis-cluster/conf:/usr/local/etc/redis/ \
-v /opt/redis-cluster/data/:/var/lib/redis/ \
-v /etc/localtime:/etc/localtime:ro \
redis:7.0 redis-server /usr/local/etc/redis/redis.conf
```
配置文件
```
#docker 启动不开启守护进程  物理机开启
daemonize no
#开启集群模式
cluster-enabled yes
cluster-announce-ip 192.168.123.210
cluster-announce-port 6379
#集群通讯端口
cluster-announce-bus-port 16379
```


规划和集群
```
cluster nodes 查看集群信息	
cluster replicate 映射主节点
主
192.168.123.117 6379
192.168.123.196 6379
192.168.123.139 6379


从
192.168.123.221 6379
192.168.123.176 6379
192.168.123.242 6379

建立联系
redis-cli -h 192.168.123.117 -p 6379 -a 密码  cluster replicate  主节点
cluster meet 192.168.123.196 6379
cluster meet 192.168.123.139 6379
cluster meet 192.168.123.221 6379
cluster meet 192.168.123.176 6379
cluster meet 192.168.123.242 6379

映射主节点

redis-cli -h 192.168.123.221 -p 6379 -a 密码  cluster replicate  主节点
redis-cli -h 192.168.123.176 -p 6379 -a 密码  cluster replicate 主节点
redis-cli -h 192.168.123.242 -p 6379 -a 密码  cluster replicate 主节点
主节点分配哈希槽

redis-cli -h 192.168.123.117 -p 6379 -a 密码  cluster addslots {0..5461}
redis-cli -h 192.168.123.196 -p 6379 -a 密码  cluster addslots {5462..10922}
redis-cli -h 192.168.123.139 -p 6379 -a 密码  cluster addslots {10923..16383}

```


Envoy Proxy 用于代理redis 集群
envoy.yaml
```
static_resources:
  listeners:
  - name: redis_listener
    address:
      socket_address:
        address: 0.0.0.0
        port_value: 6480
    filter_chains:
    - filters:
      - name: envoy.filters.network.redis_proxy
        typed_config:
          "@type": type.googleapis.com/envoy.extensions.filters.network.redis_proxy.v3.RedisProxy
          stat_prefix: egress_redis
          settings:
            op_timeout: 5s
          prefix_routes:
            catch_all_route:
              cluster: redis_cluster
          downstream_auth_passwords:
            - inline_string: "密码"
  clusters:
  - name: redis_cluster
    cluster_type:
      name: envoy.clusters.redis
      typed_config:
        "@type": type.googleapis.com/google.protobuf.Struct
        value:
          cluster_refresh_rate: 10s
          cluster_refresh_timeout: 4s
    connect_timeout: 4s
    dns_lookup_family: V4_ONLY
    lb_policy: CLUSTER_PROVIDED
    load_assignment:
      cluster_name: redis_cluster
      endpoints:
        lb_endpoints:
        - endpoint:
            address:
              socket_address:
                address: 192.168.123.210 
                port_value: 6429
        - endpoint:
            address:
              socket_address:
                address: 192.168.123.210 
                port_value: 6439
        - endpoint:
            address:
              socket_address:
                address: 192.168.123.210 
                port_value: 6449
        - endpoint:
            address:
              socket_address:
                address: 192.168.123.210 
                port_value: 6459
        - endpoint:
            address:
              socket_address:
                address: 192.168.123.210 
                port_value: 6469
        - endpoint:
            address:
              socket_address:
                address: 192.168.123.210 
                port_value: 6479     
    typed_extension_protocol_options:
      envoy.filters.network.redis_proxy:
        "@type": type.googleapis.com/google.protobuf.Struct
        value:
          auth_password:
            inline_string: "密码"                   
admin:
  access_log_path: "/opt/envoy/log/access.log"
  address:
    socket_address:
      address: 0.0.0.0
      port_value: 6490
```

```
docker run -d --name envoy --network=elknetwork --restart=always \
-p 6480:6480 -p 6490:6490 \
-v /opt/envoy/conf/envoy.yaml:/etc/envoy/envoy.yaml \
-v /opt/envoy/log:/opt/envoy/log \
-v /etc/localtime:/etc/localtime \
envoyproxy/envoy:v1.26-latest -c  /etc/envoy/envoy.yaml --service-cluster proxy --log-level info
```
docker run -d --name twemproxy --network=elknetwork --restart=always \
-p 6480:6480 \
-v /etc/nutcracker.conf:/opt/twemproxy/conf/nutcracker.conf \
malexer/twemproxy

