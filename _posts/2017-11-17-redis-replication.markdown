---
layout:     	post
title:        	"Redis Replication"
subtitle:     	"Redis 主从复制"
date:         	2017-11-17
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
tags:
    - Redis
    - 缓存
---



<a href="https://redis.io/topics/replication" target="_blank">官网 Replication</a>

***

## 简述

	master 主服务器.
	
	slave 从服务器.
	
	master 可以有多个 slave, 并且 slave 也可以有自己的 slave, 但是 slave 只归属一个 master.
	
	slave 可以升级为 master.
	
	复制是异步的(保证高性能), 存在延迟.
	
	slave 可以应用于数据冗余, 读写分离(master 只写, slave 只读), 分压持久化 (slave 负责持久化).
	
	...
	
***
	
## 建立主从	
	
	master 主机执行:						
		redis-server --port 6379
	
	slave 从机执行:
		redis-server --port 6380
	
		slaveof 127.0.0.1 6379
			提示 OK
		
		info replication
			# Replication
			role:slave
			master_host:127.0.0.1
			master_port:6379
			master_link_status:up

	master 主机执行:
		info replication
			# Replication
			role:master
			connected_slaves:1
			slave0:ip=127.0.0.1,port=6380,state=online,offset=14,lag=0

		SET redis master-slave
			提示 OK

	slave 从机执行:
		GET redis
			"master-slave"
			
		SET hello world	
			提示 (error) READONLY You can't write against a read only slave.
			意思就是 slave 只读
			这个由 redis.conf 的 slave-read-only yes 控制 (不建议修改该配置为 no)

	主从端口映射: (主从复制端口非固定)
		netstat
		
			Proto Recv-Q Send-Q Local Address           Foreign Address         State      
			tcp        0      0 localhost:57900         localhost:6380          ESTABLISHED
			tcp        0      0 localhost:6380          localhost:57900         ESTABLISHED
			tcp        0      0 localhost:43850         localhost:6379          ESTABLISHED
			tcp        0      0 localhost:43792         localhost:6379          ESTABLISHED
			tcp        0      0 localhost:6379          localhost:43792         ESTABLISHED
			tcp        0      0 localhost:6379          localhost:43850         ESTABLISHED
			
	注意:
		如果 master 的 redis.conf 开启权限验证 requirepass passwrod
		那么 slave 的 redis.conf 中的 masterauth <master-password> 也必须配置
	
***			

## 断开主从

	slave 从机执行:
		slaveof no one
		
		断开后, 该 slave 从机升级为 master 主节点
			
***
	
## 切换 master

	slave 从机执行:
		slaveof 127.0.0.2 6379
		
		切换后, 该 slave 会删除当前所有数据, 然后重新复制新 master 的数据, 
			注意可能会导致连接请求会被阻塞.

***		
		
## 数据复制

	注意: master 异步线程发送给 slave, slave 是主线程执行复制命令

	全量复制
		一般是发生在首次建立复制场景. 注意如果数据量过大, 会对主从节点和网络有很大开销影响
		
	部分复制
		用于处理在主从复制中因网络闪断等原因造成的数据丢失, 
		当从节点再次连接到主节点的时候, 主节点补发丢失数据给从节点.
			(从节点会每隔1秒会主动上报当前复制偏移量)
		
	master 主机执行:
		info replication
			# Replication
			role:master
			connected_slaves:1
			slave0:ip=127.0.0.1,port=6380,state=online,offset=14,lag=0
			
			可以看到 slave 当前复制的信息

***

## 主从心跳
	
	心跳频率在 redis.conf 中的 repl-ping-slave-period 10	

***	

## 应用场景

	读写分离
	
		存在问题: 
		
			主从数据延迟
				如果是强一致性读, 那么建议应用直接 master 中读吧
				
			读到过期数据 (V3.2 + 版本已经解决该问题)

			从节点故障
				客户端维护切换到其他 slave 从节点
					
	数据冗余
		
		当 master 主节点故障后, 将该 slave 从节点升级为 master 主节点来实现高可用高可靠
		
	持久化功能
		
		master 主节点专门负责处理, slave 负责持久化
		前提是业务能接受有延迟
		
		