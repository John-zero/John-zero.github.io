---
layout:     	post
title:        	"Redis 私有云平台"
subtitle:     	"搜狐视频(sohu tv) Redis 私有云平台"
date:         	2017-12-04
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Redis
---



<a href="https://github.com/sohutv/cachecloud" target="_blank">搜狐视频(sohu tv) Redis 私有云平台</a>
<a href="https://cachecloud.github.io/" target="_blank">搜狐视频(sohu tv) CacheCloud 官方博客</a>


## 部署安装

参考: <a href="https://github.com/sohutv/cachecloud/wiki/3.%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E6%8E%A5%E5%85%A5%E6%96%87%E6%A1%A3" target="_blank">服务器端接入文档</a>

	Download V1.2
		https://pan.baidu.com/s/1nvTv90l

	解压
		tar -xvf cachecloud-bin-1.2.tar.gz
		
	cd cachecloud-web/

	vim jdbc.properties 
		修改数据库配置信息
		
	执行 cachecloud.sql 初始化	
		
	start.sh 启动脚本中 JVM 参数配置默认是: -server -Xmx4g -Xms4g -Xss256k -XX:MaxDirectMemorySize=1G ...
		视情况修改 JVM 参数
		
	./start.sh	
	
	访问 http://127.0.0.1:9999/manage/login
		账号: admin 
		密码: admin
		
***


