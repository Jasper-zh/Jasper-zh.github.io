---
title: Spring Cloud微服务详解
date: 2021-07-22 11:58:17
tags: 总结文档
copyright: false
---

## 一、微服务架构

### 1.1 互联网应用架构演进

随着互联⽹的发展，⽤户群体逐渐扩大，⽹站的流量成倍增⻓，常规的单体架构已⽆法满⾜请求压⼒和业务的快速迭代，架构的变化势在必⾏。下⾯我们就以拉勾网的架构演进为例，从最开始的单体架构分析，⼀步步的到现在的微服务架构。

淘宝：LAMP，Linux、Apache、MySQL、PHP

#### 1.1.1 单体应用架构

在诞⽣之初，拉勾的⽤户量、数据量规模都⽐较⼩，项目所有的功能模块都放在一个工程中编码、编译、打包并且部署在一个Tomcat容器中的架构模式就是单体应用架构，这样的架构既简单实 ⽤、便于维护，成本⼜低，成为了那个时代的主流架构⽅式。

![image-20210802103712708](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802103712708.png)

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

![image-20210802103956900](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802103956900.png)

#### 1.1.2 垂直应用架构

为了避免上⾯提到的那些问题，开始做模块的垂直划分，做垂直划分的原则是基于现有的业务特性来做，核心目标标第⼀个是为了业务之间互不影响，第⼆个是在研发团队的壮⼤后为了提⾼效率，减少组件之间的依赖。

![image-20210802104039404](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802104039404.png)

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

![image-20210802104640560](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802104640560.png)



#### 1.1.4 微服务应用架构

微服务架构可以说是SOA架构的一种拓展，这种架构模式下它**拆分粒度更小**、服务更独立。把应用拆分成为一个个微小的服务，不同的服务可以使用不同的开发语言和存储，服务之间往往通过Restful等轻量级通信。微服务架构关键在于**微小、独立、轻量级通信**。

微服务是在 SOA 上做的升华粒度更加细致，微服务架构强调的⼀个重点是**业务需要彻底的组件化和服务化**

![image-20210802104754079](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210802104754079.png)

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

  ![image-20210803102008877](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210803102008877.png)

  ![image-20210803102016462](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210803102016462.png)

* **负载均衡**

  负载均衡即将请求压力分配到多个服务器（应用服务器、数据库服务器等），以此来提高服务的性能、可靠性

  ![image-20210803102123540](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803102123540.png)

* **熔断**

  熔断即断路保护。微服务架构中，如果下游服务因访问压力过大而响应变慢或失败，上游服务为了保护系统整体可用性，可以暂时切断对下游服务的调用。这种牺牲局部，保全整体的措施就叫做熔断。

  ![image-20210803102219278](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803102219278.png)

  ![image-20210803102234092](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803102234092.png)

* **链路追踪**

  微服务架构越发流行，一个项目往往拆分成很多个服务，那么一次请求就需要涉及到很多个服务。不同的微服务可能是由不同的团队开发、可能使用不同的编程语言实现、整个项目也有可能部署在了很多服务器上（甚至百台、千台）横跨多个不同的数据中心。所谓链路追踪，就是对一次请求涉及的很多个服务链路进行日志记录、性能监控

  ![image-20210803102328144](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803102328144.png)

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



![image-20210803102810160](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803102810160.png)



## 二、Spring Cloud概述

### 2.1 什么是Spring Cloud？

**[百度百科]**Spring Cloud是一系列框架的有序集合。它利用Spring Boot的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 Spring Boot的开发风格做到一键启动和部署。**Spring Cloud并没有重复制造轮子，它只是将目前各家公司开发的比较成熟、经得起实际考验的服务框架组合起来，通过Spring Boot风格进行再封装屏蔽掉了复杂的配置和实现原理，最终给开发者留出了一套简单易懂、易部署和易维护的分布式系统开发工具包**。

Spring Cloud是一系列框架的有序集合（Spring Cloud是一个规范）

开发服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等

利用Spring Boot的开发便利性简化了微服务架构的开发（自动装配）

![image-20210803103015056](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803103015056.png)

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

![image-20210803104843188](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803104843188.png)

Spring Cloud中的各组件协同工作，才能够支持一个完整的微服务架构。比如

* 注册中心负责服务的注册与发现，很好将各服务连接起来
* API网关负责转发所有外来的请求
* 断路器负责监控服务之间的调用情况，连续多次失败进行熔断保护。
* 配置中心提供了统一的配置信息管理服务,可以实时的通知各个服务获取最新的配置信息

### 2.4 Spring Cloud与Dubbo进行对比

​	Dubbo是阿里巴巴公司开源的一个高性能优秀的服务框架，基于RPC调用，对于目前使用率较高的Spring Cloud Netflix来说，它是基于HTTP的，所以效率上没有Dubbo高，但问题在于Dubbo体系的组件不全，不能够提供一站式解决方案，比如服务注册与发现需要借助于Zookeeper等实现，而SpringCloud Netflix则是真正的提供了一站式服务化解决方案，且有Spring大家族背景。

​	前些年，Dubbo使用率高于SpringCloud，但目前Spring Cloud在服务化/微服务解决方案中已经有了非常好的发展趋势。

![image-20210803105134868](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803105134868.png)

### 2.5 Spring Cloud与Spring Boot的关系

​	Spring Cloud 只是利用了Spring Boot 的特点，让我们能够快速的实现微服务组件开发，否则不使用Spring Boot的话，我们在使用Spring Cloud时，每一个组件的相关Jar包都需要我们自己导入配置以及需要开发人员考虑兼容性等各种情况。所以Spring Boot是我们快速把Spring Cloud微服务技术应用起来的一种方式。



## 三、案例准备

### 3.1 案例说明

本部分我们按照普通方式模拟一个微服务之间的调用，后续我们将一步步使用Spring Cloud的组件对案例进行改造。

需求：

![image-20210803105223408](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803105223408.png)

![image-20210803105231868](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803105231868.png)

完整业务流程图：

![image-20210803105249088](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803105249088.png)

### 3.2 案例数据库环境准备

数据库使用Mysql 5.7.x

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

![image-20210803105517677](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803105517677.png)

#### 3.3.1 页面静态化微服务

在Idea中新建module，命名为lagou-parent

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
<project
    xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>lagou-parent</artifactId>
        <groupId>com.lagou</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>lagou-service-common</artifactId>
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

2) 生成数据库实体类：com.lagou.common.pojo.**Products**

```java
package com.lagou.common.pojo;
import lombok.Data;
import javax.persistence.Id;
import javax.persistence.Table;

@Data 
@Table(name = "products") 
public class Products {
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

创建商品微服务lagou-service-product，继承lagou-parent

1）在商品微服务的pom文件中，引入公共组件坐标

```xml
<dependencies>
    <dependency>
        <groupId>com.lagou</groupId>
        <artifactId>lagou-service-common</artifactId>
        <version>1.0-SNAPSHOT</version>
    </dependency>
</dependencies>
```

2）在yml文件中配置端口、应用名、数据库连接等信息

```
server: 
	port: 9000 # 后期该微服务多实例，9000（10个以内） 
Spring: 
	application: 
		name: lagou-service-product 
	datasource: 
		driver-class-name: com.mysql.jdbc.Driver 
		url: jdbc:mysql://localhost:3306/lagou? useUnicode=true&characterEncoding=utf8&serverTimezone=UTC 
		username: root 
		password: wu7787879
```

3）Mapper接口开发

```java
package com.lagou.product.mapper; 

import com.baomidou.mybatisplus.core.mapper.BaseMapper; 
import com.baomidou.mybatisplus.core.mapper.Mapper; 
import com.lagou.common.pojo.Products; 
/**
 * 现在使用的Mybatis-plus组件，该组件是Mybatis的加强版 
 * 能够与SpringBoot进行非常友好的整合，对比Mybatis框架只有使用便捷的改变 
 * 没有具体功能的改变 
 * 具体使用：让具体的Mapper接口继承BaseMapper即可 
 */ 
public interface ProductMapper extends BaseMapper<Products> { 

}
```

4）serive层开发

```java
package com.lagou.product.service.impl;

import com.lagou.common.pojo.Products;
import com.lagou.product.mapper.ProductMapper;
import com.lagou.product.service.ProductService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service 
public class ProductServiceImpl implements ProductService {
	@Autowired 
	private ProductMapper productMapper;
	/**
	 * 根据商品ID查询商品对象 
	 */
	@Override 
	public Products findById(Integer productId) {
		return productMapper.selectById(productId);
	}
}
```

5）controller层开发

```java
package com.lagou.product.controller;

import com.lagou.common.pojo.Products;
import com.lagou.product.service.ProductService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController 
@RequestMapping("/product") 
public class ProductController {
	@Autowired 
	private ProductService productService;
	@RequestMapping("/query/{id}") 
	public Products query(@PathVariable Integer id){
		return productService.findById(id);
	}
}
```

6）启动类

```java
package com.lagou.product;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication 
@MapperScan("com.lagou.product.mapper") 
public class ProductApplication {
	public static void main(String[] args) {
		SpringApplication.run(ProductApplication.class,args);
	}
}
```



#### 3.3.4 页面静态化微服务

1）在pom文件中，引入公共组件依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project
    xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>lagou-parent</artifactId>
        <groupId>com.lagou</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <artifactId>lagou-service-page</artifactId>
    <dependencies>
        <dependency>
            <groupId>com.lagou</groupId>
            <artifactId>lagou-service-common</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
    </dependencies>
</project>
```

2）在yml文件中配置端口、应用名、数据库连接等信息

```
server: 
	port: 9100 # 后期该微服务多实例，端口从9100递增（10个以内） 
	
Spring: 
	application: 
		name: lagou-service-page 
	datasource: 
		driver-class-name: com.mysql.jdbc.Driver 
		url: jdbc:mysql://localhost:3306/lagou? useUnicode=true&characterEncoding=utf8&serverTimezone=UTC 
		username: root 
		password: wu7787879
```

3）编写PageController，在PageController中调用商品微服务对应的URL

```java
package com.lagou.page.controller;

import com.lagou.common.pojo.Products;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController 
@RequestMapping("/page") 
public class PageController {
	@Autowired 
	private RestTemplate restTemplate;
	@GetMapping("/getData/{id}") 
	public Products findDataById(@PathVariable Integer id){
		Products products = restTemplate.getForObject("http://localhost:9000/product/query/"+id, Products.class);
		System.out.println("从lagou-service-product获得product对象:"+products);
		return products;
	}
}
```

4）编写启动类，注入RestTemplate

```java
package com.lagou.page; 

import org.springframework.boot.SpringApplication; 
import org.springframework.boot.autoconfigure.SpringBootApplication; import org.springframework.context.annotation.Bean; 
import org.springframework.web.client.RestTemplate; 
import java.awt.print.Pageable;

@SpringBootApplication 
public class PageApplication {
	public static void main(String[] args) {
		SpringApplication.run(PageApplication.class,args);
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

![image-20210803112626313](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803112626313.png)

从形式上来说，Feign一个顶三，Feign = RestTemplate + Ribbon + Hystrix

![image-20210803112642721](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803112642721.png)

### 4.1 Eureka服务注册中心

常用的服务注册中心：Eureka、Nacos、Zookeeper、Consul

#### 4.1.1 关于服务注册中心

**注意：服务注册中心本质上是为了解耦服务提供者和服务消费者。**

服务消费者 → 服务提供者

服务消费者 → 服务注册中心 → 服务提供者

​	对于任何一个微服务，原则上都应存在或者支持多个提供者（比如商品微服务部署多个实例），这是由微服务的**分布式属性**决定的。

​	更进一步，为了支持弹性扩、缩容特性，一个微服务的提供者的数量和分布往往是动态变化的，也是无法预先确定的。因此，原本在单体应用阶段常用的静态LB机制就不再适用了，需要引入额外的组件来管理微服务提供者的注册与发现，而这个组件就是服务注册中心。

##### （1）注册中心实现原理

![image-20210803112923411](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803112923411.png)

![image-20210803112935155](https://gitee.com/Jasper-zh/image_host/raw/master/image-20210803112935155.png)

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

#### 4.1.3 搭建单例Eureka Server服务注册中心

#### 4.1.4 搭建Eureka Server高可用集群

#### 4.1.5 Eureka细节详解

##### （1）Eureka元数据详解

##### （2）Eureka客户端详解

##### （3）Eureka服务端详解



### 4.2 Ribbon负载均衡

#### 4.2.1 关于负载均衡

#### 4.2.2 Ribbon高级应用

#### 4.2.3 Ribbon负载均衡策略

#### 4.2.4 Ribbon核心源码刨析



### 4.3 Hystrix熔断器

#### 4.3.1 微服务当中的雪崩效应

#### 4.3.2 雪崩效应解决方案

#### 4.3.3 Hystrix简介

#### 4.3.4  Hystrix应用

##### （1）熔断处理

##### （2）降级处理

#### 4.3.5 Hystrix舱壁模式

#### 4.3.6 Hystrix工作流程与高级应用

  

### 4.4 Feign远程调用组件

#### 4.4.1 Feign简介

#### 4.4.2 Feign配置应用

#### 4.4.3 Feign对负载均衡的支持

#### 4.4.4 Feign对熔断器的支持

#### 4.4.5 Feign对请求压缩和响应压缩的支持



### 4.5 GateWay网关组件

#### 4.5.1 GateWay简介

#### 4.5.2 GateWay核心概念

#### 4.5.3 GateWay如何工作

#### 4.5.4 GateWay应用

#### 4.5.5 GateWay路由规则详解

#### 4.5.6 GateWay动态路由详解

#### 4.5.7 GateWay过滤器

##### （1）GateWay过滤器简介

##### （2）自定义全局过滤器实现IP访问限制（黑白名单）

#### 4.5.8 GateWay高可用



### 4.6 Spring Cloud Config分布式配置中心

#### 4.6.1 分布式配置中心应用场景

#### 4.6.2 Spring Cloud Config

##### （1）Config简介

##### （2）Config分布式配置应用

#### 4.6.3 Config配置手动刷新

#### 4.6.4 Config配置自动更新

##### （1）消息总线Bus

##### （2）Config + Bus实现自动刷新



## 五、第二代SpringCloud核心组件（SCA）

### 5.1 第二代介绍

#### 5.1.1 阿里开源组件

#### 5.1.2 阿里商业化组件

#### 5.1.3 集成SpringCloud组件



### 5.2 Nacos服务注册和配置中心

#### 5.2.1 Nacos介绍

#### 5.2.2 Nacos单例服务部署

#### 5.2.3 微服务注册到Nacos

#### 5.2.4 负载均衡

#### 5.2.5 Nacos数据模型（领域模型）

#### 5.2.6 Nacos配置中心

##### （1）Nacos Server添加配置

##### （2）微服务中开启Nacos配置管理



### 5.3 SCA Sentinel 分布式系统的流量防卫兵

#### 5.3.1 Sentinel 介绍

#### 5.3.2 Sentinel 部署

#### 5.3.3 服务改造

#### 5.3.4 Sentinel 关键概念

#### 5.3.5 Sentinel 流量规则模块

#### 5.3.6 Sentinel 降级规则模块

#### 5.3.7 SCA 小结







