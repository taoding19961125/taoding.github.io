# Docker 私有仓库

### 一、搭建私有仓库

- **拉取私有仓库镜像** 

  ```shell
  docker pull registry
  ```

- **启动私有仓库容器** 

  ```shell
  docker run -id --name=registry -p 5000:5000 registry
  ```

  验证：浏览器输入地址http://你的IP:5000/v2/_catalog，如看到  {"repositories":[]}  表示私有仓库搭建成功。

  ![1588774793643](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588774793643.png)

  



- **修改 daemon.json ，在文件中添加一个key，保存退出。此步用于让 Docker 信任私有仓库地址。注意将私有仓库服务器 IP 修改为自己私有仓库服务器真实 IP 。{"insecure-registries":["私有仓库服务器ip:5000"]}** 

  ```shell
  vim /etc/docker/daemon.json  
  ```

- 重启 Docker 服务 

  ```shell
  # 重启Docker服务
  systemctl restart docker
  # 开启私有仓库
  docker start registry
  ```

  ![1588775006026](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588775006026.png)

  

### 二、上传镜像

- **标记镜像为私有仓库的镜像**   

  ```shell
  docker tag 镜像名称:版本 私有仓库服务器IP:5000/镜像名称:版本
  ```

- **上传标记的镜像** 

  ```shell
  docker push 私有仓库服务器IP:5000/镜像名称:版本
  ```

  ![1588776424899](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588776424899.png)

  ![1588776487599](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588776487599.png)

  

  <font color='red'>注意：这里为了测试，私有仓库用的同一台服务器 。</font>

  

### 三、拉取镜像

- **拉取镜像**

  ```shell
  docker pull 私有仓库服务器ip:5000/镜像名称:版本
  ```

  ![1588776563348](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588776563348.png)

  