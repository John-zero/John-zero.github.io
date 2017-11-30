---
layout:     	post
title:        	"Java Logging"
subtitle:     	"Java 日志"
date:         	2017-11-30
author:       	"John-zero"
header-img: 	"img/post-bg-android.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Java
    - 日志
---



## **日志接口** 

* Commons-Logging (JCL 简称], Jakarta Commons Logging, Apache Commons Logging) 
	(<a href="http://commons.apache.org/proper/commons-logging/" target="_blank">官网</a>)
	(<a href="https://github.com/apache/commons-logging" target="_blank">GitHub</a>)
	
		对外提供统一的接口, 然后采用 适配器模式 将日志的具体操作委托给集成的具体的日志框架, 比如 Log4J, Log4J 2 等 
	
	
* SLF4j (Simple Logging Facade For Java) 基于 API 的 Java 日志框架
	(<a href="https://www.slf4j.org/" target="_blank">官网</a>)
	(<a href="https://github.com/qos-ch/slf4j" target="_blank">GitHub</a>)
	
***


## **日志框架** 

* Apache Log4J 1
	(<a href="http://logging.apache.org/log4j/1.2/" target="_blank">官网</a>)
	(<a href="https://github.com/apache/log4j" target="_blank">GitHub</a>)
	
* Apache Log4J 2
	(<a href="https://logging.apache.org/log4j/2.x/" target="_blank">官网</a>)
	(<a href="https://github.com/apache/logging-log4j2" target="_blank">GitHub</a>)
	
		Log4J 1 的升级版本, 最大的优势在于多线程并发场景下性能更优, 异步模式采用 高性能队列 Disruptor 以获取更高性能
	
* Logback
	(<a href="https://logback.qos.ch/" target="_blank">官网</a>)
	(<a href="https://github.com/qos-ch/logback" target="_blank">GitHub</a>)
	
		Logback 是在 Log4J 的基础做的改进, 官方建议配合 SLF4j 一起使用(对 SLF4j 无缝结合)
	
* JDK-Logging
	
		JDK 1.4 版本后提供的一个自带的日志库实现
	
***


## **日志服务平台** 

* ELK (Elasticsearch Logstash Kibana)
	(<a href="https://www.elastic.co/cn/" target="_blank">官网</a>)
	(<a href="https://github.com/elastic" target="_blank">GitHub</a>)
	
	技术栈
	
	+ Elasticsearch (<a href="https://github.com/elastic/elasticsearch" target="_blank">GitHub</a>)
	+ Logstash (<a href="https://github.com/elastic/logstash" target="_blank">GitHub</a>)
	+ Kibana (<a href="https://github.com/elastic/kibana" target="_blank">GitHub</a>)
	
***
	
* GrayLog2
	(<a href="https://www.graylog.org/" target="_blank">官网</a>)
	(<a href="https://github.com/Graylog2" target="_blank">GitHub</a>)
	
	技术栈
	
	- GrayLog2 (搜索和视图展示日志, 告警和权限) (<a href="https://github.com/Graylog2" target="_blank">GitHub</a>)
	- Elasticsearch (提供搜索日志, 日志文件存储) (<a href="https://github.com/elastic/elasticsearch" target="_blank">GitHub</a>)
	- MongoDB (储存日志源文件) (<a href="https://github.com/mongodb" target="_blank">GitHub</a>)
	- Collector-sidecar 或者 syslog (收集日志)
	
***


## **注意** 
	
> SLF4j, Log4J, Logback 都是出自于同一个作者

***


## **集成关系图** 

> 参考: https://www.slf4j.org/manual.html


![](img/in-post/java-logging-relationship-integration.png)

	
													    |->  log4j-jcl  					-> 	Log4J 2
																							-> 	Log4J 1
	应用  ->  Apache Commons-Logging  -> 适配桥梁 ->	|->  slf4j-api, jcl-over-slf4j		-> 	Logback
																							-> 	JDK-Logging
													    |->  jcl-over-slf4j 			    ->  SLF4j

--------------------------------------------------------------------------------------------------------------

								|-> 	log4j-slf4j-impl   ->	  Log4J 2
								|-> 	slf4j-log4j12	   -> 	  Log4J 1
								|->		logback-classic	   -> 	  Logback					
								|-> 	slf4j-jdk14		   -> 	  JDK-Logging
	应用 -> SLF4j -> 适配桥梁 ->|							
								|-> 	slf4j-simple	   ->	  simple	
								|-> 	slf4j-nop	   	   ->	  NOP		
								|-> 	slf4j-jcl	   	   ->	  Apache Commons-Logging
		

***


		