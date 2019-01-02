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

### 申请证书

1.  登录：阿里云控制台，产品与服务，证书服务，购买证书。
2.  购买：证书类型选择 免费型DV SSL，然后完成购买。
3.  补全：在 我的证书 控制台，找到购买的证书，在操作栏里选择 补全。填写证书相关信息。
4.  域名验证：可以选择 DNS，如果域名用了阿里云的 DNS 服务，再勾选一下 证书绑定的域名在 阿里云的云解析。
5.  上传：系统生成 CSR，点一下 创建。
6.  提交审核。

如果一切正常，10 分钟左右，申请的证书就会审核通过。

![image](https://static.studytime.xin/image/articles/https-config-1.jpg)

申请证书要注意的是验证域名，就是你要验证你想绑定证书的域名是你自己的，如果选择使用 DNS 验证，你需要在域名的管理里，添加一条特定的 DNS 记录，这样就可以证名这个域名是你自己的。使用了阿里云的云解析服务，这个步骤可以自动完成，会自动为你添加一条 DNS 验证的记录。

证书申请补填需要的信息:

![image](https://static.studytime.xin/image/articles/https-config-2.jpg)

在域名的管理里，因为我用了阿里云的 DNS 解析服务，所以会自动添加一条 CNAME 记录，这条记录就是验证域名所有权用的：

![image](https://static.studytime.xin/image/articles/https-config-3.png)

### 下载证书

在阿里云的证书管理那里，如果申请的证书审核通过，你就可以下载了，点击 下载，可以选择不同的类型，可以选择 NGINX，或 Apache 之类的服务器。根据自己网站的 Web 服务器类型，下载对应的证书。解压以后，你会得到两个文件一个是 *.key，一个是 *.pem。

![image](https://static.studytime.xin/image/articles/https-config-4.png)


### 配置 NGINX 的 HTTPS

有了证书，就可以去配置 Web 服务器去使用这个证书了，不同的 Web 服务器地配置方法都不太一样。下面用 NGINX 服务器作为演示。我的域名是 studytime.xin，出现这个文字的地方你可以根据自己的实际情况去替换一下。


### 下载并上传证书

创建一个存储证书的目录:

`sudo mkdir -p /usr/local/nginx/ssl/studytime`

把申请并下载下来的证书，上传到上面创建的目录的下面。

```
scp local_path root@ip:/usr/local/nginx/ssl/studytime

证书上传后的路径:
/usr/local/nginx/ssl/studytime/1650160_studytime.xin.key
/usr/local/nginx/ssl/studytime/1650160_studytime.xin.pem

```

### NGINX 配置文件

你的网站可以同时支持 HTTP 与 HTTPS，HTTP 默认的端口号是 80，HTTPS 的默认端口号是 443。也就是如果你的网站要使用 HTTPS，你需要配置网站服务器，让它监听 443 端口，就是用户使用 HTTPS 发出的请求。

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



