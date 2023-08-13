---
layout: post
title: "卸载多个kernel中的其中一个"
subtitle: "Enable lid function on laptop"
author: "Eric"
header-style: text
tags:
  - laptop
  - Linux
  - python
---





Ubuntu23.04升级kernel之后直接挂逼了，我以为要重装丢数据了，万幸的是突然想起默认是保存了两个kernel的，所以从旧版kernel还能正常启动。 启动之后第一件事就是卸载掉坏掉的那个kernel。



### 步骤



##### 1.查看已安装的内核：

在终端中运行以下命令，以列出已安装的所有内核版本：

```bash
dpkg --list | grep linux-image
```

找到你想要卸载的内核版本的包名，通常以 `linux-image-` 开头。



##### 2.卸载内核：

   使用以下命令卸载特定的内核版本，将 `<kernel_version>` 替换为你要卸载的内核版本号：

   ```bash
   sudo apt-get remove linux-image-<kernel_version>
   ```

   例如：

   ```bash
   sudo apt-get remove linux-image-6.2.0-27-generic
   ```

   这将卸载指定的内核版本。



##### 3.更新GRUB引导：

   在卸载内核后，你需要更新GRUB引导配置，以确保引导菜单正确显示。运行以下命令：

   ```bash
   sudo update-grub
   ```

   这会更新GRUB引导并移除已卸载内核的引导选项。



##### 4.清理不再使用的内核包：

   运行以下命令以删除已卸载内核的相关文件和依赖项：

   ```bash
   sudo apt-get autoremove
   ```

   这将自动清理不再使用的内核文件和依赖项。
