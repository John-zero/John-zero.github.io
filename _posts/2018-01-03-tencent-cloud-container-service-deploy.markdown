---
layout:     	post
title:        	"Tencent Cloud Container Service Deploy"
subtitle:     	"使用腾讯云容器服务快速部署"
date:         	2018-01-03
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - 容器服务
---



## 简化部署流程


![](/img/in-post/ccs-deploy/simplify-deploy.png)


***


## 简化流程描述

	1. Java Coder 使用 Git 将代码提交到 GitLab Repositories
	
	2. Java Coder 使用 Jenkins 主动触发打包构建, 然后会执行预定义的 .sh 脚本
		.sh 的脚本流程:
			①. 先使用 Gradle/Maven 构建打包
			②. Docker Build Imager (自定义 Dockerfile)
			③. Docker Push Imager To Repositories 
				(Docker Hub, 私有镜像仓库, 腾讯云镜像仓库, 阿里云镜像仓库 ...)
			④. 调用 Kubernetes HTTP API 创建/更新 服务
	
***


## 腾讯云容器服务-服务
			
![](/img/in-post/ccs-deploy/Cloud Container Service 流程.png)	
	
	
	自建 WEB 服务其实就是封装腾讯云提供的 HTTP API 操作, 
		对外(shell 脚本)暴露访问方式, 然后由该自建 WEB 服务访问腾讯云 ccs.api.qcloud.com 对应的 服务接口
		
	腾讯云 CCS, Kubernetes 是腾讯云的服务...

***


## 腾讯云容器服务

	1. 容器服务-集群
		在控制台预先创建好集群.
		
	2. Docker 镜像仓库
		提交 Docker Imager 到镜像仓库.

	3. 容器服务-服务
		服务使用 HTTP API 操作.
			自己折腾当然可以直接控制台一步一步的操作...
			Jenkins 的话就走 HTTP API 来操作...
		
	4. ...

***


## 相关文档

	腾讯云容器服务
		https://cloud.tencent.com/document/product/457
		
		容器服务-集群
			https://cloud.tencent.com/document/product/457/9090
		
		容器服务-服务
			https://cloud.tencent.com/document/product/457/9095
		
	腾讯云镜像仓库
		https://cloud.tencent.com/document/product/457/9118
	
	腾讯云容器服务 API
		https://cloud.tencent.com/document/api/457/9458
		
	...	

***

	