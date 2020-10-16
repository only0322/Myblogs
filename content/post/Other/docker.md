---
title: "Docker技术详解"
date: 2020-10-14T23:02:15+08:00
draft: true
---


### 1.docker介绍

Docker是诞生于2013年，是dotCloud的一个开源项目,基于Google推出的GO语言实现。遵从Apache2.0协议。

Docker的目标是实现轻量级的操作系统虚拟化的解决方案。

Docker的基础是Linux容器（LXC）等技术，（LXC系统提供工具来管理容器，具有先进的网络和存储支持，还有最小容器操作系统模版的广泛选择）。

在LXC的基础上Docker进行了进一步的封装，用户不需要去关心容器的管理，操作更简单。就像操作一个快速轻量的虚拟机一样简单。



### 2.docker使用

`docker images` 查看镜像

`docker ps` 查看容器

`docker start/stop` 启停容器



### 3.举例

安装Nginx : `docker pull nginx`

测试: `sudo docker run --name nginx-test -p 8081:80 -d nginx`

### 4.docker的基本命令

```

docker version查看docker版本
docker info查看docker详细信息
docker --help查看docker命令
docker images查看docker镜像

docker images -a列出本地所有的镜像
docker images -p只显示镜像ID
docker images --digests显示镜像的摘要信息
docker images --no-trunc显示完整的镜像信息

docker search tomcat

docker search -s 30 tomcat

docker pull tomcat从Docker Hub上下载tomcat镜像。等价于：docker pull tomcat:latest
docker commit -m "提交的描述信息" -a "作者" 容器ID 要创建的目标镜像名称:[标签名]提交容器使之成为一个新的镜像。
如：docker commit -m "新的tomcat" -a "lizq" f9e29e8455a5 mytomcat:1.2
docker rmi hello-world从Docker中删除hello-world镜像
docker rmi -f hello-world从Docker中强制删除hello-world镜像
docker rmi -f hello-world nginx从Docker中强制删除hello-world镜像和nginx镜像
docker rmi -f $(docker images -p)通过docker images -p查询到的镜像ID来删除所有镜像

docker run [OPTIONS] IMAGE

docker ps列出当前所有正在运行的容器
docker ps -a列出所有的容器
docker ps -l列出最近创建的容器
docker ps -n 3列出最近创建的3个容器
docker ps -q只显示容器ID
docker ps --no-trunc显示当前所有正在运行的容器完整信息
exit退出并停止容器
Ctrl+p+q只退出容器，不停止容器
docker start 容器ID或容器名称启动容器
docker restart 容器ID或容器名称重新启动容器
docker stop容器ID或容器名称停止容器
docker kill 容器ID或容器名称强制停止容器
docker rm 容器ID或容器名称删除容器
docker rm -f 容器ID或容器名称强制删除容器
docker rm -f $(docker ps -a -q)删除多个容器
docker logs -f -t --since --tail 容器ID或容器名称查看容器日志



```