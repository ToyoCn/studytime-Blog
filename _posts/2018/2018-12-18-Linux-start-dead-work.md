---
layout: post
title: 阿里云Ecs服务器配置常用命令
category: Linux 
tags: [Linux]
---

centos7阿里云Ecs配置小技巧

## 快速上手

### 延长SSH的连接超时时间

SSH登录连接服务器时,默认的连接超时时间很短，经常会断掉,为方便管理修改sshd的配置文件，然后重启sshd服务。

```
vim /etc/ssh/sshd_config;

#查找并修改
#ClientAliveInterval 0  ClientAliveInterval 120 服务端向客户端器请求消息的间隔
#ClientAliveCountMax 3  ClientAliveCountMax 10	服务端向客户端器请求无响应的次数，自动断开

#重启sshd服务使修改生效
systemctl restart sshd
```
---
### 解决ssh登录locale警告,中文乱码问题

`-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory`

```
vim /etc/environment;

LC_ALL=en_US.UTF-8
LANG=en_US.UTF-8

source /etc/environment;
```
mac 上用是iterm2终端, Shell 环境是zsh。ssh 到Linux 服务器上查看一些文件时，中文乱码。 
这种情况一般是终端和服务器的字符集不匹配，MacOSX下默认的是utf8字符集。

```
vim ~/.zshrc

export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8

source ~/.zshrc  重启终端
```
---

### 查看服务器系统信息
```
cat /etc/redhat-release
```

### 修改主机名字

```
#查看主机名
uname -a
#修改主机名字
hostnamectl set-hostname  application_server
```
### 添加管理员账户
root用户权限过高，一不小心的错误更改将会影响整个系统，所以我需要一个新的用户
```
adduser baihe           	//添加一个新用户，名字叫Sirius
passwd baihe           		//设置用户密码
gpasswd -a baihe wheel  	//给予sudo权限, 当权限不够时，可以用sudo
lid -g wheel             	//查询所有带sudo权限的用户
userdel -r baihe        	//删除用户和相应的目录
```

### 更换yum源为阿里云源
```
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

yum clean all

yum makecache
```

### ssh免密登录

1. 本地是否存在公钥，不存在安装下面创建公匙

打开item2终端,执行如下命令:
```
ssh-keygen -t rsa -C 'your email@domain.com’
-t 指定密钥类型，默认即 rsa ，可以省略
-C 设置注释文字，比如你的邮箱
```
会进行2次提示,文件名提示输入文件名,默认生成id_rsa,以及密码提示,默认为空,指定完成后会在,生成id_rsa私匙,以及id_rsa.pub公匙

cd ~/.ssh

2. 复制公匙到远程服务器存储
将上一步生成的公匙文件放入远程服务器目录中,查看远程服务器是否存在该目录,不存在进行创建目录.

登录远程服务器

```
ssh root@108.61.250.251   //输入密码登入服务器
vim ~/.ssh/authorized_keys  //切入该目录,不存在则会创建,此为root管理员,其他用户切换着对应的home家目录下对应的目录内新建.ssh/authorized_keys文件
chmod 755 .ssh/*  //给.ssh文件夹以及authorized_keys 755权限
打开本地电脑下的公匙,放入服务器目录中
vim ~/.ssh/id_rds.pub 
```
3. 设置快捷登录
#将username替换为你的ssh服务器用户名，hostname替换为服务器的ip 此时就不需要输入密码了
`ssh username@hostname`

为了更快的一键登录,ssh提供了一种方式,往~/.ssh/config里面添加配置信息就可

vim ~/.ssh/config
//添加以下文件
```
Host    alias #自定义别名
   HostName        hostname  #替换为你的ssh服务器ip或domain
   Port            port #ssh服务器端口，默认为22
   User            user #ssh服务器用户名
   IdentityFile    ~/.ssh/id_rsa #第一个步骤生成的公钥文件对应的私钥文件
```
保存文件退出,即可使用别名免密登录.
ssh alias;








