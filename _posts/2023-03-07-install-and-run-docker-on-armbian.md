---
layout: post
title: "在armbian上安装运行docker"
subtitle: "Install and run docker on armbian"
author: "Eric"
header-style: text
tags:
  - docker
  - Linux
  - armbian
---





## 1. 安装



1. Update the package list:

   ```shell
   sudo apt update
   ```

2. Install required packages:

   ```shell
   sudo apt install -y \
       apt-transport-https \
       ca-certificates \
       curl \
       gnupg \
       lsb-release
   ```

3. Add Docker’s official GPG key:

   ```shell
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. Add the stable Docker repository:

   ```shell
   echo \
     "deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. Update the package list again:

   ```shell
   sudo apt update
   ```

6. Install Docker:

   ```shell
   sudo apt install -y docker-ce docker-ce-cli containerd.io
   ```

7. Verify that Docker is installed:

   ```shell
   sudo docker run hello-world
   ```



## 2. 运行



1. Pull the Docker image:

   ```shell
   docker pull chenzhaoyu94/chatgpt-web
   ```

2. Run the container:

   ```shell
   docker run -p 8000:8000 chenzhaoyu94/chatgpt-web
   ```

3. Other commands:

   ```shell
   # 前台运行
   docker run --name chatgpt-web --rm -it -p 3002:3002 --env OPENAI_API_KEY=your_api_key chatgpt-web
   
   # 后台运行
   docker run --name chatgpt-web -d -p 3002:3002 --env OPENAI_API_KEY=your_api_key chatgpt-web
   ```

   
