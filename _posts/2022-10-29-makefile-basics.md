---
layout: post
title: "Makefile 的一些知识点"
subtitle: "Makefile"
author: "Eric"
header-style: text
tags:
  - Linux
  - Makefile
  - C/C++
  - 编译
---





Makefile是用来管理编译的, 类似于CMake, 以前有看到一种说法, 就是Makefile不适合用来管理太大的项目, 大项目要用CMake. 但实际上庞大如Linux内核, 用的也是Makefile, 当然, 这和Linux用的是C语言而不是C++关系很大. 

记录以下我学习Makefile过程中感觉比较重要的一些点的笔记.





## 知识点



### 1. = 和 :=

#### 1.  "="

​      make会将整个makefile展开后，再决定变量的值。也就是说，变量的值将会是整个makefile中最后被指定的值。看例子：

```shell
            x = foo             
            y = $(x) bar             
            x = xyz
```

​      在上例中，y的值将会是 xyz bar ，而不是 foo bar 。



#### 2.  “:=”

​      “:=”表示变量的值决定于它在makefile中的位置，而不是整个makefile展开后的最终值。

```shell
            x := foo             
            y := $(x) bar             
            x := xyz
```

​      在上例中，y的值将会是 foo bar ，而不是 xyz bar 了。



### 2.  交叉编译aarch64

```shell
sudo apt-get install gcc-12-aarch64-linux-gnu
sudo cp /usr/aarch64-linux-gnu/lib/* /lib/
```



### 3.  变量

```shell
$@：目标
$^：所有目标依赖
$<：目标依赖列表中的第一个依赖
$?：所有目标依赖中被修改过的文件
$%：当规则的目标是一个静态库文件时，$%代表静态库的一个成员名
$+：类似$^，但是保留了依赖文件中重复出现的文件
$*：在模式匹配和静态模式规则中，代表目标模式中%的部分。比如hello.c，当匹配模式为%.c时，$*表示hello
$(@D)：表示目标文件的目录部分
$(@F)：表示目标文件的文件名部分
$(*D)：在模式匹配中，表示目标模式中%的目录部分
$(*F)：在模式匹配中，表示目标模式中%的文件名部分
-: ：告诉make在编译时忽略所有的错误
@: ：告诉make在执行命令前不要显示命令
```



### 4. 显示核心数量

```shell
nproc
```
