---
Title: 系统初始化器概览
---

## 系统初始化介绍

### 类名：ApplicationContextInitializer

### 介绍：Spring容器刷新之前执行的一个回调函数

### 作用：向SpringBoot容器中注册属性

### 使用：继承接口自定义实现

## Spring Factories Loader介绍

工厂加载机制

![image-20201224110932597](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224110932597.png)

![image-20201224111038037](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224111038037.png)

![image-20201224140642428](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224140642428.png)

![image-20201224140700197](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224140700197.png)

## 系统初始化器原理解析

![image-20201224140855596](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224140855596.png)

![image-20201224140902580](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224140902580.png)



## 总结

![image-20201224171415733](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224171415733.png)

![image-20201224171441327](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224171441327.png) 

![image-20201224171601568](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224171601568.png)

![image-20201224171804476](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224171804476.png)

![image-20201224172022274](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224172022274.png)

![image-20201224172052644](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20201224172052644.png)

```
    SpringApplication.run(DemoApplication.class, args){
    	run(new Object[] { source }, args){
    		new SpringApplication(sources){
    			initialize(sources){
    				setInitializers((Collection) getSpringFactoriesInstances(
				ApplicationContextInitializer.class){
						Set<String> names = new LinkedHashSet<String>(
						SpringFactoriesLoader.loadFactoryNames(type, classLoader)){
							// 这个方法里面回加载jar包中的spring.factories中的配置文件。
							// 以key-value的形式获取需要的内容
						}
						// 在这个方法里面根据获取的names（类的全限定名）通过反射创建对象
						List<T> instances = createSpringFactoriesInstances(type, parameterTypes,
				classLoader, args, names);
						// 对内容进行排序
						AnnotationAwareOrderComparator.sort(instances);
				});
    			}
    		}.run(args);
    	}
    }
```

