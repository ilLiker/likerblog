---
title: Kakfa的ISR
tags: kafka,ISR,副本同步机制
renderNumberedHeading: true
grammar_cjkRuby: true
---

### Kafka Replica
```
1.每个topic的Replica个数必须小于replication-factor的个数，因为每个broker只能有一个partition的一个副本
2.当某个topic的replication-factor的个数大于1时，则每个partition都会有n个副本，包含leader和follower
3.所有partition的Replica会均匀分布到broker上
```

### 副本如何扩散出去消息？
```
	每个partition有一个leader和多个follower，当producer生产数据时，只会往leader中写数据。
	follower从leader中pull(拉去)数据,过程和consumer消费数据类似。
	读数据只会从leader中读，当leader挂掉时，follower顶上去作为leader提供服务，所以consumer并不会从follower中读取数据，follower只是作为副本和预备leader。
```
