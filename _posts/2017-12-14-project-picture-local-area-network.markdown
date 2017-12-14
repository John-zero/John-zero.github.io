---
layout:     	post
title:        	"Picture-Project 之 局域网"
subtitle:     	"照片项目 之 局域网 (当前仅限于 Windows 系统)"
date:         	2017-12-14
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - 工作项目
---



## 局域网 涉及需求

	比如该局域网内N台电脑都部署了 Picture-Project 项目, 项目启动就会 bind 2018 端口.
	现在需要这N台电脑能够相互自动连接上. 连接上以后就可以进行相关的业务操作, 比如照片互传, 照片备份, 照片打印, 订单备份 等业务
	
	局域网内都是 动态 IP, 使用的 Windows 命令 "arp -a", 得到 IP 列表后遍历尝试 connect
		当 connect 成功, 则会发送验证消息判断是否是 Picture-Project 项目 bind 的 2018 端口.	
	
	为什么要实现自动发现局域网所有的IP呢?
		是这样的, 如果不自动的话, 就需要在 config.properties 手动配置目标电脑的IP地址, 这对非技术或者程序员来说很麻烦.

***


## 依赖库资源

	<!-- https://mvnrepository.com/artifact/io.netty/netty-all -->
	<dependency>
		<groupId>io.netty</groupId>
		<artifactId>netty-all</artifactId>
		<version>4.1.17.Final</version>
	</dependency>

***


## Windows 命令

	ipconfig
	
	ipconfig -all

	ping ip
		局域网内两机互 ping 超时, 但是又能访问, 可能是电脑防火墙开启了
		
		控制面板 --> 系统和安全 --> Windows 防火墙 --> 自定义设置一下吧
	
	arp -a
		注意获取的是局域网内所有与本机有通信的计算机的IP地址
		当 A 电脑执行 arp -a 没有看到同在该局域网的 B 电脑, 去 B 电脑 ping 一下 A 电脑试试...

***

## 本机 IP, 局域网内所有 IP

```java
import io.netty.util.internal.PlatformDependent;
import org.slf4j.LoggerFactory;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.InetAddress;
import java.net.NetworkInterface;
import java.nio.charset.Charset;
import java.util.*;

public class IntranetUtil
{

    private static final org.slf4j.Logger logger = LoggerFactory.getLogger(IntranetUtil.class);


    /**
     * 局域网 IP
     * @return
     */
    public static List<String> intranetIP ()
    {
        List<String> ips = new ArrayList<>();

        try
        {
            if(PlatformDependent.isWindows())
            {
                Runtime runtime = Runtime.getRuntime();
                Process process = runtime.exec("arp -a"); // 注意获取的是局域网内所有与本机有通信的计算机的IP地址
                BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(process.getInputStream(), Charset.forName("GBK")));
                String inline = null;
                int lineNumber = 0;
                while((inline = bufferedReader.readLine()) != null)
                {
                    lineNumber++;
                    if(lineNumber < 4)
                        continue;

                    String [] splits = inline.split(" {4}");

                    if(splits != null && splits.length > 0 && splits[0] != null)
                    {
                        String host = splits[0].trim();

//                        logger.info("内网HOST/IP: " + host);

                        ips.add(host);
                    }
                }

                try
                {
                    if(bufferedReader != null)
                        bufferedReader.close();
                }
                catch (Exception e)
                {

                }
            }
            else if (PlatformDependent.isOsx())
            {

            }
        }
        catch (Exception e)
        {
            logger.error("局域网 IP 识别异常", e);
        }

        return ips;
    }

}
```
***


## 定时发现

	Spring Boot 项目
	
		一个标注了 @Component 的 ScheduledTasks.java 类 
		
		+ 
		
		在 SpringApplication.java 项目启动类增加 @EnableScheduling 即可


```java

@Component
public class ScheduledTasks
{

    private static final org.slf4j.Logger logger = LoggerFactory.getLogger(ScheduledTasks.class);
	
	
    @Scheduled(fixedDelay = 20L * 1000)
    public void intranetIP ()
    {
        try
        {
            List<String> ips = IntranetUtil.intranetIP();
            ips.stream().forEach(ip_host -> {
                TCPClient.connect(ip_host, 2018);
            });
        }
        catch (Exception e)
        {
            logger.error("发现内网, 然后尝试连接异常", e);
        }
    }
	
}
```		
		
***
		