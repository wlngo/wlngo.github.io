---
title: 清理系统缓存
date: 2023-02-18 18:53:02.053
updated: 2023-02-18 18:53:02.053
url: /archives/qing-li-xi-tong-huan-cun
categories: 
tags: 
---

保存数据
```
sync
```
cache释放：
To free pagecache:
```
echo 1 > /proc/sys/vm/drop_caches
```