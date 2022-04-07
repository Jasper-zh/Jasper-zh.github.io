---
title: Spring Cloud微服务详解
date: 2021-07-22 11:58:17
tags: 总结文档
copyright: false
---

{% note warning simple %}
超长警告！！！善用目录！！！
{% endnote %}

[TOC]

## 一、微服务架构

### 1.1 互联网应用架构演进

随着互联⽹的发展，⽤户群体逐渐扩大，⽹站的流量成倍增⻓，常规的单体架构已⽆法满⾜请求压⼒和业务的快速迭代，架构的变化势在必⾏。下⾯我们就以拉勾网的架构演进为例，从最开始的单体架构分析，⼀步步的到现在的微服务架构。

淘宝：LAMP，Linux、Apache、MySQL、PHP

#### 1.1.1 单体应用架构

在诞⽣之初，拉勾的⽤户量、数据量规模都⽐较⼩，项目所有的功能模块都放在一个工程中编码、编译、打包并且部署在一个Tomcat容器中的架构模式就是单体应用架构，这样的架构既简单实 ⽤、便于维护，成本⼜低，成为了那个时代的主流架构⽅式。

![image-20220309171818712](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309171818712.png)

![image-20210802103712708](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210802103712708.png)

**优点：**

* **高效开发**：项⽬前期开发节奏快，团队成员少的时候能够快速迭代
* **架构简单**：MVC架构，只需要借助IDE开发、调试即可
* **易于测试**：只需要通过单元测试或者浏览器完成
* **易于部署**：打包成单⼀可执⾏的jar或者打成war包放到容器内启动

单体架构的应用比较容易部署、测试， 在项目的初期，单体应用可以很好地运行。然而，随着需求的不断增加， 越来越多的人加入开发团队，代码库也在飞速地膨胀。慢慢地，单体应用变得越来越臃肿，可维护性、灵活性逐渐降低，维护成本越来越高。

**缺点：**

* **可靠性差**： 某个应用Bug，例如死循环、内存溢出等， 可能会导致整个应用的崩溃
* **复杂性高**： 以一个百万行级别的单体应用为例，整个项目包含的模块多、模块的边界模糊、 依赖关系不清晰、 代码质量参差不齐、 混乱地堆砌在一起。使得整个项目非常复杂。
* **扩展能力受限**： 单体应用只能作为一个整体进行扩展，无法根据业务模块的需要进行伸缩。例如，应用中有的模块是计算密集型的，它需要强劲的CPU； 有的模块则是IO密集型的，需要更大的内存。 由于这些模块部署在一起，不得不在硬件的选择上做出妥协。

业务量上涨之后，单体应用架构进一步丰富变化，比如应用集群部署、使用Nginx进行负载均衡、增加缓存服务器、增加文件服务器、数据库集群并做读写分离等，通过以上措施增强应对高并发的能力、应对一定的复杂业务场景，但依然属于单体应用架构。

![image-20210802103956900](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210802103956900.png)

#### 1.1.2 垂直应用架构

为了避免上⾯提到的那些问题，开始做模块的垂直划分，做垂直划分的原则是基于现有的业务特性来做，核心目标标第⼀个是为了业务之间互不影响，第⼆个是在研发团队的壮⼤后为了提⾼效率，减少组件之间的依赖。

![image-20210802104039404](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210802104039404.png)

**优点：**

* 系统拆分实现了流量分担，解决了并发问题
* 可以针对不同模块进⾏优化
* ⽅便⽔平扩展，负载均衡，容错率提⾼
* 系统间相互独⽴，互不影响，新的业务迭代时更加⾼效

**缺点：**

* 服务之间相互调⽤，如果某个服务的端⼝或者ip地址发⽣改变，调⽤的系统得⼿动改变
* 搭建集群之后，实现负载均衡⽐较复杂，如：内⽹负载，在迁移机器时会影响调⽤⽅的路 由，导致线上故障
* 服务之间调⽤⽅式不统⼀，基于 httpclient 、 webservice ，接⼝协议不统⼀
* 服务监控不到位：除了依靠端⼝、进程的监控，调⽤的成功率、失败率、总耗时等等这些监 控指标是没有的



#### 1.1.3 SOA应用架构

​	在做了垂直划分以后，模块随之增多，维护的成本在也变⾼，⼀些通⽤的业务和模块重复的越来越多，为了解决上⾯提到的接⼝协议不统⼀、服务⽆法监控、服务的负载均衡，引⼊了阿⾥巴巴开源的Dubbo ，⼀款⾼性能、轻量级的开源Java RPC框架，可以和Spring框架无缝集成。它提供了三⼤核⼼能力：⾯向接⼝的远程⽅法调⽤，智能容错和负载均衡，以及服务⾃动注册和发现。

​	SOA (Service-Oriented Architecture)，即面向服务的架构。根据实际业务，把系统拆分成合适的、独立部署的模块，模块之间相互独立（通过Webservice/Dubbo等技术进行通信）。

​	优点：分布式、松耦合、扩展灵活、可重用。

​	缺点：服务抽取粒度较大、服务调用方和提供方耦合度较高（接口耦合度）

![image-20210802104640560](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210802104640560.png)



#### 1.1.4 微服务应用架构

微服务架构可以说是SOA架构的一种拓展，这种架构模式下它**拆分粒度更小**、服务更独立。把应用拆分成为一个个微小的服务，不同的服务可以使用不同的开发语言和存储，服务之间往往通过Restful等轻量级通信。微服务架构关键在于**微小、独立、轻量级通信**。

微服务是在 SOA 上做的升华粒度更加细致，微服务架构强调的⼀个重点是**业务需要彻底的组件化和服务化**

![image-20210802104754079](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210802104754079.png)

**微服务架构和SOA架构相似又不同**

​	微服务架构和SOA架构很明显的一个区别就是**服务拆分粒度的不同**，但是对于拉勾的架构发展来说，我们所看到的SOA阶段其实服务拆分粒度相对来说已经比较细了（超前哦！），所以上述拉勾SOA到拉勾微服务，从服务拆分上来说变化并不大，只是引入了相对完整的新一代Spring Cloud微服务技术。自然，上述我们看到的都是拉勾架构演变的阶段结果，每一个阶段其实都经历了很多变化，拉勾的服务拆分其实也是走过了从粗到细，并非绝对的一步到位。

**举个拉勾案例来说明SOA和微服务拆分粒度不同**

​	在SOA架构的初期，“简历投递模块”和“人才搜索模块”都有简历内容展示的需求，只不过说可能略有区别，一开始在两个模块中各维护了一套简历查询和展示的代码；后期我们将服务更细粒度拆分，拆分出简历基础服务，那么不同模块调用这个基础服务即可。

### 1.2 微服务架构体现的思想及优缺点

​	微服务架构设计的核心思想就是**“微”**，拆分的粒度相对比较小，这样的话单一职责、开发的耦合度就会降低、微小的功能可以独立部署扩展、灵活性强，升级改造影响范围小。

**微服务架构的优点：** 

* 微服务很小，便于特定业务功能的聚焦
* 微服务很小，每个微服务都可以被一个小团队单独实施（开发、测试、部署上线、运维），团队合作一定程度解耦，便于实施敏捷开发
* 微服务很小，便于重用和模块之间的组装
* 微服务很独立，那么不同的微服务可以使用不同的语言开发，松耦合
* 微服务架构下，我们更容易引入新技术

**微服务架构的缺点**

* 微服务架构下，分布式复杂难以管理，当服务数量增加，管理将越加复杂
* 微服务架构下，分布式链路跟踪难等

### 1.3 微服务架构当中的核心概念

* **服务注册与服务发现**

  * 列如：

    ​	职位搜索  ——》简历服务

    ​	**服务提供者** : 简历服务

    ​	**服务消费者** : 职位搜索

  * **服务注册：**服务提供者将所提供服务的信息（服务器IP和端口、服务访问协议等）注册/登记到注册中心

  * **服务发现：**服务消费者能够从注册中心获取到较为实时的服务列表，然后根究一定的策略选择一个服务访问

  ![image-20220309171852970](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309171852970.png)

  ![image-20220309171908943](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309171908943.png)

* **负载均衡**

  负载均衡即将请求压力分配到多个服务器（应用服务器、数据库服务器等），以此来提高服务的性能、可靠性

  ![image-20210803102123540](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803102123540.png)

* **熔断**

  熔断即断路保护。微服务架构中，如果下游服务因访问压力过大而响应变慢或失败，上游服务为了保护系统整体可用性，可以暂时切断对下游服务的调用。这种牺牲局部，保全整体的措施就叫做熔断。

  ![image-20210803102219278](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803102219278.png)

  ![image-20210803102234092](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803102234092.png)

* **链路追踪**

  微服务架构越发流行，一个项目往往拆分成很多个服务，那么一次请求就需要涉及到很多个服务。不同的微服务可能是由不同的团队开发、可能使用不同的编程语言实现、整个项目也有可能部署在了很多服务器上（甚至百台、千台）横跨多个不同的数据中心。所谓链路追踪，就是对一次请求涉及的很多个服务链路进行日志记录、性能监控

  ![image-20210803102328144](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803102328144.png)

* **API网关**

  微服务架构下，不同的微服务往往会有不同的访问地址，客户端可能需要调用多个服务的接口才能完成一个业务需求，如果让客户端直接与各个微服务通信可能出现：

  1）客户端需要调用不同的url地址，增加了维护调用难度

  2）在一定的场景下，也存在跨域请求的问题（前后端分离就会碰到跨域问题，原本我们在后端采用Cors就能解决，现在利用网关，那么就放在网关这层做好了）

  3）每个微服务都需要进行单独的身份认证

  那么，API网关就可以较好的统一处理上述问题，API请求调用统一接入API网关层，由网关转发请求。API网关更专注在安全、路由、流量等问题的处理上（微服务团队专注于处理业务逻辑即可），它的功能比如：

  1. 统一接入（路由）

  2. 安全防护（统一鉴权，负责网关访问身份认证验证，与“访问认证中心”通信，实际认证业务逻辑交移“访问认证中心”处理）
  3. 黑白名单（实现通过IP地址控制禁止访问网关功能，控制访问）
  4. 协议适配（实现通信协议校验、适配转换的功能）
  5. 流量管控（限流）
  6. 长短链接支持
  7. 容错能力（负载均衡）



![image-20210803102810160](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803102810160.png)



## 二、Spring Cloud概述

### 2.1 什么是Spring Cloud？

**[百度百科]**Spring Cloud是一系列框架的有序集合。它利用Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot的开发风格做到一键启动和部署。**Spring Cloud并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包**。

Spring Cloud是一系列框架的有序集合（Spring Cloud是一个规范）

开发服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等

利用Spring Boot的开发便利性简化了微服务架构的开发（自动装配）

![image-20210803103015056](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803103015056.png)

这里，我们需要注意，Spring Cloud其实是一套规范，是一套用于构建微服务架构的规范，而不是一个可以拿来即用的框架（所谓规范就是应该有哪些功能组件，然后组件之间怎么配合，共同完成什么事情）。在这个规范之下第三方的Netflix公司开发了一些组件、Spring官方开发了一些框架/组件，包括第三方的阿里巴巴开发了一套框架/组件集合Spring Cloud Alibaba，这些才是Spring Cloud规范的实现。

Netflix搞了一套 ，简称SCN

Spring Cloud 吸收了Netflix公司的产品基础之上自己也搞了几个组件

阿里巴巴在之前的基础上搞出了一堆微服务组件,Spring Cloud Alibaba（SCA）

### 2.2 Spring Cloud解决了什么问题？

​	Spring Cloud 规范及实现意图要解决的问题其实就是微服务架构实施过程中存在的一些问题，比如微服务架构中的服务注册发现问题、网络问题（比如熔断场景）、统一认证安全授权问题、负载均衡问题、链路追踪等问题。

* Distributed/versioned configuration （分布式/版本化配置）
* Service registration and discovery （服务注册和发现）
* Routing （智能路由）
* Service-to-service calls （服务调用）
* Load balancing （负载均衡）
* Circuit Breakers （熔断器）
* Global locks （全局锁）
* Leadership election and cluster state （ 选举与集群状态管理）
* Distributed messaging （分布式消息传递平台）

### 2.3 Spring Cloud架构

​	如前所述，Spring Cloud是一个微服务相关规范，这个规范意图为搭建微服务架构提供一站式服务，**采用组件（框架）化机制**定义一系列组件，各类组件针对性的处理微服务中的特定问题，这些组件共同来构成**Spring Cloud微服务技术栈**

#### 2.3.1 Spring Cloud核心组件

​	Spring Cloud 生态圈中的组件，按照发展可以分为第一代 Spring Cloud组件和第二代 SpringCloud组件。

<table>
    <tr>
    	<th>组件</th>
        <th>第一代Spring Cloud(Netflix,SCN)</th>
        <th>第二代Spring Cloud(Alibaba,SCA)</th>
    </tr>
    <tr>
    	<td>注册中心</td>
        <td>Netflix Eureka</td>
        <td>阿里 Nacos</td>
    </tr>
    <tr>
    	<td>客户端负载均衡</td>
        <td>Netflix Ribbon</td>
        <td>阿里Dubbo LB、官方Loadbalancer</td>
    </tr>
    <tr>
    	<td>熔断器</td>
        <td>Netflix Hystrix</td>
        <td>阿里 Sentinel</td>
    </tr>
    <tr>
    	<td>网关</td>
        <td>Netflix Zuul(性能一般未来将退出Spring Could生态圈)</td>
        <td>官方Spring Cloud Gateway</td>
    </tr>
    <tr>
    	<td>配置中心</td>
        <td>官方Spring Cloud Config</td>
        <td>阿里Nacos、携程Apollo</td>
    </tr>
    <tr>
    	<td>服务调用</td>
        <td>Netflix Feign</td>
        <td>阿里Dubbo RPC</td>
    </tr>
    <tr>
    	<td>消息驱动</td>
        <td>官方 Spring Cloud Stream</td>
        <td></td>
    </tr>
    <tr>
    	<td>链路追踪</td>
        <td>官方 Spring Cloud Sleuth/Zipkin</td>
        <td></td>
    </tr>
    <tr>
    	<td></td>
        <td></td>
        <td>阿里 seata分布式事务方案</td>
    </tr>
</table>





#### 2.3.2 Spring Cloud体系结构（组件协同工作机制）

![image-20210803104843188](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803104843188.png)

Spring Cloud中的各组件协同工作，才能够支持一个完整的微服务架构。比如

* 注册中心负责服务的注册与发现，很好将各服务连接起来
* API网关负责转发所有外来的请求
* 断路器负责监控服务之间的调用情况，连续多次失败进行熔断保护。
* 配置中心提供了统一的配置信息管理服务,可以实时的通知各个服务获取最新的配置信息

### 2.4 Spring Cloud与Dubbo进行对比

​	Dubbo是阿里巴巴公司开源的一个高性能优秀的服务框架，基于RPC调用，对于目前使用率较高的Spring Cloud Netflix来说，它是基于HTTP的，所以效率上没有Dubbo高，但问题在于Dubbo体系的组件不全，不能够提供一站式解决方案，比如服务注册与发现需要借助于Zookeeper等实现，而SpringCloud Netflix则是真正的提供了一站式服务化解决方案，且有Spring大家族背景。

​	前些年，Dubbo使用率高于SpringCloud，但目前Spring Cloud在服务化/微服务解决方案中已经有了非常好的发展趋势。

![image-20210803105134868](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803105134868.png)

### 2.5 Spring Cloud与Spring Boot的关系

​	Spring Cloud 只是利用了Spring Boot 的特点，让我们能够快速的实现微服务组件开发，否则不使用Spring Boot的话，我们在使用Spring Cloud时，每一个组件的相关Jar包都需要我们自己导入配置以及需要开发人员考虑兼容性等各种情况。所以Spring Boot是我们快速把Spring Cloud微服务技术应用起来的一种方式。



## 三、案例准备

### 3.1 案例说明

本部分我们按照普通方式模拟一个微服务之间的调用，后续我们将一步步使用Spring Cloud的组件对案例进行改造。

需求：

![image-20210803105223408](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803105223408.png)

![image-20210803105231868](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803105231868.png)

完整业务流程图：

![image-20210803105249088](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803105249088.png)

![image-20220309172244762](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309172244762.png)

### 3.2 案例数据库环境准备

商品信息表：

```sql
CREATE TABLE products( 
    id INT PRIMARY KEY AUTO_INCREMENT, 
    NAME VARCHAR(50), #商品名称 
    price DOUBLE, 
    flag VARCHAR(2), #上架状态 
    goods_desc VARCHAR(100), #商品描述 
    images VARCHAR(400), #商品图片 
    goods_stock INT, #商品库存 
    goods_type VARCHAR(20) #商品类型 
);
```



### 3.3 案例工程

​	我们基于SpringBoot来构造工程环境，我们的工程模块关系如下所示：

![image-20220310134421779](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310134421779.png)

#### 3.3.1 页面静态化微服务

在Idea中新建module，命名为mycloud

**pom.xml**

```xml
 
<packaging>pom</packaging>
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.6.RELEASE</version>
</parent>
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-logging</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.4</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>11</source>
                <target>11</target>
                <encoding>utf-8</encoding>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <executions>
                <execution>
                    <goals>
                        <goal>repackage</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```



#### 3.3.2 公共组件微服务

1) 在公共组件微服务中引入数据库驱动及mybatis-plus

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mycloud</artifactId>
        <groupId>com.hao</groupId>
        <version>1.0</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>service-common</artifactId>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.3.2</version>
        </dependency>
        <!--pojo持久化使用-->
        <dependency>
            <groupId>javax.persistence</groupId>
            <artifactId>javax.persistence-api</artifactId>
            <version>2.2</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
    </dependencies>
</project>
```

2) 生成数据库实体类：com.hao.pojo.**Product**

```java
package com.hao.pojo;

import lombok.Data;

import javax.persistence.Id;
import javax.persistence.Table;

/**
 * @author hao
 * @date 2022/3/10 11:55
 */
@Data
@Table(name = "product")
public class Product {
    @Id
    private Integer id;
    private String name;
    private double price;
    private String flag;
    private String goodsDesc;
    private String images;
    private long goodsStock;
    private String goodsType;
}

```



#### 3.3.3 商品微服务

商品微服务是服务提供者，页面静态化微服务是服务的消费者

创建商品微服务service-product，继承mycloud

1）在商品微服务的pom文件中，引入公共组件坐标

```xml
<dependency>
    <groupId>com.hao</groupId>
    <artifactId>service-common</artifactId>
    <version>1.0</version>
</dependency>
```

2）在yml文件中配置端口、应用名、数据库连接等信息

```yaml
server:
  port: 9000 # 后期该微服务多实例，9000（10个以内）
Spring:
  application:
    name: service-product
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://xxx.xxx.xxx:3306/test?useUnicode=true&characterEncoding=utf8&serverTimezone=UTC
    username: root
    password: 1234
```

3）Mapper接口开发

```java
package com.hao.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.hao.pojo.Product;

/**
 * @author hao
 * @date 2022/3/10 12:33
 */
/**
 * 现在使用的Mybatis-plus组件，该组件是Mybatis的加强版 
 * 能够与SpringBoot进行非常友好的整合，对比Mybatis框架只有使用便捷的改变 
 * 没有具体功能的改变 
 * 具体使用：让具体的Mapper接口继承BaseMapper即可 
 */ 
public interface ProductMapper extends BaseMapper<Product> {
}
```

4）serive层开发

```java
package com.hao.service.impl;

import com.hao.mapper.ProductMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import com.hao.pojo.Product;
import com.hao.service.ProductService;

/**
 * @author hao
 * @date 2022/3/10 12:35
 */
@Service
public class ProductServiceImpl implements ProductService {
    @Autowired
    ProductMapper productMapper;
    @Override
    public Product getProductById(Integer id) {
        return productMapper.selectById(id);
    }
}

```

5）controller层开发

```java
package com.hao.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import com.hao.pojo.Product;
import com.hao.service.ProductService;

/**
 * @author hao
 * @date 2022/3/10 12:37
 */
@RestController
@RequestMapping("/product")
public class ProductController {
    @Autowired
    ProductService productService;
    @RequestMapping("/query/{id}")
    public Product getProduct(@PathVariable Integer id){
       return productService.getProductById(id);
    }
}
```

6）启动类

```java
package com.hao;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @author hao
 * @date 2022/3/10 12:43
 */
@SpringBootApplication
@MapperScan("com.hao.mapper")
public class ProductApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProductApplication.class);
    }
}

```



#### 3.3.4 页面静态化微服务

1）在pom文件中，引入公共组件依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mycloud</artifactId>
        <groupId>com.hao</groupId>
        <version>1.0</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>service-page</artifactId>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.hao</groupId>
            <artifactId>service-common</artifactId>
            <version>1.0</version>
        </dependency>
    </dependencies>

</project>
```

2）在yml文件中配置端口、应用名、数据库连接等信息

```yaml
server:
  port: 9100 # 后期该微服务多实例，9000（10个以内）
Spring:
  application:
    name: service-page
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://xxx.xxx.xxx:3306/test?useUnicode=true&characterEncoding=utf8&serverTimezone=UTC
    username: root
    password: 1234
```

3）编写PageController，在PageController中调用商品微服务对应的URL

```java
/**
 * @author hao
 * @date 2022/3/10 12:54
 */
@RestController
@RequestMapping("/page")
public class PageController {
    @Autowired
    private RestTemplate restTemplate;
    @GetMapping("/getProduct/{id}")
    public Product getProductById(@PathVariable Integer id){
        Product product = restTemplate.getForObject("http://localhost:9000/product/query/"+id,Product.class);
        System.out.println("当前是静态化服务，已从商品服务获得数据：" + product);
        return product;
    }
}
```

4）编写启动类，注入RestTemplate

```java
/**
 * @author hao
 * @date 2022/3/10 13:00
 */
@SpringBootApplication
public class PageApplication {
    public static void main(String[] args) {
        SpringApplication.run(PageApplication.class);
    }
    @Bean
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```



### 3.4 案例代码问题分析

我们在页面静态化微服务中使用RestTemplate调用商品微服务的商品状态接口时（Restful API 接口）。在微服务**分布式集群环境**下会存在什么问题呢？怎么解决？

**存在的问题：**

1. 在服务消费者中，我们把url地址硬编码到代码中，不方便后期维护。
2. 服务提供者只有一个服务，即便服务提供者形成集群，服务消费者还需要自己实现负载均衡。
3. 在服务消费者中，不清楚服务提供者的状态。
4. 服务消费者调用服务提供者时候，如果出现故障能否及时发现不向用户抛出异常页面？
5. RestTemplate这种请求调用方式是否还有优化空间？能不能类似于Dubbo那样玩？
6. 这么多的微服务统一认证如何实现？
7. 配置文件每次都修改好多个很麻烦！？
8. ....

上述分析出的问题，其实就是微服务架构中必然面临的一些问题：

1. 服务管理：自动注册与发现、状态监管
2. 服务负载均衡
3. 熔断
4. 远程过程调用
5. 网关拦截、路由转发
6. 统一认证
7. 集中式配置管理，配置信息实时自动更新

这些问题，Spring Cloud 体系都有解决方案



## 四、第一代Spring Cloud核心组件

说明：上面提到网关组件Zuul性能一般，未来将退出Spring Cloud 生态圈，所以这边直接就换成GateWay。

各组件整体结构如下：

![image-20210803112626313](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803112626313.png)

从形式上来说，Feign一个顶三，Feign = RestTemplate + Ribbon + Hystrix

![image-20210803112642721](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803112642721.png)

### 4.1 Eureka服务注册中心

常用的服务注册中心：Eureka、Nacos、Zookeeper、Consul

#### 4.1.1 关于服务注册中心

**注意：服务注册中心本质上是为了解耦服务提供者和服务消费者。**

服务消费者 → 服务提供者

服务消费者 → 服务注册中心 → 服务提供者

​	对于任何一个微服务，原则上都应存在或者支持多个提供者（比如商品微服务部署多个实例），这是由微服务的**分布式属性**决定的。

​	更进一步，为了支持弹性扩、缩容特性，一个微服务的提供者的数量和分布往往是动态变化的，也是无法预先确定的。因此，原本在单体应用阶段常用的静态负载均衡机制就不再适用了，需要引入额外的组件来管理微服务提供者的注册与发现，而这个组件就是服务注册中心。

##### （1）注册中心实现原理

![image-20210803112923411](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803112923411.png)

![image-20210803112935155](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210803112935155.png)

​	分布式微服务架构中，服务注册中心用于存储服务提供者地址信息、服务发布相关的属性信息，消费者通过主动查询和被动通知的方式获取服务提供者的地址信息，而不再需要通过硬编码方式得到提供者的地址信息。消费者只需要知道当前系统发布了那些服务，而不需要知道服务具体存在于什么位置，这就是透明化路由。

1. 服务提供者启动

2. 服务提供者将相关服务信息主动注册到注册中心

3. 服务消费者获取服务注册信息：

   pull模式：服务消费者可以主动拉取可用的服务提供者清单

   push模式：服务消费者订阅服务（当服务提供者有变化时，注册中心也会主动推送更新后的服务清单给消费者

4. 服务消费者直接调用服务提供者

   另外，注册中心也需要完成服务提供者的健康监控，当发现服务提供者失效时需要及时剔除

   

##### （2）主流注册中心对比

* **Zookeeper**

  Dubbo + Zookeeper

  Zookeeper它是一个分布式服务框架，是Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等。

  简单来说zookeeper本质 = 存储 + 监听通知。

  Zookeeper 用来做服务注册中心，主要是因为它具有节点变更通知功能，只要客户端监听相关服务节点，服务节点的所有变更，都能及时的通知到监听客户端，这样作为调用方只要使用Zookeeper 的客户端就能实现服务节点的订阅和变更通知功能了，非常方便。另外，Zookeeper可用性也可以，因为只要半数以上的选举节点存活，整个集群就是可用的，最少节点数为3。

* **Eureka**

  由Netflix开源，并被Pivatal集成到SpringCloud体系中，它是基于 RestfulAPI 风格开发的服务注册与发现组件。

* **Consul**

  Consul是由HashiCorp基于Go语言开发的支持多数据中心分布式高可用的服务发布和注册服务软件， 采用Raft算法保证服务的一致性，且支持健康检查。

* **Nacos**

  Nacos是一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。简单来说Nacos 就是 注册中心 + 配置中心的组合，帮助我们解决微服务开发必会涉及到的服务注册 与发现，服务配置，服务管理等问题。Nacos 是 Spring Cloud Alibaba 核心组件之一，负责服务注册与发现，还有配置。

<table>
    <tr>
    	<th>组件名</th>
        <th>语言</th>
        <th>CAP</th>
        <th>对外暴露接口</th>
    </tr>
    <tr>
    	<td>Eureka</td>
        <td>Java</td>
        <td>AP（自我保护机制，保证可用）</td>
        <td>HTTP</td>
    </tr>
    <tr>
    	<td>Consul</td>
        <td>Go</td>
        <td>CP</td>
        <td>HTTP/DNS</td>
    </tr>
    <tr>
    	<td>Zookeeper</td>
        <td>Java</td>
        <td>CP</td>
        <td>客户端</td>
    </tr>
    <tr>
    	<td>Nacos</td>
        <td>Java</td>
        <td>支持AP/CP切换</td>
        <td>HTTP</td>
    </tr>
</table>



CAP定理又称CAP原则，指的是在一个分布式系统中，Consistency（一致性）、 Availability（可用性）、Partition tolerance（分区容错性），最多只能同时三个特性中的两个，三者不可兼得。

P：分区容错性：分布式系统在遇到某节点或网络分区故障的时候，仍然能够对外提供满足一致性或可用性的服务（一定的要满足的）

C：数据一致性：all nodes see the same data at the same time

A：高可用：Reads and writes always succeed

**CAP不可能同时满足三个，要么是AP，要么是CP**

#### 4.1.2 服务注册中心组件 Eureka

服务注册中心的一般原理、对比了主流的服务注册中心方案，目光聚焦Eureka

**Eureka基础架构**

![image-20220309172742068](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309172742068.png)

![image-20220309172753324](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309172753324.png)

**Eureka交互原理**

![image-20220309172839418](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309172839418.png)

![image-20220309172855023](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309172855023.png)



Eureka 包含两个组件：Eureka Server 和 Eureka Client，Eureka Client是一个Java客户端，用于简化与Eureka Server的交互；Eureka Server提供服务发现的能力，各个微服务启动时，会通过EurekaClient向Eureka Server 进行注册自己的信息（例如网络信息），Eureka Server会存储该服务的信息；

* 图中us-east-1c、us-east-1d，us-east-1e代表不同的区也就是不同的机房
* 图中每一个Eureka Server都是一个集群。
* 图中Application Service作为服务提供者向Eureka Server中注册服务，Eureka Server接受到注册事件会在集群和分区中进行数据同步，Application Client作为消费端（服务消费者）可以从Eureka Server中获取到服务注册信息，进行服务调用。
* 微服务启动后，会周期性地向Eureka Server发送心跳（**默认周期为30秒**，默认Eureka Server 90S会将还没有续约的给剔除）以续约自己的信息
* Eureka Server在一定时间内没有接收到某个微服务节点的心跳，**Eureka Server将会注销该微服务节点（默认90秒）**
* 每个Eureka Server同时也是Eureka Client，多个Eureka Server之间通过复制的方式完成服务注册列表的同步
* Eureka Client会缓存Eureka Server中的信息。即使所有的Eureka Server节点都宕掉，服务消费者依然可以使用缓存中的信息找到服务提供者

**Eureka通过心跳检测、健康检查和客户端缓存等机制，提高系统的灵活性、可伸缩性和高可用性。**

#### 4.1.3 搭建单例Eureka Server服务注册中心

**实现过程**

1. 单实例Eureka Server—>访问管理界面
2. 服务提供者（商品微服务注册到集群）
3. 服务消费者（页面静态化微服务注册到Eureka/从Eureka Server获取服务信息）
4. 完成调用

**1、搭建Eureka Server服务**

my-parent父工程中设置Spring Cloud依赖版本信息

Spring Cloud是一个综合项目，下面有很多子项目，比如Eureka子项目

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Greenwich.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

**2、my-cloud-eureka工程pom.xml中引入依赖**

```xml
<dependencies>
    <!--Eureka server依赖-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
</dependencies>
```

**3、在yml文件中配置Eureka Server服务的端口，服务名等信息**

```yaml
#Eureka server服务端口 
server: 
    port: 9200 
spring: 
    application: 
        name: my-cloud-eureka # 应用名称，会在Eureka中作为服务的id标识 （serviceId） 
eureka: 
    instance: 
        hostname: localhost 
    client: 
        service-url: # 客户端与EurekaServer交互的地址，如果是集群，也需要写其它Server的地址 
            defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/ 
        register-with-eureka: false # 自己就是服务不需要注册自己 
        fetch-registry: false #自己就是服务不需要从Eureka Server获取服务信息,默认为true，置为false
```

**4、编写启动类，声明当前服务为Eureka注册中心**

```java
package com.hao.eureka;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
@SpringBootApplication 
//// 声明本项目是一个Eureka服务 
@EnableEurekaServer 
public class EurekaApplication {
	public static void main(String[] args) {
		SpringApplication.run(EurekaApplication.class,args);
	}
}
```

**5、访问9200端口**

![image-20220309180402488](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309180402488.png)



![image-20220309180411456](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309180411456.png)

![image-20220309180423208](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220309180423208.png)



**6、其他服务注册到Eureka**

其他服务pom添加Eureka Client依赖

```xml
<!--Eureka client-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

yml配置Eureka服务端信息

```yml
eureka: 
    client: 
        serviceUrl: # eureka server的路径 
            defaultZone: http://localhost:9200/eureka/ 
    instance: 
        #使用ip注册，否则会使用主机名注册了（此处考虑到对老版本的兼容，新版本经过实验都是ip） 
        prefer-ip-address: true 
        #自定义实例显示格式，加上版本号，便于多版本管理，注意是ip-address，早期版本是ipAddress 
        instance-id: ${spring.cloud.client.ip-address}:${spring.application.name}:${server.port}:@project.version@
```

修改启动类

```java
@SpringBootApplication 
@EnableDiscoveryClient 
//@EnableEurekaClient 
public class OtherApplication {
	public static void main(String[] args) {
		SpringApplication.run(OtherApplication.class,args);
	}
	@Bean 
	public RestTemplate restTemplate(){
		return new RestTemplate();
	}
}
```



#### 4.1.4 搭建Eureka Server高可用集群

在互联网应用当中，服务实例很少有单个的。

如果EurekaServer只有一个实例，该实例挂掉，正好微服务消费者本地缓存列表中的服务实例也不可用，那么这个时候整个系统都会受到影响。

在生产环境当中，我们会配置Eureka Server集群实现高可用。Eureka Server集群之中的节点通过点对点（P2P）通信的方式共享服务注册表。我们开启两台Eureka Server来搭建集群。

由于是在个人计算机中进行测试很难模拟多主机的情况，Eureka配置Server集群时需要执行host地址。所以需要修改个人电脑的host地址

```
127.0.0.1 eurekaA
127.0.0.1 eurekaB
```

将my-cloud-eureka复制一份为my-cloud-eureka9201

**1、修改eureka服务的配置**

**eurekaA**

```yml
server: 
    port: 9200 
spring: 
    application: 
        name: my-cloud-eureka # 应用名称，会在Eureka中作为服务的id标识 （serviceId） 
eureka: 
    instance: 
        hostname: eurekaA 
    client: 
        register-with-eureka: true 
        fetch-registry: true 
        serviceUrl: 
            defaultZone: http://eurekaA:9200/eureka,http://eurekaB:9201/eureka
```

**eurekaB**

```yaml
server: 
    port: 9201
spring: 
    application: 
        name: my-cloud-eureka # 应用名称，会在Eureka中作为服务的id标识 （serviceId） 
eureka: 
    instance: 
        hostname: eurekaA 
    client: 
        register-with-eureka: true 
        fetch-registry: true 
        serviceUrl: 
            defaultZone: http://eurekaA:9200/eureka,http://eurekaB:9201/eureka
```

**商品微服务**

```yaml
server:
    port: 9000
Spring:
    application:
        name: service-product
    datasource:
        ...
eureka:
    client:
        serviceUrl:
            # 把eureka集群中的所有url都填写了进来，也可以只写一台，因为各个 eureka server可以同步注册表
            defaultZone: http://eurekaA:9200/eureka,http://eurekaB:9201/eureka
    instance:
        prefer-ip-address: true
        instance-id: ${spring.cloud.client.ip-address}:${spring.application.name}:${server.port}:@project.version@
```

**页面静态化服务**

```yaml
server:
    port: 9100
Spring:
    application:
        name: service-page
    datasource:
        ...
eureka:
    client:
        serviceUrl:
            # 把eureka集群中的所有url都填写了进来，也可以只写一台，因为各个 eureka server可以同步注册表
            defaultZone: http://eurekaA:9200/eureka,http://eurekaB:9201/eureka
    instance:
        #使用ip注册，否则会使用主机名注册了（此处考虑到对老版本的兼容，新版本经过实验都是ip）
        prefer-ip-address: true
        #自定义实例显示格式，加上版本号，便于多版本管理，注意是ip-address，早期版本是ipAddress
        instance-id: ${spring.cloud.client.ip-address}:${spring.application.name}:${server.port}:@project.version@
```

**消费者调用提供者**

改造页面静态化微服务：之前是直接通过RestTemplate写死URL进行调用，现在通过Eureka方式进行调用。

```java
@RestController 
@RequestMapping("/page") 
public class PageController {
	@Autowired 
	private RestTemplate restTemplate;
	@Autowired 
	private DiscoveryClient discoveryClient;
    
    @RequestMapping("/getProduct01/{id}")
    public Product findDataById(@PathVariable Integer id){
        //1.获得Eureka中注册的service-product实例集合
        List<ServiceInstance> instances = discoveryClient.getInstances("service-product");
        //2.获得实例集合中的第一个
        ServiceInstance instance = instances.get(0);
        //3.根据实例信息拼接IP地址
        String host = instance.getHost();
        int port = instance.getPort();
        String url = "http://"+host+":"+port+"/product/query/"+id;
        //4.调用
        Product product = restTemplate.getForObject(url, Product.class);
        System.out.println("从service-product获得product对象:"+product);
        return product;
    }
}
```



#### 4.1.5 Eureka细节详解

##### （1）Eureka元数据详解

Eureka的元数据有两种：标准元数据和自定义元数据。

**标准元数据：**主机名、IP地址、端口号等信息，这些信息都会被发布在服务注册表中，用于服务之间的调用。

**自定义元数据：**可以使用eureka.instance.metadata-map配置，符合KEY/VALUE的存储格式。这些元数据可以在远程客户端中访问。

```yaml
instance: 
   #使用ip注册，否则会使用主机名注册了（此处考虑到对老版本的兼容，新版本经过实验都是ip） 
   prefer-ip-address: true 
   #自定义实例显示格式，加上版本号，便于多版本管理，注意是ip-address，早期版本是ipAddress 
   instance-id: ${spring.cloud.client.ip-address}:${spring.application.name}:${server.port}:@project.version@ 
   metadata-map: 
       ip: 192.168.200.128 
       port: 10000 
       user: hao 
       pwd: 123456
```

我们可以在程序中可以使用DiscoveryClient 获取指定微服务的所有元数据信息

```java
@RestController 
@RequestMapping("/metadata") 
public class MetadataController {
	@Autowired 
	    private DiscoveryClient discoveryClient;
	@RequestMapping("show") 
	    public String showMetadata(){
		String result = "";
		List<ServiceInstance> instances = discoveryClient.getInstances("service-page");
		for (ServiceInstance instance:instances) {
			//获取服务元数据 
			Map<String, String> metadata = instance.getMetadata();
			Set<Map.Entry<String, String>> entries = metadata.entrySet();
			for (Map.Entry<String,String> entry : entries){
				String key = entry.getKey();
				String value = entry.getValue();
				result+="key:"+key+",value:"+value;
			}
		}
		return result;
	}
}
```

标准元素据直接在instance属性下，而自定义元素据在instances的metadata当中

##### （2）Eureka客户端详解

服务提供者（也是Eureka客户端）要向EurekaServer注册服务，并完成服务续约等工作

**服务注册详解（服务提供者）**

* 当我们导入了eureka-client依赖坐标，配置Eureka服务注册中心地址
* 服务在启动时会向注册中心发起注册请求，携带服务元数据信息
* Eureka注册中心会把服务的信息保存在Map中。

**服务续约详解（服务提供者）**

服务每隔30秒会向注册中心续约(心跳)一次（也称为报活），如果没有续约，租约在90秒后到期，然后服务会被失效。每隔30秒的续约操作我们称之为心跳检测

* Eureka Client ：30S续约一次，在Eureka Server更新自己的状态 (Client端进行配置)
* Eureka Server：90S还没有进行续约，将该微服务实例从服务注册表（Map）剔除 (Client端进行配置)
* Eureka Client： 30S拉取服务最新的注册表并缓存到本地 (Client端进行配置)

往往不需要我们调整这两个配置

```yaml
#向Eureka服务中心集群注册服务 
eureka: 
    instance: 
        # 租约续约间隔时间，默认30秒 
        lease-renewal-interval-in-seconds: 30 
        # 租约到期，服务时效时间，默认值90秒,服务超过90秒没有发生心跳，EurekaServer会将服务从列表移除
        lease-expiration-duration-in-seconds: 90
```

**获取服务列表（服务注册表）详解（服务消费者）**

每隔30秒服务会从注册中心中拉取一份服务列表，这个时间可以通过配置修改。往往不需要我们调整

```yaml
#向Eureka服务中心集群注册服务 
eureka: 
    client: 
        # 每隔多久拉取一次服务列表 
        registry-fetch-interval-seconds: 30
```

* 服务消费者启动时，从 EurekaServer服务列表获取只读备份，缓存到本地

* 每隔30秒，会重新获取并更新数据

* 每隔30秒的时间可以通过配置eureka.client.registry-fetch-interval-seconds修改

##### （3）Eureka服务端详解

**服务下线：**

* 当服务正常关闭操作时，会发送服务下线的REST请求给EurekaServer。 
* 服务中心接受到请求后，将该服务置为下线状态

**失效剔除**

Eureka Server会定时（间隔值是eureka.server.eviction-interval-timer-in-ms，默认60s）进行检查，如果发现实例在在一定时间（此值由客户端设置的eureka.instance.lease-expiration-duration-inseconds定义，默认值为90s）内没有收到心跳，则会注销此实例。

**自我保护机制**

**自我保护模式正是一种针对网络异常波动的安全保护措施，使用自我保护模式能使Eureka集群更加的健壮、稳定的运行。**

自我保护机制的工作机制是：**如果在15分钟内超过85%的客户端节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了网络故障，Eureka Server自动进入自我保护机制**，此时会出现以下几种情况：

* Eureka Server不再从注册列表中移除长时间没收到心跳而应该过期的服务。

* Eureka Server仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上，保证当前节点依然可用。

* 当网络稳定时，当前Eureka Server新的注册信息会被同步到其它节点中。

因此Eureka Server可以很好的应对因网络故障导致部分节点失联的情况，而不会像ZK那样如果有一半不可用的情况会导致整个集群不可用而变成瘫痪。

**为什么会有自我保护机制？**

默认情况下，如果Eureka Server在一定时间内（默认90秒）没有接收到某个微服务实例的心跳，Eureka Server将会移除该实例。但是当网络分区故障发生时，微服务与Eureka Server之间无法正常通信，而微服务本身是正常运行的，此时不应该移除这个微服务，所以引入了自我保护机制。

服务中心页面会显示如下提示信息

![image-20220310103358803](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310103358803.png)

我们在单机测试的时候很容易满足心跳失败比例在 15 分钟之内低于 85%，这个时候就会触发 Eureka的保护机制，一旦开启了保护机制（**默认开启**），则服务注册中心维护的服务实例就不是那么准确了，此时我们通过修改Eureka Server的配置文件来关闭保护机制，这样可以确保注册中心中不可用的实例被及时的剔除（**不推荐**）。

```yaml
eureka: 
    server: 
        enable-self-preservation: false # 关闭自我保护模式（缺省为打开）
```

**经验：建议生产环境打开自我保护机制**

### 4.2 Ribbon负载均衡

#### 4.2.1 关于负载均衡

负载均衡一般分为**服务器端负载均衡**和**客户端负载均衡**

所谓**服务器端负载均衡**，比如Nginx、F5这些，请求到达服务器之后由这些负载均衡器根据一定的算法将请求路由到目标服务器处理。

所谓**客户端负载均衡**，比如我们要说的Ribbon，服务消费者客户端会有一个服务器地址列表，调用方在请求前通过一定的负载均衡算法选择一个服务器进行访问，负载均衡算法的执行是在请求客户端进行。

Ribbon是Netflix发布的负载均衡器。Eureka一般配合Ribbon进行使用，Ribbon利用从Eureka中读取到服务信息，在调用服务提供者提供的服务时，会根据一定的算法进行负载。

![image-20220310103626139](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310103626139.png)



#### 4.2.2 Ribbon高级应用

**需求：**复制商品微服务9001，在9000和9001编写Controller，返回服务实例端口。**Page微服务**中通过负载均衡策略调用service-product的controller

![image-20220310103703718](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310103703718.png)

在微服务中使用Ribbon不需要额外导入依赖坐标，微服务中引入过eureka-client相关依赖，会自动引入Ribbon相关依赖坐标。

**代码中使用如下，在RestTemplate上添加对应注解即可**

```java
@SpringBootApplication 
@EnableDiscoveryClient //@EnableEurekaClient 
public class PageApplication { 
    public static void main(String[] args) { 
        SpringApplication.run(PageApplication.class,args); 
    }
    @Bean 
    //Ribbon负载均衡
    @LoadBalanced 
    public RestTemplate restTemplate(){ 
        return new RestTemplate(); 
    } 
}
```

**创建serivce-product-9001微服务，创建ServerConfigController，定义方法返回当前微服务所使用的容器端口号**

修改服务提供者api返回值，返回当前实例的端口号，便于观察负载情况

```java
@RestController 
@RequestMapping("/product") 
public class ServerConfigController {
	@Value("${server.port}") 
	private String serverPort;
	@requestMapping("server") 
	public String findServerPort(){
		return serverPort;
	}
}
```

**在页面静态化微服务中调用server-product下的资源路径：http://server-product/product/server**

```java
@RequestMapping("/getPort") 
public String getProductServerPort(){ 
    String url = "http://service-product/server/query"; 
    return restTemplate.getForObject(url,String.class); 
}
```

![gif](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/gif.gif)

测试可以知道默认是一个轮询的负载均衡策略



#### 4.2.3 Ribbon负载均衡策略

Ribbon内置了多种负载均衡策略，内部负责复杂均衡的顶级接口为 com.netflix.loadbalancer.IRule ，

接口简介:

```java
package com.netflix.loadbalancer;

public interface IRule{
    public Server choose(Object key);
    public void setLoadBalancer(ILoadBalancer lb);
    public ILoadBalancer getLoadBalancer();
}
```

![image-20220310110914782](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310110914782.png)

![image-20220310110952631](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310110952631.png)

**修改负载均衡策略：**

```yaml
#针对的被调用方微服务名称,不加就是全局生效 
service-product: 
    ribbon: 
        NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule #随机策略
```

```yaml
#针对的被调用方微服务名称,不加就是全局生效 
service-product: 
    ribbon: 
        NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RoundRobinRule #轮询策略
```



#### 4.2.4 Ribbon核心源码刨析

**Ribbon工作原理**

![image-20220310111130927](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111130927.png)

* 老规矩：SpringCloud充分利用了SpringBoot的自动装配特点，找spring.factories配置文件

  ```properties
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=org.springframework.cloud.netflix.ribbon.RibbonAutoConfiguration
  ```

  ![image-20220310111300665](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111300665.png)

* LoadBalancerAutoConfiguration 类中配置

  **装配验证：**

  ![image-20220310111334187](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111334187.png)

  **自动注入**

  ![image-20220310111350726](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111350726.png)

  **注入RestTemplate定制器**

  为restTemplate对象设置loadBalancerInterceptor

  ![image-20220310111430509](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111430509.png)

  到这里，我们明白，添加了注解的RestTemplate对象会被添加一个拦截器LoadBalancerInterceptor，该拦截器就是后续拦截请求进行负载处理的。



### 4.3 Hystrix熔断器

属于一种容错机制

#### 4.3.1 微服务当中的雪崩效应

当山坡积雪内部的内聚力抗拒不了它所受到的重力拉引时，便向下滑动，引起大量雪体崩塌，人们把这种自然现象称作雪崩。

微服务中，一个请求可能需要多个微服务接口才能实现，会形成复杂的调用链路。

**服务雪崩效应：**是一种因“服务提供者的不可用”（原因）导致“服务调用者不可用”（结果），并将不可用逐渐放大的现象。

![image-20220310111744722](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111744722.png)

![image-20220310111756101](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111756101.png)

![image-20220310111803904](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111803904.png)

![image-20220310111809715](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111809715.png)

![image-20220310111825742](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310111825742.png)

扇入：代表着该微服务被调用的次数，扇入大，说明该模块复用性好

扇出：该微服务调用其他微服务的个数，扇出大，说明业务逻辑复杂

扇入大是一个好事，扇出大不一定是好事



在微服务架构中，一个应用可能会有多个微服务组成，微服务之间的数据交互通过远程过程调用完成。这就带来一个问题，假设微服务A调用微服务B和微服务C，微服务B和微服务C又调用其它的微服务，这就是所谓的“扇出”。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的“雪崩效应”。

如图中所示，最下游**商品微服务**响应时间过长，大量请求阻塞，大量线程不会释放，会导致服务器资源耗尽，最终导致上游服务甚至整个系统瘫痪。

**形成原因**

服务雪崩的过程可以分为三个阶段：

* 服务提供者不可用
* 重试加大请求流量
* 服务调用者不可用

服务雪崩的每个阶段都可能由不同的原因造成：

![image-20220310112018485](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310112018485.png)



#### 4.3.2 雪崩效应解决方案

从可用性可靠性着想，为防止系统的整体缓慢甚至崩溃，采用的技术手段；

下面，我们介绍三种技术手段应对微服务中的雪崩效应，这三种手段都是从系统可用性、可靠性角度出发，尽量防止系统整体缓慢甚至瘫痪。

**服务熔断**

熔断机制是应对雪崩效应的一种微服务链路保护机制。我们在各种场景下都会接触到熔断这两个字。高压电路中，如果某个地方的电压过高，熔断器就会熔断，对电路进行保护。股票交易中，如果股票指数过高，也会采用熔断机制，暂停股票的交易。同样，在微服务架构中，熔断机制也是起着类似的作用。当扇出链路的某个微服务不可用或者响应时间太长时，熔断该节点微服务的调用，进行服务的降级，快速返回错误的响应信息。当检测到该节点微服务调用响应正常后，恢复调用链路。

**注意：**

* 服务熔断重点在**“断”**，切断对下游服务的调用
* 服务熔断和服务降级往往是一起使用的，Hystrix就是这样。

**服务降级**

通俗讲就是整体资源不够用了，先将一些不关紧的服务停掉（调用我的时候，给你返回一个预留的值，也叫做**兜底数据**），待渡过难关高峰过去，再把那些服务打开。

服务降级一般是从整体考虑，就是当某个服务熔断之后，服务器将不再被调用，此刻客户端可以自己准备一个本地的fallback回调，返回一个缺省值，这样做，虽然服务水平下降，但好歹可用，比直接挂掉要强。

**服务限流** 服务降级是当服务出问题或者影响到核心流程的性能时，暂时将服务屏蔽掉，待高峰或者问题解决后再打开；但是有些场景并不能用服务降级来解决，比如秒杀业务这样的核心功能，这个时候可以结合服务限流来限制这些场景的并发/请求量

限流措施也很多，比如

* 限制总并发数（比如数据库连接池、线程池）
* 限制瞬时并发数（如nginx限制瞬时并发连接数）
* 限制时间窗口内的平均速率（如Guava的RateLimiter、nginx的limit_req模块，限制每秒的平均速率）
* 限制远程接口调用速率、限制MQ的消费速率等

#### 4.3.3 Hystrix简介

**[来自官网]**Hystrix(豪猪)，宣言“defend your application”是由Netflflix开源的一个延迟和容错库，用于隔离访问远程系统、服务或者第三方库，防止级联失败，从而提升系统的可用性与容错性。Hystrix主要通过以下几点实现延迟和容错。

* 包裹请求：使用HystrixCommand包裹对依赖的调用逻辑。 页面静态化微服务方（@HystrixCommand 添加Hystrix控制）
* 跳闸机制：当某服务的错误率超过一定的阈值时，Hystrix可以跳闸，停止请求该服务一段时间。
* 资源隔离：Hystrix为每个依赖都维护了一个小型的线程池(舱壁模式)。如果该线程池已满， 发往该依赖的请求就被立即拒绝，而不是排队等待，从而加速失败判定。
* 监控：Hystrix可以近乎实时地监控运行指标和配置的变化，例如成功、失败、超时、以及被拒绝的请求等。
* 回退机制：当请求失败、超时、被拒绝，或当断路器打开时，执行回退逻辑。回退逻辑由开发人员自行提供，例如返回一个缺省值。
* 自我修复：断路器打开一段时间后，会自动进入“半开”状态（探测服务是否可用，如还是不可用，再次退回打开状态）。

#### 4.3.4  Hystrix应用

##### （1）熔断处理

**目的：**商品微服务长时间没有响应，服务消费者—>**页面静态化微服务**快速失败给用户提示

* **引入依赖：** 服务消费者工程（静态化微服务）中引入Hystrix依赖坐标（也可以添加在父工程中）

  ```xml
  <!--熔断器Hystrix--> 
  <dependency> 
      <groupId>org.springframework.cloud</groupId> 
      <artifactId>spring-cloud-starter-netflix-hystrix</artifactId> 
  </dependency>
  ```

* **开启熔断：**服务消费者工程（静态化微服务）的启动类中添加熔断器开启注解

  ```java
  /**
   * 注解简化写法 
   * @SpringCloudApplication=@SpringBootApplication+@EnableDiscoveryClient+@EnableCircuitBreaker 
   */
  @SpringBootApplication 
  @EnableDiscoveryClient //@EnableEurekaClient 
  @EnableCircuitBreaker // 开启熔断 
  public class PageApplication { 
      public static void main(String[] args) { 
          SpringApplication.run(PageApplication.class,args); }
      @Bean 
      @LoadBalanced//Ribbon负载均衡 
      public RestTemplate restTemplate(){ 
          return new RestTemplate(); 
      } 
  }
  ```

* **定义服务降级的处理方式：**业务方法上使用@HystrixCommand的fallbackMethod属性关联到服务降级处理方法

  ```java
  /**
   * 提供者模拟处理超时，调用方法添加Hystrix控制 
   */ 
  // 使用@HystrixCommand注解进行熔断控制 
  @HystrixCommand( 
      // 线程池标识，要保持唯一，不唯一的话就共用了 
      threadPoolKey = "getProductServerPort2", 
      // 线程池细节属性配置 
      threadPoolProperties = { 
          @HystrixProperty(name="coreSize",value = "1"), // 线程数 
          @HystrixProperty(name="maxQueueSize",value="20") // 等待队列长 度 
      },
      // commandProperties熔断的一些细节属性配置 
      commandProperties = { 
          // 每一个属性都是一个HystrixProperty
          @HystrixProperty(name="execution.isolation.thread.timeoutInMilliseconds",value= "2000") 
      } 
  )
  @RequestMapping("/getPort2") 
  public String getProductServerPort2(){ 
      String url = "http://service-product/product/server"; 
      return restTemplate.getForObject(url,String.class); 
  }
  ```

* **商品微服务模拟超时操作**

  ```java
  @RestController 
  @RequestMapping("/product") 
  public class ProductController { 
      @Value("${server.port}")
      private String serverPort; 
      @RequestMapping("/server") 
      public String findServerPort(){ 
          try {
              Thread.sleep(10000); 
          } catch (InterruptedException e) { 
              e.printStackTrace(); 
          }
          return serverPort; 
      } 
  }
  ```

  ![image-20220310164852493](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310164852493.png)

  对于没有配置HystrixCommand的方法getPort,经过10秒仍然可以最终获取到结果。方法getPort2设置熔断配置的是2秒响应时间超过直接熔断

##### （2）降级处理

配置@HystrixCommand注解，定义降级处理方法

```java
@HystrixCommand( 
    // 线程池标识，要保持唯一，不唯一的话就共用了 
    threadPoolKey = "getProductServerPort3TimeoutFallback", 
    // 线程池细节属性配置 
    threadPoolProperties = { 
        @HystrixProperty(name = "coreSize", value = "2"), // 线程数 
        @HystrixProperty(name = "maxQueueSize", value = "20") // 等待 队列长度 
    },
    // commandProperties熔断的一些细节属性配置 
    commandProperties = { 
        // 每一个属性都是一个HystrixProperty 
        @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "2000"), 
        // hystrix高级配置，定制工作过程细节 
        // 统计时间窗口定义 
        @HystrixProperty(name = "metrics.rollingStats.timeInMilliseconds", value = "8000"), 
        // 统计时间窗口内的最小请求数 
        @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "2"), 
        // 统计时间窗口内的错误数量百分比阈值 
        @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "50"), 
        // 自我修复时的活动窗口长度 
        @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "3000") 
    },
    fallbackMethod = "myFallBack" // 回退方法 
)
@RequestMapping("/getPort3") 
public String getProductServerPort3() { 
    String url = "http://service-product/product/server"; 
    return restTemplate.getForObject(url, String.class); 
}
/**
 * 定义回退方法，返回预设默认值 
 * 注意：该方法形参和返回值与原始方法保持一致 
 */ 
public String myFallBack() { 
    return "这个结果是调用超时进行熔断了哦"; // 兜底数据 
}
```

![image-20220310165524453](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310165524453.png)



#### 4.3.5 Hystrix舱壁模式

即线程池隔离策略

如果不进行任何设置，所有熔断方法使用同一个Hystrix线程池（10个线程），那么这样的话会导致问题，这个问题并不是扇出链路微服务不可用导致的，而是我们的线程机制导致的，如果方法A的请求把10个线程都用了，方法2请求处理的时候压根都没法去访问B，因为没有线程可用，并不是B服务不可用。

![image-20220310114120082](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310114120082.png)

![image-20220310114127550](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310114127550.png)

为了避免问题服务请求过多导致正常服务无法访问，Hystrix 不是采用增加线程数，而是单独的为每一个控制方法创建一个线程池的方式，这种模式叫做“舱壁模式"，也是线程隔离的手段

#### 4.3.6 Hystrix工作流程与高级应用

  ![image-20220310114144982](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310114144982.png)

* 当调用出现问题时，开启一个时间窗（10s）
* 在这个时间窗内，统计调用次数是否达到最小请求数？
  * 如果没有达到，则重置统计信息，回到第1步
  * 如果达到了，则统计失败的请求数占所有请求数的百分比，是否达到阈值？
    * 如果达到，则跳闸（不再请求对应服务）
    * 如果没有达到，则重置统计信息，回到第1步 
* 如果跳闸，则会开启一个活动窗口（默认5s），每隔5s，Hystrix会让一个请求通过,到达那个问题服务，看是否调用成功，如果成功，重置断路器回到第1步，如果失败，回到第3步 

```java
/** 
 * 8秒钟内，请求次数达到2个，并且失败率在50%以上，就跳闸 
 * 跳闸后活动窗口设置为3s 
 */ 
@HystrixCommand( 
    commandProperties = { 
        //统计窗口时间的设置 
        @HystrixProperty(name = "metrics.rollingStats.timeInMilliseconds",value = "8000"), 
        //统计窗口内的最小请求数 
        @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "2"), 
        //统计窗口内错误请求阈值的设置 50% 
        @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "50"), 
        //自我修复的活动窗口时间 
        @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "3000") 
    } 
)
```

我们上述通过注解进行的配置也可以配置在配置文件中:

```yaml
# 配置熔断策略： 
hystrix: 
    command: 
        default: 
            circuitBreaker: 
                # 强制打开熔断器，如果该属性设置为true，强制断路器进入打开状态，将会拒绝所有的请求。 默认false关闭的 
                forceOpen: false # 触发熔断错误比例阈值，默认值50% 
                errorThresholdPercentage: 50 # 熔断后休眠时长，默认值5秒 
                sleepWindowInMilliseconds: 3000 # 熔断触发最小请求次数，默认值是20 
                requestVolumeThreshold: 2 
            execution: 
                isolation: 
                    thread: # 熔断超时设置，默认为1秒 timeoutInMilliseconds: 2000
```

基于springboot的健康检查观察跳闸状态（自动投递微服务暴露健康检查细节）

```yaml
# springboot中暴露健康检查等断点接口 
management: 
    endpoints: 
        web:
            exposure: include: "*" 
    # 暴露健康接口的细节 
    endpoint: 
        health: 
            show-details: always
```

访问健康检查接口：http://localhost:9100/actuator/health



### 4.4 Feign远程调用组件

在之前的案例中，服务消费者调用服务提供者的时候使用RestTemplate技术。

![image-20220310171310749](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220310171310749.png)

#### 4.4.1 Feign简介

Feign是**Netﬂix开发的一个轻量级RESTful的HTTP服务客户端（用它来发起请求，远程调用的）**，是以Java接口注解的方式调用Http请求，而不用像Java中通过封装HTTP请求报文的方式直接调用，Feign被广泛应用在Spring Cloud 的解决方案中。

类似于Dubbo，服务消费者拿到服务提供者的接口，然后像调用本地接口方法一样去调用，实际发出的是远程的请求。

* Feign可帮助我们更加便捷，优雅的调用HTTP API：不需要我们去拼接url然后呢调用restTemplate的api，在SpringCloud中，使用Feign非常简单，创建一个接口（在消费者--服务调用方这一端），并在接口上添加一些注解，代码就完成了
* SpringCloud对Feign进行了增强，使Feign支持了SpringMVC注解（OpenFeign）

**本质：封装了Http调用流程，更符合面向接口化的编程习惯，类似于Dubbo的服务调用**



#### 4.4.2 Feign配置应用

在服务调用者工程（消费）创建接口（添加注解）

**Feign = RestTemplate+Ribbon+Hystrix**

* 服务消费者工程（页面静态化微服务）中引入Feign依赖（或者父类工程）

  ```xml
  <dependency> 
      <groupId>org.springframework.cloud</groupId> 
      <artifactId>spring-cloud-starter-openfeign</artifactId> 
  </dependency>
  ```

* 服务消费者工程（静态化微服务）启动类使用注解@EnableFeignClients添加Feign支持

  ```java
  @SpringBootApplication 
  @EnableDiscoveryClient // 开启服务发现 
  @EnableFeignClients // 开启Feign 
  public class PageApplication { 
      public static void main(String[] args) { 
          SpringApplication.run(PageApplication.class,args); 
      } 
  }
  ```

  **注意：**此时去掉Hystrix熔断的支持注解@EnableCircuitBreaker即可包括引入的依赖，因为Feign会自动引入

* 在消费者微服务中创建Feign接口

  ```java
  /**
   * @author hao
   * @date 2022/3/10 17:29
   */
  @FeignClient(name = "service-product")
  public interface ProductFeign {
      /**
       * 通过id获取商品信息
       * @param id
       * @return
       */
      @RequestMapping("/product/query/{id}")
      public Product getProduct(@PathVariable Integer id);
      /**
       * 获得端口号
       * @return
       */
      @RequestMapping("/product/server")
      public String getPort();
  }
  ```

  **注意：**

  * @FeignClient注解的name属性用于指定要调用的服务提供者名称，和服务提供者yml文件中spring.application.name保持一致

  * 接口中的接口方法，就好比是远程服务提供者Controller中的Hander方法（只不过如同本地调用了），那么在进行参数绑定的时，可以使用@PathVariable、@RequestParam、@RequestHeader等，这也是OpenFeign对SpringMVC注解的支持，但是需要注意value必须设置，否则会抛出异常

  * @FeignClient(name = "service-product")，name在消费者微服务中只能出现一次。（升级Spring Boot 2.1.0 Spring Cloud Greenwich.M1 版本后，在2个Feign接口类内定义相同的名字， @FeignClient(name = 相同的名字 就会出现报错，在之前的版本不会提示报错），所以最好**将调用一个微服务的信息都定义在一个Feign接口中**。

* 改造PageController中原有的调用方式

  ```java
  @RestController 
  @RequestMapping("/page") 
  public class PageController {
      @Autowired 
      private ProductFeign productFeign;
      @RequestMapping("/getProduct2/{id}")
      public Product getProduct2(@PathVariable Integer id) {
          return productFeign.getProduct(id);
      }
      @RequestMapping("/getPort4")
      public String getPort() {
          return productFeign.getPort();
      }
  }
  ```

  这里之前提供方的getPort做了10秒睡眠，这里的getPort4就直接失败了。说明使用feign在默认情况有超时的配置



#### 4.4.3 Feign对负载均衡的支持

Feign 本身已经集成了Ribbon依赖和自动配置，因此我们不需要额外引入依赖，可以通过ribbon.xx 来进 行全局配置,也可以通过服务名.ribbon.xx 来对指定服务进行细节配置配置（参考之前，此处略）

Feign默认的请求处理超时时长1s，有时候我们的业务确实执行的需要一定时间，那么这个时候，我们就需要调整请求处理超时时长，Feign自己有超时设置，如果配置Ribbon的超时，则会以Ribbon的为准

```yaml
#针对的被调用方微服务名称,不加就是全局生效 
service-product: 
    ribbon: 
        #请求连接超时时间 
        #ConnectTimeout: 2000 
        #请求处理超时时间 #ReadTimeout: 5000 
        #对所有操作都进行重试 
        OkToRetryOnAllOperations: true 
        ####根据如上配置，当访问到故障请求的时候，它会再尝试访问一次当前实例（次数由 MaxAutoRetries配置）， 
        ####如果不行，就换一个实例进行访问，如果还不行，再换一次实例访问（更换次数由 MaxAutoRetriesNextServer配置）， 
        ####如果依然不行，返回失败信息。
        MaxAutoRetries: 0 #对当前选中实例重试次数，不包括第一次调用 
        MaxAutoRetriesNextServer: 0 #切换实例的重试次数 
        NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RoundRobinRule #负载策略 调整
```



#### 4.4.4 Feign对熔断器的支持

在Feign客户端工程配置文件（application.yml）中开启Feign对熔断器的支持

```yaml
# 开启Feign的熔断功能 
feign: 
    hystrix: 
        enabled: true
```

Feign的超时时长设置那其实就上面Ribbon的超时时长设置

Hystrix超时设置（就按照之前Hystrix设置的方式就OK了）

**注意**

* 开启Hystrix之后，Feign中的方法都会被进行一个管理了，一旦出现问题就进入对应的回退逻辑处理
* 针对超时这一点，当前有两个超时时间设置（Feign（ribbon）/hystrix），**熔断的时候是根据这两个时间的最小值来进行的**，即处理时长超过最短的那个超时时间了就熔断进入回退降级逻辑

```yaml
hystrix: 
    command: 
        default: 
            circuitBreaker: 
                # 强制打开熔断器，如果该属性设置为true，强制断路器进入打开状态，将会拒绝所有的请求。 默认false关闭的 
                forceOpen: false 
                # 触发熔断错误比例阈值，默认值50% 
                errorThresholdPercentage: 50 
                # 熔断后休眠时长，默认值5秒 
                sleepWindowInMilliseconds: 3000 
                # 熔断触发最小请求次数，默认值是20 
                requestVolumeThreshold: 2 
            execution: 
               isolation: 
                   thread: 
                       # 熔断超时设置，默认为1秒 
                       timeoutInMilliseconds: 5000
```

自定义FallBack处理类（需要实现FeignClient接口）

```java
/**
 * @author hao
 * @date 2022/3/10 18:26
 */
@Component
public class ProductFeignFallBack implements ProductFeign{
    @Override
    public Product getProduct(Integer id) {
        return null;
    }

    @Override
    public String getPort() {
        return "-1";
    }
}
```

```java
/**
 * @author hao
 * @date 2022/3/10 17:29
 */
@FeignClient(name = "service-product",fallback = ProductFeignFallBack.class)
public interface ProductFeign {
    /**
     * 通过id获取商品信息
     * @param id
     * @return
     */
    @RequestMapping("/product/query/{id}")
    public Product getProduct(@PathVariable Integer id);
    /**
     * 获得端口号
     * @return
     */
    @RequestMapping("/product/server")
    public String getPort();
}
```

#### 4.4.5 Feign对请求压缩和响应压缩的支持

Feign 支持对请求和响应进行GZIP压缩，以减少通信过程中的性能损耗。通过下面的参数 即可开启请求与响应的压缩功能：

```yaml
feign: 
    hystrix: 
        enabled: true 
    #开启请求和响应压缩 
    compression: 
        request: 
            enabled: true #默认不开启 
            mime-types: text/html,application/xml,application/json # 设置压缩的数据类型， 此处也是默认值 
            min-request-size: 2048 # 设置触发压缩的大小下限，此处也是默认值 
        response: 
            enabled: true #默认不开启
```



### 4.5 GateWay网关组件

网关：微服务架构中的重要组成部分

局域网中就有网关这个概念，局域网接收或者发送数据出去通过这个网关，比如用Vmware虚拟机软件搭建虚拟机集群的时候，往往我们需要选择IP段中的一个IP作为网关地址。

我们学习的GateWay-->Spring Cloud GateWay（它只是众多网关解决方案中的一种）

#### 4.5.1 GateWay简介

Spring Cloud GateWay是Spring Cloud的一个全新项目，目标是取代Netflix Zuul，它基于Spring5.0+SpringBoot2.0+WebFlux（基于高性能的Reactor模式响应式通信框架Netty，异步非阻塞模型）等技术开发，性能高于Zuul，官方测试，GateWay是Zuul的1.6倍，旨在为微服务架构提供一种简单有效的统一的API路由管理方式。

Spring Cloud GateWay不仅提供统一的路由方式（反向代理）并且基于 Filter(定义过滤器对请求过滤，完成一些功能) 链的方式提供了网关基本的功能，例如：鉴权、流量控制、熔断、路径重写、日志监控等。

**网关在架构中的位置**

![image-20220311093950944](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311093950944.png)

#### 4.5.2 GateWay核心概念

Spring Cloud GateWay天生就是异步非阻塞的，基于Reactor模型（同步非阻塞的I/O多路复用机制）

一个请求—>网关根据一定的条件匹配—匹配成功之后可以将请求转发到指定的服务地址；而在这个过程中，我们可以进行一些比较具体的控制（限流、日志、黑白名单）

* **路由（route）：** 网关最基础的部分，也是网关比较基础的工作单元。路由由一个ID、一个目标URL（最终路由到的地址）、一系列的断言（匹配条件判断）和Filter过滤器（精细化控制）组成。如果断言为true，则匹配该路由。
* **断言（predicates）：**参考了Java8中的断言java.util.function.Predicate，开发人员可以匹配Http请求中的所有内容（包括请求头、请求参数等）（类似于nginx中的location匹配一样），如果断言与请求相匹配则路由。
* **过滤器（filter）：**一个标准的Spring webFilter，使用过滤器，可以在请求之前或者之后执行业务逻辑。

#### 4.5.3 GateWay如何工作

![image-20220311094158895](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311094158895.png)

Spring 官方介绍：

客户端向Spring Cloud GateWay发出请求，然后在GateWay Handler Mapping中找到与请求相匹配的路由，将其发送到GateWay Web Handler；Handler再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（pre）或者之后（post）执行业务逻辑。

Filter在“pre”类型过滤器中可以做参数校验、权限校验、流量监控、日志输出、协议转换等，在“post”类型的过滤器中可以做响应内容、响应头的修改、日志的输出、流量监控等。

#### 4.5.4 GateWay应用

使用网关对静态化微服务进行代理（添加在它的上游，相当于隐藏了具体微服务的信息，对外暴露的是网关）

* **创建工程cloud-gateway导入依赖**

  GateWay不需要使用web模块，它引入的是WebFlux（类似于SpringMVC）。注意依赖冲突，不能有web模块，可能继承的pom工程携带了。

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <parent>
          <artifactId>mycloud</artifactId>
          <groupId>com.hao</groupId>
          <version>1.0</version>
      </parent>
      <modelVersion>4.0.0</modelVersion>
  
      <artifactId>service-page</artifactId>
  
      <properties>
          <maven.compiler.source>11</maven.compiler.source>
          <maven.compiler.target>11</maven.compiler.target>
      </properties>
  
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
          <dependency>
              <groupId>com.hao</groupId>
              <artifactId>service-common</artifactId>
              <version>1.0</version>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
          </dependency>
          <!--熔断器Hystrix-->
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
          </dependency>
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-openfeign</artifactId>
          </dependency>
      </dependencies>
  
  </project>
  ```

* **application.yml配置**

  ```yaml
  server:
      port: 9300
  eureka:
      client:
          serviceUrl:
              # eureka server的路径
              defaultZone: http://eurekaA:9200/eureka,http://eurekaB:9201/eureka
      instance:
          prefer-ip-address: true
          instance-id: ${spring.cloud.client.ip- address}:${spring.application.name}:${server.port}:@project.version@
  
  spring:
      application:
          name: cloud-gateway #网关的配置
      cloud:
          gateway:
              routes: #配置路由
                  - id: service-page-router
                    uri: http://127.0.0.1:9100
                    predicates: #当断言成功后，交给某一个微服务处理时使用的是转发
                      - Path=/page/**
                  - id: service-product-router
                    uri: http://127.0.0.1:9000
                    predicates:
                      - Path=/product/**
                    filters:
                      # http://127.0.0.1:9300/product/service/port --> /service/port --> 商品微服务
                      - StripPrefix=1
                      # 去掉uri中的第一部分，所以就要求我们通过网关访问的时候，把uri 的第一部分设置为product，从uri的第二部分开始才是真正的uri
  ```

* **启动类**

  ```java
  /**
   * @author hao
   * @date 2022/3/11 10:05
   */
  @SpringBootApplication
  @EnableDiscoveryClient
  public class GatewayApplication {
      public static void main(String[] args) {
          SpringApplication.run(GatewayApplication.class,args);
      }
  }
  ```

* **测试**

  ![image-20220311105534882](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311105534882.png)

  ![image-20220311105705744](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311105705744.png)

  

#### 4.5.5 GateWay路由规则详解

Spring Cloud GateWay 帮我们内置了很多 Predicates功能，实现了各种路由匹配规则（通过Header、请求参数等作为条件）匹配到对应的路由。

![image-20220311105726491](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311105726491.png)

**时间点后匹配**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: after_route 
                  uri: https://example.org 
                  predicates: 
                      - After=2017-01-20T17:42:47.789-07:00[America/Denver]
```

**时间点前匹配**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: before_route 
                  uri: https://example.org 
                  predicates: 
                      - Before=2017-01-20T17:42:47.789-07:00[America/Denver]
```

**时间区间匹配**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: between_route 
                  uri: https://example.org 
                  predicates: 
                      - Between=2017-01-20T17:42:47.789-07:00[America/Denver],2017-01- 21T17:42:47.789-07:00[America/Denver]
```

**指定Cookie正则匹配指定值**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: cookie_route 
                  uri: https://example.org 
                  predicates: 
                      - Cookie=chocolate,ch.p
```

**指定Header正则匹配指定值**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: header_route 
                  uri: https://example.org 
                  predicates: 
                      - Header=X-Request-Id, \d+
```

**请求Host匹配指定值**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: host_route 
                  uri: https://example.org 
                  predicates: 
                      - Host=**.somehost.org,**.anotherhost.org
```

**请求Method匹配指定请求方式**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: method_route 
                  uri: https://example.org 
                  predicates: 
                      - Method=GET,POST
```

**请求路径正则匹配**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: path_route 
                  uri: https://example.org 
                  predicates: 
                      - Path=/red/{segment},/blue/{segment}
```

**请求包含某参数**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: query_route 
                  uri: https://example.org 
                  predicates: 
                      - Query=green
```

**请求包含某参数并且参数值匹配正则表达式**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: query_route 
                  uri: https://example.org 
                  predicates: 
                      - Query=red,gree.
```

**远程地址匹配**

```yaml
spring: 
    cloud: 
        gateway: 
            routes: 
                - id: remoteaddr_route 
                  uri: https://example.org 
                  predicates: 
                      - RemoteAddr=192.168.1.1/24
```



#### 4.5.6 GateWay动态路由详解

GateWay支持自动从注册中心中获取服务列表并访问，即所谓的动态路由

实现步骤如下

* pom.xml中添加注册中心客户端依赖（因为要获取注册中心服务列表，eureka客户端已经引入）

* 动态路由配置

  ![image-20220311111402630](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311111402630.png)

**注意：动态路由设置时，uri以lb: //开头（lb代表从注册中心获取服务），后面是需要转发到的服务名称**

  

#### 4.5.7 GateWay过滤器

##### （1）GateWay过滤器简介

从过滤器生命周期（影响时机点）的角度来说，主要有两个pre和post：

![image-20220311111542911](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311111542911.png)

从过滤器类型的角度，Spring Cloud GateWay的过滤器分为GateWayFilter和GlobalFilter两种

![image-20220311111604835](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311111604835.png)

如Gateway Filter可以去掉url中的占位后转发路由，比如

```yaml
predicates:
    - id: xxx
      Path=/product/** 
      filters: 
          - StripPrefix=1 # 可以去掉product之后转发
```

**注意：GlobalFilter全局过滤器是程序员使用比较多的过滤器，我们主要讲解这种类型**

##### （2）自定义全局过滤器实现IP访问限制（黑白名单）

请求过来时，判断发送请求的客户端的ip，如果在黑名单中，拒绝访问

自定义GateWay全局过滤器时，我们实现Global Filter接口即可，通过全局过滤器可以实现黑白名单、限流等功能。

```java
/**
 * @author hao
 * @date 2022/3/11 11:22
 */
@Slf4j
@Component
public class BlackListFilter  implements GlobalFilter, Ordered {
    /**
     * 模拟黑名单（实际可以去数据库或者redis中查询）
     */
    private static List<String> blackList = new ArrayList<>();
    static {
        /*
         * 模拟本机地址
         */
        blackList.add("0:0:0:0:0:0:0:1");
        blackList.add("127.0.0.1");
    }
    /**
     * 过滤器核心方法
     * @param exchange 封装了request和response对象的上下文
     * @param chain 网关过滤器链（包含全局过滤器和单路由过滤器）
     * @return
     */
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        System.out.println("....BlackListFilter....");
        // 思路：获取客户端ip，判断是否在黑名单中，在的话就拒绝访问，不在的话就放行
        // 从上下文中取出request和response对象
        ServerHttpRequest request = exchange.getRequest();
        ServerHttpResponse response = exchange.getResponse();
        response.getHeaders().add("Content-Type","application/json;chartset=UTF-8");
        // 从request对象中获取客户端ip
        String clientIp = request.getRemoteAddress().getHostString();
        // 拿着clientIp去黑名单中查询，存在的话就决绝访问
        if(blackList.contains(clientIp)) {
            // 决绝访问，直接return
            // 状态码
            response.setStatusCode(HttpStatus.UNAUTHORIZED);
            log.info("=====>IP:" + clientIp + " 在黑名单中，将被拒绝访问！");
            byte[] msg = "Request be denied! 你被拉黑啦".getBytes();
            DataBuffer wrap  = response.bufferFactory().wrap(msg);
            return response.writeWith(Mono.just(wrap));
        }
        // 合法请求，放行，执行后续的过滤器
        return chain.filter(exchange);
    }
    /**
     * 返回值表示当前过滤器的顺序(优先级)，数值越小，优先级越高
     * @return
     */
    @Override
    public int getOrder() {
        return 0;
    }
}
```

![image-20220311123502637](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311123502637.png)

#### 4.5.8 GateWay高可用

网关作为非常核心的一个部件，如果挂掉，那么所有请求都可能无法路由处理，因此我们需要做GateWay的高可用。

**GateWay**的高可用很简单：可以启动多个GateWay实例来实现高可用，在GateWay的上游使用Nginx等负载均衡设备进行负载转发以达到高可用的目的。

启动多个GateWay实例（假如说两个，一个端口9300，一个端口9301），剩下的就是使用Nginx等完成负载代理即可。示例如下：

```nginx
#配置多个GateWay实例 
upstream gateway { 
    server 127.0.0.1:9301; 
    server 127.0.0.1:9302; 
}
location / { 
    proxy_pass http://gateway; 
}
```



### 4.6 Spring Cloud Config分布式配置中心

#### 4.6.1 分布式配置中心应用场景

往往，我们使用配置文件管理一些配置信息，比如application.yml

**单例应用架构：**配置信息的管理、维护并不会显得特别麻烦，手动操作就可以，因为就一个工程；

**微服务架构，**因为我们的分布式集群环境中可能有很多个微服务，我们不可能一个一个去修改配置然后重启生效，在一定场景下我们还需要在运行期间动态调整配置信息，比如：根据各个微服务的负载情况，动态调整数据源连接池大小，我们希望配置内容发生变化的时候，微服务可以自动更新。

场景总结如下：

* 集中配置管理，一个微服务架构中可能有成百上千个微服务，所以集中配置管理是很重要的（一次修改、到处生效）
* 不同环境不同配置，比如数据源配置在不同环境（开发dev,测试test,生产prod）中是不同的
* 运行期间可动态调整。例如，可根据各个微服务的负载情况，动态调整数据源连接池大小等配置修改后可自动更新
* 如配置内容发生变化，微服务可以自动更新配置

那么，我们就需要对配置文件进行**集中式管理**，这也是分布式配置中心的作用。

#### 4.6.2 Spring Cloud Config

##### （1）Config简介

Spring Cloud Confifig是一个分布式配置管理方案，包含了 Server端和 Client端两个部分。

![image-20220311124155605](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311124155605.png)

* Server 端：提供配置文件的存储、以接口的形式将配置文件的内容提供出去，通过使用@EnableConfifigServer注解在 Spring boot 应用中非常简单的嵌入
* Client 端：通过接口获取配置数据并初始化自己的应用

##### （2）Config分布式配置应用

**说明：Config Server是集中式的配置服务，用于集中管理应用程序各个环境下的配置。 默认使用Git存储配置文件内容，也可以SVN。**

比如，我们要对“静态化微服务或者商品微服务”的application.yml进行管理（区分开发环境（dev）、测试环境(test)、生产环境(prod)）

* **登录GitHub，创建cloud-config**

* **上传yml配置文件**，命名规则如下：

  * {application}-{profile}.yml 或者 {application}-{profile}.properties
  * 其中，application为应用名称，profile指的是环境（用于区分开发环境，测试环境、生产环境等）
  * 示例：service-page-dev.yml、service-page-test.yml、service-page-prod.yml

* **构建Config Server统一配置中心**

  * **新建SpringBoot工程，引入依赖坐标**（需要注册自己到Eureka）

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <parent>
            <artifactId>mycloud</artifactId>
            <groupId>com.hao</groupId>
            <version>1.0</version>
        </parent>
        <modelVersion>4.0.0</modelVersion>
    
        <artifactId>cloud-config</artifactId>
    
        <properties>
            <maven.compiler.source>11</maven.compiler.source>
            <maven.compiler.target>11</maven.compiler.target>
        </properties>
    
        <dependencies>
            <!--eureka client 客户端依赖引入-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
            </dependency>
            <!--config配置中心服务端-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-config-server</artifactId>
            </dependency>
        </dependencies>
    
    </project>
    ```

  * **配置启动类，使用注解@EnableConfigServer开启配置中心服务器功能**

    ```java
    @SpringBootApplication
    @EnableDiscoveryClient
    @EnableConfigServer
    public class ConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(ConfigApplication.class,args);
        }
    }
    ```

  * **application.yml**

    ```yaml
    server:
        port: 9400
    #注册到Eureka服务中心
    eureka:
        client:
            service-url:
                defaultZone: http://eurekaA:9200/eureka,http://eurekaB:9201/eureka
        instance:
            prefer-ip-address: true
            instance-id: ${spring.cloud.client.ip- address}:${spring.application.name}:${server.port}:@project.version@
    
    spring:
        application:
            name: cloud-config
        cloud:
            config:
                server:
                    git:
                        uri: https://github.com/Jasper-zh/cloud-config.git #配置git服务地址
                        username: jasper-zh #配置git用户名
                        password: xxxxx #配置git密码
                        search-paths:
                          - cloud-config
                        default-label: main
    
    
    ```

  * **测试**（随便提交了个文件）

    http://127.0.0.1:9400/main/xx.txt

    main：分支名称

    xx.txt：文件名称

    ![image-20220311140632856](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311140632856.png)

* **构建Client客户端**（在已有的静态化微服务基础上）

  案例实现：在service-page微服务中动态获取config server的配置信息

  **已有工程中添加依赖坐标**

  ```xml
  <dependency> 
      <groupId>org.springframework.cloud</groupId> 
      <artifactId>spring-cloud-config-client</artifactId> 
  </dependency>
  ```

  **application.yml修改为bootstrap.yml配置文件**

  bootstrap.yml是系统级别的，优先级比application.yml高，应用启动时会检查这个配置文件，在这个配置文件中指定配置中心的服务地址，会自动拉取配置中心配置并且启用。

  （主要是把与统一配置中心连接的信息放到bootstrap.yml）

  它会读取spring.application.name与spring.cloud.config.profile拼接命名的配置，因此往github推送了service-page-dev.yml配置，且添加了两条测试配置person.name、person.age

  ![image-20220311151335374](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311151335374.png)

  **bootstrap.yml**（部分）

  ```yaml
  spring:
    application:
      name: service-page
    datasource:
  	...
    cloud:
      config: #config客户端配置,和ConfigServer通信，并告知ConfigServer希望获取的配置信息在哪个文 件中
          profile: dev #后缀名称
          label: main #分支名称
          uri: http://localhost:9400
  ```

  **controller测试**

  ```java
  @RestController
  @RequestMapping("/page")
  public class PageController {
  
      @Value("${person.name}")
      String name;
      @Value("${person.age}")
      String age;
  
      @RequestMapping("/config")
      public String getConfig(){
          return name + age;
      }
  }
  ```

  ![image-20220311151311324](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311151311324.png)

  

#### 4.6.3 Config配置手动刷新

不用重启微服务，只需要手动的做一些其他的操作（访问一个地址/refresh）刷新，之后再访问即可

此时，客户端取到了配置中心的值，但当我们修改GitHub上面的值时，服务端（Config Server）能实时获取最新的值，但客户端（Config Client）读的是缓存，无法实时获取最新值。Spring Cloud已 经为我们解决了这个问题，那就是客户端使用post去触发refresh，获取最新数据。

* Client客户端添加依赖springboot-starter-actuator（已添加）

* Client客户端bootstrap.yml中添加配置（暴露通信端点）

  ```yaml
  management: 
      endpoints: 
          web:
              exposure: 
                  include: refresh
                      
  #也可以暴露所有的接口
  management: 
      endpoints: 
          web:
              exposure: 
                  include: "*"
  ```

* Client客户端使用到配置信息的类上添加@RefreshScope

  ```java
  @RestController
  @RequestMapping("/page")
  @RefreshScope //手动刷新
  public class PageController {
  
      @Value("${person.name}")
      String name;
      @Value("${person.age}")
      String age;
  
      @RequestMapping("/config")
      public String getConfig(){
          return name + age;
      }
  }
  ```

* 手动向Client客户端发起POST请求，http://localhost:9100/actuator/refresh，刷新配置信息

  ![image-20220311154415231](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311154415231.png)

  ![image-20220311154435590](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311154435590.png)

  

**注意：手动刷新方式避免了服务重启**

思考：可否使用广播机制，一次通知，处处生效，方便大范围配置自动刷新？

#### 4.6.4 Config配置自动更新

实现一次通知，处处生效

在微服务架构中，我们可以结合消息总线（Bus）实现分布式配置的自动更新（Spring CloudConfig + Spring Cloud Bus）

##### （1）消息总线Bus

所谓消息总线Bus，即我们经常会使用MQ消息代理构建一个共用的Topic，通过这个Topic连接各个微服务实例，MQ广播的消息会被所有在注册中心的微服务实例监听和消费。**换言之就是通过一个主题连接各个微服务，打通脉络**。

Spring Cloud Bus（基于MQ的，支持RabbitMq/Kafka） 是Spring Cloud中的消息总线方案，Spring Cloud Config + Spring Cloud Bus 结合可以实现配置信息的自动更新。

![image-20220311154722103](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311154722103.png)

##### （2）Config + Bus实现自动刷新

MQ消息代理，我们还选择使用RabbitMQ，ConfigServer（配置中心）和ConfigClient（其他微服务）都添加都消息总线的支持以及与RabbitMq的连接信息

* cloud-config和其他微服务（client）添加消息总线支持

  ```xml
  <dependency> 
      <groupId>org.springframework.cloud</groupId> 
      <artifactId>spring-cloud-starter-bus-amqp</artifactId> 
  </dependency>
  ```

* cloud-config和客户端添加配置

  ```yaml
  spring: 
      rabbitmq: 
          host: 192.169.200.128 
          port: 5672 
          username: guest 
          password: guest
  ```

* cloud-config微服务暴露接口

  ```yaml
  management: 
      endpoints: 
          web:
              exposure: 
                  include: refresh
  ```

* 重启各个服务，更改配置之后，向配置中心服务端发送post请求，各个客户端配置即可自动刷新

  http://127.0.0.1:9400/actuator/bus-refresh

* Config Client测试

  在广播模式下实现了一次请求，处处更新，如果我只想定向更新呢？

  在发起刷新请求的时候http://localhost:9004/actuator/bus-refresh/service-page:9100

  即为最后面跟上要定向刷新的实例的 **服务名:端口号**即可

​		

## 五、第二代SpringCloud核心组件（SCA）

### 5.1 第二代介绍

SpringCloud 是若干个框架的集合，包括 spring-cloud-config、spring-cloud-bus 等近 20 个子项目，提供了服务治理、服务网关、智能路由、负载均衡、断路器、监控跟踪、分布式消息队列、配置管理等领域的解决方案。Spring Cloud 通过 Spring Boot 风格的封装，屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、容易部署的分布式系统开发工具包。一般来说，Spring Cloud 包含以下组件，主要以 Netflix 开源为主。

同 Spring Cloud 一样，Spring Cloud Alibaba 也是一套微服务解决方案，包含开发分布式应用微服务的必需组件，方便开发者通过 Spring Cloud 编程模型轻松使用这些组件来开发分布式应用服务。依托Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud 应用接入阿里微服务解决方案，通过阿里中间件来迅速搭建分布式应用系统。

![image-20220311155956062](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311155956062.png)

#### 5.1.1 阿里开源组件

**Nacos：**一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

**Sentinel：**把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

**RocketMQ：**开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务。

**Dubbo：**这个就不用多说了，在国内应用非常广泛的一款高性能 Java RPC 框架。

**Seata：**阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案。

**Arthas：**开源的Java动态追踪工具，基于字节码增强技术，功能非常强大。

#### 5.1.2 阿里商业化组件

作为一家商业公司，阿里巴巴推出 Spring Cloud Alibaba，很大程度上市希望通过抢占开发者生态，来帮助推广自家的云产品。所以在开源社区，夹带了不少私货，阿里商业化组件，整体易用性和稳定性还是很高的。

Alibaba Cloud ACM：一款在分布式架构环境中对应用配置进行集中管理和推送的应用配置中心产品。

Alibaba Cloud OSS：阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的云存储服务。

Alibaba Cloud SchedulerX：阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准的定时（基于 Cron 表达式）任务调度服务。

#### 5.1.3 集成SpringCloud组件

Spring Cloud Alibaba 作为整套的微服务解决组件，只依靠目前阿里的开源组件是不够的，更多的是集成当前的社区组件，所以 Spring Cloud Alibaba 可以集成 Zuul，GateWay等网关组件，也可继承Ribbon、OpenFeign等组件。

### 5.2 Nacos服务注册和配置中心

#### 5.2.1 Nacos介绍

Nacos （Dynamic Naming and Configuration Service）是阿里巴巴开源的一个针对微服务架构中服务发现、配置管理和服务管理平台。

Nacos就是注册中心+配置中心的组合（Nacos=Eureka + Config + Bus）

官网：https://nacos.io 下载地址：https://github.com/alibaba/Nacos

**Nacos功能特性**

* 服务发现与健康检查
* 动态配置管理
* 动态DNS服务
* 服务和元数据管理（管理平台的角度，nacos也有一个ui页面，可以看到注册的服务及其实例信息（元数据信息）等），动态的服务权重调整、动态服务优雅下线，都可以去做

#### 5.2.2 Nacos单例服务部署

1. 下载解压安装包，执行命令启动（我用的 nacos-server-2.0.4.tar.gz）

   ```bash
   [root@hao nocas]# tar zxvf nacos-server-2.0.4.tar.gz
   [root@hao bin]# startup.sh -m standalone 
   ```

   ```bash
   # 我是在个人服务器上部署的，如果要在本地windows启动也可以
   cmd startup.cmd
   ```

2. 访问nacos控制台：http://ip:8848/nacos 默认端口8848，默认用户名密码nacos/nacos

   ![image-20220311164405145](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311164405145.png)

#### 5.2.3 微服务注册到Nacos

1. 在父pom中引入SCA依赖管理版本

   ```xml
   <dependencyManagement>
       <dependencies>
           <dependency>
               <groupId>org.springframework.cloud</groupId>
               <artifactId>spring-cloud-dependencies</artifactId>
               <version>Greenwich.RELEASE</version>
               <type>pom</type>
               <scope>import</scope>
           </dependency>
           <!--SCA -->
           <dependency>
               <groupId>com.alibaba.cloud</groupId>
               <artifactId>spring-cloud-alibaba-dependencies</artifactId>
               <version>2.1.0.RELEASE</version>
               <type>pom</type>
               <scope>import</scope>
           </dependency>
       </dependencies>
   </dependencyManagement>
   ```

2. 各微服务中（page、product）引入nacos客户端依赖，**必须删除eureka-client依赖**

   ```xml
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

3. application.yml修改，添加nacos配置信息

   **在yml文件中需要删除调用config和eureka相关的配置，否则启动失败**

   ```yaml
   spring:
       cloud: 
           nacos: 
               discovery: 
                   server-addr: 127.0.0.1:8848 #nacos server 地址
   ```

4. 启动商品微服务，观察nacos控制台

   ![image-20220311171334957](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311171334957.png)

   ![image-20220311171426257](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311171426257.png)

   保护阈值：可以设置为0-1之间的浮点数，它其实是一个比例值（当前服务健康实例数/当前服务总实例数）

   **场景：**

   ​		一般流程下，nacos是服务注册中心，服务消费者要从nacos获取某一个服务的可用实例信息，对于服务实例有健康/不健康状态之分，nacos在返回给消费者实例信息的时候，会返回健康实例。这个时候在一些高并发、大流量场景下会存在一定的问题

   ​		如果服务A有100个实例，98个实例都不健康了，只有2个实例是健康的，如果nacos只返回这两个健康实例的信息的话，那么后续消费者的请求将全部被分配到这两个实例，流量洪峰到来，2个健康的实例也扛不住了，整个服务A 就扛不住，上游的微服务也会导致崩溃，产生雪崩效应。

   ​		保护阈值的意义在于当服务A健康实例数/总实例数 < 保护阈值 的时候，说明健康实例真的不多了，这个时候保护阈值会被触发（状态true）

   ​		nacos将会把该服务所有的实例信息（健康的+不健康的）全部提供给消费者，消费者可能访问到不健康的实例，请求失败，但这样也比造成雪崩要好，牺牲了一些请求，保证了整个系统的一个可用。

   ​		**注意：**阿里内部在使用nacos的时候，也经常调整这个保护阈值参数。

   

#### 5.2.4 负载均衡

​		Nacos客户端引入的时候，会关联引入Ribbon的依赖包，我们使用OpenFeign的时候也会引入Ribbon的依赖，Ribbon包括Hystrix都按原来方式进行配置即可

​		此处，我们将商品微服务，再启动了一个9001端口，注册到Nacos上，便于测试负载均衡，我们通过后台也可以看出。

​		测试：启动两个提供者一个消费者，测试消费者的调用 http://localhost:9100/page/getPort

#### 5.2.5 Nacos数据模型（领域模型）

​		Namespace命名空间、Group分组、集群这些都是为了进行归类管理，把**服务**和**配置文件**进行归类，归类之后就可以实现一定的效果，比如隔离

​		比如，对于服务来说，不同命名空间中的服务不能够互相访问调用

![image-20220311172032249](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311172032249.png)

Namespace：命名空间，对不同的环境进行隔离，比如隔离开发环境、测试环境和生产环境

Group：分组，将若干个服务或者若干个配置集归为一组，通常习惯一个系统归为一个组

Service：某一个服务，比如商品微服务

DataId：配置集或者可以认为是一个配置文件

**Namespace + Group + Service   如同 Maven 中的GAV坐标，GAV坐标是为了锁定Jar，而这里是为了锁定服务**

**Namespace + Group + Data ID   如同 Maven 中的GAV坐标，GAV坐标是为了锁定Jar，而这里是为了锁定服务**

**最佳实践**

​		Nacos抽象出了Namespace、Group、Service、DataId等概念，具体代表什么取决于怎么用（非常灵活），推荐用法如下。`上面配置的cloud-alibaba依赖2.1.0.RELEASE的版本是服务是配置不了分组，配置没有group属性，需要高版本的依赖，但高版本会和Mybatis-Plus有冲突导致无法启动,我自己改了提高Mybatis-plus的版本无法解决`

![image-20220311172430835](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311172430835.png)

#### 5.2.6 Nacos配置中心

回顾之前：Spring Cloud Config + Bus（配置的自动更新）

* Github 上添加配置文件
* 创建Config Server 配置中心—>从Github上去下载配置信息
* 具体的微服务(最终使用配置信息的)中配置Config Client—> ConfigServer获取配置信息

有Nacos之后，分布式配置就简单很多

Github不需要了（配置信息直接配置在Nacos server中），Bus也不需要了(依然可以完成动态刷新)

接下来

* 去Nacos server中添加配置信息
* 改造具体的微服务，使其成为Nacos Config Client，能够从Nacos Server中获取到配置信息

##### （1）Nacos Server添加配置

![image-20220311180438220](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311180438220.png)

![image-20220311180908791](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220311180908791.png)

**dataId的完整格式如下（创建时输入名称（DataID）别漏了文件后缀）**

```bash
${prefix}-${spring.profile.active}.${file-extension}
```



##### （2）微服务中开启Nacos配置管理

* 添加依赖

  ```xml
  <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
  </dependency>
  ```

* 微服务中如何锁定 Nacos Server 中的配置文件（dataId）

  通过 Namespace + Group + dataId 来锁定配置文件，Namespace不指定就默认public，Group不指定就默认 DEFAULT_GROUP

  * `prefix` 默认为 spring.application.name 的值，也可以通过配置项spring.cloud.nacos.config.prefix来配置。
  * `spring.profile.active`即为当前环境对应的 profile。 **注意：当 spring.profile.active为空时，对应的连接符`-` 也将不存在dataId的拼接格式变成 **  ${prefix}.${file**-**extension} 
  * `file-exetension` 为配置内容的数据格式，可以通过配置项`spring.cloud.nacos.config.file-extension`来配置。目前只支持 `properties` 和 `yaml` 类型。

  ```yaml
    cloud:
      nacos:
        discovery:
          server-addr: xxx:8848
        config:
          server-addr: xxx:8848
          namespace: public  #命名空间
          group: DEFAULT_GROUP #如果使用的默认分组,可以不设置
          file-extension: yaml
  ```

  

### 5.3 SCA Sentinel 分布式系统的流量防卫兵

#### 5.3.1 Sentinel 介绍

Sentinel是一个面向云原生微服务的流量控制、熔断降级组件。替代Hystrix，针对问题：服务雪崩、服务降级、服务熔断、服务限流

**Hystrix：**

服务消费者（静态化微服务）—>调用服务提供者（商品微服务）

在调用方引入Hystrix

* 自己搭建监控平台 dashboard
* 没有提供UI界面进行服务熔断、服务降级等配置（使用的是@HystrixCommand参数进行设置，代码入侵）

**Sentinel**

* 独立可部署Dashboard/控制台组件（其实就是一个jar文件，直接运行即可）
* 减少代码开发，通过UI界面配置即可完成细粒度控制

**Sentinel 分为两个部分:**

* 核心库：（Java 客户端）不依赖任何框架/库，能够运行于所有 Java 运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。
* 控制台：（Dashboard）基于 Spring Boot 开发，打包后可以直接运行，不需要额外的 Tomcat 等应用容器。

**Sentinel 具有以下特征:**

* **丰富的应用场景**：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
* **完备的实时监控**：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
* **广泛的开源生态**：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 SpringCloud、Dubbo的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
* **完善的** **SPI** **扩展点**：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。

Sentinel主要特性：

![image-20220314100700305](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220314100700305.png)

Sentinel 的开源生态：

![image-20220314100713085](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220314100713085.png)

#### 5.3.2 Sentinel 部署

下载地址：https://github.com/alibaba/Sentinel/releases 我这里使用v1.8.1

启动：`java -jar -Dserver.port=5000 sentinel-dashboard-1.8.1.jar`

用户名/密码：`sentinel/sentinel`

`不使用-Dserver.port默认控制台端口是8080，我的8080已经占用了，所以指定一个.它连接的客户端端口默认是8719也可以通过-Dcsp.sentinel.dashboard.server指定`

![image-20220314102502849](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220314102502849.png)



#### 5.3.3 服务改造

我们之前是使用的OpenFeign（Hystrix）来进行熔断降级，做的事情有第一应用配置开启hystrix第二在Controller当中注解配置了熔断降级的规则（或者在配置文件配置）。

现在我们使用sentinel则需要将之前的Hystrix相关配置去掉（为了不污染之前的代码，复制一个页面静态化微服务 service-page-9101）

* 引入依赖

  ```xml
  <dependency>
      <groupId>com.alibaba.cloud</groupId>
      <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
  </dependency>
  ```

* 修改application配置（也要暴露端点需要依赖actuator）

  ```yaml
  spring:
      cloud:
          sentinel:
              transport:
                  dashboard: xxx.xxx.xxx:5000  # 控制台的地址端口
                  port: 8719                   # 客户端开放端口
                  clientIp: xxx                # 不配置，默认就是当前的内网ip
  
  management: 
      endpoints: 
          web:exposure: 
              include: "*"
  ```

启动服务，发送请求即可看到监控信息。

`这里有些小伙伴（自己）可能把sentinel部署在自己服务器了也就是线上，然后就会出现问题在sentinel控制台看得到自己的服务，但请求服务实时监控里面并不会有数据显示。因为你的本地idea启的服务可以往线上的sentinel注册，但线上的sentinel就访问不到你本地的客户端地址了。所以本地测试还是启个本地要么将本地地址映射个公网的然后配到clientIp`

`还有个问题就是它是懒加载，都正常的情况下它也不会显示已注册的服务，你把服务的接口访问一下才可以出现服务，实时监控也是一样`

![image-20220315114004039](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315114004039.png)

#### 5.3.4 Sentinel 关键概念

![image-20220314114302832](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220314114302832.png)



#### 5.3.5 Sentinel 流量规则模块

系统并发能力有限，比如系统A的QPS支持1个，如果太多请求过来，那么A就应该进行流量控制了，比如其他请求直接拒绝

![image-20220315120658416](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315120658416.png)

![image-20220315115924657](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315115924657.png)

进行并发测试5条线程，其中四条被拒绝。



**资源名：**默认请求路径

**针对来源：**Sentinel可以针对调用者进行限流，填写微服务（消费者）名称，默认default（不区分来源）

**阈值类型/单机阈值**

* QPS：（每秒钟请求数量）当调用该资源的QPS达到阈值时进行限流
* 线程数：当调用该资源的线程数达到阈值的时候进行限流（线程处理请求的时候，如果说业务逻辑执行时间很长，流量洪峰来临时，会耗费很多线程资源，这些线程资源会堆积，最终可能造成服务不可用，进一步上游服务不可用，最终可能服务雪崩）

**是否集群：**是否集群限流

**流控模式：**

* 直接：资源调用达到限流条件时，直接限流
* 关联：关联的资源调用达到阈值时候限流自己
* 链路：只记录指定链路上的流量

**流控效果：**

* 快速失败：直接失败，抛出异常
* Warm Up：根据冷加载因子（默认3）的值，从阈值/冷加载因子，经过预热时长，才达到设置的QPS阈值
* 排队等待：匀速排队，让请求匀速通过，阈值类型必须设置为QPS，否则无效

##### 1）控流模式之关联限流

关联的资源调用达到阈值时候限流自己，比如用户注册接口，需要调用身份证校验接口（往往身份证校验接口），如果身份证校验接口请求达到阈值，使用关联，可以对用户注册接口进行限流。

![image-20220315132055158](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315132055158.png)

![image-20220315133002723](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315133002723.png)

仅仅密集请求test01时，并不会限流。只有test02限流时test01才进行当前的限流配置

##### 2）流控模式之链路限流

链路指的是请求链路（调用链：A-->B--C，D-->E-->C）

链路模式下会控制该资源所在的调用链路入口的流量。需要在规则中配置入口资源，即该调用链路入口的上下文名称。

一棵典型的调用树如下图所示：

![image-20220314115240017](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220314115240017.png)

上图中来自入口 Entrance1 和 Entrance2 的请求都调用到了资源 NodeA ，Sentinel 允许只根据某个调用入口的统计信息对资源限流。比如链路模式下设置入口资源为 Entrance1 来表示只有从入口Entrance1 的调用才会记录到 NodeA 的限流统计当中，而不关心经 Entrance2 到来的调用。

![image-20220315133314636](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315133314636.png)

![image-20220315133404054](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315133404054.png)

别的来源并不限流，只有从test02请求test01才限流

##### 1）流控效果之Warm up

当系统长期处于空闲的情况下，当流量突然增加时，直接把系统拉升到高水位可能瞬间把系统压垮，比如电商网站的秒杀模块。

通过 Warm Up 模式（预热模式），让通过的流量缓慢增加，经过设置的预热时间以后，到达系统处理请求速率的设定值。

Warm Up 模式默认会从设置的 QPS 阈值的 1/3 开始慢慢往上增加至 QPS 设置值。

![image-20220314115351790](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220314115351790.png)

**流控效果之排队等待**

排队等待模式下会严格控制请求通过的间隔时间，即请求会**匀速**通过，允许部分请求排队等待，通常用于消息队列削峰填谷等场景。需设置具体的超时时间，当计算的等待时间超过超时时间时请求就会被拒绝。

很多流量过来了，并不是直接拒绝请求，而是请求进行排队，一个一个匀速通过（处理），请求能等就等着被处理，不能等（等待时间>超时时间）就会被拒绝

例如，QPS 配置为 5，则代表请求每 200 ms 才能通过一个，多出的请求将排队等待通过。超时时间代表最大排队时间，超出最大排队时间的请求将会直接被拒绝。排队等待模式下，QPS 设置值不要超过 1000（请求间隔 1 ms）。

![image-20220315133801944](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315133801944.png)

#### 5.3.6 Sentinel 降级规则模块

流控是对外部来的大流量进行控制，熔断降级的视角是对内部问题进行处理。

Sentinel 降级会在调用链路中某个资源出现不稳定状态时（例如调用超时或异常比例升高），对这个资源的调用进行限制，让请求快速失败，避免影响到其它的资源而导致级联错误。当资源被降级后，在接下来的降级时间窗口之内，对该资源的调用都自动熔断，**这里的降级其实是Hystrix中的熔断**。

**策略**

Sentinel不会像Hystrix那样放过一个请求尝试自我修复，就是明明确确按照时间窗口来，熔断触发后，时间窗口内拒绝请求，时间窗口后就恢复。

![image-20220315141250223](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315141250223.png)

在窗口时间(统计时间)里达到了5个以上的请求，且最大的响应时间超过了200ms则记为慢调用，当慢调用的数量达到阈值比例（全部都超过才熔断则比例是1）则进行熔断，在熔断时长里都是处于熔断状态（不会进行重试）

修改test01加上线程睡眠时间为300ms，开启5个线程并发访问。这样最终的预期结果应该是全都超过200ms，进行熔断且之后5秒之内都是熔断状态

实际测试对于慢调用比例和异常比例完全不在预期，在正常应该5的并发可以达到熔断，实际不会且配置了更高的并发也是如此直到配置了1000的线程才熔断，熔断时间确实是如配置一样没有问题。但比例阈值、最小请求数以及统计时长无法修改。

**慢调用比例 **

当 1s`统计时长` 内持续进入 >=5`最小请求数` 个请求，平均响应时间超过200ms`最大RT`（以 ms 为单位），则标记为慢调用，当慢调用请求的比例超过了比例阈值则进行熔断。注意Sentinel 默认统计的 RT 上限是 4900 ms，超出此阈值的都会算作 4900 ms，若需要变更此上限可以通过启动配置项 -Dcsp.sentinel.statistic.max.rt=xxx 来配置。

**异常比例**

当资源的`统计时间内`请求量 >= `最小请求数`，并且期间异常总数占通过量的比例超过阈值之后，资源进入降级状态知道熔断时长走完

**异常数**

当资源的`统计时间内`请求量 >= `最小请求数`，并且期间异常总数超过阈值数量，资源进入降级状态知道熔断时长走完。

![image-20220315173424207](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315173424207.png)

实际测试jmeter线程10循环1次，10次都没有进行熔断。但在下一次调用都已经是属于熔断状态。也就是说并发10的情况下它没有在前5个已经异常的情况下进行熔断，而是都通过了之后才熔断。可能是触发熔断但实际熔断有延迟，但最终确实熔断了。

在这个推论的情况下，降低并发就将线程设置为4进行测试，手动执行两次。在第五次会进行熔断。如果把第5次或者还有6次7次都放到一个线程组直接测试，那么就会都通过产生异常没有熔断，也许确实是在第5条时就熔断了但后面的7、8、9等都在熔断之前就通过了，以至于只有后面再次手动调用的才是在熔断之后的状态当中。

![image-20220315174116323](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20220315174116323.png)





#### 5.3.7 SCA 小结

SCA实际上发展了三条线

* 第一条线：开源出来一些组件

* 第二条线：阿里内部维护了一个分支，自己业务线使用

* 第三条线：阿里云平台部署一套，付费使用

从战略上来说，SCA更是为了贴合阿里云。目前来看，开源出来的这些组件，推广及普及率不高，社区活跃度不高，稳定性和体验度上仍需进一步提升。











