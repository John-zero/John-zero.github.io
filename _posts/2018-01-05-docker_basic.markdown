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
	基础:
		概念
		命令
		使用
		日志
		
	进阶:
		Docker Compose: https://docs.docker.com/compose/overview/
		Docker Swarm: https://docs.docker.com/swarm/overview/
		Docker Registry: https://docs.docker.com/registry/
		
		Docker Network: https://docs.docker.com/engine/reference/commandline/network_create/
		Docker Volume: https://docs.docker.com/engine/reference/commandline/volume_create/
		
	高级:
		开源项目
		监控
		K8S Kubernetes
		腾讯云容器服务
		阿里云容器服务
		
	生态:
		编排和调度
			K8S Kubernetes
			Dokcer Swarm
			Mesosphere DC/OS
			Rancher
		持续集成/持续部署 (CI/CD)
			Jenkins
			GitLab CI
		监控
			Sumo Logic
			Prometheus
			Sysdig
			Sysdig Monitor
			Google cAdvisor
		记录
			Logspout
			Fluentd
			Logstash
			syslog-ng
		安全
			Clair
			Docker Notary
		储存/卷管理
			Convoy
			Portworx
			Blockbridge
			Flocker
		联网
			flannel
			Weaveworks
			Project Calico
		服务发现
			Consul
			Etcd
			Proxy
		构建
			Packer
			Whales
			Gradle
		管理
			Portainer
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


## 涉及目录

	/var/lib/docker
		containers 目录: /var/lib/docker/containers
		images 目录: /var/lib/docker/image
		network 目录: /var/lib/docker/network
		...
	
	/var/docker/packages
	
	/etc/docker/
		配置文件: /etc/docker/daemon.json
		KEY 文件: /etc/docker/key.json
		...
	...

***


## Docker 命令

	CentOS 7 安装 Docker
		sudo yum install -y docker

	Docker 开机自启
		chkconfig docker on

	Docker 手动启动
		service docker start
		
	Docker 手动停止
		service docker stop
		
	Docker 版本
		docker version

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

	编写 Dockerfile, 然后基于 Dockerfile 构建 Images, 然后运行 Images 成为 Containers. 

	使用 DockerFile 自动构建 Images
		编写 Dockerfile 指令:
			FROM, RUN, CMD, LABEL, MAINTAINER, EXPOSE, ENV, ADD, COPY, ENTRYPOINT, VOLUME, USER, WORKDIR, ...	
		
		参考 Dockerfile reference : https://docs.docker.com/engine/reference/builder/		
		GitHub Dockerfile : https://github.com/dockerfile
		GitHub Dockerfile io : http://dockerfile.github.io/
	
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


## 日志
	
	日志储存位置:
		1. 容器内 (默认)
		2. 挂载映射到物理磁盘
		3. 日志收集平台
		
	日志类型:
		1. 标准输出: stdout (正常标准输出), stderr (异常标准输出)
			比如 Java 的 System.out.println(""); System.err.println(""); ...
		2. 日志文件
			比如 Log4j2 的 .log 磁盘文件日志
			
	Docker 日志驱动:
		参考: https://docs.docker.com/engine/admin/logging/overview/#supported-logging-drivers
		1. none
		2. json-file (默认)
		3. syslog
		4. journald
		5. gelf
		6. ...

	日志: 
		Docker 守护进程日志
			参考: 
				https://docs.docker.com/engine/admin/#read-the-logs
				https://docs.docker.com/engine/admin/logging/view_container_logs/
				
			CentOS:
				journalctl -u docker.service
				
			docker service logs
				参考: https://docs.docker.com/engine/reference/commandline/service_logs/
			
		Docker 容器日志
			docker logs 容器ID/容器NAME (docker logs mysql)
				参考: https://docs.docker.com/engine/reference/commandline/logs/
			
		应用日志
			建议将日志收集到统一的日志平台, 不建议使用 标准输出
			这里有两种方式:
				参考: https://docs.docker.com/engine/admin/logging/gelf/
				1. dockerd 作用于所有容器, 也就是改变默认配置
					比如:
						dockerd
						  --log-driver gelf –-log-opt gelf-address=udp://1.2.3.4:12201 \
				2. 作用于单个容器
					比如:
						docker run \
						  --log-driver gelf –-log-opt gelf-address=udp://1.2.3.4:12201 \
						  alpine echo hello world
		
***	
	