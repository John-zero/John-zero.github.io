---
layout:     	post
title:        	"Java 冷门基础知识"
subtitle:     	"持续收集一些不经意的基础知识点"
date:         	2018-01-23
author:       	"John-zero"
header-img: 	"img/post-bg-os-metro.jpg"
catalog:      	true
multilingual: 	false
tags:
    - Java
---




## 收集情况记录

 日期		| 涉及内容
------------|---------------------------------------------------------------------
 2018-01-26	| hashcode
------------|---------------------------------------------------------------------

***


## hashcode

先瞧瞧 JDK 的(Object, String, Integer, Long)的 hashcode 实现源码...

```java

public class Object
{
	
	public native int hashCode();

    public boolean equals(Object obj) {
        return (this == obj);
    }
	
}

public final class String implements java.io.Serializable, Comparable<String>, CharSequence
{
	
	public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
	
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }

}

public final class Integer extends Number implements Comparable<Integer>
{
	
	@Override
    public int hashCode() {
        return Integer.hashCode(value);
    }

	public static int hashCode(int value) {
        return value;
    }
	
    public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }

}

public final class Long extends Number implements Comparable<Long>
{

    @Override
    public int hashCode() {
        return Long.hashCode(value);
    }

    public static int hashCode(long value) {
        return (int)(value ^ (value >>> 32));
    }
	
    public boolean equals(Object obj) {
        if (obj instanceof Long) {
            return value == ((Long)obj).longValue();
        }
        return false;
    }

}

```

Object 的 native int hashCode () 默认返回的是内存对象的地址, equals(Object) 判断对象是用 ==.

String 的 int hashCode () 是遍历计算 value 数组得到对应的 hahsCode 的.

Object 是所有类的基础父类, (String, Integer, Long) 都是复写了 Object 类提供的函数重新实现, 
并且一般复写 equals(Object) 的同时也会复写 hashCode(), 这样是为了确保使用 equals(Object) 判断两个对象相等的情况下 hashCode() 结果也是相等的.


	延伸:
	
		== 和 equals(Object) 区别
			== 是比较运算符, 比较的是值或者对象地址
			equals(Object) 是函数, 在 Object 类中默认是 ==, 如果子类复写则按复写规则比较对象. 比如 Integer 比较的是具体的值
			
		31 
			31 是个奇素数 (既是奇数, 又是素数[质数]). 
			31 有个很好的特性, 就是用移位和减法来代替乘法, 可以得到更好的性能: 31*i==(i<<5)-i.
				源自于 <<Effective Java>>
				
		哪些场景会用到 hashCode ?
			比如 比较器 Comparable, Comparator 的比较对象
			比如 Map 会计算 KEY 的 hashCode 然后快速得到位于数组的 index 坐标. 
			知乎 - JDK 源码中 HashMap 的 hash 方法原理是什么？ : https://www.zhihu.com/question/20733617

***

	