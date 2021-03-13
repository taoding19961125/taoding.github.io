# Docker 概述

### 一、Docker的概念

![1586620255057](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/docker_icon.png)

- Docker 是一个开源的应用容器引擎

- 诞生于 2013 年初，基于 Go 语言实现， dotCloud 公司出品（后改名为Docker Inc）

- Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上

- 容器是完全使用沙箱机制，相互隔离

- 容器性能开销极低

- Docker 从 17.03 版本之后分为 CE（Community Edition: 社区版） 和 EE（Enterprise Edition: 企业版）



### 二、为什么选择Docker

Docker 是为解决开发环境、测试环境以及生产环境中，可能出现的 “水土不服” 情况。



![1586620255057](https://cdn.jsdelivr.net/gh/taoding19961125/cdn/images/docker/1586620255057.png)



### 三、Docker的应用场景

- Web 应用的自动化打包和发布

- 自动化测试和持续集成、发布

- 在服务型环境中部署和调整数据库或其他的后台应用

- 从头编译或者扩展现有的 OpenShift 或 Cloud Foundry 平台来搭建自己的 PaaS 环境