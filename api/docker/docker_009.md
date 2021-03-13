# Docker 服务编排

### 一、Docker Compose 概述

> 微服务架构的应用系统中一般包含若干个微服务，每个微服务一般都会部署多个实例，如果每个微服务都要手动启停，维护的工作量会很大 。
>
> - 要从 Dockerfile build image 或者去 Docker Hub 拉取 image
> - 要创建多个 Container
> - 要管理这些 Container（启动、停止、删除）



**服务编排**： 按照一定的业务规则批量管理容器

Docker Compose 是一个编排多容器分布式部署的工具，提供命令集管理容器化应用的完整开发周期，包括服务构建，启动和停止 。使用步骤：

1. 利用 Dockerfile 定义运行环境镜像
2. 使用 docker-compose.yml 定义组成应用的各服务
3. 运行 docker-compose up 启动应用

![1588687257349](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588687257349.png)



### 二、Docker Compose 安装使用

- **安装 Docker Compose**

  目前已经完全支持 Linux、Mac OS 和 Windows，在我们安装 Compose 之前，需要先安装 Docker 。

  ```shell
  # 安装 Dokcer Compose
  curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
  # 设置文件可执行权限 
  chmod +x /usr/local/bin/docker-compose
  # 查看版本信息 
  docker-compose -version
  ```

![1588774316510](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588774316510.png)



- **卸载 Docker Compose**

  ```shell
  # 删除其二进制文件
  rm /usr/local/bin/docker-compose
  ```



### 三、编排Nginx+SpringBoot项目

- **创建 docker-compose 目录**

  ```shell
  mkdir ~/taoding/docker_compose
  cd ~/taoding/docker_compose
  ```

  

- **编写 docker-compose.yml 文件**

  ```shell
  # 指定docker-compose版本
  version: '3'
  # 定义/启动哪些容器、服务 （nginx、test_app）
  services:
    nginx:
     image: nginx
     ports:
      - 80:80
     links:
      - test
     volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d
    test:
      image: test
      expose:
        - "8080"
  ```

  

- **创建 ./nginx/conf.d 目录**

  ```shell
  mkdir -p ./nginx/conf.d
  ```

  

- **在 ./nginx/conf.d 目录下编写 nginx.conf 文件**

  ```shell
  server {
      listen 80;
      access_log off;
  
      location / {
          proxy_pass http://test:8080;
      }
     
  }
  ```

  

- **在~/docker-compose 目录下 使用docker-compose 启动容器**

  ```shell
  docker-compose up
  ```

  ![1588778208528](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588778208528.png)



- **测试访问**

  ![1588778242580](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588778242580.png)

  

