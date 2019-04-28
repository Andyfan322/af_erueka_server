# Eureka 服务发现和注册  
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1528525933499&di=6eaa41b96db9e22d988ec1924db4c060&imgtype=0&src=http%3A%2F%2Fimg2.ph.126.net%2Fq3p8ZZ99ZJveeiVJ_4Ynxw%3D%3D%2F93449692285877210.png)
### 介绍

Eureka是Netflix开发的服务发现框架，本身是一个基于REST的服务，主要用于定位运行在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。SpringCloud将它集成在其子项目spring-cloud-netflix中，一实现SpringCloud的服务发现功能。
Eureka包含两个组件：Eureka Server和Eureka Client。
Eureka Server提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。
Eureka Client是一个java客户端，用于简化与Eureka Server的交互，客户端同时也就别一个内置的、使用轮询(round-robin)负载算法的负载均衡器。
在应用启动后，将会向Eureka Server发送心跳,默认周期为30秒，如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，Eureka Server将会从服务注册表中把这个服务节点移除(默认90秒)。
Eureka Server之间通过复制的方式完成数据的同步，Eureka还提供了客户端缓存机制，即使所有的Eureka Server都挂掉，客户端依然可以利用缓存中的信息消费其他服务的API。综上，Eureka通过心跳检查、客户端缓存等机制，确保了系统的高可用性、灵活性和可伸缩性。

### 一.Erueka和Zokeeper做服务注册和发现对比优势
  * #### 用zk劣势
  
   1. ZooKeeper无法很好的处理网络分区问题，当网络分区中的客户端节点无法到达Quorum时，会与ZooKeeper失去联系，从而也就无法使用其服务发现机制。
   1. 服务发现系统应该是一个AP系统，设计上针对可用性；而ZooKeeper是一个CP系统。
   1. ZooKeeper的设置和维护非常困难，实际操作的时候也容易出错，比如在客户端重建Watcher，处理Session和异常的时候。
当然，Peter Kelley提出的这几个问题并不是不能克服的，并不能说明基于ZooKeeper就不能做好一个服务发现系统，但是我们可能有更简洁的方案来实现。 

* #### Erueka优点

   1. 开源：大家可以对实现一探究竟，甚至修改源码。
   1. 可靠：经过Netflix多年的生产环境考验，使用应该比较靠谱省心
   1. 功能齐全：不但提供了完整的注册发现服务，还有Ribbon等可以配合使用的服务。
   1. 基于Java：对于Java程序员来说，使用起来，心里比较有底。spring cloud可以使用Spring Cloud, 与Eureka进行了很好的集成，使用起来非常方便。

### 二.本项目为Eureka集群
  1. 三个节点，分别为regisert1、regisert2、regisert3
  2. 默认项目不指定节点启动的为registry0(为本地特意准备，线上不需要考虑此节点)
  3. 在本地host中新增
      * localhost service-registry0
      * localhost service-registry1
      * localhost service-registry2
      * localhost service-registry3
  4. 部署本项目最好在三个独立机器或者容器内，对勇端口为9001，9002，9003
  5. 启动方式
    * nohup java -jar af-eureka-server-1.0.0-RELEASE.jar --spring.profiles.active=registry1 &
    * nohup java -jar af-eureka-server-1.0.0-RELEASE.jar --spring.profiles.active=registry2 &
    * nohup java -jar af-eureka-server-1.0.0-RELEASE.jar --spring.profiles.active=registry3 &