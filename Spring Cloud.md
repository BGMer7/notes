# Spring Cloud

## Micro Services

- 微服务架构是一种系统架构的设计风格。与传统的单体式架构（ALL IN ONE）不同，微服务架构提倡将一个单一的应用程序拆分成多个小型服务，这些小型服务都在各自独立的进程中运行，服务之间使用轻量级通信机制（通常是 HTTP RESTFUL API）进行通讯。
- 微服务架构是一种系统架构风格和思想，想要真正地搭建一套微服务系统，则需要微服务框架的支持。随着微服务的流行，很多编程语言都相继推出了它们的微服务框架。



**Java 微服务框架**

市面上的 Java 微服务框架主要有以下 5 种：

- Spring Cloud：它能够基于 REST 服务来构建服务，帮助架构师构建出一套完整的微服务技术生态链。
- Dropwizard：用于开发高性能和 Restful 的 Web 服务，对配置、应用程序指标、日志记录和操作工具都提供了开箱即用的支持。
- Restlet： 该框架遵循 RST 架构风格，可以帮助 Java 开发人员构建微服务。
- Spark：最好的 Java 微服务框架之一，该框架支持通过 Java 8 和 Kotlin 创建微服务架构的应用程序。
- Dubbo：由阿里巴巴开源的分布式服务治理框架。



随着微服务的火爆流行，国内外各大互联网公司都相继分享了他们在微服务架构中，针对不同场景出现的各种问题的解决方案和开源框架。

- **服务治理**：阿里巴巴开源的 Dubbo 和当当网在其基础上扩展出来的 DubboX、Netflix 的 Eureka 以及 Apache 的 Consul 等。
- **分布式配置管理**：百度的 Disconf、Netflix 的 Archaius、360 的 QConf、携程的 Apollo 以及 Spring Cloud 的 Config 等。
- **批量任务**：当当网的 Elastic-Job、LinkedIn 的 Azkaban 以及 Spring Cloud 的 Task 等。
- **服务跟踪**：京东的 Hydra、Spring Cloud 的 Sleuth 以及 Twitter 的 Zipkin 等。

以上这些微服务框架或解决方案都具有以下 2 个特点：

这种情况下，搭建一套微分布式微服务系统，就需要针对这些问题从诸多的解决方案中做出选择，这使得我们不得不将大量的精力花费在前期的调研、分析以及实验上。

Spring Cloud 被称为构建分布式微服务系统的“全家桶”，它并不是某一门技术，而是一系列微服务解决方案或框架的有序集合。它将市面上成熟的、经过验证的微服务框架整合起来，并通过 Spring Boot 的思想进行再封装，屏蔽调其中复杂的配置和实现原理，最终为开发人员提供了一套简单易懂、易部署和易维护的分布式系统开发工具包。

Spring Cloud 中包含了 spring-cloud-config、spring-cloud-bus 等近 20 个子项目，提供了服务治理、服务网关、智能路由、负载均衡、断路器、监控跟踪、分布式消息队列、配置管理等领域的解决方案。

Spring Cloud 并不是一个拿来即可用的框架，它是一种微服务规范，共有以下 2 代实现：

- 第一代实现：Spring Cloud Netflix 
- 第二代实现：Spring Cloud Alibaba 

这里我们介绍的 Spring Cloud 特指 Spring Cloud 的第一代实现。



## Spring Cloud

Spring Cloud常见的组件包括**Spring Cloud Gateway**、**Spring Cloud Config**、**Spring Cloud Bus**等近 20 个服务组件，这些组件提供了**服务治理、服务网关、智能路由、负载均衡、熔断器、监控跟踪、分布式消息队列、配置管理等领域**的解决方案。

| Spring Cloud组件     | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| Eureka               | Spring Cloud Netflix中服务治理工具，包含服务注册中心、服务注册和发现机制。 |
| Ribbon               | Spring Cloud Netflix中的服务调用和客户端负载均衡组件。       |
| Hystrix              | Spring Cloud Netflix中的容错管理组件，为服务中出现的延迟和故障提供强大的容错能力。 |
| Feign                | 基于 Ribbon 和 Hystrix 的声明式服务调用组件。                |
| Zuul                 | Spring Cloud Netflix中的网关组件，提供了智能路由、访问过滤等功能。 |
| Spring Cloud Gateway | 使用 Filter 链的方式提供了网关的基本功能，例如安全、监控/指标和限流等。 |
| Spring Cloud Config  | Spring Cloud 的配置管理工具，支持使用 Git 存储配置内容，实现应用配置的外部化存储，并支持在客户端对配置进行刷新、加密、解密等操作。 |
| Spring Cloud Bus     | Spring Cloud 的事件和消息总线，主要用于在集群中传播事件或状态变化，以触发后续的处理，例如动态刷新配置。 |
| Spring Cloud Stream  | Spring Cloud 的消息中间件组件，它集成了 Apache Kafka 和 RabbitMQ 等消息中间件。 |

> 注：Netflix 是美国的一个在线视频网站，它是公认的大规模生产级微服务的杰出实践者，微服务界的翘楚。Netflix 的开源组件已经在其大规模分布式微服务环境中经过了多年的生产实战验证，成熟且可靠。



### Spring Cloud和Spring Boot

1. Spring Boot 和 Spring Cloud 分工不同

   Spring Boot 是一个基于 Spring 的快速开发框架，它能够帮助开发者迅速搭 Web 工程。在微服务开发中，Spring Boot 专注于快速、方便地开发单个微服务。

   Spring Cloud 是微服务架构下的一站式解决方案。Spring Cloud 专注于全局微服务的协调和治理工作。换句话说，**Spring Cloud 相当于微服务的大管家，负责将 Spring Boot 开发的一个个微服务管理起来，并为它们提供配置管理、服务发现、断路器、路由、微代理、事件总线、决策竞选以及分布式会话等服务。**

2. Spring Cloud是基于Spring Boot实现的

   Spring Cloud 是基于 Spring Boot 实现的。与 Spring Boot 类似，Spring Cloud 也为提供了一系列 Starter，这些 Starter 是 Spring Cloud 使用 Spring Boot 思想对各个微服务框架进行再封装的产物。它们屏蔽了这些微服务框架中复杂的配置和实现原理，使开发人员能够快速、方便地使用 Spring Cloud 搭建一套分布式微服务系统。

3. Spring Cloud 和 Spring Boot的依赖项数量不一样

   Spring Boot 属于一种轻量级的框架，构建 Spring Boot 工程所需的依赖较少。

   Spring Cloud 是一系列微服务框架技术的集合体，它的每个组件都需要一个独立的依赖项（Starter POM），因此想要构建一套完整的 Spring Cloud 工程往往需要大量的依赖项。

4. Spring Cloud 不能脱离 Spring Boot单独存在

   Spring Boot 不需要 Spring Cloud，就能直接创建可独立运行的工程或模块。

   Spring Cloud 是基于 Spring Boot 实现的，它不能独立创建工程或模块，更不能脱离 Spring Boot 独立运行。

   > 注意：虽然 Spring Boot 能够用于开发单个微服务，但它并不具备管理和协调微服务的能力，因此它只能算是一个微服务快速开发框架，而非微服务框架。



### Eureka

#### 服务注册

[微服务架构之「 服务注册 」 - 不止思考 - 博客园 (cnblogs.com)](https://www.cnblogs.com/jsjwk/p/10620420.html)

了解Eureka之前首先更重要的是先了解**服务注册**这个概念。

我们先来举一个生活中的例子：在以前互联网还不够发达的时候，“114号码百事通”大家应该很熟悉，有啥需求就会去打个电话查询一下。比如想知道附近电影院电话是多少，就会先去打114问一下。那114为啥知道这么多信息呢，还不是因为各类服务者（商店、机构等）都已经在114上登记了嘛。所以这里的“114百事通”就相当于一个服务注册中心了，这里的各类商店机构就相当于可以提供不同服务的服务者了，而打电话的我们就是去寻找这些服务的消费者了。

我们再来回到微服务架构中，**一般集群都会部署很多个微服务节点。**这些节点一般也具备这2种角色，称为：**“服务的提供者” 和 “服务的消费者”。**

“服务消费者”需要调用“服务提供者”的API来获得服务。当“服务提供者”的节点有增加或减少的时候，也得让调用者（“服务消费者”）及时的知晓。而在大规模集群中，一般节点数目都很多，节点变化频繁，通过手动去维护这些节点的状态是不现实的，因此需要一个叫做“服务注册中心”的组件来实现。

“服务提供者”将自己的服务地址等信息登记到“服务注册中心”中，调用者（“服务消费者”）需要的时候，每次都先去“服务注册中心”查询即可。既解决了人工维护微服务节点状态的问题，也能解决多节点间负载均衡的问题。

#### 服务注册的原理

在分析其原理之前，我们先来看一下这里包含的一些角色，有三类：**“服务提供者”、“服务消费者”、“服务注册中心”**。

其中“服务提供者”需要将自己的服务信息注册到“服务注册中心”里面。而“服务消费者”需要到“服务注册中心”里面去查询有哪些服务可以调用。因此，我们可以分为两个视角去分析原理：

1. 从服务提供者的角度来看，服务提供者自己向注册中心进行注册

   1. 服务提供者自己注册

      自己注册就是指微服务节点在启动的时候，自己去服务注册中心登记注册了，把自己的信息和状态传过去。这种方式整体结构比较简单，对于注册中心而言也比较省事，但是对于微服务节点而言，每个微服务都得包含这么一段注册的逻辑代码，架构上看起来不是很优美。

   2. 第三方注册

      第三方注册通过一个Service Manager，这个**服务管理者会去主动管理所有的服务，以轮询或者其他方式去检测哪些微服务正在运行，会将微服务实例自动更新到服务注册中心，这是目前比较普遍的形式，也是Eureka正在使用的方式。**

2. 从服务消费者的角度来看，服务消费者向服务注册中心查询和调用服务

   1. 客户端模式

      ![img](https://img2018.cnblogs.com/blog/1453917/201903/1453917-20190329120107074-1590407600.png)

      在客户端模式下，服务消费者向服务注册中心查询自己想要的服务的地址，服务消费者会根据查询的地址自己再去访问微服务地址，（图中的第3步 API Gateway是可选项，有API Gateway的情况下，API Gateway起到负载均衡作用，没有第3步的话，那就是Client直接调用Micro service，需要Client自己写负载均衡逻辑）。

   2. 代理模式

      ![img](https://img2018.cnblogs.com/blog/1453917/201903/1453917-20190329120114615-1478757290.png)

      在代理模式下，服务消费者与微服务、服务注册中心中间有一个API Gateway组件相隔，服务消费者只需要对接API Gateway即可，API Gateway去访问服务注册中心、微服务，都对于外界隐藏，只需要最终API Gateway将结果放回给Client即可。

      这种模式下，对于Client来说，负担减少了，但是API Gateway的负担加重了，不仅需要确保自己的稳定性，还需要处理一些负载均衡的逻辑。

#### Eureka两大组件

Spring Cloud 使用 Spring Boot 思想为 Eureka 增加了自动化配置，开发人员只需要引入相关依赖和注解，就能将 Spring Boot 构建的微服务轻松地与 Eureka 进行整合。

Eureka 采用 CS（Client/Server，客户端/服务器） 架构，它包括以下两大组件：

- Eureka Server: Eureka 服务注册中心，主要提供服务注册功能。当微服务启动时，会将自己的服务注册到Eureka Server，Eureka Server维护了一个可用列表，存储了所有注册到Eureka Server的可用服务的信息，这些服务可以直接在Eureka Server的管理界面中看到。
- Eureka Client: Eureka 客户端，通常是指，微服务中的各种微服务，主要用于和Eureka Server交互，在微服务启动之后，会向Server发送心跳包，若 Eureka Server 在多个心跳周期内没有接收到某个 Eureka Client 的心跳，Eureka Server 将它从可用服务列表中移除。

![Eureka 服务注册与发现](http://c.biancheng.net/uploads/allimg/211210/1010305209-0.png)



上图中共涉及到以下 3 个角色：

- **服务注册中心（Register Service）**：它是一个 Eureka Server，用于提供服务注册和发现功能。
- **服务提供者（Provider Service）**：它是一个 Eureka Client，用于提供服务。它将自己提供的服务注册到服务注册中心，以供服务消费者发现。
- **服务消费者（Consumer Service）**：它是一个 Eureka Client，用于消费服务。它可以从服务注册中心获取服务列表，调用所需的服务。



#### Eureka服务注册流程

1. 搭建一个Eureka Server作为服务注册中心；
2. 服务提供者 Eureka Client 启动时，会把当前服务器的信息以服务名（spring.application.name）的方式注册到服务注册中心；
3. 服务消费者 Eureka Client 启动时，也会向服务注册中心注册；
4. 服务消费者会获取到一份服务可用的列表，该列表中包含了所有注册到服务注册中心的服务信息（包括服务提供者和自身的信息）；
5. 在获得了可用服务列表后，服务消费者通过 HTTP 或消息中间件远程调用服务提供者提供的服务。







































