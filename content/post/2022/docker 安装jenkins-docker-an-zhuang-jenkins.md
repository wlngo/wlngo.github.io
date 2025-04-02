---
title: docker 安装jenkins
date: 2022-07-30 11:14:50.437
updated: 2023-03-28 09:53:09.149
url: /archives/docker-an-zhuang-jenkins
categories: 
tags: 
---

#### 下载镜像
```
docker pull jenkins/jenkins:lts-jdk11
```

#### 运行镜像(不必限制内存 )
```
docker  run  -d  --name jenkins -p 8050:8080 -p 50000:50000  \
-v /opt/jenkins_home:/var/jenkins_home -v /root/.m2:/root/.m2  -uroot  \
-v /root/tls/docker/:/root/tls/docker \
-e JAVA_OPTS:"-Xmx4g -Xms4g"--restart=always  \
weiliangning.work:18040/docker/jenkins/jenkins:lts-jdk17
```
#### 默认安装

#### 配置全局工具
###### jdk
#### git
###### maven

#### 配置插件
###### github
###### maven
###### ssh
#### 设置maven 阿里云代理配置
#### 默认路径在 账号Jenkins（默认进入容器账号） ~ 根路径下.m2 隐藏文件
#### 设置github 服务器 
###### 凭据获取
、
#### Webhooks钩子函数
#### 构建后执行脚本
