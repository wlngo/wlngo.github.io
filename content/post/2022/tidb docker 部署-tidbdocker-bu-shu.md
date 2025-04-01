---
title: tidb docker 部署
date: 2022-07-24 00:14:34.932
updated: 2022-08-08 15:35:44.788
url: /archives/tidbdocker-bu-shu
categories:
tags:
  - tidb
  - docker
---
# 部署
## 部署pd1
```
docker run -d --name pd1 \
  --restart always\
  -e TZ=Asia/Shanghai \
  -v /opt/tidb/node_exporter:/opt/tidb/node_exporter \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/pd1/data:/data \
   --network=elknetwork \
  pingcap/pd:v7.1.0 \
  --name="pd1" \
  --data-dir="/data/pd1" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://pd1:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://pd1:2380" \
  --initial-cluster="pd1=http://pd1:2380,pd2=http://pd2:2380,pd3=http://pd3:2380"
```
## 部署pd2
```
docker run -d --name pd2 \
  -p 2379:2379 \
  --restart always \
   -e TZ=Asia/Shanghai \
   -v /opt/tidb/node_exporter:/opt/tidb/node_exporter \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/pd2/data:/data \
   --network=elknetwork \
  pingcap/pd:v7.1.0 \
  --name="pd2" \
  --data-dir="/data/pd2" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://pd2:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://pd2:2380" \
  --initial-cluster="pd1=http://pd1:2380,pd2=http://pd2:2380,pd3=http://pd3:2380"
```
## 部署pd3
```
docker run -d --name pd3 \
  --restart always \
   -e TZ=Asia/Shanghai \
  -v /opt/tidb/node_exporter:/opt/tidb/node_exporter \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/pd3/data:/data \
   --network=elknetwork \
  pingcap/pd:v7.1.0 \
  --name="pd3" \
  --data-dir="/data/pd3" \
  --client-urls="http://0.0.0.0:2379" \
  --advertise-client-urls="http://pd3:2379" \
  --peer-urls="http://0.0.0.0:2380" \
  --advertise-peer-urls="http://pd3:2380" \
  --initial-cluster="pd1=http://pd1:2380,pd2=http://pd2:2380,pd3=http://pd3:2380"
```

## 部署tikv1
```
docker run -d --name tikv1 \
  --restart always \
  --ulimit nofile=1000000:1000000 \
   -e TZ=Asia/Shanghai \
  -v /opt/tidb/node_exporter:/opt/tidb/node_exporter \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/tikv1/data:/data \
   --network=elknetwork \
  pingcap/tikv:v7.1.0 \
  --addr="0.0.0.0:20160" \
  --advertise-addr="tikv1:20160" \
  --data-dir="/data/tikv1" \
  --pd="pd1:2379,pd2:2379,pd3:2379" \
  --advertise-status-addr="tikv1:20180" \
  --status-addr="0.0.0.0:20180"
```
## 部署tikv2

```
docker run -d --name tikv2 \
  --restart always \
  --ulimit nofile=1000000:1000000 \
   -e TZ=Asia/Shanghai \
  -v /opt/tidb/node_exporter:/opt/tidb/node_exporter \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/tikv2/data:/data \
   --network=elknetwork \
  pingcap/tikv:v7.1.0 \
  --addr="0.0.0.0:20160" \
  --advertise-addr="tikv2:20160" \
  --data-dir="/data/tikv2" \
  --pd="pd1:2379,pd2:2379,pd3:2379" \
  --advertise-status-addr="tikv2:20180" \
  --status-addr="0.0.0.0:20180"
```
## 部署tikv3
```docker run -d --name tikv3 \
  --restart always \
  --ulimit nofile=1000000:1000000 \
   -e TZ=Asia/Shanghai \
  -v /opt/tidb/node_exporter:/opt/tidb/node_exporter \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/tikv3/data:/data \
   --network=elknetwork \
  pingcap/tikv:v7.1.0 \
  --addr="0.0.0.0:20160" \
  --advertise-addr="tikv3:20160" \
  --data-dir="/data/tikv3" \
  --pd="pd1:2379,pd2:2379,pd3:2379" \
  --advertise-status-addr="tikv3:20180" \
  --status-addr="0.0.0.0:20180"
```

## 部署tidb
```
docker run -d --name tidb \
  --restart always \
  -p 4000:4000 \
  -p 10080:10080 \
   -e TZ=Asia/Shanghai \
  -v /opt/tidb/node_exporter:/opt/tidb/node_exporter \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
   --network=elknetwork \
  pingcap/tidb:v7.1.0 \
  --store=tikv \
  --path="pd1:2379,pd2:2379,pd3:2379"
```
## 部署tiflash
```
docker run --name tiflash \
  -e TZ=Asia/Shanghai \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/tiflash/data:/data \
  -v /opt/tidb/tiflash/conf/tiflash.toml:/opt/tidb/tiflash/conf/tiflash.toml \
  --restart always  -d \
   --network=elknetwork \
   pingcap/tiflash:v7.1.0 --config=/opt/tidb/tiflash/conf/tiflash.toml
```

## 部署ng-monitoring
```
docker run --name ng-monitoring \
  -e TZ=Asia/Shanghai \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
  -v /opt/tidb/ng-monitoring/data:/data \
  -v /opt/tidb/ng-monitoring/conf/config.toml:/opt/tidb/ng-monitoring/conf/config.toml \
  --restart always  -d \
   --network=elknetwork \
   pingcap/ng-monitoring:v7.1.0 --config=/opt/tidb/ng-monitoring/conf/config.toml    
   
   
   docker exec tidb sh /opt/tidb/node_exporter/start.sh 
   docker exec pd1 sh /opt/tidb/node_exporter/start.sh
   docker exec pd2 sh /opt/tidb/node_exporter/start.sh
   docker exec pd3 sh /opt/tidb/node_exporter/start.sh
   docker exec tikv1 sh /opt/tidb/node_exporter/start.sh
   docker exec tikv2 sh /opt/tidb/node_exporter/start.sh
    docker exec tikv3 sh /opt/tidb/node_exporter/start.sh
```


## 内存优化
```
docker update --memory 6g --memory-swap -1 tiflash tidb tikv1  tikv2  tikv3  pd1 pd2 pd3
docker update --memory 1g --memory-swap -1 ng-monitoring
```
SELECT @@tidb_server_memory_limit  默认=80%
SET GLOBAL tidb_server_memory_limit = "4GB";
SELECT @@tidb_server_memory_limit_gc_trigger 默认=0.7
select sysdate()  查询日期
ALTER USER 'root'@'%' IDENTIFIED BY 'mypass';  修改root密码 

# HAProxy 代理tidb
安装
```
sudo apt install haproxy
```
/etc/haproxy/haproxy.cfg 配置
```
global                                     # 全局配置。
   log         127.0.0.1 local2            # 定义全局的 syslog 服务器，最多可以定义两个。
   chroot      /var/lib/haproxy            # 更改当前目录并为启动进程设置超级用户权限，从而提高安全性。
   pidfile     /var/run/haproxy.pid        # 将 HAProxy 进程的 PID 写入 pidfile。
   maxconn     4000                        # 每个 HAProxy 进程所接受的最大并发连接数。
   user        haproxy                     # 同 UID 参数。
   group       haproxy                     # 同 GID 参数，建议使用专用用户组。
   nbproc      40                          # 在后台运行时创建的进程数。在启动多个进程转发请求时，确保该值足够大，保证 HAProxy 不会成为瓶颈。
   daemon                                  # 让 HAProxy 以守护进程的方式工作于后台，等同于命令行参数“-D”的功能。当然，也可以在命令行中用“-db”参数将其禁用。
   stats socket /var/lib/haproxy/stats     # 统计信息保存位置。

defaults                                   # 默认配置。
   log global                              # 日志继承全局配置段的设置。
   retries 2                               # 向上游服务器尝试连接的最大次数，超过此值便认为后端服务器不可用。
   timeout connect  2s                     # HAProxy 与后端服务器连接超时时间。如果在同一个局域网内，可设置成较短的时间。
   timeout client 30000s                   # 客户端与 HAProxy 连接后，数据传输完毕，即非活动连接的超时时间。
   timeout server 30000s                   # 服务器端非活动连接的超时时间。

listen admin_stats                         # frontend 和 backend 的组合体，此监控组的名称可按需进行自定义。
   bind 0.0.0.0:8080                       # 监听端口。
   mode http                               # 监控运行的模式，此处为 `http` 模式。
   option httplog                          # 开始启用记录 HTTP 请求的日志功能。
   maxconn 10                              # 最大并发连接数。
   stats refresh 30s                       # 每隔 30 秒自动刷新监控页面。
   stats uri /haproxy                      # 监控页面的 URL。
   stats realm HAProxy                     # 监控页面的提示信息。
   stats auth admin:pingcap123             # 监控页面的用户和密码，可设置多个用户名。
   stats hide-version                      # 隐藏监控页面上的 HAProxy 版本信息。
   stats  admin if TRUE                    # 手工启用或禁用后端服务器（HAProxy 1.4.9 及之后版本开始支持）。

listen tidb-cluster                        # 配置 database 负载均衡。
   bind 0.0.0.0:3390                       # 浮动 IP 和 监听端口。
   mode tcp                                # HAProxy 要使用第 4 层的传输层。
   balance leastconn                       # 连接数最少的服务器优先接收连接。`leastconn` 建议用于长会话服务，例如 LDAP、SQL、TSE 等，而不是短会话协议，如 HTTP。该算法是动态的，对于启动慢的服务器，服务器权重会在运行中作调整。
   server tidb-1 10.9.18.229:4000 check inter 2000 rise 2 fall 3       # 检测 4000 端口，检测频率为每 2000 毫秒一次。如果 2 次检测为成功，则认为服务器可用；如果 3 次检测为失败，则认为服务器不可用。
   server tidb-2 10.9.39.208:4000 check inter 2000 rise 2 fall 3
   server tidb-3 10.9.64.166:4000 check inter 2000 rise 2 fall 3
```