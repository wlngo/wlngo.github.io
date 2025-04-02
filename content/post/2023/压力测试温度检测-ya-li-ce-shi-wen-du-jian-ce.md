---
title: 压力测试温度检测
date: 2023-01-01 19:54:49.762
updated: 2023-01-01 19:54:49.762
url: /archives/ya-li-ce-shi-wen-du-jian-ce
categories: 
tags: 
---

消耗 CPU 资源
stress 消耗 CPU 资源的方式是通过调用 sqrt 函数计算由 rand 函数产生的随机数的平方根实现的。下面的命令会产生 32 个这样的进程不断的进行计算

```
stress -c 32
```
温度检测
```
sensors
```