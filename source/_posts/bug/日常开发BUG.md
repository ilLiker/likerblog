---
title: 日常开发BUG
---

### Springboot + Mysql

```
insert 时间明明时正确的
insert into kd_user_temperature (created_on, updated_on, created_on_day, temperature, time_type, user_id) values ('2020-10-30 16:46:48.59', '2020-10-30 16:46:48.59', '2020-10-30 16:46:48.59', 38.0, 2, 9216)

查询的时候Springboot中打印的where中的查询时间时2020-10-30 00:00:00
查询日志中却是2020-10-30 08:00:00
2020-10-30T08:43:33.467369Z	 1138 Query	select usertemper0_.id as id1_9_, usertemper0_.created_on as created_2_9_, usertemper0_.updated_on as updated_3_9_, usertemper0_.created_on_day as created_4_9_, usertemper0_.temperature as temperat5_9_, usertemper0_.time_type as time_typ6_9_, usertemper0_.user_id as user_id7_9_ from kd_user_temperature usertemper0_ where usertemper0_.created_on_day='2020-10-30 08:00:00.0' and usertemper0_.user_id=9216
 
查看配置都没有问题
serverTimezone=Asia/Shanghai
docker中的时间也没有问题
数据库时间也没有问题

最后终于是定位了原因，既然是springboot所在的容器出了问题，那肯定是在Springboot的容器中的时区又问题。
回忆了一下build镜像和run容器时都没有设置时区，刚run好的容器时区是有问题的，当时的解决方式如下：
  docker exec -it container /bin/bash // 进入交互模式，container为容器ID或名称，下同
  ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
  docker restart container // 重启容器
  docker exec container date -R // 查看时区
  
  这种方式只是临时性解决时区问题。既然是这样那就试试使用根本的方式解决时区的问题，如下：
  1、利用Dockerfile创建镜像时。在Dockerfile中加入

  ENV TIME_ZONE=Asia/Shanghai 
  RUN ln -snf /usr/share/zoneinfo/$TIME_ZONE /etc/localtime && echo $TIME_ZONE > /etc/timezone
  2、容器创建时。加入时区挂载选项：-v /etc/localtime:/etc/localtime。实例：

  docker run -d  -v /etc/localtime:/etc/localtime --name 。。。
  
  最终解决。所以不要偷懒，还是老老实实的按照靠谱的方法解决。

```



### 查看数据库查询日志

```
show variables like '%general_log%';
set global general_log = on;
```

