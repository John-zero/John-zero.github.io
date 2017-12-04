---
layout:       post
title:        "Redis Persistence"
subtitle:     "Redis 持久化"
date:         2017-11-18
author:       "John-zero"
header-img:   "img/post-bg-android.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Redis
---



<a href="https://redis.io/topics/persistence" target="_blank">官网 Persistence</a>

***

>如果只是将 Redis 作为缓存, 那么是不建议开启持久化功能的.

***

## RDB (默认开启)

	相关配置: (V3.2)
		save 900 1
		save 300 10
		save 60 10000
		stop-writes-on-bgsave-error yes
		rdbcompression yes
		rdbchecksum yes
		dbfilename dump.rdb
		dir ./
	
	触发:
		自动触发配置:
			save m n 表示 m 秒内数据存在 n 次修改则自动触发 bgsave
		手动触发:
			bgsave (短阻塞 [fork阶段]) 和 save (长阻塞, 废弃)
			应用于定时备份机制, 比如控制每天0点备份就可以由应用程序远程手动触发一次	
		
	工作流程: (注意: 根据内存生成临时快照文件)
		bgsave
		父进程 fork 子进程, 注意 fork 操作过程中会阻塞 父进程, fork 操作完成后 父进程 才继续响应其他命令
		子进程 根据父进程内存生成 RDB 文件
		子进程 生成 RDB 文件成功后进行原子替换, 通知 父进程 任务完成	
		
	适用场景: 
		备份, 全量复制 
	
	优点:
		对性能影响小, fork 子进程
		生成 RDB 完整快照文件
		RDB 文件数据恢复比 AOF 数据恢复快很多
	
	缺点:
		快照是定期生成的, 没有办法实时持久化(秒级持久化), 在快照生成的时候存在丢失部分数据
		在单核, 数据集非常大的情况下, fork 子进程会耗时较久, 会影响到 Redis 主进程 (不过现在生产环境基本不存在单核的服务器). 
			并且 bgsave 每次都需要 fork 子进程, 属于重量级操作, 频繁操作成本过高
		RDB 文件使用特定二进制格式保存, 格式版本演进迭代, 存在老版本 Redis 无法兼容新版本 Redis 格式问题
		
	文件处理:
		保存:
			配置文件配置默认是  dir ./
			Redis 在线修改文件路径 config set dir {newDir}, 注意: 路径映射的是文件夹
		压缩:
			默认采用 LZF 算法对生成的 RDB 文件进行压缩处理, 压缩后的文件远小于内存大小, 默认开启	
        
	文件修复:
		加载 RDB 文件异常:
			Short read or OOM loading DB. Unrecoverable error, aborting now.
		检测:
			redis-check-dump 工具可以检测 RDB 文件并获取对应的错误报告

***

## AOF (append only file)

	相关配置: (V3.2)
		appendonly no
		appendfilename "appendonly.aof"
		appendfsync everysec
		no-appendfsync-on-rewrite no
		auto-aof-rewrite-percentage 100
		auto-aof-rewrite-min-size 64mb
		aof-load-truncated yes	

	默认关闭 appendonly no,  如果开启则配置: appendonly yes
	默认文件名配置: appendfilename "appendonly.aof"
	建议同时配置重写机制:
		auto-aof-rewrite-percentage 100
		auto-aof-rewrite-min-size 64mb
		不建议手动执行 BGREWRITEAOF 命令触发 AOF rewrite 功能	

	工作流程: (注意: 根据 aof_buf 缓冲区结合当前内存进行命令合并操作)
		命令写入 append (先写入 aof_buf 缓冲区)
		文件同步 sync (aof_buf 缓冲区同步文件有不同策略)
		文件重写 rewrite
		重启加载 load	

	AOF 策略:
		appendfsync no
			命令写入 aof_buf 后调用系统 write 操作, 不对 AOF 文件 fsync 同步
			同步操作由操作系统 OS 负责, 周期通常是 30秒
			速度最快
		appendfsync always
			每次操作命令写入 aof_buf 后立即调用系统 fsync 同步到 AOF 文件
			数据安全性最高, 但是速度最慢
		appendfsync everysec (默认)
			命令写入 aof_buf 后调用系统 write 操作, fsync 同步文件操作由专门线程每秒调用一次
			no 和 always 的折中做法, 兼顾性能和数据安全
			
		write 操作会触发延迟写 (delayed write) 机制
	    fsync 操作针对单个文件做强制硬盘同步操作, 会阻塞直到硬盘完成后才返回
		
	文件处理:
		保存:
			配置文件配置默认是  dir ./
			Redis 在线修改文件路径 config set dir {newDir}, 注意: 路径映射的是文件夹
		压缩:
			重写机制

	重写机制:
		AOF 文件重写是把 Redis 进程内的数据转化为写命令同步到新的 AOF 文件的过程
		重写目的:
			降低文件占用空间 (不写入超时数据, 只保留最终数据, 合并命令)
			AOF 文件更快地被 Redis 加载	
		触发规则:
			手动: 调用 bgrewriteaof 命令
			自动: 配置 auto-aof-rewrite-percentage 和 auto-aof-rewrite-min-size

	优点:
		最安全, 数据基本可以保证不会丢失(除非 OS flush 文件异常)
		AOF 文件在发生断电等情况下导致某条日志只写入一半的情况下使用 redis-check-aof 工具也能修复
		AOF 文件易读, 可修改, 直接 文本协议格式 写入	

	缺点:
		AOF 文件通常会比 RDB 文件更大
		性能消耗比 RDB 高
		数据恢复比 RDB 慢
		
	注意:
		AOF 在 Redis 服务运行一段时间后才开启的话, 是不适合应用于备份场景的, 因为没有开启前的操作日志
		
***


## 相同点

 点 			| RDB 																		| AOF
----------------|---------------------------------------------------------------------------|---------------------------------------------------------------------------	
工作流程		| fork 子进程																| fork 子进程
保存路径		| dir ./																	| dir ./

***


## 不同点

 点 			| RDB 																		| AOF
----------------|---------------------------------------------------------------------------|---------------------------------------------------------------------------	
工作流程		| 会判断当前是否有正在执行的 RDB / AOF 子进程								| 只判断当前是否有正在执行的 AOF 子进程
操作对象		| 父进程内存 																| aof_buf 缓冲区
压缩			| LZF 算法压缩, rdbcompression yes											| 重写机制
储存协议格式	| 紧凑压缩的二进制数据快照													| 文本协议
大小			| 小																		| 大
性能消耗比		| 低 (看配置)																| 高 (看配置)
数据恢复速度	| 快																		| 慢


***