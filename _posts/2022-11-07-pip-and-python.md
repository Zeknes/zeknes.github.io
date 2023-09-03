---
layout: post
title: "pip和python模块, 打包, 执行相关"
subtitle: "About modules, repackaging, compiling of pip and python"
author: "Eric"
header-style: text
tags:
  - Linux
  - Pip
  - Python
  - 编译
  - ssh
---



一些关于`pip`和`python`的操作. 包括基础设置, 换源, 模块相关的. 和一些编`译打包`等等. 与之前Python代码的不同, 这篇主要是关于 `pip` 和 `python` 本身. 



## 1. pip



##### pip设置清华源

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```



##### 查看模块位置

```bash
pip show PySide6
```



##### 默认pyside designer软链接

```bash
sudo ln -s ~/.local/lib/python3.10/site-packages/PySide6/designer /usr/bin/designer
```



##### 全局使用一个模块

以pyinstaller为例

```bash
sudo pip install -U pyinstaller
```



##### pyinstaller编译

```bash
pyinstaller -F main.py
pyinstaller -D main.py
pyinstaller -D main.py --key password
pyinstaller -i <img.ico> -F main.py
import sys
sys.setrecursionlimit(100000)
```



## 2. Python



##### Python启动参数自带库

```python
#!/usr/bin/python3

import argparse


def test_for_sys(year, name, body):
    print('the year is', year)
    print('the name is', name)
    print('the body is', body)


parser = argparse.ArgumentParser(description='Test for argparse')
parser.add_argument('--name', '-n', help='name 属性，非必要参数')
parser.add_argument('--year', '-y', help='year 属性，非必要参数，但是有默认值', default=2017)
parser.add_argument('--body', '-b', help='body 属性，必要参数', required=True)
args = parser.parse_args()

if __name__ == '__main__':
    try:
        test_for_sys(args.year, args.name, args.body)
    except Exception as e:
        print(e)
```



##### Python启动参数click注解

```python
#!/usr/bin/python3

import click

@click.command()
@click.option('--name','-n',default='Leijun',help='name 参数，非必须，有默认值')
@click.option('--year',help='year 参数',type=int)
@click.option('--body',help='body 参数')
def test_for_sys(year, name, body):
    print('the year is', year)
    print('the name is', name)
    print('the body is', body)

if __name__ == '__main__':
    test_for_sys()
```



##### Python从上一级目录引入包

```python
import sys
sys.path.insert(0, sys.path[0]+"/../")
from config.env import *
```



## 3. 一个Python执行ssh配置文件的例子

##### 配置文件ini

```python
[ssh]
host=119.23.106.34
port=22
username=user
password=password
```

##### python代码

```python
# -*- coding: utf-8 -*-
import paramiko
import os
from configparser import ConfigParser


# 读取配置文件获取服务器的登录信息
def read_ini():
    info = dict()
    cf = ConfigParser()
    cf.read('config.ini', encoding='utf-8')
    keys = cf.options('ssh')
    for each in keys:
        info[each] = cf.get('ssh', each)
    print(info)
    return info


# 连接服务区并执行shell命令返回输出结果
def ssh_test(host, port, username, password):
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    # 连接服务器
    try:
        ssh.connect(hostname=host, port=port, username=username, password=password)
    except Exception as e:
        print(e)
        return

    # 设置一个内部函数，执行shell命令并返回输出结果
    def run_shell(cmd):
        ssh_in, ssh_out, ssh_error = ssh.exec_command(cmd)
        result = ssh_out.read() or ssh_error.read()
        return result.decode().strip()

    # 获取指定文件夹的绝对地址
    cmd_get_path = 'cd dbs;pwd'
    db_path = run_shell(cmd_get_path)

    # 获取指定文件夹中文件的名称，并跟上面得到的文件夹绝对地址组合起来
    cmd_get_sqls = 'cd dbs;ls'
    sqls = run_shell(cmd_get_sqls)
    lis = ['{}/{}'.format(db_path, each) for each in sqls.split('\n')]
    print(lis)

    # 关闭连接
    ssh.close()
    return lis


# 链接服务器进行文件传输
def sftp_test(host, port, username, password, from_file, to_file):
    transport = paramiko.Transport((host, port))
    try:
        transport.connect(username=username, password=password)
    except Exception as e:
        print(e)
        return
    sftp = paramiko.SFTPClient.from_transport(transport)

    # 将文件下载到本地，如果是上传使用 put
    sftp.get(from_file, to_file)
    transport.close()


if __name__ == '__main__':
    info = read_ini()
    h = info.get('host', None)
    p = int(info.get('port', None)) # 端口是int类型
    u = info.get('username', None)
    pw = info.get('password', None)
    files = ssh_test(h, p, u, pw)

    path = 'F:\\dbs'
    if files:
        for each in files:
            name = each.split('/')[-1]
            ss = sftp_test(h, p, u, pw, each, os.path.join(path, name))
```



## 4. Python编译客户端

```shell
sudo pip install pyinstaller
pyinstaller main.py

# 如果libpython报错
sudo find / -name libpython3.6m.so.1.0

sudo cp dist/ build/ -r
```