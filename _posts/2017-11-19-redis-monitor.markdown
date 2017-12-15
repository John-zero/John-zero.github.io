---
layout:     	post
title:        	"Redis Monitor"
subtitle:     	"Redis 运维监控"
date:         	2017-11-19
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Redis
---


## 单机监控项

**监控数据来源**

&#8194;&#8194; info 命令 <a href="https://redis.io/commands/info" target="_blank">(官网 info)</a> 
	
**建议监控属性**

 类型 		| 属性 | 描述
------------|-------------------------------|---------------------------------------------------------------------------	
Server 		| 服务器信息					|
			|								|
Clients 	| 客户端信息					|
			| connected_clients 			| 已连接客户端的数量
			| client_longest_output_list	| 当前所有输出缓冲区中队列队形个数的最大值
			| client_biggest_input_buf	 	| 当前所有输入缓冲区中占用的最大容量
			| blocked_clients				| 正在等待阻塞命令的客户端数量
			|								|
Memory 		| 内存信息						|
			| used_memory 					| 由 Redis 分配器分配的内存总量, 以字节 (byte) 为单位
			| used_memory_human				|
			| used_memory_rss 				| 从操作系统的角度, 返回 Redis 已分配的内存总量 (俗称常驻集大小)
			| used_memory_rss_human			| 
			| used_memory_peak				|
			| used_memory_peak_human		|
			| used_memory_peak_perc			|
			| used_memory_overhead			|
			| used_memory_startup			|
			| used_memory_dataset			|
			| used_memory_dataset_perc		|
			| total_system_memory			|
			| total_system_memory_human		|
			| used_memory_lua				|
			| used_memory_lua_human			|
			| maxmemory						|
			| maxmemory_human				|
			| maxmemory_policy				|
			| mem_fragmentation_ratio 		| used_memory_rss 和 used_memory 之间的比率
			| mem_allocator					|
			| active_defrag_running	 		|
			| lazyfree_pending_objects		|
			|								|
Persistence	| 持久化信息 					|	
			| loading						| 是否在加载持久化文件, 0:否; 1:是
			|								|
Stats 		| 全局统计信息	 				|
			| total_commands_processed 		| 采集周期内执行命令总数
			| rejected_connections 			| 采集周期内拒绝连接总数
			| expired_keys 					| 采集周期内过期 Key 总数
			| evicted_keys 					| 采集周期内踢出 Key 总数
			| keyspace_hits 				| 采集周期内 Key 命中总数	
			| keyspace_misses 				| 采集周期内 Key 拒绝总数
			| keyspace_hit_ratio 			| 访问命中率
			|								|
Replication | 复制信息						|
			|								|
CPU			| CPU 消耗信息					|
			| used_cpu_sys 					| Redis 服务器耗费的系统 CPU
			| used_cpu_user 				| Redis 服务器耗费的用户 CPU
			| used_cpu_sys_children 		| 后台进程耗费的系统 CPU
			| used_cpu_user_children 		| 后台进程耗费的用户 CPU
			|								|
Commandstats| 命令统计信息					|
			|								|		
Keyspace	| 数据库键统计信息				|
			| db0							|
			| db1							|
			| db...N						| keys=N,expires=N,avg_ttl=N (单位: 秒)
			| db15							|
		
**内存消耗**

	重点关注 mem_fragmentation_ratio, 
		当其值 > 1 时, 说明 used_memory_rss - used_memory 多出的部分被内存碎片消耗掉了
		当其值 < 1 时, 一般可能是 Redis 内存交换 (Swap) 到硬盘导致, 也需要关注到
		
		
**参考**

&#8194;&#8194;Open-Falcon Redis 监控脚本 <a href="https://github.com/iambocai/falcon-monit-scripts/tree/master/redis" target="_blank">redis-monitor</a>
	
&#8194;&#8194;Open-Falcon Redis 监控脚本 <a href="https://github.com/ZhuoRoger/redismon" target="_blank">redismon</a> 
	
***


## 慢查询 SLOWLOG

	SLOWLOG GET [N]

	例如:
		SLOWLOG GET
		SLOWLOG GET 100
		
	...	
	

## redis-cli --stat 实时统计

	redis-cli --stat
	
keys      | mem      | clients | blocked | requests            | connections
----------|----------|---------|---------|---------------------|------------
0         | 807.92K  | 1       | 0       | 4769 (+0)           | 4           
0         | 807.92K  | 1       | 0       | 4770 (+1)           | 4           
0         | 807.92K  | 1       | 0       | 4771 (+1)           | 4           
0         | 807.92K  | 1       | 0       | 4772 (+1)           | 4

***


## redis-cli --bigkeys 采样分析大对象

	redis-cli --bigkeys
	
	该命令使用 scan 命令对 Redis 的键采样分析, 从中统计分析找到内存占用比较大的键值.

***


## 更多监控等待挖掘...

	**持久化阻塞**
	**连接拒绝**
	**命中率**
	**CPU**
	**内存**
	**网络**
	**磁盘**
	**等等**

***

	
	