---
layout:     	post
title:        	"MYSQL 库表 使用 Script 操作"
subtitle:     	""
date:         	2017-12-07
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - 日志
---



## MYSQL

	查看版本
		SELECT VERSION();
		
	显示引擎
		SHOW ENGINES;
		
	连接通信状态
		SHOW FULL PROCESSLIST;
		
		(注意: 非 SUPER 权限只能查看当前账户运行的线程)
		
	...	
		
***


>Account Management Statements: https://dev.mysql.com/doc/refman/5.7/en/account-management-sql.html


## 账号管理

	查看指定用户 (root) 的权限
		SHOW GRANTS FOR root;
		
	创建新用户
		CREATE USER 'testRead'@'%' IDENTIFIED BY '123456';
		
	赋予指定用户权限 (参考: https://dev.mysql.com/doc/refman/5.7/en/grant.html)
	
		注意:
			*.* = 库.表
			
			developer@'%' 账户@HOST (developer 如果省略 @'...' 则默认是 @'%')
				%			匹配所有主机(本机和外网)
				localhost	不会解析成 IP 地址, 通过 UNIXsocket 连接
				127.0.0.1	通过 TCP/IP 协议连接, 并且只能本机访问
				::1			兼容支持 IPV6, 等同于 IPV4 的 127.0.0.1
	
		常规操作:
			GRANT INSERT, DELETE, UPDATE, SELECT ON *.* TO developer@'%';
		
		表结构操作:
			GRANT CREATE, ALTER, DROP ON *.* TO developer@'%';
		
		索引 操作:
			GRANT INDEX ON *.* TO developer@'%';
			
		...	
		
		生产环境 显示全部库, SELECT, VIEW (视图) 权限:
			GRANT SHOW DATABASES, SELECT, SHOW VIEW ON *.* TO 'testRead'@'%';
		
		...	
		
		赋予 root 用户对所有库和表的所有权限
			GRANT ALL PRIVILEGES ON *.* TO 'root'@'%';

	简单点:
		GRANT ALL PRIVILEGES ON *.* TO 'dev'@'%' IDENTIFIED BY '123456'; // 创建用户并且赋予所有权限
		
	删除用户和权限: (参考: https://dev.mysql.com/doc/refman/5.7/en/drop-user.html)
		DROP USER 'dev'@'%';
			
	刷新授权
		FLUSH PRIVILEGES;
		
	...
	
***


## 库

	显示存在的全部库
		SHOW DATABASES;

	创建库
		CREATE DATABASE `script` DEFAULT character SET utf8 COLLATE utf8_general_ci;
		简单点: CREATE DATABASE `script`;	
		
	删除库
		DROP DATABASE IF EXISTS `script`;

	使用库
		USE `script`;
		
***


> Data Definition Statements: https://dev.mysql.com/doc/refman/5.7/en/sql-syntax-data-definition.html


## 表

	创建表
		DROP TABLE IF EXISTS `mysql_script`;
		CREATE TABLE `mysql_script` (
		  `id` bigint(20) NOT NULL AUTO_INCREMENT,
		  `script_name` varchar(64) NOT NULL COMMENT '脚本名称',
		  PRIMARY KEY (`id`),
		  KEY `script_name` (`script_name`)
		) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='Script 表';

	新增字段
		ALTER TABLE script.`mysql_script` ADD COLUMN `create_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间';
		
	修改
		修改表名:
			ALTER TABLE script.`mysql_script` RENAME TO `table_script`;
			
		修改字段名称:
			ALTER TABLE script.`table_script` CHANGE create_time new_create_time timestamp;
			
		修改字段属性:
			ALTER TABLE script.`table_script` MODIFY `new_create_time` bigint(20);		
			
		修改字段名称并且修改属性: 
			ALTER TABLE script.`table_script` CHANGE create_time new_create_time bigint(20);
	
	删除字段
		ALTER TABLE script.`table_script` DROP `new_create_time`;

***


## 索引
	
	增加索引
		普通索引
			ALTER TABLE `statistic` ADD INDEX stat_time_index (`stat_time`);
			
		主键索引 
			ALTER TABLE `statistic` ADD PRIMARY KEY (`stat_time`);
			
		唯一索引
			ALTER TABLE `statistic` ADD UNIQUE (`stat_time`);
			
		全文索引
			ALTER TABLE `statistic` ADD FULLTEXT (`stat_time`);
			
		多列索引
			ALTER TABLE `statistic` ADD INDEX stat_time_index (`column_0`, `column_1`);

	删除索引
		DROP TABLE stat_time_index ON `statistic`
		ALTER TABLE `statistic` DROP INDEX stat_time_index 
		ALTER TABLE `statistic` DROP PRIMARY KEY

***

		