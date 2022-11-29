---
layout: post
title: "Linux一些网络相关的设置"
subtitle: "Some Internet related settings on Linux system"
author: "Eric"
header-style: text
tags:
  - Linux
  - Internet
  - Shell
---





一些Linux上网络相关的配置, 关于配置静态ip部分目前还有很多没学习完善的地方, 等以后完善.





## 1. 配置网络



#### 1. 重启网卡 ubuntu 22.04

```shell
sudo /etc/init.d/networking restart
sudo systemctl restart networking
```


#### 2. linux命令行配置静态ip地址

参考

```shell
https://blog.csdn.net/xun527/article/details/112222131
```





## 2. 配置无线路由



开发板的linux默认是无法同时开启热点和wifi, 需要桥接wifi就很麻烦. 但是通过虚拟一个网卡来开启热点就可以做到.

```shell
sudo iw dev wlan0 interface add wlan1 type __ap
sudo ip link set dev wlan1 address 22:33:44:55:66:00
sudo nohup create_ap -c 11 wlan1 eth0 SSID password --no-virt &
```





### 详细教程



#### 1. 换源

```shell
sudo sed -i "s@http://\(deb\|security\).debian.org@https://mirrors.ustc.edu.cn@g" /etc/apt/sources.list
sudo apt update && sudo apt upgrade -y
```



#### 2. 安装软件和依赖

```shell
sudo apt-get install hostapd -y
```



#### 3. 安装和配置

```shell
cd /home/firefly/wifi/create_ap
sudo make install

cd ..
sudo cp openwifi /usr/bin/
sudo cp wifi.desktop /etc/xdg/autostart/
sudo cp wifi.desktop /usr/share/applications/

# 3566 需要 --no-virt
# 3568 不需要
```



工具地址

```shell
https://github.com/eric0202/linux-router
```

