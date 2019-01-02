---
layout: post
title:  博客搭建系列:阿里云免费ssl证书配置网站https
category: PHP 
tags: [PHP]
---

针对网站升级https，整理了下http和https的一些分析，以及配置方法整理

## 目录

### HTTP与HTTPS是什么？

   HTTP协议（超文本传输协议）是互联网上应用最为广泛的一种网络协议，常被用于在web浏览器和网站服务器之间传递信息，http协议传输数据是以明文方式进行传送，如果中途被截获，就可以读取其中的信息。还记得之前公司某一台医疗设备的登录界面被截获，页面上都是广告的情况。
    为了解决HTTP协议的这一缺陷，就延伸出HTTPS协议 （安全套接字层超文本传输协议），HTTPS在HTTP的基础上加入了SSL协议，SSL依靠证书来验证服务器的身份，为web浏览器和服务器之间的通信数据进行加密。

HTTPS协议的主要作用分为两种：
1. 建立一个信息安全通道，来保证数据传输的安全
2. 确认网站的真实性。

### HTTP与HTTPS有什么区别

1. https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
2. http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
3. http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
4. http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比


### 证书申请配置流程

1. 阿里云或者腾讯云都有免费的证书，我的域名备案服务商是阿里云，所有我也就用阿里云了
2. 登录阿里云->阿里云控制台->产品与服务->SSL证书（应用安全）->购买证书
3. 购买证书->选择品牌（ Symantec）->证书类型选择（免费型DV SSL）->立刻购买
4. 证书申请录入需要的信息-> 证书绑定域名，所在地等
5. 上传：系统生成 CSR，点一下 创建。
6. 完成上述后，提交审核即可
7. 如果正常，一般1，2个小时，证书就会审核通过

如果一切正常，10 分钟左右，申请的证书就会审核通过。

![image](https://static.studytime.xin/image/articles/https-config-1.jpg)

证书申请是需要验证域名的，阿里云ssl证书的认证提供了三种
- 自动 DNS 验证，此时你需要在域名解析记录中，增加一条TXT记录，证明域名是你的，阿里云的域名的话就是自动增加一条认证记录
- 手工DNS验证，需要自行在域名解析记录中，增加一条TXT记录，进行域名认证
- 文件认证，按照说明下载认证文件传入服务器中，域名指向目录下，进行访问即可。

证书申请需要填写的信息:

![image](https://static.studytime.xin/image/articles/https-config-2.jpg)

在域名的管理里，因为我用了阿里云的 DNS 解析服务，所以会自动添加一条 CNAME 记录，这条记录就是验证域名所有权用的：

![image](https://static.studytime.xin/image/articles/https-config-3.png)

### 下载证书

在阿里云证书管理目录中，如果申请的证书通过审核后，就可以进行下载。可以选择下载不同的类型，常见的有nginx,以及apach等服务器。根据自己网站的类型，选择下载对应的证书。上传到服务器目录中，解压后会获取到两个文件，文件后缀分别是 *.key，和 *.pem。

![image](https://static.studytime.xin/image/articles/https-config-4.png)


### 配置 NGINX 的 HTTPS

证书上传服务器后，就需要去配置服务器。不同的服务器配置不一样。因为我的项目运行环境是lnmp环境下。我的配置方法就是用的是nginx类型的。我的域名是 www.studytime.xin.

### 下载并上传证书实例

登录服务器`ssh AliCloud`

创建一个存储证书的目录:
`sudo mkdir -p /usr/local/nginx/ssl/studytime`

把申请并下载下来的证书，上传到上面创建的目录的下面。

```
scp local_path root@ip:/usr/local/nginx/ssl/studytime

证书上传后的路径:
/usr/local/nginx/ssl/studytime/1650160_studytime.xin.key
/usr/local/nginx/ssl/studytime/1650160_studytime.xin.pem
```

### 配置NGINX文件

可以允许网站同时支持http以及https。http使用的默认断后是80,https使用的默认顿口是443。
针对这个问题需要特殊说明下，服务器必须开启响应的80和443端口给外网。一般服务器的安全组是默认开启的，若没有开启可以自行搜索处理，centos7怎么开启80和443端口等。

下面是一个基本的监听 443 端口，使用了 SSL 证书的 NGINX 配置文件，创建一个配置文件：

`touch /usr/local/nginx/conf/vhosts/studytime.conf`

把下面的代码粘贴进去：

```
server {
  listen       443;
  server_name  www.studytime.xin;
  ssl          on;
  root /data/wwwroot/blog;
  index index.html;
  ssl_certificate  /usr/local/nginx/ssl/studytime/1650160_studytime.xin.pem;
  ssl_certificate_key  /usr/local/nginx/ssl/studytime/1650160_studytime.xin.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
  ssl_prefer_server_ciphers on;
}
```

上面的配置里，ssl_certificate 与 ssl_certificate_key 这两个指令指定使用了两个文件，就是你下载的证书，解压之后看到的那两个文件，一个是 *.pem，一个是 *.key。你要把这两个文件上传到服务器上的某个目录的下面。

重新加载 NGINX 服务：
```
sudo nginx -s reload
```


### 验证配置

在浏览器上输入带 https 的网站地址：[https://studytime.xin](https://studytime.xin/)

如果正确的配置了让服务器使用 SSL 证书，会在地址栏上显示一个绿色的小锁头图标。

点开那个小锁头，会显示安全连接，再打开 详细信息。

NGINX 配置使用 301 重定向,会让对 HTTP 网页的请求，重定向到 HTTPS 版本的网页上。
：

```
server {
  listen        80;
  server_name   studytime.xin www.studytime.xin;
  return 301    https://$host$request_uri;
}
```



