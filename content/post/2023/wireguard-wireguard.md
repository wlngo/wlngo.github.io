---
title: wireguard
date: 2023-08-29 10:24:21.388
updated: 2024-05-31 19:46:05.901
url: /archives/wireguard
categories: 
tags: 
---

wireguard部署

>   -p 51820:51820/udp  暴露端口
  
```
docker run -d \
  --name=wireguard  \
  --cap-add=NET_ADMIN \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Shanghai \
  -e SERVERURL=www.wlngo.top  \
  -e SERVERPORT=51820 \
  -e PEERDNS=192.168.123.1  \
  -e INTERNAL_SUBNET=10.13.13.0  \
  -e ALLOWEDIPS=0.0.0.0/0  \
  -e PERSISTENTKEEPALIVE_PEERS="all" \
  -e LOG_CONFS=true  \
  -v /mnt/nvme0n1-4/opt/wireguard/config:/config \
  --sysctl="net.ipv4.conf.all.src_valid_mark=1" \
  --restart always \
  lscr.io/linuxserver/wireguard:v1.0.20210914-ls31
```

wireguard-ui 初始化使用 0.5.2版本 0.6.2 无法初始化
> --network=wireguard 
```
docker run -d  --net=host  -e 'BIND_ADDRESS'='192.168.123.1:5000' --restart=always --name wireguard-ui \
-e WGUI_MANAGE_RESTART=true -e WGUI_MANAGE_START=true --cap-add=NET_ADMIN \
-v /mnt/nvme0n1-4/opt/wireguard-ui/db:/app/db -v /mnt/nvme0n1-4/opt/wireguard/config/wg_confs:/etc/wireguard \
ngoduykhanh/wireguard-ui:0.6.2

```

inotifywait 监控文件变化脚本
```
#!/bin/bash
dir=/opt/wireguard/config/wg_confs/wg0.conf               #指定需要监视的文件夹
log_file=/opt/wireguard/logs/changes.log   #指定输出信息的文件，方便后面查看
while
inotifywait -r $dir -o $log_file -e modify  --timefmt '%y-%m-%d %H:%M' --format '%T %w%f %e'      #这里只监视文件的close动作
do
    docker restart  wireguard        #执行同步文件的脚本
done
```