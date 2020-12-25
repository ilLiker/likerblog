---
Title: SpringBoot启动流程解析
---

![image-20201224094557526](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224094557526.png)

### 启动流程介绍

#### 框架初始化

##### 配置资源加载器

##### 配置primarySources

##### 应用环境检测

##### 配置系统初始化器

##### 配置应用监听器

##### 配置main方法所在类

#### 框架启动

计时器开始计时

Headless模式赋值

发送ApplicationStartingEvent

配置环境模块

发送ApplicationEnvironmentPreparedEvent

打印Banner

创建应用上下文对象

初始化失败分析器

![image-20201224102709557](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224102709557.png)

#### 自动化装配

![image-20201224102725032](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224102725032.png)

![image-20201224102922951](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224102922951.png)

![image-20201224102954307](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224102954307.png)

![image-20201224103010682](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224103010682.png)