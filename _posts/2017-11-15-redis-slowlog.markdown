---
layout:     	post
title:        	"Redis Slowlog"
subtitle:     	""
date:         	2017-11-15 16:00:00
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
tags:
    - Redis
    - 缓存
---


**Redis 慢查询 SLOWLOG**

<a href="https://redis.io/commands/slowlog" target="_blank">官网 SLOWLOG 命令</a>

日期 		| 更新记录
------------|-----------------------
2017-11-08	| 初始化 慢查询 SLOWLOG

***

## **命令执行过程**

	1.1. 客户端发送命令
	
	1.2. 命令在 Redis 排队
	
	1.3. 命令在 Redis 执行 (注意: 慢查询只统计此步骤)
	
	1.4. 客户端接收返回结果
	
	
	注意: 没有慢查询不代表客户端没有超时, 因为整个流程还涉及客户端和网络通信延迟等等...	
		
***
</br>
		
## **配置参数设置**
			
	2.1 slowlog-log-slower-than (预设阈值, 单位: 微妙, 默认 10000 [10秒], [1秒 = 1000毫秒 = 1000000微妙])
	
		slowlog-log-slower-than 10000

	2.2 slowlog-max-len (慢查询最大记录数, 默认 128)
	
		slowlog-max-len 128
		
***
</br>
		
## **储存方式**

	Redis 使用一个 内存列表 来储存慢查询日志, slowlog-max-len 就是限制该列表的最大长度 (FIFO 队列, 先进先出)
		
***
</br>
		
## **慢查询日志组成**
	
	标识ID, 发生时间戳, 命令耗时, 执行命令和参数	
		
***
</br>
		
## **访问和管理**

	5.1 参数配置

		5.1.1 redis.conf 配置文件配置
		
			slowlog-log-slower-than 1000
			
			slowlog-max-len 1000
			
		
		5.1.2 命令配置
	
			CONFIG SET slowlog-log-slower-than 100 // 100毫秒

			CONFIG SET slowlog-max-len 1000 //1000 条
			
			CONFIG REWRITE

	5.2 查询配置
	
		CONFIG GET slowlog-log-slower-than

		CONFIG GET slowlog-max-len
		
	5.3 查看慢查询日志列表

		SLOWLOG GET [N]

		例如:
			SLOWLOG GET
			SLOWLOG GET 100
		
	5.4 查看慢查询日志当前记录长度

		SLOWLOG LEN
		
	5.5 清空慢查询日志
	
		SLOWLOG RESET
		
		
***
</br>
	
	
	