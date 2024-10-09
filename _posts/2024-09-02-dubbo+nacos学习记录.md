---
layout:     post
title:      SpringBoot+Dubbo+nacos学习记录和demo
subtitle:   demo
date:       2024-09-02
author:     toan
catalog:	true
tags:
    - SpringBoot
    - nacos
    - dubbo
---



## 参考记录

主要参考了以下两个文章：[SpringBoot整合nacos和dubbo](https://blog.csdn.net/tian330726/article/details/140859257) 和 [SpringBoot整合dubbo和nacos](https://blog.csdn.net/qq_33479841/article/details/134379397)

* `dubbo`官网文档：[dubbo官网](https://cn.dubbo.apache.org/zh-cn/overview/home/)

## demo

[dubbo+nacos demo工程](https://github.com/toanwang/SpringComponent/tree/master/dubbo-demo)

* 需要本地先部署`nacos`

## dubbo

写`demo`主需要三个注解

* `api`模块
  * 定义`consumer`和`provider`需要使用的接口

* `consumer`模块
  * `@DubboReference(version = "1.0.0", timeout = 1000, retries = 1)`：声明`api`中的接口需要用`dubbo`调用，指定版本号，超时时间和重试次数
* `provider`模块
  * `EnableDubbo`：`dubbo`的自动配置和服务扫描
  * `@DubboService(version = "1.0.0")`：声明集成`api`中的类，指定接口版本号

### application

```yaml
server:
  port: 8080
dubbo:
  application:
    name: dubbo-provider #服务名称
    version: 1.0.0 # 服务版本号
  registry:
    address: nacos://localhost:8848?namespace=30e4480b-f903-4d26-8084-cedff611a340&username=nacos&password=nacos
  protocol:
    name: dubbo
```

* `consumer`和`provider`的配置类似，自己测试过程中发现写这些就可以把`demo`跑起来
  * 这里面的`version`是服务版本号，代码里面调用的是接口版本号，两者不一样

### interview

[dubbo面试](https://javabetter.cn/interview/dubbo-17.html#_3-%E8%83%BD%E8%AF%B4%E4%B8%8B-dubbo-%E7%9A%84%E6%80%BB%E4%BD%93%E7%9A%84%E8%B0%83%E7%94%A8%E8%BF%87%E7%A8%8B%E5%90%97)

1. 服务调用过程
2. 支持的协议
3. 用到的设计模式：责任链；观察者；装饰器；工厂模式；代理模式；适配器模式

## nacos

`nacos`本地启动之后，可以再`localhost:8848/nacos`查看服务注册情况

* 服务列表展示的是注册的服务和接口，消费者不在里面