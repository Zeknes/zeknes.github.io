---
layout: post
title: "在驱动不支持的笔记本上实现合盖休眠功能"
subtitle: "Enable lid function on laptop"
author: "Eric"
header-style: text
tags:
  - laptop
  - Linux
  - python
---







终于解决了我笔记本无法合盖休眠的问题, 我还以为要从底层编写驱动, 最终还是通过`python`代码加上`systemd`系统服务的方式解决了. 太难了, 困扰了我一年多的问题. 



首先, 肯定是没有网上说的改一个配置就行那么简单, 要不然我也不会忙活这么久了.



### 1. 代码

`/opt/lid_signal.py`

```python
import subprocess
import time

def get_lid_state():
    output = subprocess.check_output("cat /proc/acpi/button/lid/LID0/state", shell=True)
    state_bytes = output.split(b":")[-1].strip()
    state = state_bytes.decode()
    return state

def main():
    print("Starting lid signal detection...")
    while True:
        lid_state = get_lid_state()
        if lid_state == "closed":
            subprocess.run(["systemctl", "suspend"])  # call systemctl suspend command
        time.sleep(5)

if __name__ == "__main__":
    main()

```



`/etc/systemd/system/lid_signal.service`

```shell
[Unit]
Description=Lid Signal Detection
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/bin/python3 /opt/lid_signal.py
Restart=always

[Install]
WantedBy=multi-user.target

```



### 2. 修改权限



根据情况看需不需要改service文件

```shell
sudo chmod 755 /opt/lid_signal.py
```



### 3. 启动服务



```shell
sudo systemctl daemon-reload
sudo systemctl enable lid_signal.service
sudo systemctl start lid_signal.service

```

