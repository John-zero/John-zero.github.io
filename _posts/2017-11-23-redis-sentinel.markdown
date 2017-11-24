---
layout:     	post
title:        	"Redis Sentinel"
subtitle:     	"Redis 哨兵"
date:         	2017-11-23
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Redis
    - 缓存
---



<a href="https://redis.io/topics/sentinel" target="_blank">官网 Sentinel</a>
<a href="https://redis.io/topics/sentinel-clients" target="_blank">官网 Sentinel Clients 指南</a>


## Sentinel 节点

	Sentinel 节点本身就是独立的 Redis 节点. 只不过特殊一些, 不存储数据, 并且只支持部分命令.	
	
	默认使用 TCP 端口是 26379

***


## 作用

	主从结构部署实现高可用, 当主节点出现故障时, Redis Sentinel 能自动完成故障发现和故障转移并通知应用方.

***


## 功能
	
	监控 (Monitoring)
		Sentinel 节点会定期检测 Redis 数据节点和其他的 Sentinel 节点是否可达
	通知 (Notification)
		Sentinel 节点会将故障转移的结果通知给应用方
	主节点故障转移 (Automatic failover) (使用 Raft 算法)
		实现从节点晋升为主节点并维护后续正确的主从关系
	配置提供者 (Configuration provider)
		在 Redis Sentinel 结构中, 客户端在初始化的时候连接的是 Sentinel 节点集合, 从中获取主节点信息
	
***
	

## 和主从复制模式的区别

	Redis Sentinel 与 Redis 主从复制模式只是多了 N 个 Sentinel 节点
	
***	


## 高可用性 (HA)
	
	Redis Sentinel 是一个分布式架构, 其中包含 N个 Sentinel 节点和 Redis 数据节点
	
	Sentinel 节点建议部署节点数是 奇数, 比如 3, 5 ...
	
	每个 Sentinel 都会对 Redis 数据节点和其他的 Sentinel 节点进行监控.
	当 Sentinel 通过定期检查 (发送 PING 命令)发现 Redis 数据节点不可达时, 会对 Redis 数据节点做下线标识.
	如果被标识的是主节点时, Sentinel 会和其他 Sentinel 节点进行 "协商", 当大多数 Sentinel 节点都认为主节点不可达时, 
	它们会选举一个 Sentinel 节点来完成自动故障转移的工作, 
	同时会将主从节点的变化实时通知给 Redis 应用方

*** 


>部署结构(监控一个主节点): Master, Slave_0, Slave_1, Sentinel_0, Sentinel_1, Sentinel_2 
>部署结构(监控N个主节点): M_0, M_0_S_0, M_0_S_1, M_1, M_1_S_0, M_1_S_1, Sentinel_0, Sentinel_1, Sentinel_2 


## 配置, 部署

	在 Redis 安装目录下有 sentinel.conf 是默认的配置文件, 注意该配置状态会被持久化, 所以停止和重启 Sentinel 进程都是安全的
	
	先部署 主从复制 (Master, Slave_0, Slave_1)
		部署过程略...
	
	在部署 Sentinel_0, Sentinel_1, Sentinel_2 
		部署过程略...

***

## Sentinel 节点的自发现和是否可达
	
	### Sentinel_0, Sentinel_1, Sentinel_2 是怎么发现各自发现的呢?
	
		在 Sentinel 的配置文件中 sentinel monitor master 127.0.0.1 6379 2
		
		先启动的 Sentinel_0, 然后它会跟 Master 创建订阅关系建立订阅连接, 然后定时 PUBLISH 自身信息 (IP, PORT, ID 等等) 到该订阅连接上, 并且也接收该订阅连接的信息

		然后启动 Sentinel_1, 它也会跟 Master 创建订阅关系建立订阅连接, 然后定时 PUBLISH 自身信息到该订阅连接, 并且也接收该订阅连接的信息

		Sentinel_2 同理	
		
		并且 Sentinel 之间也会互相创建命令连接应用于通信 (心跳, 广播 等).
		
	### Sentinel 是怎么发现其他 Sentinel 是否可达的呢?

		每个 Sentinel 都会每秒定期发送 PING 命令来判断其余 Sentinel 节点是否可达

***


## Sentinel 节点对 Redis 主从节点的发现和是否可达

	### Sentinel 对 Redis 主从节点的发现
	
		在 Sentinel 的配置文件中 sentinel monitor master 127.0.0.1 6379 2
			
		然后 Sentinel 节点会从配置的主节点获取有关从节点的相关信息
	
	### Sentinel 判断其他 Redis 节点 是否可达

		每个 Sentinel 都会每秒定期发送 PING 命令来判断其余 Redis 节点是否可达	

***


## Sentinel 的定时任务

	### 10秒间隔定时任务
		每隔 10 秒, 每个 Sentinel 节点会向 Master 主节点发送 info 命令获取最新的拓扑(tuo pu)结构 (info replication 中会包含 Slave 从节点信息)
	
	### 2秒间隔定时任务
		每隔 2 秒, 每个 Sentinel 节点会向 Redis 数据节点的 _sentinel_:hello 频道上发送该 Sentinel 节点对于主节点的判断以及当前 Sentinel 节点的信息
			同时每个 Sentinel 节点也会订阅该频道来了解其他 Sentinel 节点以及它们对主节点的判断
				1. 发现新的 Sentinel 节点
				2. Sentinel 节点之间交换主节点状态
				
	### 1秒间隔定时任务
		每隔 1 秒, 每个 Sentinel 节点会向主节点, 从节点, 其他 Sentinel 节点发送一条 PING 命令做一次心跳检测, 来确认这些节点当前是否可达, 不可达则主观下线
		down-after-milliseconds 1000 (单位: 毫秒)

***		
		
		
## Sentinel 对节点做失败判定状态		

	主观下线
		每隔 1秒, 每个 Sentinel 都会去 PING 其他相关的节点, 当这些节点超过 down-after-milliseconds 没有进行有效回复, 就对该节点进行主观的下线
			但是这可能存在误判, 比如 网络延迟, 对应的节点繁忙导致没有及时响应 等等
	
	客观下线	
		当 Sentinel 主观下线的节点是 Master 主节点的时候会通过 sentinel is-master-down-by-addr 命令向其他 Sentinel 节点询问对主节点的判断,
			当超过 <quorum> 个数的时候则认为该 Master 主节点的确有问题, 这时才会作出 客观下线 的判断

***		


## Sentinel API

	### PING
		返回 PONG
		
	### SENTINEL masters
		返回所有被监控的主节点状态以及相关统计信息
	
	### SENTINEL master <master name>
		返回指定 master 主节点状态以及相关统计信息
	
	### SENTINEL slaves <master name>
		返回指定 master 主节点的从节点状态以及相关统计信息
	
	### SENTINEL sentinels <master name>
		返回指定 master 的 Sentinel 节点集合 (不包含当前 Sentinel 节点)
	
	### SENTINEL get-master-addr-by-name <master name>
		返回指定 master 主节点的 IP 和 PORT
	
	### SENTINEL reset <pattern[通配符]>
		当前 Sentinel 节点对符合 <pattern[通配符]> 的主节点的配置进行重置
			包含清除主节点的相关状态(例如故障转移), 重新发现从节点和 Sentinel 节点
	
	### SENTINEL failover <master name>
		对指定 master 主节点进行强制的故障转移
	
	### SENTINEL ckquorum <master name>
		检测当前可达的 Sentinel 节点总数是否达到 <quorum> 的个数
	
	### SENTINEL flushconfig
		将 Sentinel 节点的配置强制刷到磁盘上
	
	### SENTINEL remove <master name>
		取消当前 Sentinel 节点对指定 master 主节点的监控
	
	### SENTINEL monitor <master name> <ip> <port> <quorum>
	
	### SENTINEL set <master name>
		动态修改 Sentinel 节点的配置选项
		比如:
			SENTINEL set master quorum 2
			...
	
	### SENTINEL is-master-down-by-addr
		Sentinel 节点之间用来交换对主节点是否下线的判断

***


## Sentinel Clients -- Jedis

	客户端初始化连接是 Sentinel 节点集合

	Jedis 伪代码
	
	```java
		private static JedisSentinelPool jedisSentinelPool;
	
		public synchronized static void initRedis (String masterName, Set<String> sentinels, int dbIndex)
		{
			if(sentinels == null || sentinels.isEmpty())
			{
				LOG.info("没有任何 Sentinel 信息...");
				return;
			}

			if(jedisSentinelPool == null)
			{
				jedisSentinelPool = new JedisSentinelPool(masterName, sentinels, JedisConfig.jedisPoolConfig);

				DBIndex = dbIndex;
			}
			
			/**
			 * 健康检查
			 */
			highAvailable ();
		}

		/**
		 * 获取 Jedis
		 * @return
		 */
		public static Jedis getResource()
		{
			try
			{
				if(jedisSentinelPool == null)
					throw new NullPointerException("Jedis Pool 未初始化");

				Jedis jedis = jedisSentinelPool.getResource();

				jedis.select(DBIndex);

				return jedis;
			}
			catch (JedisConnectionException e)
			{
				LOG.error("Jedis Pool 连接出现异常", e);
			}
			catch (JedisException e)
			{
				e.printStackTrace();
				LOG.error("Jedis Pool 出现异常", e);
			}
			return null;
		}

		/**
		 * 监控检查
		 * @return
		 */
		public boolean highAvailable ()
		{
			try(Jedis jedis = getResource())
			{
				String reply = jedis.ping();
				if("PONG".equals(reply))
					return true;
			}
			catch(Exception e)
			{
				LOG.error(e);
			}

			return false;
		}
	
	```


***

		