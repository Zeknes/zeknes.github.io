---
layout: post
title: "Linux相关的各项优化和修改"
subtitle: "Linux system tweaks and optimization"
author: "Eric"
header-style: text
tags:
  - Linux
  - autostart
  - tweaks
  - rdp
  - display
---





### 1. 自启

##### 启动文件和目录

```shell
./.profile
./.bashrc
/etc/rc.local
/etc/profile.d/
/etc/init.d/
/etc/profile
/etc/profile.d/
https://blog.csdn.net/qq_35440678/article/details/80489102
```

##### 终端美化

```shell
https://blog.csdn.net/m0_46322376/article/details/105761285
```

##### guake乱码

```shell
export LC_ALL=en_US.UTF-8  
export LANG=en_US.UTF-8
```

##### melo

```shell
git clone https://github.com/abertsch/Menlo-for-Powerline.git 
 
cd Menlo-for-Powerline
 
sudo mv "Menlo for Powerline.ttf" /usr/share/fonts/ 
 
sudo fc-cache -vf /usr/share/fonts/
```

##### 判断wayland

```shell
echo $XDG_SESSION_TYPE
echo $WAYLAND_DISPLAY
```

##### ** tty自动登录自动任务 **

```shell
在你的bashrc或者zshrc里加
# 如果当前在tty1自动开启startx
【 $(tty) = "/dev/tty1" 】 && cd ~ && startx

然后tty免密登录有点点复杂

 vim /etc/systemd/system/getty.target.wants/getty@tty1.service
把ExecStart改成
ExecStart=-/sbin/agetty -o '-p -f 用户名' -n -a 用户名 --noclear %I $TERM
```

##### 更换tty

```shell
chvt 1-7
```

##### 控制log

```python
sudo vim /etc/rsyslog.d/50-default.conf
sudo service rsyslog restart
sudo service cron restart
```

##### vim配置

```python
/etc/vim/vimrc
```

##### 校准时间

```shell
sudo apt install ntpdate
sudo ntpdate ntp1.aliyun.com
```



### 2.  安装mingw

```shell
git clone https://github.com/Zeranoe/mingw-w64-build
cd mingw-w64-build
mkdir src
mkdir bld
sudo apt install bison flex subversion texinfo
./mingw-w64-build x86_64
```



### 3. 远程连接rdp

远程机：

```shell
sudo apt install xrdp
sudo vim /etc/xrdp/xrdp.ini

sudo ufw allow 3389
```

本机：

```shell
sudo apt install freerdp2-x11
xfreerdp /v:192.168.57.230 /u:firefly /p:firefly /f
# f fullscreen        
```

参考：

```html
https://loneking.cn/server/434
```

重启：

```shell
sudo  /etc/init.d/xrdp  restart
```



### 4. 桌面相关

查询自己是x11还是wayland

```shell
echo $XDG_SESSION_TYPE
```

修改显示管理器

```shell
sudo dpkg-reconfigure lightdm
```



### 5. 免去sudo输入密码

```shell
sudo visudo
```

```shell
原文
%sudo   ALL=(ALL:ALL) ALL

更改为
%sudo   ALL=(ALL:ALL) NOPASSWD: ALL
```



### 6. 默认启动模式

设置为图形界面模式

```shell
systemctl set-default graphical.target
```

设置为命令行模式

```shell
systemctl set-default multi-user.target
```



### 7. 向另一个tty传输命令



```shell
exec command > /dev/tty2
```

```shell
setsid sh -c 'exec command <> /dev/tty2 >&0 2>&1'
```



### 8. 文件管理

##### 保护文件（夹）不被sudo删除

```shell
sudo chattr +i -R file/folder

sudo chattr -i -R file/folder
```



### 9. 默认bash增加颜色

```python
echo 'PS1="\[\e[01;32m\]\h \[\e[01;35m\]\A \[\e[01;34m\](\w) \[\e[01;36m\]\\$ \[\e[0m\]"' >> ~/.bashrc
```



### 10. 安装时选择了中文安装

```python
# This file is written by xdg-user-dirs-update
# If you want to change or add directories, just edit the line you're
# interested in. All local changes will be retained on the next run.
# Format is XDG_xxx_DIR="$HOME/yyy", where yyy is a shell-escaped
# homedir-relative path, or XDG_xxx_DIR="/yyy", where /yyy is an
# absolute path. No other format is supported.
# location .config/user-dirs.dirs
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Downloads"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"
```



### 11. 自动安装驱动

```python
ubuntu-drivers autoinstall
```



### 12. 查看和踢出用户

查看用户

```python
w
```

踢出用户

```python
pkill -t <终端>
pkill <进程名>
```

##### 1. 查看用户登录记录

```python
last
last -F
```

##### 2. 用户登录日志文件

```python
/var/log/wtmp
```



### 13. minimum中文显示

```shell
sudo apt install -y locales ttf-wqy-zenhei
```



### 14. 编译.autogen.sh

```shell
sudo apt install automake autopoint
```



### 15. 解决linux终端无法退格的问题

```shell
sudo apt remove ncurses-base
sudo apt install ncurses-base
```