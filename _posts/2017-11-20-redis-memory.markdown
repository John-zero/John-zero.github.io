---
layout:     	post
title:        	"Redis Memory"
subtitle:     	"Redis 内存"
date:         	2017-11-??
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
catalog:      	true
tags:
    - Redis
    - 缓存
---


## 内存监控

**info 监控**

 类型 		| 属性 | 描述
------------|-------------------------------|---------------------------------------------------------------------------
Clients 	| 								|
			| connected_clients 			| 已连接客户端的数量
			|								|
			|								|
Memory 		| 								|
			|								|
			| used_memory 					| 由 Redis 分配器分配的内存总量, 以字节 (byte) 为单位
			| used_memory_rss 				| 从操作系统的角度, 返回 Redis 已分配的内存总量 (俗称常驻集大小)
			| mem_fragmentation_ratio 		| used_memory_rss 和 used_memory 之间的比率
			|								|
			|								|
			|								|
			|								|
			
	一般重点关注: used_memory, used_memory_rss, mem_fragmentation_ratio
			
**客户端监控**	

	client list

	注意 输入缓冲区 qbuf (总容量), qbuf-free (剩余容量)

		Redis 会为每个客户端都分配输入缓冲区, 也就是将客户端发送的命令临时保存, 等 Redis 来获取执行 
		
		造成该缓冲区过大的原因一般是 Redis 的处理速度跟不上输入缓冲区的输入速度, 
			并且每次输入缓冲区的命令还包含了大量的 bigkey 对象

		该缓冲区使用不当会导致
			该缓冲区大小不能超过 1G, 不然会导致客户端连接被关闭
			该缓冲区不受 maxmemory 控制
	
	注意 输出缓冲区 obl (固定缓冲区长度), oll (动态缓冲区列表长度), omem (使用字节数)	

		Redis 会为每个客户端都分配输出缓冲区, 也就是将命令的执行结果返回给客户端, 等 Redis 和 客户端交互返回结果提供缓冲
		
		obl 返回比较小的执行结果 (使用的是 字节数组 缓存)
		
		oll 返回比较大的执行结果 (使用的是 列表 缓存), 比如 HGETALL, SMEMBERS 等
		
		
	注意客户端存活状态
		
		age 客户端连接的时间
		idle 最近一次空闲时间
		
		如果最近一次空闲时间太大, 那么就可以考虑优化客户端的连接数, 减少客户端连接数量

***

## 内存消耗

	内存消耗主要包括: 自身内存 + 对象内存 + 缓冲内存 + 碎片内存.
		而缓冲内存主要包括: 客户端缓冲, 复制积压缓冲区, AOF 缓冲区.


***

## 内存分配

	内存分配方式: jemalloc (默认), glibc, tcmalloc
		jemalloc 
			着重于减少内存碎片和支持可伸缩的并发性
			基于申请内存的大小把内存分配分为三个等级:  (对于 64位 系统, 一般页大小为 4K, chunk 大小为 4M)
				Small 的 size 以 8byte, 16 [规则: 16, 32, 48, ..., 128], 32, 64, 128, 256, 512分隔开, 小于页大小
				Large 的 size 以分页 [规则: 4KiB, 8, 12, ..., 4072]为单位, 等差间隔排列, 小于 chunk 的大小
				Huge 的大小是 chunk [规则: 4MiB, 8, 12, 16, ...] 大小的整数倍
		glibc
			...
		tcmalloc
			...
	
	内存管理和分配
		动态内存管理方式
			最大的好处就是能够较为充分的利用内存空间, 减少内存碎片化, 
			与此同时带来的劣势就是容易引起频繁的内存抖动
			通常采用 "空间预分配" 和 "惰性空间释放" 两种优化策略来减少内存抖动
		内存管理的优化
			最基本的出发点就是浪费一点空间还是牺牲一些时间的权衡
			像 STL, tcmalloc, protobuf3的arena机制等采用的核心思路都是 "预分配迟回收"

***
		
## 内存碎片		
		
	内存碎片解决方案:
		安全重启, 重启节点可以对内存碎片重新规整. 
			比如 Sentinel 或者 Cluster, 将碎片率过高的主节点切为从节点, 进行安全重启	

***

## 内存限制

	Redis 默认是无限使用服务器内存

	在 redis.conf 中 maxmemory 来限制内存, 
	需要注意的是该值限制的只是 Redis 实际使用的内存量, 也就是 used_memory
	由于内存碎片的存在, 实际消耗的内存可能远比 maxmemory 设置的更大.

***

## 内存回收策略
	
	删除到达过期时间的键对象
		惰性删除: 客户端读取带有超时属性的键时会主动检查是否过期, 过期则删除该键并返回空
		定时任务删除: 10秒运行间隔的定时任务, 根据键的过期比率和使用快慢两种速率模式回收键
	
***

## 内存溢出控制策略	
	内存所使用达到 maxmemory 上限时触发内存溢出的控制策略
		在 redis.conf 中 maxmemory-policy 策略控制
			noeviction: 默认策略, 不删除数据, 拒绝写响应读, 写返回 (error) OOM command not allowed when used memory
			volatile-lru: 根据 LRU 算法删除设置了超时属性的键, 直到腾出足够空间为止
				如果没有可删除的键对象, 回退到 noeviction 策略
			allkeys-lru: 根据 LRU 算法删除键, 不管有没有设置超时属性, 直到腾出足够空间为止
			allkeys-random: 随机删除所有键, 直到腾出足够空间为止			
			volatile-random: 随机删除过期键, 直到腾出足够空间为止
			volatile-ttl: 根据键值对象的 TTL 属性, 删除最近将要过期数据
				如果没有可删除的键对象, 回退到 noeviction 策略
			
***

## 内存优化
	1. scan + object idletime 命令批量查询哪些键长时间未被访问, 然后对其进行删除清理
	2. 压缩 KEY 和 VALUE
		二进制压缩 (Google Protostuff, kryo, hessian)
		JSON (压缩: Google Snappy, GZIP)
		XML


***


		