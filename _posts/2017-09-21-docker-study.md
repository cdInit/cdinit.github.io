---
layout: post
title:  "Docker的安装以及常用命令"
categories: docker
tags: docker
author: Init
---

* content
{:toc}

本文介绍docker的安装与常用命令，以及制作自己的镜像文件。也是最近学习docker的笔记。




## 安装docker

安装还是比较简单的，而且还有加速，下载镜像简直不要太快。

``` sh
1.安装yum-utils
sudo yum install -y yum-utils

2.下载docker yum源
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    
3.更新软件缓存
sudo yum makecache fast

4.获取、安装Docker CE
sudo yum -y install docker-ce

5.设置DaoCloud 加速器
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://35fae48b.m.daocloud.io

6.启动docker并查看安装的docker版本
systemctl start docker

7.查看版本信息
docker info

8.设置Docker开机启动
systemctl enable docker

```
## 使用docker

### 基本使用

#### 1.搜索镜像

``` sh
docker search centos
```
#### 2.下载镜像

``` sh
docker pull centos
```
#### 3.列出本地镜像

``` sh
docker images
```
#### 4.使用镜像创建容器

``` sh
docker run --name container-name -d image-name
docker run --name centos-node -d centos
docker run -i -t ubuntu /bin/bash
```
#### 5.查看容器

``` sh
docker ps -a
```
#### 6.启动容器

``` sh
docker run -idt centos /bin/bash
```

[docker run 命令参数及使用](http://www.cdinit.com/2017/09/21/docker-run/)

#### 7.给容器改名

``` sh
docker rename fervent_newton centos-node
```
#### 8.登陆容器

``` sh
docker exec -it centos-node /bin/bash
```
#### 9.退出容器

``` sh
exit
```

### 创建自己的镜像

``` sh
1.登陆容器
docker exec -it centos-node /bin/bash

2.下载wget
yum install -y wget

3.下载解压nodejs
cd /usr/local/src/
wget https://nodejs.org/dist/v6.11.3/node-v6.11.3-linux-x64.tar.xz
xz -d node-v6.11.3-linux-x64.tar.xz
tar -xvf  node-v6.11.3-linux-x64.tar

4.将node添加到环境变量
vi /etc/profile
在末尾添加
export PATH="/usr/src/node-v6.11.3-linux-x64/bin:$PATH"
保存退出并重新编译
source /etc/profile
查看环境变量
echo $PATH
重启容器
exit

5.制作镜像
docker ps -l
docker commit containerid cdinit/centos-node 
//以contrainerid这个container制作cdnint/node这个镜像,注意必须以 “用户名/” 开头，否则push不上去
docker images
docker push cdinit/node

6.在新环境中使用镜像
    6.1 安装docker
    6.2 下载 docker pull cdinit/centos-node (自己创建的镜像)
    6.3 创建容器
        docker run --name node -idt cdinit/centos-node /bin/bash
    6.4 最多的还是要用到端口映射和路径映射
        docker run -dit -v /home:/home -p 80:80 cdinit/centos-node /bin/bash
    6.5 因为有环境变量，所以需要重新source一下/etc/profile
    
```

折腾了半天，终于可以使用自制镜像了。

### 映射端口和路径

``` sh
端口映射
docker run -d -it -p 80:80 cdinit/node /bin/bash

端口映射 && 路径映射
docker run -dit -v /home:/home -p 80:80  cdinit/node /bin/bash
映射了路径后，在docker伪终端中新建删除都会影响真实路径的文件

```

#### 容器指定ip地址

(官方文档在这里)[https://docs.docker.com/engine/userguide/networking/work-with-networks/#create-networks]

``` sh

方便程序在多个容器之间的访问

创建一个名为docker1的网络
docker network -o "com.docker.network.bridge.name"="docker1" --subnet 172.20.0.0/16 docker1
新建一个容器并指定ip
docker run --network=docker1 --ip=172.20.0.188 -itd --name=node3 cdinit/centos-node
进入docker并安装net-tools
yum install -y net-tools
查看ip
ifconfig

可以看见ip已经为设置的ip了
```