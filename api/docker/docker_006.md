# Docker 容器数据卷

> 问题：
>
> （1）Docker 容器删除后，在容器中产生的数据还在吗 ？
>
> （2）Docker 容器和外部机器可以直接交换文件吗 ？
>
> （3）容器之间如何进行数据交互 ？



### 一、数据卷概念及作用

Docker 的镜像是由一系列的只读层组合而来，当启动一个容器的时候，Docker 加载镜像的所有只读层，并在最上层加入一个读写层 。这个设计使得 Docker 可以提高镜像构建、存储和分发的效率，节省了时间和存储空间，然而也存在如下问题 。

- 容器中的文件在宿主机上存在形式复杂，不能在宿主机上很方便的对容器中的文件进行访问

- 多个容器之间的数据无法共享

- 当删除容器时，容器产生的数据将丢失

  

为了解决这些问题，Docker 引入了数据卷（volume）机制。volume 是存在一个或多个容器中的特定文件或文件夹，这个目录能够独立于联合文件系统的形式在宿主机中存在，并为数据的共享与持久提供一下便利 。

- volume 在容器创建时就初始化，在容器运行时就可以使用其中的文件
- volume 能在不同的容器之间共享和重用  ( <font color='red'>容器间的通信</font> )
- 对 volume 中的数据的操作会马上生效   （ <font color='red'>当容器目录和数据卷目录绑定后，对方的修改会立即同步 </font>）
- 对 volume 中数据操作不会影响到镜像本身
- volume 的生存周期独立于容器的生存周期 。（ <font color='red'>即使删除容器，volume 仍然会存在，没有任何容器使用的 volume 也不会被 Docker 删除</font> ）



**小结：**

1. 数据卷概念 ：宿主机的一个目录或文件
2. 数据卷作用：
   - 容器数据持久化
   - 客户端和容器数据交换
   - 容器间数据交换



### 二、配置数据卷

1. ##### **一个容器挂载一个数据卷**

   ![1588497726677](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588497726677.png)

   - 创建启动容器时，使用 –v 参数设置数据卷	

   ```shell
   # 设置数据卷
   docker run ... –v  宿主机目录(文件):容器内目录(文件) ...
   # 带权限的设置数据卷 (ro:read only,只读)
   docker run ... –v  宿主机目录(文件):容器内目录(文件):ro ...
   ```

    <font color='red'>注意事项：1. 目录必须是绝对路径      2. 如果目录不存在，会自动创建      3. 可以挂载多个数据卷</font>

   ​         这里我们创建一个名为 c_tomcat 的容器，挂载宿主机目录 /root/taoding 至 容器目录 /usr/local/tomcat/project_data 中（ 实际情况下，我们可以直接挂载至 c_tomcat 容器下 webapps 目录，方便以后部署系统 ），如下图所示：

![](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588474469446.png)

![1588478098399](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588478098399.png)

​	  	   我们在宿主机挂载目录下或容器挂载目录下操作文件，对方会立即同步修改 。这里我们在容器内挂载			目录中创建一名为 test.txt 的文件，宿主机相应目录也相应生成改文件 。进一步在宿主机中修改 test.txt 文			件中内容，容器内相应文件也立即响应该修改 。

![1588478771577](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588478771577.png)



2. **多个容器挂载同一数据卷，<font color='red'>实现容器间通讯</font>**

   <img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588497636547.png" alt="1588497636547" style="zoom:67%;" />

   ![1588480940684](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588480940684.png)



### 三、数据卷容器

> 问题：多容器进行数据交换：
>
> ​		1. 多个容器挂载同一个数据卷 （ 很多个，操作复杂，数据持久化、恢复等麻烦 ）



**数据卷容器**： 命名的容器挂载数据卷，其他的容器通过挂载这个父容器实现数据共享，挂载数据卷的容器，我们称为数据卷容器。 

<img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588497901897.png" alt="1588497901897" style="zoom:67%;" />



1. 创建、启动数据卷容器，使用 –v 参数设置数据卷

  ```shell
  docker run –it --name ... –v 宿主机目录(文件):容器内目录(文件) ...
  ```

![1588589064068](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588589064068.png)



2. 创建启动容器，使用 –-volumes-from 参数设置数据卷

  ```shell
  docker run –it --name ... --volumes-from ...
  docker run –it --name ... --volumes-from ...
  ```

 

![1588589378031](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588589378031.png)

![1588589476360](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588589476360.png)

此时，我们在这些容器中操作数据或文件，其他容器或数据卷容器也将相应发生变化 。<font color='red'>即使容器所绑定的数据卷容器损坏或被删除，其下绑定的容器也可正常使用。</font>

![1588590425018](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588590425018.png)



