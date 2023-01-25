---
layout: post
title: "使用qemu模拟运行一个arm64 ubuntu系统"
subtitle: "Use qemu to run an arm64 ubuntu iso"
author: "Eric"
header-style: text
tags:
  - Linux
  - 交叉编译
  - 虚拟化
  - qemu
  - ubuntu
---



通过`qemu` 运行一个`arm64` `ubuntu` 系统可以用于交叉编译等等. 相比于用 `Qt交叉编译`工具链, 通过`qemu` 模拟的系统有完全的功能.





## 1. 安装qemu等



```shell
sudo apt-get install -y qemu-system-aarch64 qemu
```



生成 `qcow2` 文件

```shell
qemu-img create ubuntu22.04-arm64.qcow2 25G
```





## 2. 下载必要文件



1. 下载`iso`
2. 下载`QEMU_EFI.fd`





## 3. 安装



```shell
qemu-system-aarch64 -m 4096 -cpu cortex-a57 -smp 6 -M virt -bios QEMU_EFI.fd -no
graphic -drive if=none,file=ubuntu-arm64.iso,id=cdrom,media=cdrom -device virtio-s
csi-device -device scsi-cd,drive=cdrom -drive if=none,file=ubuntu.qcow2,id=hd0 -
device virtio-blk-device,drive=hd0

```



```shell
qemu-system-aarch64 -m 4096 -cpu cortex-a57 -smp 6 -M virt -bios QEMU_EFI.fd -nographic -drive if=none,file=ubuntu.qcow2,id=hd0 -device virtio-blk-device,drive=hd0

```

