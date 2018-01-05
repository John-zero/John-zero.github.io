---
layout:     	post
title:        	"Docker 基础"
subtitle:     	"Docker 基础: 基本原理, 基础命令, 镜像构建运行"
date:         	2018-01-05
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Docker
---


<!--
	进阶:
		Docker Compose: https://docs.docker.com/compose/overview/
		Docker Swarm: https://docs.docker.com/swarm/overview/
		Docker Registry: https://docs.docker.com/registry/
		
		Docker Network: https://docs.docker.com/engine/reference/commandline/network_create/
		Docker Volume: https://docs.docker.com/engine/reference/commandline/volume_create/
		
	高级:
		开源项目
		K8S Kubernetes
		腾讯云容器服务
		阿里云容器服务
-->


## 涉及站点

Docker 官网: <a href="https://www.docker.com/" target="_blank">https://www.docker.com/</a>

Docker 文档: <a href="https://docs.docker.com/get-started/" target="_blank">https://docs.docker.com/get-started/</a>

Docker Hub: <a href="https://www.docker.com/" target="_blank">https://hub.docker.com/</a>

Docker 中文社区: <a href="http://dockone.io/" target="_blank">http://dockone.io/</a>

GitHub Docker Resources (Star: 1.2K +): https://github.com/hangyan/docker-resources
	
***


## 基本概念

	...

***


## Docker 命令

	安装
		CentOS 7 : sudo yum install -y docker

	参考 Docker base command : https://docs.docker.com/engine/reference/commandline/docker/

	docker info # 查看 docker 详细信息
	
	docker search *** # 搜索 镜像名称 中包含 *** 的镜像 (Images)
		docker search nginx
		docker search redis
		
	docker pull *** # 下载镜像
		docker pull redis
		docker pull redis:latest
		
		参考 Docker Hub --> Redis : https://hub.docker.com/_/redis/
		
	docker images # 查看已下载镜像列表	
		
	docker run *** # 运行镜像
		docker run --name redis -d -p 6379:6379 redis
		
	docker ps
		docker ps # 默认显示当前运行容器
		docker ps -a # 显示全部容器
		docker ps --help # 命令帮助
		
	docker stop	容器ID/容器NAME # 停止容器
		
	docker start 容器ID/容器NAME # 启动容器
		
	docker logs 容器ID/容器NAME # 查看容器日志
		
	docker rm 容器ID/容器NAME # 移除容器
	
	docker rmi 镜像ID/镜像NAME # 移除镜像
		
	... 还有 N 多命令...
	
***


## DockerFile, Images, Containers 关系

	编写 Dockerfile, 然后使用 Dockerfile 构建 Images, 然后使用 Images 运行成为 Containers. 

	使用 DockerFile 自动构建 Images
		编写 Dockerfile 指令:
			FROM, RUN, CMD, LABEL, MAINTAINER, EXPOSE, ENV, ADD, COPY, ENTRYPOINT, VOLUME, USER, WORKDIR, ...	
		
		参考 Dockerfile reference : https://docs.docker.com/engine/reference/builder/			
	
	使用 docker build 构建 Images
		docker build [OPTIONS] PATH | URL | -
		举例命令:
			docker build .  # 使用当前目录的 Dockerfile 构建 Images
		
		参考 Docker build reference : https://docs.docker.com/engine/reference/commandline/build/
		
	使用 docker run 运行 Images, 运行后就是 Containers
		docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
		举例命令:
			docker run --name redis -d -p 6379:6379 redis  # 运行 redis Images, 并且指定暴露端口 
		
		参考 Docker run reference : https://docs.docker.com/engine/reference/commandline/run/
		
***


## 加速器

	阿里云 加速器: https://cr.console.aliyun.com/#/accelerator
	DaoCloud 加速器: https://www.daocloud.io/mirror#accelerator-doc

***
	