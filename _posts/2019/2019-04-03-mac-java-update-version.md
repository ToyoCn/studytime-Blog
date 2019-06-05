---
layout: post
title:  Mac 修改系统默认Java版本
category: PHP 
tags: [PHP]
---

Mac使用时，怎么去除修改系统默认Java版本
## 流程方法

### 查看当前版本，终端输入  
`java -version`

### 查看存在的java sdk版本
进入目录`/Library/Java/JavaVirtualMachines`


### 复制需要更改的java sdk所在目录更新使用版本
`export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home/`

![](https://static.studytime.xin/image/articles/java/update-version/mac-java-update-version1.jpg)


### 查看更新后的版本
`java -version`

```$xslt
➜  ~ java -version
java version "1.8.0_191"
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
```

### 永久生效变更版本
`echo 'export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home/' >> ~/.bash_profile source ~/.bash_profile
`





















