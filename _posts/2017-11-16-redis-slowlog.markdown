---
layout:     	post
title:        	"Redis Slowlog"
subtitle:     	""
date:         	2017-11-16
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
catalog:      	true
tags:
    - Redis
    - 缓存
---



<a href="https://redis.io/commands/slowlog" target="_blank">官网 SLOWLOG</a>

***

## 命令执行过程

	1. 客户端发送命令
	2. 命令在 Redis 排队
	3. 命令在 Redis 执行 (注意: 慢查询只统计此步骤)
	4. 客户端接收返回结果

注意: 没有慢查询不代表客户端没有超时, 因为整个流程还涉及客户端和网络通信延迟等等...	
		
***
		
## 配置参数设置
			
	1. slowlog-log-slower-than (预设阈值, 单位: 微妙, 默认 10000[10秒], [1秒 = 1000毫秒 = 1000000微秒])
	
		slowlog-log-slower-than 10000

	2. slowlog-max-len (慢查询最大记录数, 默认 128)
	
		slowlog-max-len 128
		
***
		
## 储存方式

	Redis 使用一个 内存列表 来储存慢查询日志
	
	slowlog-max-len 就是限制该列表的最大长度 (FIFO 队列, 先进先出)
		
***
		
## 慢查询日志组成
	
	标识ID, 发生时间戳, 命令耗时, 执行命令和参数	
		
***
		
## 访问和管理

	1. 参数配置

		1.1 redis.conf 配置文件配置
		
			slowlog-log-slower-than 10000
			
			slowlog-max-len 128
			
		
		1.2 命令配置
	
			CONFIG SET slowlog-log-slower-than 10000 // 10000微秒, 10毫秒

			CONFIG SET slowlog-max-len 1000 //1000 条
			
			CONFIG REWRITE

	2. 查询配置
	
		CONFIG GET slowlog-log-slower-than

		CONFIG GET slowlog-max-len
		
	3. 查看慢查询日志列表 (耗时单位: 微秒)

		SLOWLOG GET [N]

		例如:
			SLOWLOG GET
			SLOWLOG GET 100
		
	4. 查看慢查询日志当前记录长度

		SLOWLOG LEN
		
	5. 清空慢查询日志
	
		SLOWLOG RESET
		
		
***

## 排查思路 (Redis 内部原因)

	1. info commandstats
	
		查看命令统计信息, 
			注意是否用了时间复杂度为 O(N) 类似的命令, 比如 HGETALL, SMEMBERS 等等
			注意 usec_pre_call 耗时是否合理, 单位: 微妙
			
	
	2. API或者结构使用不合理
		
		2.1 KEYS *
		
		2.2 SORT
		
		2.3 List, Hash, Set, Sorted Set 单 KEY 内元素过大
	
			比如 SET 单 KEY 内元素几十几百万, 使用 SMEMBERS [时间复杂度 O(N)] 的时候, 就必然会导致慢查询
			
		2.4 ...	

	3. 等等	

			
***

## 排查思路 (系统原因)

	1. 系统 CPU 繁忙
	
	2. 内存交换
		redis-cli -p 6379 info server | grep process_id
			得到 process_id:12306
			
		cat /proc/12306/smaps | grep Swap
			得到 Swap: 0 kB
				 Swap: 4 kB
				 
			0 KB, 4KB 都是正常现象	 
	
	3. 磁盘过载
		iotop
		iostat -d -k
	
	4. 等等	
	
***	
	