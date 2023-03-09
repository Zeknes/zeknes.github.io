---
layout: post
title: "自动添加系统服务脚本"
subtitle: "Add system service automatically with a script"
author: "Eric"
header-style: text
tags:
  - Linux
  - python
---

























x写了一个自动添加系统服务的脚本, 以后添加服务就方便了很多. 



### 1. 代码

`add_service.py`

```python
#!/usr/bin/env python3

import os

# Prompt the user to input a filename
filename = input("Enter a filename: ")

# Get the full path to the file
if filename.startswith('/'):
    # If the filename starts with '/', it is already the full path
    script = filename
else:
    # If the filename does not start with '/', use the current working directory to get the full path
    script = os.path.abspath(filename)

# Extract the filename from the path
service_name = os.path.basename(script)

# Generate a systemd service file with the service name based on the filename
service_file = f"""[Unit]
Description=Systemd service for {service_name}

[Service]
"""

# If the script is a Python file, add "python3" before the script name in the service file
if script.endswith('.py'):
    service_file += f"ExecStart=python3 {script}\n"
else:
    service_file += f"ExecStart={script}\n"

service_file += """Restart=always
User=root

[Install]
WantedBy=multi-user.target"""

# Write the service file to disk
with open(f"{service_name}.service", "w") as f:
    f.write(service_file)

# Use the systemctl command to start the service
os.system(f"systemctl enable {service_name}.service")
os.system(f"systemctl start {service_name}.service")

print(f"Systemd service for {service_name} has been created and started!")

```


