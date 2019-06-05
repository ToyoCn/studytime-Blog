---
layout: post
title:  Sqoop知识梳理
category: bigdata 
tags: [bigdata]
---

Sqoop知识梳理




## Sqoop学习之路

### 一、概述

Sqoop (SQL to Hadoop) 是Apache顶级项⽬,官⽹地址：http://sqoop.apache.org.

为了高效的实现关系数据库与hadoop之间的数据导入导出，hadoop生态圈提供了工具sqoop.

核心的功能:

- 把关系型数据库的数据导入到 Hadoop 系统 ( 如 HDFS HBase 和 Hive) 中.
- 把数据从 Hadoop 系统里抽取并导出到关系型数据库里.

![](https://static.studytime.xin/image/articles/Sqoop/9BE2EC62-CEAC-4D14-805C-B3A0DCA1E0E2.png)

版本介绍:
Sqoop 2.0 主要解决 Sqoop 1.x 扩展难的问题，提出的 Server-Client 模型，具体用的不是特别多.
本文主要介绍的还是 Sqoop 1.x，最新的 Sqoop 版本是 1.4.7.

常用场景:
- 数据迁移:关系型数据库迁移至hadoop大数据平台中,进行大数据分析等，sql to hadoop
- 可视化分析结果存储，hadoop大数据分析后，统计结果导入关系型数据库。现有可视化工具与关系型数据库配合良好
- 数据增量导入


### 二、基本思想

采用插拔式Connector的架构，Connector是与特定数据源相关的组件，主要负责抽取和加载数据.

主要具备的特点:
- 性能高，Sqoop采用MapReduce完成数据的导入导出，具备了MapReduce所具有的优点，包括并发度可控，高容错性，高扩展性等.
- 自动类型转换，可读取数据源元信息，自动完成数据类型映射，当然用户也可自定义类型映射关系.
- 自动传播元信息，数据在数据发送端和数据接收端之间传递数据的同时，也会传递元信息，保证接收端和采集端元信息一致.


### 三、工作机制

Sqoop1是一个客户端工具，不需要启动任何服务就可以使用。是一个只有的Map的MapReduce作业，充分利用MapReduce的高容错行以及高扩展性的优点，将数据迁移任务转换为MapReduce来作业。


整体架构:
将导入或导出命令翻译成MapReduce程序来实现, 在翻译出的MapReduce中主要是对InputFormat和OutputFormat进行定制.

Sqoop1的整体架构图:
![](https://static.studytime.xin/image/articles/Sqoop/04222D63-C912-4F34-B1A4-3ED1F108ACE6.png)

工作流程简述:
- 客户端shell提交迁移作业
- Sqoop从关系型数据库中读取元信息
- 根据鬓发度和数据表大小将数据划分成若干分片，每片举哀给一个Map Task处理
- 多个Map Task同时读取数据库中数据，并行将数据写入目标存储系统中，比如（hdfs、Hbase、Hive等）

允许用户通过定制各种参数控制作业，包括任务并发度，数据数据源，目标数据源，超时时间等。

缺点整理:
- Connector定制麻烦
- 客户端软件繁多
- 安全问题


### 四、安装
#### 1、 前提概述

将来sqoop在使用的时候有可能会跟那些系统或者组件打交道？
HDFS， MapReduce， YARN， ZooKeeper， Hive， HBase， MySQL

#### 2、软件下载

下载地址http://mirrors.hust.edu.cn/apache/
![](https://static.studytime.xin/image/articles/Sqoop/1F5D9A30-B43B-4054-9043-E3D84172230F.png?x-oss-process=image/resize,m_fixed,h_400,w_800)

sqoop版本说明:
绝大部分企业所使用的sqoop的版本都是 sqoop1
sqoop-1.4.6 或者 sqoop-1.4.7 它是 sqoop1
sqoop-1.99.4----都是 sqoop2
此处使用sqoop-1.4.7版本sqoop-1.4.7.bin__hadoop-2.0.4-alpha.tar.gz

#### 3、安装步骤
（1）通过命令下载Sqoop，解压后，放到/software/ 目录中：

```
cd /data/pkg/
wget http://mirrors.shu.edu.cn/apache/sqoop/1.4.7/sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz
tar -zxvf sqoop-1.4.7.bin__hadoop-2.6.0.tar.gz -C /software/
cd /software/
mv sqoop-1.4.7.bin__hadoop-2.6.0/ sqoop-1.4.7
```

(2) 进入到 conf 文件夹，找到 sqoop-env-template.sh，修改其名称为 sqoop-env.sh cd conf

```
cd sqoop-1.4.7/conf/
mv sqoop-env-template.sh sqoop-env.sh
```

(3) 修改 sqoop-env.sh

```
#Set path to where bin/hadoop is available
export HADOOP_COMMON_HOME=/software/hadoop-2.7.3

#Set path to where hadoop-*-core.jar is available
export HADOOP_MAPRED_HOME=/software/hadoop-2.7.3

#set the path to where bin/hbase is available
export HBASE_HOME=/software/hbase-1.2.4

#Set the path to where bin/hive is available
export HIVE_HOME=/software/apache-hive-2.1.1-bin

#Set the path for where zookeper config dir is
export ZOOCFGDIR=/software/zookeeper-3.4.9
```

为什么在sqoop-env.sh 文件中会要求分别进行 common和mapreduce的配置呢？？？
> 在apache的hadoop的安装中；四大组件都是安装在同一个hadoop_home中的
> 但是在CDH, HDP中， 这些组件都是可选的。
> 在安装hadoop的时候，可以选择性的只安装HDFS或者YARN，
> CDH,HDP在安装hadoop的时候，会把HDFS和MapReduce有可能分别安装在不同的地方。

（4）加入 mysql 驱动包到 sqoop1.4.7/lib 目录下
```
# 下载 mysql connector
cd /data/pkg
wget --no-check-certificate  http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.32.tar.gz


# 该命令会在/data/pkg下产生mysql-connector-java-5.1.32.tar.gz
tar -zxvf mysql-connector-java-5.1.32.tar.gz -C /software

# 拷贝文件
cp /software/mysql-connector-java-5.1.32/mysql-connector-java-5.1.32.jar /software/sqoop-1.4.7/lib/
```

（5）配置系统环境变量
```

vim /etc/profile
# sqoop
export SQOOP_HOME=/software/sqoop-1.4.7
export PATH=$PATH:$SQOOP_HOME/bin
```

保存退出使其立即生效
```
source /etc/profile
```

(6) 验证安装是否成功
sqoop-version 或者 sqoop version
![](https://static.studytime.xin/image/articles/Sqoop/DA8C4F41-2360-4C57-881F-BFCDCF087890.png)

(7) 解决告警问题，修改配置文件 configure-sqoop
`vim bin/configure-sqoop`
注释截图中的内容，行数已标明
![](https://static.studytime.xin/image/articles/Sqoop/F466D8FD-3130-47DB-A5CF-3EBF3B388F85.png)

(8) 查看 Sqoop 版本
![](https://static.studytime.xin/image/articles/Sqoop/757D2AC9-BC47-4710-AEF5-984FE5A4CCA0.png)

#### 四、Sqoop的基本命令

1. 基本操作
可以使用 sqoop help 来查看，sqoop 支持哪些命令

```
[hadoop@bigdata hadoop]$ sqoop help
19/06/05 08:12:38 INFO sqoop.Sqoop: Running Sqoop version: 1.4.7
usage: sqoop COMMAND [ARGS]

Available commands:
  codegen            Generate code to interact with database records
  create-hive-table  Import a table definition into Hive
  eval               Evaluate a SQL statement and display the results
  export             Export an HDFS directory to a database table
  help               List available commands
  import             Import a table from a database to HDFS
  import-all-tables  Import tables from a database to HDFS
  import-mainframe   Import datasets from a mainframe server to HDFS
  job                Work with saved jobs
  list-databases     List available databases on a server
  list-tables        List available tables in a database
  merge              Merge results of incremental imports
  metastore          Run a standalone Sqoop metastore
  version            Display version information

See 'sqoop help COMMAND' for information on a specific command.
[hadoop@bigdata hadoop]$
```

特殊说明可以使用`sqoop help COMMAND`查看具体命令的用法

2. 列出所有的数据库
```
sqoop list-databases --connect jdbc:mysql://bigdata/ --username root --password baihe2019
```
![](https://static.studytime.xin/image/articles/Sqoop/00CC4C23-906E-447F-B841-7294CC2A5C74.png)


3. 列出所有的数据表

```
sqoop list-tables --connect jdbc:mysql://bigdata/bigdata --username root -P
```
![](https://static.studytime.xin/image/articles/Sqoop/7B1496D1-9768-4FC3-A395-30F1E62C3DFA.png)

#### 五、Sqoop练习

#####  import 基本操作

语法格式: `sqoop import (generic-args) (import-args)`

常用参数:

```
--connect &lt;jdbc-uri]]> jdbc 连接地址
--connection-manager &lt;class-name]]> 连接管理者
--driver &lt;class-name]]> 驱动类
--hadoop-mapred-home &lt;dir]]> $HADOOP_MAPRED_HOME
--help help 信息
-P 从命令行输入密码
--password &lt;password]]> 密码
--username &lt;username]]> 账号
--verbose 打印流程信息
--connection-param-file &lt;filename]]> 可选参数
```

案例1:
导出数据库bigdata中的doctor表到hadoop的/prod/data目录中
```
sqoop import --connect jdbc:mysql://bigdata:3306/bigdata --username root --password baihe2019 --table doctor -m 1 --target-dir /prod/data
```

导入：指定自定义查询SQL,导出数据库bigdata中的sql语句中查询的数据到hadoop的/prod/data目录中
```
sqoop import --connect jdbc:mysql://bigdata:3306/bigdata --username root --password baihe2019 -m 1 --query "select doctoruid from doctor where doctoruid > 52314 and \$CONDITIONS"  --target-dir /prod/data1
```



























