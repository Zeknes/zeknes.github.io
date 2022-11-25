---
layout: post
title: "开发板之间批量复制安装pip依赖"
subtitle: "A simple tutorial for pip migration"
author: "Eric"
header-style: text
tags:
  - Python
  - Pip
  - Linux
  - 开发板
---


目标
-------

PyQt开发时, 在一台开发板上搭建好了开发环境, 在其他开发板上要运行就需要重新搭建, 其中, pip依赖通常是需要联网脚本安装的. 那么在没有需要接受移植依赖的开发板没有网络环境之类的情况, 就用的上这个方法了. 比如我之前用的上就是因为要被部署的机子多, 且无网络.



步骤
-------



#### 1. 环境准备

两个开发板最好环境相同, 比如同为 ubuntu 20.04, 如果不行, 至少保证cpu为同架构, 如两者都为arm 64. 用电脑pypi下载的不行, 因为电脑下载的通常为amd 64架构.



#### 2. 旧版操作



##### 2.1. 新建文件夹, 生成配置文件

```bash
mkdir requirements
cd requirements
pip freeze > requirements.txt
```



##### 2.2. 响应报错

这个时候通常pip会报错有些依赖无法下载, 这是因为某些依赖是自带的之类的. 这种通常另一个板子也自带, requirements.txt文件删除这条就行了.



##### 2.3. 下载到本地

```bash
pip download -r requirements.txt
sudo cp * # 复制到u盘之类的位置
```



### 3. 新板操作



##### 3.1. 直接u盘安装或者复制到板子安装

```bash
sudo cp * # 板子位置
pip install --no-index --find-links="." -r requirements.txt	
```



## 4. 如果新板子有网络, 只需要pip名单



```bash
sudo pip install requirements.txt
```



![pips](/_includes/res/2022-07-29-migrate-pip-requirements-from-board-to-board/pips.png)
