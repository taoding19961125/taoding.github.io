# Dockerfile

### 一、镜像原理

> 思考：
>
> - Docker 镜像本质是什么？
> - Docker 中一个 Centos 镜像为什么只有 200 MB，而一个 Centos 操作系统的 iso 文件要几个 G ？
> - Docker 中一个 Tomcat 镜像为什么有 500 MB，而一个 Tomcat 安装包只有 70 多 MB ？



操作系统组成部分：• 进程调度子系统    • 进程通信子系统    • 内存管理子系统    • 设备管理子系统  

​								   <font color='red'>• 文件管理子系统</font>    • 网络通信子系统    • 作业控制子系统

![1588604064938](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588604064938.png)

Linux文件系统由 bootfs 和 rootfs 两部分组成：

- bootfs：包含 bootloader（引导加载程序）和 kernel（内核）
- rootfs： root 文件系统，包含的就是典型 Linux 系统中的 /dev，/proc，/bin，/etc 等标准目录和文件
- <font color='red'>不同的 Linux 发行版，bootfs 基本一样，而 rootfs 不同，如 Ubuntu，Centos 等</font>

相似的，Docker 镜像原理：

<img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588604503807.png" alt="1588604503807" style="zoom: 67%;" />

- Docker 镜像是由特殊的文件系统叠加而成
- 最底端是 bootfs，并使用宿主机的 bootfs
- 第二层是 root 文件系统 rootfs ,称为 base imag
- 然后再往上可以叠加其他的镜像文件
- 统一文件系统（Union File System）技术能够将不同的层整合成一个文件系统，为这些层提供了一个统一的视角，这样就隐藏了多层的存在，在用户的角度看来，只存在一个文件系统 。
- 一个镜像可以放在另一个镜像的上面 。位于下面的镜像称为父镜像，最底部的镜像成为基础镜像 。
- 当从一个镜像启动容器时，Docker 会在最顶层加载一个读写文件系统作为容器



**总结：**

1. Docker 镜像本质是什么 ？
   - 一个分层文件系统 。

2. Docker 中一个 Centos 镜像为什么只有 200 MB，而一个 Centos 操作系统的 iso 文件要几个 G ？
   -  Centos 的 iso 镜像文件包含 bootfs 和 rootfs ，而 Docker 的 Centos 镜像复用操作系统的bootfs，只有 rootfs 和其他镜像层 。

3. Docker 中一个tomcat镜像为什么有500MB，而一个tomcat安装包只有70多MB？
   - 由于 Docker 中镜像是分层的，Tomcat 虽然只有 70 多MB，但他需要依赖于父镜像和基础镜像，所以整个对外暴露的 Tomcat 镜像为 500 多MB 。



### 二、容器转为镜像

```shell
docker commit  容器id  镜像名称: 版本号
```

![1588605524917](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588605524917.png)

<font color='red'>注意：在将容器转为镜像时，容器所通过目录挂载方式生成的文件夹及其文件将不被保留，其它挂载目录以外的文件，包含人为操作产生的数据及文件将被保留 。即目录挂载的都不生效 。</font>



### 三、镜像转为压缩文件

```shell
docker save -o  压缩文件名称  镜像名称:版本号
```

![1588605948697](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588605948697.png)



### 四、通过压缩文件还原镜像

```shell
docker load  –i  压缩文件名称
```

![1588606129342](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588606129342.png)



### 五、Dockerfile 概述及其关键字

> 因为通过容器转为镜像方式，目录挂载都不生效，所以这里引入 Dockerfile 实现应用的无缝移植。

**Dockerfile 概念：**

- Dockerfile 是一个文本文件
- 包含了一条条的指
- 每一条指令构建一层，基于基础镜像，最终构建出一个新的镜像
- 对于开发人员：可以为开发团队提供一个完全一致的开发环境
- 对于测试人员：可以直接拿开发时所构建的镜像或者通过 Dockerfile 文件构建一个新的镜像开始工作
- 对于运维人员：在部署时，可以实现应用的无缝移植



**Dockerfile 关键字：**

| 关键字      | 作用                     | 备注                                                         |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| FROM        | 指定父镜像               | 指定 Dockerfile 基于哪个 image 构建                          |
| MAINTAINER  | 作者信息                 | 用来标明这个 Dockerfile 谁写的                               |
| LABEL       | 标签                     | 用来标明 Dockerfile 的标签，可以使用 Label 代替 Maintainer 最终都是在 docker images 基本信息中可以查看 。 |
| RUN         | 执行命令                 | 执行一段命令 默认是 /bin/sh 格式: RUN command 或者 RUN ["command" , "param1","param2"] |
| CMD         | 容器启动命令             | 提供启动容器时候的默认命令 和 ENTRYPOINT 配合使用.格式 CMD command param1 param2 或者 CMD ["command" , "param1","param2"] |
| ENTRYPOINT  | 入口                     | 一般在制作一些执行就关闭的容器中会使用                       |
| COPY        | 复制文件                 | build 的时候复制文件到 image 中（不会解压）                  |
| ADD         | 添加文件                 | build 的时候添加文件到 image 中，不仅仅局限于当前 build 上下文，可以来源于远程服务 。（<font color='red'>压缩文件会自动解压</font>） |
| ENV         | 环境变量                 | 指定 build 时候的环境变量 。可以在启动的容器的时候通过 -e 覆盖格式ENV name=value 。 |
| ARG         | 构建参数                 | 构建参数，只在构建的时候使用的参数 。如果有ENV，那么ENV的相同名字的值始终覆盖 arg 的参数 。 |
| VOLUME      | 定义外部可以挂载的数据卷 | 指定 build 的 image 那些目录可以启动的时候挂载到文件系统中 启动容器的时候使用 -v 绑定 格式 VOLUME ["目录"] |
| EXPOSE      | 暴露端口                 | 定义容器运行的时候监听的端口 启动容器的使用 -p 来绑定暴露端口 格式: EXPOSE 8080 或者 EXPOSE 8080/udp |
| WORKDIR     | 工作目录                 | 指定容器内部的工作目录 如果没有创建则自动创建 如果指定 / 使用的是绝对地址 如果不是/开头那么是在上一条 workdir 的路径的相对路径 |
| USER        | 指定执行用户             | 指定 build 或者启动的时候，用户在 RUN CMD ENTRYPONT 执行的时候的用户 。 |
| HEALTHCHECK | 健康检查                 | 指定监测当前容器的健康监测的命令，基本上没用。因为很多时候应用本身有健康监测机制 。 |
| ONBUILD     | 触发器                   | 当存在 ONBUILD 关键字的镜像作为基础镜像的时候，当执行 FROM 完成之后 会执行 ONBUILD 的命令，但是不影响当前镜像，用处也不怎么大。 |
| STOPSIGNAL  | 发送信号量到宿主机       | 该 STOPSIGNAL 指令设置将发送到容器的系统调用信号以退出 。    |
| SHELL       | 指定执行脚本的shell      | 指定 RUN CMD ENTRYPOINT 执行命令的时候使用的 shell           |

我们在编写 Dockerfile 时，可在 Docker Hub （ https://hub.docker.com/ ）上搜索镜像，模仿其写法进行编写。这里以 Tomcat 为例：

![1588686442678](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588686442678.png)

点击其中一个 Tags 

![1588686503906](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588686503906.png)

这边得到的就是其 Dockerfile 。

![1588686651838](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588686651838.png)





### 六、案例—部署SpringBoot项目

编写 Dockerfile 文件，内容如下：

```shell
FROM java:8
MAINTAINER taoding <991104402@qq.com>
ADD springboot-hello-0.0.1-SNAPSHOT.jar test.jar
CMD java -jar test.jar

```

 使用 Dockerfile 构建镜像 ,看见 Successfully ... 字样，构建成功 。

```shell
docker bulid –f dockerfile文件路径 –t 镜像名称:版本
```

![1588685814996](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588685814996.png)

检验是否成功：

![1588686099683](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588686099683.png)

![1588686141384](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588686141384.png)



