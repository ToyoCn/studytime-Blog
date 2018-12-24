---
layout: post
title: 环境搭建篇:redis编译安装配置篇
category: PHP 
tags: [PHP]
---


redis编译以及安装配置相关

## 目录

### 安装前准备

```
yum install gcc 
yum install gcc-c++ 

mkdir -p /data/pkg
cd /data/pkg
```

### 下载源码包以及解压
```
wget http://download.redis.io/releases/redis-4.0.10.tar.gz
tar -zxf redis-4.0.10.tar.gz
cd redis-4.0.10
```

### 编译安装

```
make
make install
```

make install 后，会在/usr/local/bin目录底下生成多个可执行文件。
```

redis-cli 				redis命令行操作工具
redis-benchmark			redis性能测试工具	
redis-check-aof 		数据修复
redis-check-dump 		检查导出工具
redis-sentinel			redis哨兵
redis-server			redis服务启动
```

### 配置前准备

```
mkdir -p /usr/local/redis/bin
mkdir -p /usr/local/redis/etc

mv /usr/local/bin/redis-* /usr/local/redis/bin/

ln -s /usr/local/redis/bin/{redis-cli,redis-server} /usr/local/bin
```

### 配置

```
cp redis.conf /usr/local/redis/etc 
cp sentinel.conf /usr/local/redis/etc

ln -s /usr/local/redis/etc/* /usr/local/etc
```

### 修改配置文件

```

vim /usr/local/redis/etc/redis.conf

#修改Redis配置文件，使Redis以后台进程的形式启动
将daemonize no这行修改为daemonize yes
修改requirepass foobared 为requirepass Syudytime% 可设置成自己的密码
修改bind 127.0.0.1 为bind 0.0.0.0 可开启远程访问
修改appendonly no 为appendonly yes #开启aof持久化

```

### 启动服务

```
/usr/local/bin/redis-server /usr/local/redis/etc/redis.conf

ps -ef | grep redis
netstat -tunpl | grep 6379
```

### 停止
```
pkill redis-server
或者
/usr/local/bin/redis-cli shutdown
```

### 将redis做成服务

#### 复制脚本到/etc/rc.d/init.d目录

```
pkill redis-server
cp /data/pkg/redis-4.0.8/utils/redis_init_script /etc/rc.d/init.d/redis
```

#### 如果这时添加注册服务：

```
chkconfig --add redis
```

#### 将报以下错误：

```
redis服务不支持chkconfig
```

为此，我们需要更改redis脚本。

#### 更改redis脚本

```
vim /etc/rc.d/init.d/redis
```
看到的配置文件

```
#!/bin/sh 
#chkconfig: 2345 80 90 
# Simple Redis init.d script conceived to work on Linux systems 
# as it does use of the /proc filesystem. 
   
REDISPORT=6379 
EXEC=/usr/local/redis/bin/redis-server 
CLIEXEC=/usr/local/redis/bin/redis-cli 
   
PIDFILE=/var/run/redis_${REDISPORT}.pid 
CONF="/etc/redis/${REDISPORT}.conf" 
   
case "$1" in 
    start) 
        if [ -f $PIDFILE ] 
        then 
                echo "$PIDFILE exists, process is already running or crashed" 
        else 
                echo "Starting Redis server..." 
                $EXEC $CONF & 
        fi 
        ;; 
    stop) 
        if [ ! -f $PIDFILE ] 
        then 
                echo "$PIDFILE does not exist, process is not running" 
        else 
                PID=$(cat $PIDFILE) 
                echo "Stopping ..." 
                $CLIEXEC -p $REDISPORT shutdown 
                while [ -x /proc/${PID} ] 
                do 
                    echo "Waiting for Redis to shutdown ..." 
                    sleep 1 
                done 
                echo "Redis stopped" 
        fi 
        ;; 
    *) 
        echo "Please use start or stop as first argument" 
        ;; 
esac

```

和原配置文件相比:
1. 原文件是没有以下第2行的内容的，

```
#chkconfig: 2345 80 90
```

2. 原文件EXEC、CLIEXEC参数，也是有所更改。

```
EXEC=/work/redis/bin/redis-server   
CLIEXEC=/work/redis/bin/redis-cli 
```

3. redis开启的命令，以后台运行的方式执行。

```
$EXEC $CONF &
```
4. 将redis配置文件拷贝到/etc/redis/${REDISPORT}.conf

```
mkdir /etc/redis  
cp /usr/local/redis/etc/redis.conf /etc/redis/6379.conf  
```

### 注册redis服务

```
chkconfig --add redis
```

### 启动redis服务

```
service redis start
```

### 将redis加入环境变量

```
vim /etc/profile 
export PATH="$PATH:/usr/local/redis/bin"

source /etc/profile 
```

### 测试启动redis客户端
```
redis-cli
```
