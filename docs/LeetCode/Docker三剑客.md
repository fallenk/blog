---
title: Docker三剑客
date: 2018-11-28 10:37:36
tags: [编程, 入门]
category: [Docker容器]
---
## 前言
理解Docker的三个核心概念：镜像，容器，仓库；

1. 开发人员使用Dockerfile定制镜像，使用docker-compose在`.yml`文件中配置所有容器的部署方法、文件映射、容器链接等，运行`docker-compose up`执行安装容器并且自动部署。
2. 运维人员从仓库下载容器，运行脚本。
<!--more-->
因此下面将介绍具体的操作流程。

# Docker Compose 项目
`Docker Compose` 是 `Docker` 官方编排（Orchestration）项目之一，负责**快速的部署分布式应用**。

## Compose 简介
Compose 项目是 Docker 官方的开源项目，负责**实现对 Docker 容器集群的快速编排**。从功能上看，跟 OpenStack 中的 Heat 十分类似。

Compose 定位是 **「定义和运行多个 Docker 容器的应用（Defining and running multi-container Docker applications）」**，其前身是开源项目 Fig。

我们知道使用一个 Dockerfile 模板文件，可以让用户很方便的定义一个单独的应用容器。然而，在日常工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个 Web 项目，除了 Web 服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

`Compose` 恰好满足了这样的需求。**它允许用户通过一个单独的 `docker-compose.yml` 模板文件（`YAML` 格式）来定义一组相关联的应用容器为一个项目（project）。**
Compose 中有两个重要的概念：

- 服务 (service)：一个应用的容器，实际上可以包括若干**运行相同镜像的容器实例**。
- 项目 (project)：**由一组关联的应用容器组成的一个完整业务单元**，在 `docker-compose.yml` 文件中定义。

`Compose` 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理。

Compose 项目由 Python 编写，实现上调用了 Docker 服务提供的 API 来对容器进行管理。因此，只要所操作的平台支持 Docker API，就可以在其上利用 Compose 来进行编排管理。

## 安装与卸载
Compose 支持 Linux、macOS、Windows 10 三大平台。

Compose 可以通过 Python 的包管理工具 pip 进行安装，也可以直接下载编译好的二进制文件使用，甚至能够直接在 Docker 容器中运行。

前两种方式是传统方式，适合本地环境下安装使用；最后一种方式则不破坏系统环境，更适合云计算场景。

Docker for Mac 、Docker for Windows 自带 docker-compose 二进制文件，安装 Docker 之后可以直接使用。

```
$ docker-compose --version

docker-compose version 1.17.1, build 6d101fb
```

## 使用
###术语
首先介绍几个术语。

- 服务 (service)：一个应用容器，实际上可以运行多个相同镜像的实例。
- 项目 (project)：由一组关联的应用容器组成的一个完整业务单元。

可见，一个项目可以由多个服务（容器）关联而成，Compose 面向项目进行管理。
### 场景
最常见的项目是 web 网站，该项目应该包含 web 应用和缓存。

下面我们用 Python 来建立一个能够记录页面访问次数的 web 网站。
### web 应用
新建文件夹，在该目录中编写 app.py 文件

```
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! 该页面已被访问 {} 次。\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", debug=True)
```
### Dockerfile
编写 Dockerfile 文件，内容为

```
FROM python:3.6-alpine
ADD . /code
WORKDIR /code
RUN pip install redis flask
CMD ["python", "app.py"]
```
### docker-compose.yml
编写 docker-compose.yml 文件，这个是 Compose 使用的主模板文件。

```
version: '3'
services:

  web:
    build: .
    ports:
     - "5000:5000"

  redis:
    image: "redis:alpine"
```
### 运行 compose 项目
`$ docker-compose up`

此时访问本地 5000 端口，每次刷新页面，计数就会加 1。


