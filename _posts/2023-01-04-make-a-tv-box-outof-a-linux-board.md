---
layout: post
title: "将Linux开发版用作电视盒子"
subtitle: "Make a tv box out of a Linux board"
author: "Eric"
header-style: text
tags:
  - Linux
  - HDMI
  - PulseAudio
  - Shell
---





整了一个开发版, 准备偶尔用作`Linux电视盒子`, 结果发现每次开机都要修改音频输出通道, 不然的话默认识别为蜂鸣器输出音频, 就很反人类. 



### 1. 播放环境



开发版默认是`xfce4` , 用于4k显示和播放视频都很吃力. 首先要换为 `gnome`桌面.



```shell
sudo apt install -y gnome vlc smplayer
```

```shell
sudo dpkg-reconfigure lightdm
```

```shell
cd /etc/lightdm/lightdm.conf.d/
# sudo vim 修改配置文件
```





### 2. 修改音频输出



列出可用的音频输出列表

```shell
pacmd list-sinks
```

选择`name`, 如:

```shell
name: <alsa_output.platform-hdmi0-sound.HDMI__hw_rockchiphdmi0__sink>
```

需要的值为

```shell
alsa_output.platform-hdmi0-sound.HDMI__hw_rockchiphdmi0__sink
```



将HDMI设置为默认

```shell
pacmd set-default-sink alsa_output.platform-hdmi0-sound.HDMI__hw_rockchiphdmi0__sink
```

