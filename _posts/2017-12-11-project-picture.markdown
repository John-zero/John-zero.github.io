---
layout:     	post
title:        	"照片 Picture-Project"
subtitle:     	"照片项目"
date:         	2017-12-11
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - 工作项目
---



## 缩略图

单反拍摄高清无码照片大小3MB+, 导致 WEB 页面加载贼慢, 所以优化生成缩略图来代替.
	
使用的 Google Java Library:	
	
	<!-- https://github.com/coobird/thumbnailator -->
	<!-- https://mvnrepository.com/artifact/net.coobird/thumbnailator -->
	<dependency>
		<groupId>net.coobird</groupId>
		<artifactId>thumbnailator</artifactId>
		<version>0.4.8</version>
	</dependency>
		
		
伪代码
		
```java

// 照片常量类
public class PictureConstant
{
	
	// 缩略图标识
    public final static String THUMBNAIL = "thumbnail";

}

// 图片信息枚举
public enum PictureInfoEnum
{
	SIZE, // 大小
	WIDTH, // 宽度
	HEIGHT, // 高度
	;
}


//
// 以下是 PictureUtils 类的函数
//


// 图片信息
public static Map<PictureInfoEnum, Object> imageInfo (String imagePath)
{
	Map<PictureInfoEnum, Object> attribute = new HashMap<>();
	try
	{
		File imageFile = new File(imagePath);
		if(!imageFile.exists())
		{
			logger.warn(String.format("%s 图片文件不存在...", imagePath));
			return attribute;
		}
		if(imageFile.isDirectory())
		{
			logger.warn(String.format("%s 非图片文件...", imagePath));
			return attribute;
		}

		BufferedImage sourceImage = ImageIO.read(new FileInputStream(imageFile));

		double imageSize = imageFile.length() / 1024.0; // 源图大小
		attribute.put(PictureInfoEnum.SIZE, imageSize);
		logger.info(String.format("%s, 源图大小: %.1f", imagePath, imageSize));

		int imageWidth = sourceImage.getWidth(); // 源图宽度
		attribute.put(PictureInfoEnum.WIDTH, imageWidth);
		logger.info(String.format("%s, 源图宽度: %s", imagePath, imageWidth));

		int imageHeight = sourceImage.getHeight(); // 源图高度
		attribute.put(PictureInfoEnum.HEIGHT, imageHeight);
		logger.info(String.format("%s, 源图高度: %s", imagePath, imageHeight));
	}
	catch (Exception e)
	{
		logger.error("图片信息, 异常", e);
	}

	return attribute;
}

// 根据图片路径名称内部计算大小像素生成缩略图
public static JSONObject thumbnail (String sourcePath, String targetPath)
{
	logger.info(String.format("源图片: %s, 目标缩略图: %s", sourcePath, targetPath));

	JSONObject jsonObject = new JSONObject();
	try
	{
		long startNano = System.nanoTime();

		File sourcePicture = new File(sourcePath);
		File targetPicture = new File(targetPath);

		Map<PictureInfoEnum, Object> attribute = imageInfo (sourcePicture);
		if(attribute == null || attribute.isEmpty())
		{
			jsonObject.put("code", "failure");
			jsonObject.put("message", String.format("%s 图片属性获取失败, 导致无法生成缩略图...", sourcePath));
			return jsonObject;
		}

		if(sourcePath.indexOf("_" + PictureConstant.THUMBNAIL) > -1) // 存在缩略图标识, 则直接返回图片全名称
		{
			return pictureCopy (sourcePicture, targetPicture);
		}

		double imageSize = (double) attribute.get(PictureInfoEnum.SIZE);
		if(imageSize < 1024.0D) // 图片大小小于阈值, 无须生成缩略图, 直接将原图拷贝作为缩略图
		{
			return pictureCopy (sourcePicture, targetPicture);
		}

		int imageWidth = (int) attribute.get(PictureInfoEnum.WIDTH);
		int imageHeight = (int) attribute.get(PictureInfoEnum.HEIGHT);

		int widthPixels, heightPixels;
		int maxCommonDivisor = AlgorithmUtil.maxCommonDivisor(imageWidth, imageHeight);
		if(maxCommonDivisor > 1) // 动态计算
		{
			widthPixels = imageWidth / maxCommonDivisor * 300;
			heightPixels = imageHeight / maxCommonDivisor * 300;

			// 如果动态计算后的像素大小超过原像素大小, 则保持原来的尺寸大小
			if(widthPixels > imageWidth)
				widthPixels = imageWidth;
			if(heightPixels > imageHeight)
				heightPixels = imageHeight;
		}
		else  // 原像素大小
		{
			widthPixels = imageWidth;
			heightPixels = imageHeight;
		}

		logger.info(String.format("根据图片路径名称 %s --> %s 内部计算缩略图大小像素: 宽: %s, 高: %s", sourcePath, targetPath, widthPixels, heightPixels));

		Thumbnails.of(sourcePicture).size(widthPixels, heightPixels).keepAspectRatio(true).toFile(targetPicture);

		long endNano = System.nanoTime();

		logger.info(String.format("根据图片路径名称 %s --> %s 内部计算大小像素生成缩略图耗时: %s", sourcePath, targetPath, (endNano - startNano) / 1000000));
	}
	catch (Exception e)
	{
		jsonObject.put("code", "exception");
		jsonObject.put("exception", e);

		logger.error("根据图片路径名称内部计算大小像素生成缩略图 异常", e);
	}

	return jsonObject;
}

// 图片拷贝
public static JSONObject pictureCopy (File sourcePicture, File targetPicture)
{
	JSONObject jsonObject = new JSONObject();

	try
	{
		Files.copy(sourcePicture, targetPicture);
	}
	catch (Exception e)
	{
		logger.error(String.format("%s 直接拷贝作为缩略图...异常", sourcePicture.getPath()), e);

		jsonObject.put("code",  "exception");
		jsonObject.put("exception", e);

		return jsonObject;
	}

	jsonObject.put("code", "copy");
	jsonObject.put("message", String.format("%s 直接拷贝作为缩略图...", sourcePicture.getPath()));

	return jsonObject;
}
```	

注意点:
	
	如果由于 Thumbnails 使用不当而导致出现异常
		容易出现 java.lang.OutOfMemoryError: Java heap space 内存溢出
	
常见异常:
	
	1. java.lang.NegativeArraySizeException: null
		at java.awt.image.DataBufferByte.<init>(DataBufferByte.java:76)
		at java.awt.image.Raster.createInterleavedRaster(Raster.java:266)
		at java.awt.image.BufferedImage.<init>(BufferedImage.java:376)
		at net.coobird.thumbnailator.builders.BufferedImageBuilder.build(Unknown Source)
		at net.coobird.thumbnailator.makers.ThumbnailMaker.makeThumbnail(Unknown Source)
		at net.coobird.thumbnailator.makers.FixedSizeThumbnailMaker.make(Unknown Source)
		at net.coobird.thumbnailator.Thumbnailator.createThumbnail(Unknown Source)
		at net.coobird.thumbnailator.Thumbnails$Builder.toFile(Unknown Source)	
		
	这个大部分原因都是因为 width, height 设置不合理而导致的
		
***

		