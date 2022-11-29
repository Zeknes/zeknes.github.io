---
layout: post
title: "Linux远程桌面协议"
subtitle: "Linux remote desktop protocols"
author: "Eric"
header-style: text
tags:
  - Linux
  - 远程桌面
  - Shell
  - SPICE
  - VNC
  - RDP
---



简单说一下远程桌面相关的一些小细节, 毕竟工作做了这么久远程桌面, 真的要详细说的话, 几天都说不完. 



### 1. RDP



服务端

```shell
 sudo apt install -y xserver-xorg xrdp xorg dbus-x11 x11-xserver-utils
```



`debian`允许root用户ssh登陆

```shell
/etc/ssh/sshd_config

PermitRootLogin yes
```



重启服务

```shell
/etc/init.d/ssh restart
```



指定rdp连接的桌面

以`xfce`为例

```shell
echo xfce4-session >~/.xsession 
以及在  vi /etc/X11/Xsession 
首行添加 xfce4-session
```





### 2. VNC



vnc几乎只适用于调试, 没有usb重定向, 画面质量也很低, 但是方便网页登陆.

```shell
sudo apt isntall tigervnc* -y

vncserver :n
```



`iptables`

```shell
iptables -I INPUT -p tcp --dport 3389 -j ACCEPT
echo 'iptables -I INPUT -p tcp --dport 3389 -j ACCEPT' >> /etc/profile
```





### 3. SPICE



编译报错xorg-macros 1.3

```python
sudo apt-get install xutils-dev
```



x11spice位置

```python
https://gitlab.freedesktop.org/spice/x11spice
```
