---
layout:     	post
title:        	"面试小结"
subtitle:     	"聊聊最近面试涉及的点(注意: 因人而异)"
date:         	2018-01-23
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - 面试
---


<!--	
	Java, Linux, MYSQL, Redis, Netty, RabbitMQ, Dubbo, Spring 套件...
-->


## 个人小结

最近面试了几家公司, 特此小结和反思一下.
	
其实就面试而言, 本身也是发散性的, 从一个点可以延伸到另外一个点, 直到面试官问到你答不出来, 人仰马翻怀疑人生为止...
	
主要分为:

	0. 项目经验

	1. 相关基础 (Java, Linux 系统)

	2. 基础组件和开源框架
	
	3. 运维监控

	4. 发散性思维
		业务设计
		架构设计
		...
		
	5. "你有什么要问我的吗?"	

***


## 0. 项目经验

每个人所供职的公司不一样, 负责的业务也就有所不一样; 数据量不一样, 所使用的技术也就有所不一样.

所以就项目经验而言, 自由发挥吧

比如介绍一下公司产品业务, 项目架构, 人员划分, 你所参与和负责的点, 技术亮点, 

遇到的难题(怎么发现的怎么解决的), 一些通用技术点是怎么去解决的(分布式锁, 分布式事务) 等等 

***


## 1. 相关基础 (Java, Linux 系统)

	Java 基础
		String
			不可变特性
			String, StringBuilder, StringBuffer
			String intern() 的作用, 机制, 不同版本所带来的影响
			String 位于常量池, 内存分配机制 (HotSpot 1.6 常量池 在 永久代 上分配内存, HotSpot 1.7 常量池 在 Heap 堆 上分配内存)
		
		Object
			有哪些方法? 有哪些是 native 方法?
			线程协作 (wait(), notify()/notifyAll()), synchronized
			hashcode()
		
		容器 
			List 
			Map 
			Set 
			...
			
			延伸:
				初始容量, 负载引子 的定义
				有序, 无序, FIFO, 扩容, ...
				涉及算法: 红黑树, TimeSort, ...
				比较器: Comparable, Comparator
				快速失败, 安全失败
				工具类: Arrays, Collections
				...
			
		锁
			(锁概念, 锁分类[可重入锁, 悲观锁, 乐观锁, 自旋锁, 无锁 ...])
			synchronized (关键字) 
			Lock (接口)
			... 
			
		线程
			线程状态
			线程挂起
			...
			
			延伸:
				线程安全
				线程池
				...
				
		JUC (java.util.concurrent)
			AQS: AbstractQueuedSynchronizer
			线程池: ThreadPoolExecutor, ScheduledThreadPoolExecutor, Executors
			Lock: Lock, ReentrantLock, ReentrantReadWriteLock, StampedLock (JDK 8新增的不可重入的读写锁), LockSupport
			容器: ConcurrentHashMap, CopyOnWriteArrayList, ...
			Atomic: AtomicInteger, AtomicLong ...
			工具: CountDownLatch (计数器), CyclicBarrier (环栅栏), Exchanger (同步器), Semaphore (信号量), Executors
			...
			
		NIO (同步非阻塞 IO, New IO, JDK 1.4+)
			阻塞, 非阻塞
			同步, 异步
			NIO 核心: Channel, Buffer, Selector (多路复用选择器)
			
			延伸:
				IO 面向流, NIO 面向缓冲区
				BIO (同步阻塞 IO), OIO (同步阻塞 IO, Old IO), NIO, AIO (异步非阻塞 IO, NIO.2, JDK 1.7+) 模型, 区别, 使用场景
				Linux 提供 poll, select, epoll; Windows 提供 IOCP
				Netty/Mina
				模型 (Reactor 模型, Proactor 模型)
				Zero-Copy 内存零拷贝
				序列化/反序列化
				...
		
		ClassLoader 类加载机制
			加载/卸载过程
			双亲委派模型 (Parent Delegation Model)
			类加载器
			...
			
			延伸:
				Class.forName() 和 ClassLoader.loadClass() 区别
				频繁加载类, 会造成哪些影响
				...
		
		JVM
			运行时区域 (堆外内存) (异常)
			配置参数 (-Xmx2048M -Xms1024M -Xss1M ...)
			工具 (Visual VM, JConsole, ...)
			命令 (jps, jinfo, jhat, jmap, jstack, jstat, jstatd, jcmd, hprof)
			分析工具: Memory Analyzer (MAT) ...
			...
			
			延伸:
				调优思路, 问题发现, 问题定位, 问题处理
				Linux 监控调优
				内存泄漏, 内存溢出
				JIT (Just-In-Time Compiler, 即时编译器)
				堆外内存回收机制 (堆内的 DirectByteBuffer 对象被GC时, 它背后的堆外内存也会被回收)
				...
				
		GC
			GC 支持: -XX:+UseSerialGC, -XX:+UseParNewGC, -XX:+UseParallelGC, -XX:+UseParallelOldGC, -XX:+UseConcMarkSweepGC, -XX:+UseG1GC
			GC 日志: -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -Xloggc:E:/log/gc.log
			STW (Stop-The-World)
			Young GC, Full GC 触发机制
			...
			
			延伸:
				GC 垃圾收集器的选择 (吞吐量优先垃圾收集器, 并发低停顿垃圾收集器, 并发与并行的垃圾收集器)
				...
		
		JMM (Java Memory Model)
			原子性
			可见性
			有序性
			
			延伸:
				重排序 (Happen-Before 规则)
				顺序一致性
				voliatile
				final
				Lock 锁
				...
		
		内存屏障 (Memory Barrier)
			voliatile
			...
			
		...
		
	
	Linux 系统
		基本命令
		监控命令
		Shell 脚本
		...
		
		
	HTTP/HTTPS
		HTTP 0.9, HTTP 1.0, HTTP 1.1, HTTPS (HTTP/2.0 SSL/TLS)
		交互流程
		HTTPS 原理
		状态码
		...
	
	
	TCP/IP
		OSI 7层模型, TCP/IP 4层模型
		TCP, UDP, HTTP, IP
		TCP 可靠性, 滑动窗口, TTL, 三次握手四次挥手 ...
		...
		
		延伸:
			长链接, 短链接
			大端, 小端
			...
		
		
	...
	
***


## 2. 基础组件和开源框架

	DB 组件
		MYSQL
			部署方案
			优化策略: SQL 优化 (EXPLAIN), 配置优化, 项目 DAO 层优化 ...
			项目框架选择使用 (JDBC, Hibernate, MyBatis, Spring JDBC, ...)
			分库分表 (多数据源)
			索引 (索引区别, 使用场景, B-Tree 实现)
			事务, ACID
			...
		
		
	缓存组件
		Redis [Jedis (Redis Java Client)]
			部署方案
			基本数据结构, 使用场景, 各种命令 
			监控优化
			序列化/反序列化
			...
			
			延伸:
				Redis 和 Memcached 的区别
				分布式锁
				使用缓存需要注意的点: 缓存击穿, 缓存穿透, 热点缓存, ...
				...
				
		
	配置中心	
		Zookeeper [Apache Curator (Zookeeper Java Client)]
			使用场景
			选举流程
			...
			
			延伸:
				分布式锁
			
			
	负载均衡
		Nginx
			部署方案
			基本概念 (正向代理, 反向代理, ...)
			负载均衡算法
			缓存: 静态缓存, (Proxy Cache) 代理缓存, 分布式缓存
			...
		
	
	开源框架
		Spring
			Spring AOP, Spring IOC
			Spring Boot, Spring Cloud
			Spring 事务
			...
		
		Netty
			Netty 模型
			编解码 (粘包拆包)
			序列化/反序列化
			断线重连
			...
		
***


## 3. 运维监控

	监控方案
		阿里云/腾讯云自带监控平台, 小米 Open-Falcon, Zabbix ...
		
	监控通知
		微信, 短信, 邮件
		
	监控项
		Linux (CPU, Memory, IO, Network)
		Redis 
		MYSQL
		各种线程池
		各种接口
		...

***


## 4. 发散性思维

	看过哪些源码?

	秒杀场景, 如何架构设计
	
	15亿用户, 根据 角色ID 或者 电话 定位用户信息场景
	
	应用程序无辜挂掉, 没有任何日志, 如何定位解决?
	
	新手产品提供需求, 如何沟通?
	
	如何定位高负载的应用程序问题?
	
	...
		
***


## 5. "你有什么要问我的吗?"

	应聘岗位的工作方向内容
	
	涉及的技术栈

	你有什么建议?

	...

***


## 附载

	序列化/反序列化
		Java 原生序列化 (1. implements java.io.Serializable, 2. implements java.io.Externalizable)
		JSON 序列化 (Alibaba fastjson, Google gson, jackson ...)
		Google Protocol Buffer (跨语言)
		Apache Thrift (跨语言)
		Hessian (跨语言)
		...
		
	...

***
