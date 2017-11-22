---
layout:     	post
title:        	"Redis Distributed Lock"
subtitle:     	"Redis 分布式锁"
date:         	2017-11-22
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
catalog:      	true
tags:
    - Redis
    - 缓存
---



## 实现方式

0: setnx(), expire(), del() 悲观锁模式

	缺点: expire() 执行前宕机, 有概率会导致死锁
	
1: setnx(), get(), getset(), del() 悲观锁模式

	缺点: 如果多台服务器本地时间不一致容易出现过期时间判断问题
	
2: set(SET KEY VALUE EX 5秒 NX), get(), del() [推荐使用] 悲观锁模式

	参考: http://doc.redisfans.com/string/set.html
	
3: watch(), multi(), exec() 乐观锁模式


***


## 缺点

 描述						| 解决方案
----------------------------|------------------------------------------------------------------------------------
锁失效时间无法合理评估 		| 	
客户端误删 					| 避免客户端误删, 利用 value 进行对比, 先 GET KEY 判断 value 匹配才 DEL KEY
宕机, 锁数据会丢失 			| 	
单机存在单点故障 			| 集群部署 (Master-Slave 主从不行, 因为主从数据是异步复制)		 


***


## 可重入锁的实现

使用 Jedis 封装分布式锁, 目前的做法是基于 Thread (ThreadLocal<LockDataVo> 或者 ConcurrentHashMap<Thread, LockDataVo>) 单进程单线程的可重入锁实现


***


### 注意的点

使用 Jedis 封装分布式锁, 在获取锁的时候, 利用重复尝试方式, 会需要 hold 住 Jedis 实例对象, 如果单次操作比较耗时的话, 这会引起 JedisPool 的连接数不够用, 建议加大连接数 

应该尽可能的使用 Lua Script 来组合命令保证原子性, 但是腾讯云的 伪Redis 是不支持 Lua Script. (蛋疼...)


***


## 参考实现

* <a href="http://curator.apache.org/" target="_blank">Apache Curator 分布式锁实现</a> 
* <a href="https://github.com/redisson/redisson/wiki/8.-Distributed-locks-and-synchronizers" target="_blank">redisson Distributed locks</a> (基于 Lua Script)


***


## 扩展阅读(深度思考)

* <a href="http://zhangtielei.com/posts/blog-redlock-reasoning.html" target="_blank">基于Redis的分布式锁到底安全吗（上）？</a> 
* <a href="http://zhangtielei.com/posts/blog-redlock-reasoning-part2.html" target="_blank">基于Redis的分布式锁到底安全吗（下）？</a> 


***

		