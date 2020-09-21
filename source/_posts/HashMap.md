---
title: HashMap实现原理
tags: 
renderNumberedHeading: true
grammar_cjkRuby: true
---



## jdk1.7
```
HashMap存的内容是一个数组散列。数组中的每个元素是一个链条；
Entry的插入是头插入。当有两个key计算出的index的是同一个那么第一个会首先存到对应的index中，第二个会把第一个设置为next，然后Entry链表会下移
	int hashcode = key.hashcode();
	int index = hashcode % table.length; //这只是一个伪代码，实际中并不是这样计算index的
	//下面这一步，会完成Entry的头插入操作
	table[index] = new Entry(key,value,table[index]);

index计算过程：
	首先，获取hashcode值，然后右移 在取异或值。
	为什么这样计算，因为不移位的情况下，hashcode值的高位在计算&操作时无法起到作用，所以容易 index冲突，会造成某个链表会很长；
扩容：
	1.7 中如果达到了扩容阈值，先扩容，后存值
		扩容条件，当size >= threshold时，并且key要存放的地方的不为空
		扩容时，会重新计算length的值，也会重新计算index的值，所以链表也会有所改变
		null的key会固定的落在index为0的地方
	1.8 中加了红黑树
		因为极端情况下可能会出现一个链表特别长的情况，这种情况下，查询效率会非常慢。所以当链表长度达到8后会改成红黑树
		为什么要使用红黑树？因为红黑树既可以保证插入的效率，也可以保证查询的效率
		index冲突时采用的是尾插入。因为就是要确定是否有8个所以需要遍历，所以反正要遍历，所以就直接插入到尾部了。
		链表和红黑树切换时机：
			当链表长度达到8时，会变成红黑树。
			当红黑树长度变为6时，会变成链表
		为什么会是6和8呢？ 而不是8和8呢？
			为了防止在8的长度上频繁添加删除，会造成链表和红黑树的频繁切换；
	
```
## jdk1.8


![enter description here](./images/1600691842904.png)


![enter description here](./images/1600699351349.png)