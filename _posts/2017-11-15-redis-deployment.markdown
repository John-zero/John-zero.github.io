---
layout:     	post
title:      	"Redis 部署搭建"
subtitle:   	"单机, 哨兵, 集群"
date:       	2017-11-15
author:       	"John-zero"
header-img: 	"img/post-bg-js-module.jpg"
catalog: 		false
tags:
    - Redis
    - 缓存
---



<a href="https://redis.io/download" target="_blank">官网 Download</a>

***

## Redis Standalone (单机)

#### 安装

1. 源码安装

	参考: <a href="https://redis.io/download#installation" target="_blank">Redis Install</a>


#### 配置文件
	
* <a href="https://redis.io/topics/config" target="_blank">Redis Config</a>

* 密码设置
	
	打开 redis.conf, 找到 # requirepass foobared, 去掉 #, 改为 requirepass 123456

	
#### 运行

1. 源码运行

	1.1 默认配置

		redis-server

		(不推荐. 默认配置不适合生产环境, 生产环境基本都会对配置进行自定义调整)

	1.2 运行配置

		redis-server --port 6379

		(不推荐. 如果需要修改的配置较多, 则不方便后续的可维护性)

	1.3 配置文件

		redis-server /opt/redis/redis.conf

		(推荐, 直接指定自定义配置, 方便后续可维护性)

2. Docker 运行

	2.1 默认配置

		docker run -d -ti -p 6379:6379 --name redis redis

	2.2 指定参数 (参考: <a href="https://store.docker.com/images/redis" target="_blank">Docker Store Redis</a>)
	
		docker run -d -ti -p 6379:6379 --name redis redis -v /opt/redis/redis.conf:/etc/redis/redis.conf -v /data/redis/data:/data --restart always redis-server /etc/redis/redis.conf

		折断:
			docker run -d -ti -p 6379:6379 --name redis redis \ 
			-v /opt/redis/redis.conf:/etc/redis/redis.conf \
			-v /data/redis/data:/data \
			--restart always \
			redis-server /etc/redis/redis.conf

	命令 											| 说明
	------------------------------------------------|-----------------------
	docker run  									| 运行 (参考: <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank">docker run</a>)
	-d 												|
	-ti												|
	-p 6379:6379 									|
	--name redis 									| 容器名称 
	redis  											| Docker Redis Image 名称
	-v /opt/redis/redis.conf:/etc/redis/redis.conf  | 配置文件映射 (/opt/redis/redis.conf 为自定义配置文件路径)
	-v /data/redis/data:/data 						| data 挂载 (/data/redis/data 为自定义挂载路径)
	--restart always 								| 重启策略 (参考: <a href="https://docs.docker.com/engine/reference/commandline/run/#restart-policies-restart" target="_blank">Restart policy</a>)
	redis-server /etc/redis/redis.conf 				|


#### 连接
	
* 参考: <a href="https://redis.io/topics/rediscli" target="_blank">Redis Client</a>
	
* 格式: 
	
		redis-cli [-h {HOST}] [-p {PORT}] [-a 'password']
		
			没有 -h 参数则默认 127.0.0.1
			没有 -p 参数则默认 6379
	
	例如: (当然, 没有设置密码, 就不需要密码授权了)
			
		redis-cli -h 127.0.0.1 -p 6379 -a '123456'
		
		redis-cli -h 127.0.0.1 -p 6379
			配合授权命令: auth '123456' 也可以
		

#### 停止
	
* 无密码:

		redis-cli shutdown
		
			断开与客户端的连接, 然后生成持久化文件
				如果没有开启 AOP 持久化功能则自动执行 bgsave (RDB)
	
* 有密码: 
	
		redis-cli -h HOST -p PORT -a password shutdown
		
* 强杀:
		
		kill -9 {Redis Service PID}
			(不推荐强制杀死 Redis 服务, 会导致一些无法预测的情况)
	
***		

### Redis Sentinel (哨兵)

***

### Redis Cluster (集群)

***

