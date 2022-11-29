---
layout: post
title: "ADB的一些常用操作"
subtitle: "Common usages of adb"
author: "Eric"
header-style: text
tags:
  - Android(安卓)
  - Linux
  - ADB
  - Shell
---





做了这么久的`Linux开发板`, 各种连接方式都试过了, 从`HDMI`屏幕接键盘, `ssh`, `telnet`, `serial port` `串口`, `adb`, 这么长时间的经验总结 - 应用层开发最好用的还是`ssh`和`adb`





## 常见使用



### 1. 连接



查看设备

```shell
adb devices
```



如果只有一个设备, 则可以直接进入终端

```
adb shell
```



如果devices有多个设备, 则需要指定序列号

```
adb -s 27a6f798a9aba056 shell
```



scrcpy同理





### 2. 传输



adb的传输是不需要指定 -r 递归的 , 也不需要ip地址, 指定双方路径即可, pull和push相同



```
adb push ./a.txt /home/firefly/Desktop/
```

```
adb pull /home/firefly/Desktop ./
```





### 3. 命令



可以直接在主机执行命令

```
adb shell reboot
```



安装软件

```
adb install *.apk
```



查看包名和路径

```
adb shell pm list packages -f
```



如果不需要看路径, 去掉 -f参数

```
adb shell pm list packages
```



用包名卸载软件

```
adb uninstall com.android.chrome
```

