---
layout:     post
title:      "服务注册中心学习01"
subtitle:   " \"SpringCloud\""
date:       2021-06-04 12:00:00
author:     "Soma"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - SpringCould
    - Eureka
---

## 什么是服务治理

传统的rpc远程调用框架中，管理服务之间依赖关系比较复杂，所以需要使用服务治理，用于管理服务之间的依赖关系，实现**服务调用、负载均衡、容错、服务的发现与注册**。

## Eureka

Eureka包含两个组件：

- Eureka Server提供服务注册服务

  各个微服务节点通过配置启动后，会在Eureka Server中进行注册，Eureka Server的服务注册表中会存储所有可用服务节点的信息。

- Eureka Client通过注册中心进行访问

  是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询的负载均衡器。应用启动后，会向Eureka Server发送心跳（默认30s）。如果Eureka Server在多个心跳周期内没有收到某个节点的心跳，Eureka Server将其从服务注册表中移除（默认90s）。



### Eureka集群

实现负载均衡+故障容错

> 相互注册、相互守望

![image-604-01](/img/image-604-01.png)

如上图所示，一般注册中心和服务的提供方都会采用集群配置。

核心注解<code>@LoadBalanced</code>，默认轮询。

### 服务发现Discovery

<code>@EnableDiscoveryClient</code>

获取服务列表<code>discoveryClient.getServices()</code>

获取服务实例<code>discoveryClient.getInstances()</code>

### Eureka自我保护机制

保护模式主要用于一组客户端和Eureka Server之间存在网络区分场景下的保护。一旦进入保护模式，Eureka Server将会尝试保护其服务注册表中的信息，不再删除其中数据，也就是不会注销任何微服务。

防止网络分区故障等因素，导致微服务与Eureka Server无法正常通信，此时微服务本身是正常的，不应该注销。当Eureka Server短时间内丢失过多客户端的心跳时，就会开启自我保护机制。

保证了高可用（A）和分区容错性（P）。

> 但Eureka已经停更了。



## ZooKeeper

zookeeper是一个分布式的协调服务，可以作为注册中心，但实际用得不多。

只是不用区分Client和Server，统一<code>@EnableDiscoveryClient</code>。

但是没有Eureka的自我保护机制，会存在临时节点和持久节点，创建的时候 -e 区分。对于临时节点，zk一定时间监测不到服务的心跳就将服务注销。

说到zk，就涉及到一个面试题。

**zk如何实现分布式锁？**

假设 Client A、B、C 要去获取zk上的一个名为 lock 的持久节点，即为分布式锁。

Client要获取 lock ，就要先调用createNode方法，在 lock 下创建一个带序号的临时顺序节点，然后调用getChildren方法，获取 lock 下所有临时顺序节点，如果发现自己创建的临时顺序节点序号排在最前面，就代表获取到了锁，可以访问资源，访问结束删除节点。否则监听比自己小的节点的删除事件，直到该节点删除，再循环判断是否获取到了锁。

假设A获取到了锁，则B和C必须等待，如果此时A宕机了，zk会有重试机制，如果多次重试之后还是监测不到A的心跳，才会删除A。

## Consul

Consul是开源的分布式服务发现和配置管理系统，用Go语言开发的。

提供了微服务系统中的服务治理、配置中心、控制总线等功能。

和zk差不多。

## CAP

- C（Consistency）：一致性
- A（Availability）：可用性
- P（Partition Tolerance）：分区容错性

一个分布式系统无法同时满足三个需求，最多只能同时较好满足两个。



> 但其实这三个现在都不是主流了，现在都用SpingCloud Alibab的Nacos，同时作为注册中心、配置中心、服务总线。

--------

——Soma 2021.6.4 23:00

