---
layout: post
title: 环境搭建篇:mongodb编译安装配置篇
category: PHP 
tags: [PHP]
---


mongodb编译以及安装配置相关

### 目录

### 创建mongodb用户组和用户

```
groupadd mongodb
useradd -r -g mongodb -s /sbin/nologin -M mongodb
```

### 下载加压mongodb包
```
tar -zxf mongodb-linux-x86_64-rhel62-3.2.10.tgz

```

### 创建mongodb相关模目录
```
mkdir -p /data/db/mongodb
mkdir -p /usr/local/mongodb/conf
mkdir -p /var/run/mongodb
mkdir -p /data/log/mongodb
```

### 将文件复制到mongodb/目录

```
cp -R /date/pkg/mongodb-linux-x86_64-rhel62-3.2.10/. /usr/local/mongodb
```

### 创建mongodb配置文件mongodb.conf
```
vim /usr/local/mongodb/conf/mongodb.conf

```

### 添加下面内容，保存退出
```

dbpath=/data/db/mongodb/ #数据目录存在位置
logpath=/data/log/mongodb/mongodb.log #日志文件存放目录
logappend=true #写日志的模式:设置为true为追加
fork=true  #以守护程序的方式启用，即在后台运行
verbose=true
vvvv=true #启动verbose冗长信息，它的级别有 vv~vvvvv，v越多级别越高，在日志文件中记录的信息越详细
maxConns=20000 #默认值：取决于系统（即的ulimit和文件描述符）限制。MongoDB中不会限制其自身的连接
pidfilepath=/var/run/mongodb/mongodb.pid
directoryperdb=true #数据目录存储模式,如果直接修改原来的数据会不见了
profile=0 #数据库分析等级设置,0 关 2 开。包括所有操作。 1 开。仅包括慢操作
slowms=200 #记录profile分析的慢查询的时间，默认是100毫秒
quiet=true
syncdelay=60 #刷写数据到日志的频率，通过fsync操作数据。默认60秒
port=27017  #端口
#bind_ip = 10.1.146.163 #IP
#auth=true  #开始认证
#nohttpinterface=false #28017 端口开启的服务。默认false，支持
#notablescan=false#不禁止表扫描操作
#cpu=true #设置为true会强制mongodb每4s报告cpu利用率和io等待，把日志信息写到标准输出或日志文件

```

### 修改mongodb目录权限
```
chown -R mongodb:mongodb /usr/local/mongodb 
chown -R mongodb:mongodb /var/run/mongodb 
chown -R mongodb:mongodb /data/log/mongodb
```

### 将mongodb命令加入环境变量，修改profile文件
```
vim /etc/profile

PATH=$PATH:/usr/local/mongodb/bin
export PATH

source /etc/profile
```

### 将mongodb服务脚本加入到init.d/目录，创建mongod文件

```
vim /etc/init.d/mongod

#!/bin/sh  
# chkconfig: 2345 93 18
# description:MongoDB  

#默认参数设置
#mongodb 家目录
MONGODB_HOME=/usr/local/mongodb

#mongodb 启动命令
MONGODB_BIN=$MONGODB_HOME/bin/mongod

#mongodb 配置文件
MONGODB_CONF=$MONGODB_HOME/conf/mongodb.conf

MONGODB_PID=/var/run/mongodb/mongodb.pid

#最大文件打开数量限制
SYSTEM_MAXFD=65535

#mongodb 名字  
MONGODB_NAME="mongodb"
. /etc/rc.d/init.d/functions

if [ ! -f $MONGODB_BIN ]
then
    echo "$MONGODB_NAME startup: $MONGODB_BIN not exists! "  
    exit
fi

start(){
    ulimit -HSn $SYSTEM_MAXFD
    $MONGODB_BIN --config="$MONGODB_CONF"  
    ret=$?
    if [ $ret -eq 0 ]; then
        action $"Starting $MONGODB_NAME: " /bin/true
    else
        action $"Starting $MONGODB_NAME: " /bin/false
    fi
}

stop(){
    PID=$(ps aux |grep "$MONGODB_NAME" |grep "$MONGODB_CONF" |grep -v grep |wc -l) 
    if [[ $PID -eq 0  ]];then
        action $"Stopping $MONGODB_NAME: " /bin/false
        exit
    fi
    kill -HUP `cat $MONGODB_PID`
    ret=$?
    if [ $ret -eq 0 ]; then
        action $"Stopping $MONGODB_NAME: " /bin/true
        rm -f $MONGODB_PID
    else   
        action $"Stopping $MONGODB_NAME: " /bin/false
    fi
}

restart(){
    stop
    sleep 2
    start
}

case "$1" in
    start)
        start
        ;;
    stop)
        stop
        ;;
    status)
    status $prog
        ;;
    restart)
        restart
        ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart}"
esac

```

### 为mongod添加可执行权限

```language
chmod +x /etc/init.d/mongod
```

### 将mongodb加入系统服务

```language
chkconfig --add mongod
```

### 修改服务的默认启动等级

```
chkconfig mongod on
```

### 启动mongodb

```
service mongod start
```

### 查看是否启动

```
ps -ef | grep mongod
netstat -anp | grep  27017
```








