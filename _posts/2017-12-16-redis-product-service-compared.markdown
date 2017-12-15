---
layout:     	post
title:        	"Redis Product Service Compared"
subtitle:     	"Redis 产品 服务 对比"
date:         	2017-12-16
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Redis
---



## 对比涉及

Redis 官方: https://redis.io/

腾讯云 Redis: https://www.qcloud.com/document/product/239/3205

	基于 Grocery 兼容开源 Redis 协议的分布式云存储产品
	
阿里云 Redis: https://help.aliyun.com/document_detail/26342.html

	ApsaraDB for Redis 兼容开源 Redis 协议标准, 提供持久化的内存数据库服务, 当前支持兼容 Redis 3.2 的版本命令

<!--
Amazon ElastiCache Redis: https://amazonaws-china.com/cn/elasticache/redis/
-->
	
***


## 提供规格

腾讯云 Redis:

	1. 主从版 (容量上限 60G)
	2. 容量型主从版 (容量 64G 到 384G)
	3. 集群版 (维护中) (容量 无上限)
	
阿里云 Redis:

	1. 标准版-单副本
	2. 标准版-双副本
	3. 集群版-双副本
	4. 读写分离实例
	5. 集群版-单副本
	
***


## 部分区别

腾讯云 Redis:

	1. DB 库 16个, 0-15
	2. 集群默认使用 0号 DB库
	3. <a href="https://cloud.tencent.com/document/product/239/4059" target="_blank">主从版与集群版区别</a>
	4. ...
	
阿里云 Redis:

	1. DB 库 256个, 0-255
	2. 集群支持使用 多DB库 模式
	3. <a href="https://help.aliyun.com/document_detail/57797.html" target="_blank">Redis 4.0、codis 、云数据库 Redis 版集群对比分析</a>
	4. ...
	
***


## 使用限制, 命令限制

腾讯云 Redis:

	1. <a href="https://cloud.tencent.com/document/product/239/4073" target="_blank">主从版使用限制</a>
	2. <a href="https://cloud.tencent.com/document/product/239/11988" target="_blank">集群版使用限制</a>
	
阿里云 Redis:

	1. <a href="https://help.aliyun.com/document_detail/54961.html" target="_blank">使用限制</a>
	2. <a href="https://help.aliyun.com/document_detail/26356.html" target="_blank">支持的 Redis 命令</a>

EVAL 脚本 (对基于 Redis 实现 分布式锁 有所影响)

	腾讯云 Redis 主从, 集群 暂时都不支持
	
	阿里云 Redis 集群不支持, 但是 标准版 支持
	
***


## 参考文档

<a href="https://help.aliyun.com/document_detail/57797.html" target="_blank">Redis 4.0、codis 、云数据库 Redis 版集群对比分析</a> 

***


		