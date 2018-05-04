---
layout:     	post
title:        	"Apache Commons IO"
subtitle:     	"IO 相关操作实用工具类"
date:         	2017-12-27
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - IO
---



## Apache Commons IO

	官网主页
		http://commons.apache.org/proper/commons-io/
		
	User guide
		http://commons.apache.org/proper/commons-io/description.html
	
***


## 项目引入
	
	Maven
		<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
		<dependency>
			<groupId>commons-io</groupId>
			<artifactId>commons-io</artifactId>
			<version>2.6</version>
		</dependency>
		
	Gradle 
		// https://mvnrepository.com/artifact/commons-io/commons-io
		compile group: 'commons-io', name: 'commons-io', version: '2.6'

***


## 主要涉及

1. Utility classes (使用工具类)
2. Input (输入)
3. Output (输出)
4. Filters (过滤器)
5. Comparators (比较器)
6. File Monitor (文件监控)

***


## File Monitor

```java

import org.apache.commons.io.monitor.FileAlterationListener;
import org.apache.commons.io.monitor.FileAlterationMonitor;
import org.apache.commons.io.monitor.FileAlterationObserver;
import org.slf4j.LoggerFactory;

import java.io.File;

/**
 * Created by John_zero on 2017/12/27.
 */
public final class FileUtils
{

    private static final org.slf4j.Logger logger = LoggerFactory.getLogger(FileUtils.class);

    /**
     * 文件夹监听 (默认开启) (底层用的是 Thread 运行, 所以不建议监听太多)
     * @param directoryName 文件夹路径
     * @param fileAlterationListener
     * @param monitoInterval
     * @return
     */
    public static FileAlterationMonitor directoryMonitor (String directoryName, FileAlterationListener fileAlterationListener, long monitoInterval)
    {
        if(directoryName == null)
            return null;

        if(fileAlterationListener == null)
            return null;

        if(monitoInterval <= 0)
            monitoInterval = 3;

        File monitorDirectoryFile = new File(directoryName);
        if(!monitorDirectoryFile.exists())
        {
            monitorDirectoryFile.mkdirs();
        }
        else if(monitorDirectoryFile.exists())
        {
            if(!monitorDirectoryFile.isDirectory())
                return null;
        }

        FileAlterationObserver fileAlterationObserver = new FileAlterationObserver(monitorDirectoryFile);

        fileAlterationObserver.addListener(fileAlterationListener);

        FileAlterationMonitor fileAlterationMonitor = new FileAlterationMonitor(monitoInterval, fileAlterationObserver);

        try
        {
            fileAlterationMonitor.start();
        }
        catch (Exception e)
        {
            e.printStackTrace();
        }

        return fileAlterationMonitor;
    }

}

```

使用工具类:

```java
String directoryPath = ""; // 监控的文件夹路径

FileAlterationMonitor fileAlterationMonitor = FileUtils.directoryMonitor(directoryPath, new FileAlterationListener() {
	@Override
	public void onStart(FileAlterationObserver fileAlterationObserver) { }

	@Override
	public void onStop(FileAlterationObserver fileAlterationObserver) { }

	@Override
	public void onDirectoryCreate(File file) { }

	@Override
	public void onDirectoryChange(File file) { }

	@Override
	public void onDirectoryDelete(File file) { }

	@Override
	public void onFileCreate(File file)
	{
		try
		{
			// TODO 
		}
		catch (Exception e)
		{
			e.printStackTrace();
		}
	}

	@Override
	public void onFileChange(File file)
	{
		try
		{
			// TODO 
		}
		catch (Exception e)
		{
			e.printStackTrace();
		}
	}

	@Override
	public void onFileDelete(File file)
	{
		try
		{
			// TODO 
		}
		catch (Exception e)
		{
			e.printStackTrace();
		}
	}

}, 2);

```

注意点: 

1. 在 FileAlterationListener 接口实现函数内必须使用 try-catch 捕获异常, 如果不然出现异常没有捕获会导致后续监听无效...

2. 监听的文件夹或者文件路径如果是共享模式, 监听会失效, 对于共享模型下的文件夹或者文件请谨慎使用之...

***


## 扩展

文件操作监听工具: <a href="http://file-leak-detector.kohsuke.org" target="_blank">http://file-leak-detector.kohsuke.org</a>

文件上传(阿里云 OSS SDK, 各种骚操作): <a href="https://github.com/aliyun/aliyun-oss-java-sdk" target="_blank">https://github.com/aliyun/aliyun-oss-java-sdk</a>

***


## 其他

其他几个相关领域的使用就比较简单, 就没贴代码了哈...

***

	