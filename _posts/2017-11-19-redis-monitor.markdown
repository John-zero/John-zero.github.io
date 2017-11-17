---
layout:     	post
title:        	"Redis Monitor"
subtitle:     	"Redis 运维监控"
date:         	2017-11-19
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
tags:
    - Redis
    - 缓存
---


## 单机监控项

**监控数据来源**

	info 命令 <a href="https://redis.io/commands/info" target="_blank">(官网 info)</a> 
	
建议监控属性

 类型 		| 属性 | 描述
------------|-------------------------------|---------------------------------------------------------------------------	
Server 		| 								|
Clients 	| 								|
			| connected_clients 			| 已连接客户端的数量
Momory 		| 								|
			| used_memory 					| 由 Redis 分配器分配的内存总量, 以字节 (byte) 为单位
			| used_memory_rss 				| 从操作系统的角度, 返回 Redis 已分配的内存总量 (俗称常驻集大小)
			| mem_fragmentation_ratio 		| used_memory_rss 和 used_memory 之间的比率
Persistence	| 								|		
Stats 		| 								|
			| total_commands_processed 		| 采集周期内执行命令总数
			| rejected_connections 			| 采集周期内拒绝连接总数
			| expired_keys 					| 采集周期内过期 Key 总数
			| evicted_keys 					| 采集周期内踢出 Key 总数
			| keyspace_hits 				| 采集周期内 Key 命中总数	
			| keyspace_misses 				| 采集周期内 Key 拒绝总数
			| keyspace_hit_ratio 			| 访问命中率
Replication | 								|
CPU			| 								|
			| used_cpu_sys 					| Redis 服务器耗费的系统 CPU
			| used_cpu_user 				| Redis 服务器耗费的用户 CPU
			| used_cpu_sys_children 		| 后台进程耗费的系统 CPU
			| used_cpu_user_children 		| 后台进程耗费的用户 CPU
Keyspace	| 								|
			| db0							|
			| db1							|
			| db...N						| keys=N,expires=N,avg_ttl=N (单位: 秒)
			| db15							|
			
参考

	Open-Falcon Redis 监控脚本 <a href="https://github.com/iambocai/falcon-monit-scripts/tree/master/redis" target="_blank">redis-monitor</a>
	
	Open-Falcon Redis 监控脚本 <a href="https://github.com/ZhuoRoger/redismon" target="_blank">redismon</a> 
	
***

## redis-cli --stat 实时统计

	redis-cli --stat
	
keys      | mem      | clients | blocked | requests            | connections
----------|----------|---------|---------|---------------------|------------
0         | 807.92K  | 1       | 0       | 4769 (+0)           | 4           
0         | 807.92K  | 1       | 0       | 4770 (+1)           | 4           
0         | 807.92K  | 1       | 0       | 4771 (+1)           | 4           
0         | 807.92K  | 1       | 0       | 4772 (+1)           | 4

***

## redis-cli --bigkeys 采样分析

	redis-cli --bigkeys
	
	该命令使用 scan 命令对 Redis 的键采样分析, 从中统计分析找到内存占用比较大的键值.

***

## 更多监控等待挖掘...

***

	
	