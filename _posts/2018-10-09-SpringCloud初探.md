---
layout: post
title: "SpringCloud初探"
categories: misc
author: "yaabert" 
---

## springcloud

学习了下eureka注册中心 server和client的使用 仅仅是能连接而已
eureka仅仅为服务注册中心,相当于一个中介,服务提供者在这里注册他的服务,服务消费者在这里领取服务,避免了消费者直接连接服务,eureka则通过相对应的组件来管理服务调用,从而避免一些服务消费异常直接返回给消费者的异常情况.

##### 消费者和服务提供者
消费者使用@EnableDiscoveryClient注释启动类,设置spring.application.name , server.port,和eureka.client.service-url.defaultZone
服务注册者同样使用@EnableFeignClients注释外,再增加@EnableDiscoveryClient注释,用@FeignClient(name = "服务提供者的spring.application.name")注释新建接口,使之调用消费者服务,配置类的内容与服务注册者无异.               
    
        
##### 服务消费者熔断Hystrix
服务的熔断有两种方式 一种是使用feign 一种是使用rest+ribbon,其实feign里默认集成了ribbon,
而且代码编写上也是feign更加简洁,只需要注入接口 添加注释即可,ribbon则需要注入service,注入RestTemplate,在service中注入RestTmplate,使用RestTmplate的getForObject方法来进行访问注册服务,此时熔断只需要在该方法上注释@HystrixCommand(fallbackMethod ="熔断的方法"),说来惭愧 feign更方便但是不会用熔断,暂时不表 以后补上吧
    
