---
layout:     	post
title:        	"Spring, Spring Boot, Spring Cloud 学习计划"
subtitle:     	"Spring, Spring Boot, Spring Cloud 学习计划"
date:         	2018-02-12
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Java
---



## 官网 + GitHub
		
	官网:
		Spring: https://spring.io
		
		Spring Boot: http://projects.spring.io/spring-boot/
	
		Spring Cloud: http://projects.spring.io/spring-cloud/
		
			Main Projects
				...

	GitHub: 
		Spring: https://github.com/spring-projects
		
		Spring Boot: https://github.com/spring-projects/spring-boot
		
		Spring Cloud: https://github.com/spring-cloud
				
***


## 学习计划

	项目构建 (Maven/Gradle)
	
	服务注册与发现 (Netflix Eureka/HashiCorp Consul/Zookeeper)
		GitHub: 
			https://github.com/Netflix/eureka
			https://github.com/HashiCorp/consul
			https://github.com/spring-cloud/spring-cloud-zookeeper
	
	负载均衡 (Netflix Ribbon)
		GitHub: https://github.com/Netflix/ribbon
	
	远程调用 (Feign)
		GitHub: https://github.com/OpenFeign/feign
	
	熔断器 (Netflix Hystrix)
		GitHub: https://github.com/Netflix/hystrix
	
	熔断监控 (Netflix Hystrix Dashboard/Turbine)
		Netflix Hystrix Dashboard 单个应用
		Turbine 集群应用
			GitHub: https://github.com/Netflix/Turbine
	
	统一配置 (Spring Cloud Config)
		Git/SVN
		GitHub: https://github.com/spring-cloud/spring-cloud-config
		https://springcloud.cc/spring-cloud-config.html
		
	消息总线 (Spring Cloud Bus)	
		Spring Cloud Bus + Spring Cloud Config
		GitHub: https://github.com/spring-cloud/spring-cloud-bus
	
	服务网关 APIGateway (Netflix Zuul/Spring Cloud gateway/)
		GitHub: 
			https://github.com/Netflix/zuul
			https://cloud.spring.io/spring-cloud-gateway/
	
	配置管理API (Netflix Archaius)
		GitHub: https://github.com/Netflix/archaius
	
	分布式跟踪 (Spring Cloud Sleuth)
		Spring Cloud Sleuth + ZipKin + (In-Memory/MySql/Cassandra/Elasticsearch)
		GitHub: https://github.com/spring-cloud/spring-cloud-sleuth
	
	安全 (Spring Cloud Security)
		GitHub: https://github.com/spring-cloud/spring-cloud-security
		
	数据流操作开发包 (Spring Cloud Stream)
		GitHub: https://github.com/spring-cloud/spring-cloud-stream
		
	任务计划, 调度 (Spring Cloud Task)	
		GitHub: https://github.com/spring-cloud/spring-cloud-task
		
	...	

***


## Spring Boot 整合

	Log4j2 (日志)
		Apache Log4j -> Java Logging -> Logback -> Apache Log4j2
	
	Swagger2 (API 文档)
	
	Thymeleaf (模板引擎: 传统 JSP, 中生代 Velocity, 后现代 Thymeleaf)
	
	Redis
	
	MyBatis (数据源: Druid) / Spring Data JPA (Hibernate)
	
	MongoDB
	
	Kafka
	
	RabbitMQ
	
	Elasticsearch
	
	Shiro/Spring Security (安全)
	
	Actuator (单个应用监控)
	
	Admin (Actuator 整合, 可视化 UI)
	
	...

***


## Spring Boot 功能
	
	Servlet 容器
		Apache Tomcat / Eclipse Jetty

	过滤器
	
	拦截器
	
	验证
	
	配置
	
	定时任务
	
	邮件服务
	
	文件上传 (分布式文件系统: FastDFS)
	
	Jenkins
	
	测试
	
	...

***


## 参考文档

<a href="http://www.springcloud.cc/" target="_blank">Spring Cloud 中文网</a> 

<a href="http://blog.didispace.com/Spring-Boot%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/">程序猿DD - Spring Boot 基础教程</a>

<a href="http://blog.didispace.com/Spring-Cloud%E5%9F%BA%E7%A1%80%E6%95%99%E7%A8%8B/">程序猿DD - Spring Cloud 基础教程</a>

<a href="http://www.ityouknow.com/spring-boot.html">纯洁的微笑 - Spring Boot 基础教程</a>

<a href="http://www.ityouknow.com/spring-cloud.html">纯洁的微笑 - Spring Cloud 基础教程</a>

<a href="https://segmentfault.com/n/1330000009887617" target="_blank">Java 微服务实战 - Spring Boot 系列</a> 

...

***