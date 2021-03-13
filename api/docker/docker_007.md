# Docker 下常用软件的部署应用

### 一、部署 MySQL

> 在 Docker 容器中部署 MySQL，并通过外部客户端操作 MySQL 。
>
> 实现步骤：① 搜索 MySQL 镜像    ② 拉取 MySQL 镜像    ③ 创建容器    ④ 操作容器中的 MySQL
>
> 可能遇见问题：容器内的网络服务和外部机器不能直接通信 。

针对可能遇见的问题：容器内的网络服务和外部机器不能直接通信，我们可以采取<font color='red'>**端口映射**</font>的方式解决。

![1588592104946](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588592104946.png)

解释：容器内的网络服务和外部机器不能直接通信，外部机器和宿主机可以直接通信，宿主机和容器可以直接通信 。当容器中的网络服务需要被外部机器访问时，可以将容器中提供服务的端口映射到宿主机的端口上 。外部机器访问宿主机的该端口，从而间接访问容器的服务 。

1. **搜索 MySQL 镜像**

   ```shell
   docker search mysql
   ```

   ![1588592256172](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588592256172.png)

   

2. **拉取 MySQL 镜像**

   ```shell
   docker pull mysql:5.6
   ```

   ![1588592463665](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588592463665.png)

   

3. **创建容器，设置端口映射、目录映射 ( 这里使用 3307 是宿主机3306 已经有一个 MySQL )**

   ```shell
   # 在/root目录下创建mysql目录用于存储mysql数据信息
    docker run -id  -p 3307:3306  --name=c_mysql \
    -v /root/taoding/mysql/conf:/etc/mysql/conf.d \
    -v /root/taoding/mysql/logs:/logs \
    -v /root/taoding/mysql/data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=123456 \
    mysql:8
   ```

   ![1588595823370](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588595823370.png)

   参数说明：

   - **-p 3307:3306**：将容器的 3306 端口映射到宿主机的 3307 端口
   - **-v /root/taoding/mysql/conf:/etc/mysql/conf.d**：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf 配置目录
   - **-v /root/taoding/mysql/logs:/logs**：将主机当前目录下的 logs 目录挂载到容器的 /logs 日志目录
   - **-v /root/taoding/mysql/data:/var/lib/mysql** ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 数据目录
   - **-e MYSQL_ROOT_PASSWORD=123456：**初始化 root 用户的密码。

   

4. **进入容器，操作 MySQL** 

   ```shell
   docker exec –it c_mysql /bin/bash
   ```

   

5. **使用外部机器连接容器中的 MySQL** 

   ![1588596740042](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588596740042.png)

   



### 二、部署 Tomcat

1. **搜索 Tomcat 镜像**

   ```shell
   docker search tomcat
   ```

![1588597133373](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588597133373.png)



2. **拉取 Tomcat 镜像**

   ```shell
   docker pull tomcat
   ```

![1588597251652](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588597251652.png)



3. **创建容器，设置端口映射、目录映射**

   ```shell
   docker run -id --name=c_tomcat \
   -p 8080:8080 \
   -v /root/taoding/tomcat:/usr/local/tomcat/webapps \
   tomcat:8
   ```

![1588597336260](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588597336260.png)

参数说明：

- **-p 8080:8080：**将容器的8080端口映射到主机的8080端口
- **-v /root/taoding/tomcat:/usr/local/tomcat/webapps：**将主机中当前目录挂载到容器的webapps

<font color='red'>注意：进入容器 c_tomcat 容器内部，不难发现原本 webapps 中文件都处于 webapps.dist 文件夹内，如需访问 Tomcat 自带的页面， 将 webapps.disp 下的文件全都移到 webapps 文件夹内即可 。</font>



4. **使用外部机器访问 Tomcat**

   ![1588598379469](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588598379469.png)





### 三、部署 Nginx

1. **搜索 Nginx 镜像**

   ```shell
   docker search nginx
   ```

   ![1588598703938](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588598703938.png)

   

2. **拉取 Nginx 镜像**

   ```shell
   docker pull nginx
   ```

   ![1588598768426](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588598768426.png)

   

3. **创建容器，设置端口映射、目录映射**

   ```shell
   # 在 /root/taoding 目录下创建 nginx 目录用于存储 nginx 数据信息
   mkdir ~/taoding/nginx
   cd ~/taoding/nginx
   mkdir conf
   cd conf
   # 在~/nginx/conf/下创建nginx.conf文件,粘贴下面内容
   vim nginx.conf
   ```

   ```shell
   user  nginx;
   worker_processes  1;
   
   error_log  /var/log/nginx/error.log warn;
   pid        /var/run/nginx.pid;
   
   
   events {
       worker_connections  1024;
   }
   
   
   http {
       include       /etc/nginx/mime.types;
       default_type  application/octet-stream;
   
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';
   
       access_log  /var/log/nginx/access.log  main;
   
       sendfile        on;
       #tcp_nopush     on;
   
       keepalive_timeout  65;
   
       #gzip  on;
   
       include /etc/nginx/conf.d/*.conf;
   }
   
   ```

   ```shell
   docker run -id --name=c_nginx -p 80:80 \
   -v /root/taoding/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
   -v /root/taoding/nginx/logs:/var/log/nginx \
   -v /root/taoding/nginx/html:/usr/share/nginx/html \
   nginx
   ```

   ![1588599756626](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588599756626.png)

   

- 参数说明：

  - **-p 80:80**：将容器的 80端口映射到宿主机的 80 端口。
  - **-v /root/taoding/nginx/conf/nginx.conf:/etc/nginx/nginx.conf**：将主机当前目录下的 /conf/nginx.conf 文件挂载到容器的 :/etc/nginx/nginx.conf 配置文件 （<font color='red'>方便以后修改配置文件</font>）
  - **-v /root/taoding/nginx/logs:/var/log/nginx**：将主机当前目录下的 logs 目录挂载到容器的 /var/log/nginx 日志目录
  - **-v /root/taoding/nginx/html:/usr/share/nginx/html** ：将主机当前目录下的 html 目录挂载到容器 /usr/share/nginx/html 静态页面目录中，方便以后部署前端页面。

  

4. 在 /root/taoding/nginx/html 目录下创建测试页面，使用外部机器访问 Nginx

   ![1588600612108](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588600612108.png)

   

   ![1588600589779](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588600589779.png)

   



### 四、部署 Redis

1. **搜索 Redis 镜像**

   ```shell
   docker search redis
   ```

   ![1588600925913](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588600925913.png)

   

2. **拉取 Redis 镜像**

   ```shell
   docker pull redis
   ```

   ![1588600997376](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588600997376.png)



3. **创建容器，设置端口映射**

   ```shell
   docker run -id --name=c_redis -p 6379:6379 redis
   ```

   ![1588601100658](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588601100658.png)

   

4. **使用外部机器连接 Redis**

   ![1588601240510](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588601240510.png)

   

### 五、部署 Zookeeper

1. **搜索 Zookeeper 镜像**

   ```shell
   docker search zookeeper 
   ```

   ![1588601658471](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588601658471.png)

   

2. **拉取 Zookeeper镜像**

   ```shell
   docker pull zookeeper
   ```

   ![1588601699645](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588601699645.png)



3. **创建容器，设置端口映射**

   ```shell
   docker run -id --name=c_zookeeper -p 2182:2182 zookeeper
   ```

   ![1588601778519](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588601778519.png)

   

4. **使用命令查看 Zookeeper 运行状态**

   ```shell
   docker logs -f zk容器名称
   ```

   ![1588603272439](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1588603272439.png)



