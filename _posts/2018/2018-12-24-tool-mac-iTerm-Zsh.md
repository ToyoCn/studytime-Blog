---
layout: post
title: 工具篇:iTerm与Zsh使用整理
category: tool 
tags: [tool]
---

本章主要对mac常用的控制台管理工具的安装与使用进行整理。



## iTerm与Zsh篇

### iTerm2 安装与配置

安装iTerm2比较简单，直接从官网下载安装即可。安装好之后，我们还需要进行一系列的设置才行。

### 主题配置

iTerm2支持许多的主题配色，可以自己定义，也可以参考网上现成的主题配色。我个人比较喜欢draculatheme配色。支持item，vim，phpstorm , 下方存在主题官网路径，按照教程安装即可。

下面是一些常用的主题配色的预设置文件：

iTerm2 dracula 配色: https://draculatheme.com/iterm/
iTerm2 Solarized 配色: https://github.com/altercation/solarized
iTerm2 配色合集网站: http://iterm2colorschemes.com/
iTerm2 配色合集GitHub地址：https://github.com/mbadolato/iTerm2-Color-Schemes/tree/master/schemes
这些配色预设置文件，可以直接导入到iTerm2中，然后可以直接在设置中选择：

### 其他配置

区分目录和文件的颜色设置:

Preferences -> Profiles -> Text -> Text Rendering 把 Draw bold text in bright colors 前面的勾去掉，
文件和目录可以很容易区分了……

### 命令别名
通过在.zshrc中配置alias，可以方便的为其他的命令设置别名，这是个很不错的功能.
`vim ~/.zshrc`

```angular2
# For git
alias gs="git status"
alias ga='git add'
alias gd='git diff'
alias gf='git fetch'
alias grv='git remote -v'
alias gbr='git branch'
alias gpl="git pull"
alias gps="git push"
alias gco="git checkout"
alias gl="git log"
alias gc="git commit -m"
alias gm="git merge"

# For local
alias cd..="cd .."
alias cd...="cd ../.."
alias cd....="cd ../../.."
alias ..="cd .."
alias ...="cd ../.."
alias ....="cd ../../.."
alias ip="curl ip.cn"
```

`source ~/.zshrc`

### 快速跳转
Zsh支持目录的快速跳转，我们可以使用 d 这个命令，列出最近访问过的各个目录，然后选择目录前面的数字进行快速跳转

### 增加指令高亮效果

```
切入扩展目录
cd ~/.oh-my-zsh/custom/plugins
执行指令将工程克隆到当前目录
git clone git://github.com/zsh-users/zsh-syntax-highlighting.git

打开`.zshrc`文件，在最后添加下面内容
vim  ~/.zshrc
添加代码
source ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

plugins=(zsh-syntax-highlighting)
保存文件。
执行
source ~/.zshrc
```

### 自动提示命令

```
切入扩展目录
cd ~/.oh-my-zsh/custom/plugins

执行指令将工程克隆到当前目录
git clone git://github.com/zsh-users/zsh-autosuggestions

打开.zshrc文件，在最后添加下面内容
~/.oh-my-zsh/custom/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
plugins=(zsh-autosuggestions)
保存文件。

cd ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions

vim zsh-autosuggestions.zsh 

修改 ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=10' 

source ~/.zshrc
```