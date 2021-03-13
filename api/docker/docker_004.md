# 配置 Docker 镜像加速器

> 默认情况下，将会从 DockerHub 拉取镜像。 国内从 DockerHub 拉取镜像有时会遇到困难 ，此时可以配置镜像加速器。（ 类似 Maven 的 Mirror ）

###  一、镜像加速器

Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：

- USTC   ：中科大镜像加速器（ https://docker.mirrors.ustc.edu.cn ）

- 阿里云 ：( https://<你的ID>.mirror.aliyuncs.com )

- 网易云 ：( https://hub-mirror.c.163.com/ )

- 腾讯云 ：( https://mirror.ccs.tencentyun.com ) 

- 七牛云 ：( https://reg-mirror.qiniu.com )

  

### 二、配置镜像加速器

这里我们以配置阿里云镜像加速器为例：

1. ##### 获取阿里云镜像加速器地址 ：

每个阿里云账号的加速地址是唯一专属的（ https://<你的ID>.mirror.aliyuncs.com ）。登录网址 https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors ，左侧菜单选中镜像加速器即可查看你的专属地址。

![1588307453732](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/aliyun_mirror.png)



2. ##### 配置加速器地址：可根据自身的系统，依据上图中的操作文档，直接进行配置。

   ( 1 )  Linux  >>  Centos :  可以通过修改 daemon 配置文件 /etc/docker/daemon.json 来使用加速器。

   ```shell
   # 创建文件夹
   sudo mkdir -p /etc/docker
   
   # 用户输入的内容同时保存至文件daemon.json中
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://你的专属ID.mirror.aliyuncs.com"]
   }
   EOF
   
   # 重新加载Docker守护进程
   sudo systemctl daemon-reload
   
   # 重新启动Docker
   sudo systemctl restart docker
   ```

 		直接执行命令，并 cat 查看是否存在其内容：

<img src="https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/centos_mirror.png" alt="1588309409017" style="zoom:150%;" />



   （ 2 ）Windows 10 ：Docker — Setting 下加入你的镜像加速地址 , 点击 Apply&Restart 保存后 Docker 就会重启并应用配置的镜像地址了。

   ![1588309743243](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/windows_mirror.png)



### 三、检查镜像加速器是否生效

 执行 **docker info** 命令，如果从结果中看到了如下内容，说明配置成功。 

```shell
#显示Docker系统信息，包括镜像和容器数
docker info
```

![1588310095725](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/docker-info.png)

