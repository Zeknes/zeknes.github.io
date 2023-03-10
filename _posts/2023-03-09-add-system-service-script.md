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





写了一个自动添加系统服务的脚本, 以后添加服务就方便了很多. 



### 1. 代码

`add_service.py`

```python
#!/usr/bin/env python3

import os
import sys

# Get the filename from the command-line arguments or prompt the user to enter one
if len(sys.argv) > 1:
    filename = sys.argv[1]
else:
    filename = input("Enter a filename: ")

# Get the full path to the file
if filename.startswith('/'):
    # If the filename starts with '/', it is already the full path
    script = filename
else:
    # If the filename does not start with '/', use the current working directory to get the full path
    script = os.path.abspath(filename)

print(f"Input file is : {filename}")

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
with open(f"/etc/systemd/system/{service_name}.service", "w") as f:
    f.write(service_file)

# Use the systemctl command to start the service
os.system(f"systemctl enable {service_name}.service")
os.system(f"systemctl start {service_name}.service")

print(f"Systemd service for {service_name} has been created and started!")

```



又写了一个挺有用的



```python
#!/usr/bin/python3

import os
import sys

def modify_script(script):
    with open(script, "r+") as f:
        # Read the first line of the file
        first_line = f.readline().strip()

        # Check if the file is a Python script and starts with the correct shebang
        if script.endswith(".py") and not first_line.startswith("#!/usr/bin/python3"):
            # Move the file pointer back to the beginning of the file
            f.seek(0)

            # Write the new shebang at the beginning of the file
            f.write("#!/usr/bin/python3\n")

            # Move the file pointer to the end of the line and add a newline character
            f.seek(0, os.SEEK_END)
            f.write("\n")

# Get the filename from the command-line arguments or prompt the user to enter one
if len(sys.argv) > 1:
    filename = sys.argv[1]
else:
    filename = input("Enter a filename: ")

# Get the full path to the file
if os.path.isabs(filename):
    # If the filename is already an absolute path, it is already the full path
    script = filename
else:
    # If the filename is not an absolute path, use the current working directory to get the full path
    script = os.path.abspath(filename)

# Modify the script if necessary
modify_script(script)

# Copy the script to /usr/bin and change permissions to make it executable
os.system(f"sudo cp {script} /usr/bin")
os.system(f"sudo chmod +x /usr/bin/{os.path.basename(script)}")

# Print information to help with debugging
print(f"The script has been copied to /usr/bin/{os.path.basename(script)}")
print("Make sure that /usr/bin is in your system's PATH variable.")
print(f"To run the script, use the command '{os.path.basename(script)}'")


```

