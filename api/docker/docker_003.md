# Docker 架构

### 一、了解Docker三个重要概念



![1587910610515](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1587910610515.png)



- **Image 镜像**： Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统。 镜像可以用来创建docker容器。

  

- **Container 容器**： 镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。(<font color='red'>使用沙箱机制，每个容器相互隔离，保证平台的安全。</font>）

  

- **Repostory 仓库**： 仓库可看着一个代码控制中心，用来保存镜像。 (仓库分为两种：公有仓库和私有仓库。最大的公开仓库是Docker Hub，存放了数量庞大的镜像供用户下载。这里仓库的概念与Git类似，Registry 可以理解为 Github 这样的托管服务。)