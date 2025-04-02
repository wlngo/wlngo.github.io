---
title: 内存检测命令
date: 2023-01-01 17:55:18.483
updated: 2023-01-01 17:55:18.483
url: /archives/nei-cun-jian-ce-ming-ling
categories: 
tags: 
---

查看内存错误
```
grep [0-9] /sys/devices/system/edac/mc/mc*/csrow*/ch*_ce_count
```
```
 dmesg|grep -i error
```
查看内存速度
```
dmidecode -t memory
```