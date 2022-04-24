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



#### Eureka server部署

pom.xml

```xml
<!--eureka-server -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    <version>2.2.10.RELEASE</version>
</dependency>
```

需要注意的是，Spring Cloud的版本需要和Spring Boot保持一致。



application.yml

```yml
server:
  port: 8300

spring:
  application:
    name: springcloud-eureka-server

eureka:
  instance:
    hostname: localhost
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/

```



EurekaApplication.java

```java
package com.gatsby.eureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@EnableEurekaServer
@SpringBootApplication
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```





### Ribbon

Spring Cloud Ribbon是基于Netflix Ribbon实现的一套客户端负载均衡的工具。它是一个基于HTTP和TCP的客户端负载均衡器。它可以通过在客户端中配置ribbonServerList来设置服务端列表去轮询访问以达到均衡负载的作用。

通过Spring Cloud的封装，可以让我们轻松地将面向服务的REST模版请求自动转换成客户端负载均衡的服务调用。Spring Cloud Ribbon虽然只是一个工具类框架，它不像服务注册中心、配置中心、API网关那样需要独立部署，但是它几乎存在于每一个Spring Cloud构建的微服务和基础设施中。因为微服务间的调用，API网关的请求转发等内容，实际上都是通过Ribbon来实现的，包括后续我们将要介绍的Feign，它也是基于Ribbon实现的工具。所以，对Spring Cloud Ribbon的理解和使用，对于我们使用Spring Cloud来构建微服务非常重要。

#### 负载均衡

在任何一个系统中，负载均衡都是必须要做的一件事，它是系统处理高并发、缓解网络压力和服务端扩容的重要手段之一。

常见的负载均衡手段主要分为两种：

- 服务端负载均衡
- 客户端负载均衡

#### 负载均衡的方式

服务端负载均衡是最常见的负载均衡方式，工作原理主要就是提供通过软件（例如Nginx）或者硬件（例如F5），在客户端和服务端之间加上负载均衡的设备，这个负载均衡服务器维护了一个可用的服务器清单，然后通过心跳来筛选可用的和不可用的服务器节点，以保证列表清单中的服务器都是可用的。

当负载均衡服务器搭建完成之后，客户端发起的请求并非直接发送给服务端，而是发送给负载均衡服务器，由负载均衡服务器按照某种算法（例如轮询，随机等等），从其维护的服务器列表中挑选一个可用的服务器，然后进行转发。

一般来说，负载均衡服务器具有这么几个特点：

- 需要建立一个独立的负载均衡服务器。
- 负载均衡是在客户端发送请求后进行的，因此客户端并不知道到底是哪个服务端提供的服务。（反向代理）
- 可用服务端清单存储在负载均衡服务器上。



### Open Feign

#### 服务调用

OpenFeign是一个显示声明式的WebService客户端。使用OpenFeign能让编写Web Service客户端更加简单。使用时只需定义服务接口，然后在上面添加注解。OpenFeign也支持可拔插式的编码和解码器。

Spring Cloud对Feign进行了封装，使其支持MVC注解和HttpMessageConverts。和Eureka（服务注册中心）和Ribbon组合可以实现负载均衡。在Spring Cloud中使用OpenFeign，可以做到使用HTTP请求访问远程服务，就像调用本地方法一样的，开发者完全感知不到这是在调用远程方法，更感知不到在访问HTTP请求，非常的方便。



#### Open Feign的作用

- OpenFeign的设计宗旨式简化Java Http客户端的开发。Feign在restTemplate的基础上做了进一步的封装，由其来帮助我们定义和实现依赖服务接口的定义。在OpenFeign的协助下，我们只需创建一个接口并使用注解的方式进行配置（类似于Dao接口上面的Mapper注解）即可完成对服务提供方的接口绑定，大大简化了Spring cloud Ribbon的开发，自动封装服务调用客户端的开发量。
- OpenFeign集成了Ribbon,利用Ribbon维护了服务列表，并且通过Ribbon实现了客户端的负载均衡。与Ribbon不同的是，通过OpenFeign只需要定义服务绑定接口且以申明式的方法，优雅而简单的实现了服务调用。



#### Feign和OpenFeign

**相同点**

- Feign 和 OpenFeign 都是 Spring Cloud 下的远程调用和负载均衡组件。
- Feign 和 OpenFeign 作用一样，都可以实现服务的远程调用和负载均衡。
- Feign 和 OpenFeign **都对 Ribbon 进行了集成**，都利用 Ribbon 维护了可用服务清单，并通过 Ribbon 实现了客户端的负载均衡。
- Feign 和 OpenFeign 都是在服务消费者（客户端）定义服务绑定接口并通过注解的方式进行配置，以实现远程服务的调用。

**不同点**

- Feign 和 OpenFeign 的依赖项不同，Feign 的依赖为 spring-cloud-starter-feign，而 OpenFeign 的依赖为 spring-cloud-starter-openfeign。
- Feign 和 OpenFeign 支持的注解不同，Feign 支持 Feign 注解和 JAX-RS 注解，但不支持 Spring MVC 注解；OpenFeign 除了支持 Feign 注解和 JAX-RS 注解外，还支持 Spring MVC 注解。



[详细讲解OpenFeign的使用姿势！-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/775626)

```text
Feign is a declarative web service client. It makes writing web service clients e
asier. To use Feign create an interface and annotate it. It has pluggable annotat
ion support including Feign annotations and JAX-RS annotations. Feign also suppor
ts pluggable encoders and decoders. Spring Cloud adds support for Spring MVC anno
tations and for using the same HttpMessageConverters used by default in Spring We
b. Spring Cloud integrates Eureka, Spring Cloud CircuitBreaker, as well as Spring
Cloud LoadBalancer to provide a load-balanced http client when using Feign.
```

Feign的作用在于，一个显式的WebService客户端，使用OpenFeign让Web Service更加简单，使用时只需要定义服务接口，然后在上面添加注解，同时OpenFeign配合Eureka对于负载均衡做了封装。

在Spring Cloud中使用OpenFeign，可以做到使用HTTP请求访问远程服务，就像调用本地方法一样的，开发者完全感知不到这是在调用远程方法，更感知不到在访问HTTP请求，非常的方便。

- OpenFeign的设计宗旨式简化Java Http客户端的开发。Feign在restTemplate的基础上做了进一步的封装，由其来帮助我们定义和实现依赖服务接口的定义。在OpenFeign的协助下，我们只需创建一个接口并使用注解的方式进行配置（类似于Dao接口上面的Mapper注解）即可完成对服务提供方的接口绑定，大大简化了Spring cloud Ribbon的开发，自动封装服务调用客户端的开发量。
- OpenFeign集成了Ribbon,利用ribbon维护了服务列表，并且通过ribbon实现了客户端的负载均衡。与ribbon不同的是，通过OpenFeign只需要定义服务绑定接口且以申明式的方法，优雅而简单的实现了服务调用。



#### OpenFeign配置

pom.xml

```shell
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.6.7</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>Feign-Provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Feign-Provider</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>2021.0.1</spring-cloud.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-webflux</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>io.projectreactor</groupId>
            <artifactId>reactor-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

不得不说pom.xml的配置确实是比较头疼的事情，因为Spring Boot和Spring Cloud存在大量版本冲突，最后想了一个办法，就是在idea创建新的项目的时候直接使用idea默认创建，那么版本冲突就可以得到解决。



application.yml

```yml
server:
  port: 8900

spring:
  application:
    name: feign-provider

eureka:
  client:
    register-with-eureka: false #服务消费者可以不向服务注册中心注册服务
    service-url:
      defaultZone: http://192.168.1.106:8300/eureka/
    fetch-registry: true  #服务消费者客户端需要去检索服务
```

端口是指这个服务的运行端口，name是这个服务的name，defaultZone是Eureka服务器的地址，**所以OpenFeign的前提是需要有一个注册中心，并且这个注册中心上已经有服务在跑。OpenFeign的本质只是省略IP端口的配置，这些配置由注册中心来做。**



FeignApplication.java

```java
package com.example.feignprovider;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication  //springboot启动注解
@EnableFeignClients     //启用OpenFeign
public class FeignProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(FeignProviderApplication.class, args);
    }

}
```



ServiceInterface.java

```java
package com.example.feignprovider.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

/**
 * @author -- gatsby
 * @date -- 2022/4/23
 * @description -- Port Service
 */


@Component
@FeignClient(value = "eureka-client-provider")
public interface PortService {
    @RequestMapping(value = "/provider/port", method = RequestMethod.GET)
    String getPort();
}
```

**这是OpenFeign的关键调用，OpenFeign通过注解@FeignClient来向Eureka请求，value值就是在Eureka中注册的那个服务，此时在Eureka服务器中，有一个服务注册为eureka-client-provider，通过注解@FeignClient获取到这个服务，然后在接口的方法中定义这个服务的请求地址`/provider/port`，这样我们就无需知道IP地址或者端口，而是直接调用这个服务的这个方法，并且注入进我们自己的interface。**



Controller.java

```java
package com.example.feignprovider.controller;

import com.example.feignprovider.service.PortService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

/**
 * @author -- gatsby
 * @date -- 2022/4/23
 * @description -- Port Controller
 */

@RestController
@RequestMapping(value = "/feign-provider")
public class PortController {
    PortService portService;

    @Autowired
    PortController(PortService portService) {
        this.portService = portService;
    }

    @GetMapping(value = "/port")
    public String getPort() {
        return portService.getPort();
    }
}
```

这个服务的Controller，直接将Service注入，并且调用，再提供给对外的路径，如果将这个服务注册到Eureka，那么这个路径也将成为一个Eureka的FeignClient供别的服务调用。





# Spring Cloud Alibaba

## Nacos

[Nacos Spring Boot 快速开始](https://nacos.io/zh-cn/docs/quick-start-spring-boot.html)

### Nacos和Eureka









### Spring Boot+Nacos	

























