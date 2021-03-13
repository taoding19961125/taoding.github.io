# Docker 相关命令

> 这部分包含主要三部分命令：服务相关命令、镜像相关命令、容器相关命令。对应图中的 daemon 、image 和 container 三部分。

![1588310978813](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/docker_parts.png)

### 一、 服务相关命令

服务相关命令，也成为后台守护进程相关命令，操作 Docker 整体。

- ##### 启动 Docker 服务

  ```shel
  systemctl start docker
  ```

- #####  停止 Docker 服务

  ```shell
  systemctl stop docker
  ```

- #####  重启 Docker 服务

  ```shell
  systemctl restart docker
  ```

- #####  查看 Docker 服务状态

  ```shell
  systemctl status docker
  ```

  开启时：active (running)

  ![1588312026700](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588312026700.png)

  关闭时：inactive (dead)

  ![1588312191114](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588312191114.png)

  

- #####  设置开机启动 Docker 服务

  ```shell
  systemctl enable docker
  ```

  ![1588312289609](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588312289609.png)

  

### 二、 镜像相关命令

- ##### 查看镜像：查看本地所有的镜像

  ```shell
  # 查看本地所有镜像
  docker images
  # 查看所有镜像的id
  docker images –q 
  ```

  默认为空：

  ![1588312828505](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588312828505.png)

  拉取后查看：

  ![1588316642942](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588315463118.png)

  其中字段TAG是该镜像的版本信息，IMAGE ID是镜像的唯一ID。

  <font color='red'>注意：我们观察这里的 Redis，存在两个，并且 IMAGE ID 相同，说明此时 Redis 最新版本就是6 。一个镜像可以打不同的版本名称，他们可能是同一个。</font>

  如下图：点开，其指向的都是6这个版本。

  ![1588316956519](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588316956519.png)

  ![1588317058036](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588317058036.png)

  

  

- ##### 搜索镜像：从网络中查找需要的镜像

  ```shell
  docker search 镜像名称
  ```

  ![1588313255318](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588313255318.png)

  其中字段 NAME 代表其镜像名称，DESCRIPTION 是该镜像的相关描述介绍，STARS 类似于Github 上的星数，<font color='red'>OFFICIAL 是否官方的（标示OK的为官方）</font>，AUTOMATED 是否自动流程构建的。

  

- #####  拉取镜像：从 Docker 仓库下载镜像到本地，镜像名称格式为 name:version 。

  <font color="red">如果版本号不指定则是最新的版本。如果不知道镜像版本，可以去 Docker Hub 搜索对应镜像查看。</font>

  ```shell
  docker pull 镜像名称
  ```

  ![1588314260974](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588314260974.png)



- #####  删除镜像：删除本地镜像

  ```shell
  # 通过镜像ID，删除指定本地镜像
  docker rmi 镜像id 
  # 通过进行名称和版本号，删除指定本地镜像
  docker rmi name:version
  #查看所有镜像id
  docker images -q
  # 删除所有本地镜像，``里面引用，相当于把``里面的执行的结果当做参数来执行
  docker rmi `docker images -q` 
  ```

  现有本地镜像：

  ![1588316642942](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588315463118.png)

  这里我们执行 docker rmi 96812ab2b931 （IMAGE ID 方式）来删除 Redis 镜像，发现该ID指向多个镜像仓库源，报错。

  ![1588317323038](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588317323038.png)

  此时我们可以通过 docker  REPOSITORY :TAG 方式来删除本地镜像，如下图所示。

  ![1588317975223](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588317975223.png)

  但此时，本地镜像并没有真正被删除，因为仍被 TAG 为 latest 的 redis 镜像所引用。不过，我们如果尝试去删除 IAMGE ID 在本地仅有一个引用的镜像时，如删除 TAG 为 8 的 mysql , 镜像将会被真正删除。

  ![1588318510242](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588318510242.png)



### 三、 容器相关命令

容器是由镜像和物理的文件运行出来的实例。容器相关命令主要是关于如何通过镜像创建出容器并且对齐进行相关操作。

- ##### 查看容器

  ```shell
  # 查看正在运行的容器
  docker ps
  # 查看所有容器
  docker ps –a 
  ```

  默认无：

  ![1588320850978](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588320850978.png)

  后续创建后查看：

![1588325865347](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588325865347.png)



- #####  创建并启动容器

  ```shell
  docker run  参数
  ```

  ```
  参数说明：
  • -i ：保持容器运行，通常与 -t 同时使用。加入 it 这两个参数后，容器创建后自动进入容器中。退出容器后，容器自动关闭 。
  • -t ：为容器重新分配一个伪输入终端，通常与 -i 同时使用 。
  • -d ：以守护（后台）模式运行容器。创建一个容器在后台运行，需要使用 docker exec 进入容器。退出后，容器不会关闭 。
  • -it ：创建的容器一般称为交互式容器，-id 创建的容器一般称为守护式容器 。
  • --name ：为创建的容器命名 。
  ```

  1. #####  以 -it 方式创建容器

     例：创建一个 Centos 容器：也可缩写成 docker run -it --name=c_centos centos:7 。

![1588322605602](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588322605602.png)

​					其中 centos:7 也可用 IMAGE ID 来替换 , 只要能保证可以指定唯一镜像，写成 docker run -it --					name=c_centos  5e35e350aded 。

​		<font color='red'>注意</font>：<font color='red'>①  -it 方式创建的容器，创建后将自动进入容器内部， [root@localhost ~ ] 也相应变成 							[root@c7a7562634f0 /] ,代表此时处于容器内部 。此时可执行该容器所包含的命令来进行相应操							作 。</font>

​					<font color='red'>② -it 方式创建的容器，执行 exit 退出容器后将回到宿主机，同时容器自动关闭 。</font>如下图所示：

![1588324227639](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588324227639.png)



2. ##### 以 -id 方式创建容器

   例：创建一个 Centos 容器：docker run -id --name=c_centos02  centos:7 。

   ![1588325090276](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588325090276.png)

   <font color='red'>*以 -id 方式创建的容器，将以守护（后台）模式运行。容器创建成功后不会自动进入容器内部 ，需要使用 docker exec 进入容器 ( “/bin/bash” 为进入容器的初始化指令，docker ps -a 后 COMMAND 字段可见 ) 。*</font>如下图：

![1588325412570](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588325412570.png)

<font color='red'>	*以 -id 方式创建的容器，exit 退出后，容器不会关闭。</font>

![1588325827087](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588325827087.png)



- ##### 进入容器

  ```shell
  # 退出容器，容器不会关闭
  docker exec 参数 
  ```

  ![1588325412570](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588325412570.png)

  ![1588325827087](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588325827087.png)

  

- #####  停止容器

  ```shell
  docker stop 容器名称/ID
  ```

  ![1588330135540](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588330135540.png)

  

- #####  启动容器

  ```shell
  docker start 容器名称/ID
  ```

  ![1588330193696](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588330193696.png)

  

- #####  删除容器

  ```shell
  # 删除容器
  docker rm 容器名称/ID
  # 查看所有容器ID
  docker ps -aq
  # 删除所有容器
  docker rm `docker ps -aq`
  ```

  ![1588330350303](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588330350303.png)

  <font color='red'>**如果容器是运行状态则删除失败，需要停止容器才能删除 。*</font>

- #####  查看容器信息

  ```shell
  docker inspect 容器名称/ID
  ```

  ![1588330985407](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588330985407.png)



