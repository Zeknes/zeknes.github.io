---
layout: post
title: "配置一块新的Orangepi开发板"
subtitle: "Setup a new orangepi board"
author: "Eric"
header-style: text
tags:
  - Linux
  - Orangepi
  - 开发板
---





其实最好的方式应该是在一块开发板上设置完成后导出系统然后编译为完整系统镜像, 但是个人电脑配置和储存实在是有点捉急, 那暂时就先记录下配置一块新的开发板的步骤吧. 





## 1. 替换国内源



这里用的是清华的源, 适用于 `ubuntu ports` 22.04. 其他版本的去 `tuna` 官网找就行了. 因为我最常用 `ubuntu` 22.04 版本 `Jammy` 所以这里只贴这个版本



```shell
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports/ jammy-security main restricted universe multiverse
```






## 2. 配置3D加速和必要软件



```shell
#!/bin/bash
sudo add-apt-repository ppa:liujianfeng1994/panfork-mesa
sudo add-apt-repository ppa:liujianfeng1994/rockchip-multimedia
sudo apt update
sudo apt upgrade -y

sudo apt install -y i3 smplayer chromium-browser vlc fish rofi

sudo apt dist-upgrade
```





## 3. 配置i3桌面和rofi等



#### 1. 总体配置

总体配置的详细方式过于麻烦, 用之前的文件复制进去就行了.



#### 2. 配置启动x服务器和开机服务



```shell
vim ~/.xinitrc
```

```shell
#!/bin/sh

# start some nice programs

if [ -d /etc/X11/xinit/xinitrc.d ] ; then
 for f in /etc/X11/xinit/xinitrc.d/?*.sh ; do
  [ -x "$f" ] && . "$f"
 done
 unset f
fi

exec i3
```



```shell
vim ~/.profile
```

```shell
[ $(tty) = "/dev/tty1" ] && cd ~ && startx
```





## 4. 配置wayland



下次继续
