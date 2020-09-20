---
title: flink
tags: 新建,模板,小书匠
renderNumberedHeading: true
grammar_cjkRuby: true
---

开发文档地址：
```
https://ci.apache.org/projects/flink/flink-docs-release-1.9/dev/types_serialization.html
```

### Stateful Computations over Data Streams
	离线处理/批处理 有没有状态？ 一般不需要状态
	
	
	Unbound stream  无界的   就是流处理
	bound stream	有界的		批处理
	
	
	flink比较好的：
		
		
		
		
Depending on the type of data sources, i.e. bounded or unbounded sources, you would either write a batch program or a streaming program where the DataSet API is used for batch and the DataStream API is used for streaming. 



### DataSet and DataStream
```
	Flink has the special classes DataSet and DataStream to represent data in a program. You can think of them as immutable collections of data that can contain duplicates. In the case of DataSet the data is finite while for a DataStream the number of elements can be unbounded.

	These collections differ from regular Java collections in some key ways. First, they are immutable, meaning that once they are created you cannot add or remove elements. You can also not simply inspect the elements inside.

	A collection is initially created by adding a source in a Flink program and new collections are derived from these by transforming them using API methods such as map, filter and so on.
```

### Anatomy of a Flink Program

```
Flink programs look like regular programs that transform collections of data. Each program consists of the same basic parts:

Obtain an execution environment,
Load/create the initial data,
Specify transformations on this data,
Specify where to put the results of your computations,
Trigger the program execution
```

### flink的connect算子
```
	connect算子可以合并两个DataStream对象，两个DataStream的类型不必一致
	union	算子同样可以合并两个DataStream对象，两个DataStream的类型需要一致
```

### flink的自定义分区器
  步骤：
1. 继承Partitioner类，定义输入类型为范型 
2. 实现partition方法
3. 返回分区数
  ```java
  class LikerPartitioner extends Partitioner[String]{
  override def partition(key: String, numPartitions: Int): Int = {
    if(key == "liker"){
      0
    } else if(key == "erlei"){
      1
    } else if(key == "hehe"){
      2
    }else {
	  3
	}
  }
}
  ```
  
  ### 自定义DataSource
	DataSource有如下几种
	  1. SourceFunction
	  2. RichSourceFunction
	  2. ParallelSourceFunction
	  3. RichParallelSourceFunction
  ```
其中SourceFunction和ParallelSourceFunction的区别是
	1. SourceFunction 不能设置并行度
	2. ParallelSourceFunction可以设置并行度
	3. 而RichXXX主要是有两个open、close方法，这两个方法可以作为初始化和关闭资源使用。
  	
  ```
  ### 自定义MySql的DataSource
	步骤：
		1. 继承RichSourceFunction类
		2. open方法中建立连接
		3. run方法中执行查询语句。并将数据collect到sourceContetxt中
		4. close释放连接
  ```java
  class MySQLSource(sql:String) extends RichSourceFunction[Liker]{

  var connection:Connection = _
  var pstmt:PreparedStatement = _

  override def open(parameters: Configuration): Unit = {
    super.open(parameters)
    connection = JdbcUtil.connection
    pstmt = connection.prepareStatement(sql)
  }

  override def close(): Unit = {
    super.close()
    JdbcUtil.relase(connection,pstmt,null)
  }
  override def run(sourceContext: SourceFunction.SourceContext[Liker]): Unit = {
    var rs = pstmt.executeQuery()
    while (rs.next()){
      val liker = Liker(rs.getInt(0),rs.getString(1),rs.getString(2))
      sourceContext.collect(liker)

    }
  }

  override def cancel(): Unit = {
	
  }
}
  ```
  
  ### Execution Parameters
  
   ```
   Execution Parameters
   ```
   
   
   ### Controlling Latency
   ```
   Controlling Latency
   By default, elements are not transferred on the network one-by-one (which would cause unnecessary network traffic) but are buffered. The size of the buffers (which are actually transferred between machines) can be set in the Flink config files. While this method is good for optimizing throughput, it can cause latency issues when the incoming stream is not fast enough. To control throughput and latency, you can use env.setBufferTimeout(timeoutMillis) on the execution environment (or on individual operators) to set a maximum wait time for the buffers to fill up. After this time, the buffers are sent automatically even if they are not full. The default value for this timeout is 100 ms.
```
```scala
val env: LocalStreamEnvironment = StreamExecutionEnvironment.createLocalEnvironment
env.setBufferTimeout(timeoutMillis)
```
```
env.generateSequence(1,10).map(myMap).setBufferTimeout(timeoutMillis)
To maximize throughput, set setBufferTimeout(-1) which will remove the timeout and buffers will only be flushed when they are full. To minimize latency, set the timeout to a value close to 0 (for example 5 or 10 ms). A buffer timeout of 0 should be avoided, because it can cause severe performance degradation.
   ```
   