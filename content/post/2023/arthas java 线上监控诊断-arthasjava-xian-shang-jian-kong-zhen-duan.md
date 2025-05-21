---
title: arthas java 线上监控诊断
date: 2023-03-17 14:40:29.515
updated: 2023-11-29 17:43:43.389
url: /archives/arthasjava-xian-shang-jian-kong-zhen-duan
categories: 
tags: 
---

### 运行自建镜像
```
 docker run -d --name arthas-tunnel-server --restart always -p 8580:8080 -p 7777:7777   weiliangning.work:18040/docker/arthas-tunnel-server
```

### java agent参数
-javaagent:/opt/arthas/arthas-agent.jar
-Darthas.tunnelServer=ws://www.weiliangning.work:7777/ws 
-Darthas.appName=appname
### docker 容器参数
--cap-add=SYS_ADMIN 必须给容器根路径权限 否则无法生产cpu 火焰图
profiler start

### arthas 获取spring bean

思路参考 https://github.com/alibaba/arthas/issues/482 

方法一 有dubbo
```
ognl '#context=@com.alibaba.dubbo.config.spring.extension.SpringExtensionFactory@contexts.iterator.next, #context.getBean("bean名字")'
```

方法二 有hutool

查看工具类的 classload
```
sc -d cn.hutool.extra.spring.SpringUtil
```
使用ognl 执行静态方法

```
ognl -c 685f4c2e '@cn.hutool.extra.spring.SpringUtil@getBean("bean名字")'
```