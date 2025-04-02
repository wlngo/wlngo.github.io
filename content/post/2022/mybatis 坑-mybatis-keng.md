---
title: mybatis 坑
date: 2022-12-26 15:02:15.902
updated: 2022-12-26 15:02:15.902
url: /archives/mybatis-keng
categories: 
tags: 
---

如果@Parm参数为对象     并且为多参数     并且使用了bind     剩余参数即使不需要bind  也要进行绑定  否则接受不到参数