---
title: jenkins 删除历史构建
date: 2023-03-27 22:18:02.58
updated: 2023-03-27 22:18:02.58
url: /archives/jenkins-shan-chu-li-shi-gou-jian
categories: 
tags: 
---

![image](/upload/2023/03/image.png)
```
def jobName = "tes"  //删除的项目名称
def maxNumber = 200 // 保留的最小编号，意味着小于该编号的构建都将被删除

Jenkins.instance.getItemByFullName(jobName).builds.findAll {
it.number <= maxNumber
}.each {
it.delete()
}
```