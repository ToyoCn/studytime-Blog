---
layout: post
title:  Mac配置Maven及IntelliJ IDEA Maven配置
category: Java 
tags: [Java]
keywords: Java,Sqoop,Sqoop学习之路
---

mac上安装maven、以及IDEA上Maven配置

## 目录

### 下载Maven
官方地址：http://maven.apache.org/download.cgi

### 解压maven
`tar -zxvf apache-maven-3.6.0-bin.tar.gz -C /Users/baihe/Software/apache-maven-3.6.1`

### 配置全局环境变量
```
vim ~/.bash_profile

# 写入
export MAVEN_HOME=/Users/baihe/Software/apache-maven-3.6.1
export PATH=$PATH:$MAVEN_HOME/bin

# 更新环境变量配置
source ~/.bash_profile
```

### 测试maven
`mvn -v`
![](https://static.studytime.xin/image/articles/java/mac-install-maven/maven-2.jpg?x-oss-process=image/resize,m_fixed,h_500,w_1000)

### 配置maven本地仓库
Maven很像一个很大仓库，装了很多的jar包，我们需要的时候就去拿，这就涉及到一个“本地仓库”的问题，默认情况，会在/user/name/.m2下，所以我们需要去更改一下。

```
# 创建仓库目录
cd /Users/baihe/Software/apache-maven-3.6.1
mkdir repository

# 修改自定义的本地仓库地址
vim /Users/baihe/Software/apache-maven-3.6.1/conf/settings.xml

# 修改本地的仓库地址
<localRepository> /Users/baihe/Software/apache-maven-3.6.1/repository </localRepository>

# 保存退出
:wq
```

### 中央仓库下载文件到本地库
`mvn help:system`

成功效果如下:
![](https://static.studytime.xin/image/articles/java/mac-install-maven/maven-1.jpg?x-oss-process=image/resize,m_fixed,h_500,w_1000)


### 配置Intellij IDEA
打开IntelliJ IDEA：
Preference->Build->Build Tools->Maven

![](https://static.studytime.xin/image/articles/java/mac-install-maven/maven-3.png?x-oss-process=image/resize,m_fixed,h_400,w_600)








