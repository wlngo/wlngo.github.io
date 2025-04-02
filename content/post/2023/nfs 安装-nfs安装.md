---
title: nfs 安装
date: 2023-08-23 22:40:03.3
updated: 2023-08-23 22:56:49.307
url: /archives/nfs安装
categories: 
tags: 
---

#安装NFS服务器端
```
sudo apt-get install nfs-kernel-server
```
NFS服务器端配置 (/etc/exports)
```
/opt/nfs 192.168.123.0/24(rw,sync,no_subtree_check,insecure,no_root_squash)
/opt/nfs 10.0.0.0/24(rw,sync,no_subtree_check,insecure,no_root_squash)
```
解释
1. rw 读写访问
2. sync 所有数据在请求时写入共享
3. No_root_squash 客户机用root访问该共享文件夹时，不映射root用户
4. insecure NFS通过1024以上的端口发送 (如果客户端必须要 mount -o noresvport 访问，那么就需要 nfs 服务器配置允许非特权端口访问。需要修改 /etc/exports，设置 insecure 选项。)
5. no_subtree_check 不检查父目录权限


#安装NFS客户端
```
sudo apt-get install nfs-common
```

NFS客户端开机自启 配置目录(/etc/fstab)
```
#nfs
10.0.0.2:/opt/nfs /opt/webserver nfs vers=4,minorversion=0,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,_netdev,noresvport 0 0
```
参数解释
1. vers=4 文件系统版本4
2. minorversion=0 生成注册句柄 minorVersion 是供应商为其产品定义的版本识别码 通常搭配vers=4使用
3. rsize 每个READ命令字向服务器读取文件的最大字节数。实际数据小于或等于此值。resize必须是1024倍数的正整数，小于1024时自动设为4096，大于1048576时自动设为1048576。默认时，服务器和客户端进行协商后设置
4. wsize 每个WRITE命令字向服务器写入文件的最大字节数。实际数据小于或等于此值。resize必须是1024倍数的正整数，小于1024时自动设为4096，大于1048576时自动设为1048576。默认时，服务器和客户端进行协商后设置。
5. hard 使用硬挂载的方式挂载系统，该值是默认值，重复请求直到NFS服务器回应
6. timeo=600 — 将NFS 客户端在重试NFS 请求之前用于等待响应的超时值设置为600 分秒（60 秒）。 如果您必须更改超时参数( timeo )，我们建议您使用至少为 150 的值，这相当于15 秒
7.  retrans=2 — 将NFS 客户端在尝试进一步恢复操作之前重试请求的次数设置为2。 noresvport — 告诉NFS 客户端在重新建立网络连接时使用新的传输控制协议(TCP) 源端口。
8. _netdev它用于指示系统在网络挂载文件系统时等待网络连接建立之后再进行挂载操作
9. noresvport — 告诉NFS 客户端在重新建立网络连接时使用新的传输控制协议(TCP) 源端口。 这样做有助于确保EFS 文件系统在网络恢复事件后具有不间断的可用性。 _netdev — 如果存在 /etc/fstab ，则防止客户端在启用网络之前尝试装载EFS 文件系统  
(如果客户端必须要 mount -o noresvport 访问，那么就需要 nfs 服务器配置允许非特权端口访问。需要修改 /etc/exports，设置 insecure 选项。）

[阿里云nfs挂载参考](https://help.aliyun.com/zh/nas/enable-an-automatic-mount-of-an-nfs-file-system-at-server-startup)