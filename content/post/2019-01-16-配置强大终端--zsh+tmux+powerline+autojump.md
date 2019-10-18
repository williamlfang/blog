---
title: "配置强大终端: zsh+tmux+powerline+autojump"
author: William
date: 2019-01-16
lastmod: 2019-01-16
categories: [Programming]
tags: [Linux,terminal,zsh,tmux,powerline,autojump]
description: 使用 zsh+tmux+powerline+autojump 组合，通过合理配置，打造强大的操作终端
draft: false
ToC: true
---

对于经常在远程服务器进行编程的程序员，终端(`terminal`)无疑是我们接触最多的接口了。通过终端，我们可以直接使用命令来操作服务器系统，诸如修改文件、编辑程序、执行命令、传输文件等。通过配置合理的插件，我们可以打造出一款功能强大、符合个人使用习惯的「终端机」。

这篇文件主要介绍几个强大的插件工具，并通过自定义配置文件，实现以上目标。

<!--more-->

# zsh:不仅是shell

```bash
yum install zsh

## 更改默认 shell
chsh -s /bin/zsh trader

echo $SHELL
/usr/bin/zsh
```


# tmux:终端组合

```bash
yum install tmux
```


# powerline:多点信息

[tmux-powerline](https://github.com/erikw/tmux-powerline)

## install

```bash
pip install --user git+git://github.com/powerline/powerline

cd ~/Documents
git clone https://github.com/erikw/tmux-powerline.git
```

## `~/.tmux.conf`

```bash
## =============================================================================
## https://github.com/erikw/tmux-powerline
set-option -g status on
set-option -g status-interval 2
set-option -g status-justify "centre"
set-option -g status-left-length 60
set-option -g status-right-length 150
set-option -g status-left "#(~/Documents/tmux-powerline/powerline.sh left)"
set-option -g status-right "#(~/Documents/tmux-powerline/powerline.sh right)"
set-window-option -g window-status-current-format "#[fg=colour235, bg=colour27]⮀#[fg=colour255, bg=colour27] #I ⮁ #W #[fg=colour27, bg=colour235]⮀"
## =============================================================================
```

# autojump:快速跳转

## 安装

可以使用 `apt` 直接安装稳定发行版

```bash
# 如果提示找不到软件，需要 `update` 一下。
sudo apt update
sudo apt install autojump
```

## 配置

安装成功后，可以找到说明文档进行配置

```bash
vim /usr/share/doc/autojump/README.Debian                                  
Autojump for Debian                                                                                   
-------------------

To use autojump, you need to configure you shell to source
/usr/share/autojump/autojump.sh on startup.

If you use Bash, add the following line to your ~/.bashrc (for non-login
interactive shells) and your ~/.bash_profile (for login shells):
. /usr/share/autojump/autojump.sh

If you use Zsh, add the following line to your ~/.zshrc (for all interactive shells):
. /usr/share/autojump/autojump.sh

-- Tanguy Ortolo <tanguy+debian@ortolo.eu> Tue, 10 Aug 2010 07:55:16 +0200
```

由于我使用 `zsh`，因此需要把 `. /usr/share/autojump/autojump.sh` 放到 `~/.zshrc` 文件

```bash
echo ". /usr/share/autojump/autojump.sh" >> ~/.zshrc

## -------------------------------------------------
## 如果使用 Mac Bash，则在 ~/.bash_profile 添加
echo "source $(brew --prefix)/etc/profile.d/autojump.sh" >> ~/.bash_profile
## 如果使用 Mac Zsh， 则在 ~/.zshrc 最后添加一行
echo "[[ -s $(brew --prefix)/etc/profile.d/autojump.sh ]] && . $(brew --prefix)/etc/profile.d/autojump.sh" >> ~/.zshrc

## -------------------------------------------------
## 如果使用 CentOS, 需要
echo "/usr/share/autojump/autojump.bash" >> ~/.bashrc
## 如果有提示：/usr/share/autojump/autojump.bash: 权限不够
## 那么需要给autojump.bash开权限
## chmod 777 /usr/share/autojump/autojump.bash
```

这样，我们便可以使用命令 `autojump` 进行快速的文件目录跳转。当然，也可以使用短命令 `j` 进行操作，并配合 `tab` 键快速的筛选。

```bash
j bl__                                                                              │
bl__1__/home/william/Documents/blog               bl__4__/home/william/Documents/blog/content/post
bl__2__/home/william/Documents/blog               bl__5__/home/william/Documents/blog/content           │
bl__3__/home/william/Documents/blog
```

