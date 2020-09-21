---
title: ConcurrentHashMap
tags: 
renderNumberedHeading: true
grammar_cjkRuby: true
---


# ConcurrentHashMap
	线程安全的Map集合
## 内部数据结构
	ConcurrentHashMap的内部是Segment[]数组，Segment中存储的是HashEntry<Key,value>[]数组
	其实Segment就类似是一个HashMap
	
## 几个重要的默认参数
	concurrencyLevel
	initialCapacity
	loadFactor

## 一个Segment中应该有几个HashEntry
	默认情况下 DEFAULT_CONCURRENCY_LEVEL =16 ，DEFAULT_CAPACITY =16
	DEFAULT_CONCURRENCY_LEVEL	代表Segment[]的长度
	DEFAULT_CAPACITY			代表HashEntry[]的长度
	那么按照除法应该是一个Segment创建的时候有一个HashEntry，但是事实却是2
		因为内部计算中有一部计算按照DEFAULT_CAPACITY/DEFAULT_CONCURRENCY_LEVEL + 1 的计算
		举个例子 DEFAULT_CONCURRENCY_LEVEL = 16 ， DEFAULT_CAPACITY = 17
		那么这时 DEFAULT_CAPACITY/DEFAULT_CONCURRENCY_LEVEL = 1，按照除法算得话，一个Segment存一个HashEntry，但是这样的话16个Segment就只能存16个HashEntry了，而我们设置的是17个，这显然不合理；
		所以会改成一个Segment存两个HashEntry。

## put方法

	当put时如果要put到的位置的segment为空则会先初始化Segment
	然后在放到Segment的HashEntry[]里面
	这个过程是使用Unsafe的方式实现的；
	当第一个Segment创建好后，后面的Segment会按照第一个Segment为原型进行创建，这样就减少了Segment初始化的一系列的判断；

## HashCode 如何确定Segment和HashEntry的位置？
	高位确定的是 在Segment数组的位置
	低的几位确定的是 在hashEntry的位置
	