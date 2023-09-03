---
layout: post
title: "Linux笔记2"
subtitle: "Linux notes2"
author: "Eric"
header-style: text
tags:
  - Ubuntu
  - Linux
---





之前的Linux笔记记的东西有点多了，新的文件继续



### 1. 系统代理

###### Apt 代理

```Bash
# /etc/apt/apt.conf
Acquire::http::Proxy "http://127.0.0.1:7890/";
Acquire::https::Proxy "http://127.0.0.1:7890/";
```

###### Clash country 地址

```Bash
wget https://cdn.jsdelivr.net/gh/Dreamacro/maxmind-geoip@release/Country.mmdb
```

###### Git 代理

```Bash
git config --global http.proxy "http://127.0.0.1:7890"
git config --global https.proxy "http://127.0.0.1:7890"
```

###### Pip

```Bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
# ~/.pip/pip.conf

[global]
proxy = http://127.0.0.1:7890
```

###### Pnpm

```Bash
sudo apt install -y nodejs npm
sudo npm install -g pnpm
npm config set proxy http://127.0.0.1:7890
npm config set https-proxy http://127.0.0.1:7890

pnpm config set proxy http://127.0.0.1:7890
pnpm config set https-proxy http://127.0.0.1:7890
```

###### Zerotier

```Bash
curl -s https://install.zerotier.com | sudo bash
sudo zerotier-cli join NETWORK_ID
```



### 2. Gnome extensions

```Bash
Simple System Monitor
Gnome clipboard
Transparent Top Bar (Adjustable transparency)
```



### 3. Anaconda

###### 保存恢复环境

```Bash
conda pack -n chatglm -o env_chatglm.tar.gz

conda create -n chatglm --use-local -f env_chatglm.tar.gz
```



### 4. 其他

###### ufw

```Bash
sudo ufw allow from 192.168.1.100 to any port 12345
sudo ufw delete allow 12345
```

###### Ssh

```Bash
ssh-keygen -t rsa -b 4096 -C "zeknes@163.com"

cat ~/.ssh/id_rsa.pub
```
