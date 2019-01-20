---
layout: post
title:  博客搭建系列:jekyll部署GitHub Pages
category: Blog 
tags: [Blog]

---

jekyll部署GitHub Pages 搭建个人博客


![](https://static.studytime.xin/image/articles/Blog/Blog.png?x-oss-process=image/resize,m_fixed,h_500,w_1000)

## 目录

### 基本介绍

从事互联网行业日久后，技术渐长，总想着写写，把自己回，让别人更多的看到自己。个人是这样想的哈哈。
之前曾经自己使用laravel自己写了个项目，不了了之值。
当时的感觉就会耗时不说，最重要的感觉上班在写，下班后继续写博客代码。失去了，做这件事情的本意（分享）。

其实现在搭建一个技术博客，非常简单。

常用的有下面几种：
- GitHub Pages + Jekyll
- GitHub Pages + Hexo
- wordpress
- 自行构建依托与java或者PHP，配合前端vue，h5等

### 什么是GitHub Pages

Github Pages 是面向用户、组织和项目开放的公共静态页面搭建托管服务，站点可以被免费托管在 Github 上，你可以选择使用 Github Pages 默认提供的域名 github.io 或者自定义域名来发布站点。Github Pages 支持 自动利用 Jekyll 生成站点，也同样支持纯 HTML 文档，将你的 Jekyll 站点托管在 Github Pages 上是一个不错的选择。

### 什么是jekyll

jekyll是一个简单的免费的Blog生成工具，类似WordPress。但是和WordPress又有很大的不同，原因是jekyll只是一个生成静态网页的工具，不需要数据库支持。但是可以配合第三方服务,例如Disqus。最关键的是jekyll可以免费部署在Github上，而且可以绑定自己的域名。

### 个人博客搭建

整体流程:
- 选择一个jekyll主题，fork到自己的github仓库内
- 使用gitHub的GitHub Pages，配置项目
- 设置 GitHub Pages
- 删除原有博客中内容，编写自由内容

### 步骤详解

#### 1. fork项目
以我个人项目为例，打给地址`https://github.com/MyStudytime/studytime-Blog`，点击`Fork`按钮复制代码到自己的仓库。大概1分钟左右可以完成。
![](https://static.studytime.xin/image/articles/Blog/Blog-5.png)

#### 2. 删除 CNAME 文件
删除项目中的 CNAME 文件，CNAME 是定制域名的时候使用的内容，如果不使用定制域名会存在冲突。

#### 3. 设置 GitHub Pages
点击`Settings`按钮打开设置页面，页面往下拉到GitHub Pages相关设置，在`Source`下面的复选框中选择`master branch`，然后点击旁边的`Save`按钮保存设置。

#### 4. 重命名项目
点击`Settings`按钮打开设置页面，重命名项目名称为：YourGithubUserName.github.io
![](https://static.studytime.xin/image/articles/Blog/Blog-3.png)

#### 5. 重命名之后，再次回到 Settings > GitHub Pages 页面
会发现存在这样一个地址： https://YourGithubUserName.github.io

特别注意此处完成后，虽然也可以打开页面，但是跳转页面还是我的页面，需要修改jekyll配置文件。

#### 6. 配置 _config.yml
打开项目目录下的 _config.yml 文件，修改以下配置：

```
repository: MyStudytime/studytime-Blog  自己的仓库地址
github_url: https://github.com/MyStudytime   自己的github地址
url: https://www.studytime.xin  上面设置的https://YourGithubUserName.github.io
```

完成上述配置后，大概等待一二分钟后，可访问`https://YourGithubUserName.github.io`即可打开网站地址。

#### 7. 配置自定义域名
完成上述配置后，虽然`https://YourGithubUserName.github.io`也可以打开博客。但是对于崇尚个性的程序员难免会，想着配置自己的域名。
对于怎么域名以及备案这些，就不再此，再述了。

- 首页需要进行域名解析，我的域名是阿里云域名，我就在阿里云上进行域名解析了。
![](https://static.studytime.xin/image/articles/Blog/Blog-7.png)

- 然后重新打开项目的`Settings` > `GitHub Pages`页面，`Custom domain` 下的输入框输入刚才设置的域名：`blog.studytime.xin`，点击保存即可。
- 重新配置 _config.yml,打开项目目录下的 _config.yml 文件，修改配置成`url: http://blog.studytime.xin`
![](https://static.studytime.xin/image/articles/Blog/Blog-8.png)

- 等待一分钟之后，浏览器访问地址：`blog.studytime.xin` 即可访问博客。









