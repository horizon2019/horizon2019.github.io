---
title: Docker知识总结
date: 2019-04-21 16:11:18
tags: Docker
---


**Docker是什么?**
Docker是一种新的容器化技术，为应用开发和部署提供“一站式”容器解决方案，能帮助开发者高效快速的构建应用，实现“Build，Ship and Run Any App, Anywhere”，从而达到“一次构建，到处运行”的目的。Docker也解决了跨平台部署的问题，你可以在MacOS, Windows, Linux上安装Docker，然后下载你所需要的Docker镜像(image)进行程序开发。当你的程序需要发布的时候，仅仅需要将你的Docker镜像打包发布，不再需要搭建新环境，让软件开发流程变得快速简单。

**Docker的主要构成：**
* Docker Client：用户和 Docker 守护进程进行通信的接口，也就是 docker 命令。
* Docker Daemon：宿主机上用于用户应答用户请求的服务。
* Registry：注册服务器，注册服务器是存放仓库（Repository）的具体服务器。

**Docker的三元素：**
* Container：用于运行应用程序的容器，包含操作系统、用户文件和元数据，相当于镜像Images的一个运行实例。。
* Images：只读的 Docker 容器模板，简言之就是系统镜像文件。
* DockerFile：进行镜像创建的指令文件。


**Docker基础命令**
Docker提供了很多命令来管理镜像、容器和仓库。包括：
从Docker Hub仓库中查找search、上传push、下载pull镜像。
查看本地已有镜像、容器信息的images、inspect和ps命令。
删除本地镜像和容器的rmi和rm命令。
基于已有容器创建镜像的commit命令和基于Dockerfile创建镜像build命令。
运行、进入容器的run、exec和attach命令。
镜像的保存和导入命令save和load，容器的导出导入命令export和import


![](https://www.blog.starmoon.tech/img/docker.png)

`对容器的操作：`
docker exec -i -t webapp /bin/bash
docker start <ID>/<name>
docker stop <ID>/<name>
docker restart <ID>/<name>
docker rm <ID>/<name>
docker ps 查看正在运行的容器
`镜像的操作：`
docker rmi <ID>/<name>删除镜像
docker run <imagename>运行镜像
docker pull <imagename>下载镜像 
docker images查看本地镜像 
docker info检查安装 

**什么是Dockerfile?**
Dockerfile实际上是由一行行命令组成的,让*用户可以方便的创建自定义镜像*。
Dockerfile大体由四部分组成：
指明基础镜像指令FROM
维护者信息指令MAINTAINER
镜像操作指令RUN、EVN、ADD和WORKDIR等
容器启动时的执行指令CMD、ENTRYPOINT和USER等
Dockerfile示例：
```
FROM python:2.7//从dockerhub上pull下python 2.7的基础镜像。
MAINTAINER yourname <your email>//维护者的信息
COPY . /app//copy当前目录到容器中的 /app目录下
WORKDIR /app //指定工作路径为/app
RUN pip install -r requirements.txt//安装依赖
EXPOSE 5000 //暴露5000端口
ENTRYPOINT ["python"]//一个 Dockerfile 中只能有一个 ENTRYPOINT，如果有多个，则最后一个生效。
CMD ["app.py"] //启动app
```