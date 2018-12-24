---
layout: post
title: LNMP环境搭建篇:Nginx1.12.2编译安装
category: PHP
tags: [PHP]
---

LNMP环境搭建篇,系列第二篇nginx编译安装

## 目录

### 准备工作
```
#新建项目存放目录   
mkdir -p /data/wwwoot  	
#新建日志存放目录	
mkdir -p /data/log/nginx	
```

### 添加用户
```
useradd -r -s /sbin/nologin www
```

### 新建安装包存放目录

```
mkdir -p /data/pkg
cd /data/pkg
```

### 下载安装包解压
```
#nginx官方地址
#http://nginx.org/en/download.html

wget http://nginx.org/download/nginx-1.12.2.tar.gz
tar -zxf nginx-1.12.2.tar.gz
cd nginx-1.12.2
```

### 安装依赖扩展
```
yum -y install gcc gcc-c++ autoconf automake libtool make cmake
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel
```

### 设置系统资源限制
```
ulimit -SHn 65535
```

### 编译安装
```
./configure \
--prefix=/usr/local/nginx \
--user=www \
--group=www \
--with-http_ssl_module \
--with-pcre

#make的地方有一个小技巧，如果服务器是双核，可以通过-j2来指定用双核进行编译，-j4代表4核编译。
make -j && make install
```

### 启动nginx
```
/usr/local/nginx/sbin/nginx
```

### 查看nginx是否启动
```
ps -ef | grep nginx 
```

### 加入环境变量
```
vim /etc/profile

PATH=$PATH:/usr/local/nginx/sbin
export PATH

source /etc/profile
```

### 扩展
```
#常用nginx命令
/usr/local/nginx/sbin/nginx -s quit       停止ngix
/usr/local/nginx/sbin/nginx -s reload     重新载入nginx(当配置信息发生修改时)
/usr/local/nginx/sbin/nginx -v            查看版本
/usr/local/nginx/sbin/nginx -t            查看nginx的配置文件的目录
/usr/local/nginx/sbin/nginx -h            查看帮助信息
```



