# 安装 Docker

> Docker可以运行在MAC、Windows、CentOS、UBUNTU等操作系统上，这里将基于Windows 10 和 CentOS 7 安装Docker。

### 一、Windows下安装

1. ##### Win10 下需先开启 Hyper-V 功能 。

   ![1586621388649](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586621388649.png)

   

2. ##### 安装Toolbox ( https://hub.docker.com/editions/community/docker-ce-desktop-windows  ）。

   <img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586698883269.png" style="zoom:80%;" />

   <img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586698963244.png" style="zoom:80%;" />

   

3. ##### 安装完成后，可以在命令行执行 docker version 来查看版本号 。

   ```shell
   # 执行 docker version 来查看版本号
   docker version 
   ```

   <img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586699764244.png" alt="1586699764244" style="zoom:80%;" />

   

### 二、Linux下安装

1. ##### yum 包更新到最新版本 。

   ```shell
   # 升级所有包同时也升级软件和系统内核
   yum update
   ```

   

2. ##### 安装需要的软件包， yum-util 提供 yum-config-manager 功能，另外两个 device-mapper-persistent-data 和 lvm2 是 devicemapper 的驱动依赖 。 

   ```shell
   # 安装需要的软件包
   yum install -y yum-utils device-mapper-persistent-data lvm2
   ```

<img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586701813520.png" style="zoom: 80%;" />



3. ##### 设置稳定的 yum 仓库 。																												

   ```shell
   # 设置yum源
   yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```

   ![1586701308548](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586701308548.png)

   

4. ##### 安装 Docker，出现输入的界面都按 y 。

   ```shell
   # 安装Docker，出现输入的界面都按 y 
   yum install -y docker-ce
   ```

   <img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586702153413.png" alt="1586702153413" style="zoom:80%;" />

   

5. ##### 查看 Docker 版本，验证是否验证成功 。

   ```shell
   # 查看Docker版本，验证是否验证成功
   docker -v
   ```

   <img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586702194733.png" alt="1586702194733" style="zoom:80%;" />

