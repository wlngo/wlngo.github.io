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
![image-1659324639617](/upload/2022/08/image-1659324639617.png)![image]
#### git
![image-1659804236230](/upload/2022/08/image-1659804236230.png)
###### maven
![image-1659324682359](/upload/2022/08/image-1659324682359.png)

#### 配置插件
###### github
![image-1659335027512](/upload/2022/08/image-1659335027512.png)
###### ![image-1659335041226](/upload/2022/08/image-1659335041226.png)
###### maven
![image-1659335068848](/upload/2022/08/image-1659335068848.png)
###### ssh
![image-1659335087816](/upload/2022/08/image-1659335087816.png)
#### 设置maven 阿里云代理配置
#### 默认路径在 账号Jenkins（默认进入容器账号） ~ 根路径下.m2 隐藏文件
#### 设置github 服务器 
###### 凭据获取
![image-1659326968638](/upload/2022/08/image-1659326968638.png)
![image-1659326874772](/upload/2022/08/image-1659326874772.png)
#### Webhooks钩子函数
![image-1659327003630](/upload/2022/08/image-1659327003630.png)
![image-1659327033893](/upload/2022/08/image-1659327033893.png)
#### 构建后执行脚本
![image-1659336003564](/upload/2022/08/image-1659336003564.png)