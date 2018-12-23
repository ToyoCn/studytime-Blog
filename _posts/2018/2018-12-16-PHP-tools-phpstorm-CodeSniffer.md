---
layout: post
title: PHPStorm IDE使用CodeSniffer进行代码规范化管理
category: tool 
tags: [tool]
---

PHP_CodeSniffer是一个优秀的代码风格检测工具,定义了一系列的代码规范。

> 通常使用官方的代码规范标准，比如PHP的PSR2,能够检测出不符合代码规范的代码并发出警告或报错(可设置报错等级),常被用作团队开发时维护编码风格以及标准。

## 快速上手


### 安装

- mac安装:
```
brew install php-code-sniffer
//检测安装是否成功
phpcs --h
//安装完成后的路径
/usr/local/Cellar/php-code-sniffer
```

### phpcs的配置

1. 查看详细配置。使用命令:phpcs --config-show

2. 设置默认的编码标准。（这个很重要，建议使用 PSR2 的标准)

```
# 查看配置
$ phpcs -i
The installed coding standards are MySource, PEAR, PHPCS, PSR1, PSR2, Squiz and Zend

# 设置编码标准为 PSR2
$ phpcs --config-set default_standard PSR2
```

3. 隐藏警告。（当然，对于强迫症来说，警告都是不允许的，非强迫症患者可以使用此配置项）

```
# 隐藏警告提醒
$ phpcs --config-set show_warnings 0
# 开启警告提醒
$ phpcs --config-set show_warnings 1
```

4. 显示检查进程。（如果项目需要检查的文件较多可以开启这个）

```
# 显示检查进程
$ phpcs --config-set show_progress 1
# 关闭进程显示
$ phpcs --config-set show_progress 0
```

5. 显示颜色

```
# 显示颜色
$ phpcs --config-set colors 1
# 关闭颜色显示
$ phpcs --config-set colors 0
```

6. 修改错误和警告等级

```
# 显示所有的错误和警告
$ phpcs --config-set severity 1
# 显示所有的错误，部分警告 注意等级可有从 5-8 5 的警告显示会更多，8 的更少
$ phpcs --config-set severity 1
$ phpcs --config-set warning_severity 5 
```

7. 设置默认编码

```
# 设置 utf-8
$ phpcs --config-set encoding utf-8
```

8. 设置 tab 的宽度

```
# tab 为 4 个空格
$ phpcs --config-set tab_width 4
# 也可以对单独文件生效
$ phpcs --tab-width=0 /path/to/code
```

9. 代码验证

```
# 校验单个文件
$ phpcs filename
＃ 校验目录 注意这个时候别因为 linux 学的太好加个 -R 哈。
$ phpcs /path/dir

```

### 代码规范检测,命令行使用

```
$ phpcs /home/www/init.php
FILE: /home/www/init.php
-------------------------------------------------------------
FOUND 2 ERROR(S) AFFECTING 2 LINE(S)
-------------------------------------------------------------
  1 | ERROR | Extra newline found after the open tag
 13 | ERROR | Missing function doc comment
-------------------------------------------------------------

```

### 设置PHPStorm整合CodeSniffer

1. **配置 Code Sniffer**

---
在 “Preferences”->“Languages & Frameworks”->“PHP”->“Quality Tools” ->“Code Sniffer” 配置中，“Configuration” 项后点击...并输入 phpcs 路径，可以使用 “Validate” 按钮验证phpcs路径是否正确。

![image](http://static.studytime.xin/image/articles/php_code_snaffer.jpg)

![image](http://static.studytime.xin/image/articles/php-code_snaffer2.jpg)

2. **开启验证**

---
在 “Preferences”->“Editor”->“Inspections”->“Quality Tools”配置中，勾选上 “PHP Code Sniffer validation”。

具体参数中,
Show warnings as: Warnning，标示提示级别
Coding standard PSR2 代表执行的规范如果找不到这个选项，点一下紧挨着的刷新按钮。

![image](http://static.studytime.xin/image/articles/php-code-snaffer3.jpg)










