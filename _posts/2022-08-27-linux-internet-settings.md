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



##  说明



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



