---
layout:     post
title:      "服务调用学习"
subtitle:   " \"SpringCloud\""
date:       2021-06-06 12:00:00
author:     "Soma"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - SpringCould
    - OpenFeign
---

## Ribbon

主要功能是提供客户端的负载均衡算法和服务调用。

### Load Balance

负载均衡，将用户请求以某种算法平摊到不同服务器上，达到系统的HA（高可用）

### Ribbon本地负载均衡客户端 vs Nginx服务端负载均衡

- 对于Nginx（集中式LB），反向代理，负载均衡由服务端实现，所有客户端的请求打到Nginx，Nginx实现转发请求。

  - **正向代理和反向代理**

    正向代理就是，客户端与最终请求的服务器之间的一个服务器，作为跳板，例如Vpn。

    反向代理就是，客户端请求一个代理服务器，一般都是一个IP，该IP作转发，将请求作负载均衡分配到不同的服务器，既不暴露真实服务器的IP和端口号，也能保证高HA。

    总结就是看代理客户端还是代理服务端。

- 对于Ribbon本地负载均衡（进程内LB），在调用微服务接口的时候，会从注册中心的注册表中将服务列表缓存到JVM本地，在本地根据策略选择服务器实现RPC远程调用。

### Ribbon负载均衡算法

- 轮询
- 随机
- 相应速度快的
- 最好可用性的
- 并发较小的
- 符合判断性能和可用性

## OpenFeign

Feign是一个声明式的WebService客户端，使用方法只要定义一个服务接口然后在上面添加注解。

相较于Ribbon+RestTemplate模版化的调用方法，Feign封装这些过程，简化了服务调用的过程。OpenFeign在Feign的基础上支持了SpringMVC的注解，@FeignClient注解可以解析@RequestMapping下的接口，通过动态代理的方式生成实现类，实现类中作负载均衡并调用其他服务。

<code>@EnableFeignClients</code>用在客户端启动类上的

<code>@FeignClient</code>用在服务端接口上的

### OpenFeign超时控制

OpenFeign默认等待1s，超过后则报错，可以在yml中开启配置调整时间。

### OpenFeign日志增强

Feign提供了日志打印功能，对调用情况监控和输出，可以调整日志级别。

--------

——Soma 2021.6.6 23:00

