---
layout: post
title:  Hbase伪分布式搭建
category: Hbase 
tags: [Hbase]
---

Hbase伪分布式搭建

### 搭建说明

#### Hbase部署架构图

![](https://static.studytime.xin/image/articles/hbase/hbase-install-1.png?x-oss-process=image/resize,m_fixed,h_500,w_1000)

#### 下载安装包
上传下载zookeeper-3.4.9.tar.gz、hbase-1.2.4-bin.tar.gz安装包到`/data/pkg`目录下

#### 解压缩安装包到文件目录

```
cd /data/pkg 
tar zxvf zookeeper-3.4.9.tar.gz -C /software/
tar -zxvf hbase-1.2.4-bin.tar.gz -C /software/
```

#### 安装zookeeper
```
cd /software/zookeeper-3.4.9/
cp conf/zoo_sample.cfg conf/zoo.cfg
vim conf/zoo.cfg

### 修改配置文件
# 修改数据目录
dataDir=/data/zookeeper
# 取消注释
autopurge.snapRetainCount=5
autopurge.purgeInterval=1
```

#### 启动zookeeper
`bin/zkServer.sh start`

#### 验证启动zookeeper成功
```
jps | grep Quorum
46309 QuorumPeerMain 代表成功
```


#### 安装Hbase伪分布式
进入安装包目录`cd /software/hbase-1.2.4/`

##### 1. 修改配置文件
编辑`conf/hbase-env.sh`文件
```
# 修改java环境变量
export JAVA_HOME=/usr/jdk1.8.0_102/
# 关闭zookeeper默认的zookeeper
export HBASE_MANAGES_ZK=false
注释掉
#export HBASE_MASTER_OPTS="$HBASE_MASTER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"
#export HBASE_REGIONSERVER_OPTS="$HBASE_REGIONSERVER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"
```

若不注释会出现截图中的问题,[参照问题说明](https://www.cnblogs.com/ThinkVenus/p/8042743.html)

![](https://static.studytime.xin/image/articles/hbase/hbase-install-2.jpg)


##### 2. 编辑conf/hbase-site.xml文件
```
<configuration>
  <property>
      <name>hbase.cluster.distributed</name>
      <value>true</value>
   </property>
   <property>
      <name>hbase.rootdir</name>
      <value>hdfs://bigdata:9000/hbase</value>
   </property>
   <property>
      <name>hbase.zookeeper.quorum</name>
      <value>bigdata</value>
   </property>
</configuration>
```

##### 3. 编辑conf/regionservers文件
增加一行`bigdata`
该文件表示在哪些主机上启动RegionServers，每一行表示一个主机名，执行命令的时候需要这些机器上的SSH登陆权限.


#### 重启hdfs
```
stop-dfs.sh
start-df.sh
```

#### 启动HBase
`bin/start-hbase.sh`

#### 查看是否启动成功
`http://bigdata:16010/master-status`


#### 特殊说明
bigdata 代表主机名










