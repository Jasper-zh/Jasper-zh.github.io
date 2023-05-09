---
title: SpringSecurity详解
date: 2022-08-25 14:11:53
tags: 总结文档
categories: 
- 记录文档
---

# 一、SpringSecurity入门

## 1.1 Spring Security简介

​	Spring Security是一个功能强大且高度可定制的身份验证和访问控制框架。它是用于保护基于 Spring的应用程序的实际标准。Spring Security是一个框架，致力于为Java应用程序提供身份验证和授权。与所有Spring项目一样，Spring Security的真正强大之处在于可以轻松扩展以满足自定义要求.

> 官网地址：https://spring.io/projects/spring-security



## 1.2 安全技术方案对比

目前在整个Java开发的系统中,需要用于身份验证和访问控制框架的框架除了Spring Security, 还有一个就是Apache shiro框架.

**Shiro**

Shiro是一个强大而灵活的开源安全框架，能够非常清晰的处理认证、授权、管理会话以及密码加密。如下是它所具有的特点：

* 易于理解的 Java Security API；
* 简单的身份认证（登录），支持多种数据源（LDAP，JDBC，Kerberos，ActiveDirectory等）
* 对角色的简单的鉴权（访问控制），支持细粒度的鉴权；
* 支持一级缓存，以提升应用程序的性能；
* 内置的基于 POJO 企业会话管理，适用于 Web 以及非 Web 的环境；
* 异构客户端会话访问；
* 非常简单的加密 API；
* 不跟任何的框架或者容器捆绑，可以独立运行。

**Spring Security**

除了不能脱离Spring，shiro的功能它都有。而且Spring Security对Oauth、OpenID也有支持,Shiro则需要自己手动实现。Spring Security的权限细粒度更高。

> OAuth在"客户端"与"服务提供商"之间，设置了一个授权层（authorization layer）。"客户端"不能直接登录"服务提供商"，只能登录授权层，以此将用户与客户端区分开来。"客户端"登录授权层所用的令牌（token），与用户的密码不同。用户可以在登录的时候，指定授权层令牌的权限范围和有效期。
> "客户端"登录授权层以后，"服务提供商"根据令牌的权限范围和有效期，向"客户端"开放用户储存的资料。
>
> OpenID 系统的第一部分是身份验证，即如何通过 URI 来认证用户身份。目前的网站都是依靠用户名和密码来登录认证，这就意味着大家在每个网站都需要注册用户名和密码，即便你使用的是同样的密码。如果使用 OpenID ，你的网站地址（URI）就是你的用户名，而你的密码安全的存储在一个 OpenID 服务网站上（你可以自己建立一个 OpenID 服务网站，也可以选择一个可信任的 OpenID 服务网站来完成注册）。
> 与OpenID同属性的身份识别服务商还有ⅥeID,ClaimID,CardSpace,Rapleaf,Trufina ID Card
> 等，其中ⅥeID通用账户的应用最为广泛



## 1.3 Spring Security功能简介

1. 认证: 用户登录, 解决的是"你是谁?"
2. 授权: 判断用户拥有什么权限，可以访问什么资源. 解决的是"你能干什么?"
3. 安全防护，防止跨站请求，session 攻击等



## 1.4 Spring Security应用场景

1. 用户登录, 传统基于web开发的项目的登录功能.

   ![]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615110728666.png)

2. 用户授权, 在系统中用户拥有哪些操作权限

   ![异常页- Ant Design](https://gw.alipayobjects.com/mdn/rms_08e378/afts/img/A*j5LCQabCiz8AAAAAAAAAAABkARQnAQ)

3. 单一登录, 一个账号只能在同一时间只能在一个地方进行登录, 如果在其他地方进行第二次登录,则剔除之前登录操作

   ![]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615130617884.png)

4. 集成cas,做单点登录,即多个系统只需登录一次，无需重复登录

   ![image-20220615135636770]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615135636770.png)

5. 集成oauth2 ,做登录授权, 可以用于app登录和第三方登录(QQ,微信等), 也可以实现cas的功能.

   ![image-20220615135719376]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615135719376.png)



## 1.5 Spring Security入门案例

**快速开始**

### 1.5.1 IDEA创建工程

![image-20220615140801008]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615140801008.png)

![image-20220615140909106]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615140909106.png)

### 1.5.2 编写Controller

![image-20220615142001064]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615142001064.png)

```java
package cn.yournotes.security_demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @GetMapping("/hello")
    public String hello(){
        return "hello world";
    }
}
```

启动测试

![image-20220615141855306]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615141855306.png)

### 1.5.3 加入Security依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

再次启动测试

![20220615_142908]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/20220615_142908.gif)

我们可以先观察下表单页面

![image-20220615143401026]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615143401026.png)

我们可以看到这个结构主要有三点：

* 表单的提交方式是`post`，接口地址是`/login`
* 有两个正常的`input`，且input参数名称分别是`username`和`password`
* 还有一个隐藏域的`input`，参数名是`_csrf`



SpringBoot已经为SpringSecurity提供了默认配置，默认工程中所有资源都必须通过Security认证后才能访问。那么问题来了！此刻并没有连接数据库，也并未在内存中指定认证用户，如何认证呢？

其实SpringBoot已经提供了默认用户名user，密码在项目启动时随机生成，如图：

![image-20220615144004741]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615144004741.png)

认证通过继续访问处理器资源

![20220615_144221]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/20220615_144221.gif)



# 二、SpringSecurity认证

>说明：为方便学习，这里已经事先为准备好一个半成品的后台管理系统，而想要完善另一部分，就需要用到今天学习的内容SpringSecurity了！

## 2.1 下载案例

先下载相关资源代码以及SQL：

![image-20220615161612015]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615161612015.png)

**导入数据**

创建个数据库security_management然后导入素材当中的sql文件：

![image-20220615161922548]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615161922548.png)



**导入工程**

![image-20220615165005166]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615165005166.png)

**启动**

![image-20220615165644551]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615165644551.png)

## 2.2 SpringSecurity认证基本原理与两种方式

现在在初始项目中是没有认证的，直接进入首页。那么现在我们给它加上Security依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

### 2.2.1 过滤链介绍

在使用`SpringSecurity`框架，该框架会默认自动地替我们将系统中的资源进行保护，每次访问资源的时候都必须经过一层身份的校验，如果通过了则重定向到我们输入的url中，否则访问是要被拒绝的。那么`SpringSecurity`框架是如何实现的呢? `Spring Security`功能的实现主要是由一系列过滤器相互配合完成。也称之为过滤器链.

![image-20220615170240484]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615170240484.png)

过滤器是一种典型的AOP思想，下面简单了解下这些过滤器链,后续再源码剖析中在涉及到过滤器链之后再仔细讲解.

1. org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter

   > 根据请求封装获取WebAsyncManager，从WebAsyncManager获取/注册的安全上下文可调用处理拦截器

2. org.springframework.security.web.context.SecurityContextPersistenceFilter

   > SecurityContextPersistenceFilter主要是使用SecurityContextRepository在session中保存或更新一个SecurityContext，并将SecurityContext给以后的过滤器使用，来为后续fifilter建立所需的上下文。SecurityContext中存储了当前用户的认证以及权限信息。

3. org.springframework.security.web.header.HeaderWriterFilter

   > 向请求的Header中添加相应的信息,可在http标签内部使用security:headers来控制

4. org.springframework.security.web.csrf.CsrfFilter

   > csrf又称跨域请求伪造，SpringSecurity会对所有post请求验证是否包含系统生成的csrf的token信息，如果不包含，则报错。起到防止csrf攻击的效果。

5. org.springframework.security.web.authentication.logout.LogoutFilter

   > 匹配URL为/logout的请求，实现用户退出,清除认证信息。

6. org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter

   > 表单认证操作全靠这个过滤器，默认匹配URL为/login且必须为POST请求。

7. org.springframework.security.web.authentication.ui.DefaultLoginPageGeneratingFilter

   > 如果没有在配置文件中指定认证页面，则由该过滤器生成一个默认认证页面。

8. org.springframework.security.web.authentication.ui.DefaultLogoutPageGeneratingFilter

   > 由此过滤器可以生产一个默认的退出登录页面

9. org.springframework.security.web.authentication.www.BasicAuthenticationFilter

   > 此过滤器会自动解析HTTP请求中头部名字为Authentication，且以Basic开头的头信息。

10. org.springframework.security.web.savedrequest.RequestCacheAwareFilter

    > 通过HttpSessionRequestCache内部维护了一个RequestCache，用于缓存HttpServletRequest

11. org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter

    > 针对ServletRequest进行了一次包装，使得request具有更加丰富的API

12. org.springframework.security.web.authentication.AnonymousAuthenticationFilter

    > 当SecurityContextHolder中认证信息为空,则会创建一个匿名用户存入到SecurityContextHolder中。spring security为了兼容未登录的访问，也走了一套认证流程，只不过是一个匿名的身份。

13. org.springframework.security.web.session.SessionManagementFilter

    > securityContextRepository限制同一用户开启多个会话的数量

14. org.springframework.security.web.access.ExceptionTranslationFilter

    > 异常转换过滤器位于整个springSecurityFilterChain的后方，用来转换整个链路中出现的异常

15. org.springframework.security.web.access.intercept.FilterSecurityInterceptor

    > 获取所配置资源访问的授权信息，根据SecurityContextHolder中存储的用户信息来决定其是否有权限。



Spring Security默认会加载15个过滤器, 但是随着配置可以增加或者删除一些过滤器.



## 2.2.2 认证方式

**HttpBasic认证**

HttpBasic登录验证模式是Spring Security实现登录验证最简单的一种方式，也可以说是最简陋的一种方式。它的目的并不是保障登录验证的绝对安全，而是提供一种“防君子不防小人”的登录验证。

在使用的Spring Boot早期版本为1.X版本,依赖的Security 4.X版本，那么就无需任何配置，启动项目访问则会弹出默认的httpbasic认证。现在使用的是spring boot2.0以上版本（依赖Security5.X版本），HttpBasic不再是默认的验证模式，在spring security 5.x默认的验证模式已经是表单模式。

HttpBasic模式要求传输的用户名密码使用Base64模式进行加密。如果用户名是 "admin" ，密码是“ admin”,则将字符串"admin:admin" 使用Base64编码算法加密。加密结果可能是：YWtaW46YWRtaW4=。HttpBasic模式真的是非常简单又简陋的验证模式，Base64的加密算法是可逆的,想要破解并不难.

![image-20220615172055505]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615172055505.png)

**FormLogin认证**

Spring Security的HttpBasic模式，该模式比较简单，只是进行了通过携带Http的Header进行简单的登录验证，而且没有定制的登录页面，所以使用场景比较窄。对于一个完整的应用系统，与登录验证相关的页面都是高度定制化的，非常美观而且提供多种登录方式。这就需要Spring Security支持我们自己定制登录页面, spring boot2.0以上版本（依赖Security 5.X版本）默认会生成一个登录页面.



## 2.3 表单认证

### 2.3.1 自定义表单登录页面

编写SecurityConfiguration配置类

```java
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()                   // 开启formLogin认证
                .loginPage("/login.html")  // 自定义登录页
                .and()
                .authorizeRequests()       // 选择哪些请求
                .anyRequest()              // 任意请求
                .authenticated();          // 选择的请求请求需要认证
    }
}
```

启动后访问会发现有问题：

**重定向次数过多**

![image-20220615183617420]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615183617420.png)

原因在于我们设置了对所有请求进行认证，因此访问login.html资源时也需要进行认证而认证页 又是访问login.html因此陷入死循环。它本身的登录不需要额外配就会过滤，但我们自己指定的登录页它不会帮我们去过滤掉，我们需要手动放行。

```java
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()                   // 开启formLogin认证
                .loginPage("/login.html")  // 自定义登录页
                .and()
                .authorizeRequests()       // 选择哪些请求
                .antMatchers("/login.html").permitAll() //这个请求开允许
                .anyRequest()              // 任意请求
                .authenticated();          // 选择的请求请求需要认证
    }
}
```

再次启动

**404**

![image-20220615184410172]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615184410172.png)

因为在Springboot都是请求的方式path都是找controller，它不会去资源文件去加载，我们需要配置成接口路径在接口里面返回页面。

在项目里面已经有了这个接口，配置文件配置了前缀templats以及后缀.html 因此返回login就可在资源文件找到页面

![image-20220615184643697]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615184643697.png)

```java
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()                   // 开启formLogin认证
                .loginPage("/toLoginPage")  // 自定义登录页
                .and()
                .authorizeRequests()       // 选择哪些请求
                .antMatchers("/toLoginPage").permitAll() //这个请求开允许
                .anyRequest()              // 任意请求
                .authenticated();          // 选择的请求请求需要认证
    }
}
```

修改后再次启动，这次会出现样式丢失的问题

**样式丢失**

![image-20220615185118914]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220615185118914.png)

这个很显然就是这些资源请求被拦截的问题，我们需要在Security配置当中通过WebSecurity去忽略指定的内容不参与安全控制。

```java
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()                   // 开启formLogin认证
                .loginPage("/toLoginPage")  // 自定义登录页
                .and()
                .authorizeRequests()       // 选择哪些请求
                .antMatchers("/toLoginPage").permitAll() //这个请求开允许
                .anyRequest()              // 任意请求
                .authenticated();          // 选择的请求请求需要认证
    }
    @Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/css/**", "/images/**", "/js/**",
                "/favicon.ico");
    }
}
```

Spring Security中，安全构建器HttpSecurity和WebSecurity的区别是 :

* WebSecurity不仅通过HttpSecurity定义某些请求的安全控制，也通过其他方式定义其他某些请求可以忽略安全控制;
* HttpSecurity仅用于定义需要安全控制的请求(当然HttpSecurity也可以指定某些请求不需要安全控制);
* 可以认为HttpSecurity是WebSecurity的一部分，WebSecurity是包含HttpSecurity的更大的一个概念;
* 构建目标不同
  * WebSecurity构建目标是整个Spring Security安全过滤器FilterChainProxy,
  * HttpSecurity的构建目标仅仅是FilterChainProxy中的一个SecurityFilterChain。



### 2.3.2 表单登录

在上面过滤器链中我们知道有一个过滤器叫做UsernamePasswordAuthenticationFilter是处理表单登录的，那么现在来观察一下这个过滤器。

![image-20220621141816713]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220621141816713.png)

在源码当中可以看到，表单中两个input的name分别是username和password参数，路径为/login，方式是POST。这些都可以修改为自定义的值

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()                   // 开启formLogin认证
                .loginPage("/toLoginPage")  // 自定义登录页
                .loginProcessingUrl("/login") // 登录接口url
                .usernameParameter("username").passwordParameter("password") //自定义表单name
                .successForwardUrl("/")    //登录成功跳转
                .and()
                .authorizeRequests()       // 选择哪些请求
                .antMatchers("/toLoginPage").permitAll() //这个请求开允许
                .anyRequest()              // 任意请求
                .authenticated();          // 选择的请求请求需要认证
        // 关闭scrf防护
        http.csrf().disable();
    }
```

后端关于login接口的参数、路径、类型定义好后，那么自定义页面进行对应的请求即可。

![image-20220621144637763]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220621144637763.png)

配置完成重启进行登录用户名user、密码在后端控制台生成

![image-20220621145850528]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220621145850528.png)

登录完成发现还是有新问题，里面的内容是拒绝请求

![image-20220621145757379]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220621145757379.png)

问题在于，在首页最下面使用了iframe

![image-20220621150150568]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220621150150568.png)

发现行内框架iframe这里出现问题了. Spring Security下，X-Frame-Options默认为DENY，其实非Spring
Security环境下，X-Frame-Options的默认大多也是DENY，这种情况下，浏览器拒绝当前页面加载任何
Frame页面，设置含义如下：

* DENY：浏览器拒绝当前页面加载任何Frame页面 此选择是默认的.
* SAMEORIGIN：frame页面的地址只能为同源域名下的页面

那么需要响应时需要允许iframe的加载

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()                   // 开启formLogin认证
        .loginPage("/toLoginPage")  // 自定义登录页
        .loginProcessingUrl("/login") // 登录接口url
        .usernameParameter("username").passwordParameter("password") //自定义表单name
        .successForwardUrl("/")    //登录成功跳转
        .and()
        .authorizeRequests()       // 选择哪些请求
        .antMatchers("/toLoginPage").permitAll() //这个请求开允许
        .anyRequest()              // 任意请求
        .authenticated();          // 选择的请求请求需要认证
    // 关闭scrf防护
    http.csrf().disable();
    // 允许iframe加载页面
    http.headers().frameOptions().sameOrigin();
}
```



### 2.3.3 基于数据库实现认证功能

目前认证使用的是默认的用户名user以及自动生成打印在控制台的秘密，那么现在要将用户凭证配置在数据库。

实现这样的功能就需要实现security当中的UserDetailsService接口，重写里面的loadUserByUsername进行自定义校验即可。

方法传进来的参数是用户名，拿去用自己的service去数据库查处用户实体

```java
@Service
public class MyUserDetailsService implements UserDetailsService {
    @Autowired
    UserService userService;

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        UserVo user = userService.findByUsername(s);
        if(user == null){
            throw new UsernameNotFoundException("没有找到这个用户名:"+s);
        }
        // 先声明一个权限集合,构造函数必须传值且不能为null,传个空集合即可
        Collection<? extends GrantedAuthority> authorities = new ArrayList<>();
        // 需要返回一个SpringSecurity的UserDetails对象
        UserDetails userDetails = new User(
                user.getUsername(),      // 用户名
                "{noop}"+user.getPassword(), //密码{noop}表示不加密
                true,            // 用户是否启用 true启用
                true,    // 用户是否过期 true没过期
                true,  // 用户凭证是否过期 true没过期
                true,    // 是否锁定 true未锁定
                authorities
        );
        return userDetails;
    }
}
```

定义好我们的认证逻辑后，将这个逻辑配置到Security当中，一样还是`在SecurityConfiguration`的configure重载方法进行配置

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.userDetailsService(myUserDetailsService);
}
```

### 2.3.4 密码加密认证

在基于数据库完成用户登录过程中，我们使用的密码是明文，给出UserDetails时密码使用了`{noop}`进行标记。那么下面对SpringSecurity中密码编码进行一些探讨。

SpringSecurity当中PasswordEncoder就是对我们密码进行编码的工具接口。该接口有两个功能：一个是**匹配验证**。另一个是**密码编码**。

![image-20220624121053806]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624121053806.png)

**BCrypt算法介绍**

任何应用考虑到安全，绝不能明文的方式保存密码。密码应该通过哈希算法进行加密。 有很多标准的算法比如SHA或者MD5，结合salt(盐)是一个不错的选择。 Spring Security 提供了BCryptPasswordEncoder类,实现Spring的PasswordEncoder接口使用BCrypt强哈希方法来加密密码。BCrypt强哈希方法 每次加密的结果都不一样,所以更加的安全。

bcrypt算法相对来说是运算比较慢的算法，在密码学界有句常话：越慢的算法越安全。黑客破解成本越高.通过salt和const这两个值来减缓加密过程，它的加密时间（百ms级）远远超过md5（大概1ms左右）。对于计算机来说，Bcrypt 的计算速度很慢，但是对于用户来说，这个过程不算慢。bcrypt是单向的，而且经过salt和cost的处理，使其受攻击破解的概率大大降低，同时破解的难度也提升不少，相对于MD5等加密方式更加安全，而且使用也比较简单

bcrypt加密后的字符串形如：`$2a$10$wouq9P/HNgvYj2jKtUN8rOJJNRVCWvn1XoWy55N3sCkEHZPo3lyWq`

> 其中$是分割符，无意义；2a是bcrypt加密版本号；10是const的值；而后的前22位是salt值；再然后的字符串就是密码的密文了；这里的const值即生成salt的迭代次数，默认值是10，推荐值12。

**在项目中使用BCrypt**

PasswordEncoder有很多实现类因此是提供了工厂方法模式去创建，通过指定不同的参数得到不同的`具体编码器`

此工厂类是PasswordEncoderFactories

![image-20220624125222715]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624125222715.png)

之前我们在项目中密码使用的是明文的是`noop` , 代表不加密使用明文密码, 现在用`BCrypt`只需要将`noop` 换成`bcrypt` 即可

`{noop}` 改成 `{bcrypt}` 后也记得要将数据库的密码改为加密后的密码，否则在认证时对数据库密码进行解密就对不上了。

既然我们指定了`{bcrypt}` ，那就看看bcrypt编码器对指定的密码进行加密的结果，复制到数据库即可

![image-20220624130752568]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624130752568.png)

![image-20220624133303944]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624133303944.png)

**登录成功**

![20220624_133148]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/20220624_133148.gif)

### 2.3.5 获取当前登录用户

在传统web系统中, 我们将登录成功的用户放入session中, 在需要的时候可以从session中获取用户,那么Spring Security中我们如何获取当前已经登录的用户呢?

* SecurityContextHolder

  保留系统当前的安全上下文SecurityContext，其中就包括当前使用系统的用户信息。

* SecurityContext

  安全上下文，获取当前经过身份验证的主体或者身份验证请求令牌

**代码实现：**

```java
@RequestMapping("/loginUser")
@ResponseBody
public UserDetails getCurrentUser(){
    UserDetails userDetails = (UserDetails)SecurityContextHolder.getContext().getAuthentication().getPrincipal();
    return userDetails;
}
```

除了上述方法, Spring Security 还提供了2种方式可以获取.

```java
@RequestMapping("/loginUser01")
@ResponseBody
public UserDetails getCurrentUser(Authentication auth){
    UserDetails userDetails = (UserDetails)auth.getContext().getAuthentication().getPrincipal();
    return userDetails;
}

@RequestMapping("/loginUser02")
@ResponseBody
public UserDetails getCurrentUser(getCurrentUser(@AuthenticationPrincipal UserDetails userDetails){
    return userDetails;
}
```

### 2.3.6 remember me 记住我

在大多数网站中，都会实现RememberMe这个功能，方便用户在下一次登录时直接登录，避免再次输入用户名以及密码去登录,Spring Security针对这个功能已经帮助我们实现。

下面我们来看下他的原理图：

#### 2.3.6.1 简单生成Token方法

![image-20220624134742201]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624134742201.png)

Token=MD5(username+分隔符+expiryTime+分隔符+password)

**注意: 这种方式不推荐使用, 有严重的安全问题. 就是密码信息在前端浏览器cookie中存放. 如果cookie**
**被盗取很容易破解.**

**代码实现：**

1. 前端的选框按钮

   ```html
   <div class="form-group">
       <div>
           <!--记住我 name为remember-me value值可选true yes 1 on 都行-->
           <input type="checkbox" name="remember-me" value="true"/>记住我
       </div>
   </div>
   ```

2. 后端开启remember-me功能

   ```java
   .and().rememberMe()//开启记住我功能
   .tokenValiditySeconds(1209600)// token失效时间默认2周
   .rememberMeParameter("remember-me")// 自定义表单name值
   ```

   登录接口配置上面一段即可开启

   ```java
   @Override
   protected void configure(HttpSecurity http) throws Exception {
       http.formLogin()                   // 开启formLogin认证
           .loginPage("/toLoginPage")  // 自定义登录页
           .loginProcessingUrl("/login") // 登录接口url
           .usernameParameter("username").passwordParameter("password") //自定义表单name
           .successForwardUrl("/")    //登录成功跳转
           .and().rememberMe()//开启记住我功能
           .tokenValiditySeconds(1209600)// token失效时间默认2周
           .rememberMeParameter("remember-me")// 自定义表单name值
           .and()
           .authorizeRequests()       // 选择哪些请求
           .antMatchers("/toLoginPage").permitAll() //这个请求开允许
           .anyRequest()              // 任意请求
           .authenticated();          // 选择的请求请求需要认证
       // 关闭scrf防护
       http.csrf().disable();
       // 允许iframe加载页面
       http.headers().frameOptions().sameOrigin();
   }
   ```

3. 登录成功产生一条cookie
   ![image-20220624144507496]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624144507496.png)

   

#### 2.3.6.2 持久化的Token生成方法

![image-20220624144745432]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624144745432.png)

存入数据库的Token包含：

token: 随机生成策略,每次访问都会重新生成

series: 登录序列号，随机生成策略。用户输入用户名和密码登录时，该值重新生成。使用remember-me功能，该值保持不变

expiryTime: token过期时间。

Token = encode(series+token)

**代码实现**

1. 后端配置

   同样也是在SecurityConfiguration进行配置

   ```java
   .and().rememberMe()//开启记住我功能
   .tokenValiditySeconds(1209600)// token失效时间默认2周
   .rememberMeParameter("remember-me")// 自定义表单name值
   .tokenRepository(getPersistentTokenRepository()) //设置token的存储库
   ```

   ```java
   @Autowired
   DataSource dataSource;
   /**
    * 持久化token,负责token与数据库之间的相关操作
    *
    * @return
    */
   @Bean
   public PersistentTokenRepository getPersistentTokenRepository() {
       JdbcTokenRepositoryImpl tokenRepository = new JdbcTokenRepositoryImpl();
       tokenRepository.setDataSource(dataSource); //设置数据源
       // 启动时创建一张表, 第一次启动的时候创建, 第二次启动的时候需要注释掉, 否会保存
       tokenRepository.setCreateTableOnStartup(true);
       return tokenRepository;
   }
   ```

2. 重新登录

   ![image-20220624150903356]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624150903356-16560556187322.png)

   表已创建就可以把 `tokenRepository.setCreateTableOnStartup(true)`去掉了

   启动项目重新登录，数据库会插入记录。说明持久化token方式已经生效。

   ![image-20220624151059011]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624151059011.png)

   ![image-20220624151438664]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624151438664.png)

3. Cookie窃取伪造访问

   使用网页登录系统，记录remember-me的值

   在postman伪造cookie，进行请求

   ![image-20220624161437398]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624161437398.png)

   可以正常访问，而不是拦截到登录

   ![image-20220624162530909]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624162530909.png)

4. 安全验证

   如果要禁止是`记住我`的token进行访问，可以进行控制

   ```java
   @GetMapping("/{id}")
   @ResponseBody
   public UserVo getById(@PathVariable Integer id) {
       //获取认证信息
       Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
       // 判断认证信息是否来源于RememberMe
       if (RememberMeAuthenticationToken.class.isAssignableFrom(authentication.getClass())) {
           throw new RememberMeAuthenticationException("认证信息来源于 RememberMe,请重新登录");
       }
       UserVo user = userService.getById(id);
       return user;
   }
   ```

   ![image-20220624162807782]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624162807782.png)

   

   所以在重要的步骤我们可以加以控制`记住我`的访问



### 2.3.7 自定义登录状态处理

在某些场景下,用户登录成功或失败的情况下用户需要执行一些后续操作,比如登录日志的搜集,或者在现在目前前后端分离的情况下用户登录成功和失败后需要给前台页面返回对应的错误信息, 有前台主导登录成功或者失败的页面跳转.这个时候需要要到用到AuthenticationSuccessHandler与AnthenticationFailureHandler.

* 实现AuthenticationSuccessHandler接口，并重写onAnthenticationSuccesss()方法.
* 实现AuthenticationFailureHandler接口，并重写onAuthenticationFailure()方法；

**代码实现**

```java
@Service
public class MyAuthHandle implements AuthenticationSuccessHandler, AuthenticationFailureHandler {
    private RedirectStrategy redirectStrategy = new DefaultRedirectStrategy();
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authentication) throws IOException, ServletException {
    }
    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        System.out.println("登录成功后续处理....");
        //重定向到index页
        redirectStrategy.sendRedirect(request, response, "/");
    }
    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {
        System.out.println("登录失败后续处理....");
        //重定向到login页
        redirectStrategy.sendRedirect(request, response, "/toLoginPage");
    }
}
```

在SecurityConfiguration配置上这些自定义处理

配置在登录表单后即可

```java
http.formLogin()                   // 开启formLogin认证
    .loginPage("/toLoginPage")     // 自定义登录页
    .loginProcessingUrl("/login")  // 登录接口url
    .usernameParameter("username").passwordParameter("password") //自定义表单name
    .successForwardUrl("/")        //登录成功跳转
    .successHandler(myAuthHandle)
    .failureHandler(myAuthHandle)
    // 略...
```



除了登录成功登陆失败这些节点还有其他的很多节点都可以进行自定义处理

```java
.and()
.formLogin()  //开启登录
.permitAll()  //允许所有人访问
.successHandler(myAuthHandle) // 登录成功逻辑处理
.failureHandler(myAuthHandle) // 登录失败逻辑处理

.and()
.logout()   //开启注销
.permitAll()    //允许所有人访问
.logoutSuccessHandler(myAuthHandle) //注销逻辑处理
.deleteCookies("JSESSIONID")    //删除cookie

.and().exceptionHandling()
.accessDeniedHandler(myAuthHandle)    //权限不足的时候的逻辑处理
.authenticationEntryPoint(myAuthHandle)  //未登录是的逻辑处理
```

各阶段处理器列举几个

| 阶段       | 实现接口                     | 重写方法                   |
| ---------- | ---------------------------- | -------------------------- |
| 登录成功时 | AuthenticationSuccessHandler | onAnthenticationSuccesss() |
| 登录失败时 | AuthenticationFailureHandler | onAuthenticationFailure()  |
| 注销成功时 | LogoutSuccessHandler         | onLogoutSuccess()          |
| 没有权限时 | AccessDeniedHandler          | handle()                   |
| 没有登录时 | AuthenticationEntryPoint     | commence()                 |

向上面不是一个前后端分离的例子，如果前后的分离结合这些自定义处理器就可以实现，比如

```java
@Override
public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException {
    //登录成功时返回给前端的数据
    ApiResult result = ApiResult.result(ApiCode.SUCCESS, "登录成功", "");
    response.setContentType("application/json;charset=utf-8");
    response.getWriter().write(JSON.toJSONString(result));
}
```

```java
@Override
public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException {
    ApiResult result = null;
    if(e instanceof UsernameNotFoundException){
        result = ApiResult.result(ApiCode.FAIL_AUTH,"用户名找不到","");
    }else if(e instanceof BadCredentialsException){
        result = ApiResult.result(ApiCode.FAIL_AUTH,"密码错误","");
    }else {
        result = ApiResult.fail(e.getMessage());
    }
    //处理编码方式，防止中文乱码的情况
    response.setContentType("text/json;charset=utf-8");
    //返回给前台
    response.getWriter().write(JSON.toJSONString(result));
}
```



就不一一举例了，实现接口重写好方法，在SecurityConfiguration进行配置即可



## 2.4 图形验证码

```tex
图形验证码一般是防止恶意，人眼看起来都费劲，何况是机器。不少网站为了防止用户利用机器人自动注册、登录、灌水，都采用了验证码技术。所谓验证码，就是将一串随机产生的数字或符号，生成一幅图片， 图片里加上一些干扰,。也有目前需要手动滑动的图形验证码，这种可以有专门去做的第三方平台.：比如极验(https://www.geetest.com/), 这里介绍的主要针对图形验证码。
```

Spring Security添加验证码大致可以分为三个步骤：

1. 根据随机数生成验证码图片
2. 将验证码图片显示到登录页面
3. 认证流中加入验证码校验

Spring Security的认证校验是由UsernamePasswordAuthenticationFilter过滤器完成的，所以我们的验证码校验逻辑应该在这个过滤器之前。验证码通过后才能到后续的操作. 流程如下:

![image-20220624173343774]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220624173343774.png)

代码实现：

* 自定义验证码过滤器ValidateCodeFilter

  ```java
  package cn.yournotes.config;
  
  import cn.yournotes.controller.ValidateCodeController;
  import cn.yournotes.exception.ValidateCodeException;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.data.redis.core.StringRedisTemplate;
  import org.springframework.stereotype.Component;
  import org.springframework.util.StringUtils;
  import org.springframework.web.bind.ServletRequestBindingException;
  import org.springframework.web.filter.OncePerRequestFilter;
  
  import javax.servlet.FilterChain;
  import javax.servlet.ServletException;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;
  
  /**
   * 验证码验证filter 需要继承OncePerRequestFilter确保在一次请求只通过一次filter，而不
   * 需要重复执行
   */
  @Component
  public class ValidateCodeFilter extends OncePerRequestFilter {
      @Autowired
      MyAuthHandle myAuthHandle;
      @Autowired
      StringRedisTemplate stringRedisTemplate;
  
      @Override
      protected void doFilterInternal(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, FilterChain filterChain) throws ServletException, IOException {
          // 判断是否是登录请求,只有登录请求才去校验验证码
          if (httpServletRequest.getRequestURI().equals("/login") && httpServletRequest.getMethod().equalsIgnoreCase("post")) {
              try {
                  validate(httpServletRequest);
              } catch (ValidateCodeException e) {
                  myAuthHandle.onAuthenticationFailure(httpServletRequest, httpServletResponse, e);
                  return;
              }
          }
          //如果不是登录请求，直接调用后面的过滤器链
          filterChain.doFilter(httpServletRequest, httpServletResponse);
      }
  
      private void validate(HttpServletRequest request) throws ValidateCodeException {
          //获取ip
          String remoteAddr = request.getRemoteAddr();
          //拼接redis的key
          String redisKey = ValidateCodeController.REDIS_KEY_IMAGE_CODE + "-" + remoteAddr;
          //从redis中获取imageCode
          String redisImageCode = stringRedisTemplate.boundValueOps(redisKey).get();
          String imageCode = request.getParameter("imageCode");
          if (!StringUtils.hasText(imageCode)) {
              throw new ValidateCodeException("验证码的值不能为空！");
          }
          if (redisImageCode == null) {
              throw new ValidateCodeException("验证码已过期！");
          }
          if (!redisImageCode.equals(imageCode)) {
              throw new ValidateCodeException("验证码不正确！");
          }
          // 从redis中删除imageCode
          stringRedisTemplate.delete(redisKey);
      }
  }
```

* 自定义异常

  ```java
  public class ValidateCodeException extends AuthenticationException {
      public ValidateCodeException(String msg){
          super(msg);
      }
  }
```

* 验证码生成

  ```java
  import cn.yournotes.domain.ImageCode;
  import org.springframework.beans.factory.annotation.Autowired;
  import org.springframework.data.redis.core.StringRedisTemplate;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  import javax.imageio.ImageIO;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.awt.*;
  import java.awt.image.BufferedImage;
  import java.io.IOException;
  import java.util.Random;
  import java.util.concurrent.TimeUnit;
  
  /**
   * 处理生成验证码的请求
   */
  @RestController
  @RequestMapping("/code")
  public class ValidateCodeController {
  
      public final static String REDIS_KEY_IMAGE_CODE = "REDIS_KEY_IMAGE_CODE";
      public final static int expireIn = 60;  // 验证码有效时间 60s
  
      //使用sessionStrategy将生成的验证码对象存储到Session中，并通过IO流将生成的图片输出到登录页面上。
      @Autowired
      public StringRedisTemplate stringRedisTemplate;
  
      @RequestMapping("/image")
      public void createCode(HttpServletRequest request, HttpServletResponse response) throws IOException {
          //获取访问IP
          String remoteAddr = request.getRemoteAddr();
          //生成验证码对象
          ImageCode imageCode = createImageCode();
          //生成的验证码对象存储到redis中 KEY为REDIS_KEY_IMAGE_CODE+IP地址
          stringRedisTemplate.boundValueOps(REDIS_KEY_IMAGE_CODE + "-" + remoteAddr)
                  .set(imageCode.getCode(), expireIn, TimeUnit.SECONDS);
          //通过IO流将生成的图片输出到登录页面上
          ImageIO.write(imageCode.getImage(), "jpeg", response.getOutputStream());
      }
  
      /**
       * 用于生成验证码对象
       *
       * @return
       */
      private ImageCode createImageCode() {
  
          int width = 100;    // 验证码图片宽度
          int height = 36;    // 验证码图片长度
          int length = 4;     // 验证码位数
          //创建一个带缓冲区图像对象
          BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
          //获得在图像上绘图的Graphics对象
          Graphics g = image.getGraphics();
  
          Random random = new Random();
  
          //设置颜色、并随机绘制直线
          g.setColor(getRandColor(200, 250));
          g.fillRect(0, 0, width, height);
          g.setFont(new Font("Times New Roman", Font.ITALIC, 20));
          g.setColor(getRandColor(160, 200));
          for (int i = 0; i < 155; i++) {
              int x = random.nextInt(width);
              int y = random.nextInt(height);
              int xl = random.nextInt(12);
              int yl = random.nextInt(12);
              g.drawLine(x, y, x + xl, y + yl);
          }
  
          //生成随机数 并绘制
          StringBuilder sRand = new StringBuilder();
          for (int i = 0; i < length; i++) {
              String rand = String.valueOf(random.nextInt(10));
              sRand.append(rand);
              g.setColor(new Color(20 + random.nextInt(110), 20 + random.nextInt(110), 20 + random.nextInt(110)));
              g.drawString(rand, 13 * i + 6, 16);
          }
          g.dispose();
          return new ImageCode(image, sRand.toString());
      }
  
      /**
       * 获取随机演示
       *
       * @param fc
       * @param bc
       * @return
       */
      private Color getRandColor(int fc, int bc) {
          Random random = new Random();
          if (fc > 255) {
              fc = 255;
          }
          if (bc > 255) {
              bc = 255;
          }
          int r = fc + random.nextInt(bc - fc);
          int g = fc + random.nextInt(bc - fc);
          int b = fc + random.nextInt(bc - fc);
          return new Color(r, g, b);
      }
  
  }
  ```

* security配置

  需要配置两个东西，一个是在UsernamePasswordAuthenticationFilter之前需要加一个过滤器进行验证码处理，第二是放行验证码接口让前端请求显示。`记得把验证码接口地址放到html图片验证码处、redis也配置好`

  ```java
  http.addFilterBefore(validateCodeFilter,UsernamePasswordAuthenticationFilter.class);
  http.formLogin()                   // 开启formLogin认证
      .loginPage("/toLoginPage")  // 自定义登录页
      .loginProcessingUrl("/login") // 登录接口url
      .usernameParameter("username").passwordParameter("password") //自定义表单name
      .successForwardUrl("/")    //登录成功跳转
      .successHandler(myAuthHandle)
      .failureHandler(myAuthHandle)
      .and().logout().logoutUrl("/logout")//设置退出url
      .logoutSuccessHandler(myAuthHandle)//自定义退出处理
      .and().authorizeRequests()
      .antMatchers("/toLoginPage","/code/**").permitAll()
  ```




## 2.5 session管理

Spring Security可以与Spring Session库配合使用，只需要做一些简单的配置就可以实现一些功能，如(会话过期、一个账号只能同时在线一个、集群session等)

### 2.5.1 会话超时

1. 配置session会话超时时间，默认为30分钟，我们设置短些进行测试，但最小只能设置60s

   ```properties
   # session设置
   # 当session超时后, 默认跳转到登录页面.
   # 配置session超时时间
   server.servlet.session.timeout=60
   ```

2. 自定义设置session超时后地址

   设置session管理和失效后跳转地址

   ```java
   // 设置session管理
   // session无效后跳转的路径,默认是登录页面。
   // 前后的分离的话就不需要配置这些跳转的相关配置了
   http.sessionManagement().invalidSessionUrl("/toLoginPage")
   ```



### 2.5.2 并发控制

​	并发控制即同一个账号同时在线个数,同一个账号同时在线个数如果设置为1表示，该账号在同一时间内只能有一个有效的登录，如果同一个账号又在其它地方登录，那么就将上次登录的会话过期，即后面的登录会踢掉前面的登录

1. 修改超时时间

   ```properties
   #session设置
   #配置session超时时间
   server.servlet.session.timeout=600
   ```

2. 设置最大会话数量

   ```java
   http.sessionManagement()//设置session管理
       .invalidSessionUrl("/toLoginPage")// session无效后跳转的路径, 默认是登录页面
       .maximumSessions(1)//设置session最大会话数量 ,1同一时间只能有一个用户登录
       .expiredUrl("/toLoginPage");//设置session过期后跳转路径
   ```

   使用第二个浏览器再进行登录时 ，第一个浏览器会失去登录信息访问会进入登录页

   ![image-20220626091030598]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220626091030598.png)

3. 阻止用户第二次登录

   sessionManagement也可以配置 maxSessionsPreventsLogin：boolean值，当达到maximumSessions设置的最大会话个数时阻止登录。

   ```java
   http.sessionManagement()//设置session管理
       .invalidSessionUrl("/toLoginPage")// session无效后跳转的路径, 默认是登录页面
       .maximumSessions(1)//设置session最大会话数量 ,1同一时间只能有一个用户登录
       .expiredUrl("/toLoginPage");//设置session过期后跳转路径
   ```



### 2.5.3 集群session

	实际场景中一个服务会至少有两台服务器在提供服务，在服务器前面会有一个nginx做负载均衡，用户访问nginx，nginx再决定去访问哪一台服务器。当一台服务宕机了之后，另一台服务器也可以继续提供服务，保证服务不中断。如果我们将session保存在Web容器(比如tomcat)中，如果一个用户第一次访问被分配到服务器1上面需要登录，当某些访问突然被分配到服务器二上，因为服务器二上没有用户在服务器一上登录的会话session信息，服务器二还会再次让用户登录，用户已经登录了还让登录就感觉不正常了。

![image-20220626091915015]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220626091915015.png)

	解决这个问题的思路是用户登录的会话信息不能再保存到Web服务器中，而是保存到一个单独的库
(redis、mongodb、jdbc等)中，所有服务器都访问同一个库，都从同一个库来获取用户的session信
息，如用户在服务器一上登录，将会话信息保存到库中，用户的下次请求被分配到服务器二，服务器二
从库中检查session是否已经存在，如果存在就不用再登录了，可以直接访问服务了。

1. 引入依赖

   ```xml
   <!-- 基于redis实现session共享 -->
   <dependency>
       <groupId>org.springframework.session</groupId>
       <artifactId>spring-session-data-redis</artifactId>
   </dependency>
   ```

2. 设置session存储类型

   ```properties
   #使用redis共享session
   spring.session.store-type=redis
   ```

3. 测试

   ```
   1. 设置不同端口启动两个服务
   2. 访问第一个服务进行登录，第二服务就可以直接访问
   ```



## 2.6 csrf防护机制

### 2.6.1 什么是csrf?

​	CSRF（Cross-site request forgery），中文名称：跨站请求伪造

​	你这可以这么理解CSRF攻击：攻击者盗用了你的身份，以你的名义发送恶意请求。CSRF能够做的事情包括：以你名义发送邮件，发消息，盗取你的账号，甚至于购买商品，虚拟货币转账......造成的问题包括：个人隐私泄露以及财产安全。

​	CSRF这种攻击方式在2000年已经被国外的安全人员提出，但在国内，直到06年才开始被关注，08年，国内外的多个大型社区和交互网站分别爆出CSRF漏洞，如：NYTimes.com（纽约时报）、Metafilter（一个大型的BLOG网站），YouTube和百度HI......而现在，互联网上的许多站点仍对此毫无防备，以至于安全业界称CSRF为“沉睡的巨人”。

![image-20220626093311778]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220626093311778.png)

从上图可以看出，要完成一次CSRF攻击，受害者必须依次完成三个步骤：

1. 登录受信任网站A，并在本地生成Cookie。
2. 在不登出A的情况下，访问危险网站B。
3. 触发网站B中的一些元素



### 2.6.3 CSRF的防御策略

在业界目前防御 CSRF 攻击主要有三种策略：验证 HTTP Referer 字段；在请求地址中添加 token  并验证；在 HTTP 头中自定义属性并验证。

1. 验证 HTTP Referer 字段

   根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址。在通常情况下，访问一个安全受限页面的请求来自于同一个网站，在后台请求验证其 Referer 值，如果是以自身安全网站开头的域名，则说明该请求是是合法的。如果 Referer 是其他网站的话，则有可能是黑客的 CSRF 攻击，拒绝该请求。

2. 在请求地址中添加 token 并验证

   CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于 cookie 中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。

3. 在 HTTP 头中自定义属性并验证

   这种方法也是使用 token 并进行验证，和上一种方法不同的是，这里并不是把 token 以参数的形式置于 HTTP 请求之中，而是把它放到 HTTP 头中自定义的属性里。



### 2.6.4 Security中的csrf防御机制

`org.springframework.security.web.csrf.CsrfFilter`

> csrf又称跨站请求伪造，SpringSecurity会对所有post请求验证是否包含系统生成的csrf的token信息，如果不包含，则报错。起到防止csrf攻击的效果。(1. 生成token 2.验证token)

1. 开启csrf防护

   ```java
   //开启csrf防护, 可以设置哪些不需要防护
   http.csrf().ignoringAntMatchers("/user/xxx");
   ```

2. 页面需要添加token值

   ```java
   <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}"/>
   ```


这样使用的前提是前后端不分离的项目，在同一个session里面取toke，每次都是随机生成的因此无法伪造。如果前后端分离token就又需要远程请求获取而不是直接在session里面取这又会不安全，如果是前后端分离该怎么设计这个暂时不探讨。



## 2.7 跨域与CORS

### 2.7.1 跨域

​	跨域，实质上是浏览器的一种保护处理。如果产生了跨域，服务器在返回结果时就会被浏览器拦截(注意：此时请求是可以正常发起的，只是浏览器对其进行了拦截)，导致响应的内容不可用. 产生跨域的几种情况有一下:

* 协议不同（http/https）
* 域名不同 
* 端口号不同

### 2.7.2 解决跨域

1. JSONP

   浏览器允许一些带src属性的标签跨域，也就是某些标签属性上写url地址是不会跨域产生跨域问题的

2. CORS解决跨域

   CORS是一个W3C标准，全程是“跨域资源共享”（Cross-origin resource sharing）. CORS需要浏览器和服务器同时支持。目前所有浏览器都支持该功能，IE浏览器不能低于IE10 浏览器在发起真正的请求之前，会发起一个OPTIONS类型的预检请求，用于请求服务器是否允许跨域，在得到许可的情况下才会发起请求。



### 2.7.3 基于SpringSecurity的CORS支持

1. 声明跨域配置源

   ```java
   /**
    * 跨域配置信息源
    *
    * @return
    */
   public CorsConfigurationSource corsConfigurationSource() {
       CorsConfiguration corsConfiguration = new CorsConfiguration();
       // 设置允许跨域的站点
       corsConfiguration.addAllowedOrigin("*");
       // 设置允许跨域的http方法
       corsConfiguration.addAllowedMethod("*");
       // 设置允许跨域的请求头
       corsConfiguration.addAllowedHeader("*");
       // 允许带凭证
       corsConfiguration.setAllowCredentials(true);
       // 对所有的url生效
       UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
       source.registerCorsConfiguration("/**", corsConfiguration);
       return source;
   }
   ```

2. 开启跨域支持

   ```java
   //允许跨域
   http.cors().configurationSource(corsConfigurationSource());
   ```



# 三、SpringSecurity授权

## 3.1 授权简介

​	在第二部分中说明的都是用户认证, 不管是用户名密码,还是图形验证码等,最终的目的都是一个:让系统知道你到底是谁在访问你的系统, 解决的问题是, 你是谁? 这部分主要讲解你能在系统中做什么事情, 针对这个有的叫做: 授权, 有的叫做:鉴权, 还有叫权限控制. 最终的目的就是你能在系统中能过做什么?

### 3.1.1 SpringSecurity对授权的定义

![image-20220627101902078]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220627101902078.png)

安全控制问题其实就是控制是否能访问url



### 3.1.2 Spring Security授权原理

![image-20220627102053058]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220627102053058.png)

在我们应用系统里面，如果想要控制用户权限，需要有两部分数据

1. 系统配置信息数据：记录系统里面有哪些URL，每个URL需要怎样的权限
2. 用户权限信息：请求用户拥有的权限。

系统用户发送一个请求：系统配置信息和用户权限信息作对比，如果对比成功则允许访问。

当一个系统授权规则比较简单基本不变的时候，系统的权限配置可以写到我们的代码当中去。比如前台门户网站等权限比较单一，可以使用简单的授权配置即可完成，如果权限复杂比如办公OA，电商后台管理系统等就不能写在代码里面了。需要设计RBAC权限模型设计。

## 3.2 SpringSecurity授权

### 3.2.1 内置权限表达式

Spring Security 使用Spring EL来支持，主要用于Web访问和方法安全上, 可以通过表达式来判断是否具有访问权限. 下面是Spring Security常用的内置表达式. ExpressionUrlAuthorizationConfigurer定义了所有的表达式

| 表达式                                 | 说明                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| permitAll                              | 任何人都允许访问                                             |
| denyAll                                | 任何人都不允许访问                                           |
| anonymous                              | 匿名用户允许访问                                             |
| rememberMe                             | 记住我的用户可以访问                                         |
| authenticated                          | 指定任何经过身份验证的用户都允许访问，不包括anonymous        |
| fullyAuthenticated                     | 指定由经过身份验证的用户允许访问,不包含anonymous和rememberMe |
| hasRole(role)                          | 指定需要特定的角色的用户允许访问, 会自动在角色前面插入'ROLE_' |
| hasAnyRole([role1,role2])              | 指定需要任意一个的角色的用户允许访问, 会自动在角色前面插入'ROLE_' |
| hasAuthority(authority)                | 需要有特定的权限允许访问                                     |
| hasAnyAuthority([authority,authority]) | 需要有其中任意一个的权限允许访问                             |
| hasIpAddress(ip)                       | 需要特定的IP地址可以访问                                     |



### 3.2.2 URL安全表达式

基于web访问使用表达式保护url请求路径.

1. 设置url访问权限

   ```java
   // 设置/user/** 访问需要ADMIN角色
   http.authorizeRequests().antMatchers("/user/**").hasRole("ADMIN");
   // 设置/product/** 访问需要PRODUCT角色和IP地址为127.0.0.1. hasAnyRole("PRODUCT,ADMIN")
   http.authorizeRequests().antMatchers("/product/**").access("hasAnyRole('ADMIN,PRODUCT') and hasIpAddress('127.0.0.1')");
   // 设置自定义权限不足信息.
   http.exceptionHandling().accessDeniedHandler(myAuthHandle);
   ```

2. 自定义权限不足处理`(在之前上面的表格列举过了，实现AccessDeniedHandler重写handle方法即可,在上一步配上自己的实现类即可）`

   ```java
   @Override
   public void handle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AccessDeniedException e) throws IOException, ServletException {
       httpServletResponse.setStatus(HttpServletResponse.SC_FORBIDDEN);
       httpServletResponse.setContentType("text/html;charset=UTF-8");
       httpServletResponse.getWriter().write("权限不足，请联系管理员!");
   }
   ```

3. 设置用户对应的角色权限`在自定义的UserDetailService当中获取Security用户实体的过程里面给用户去加权限`

   ```java
   // 先声明一个权限集合,构造函数必须传值且不能为null,传个空集合即可
   Collection<GrantedAuthority> authorities = new ArrayList<>();
   if ("admin".equalsIgnoreCase(user.getUsername())) {
       authorities.add(new SimpleGrantedAuthority("ROLE_ADMIN"));
   } else {
       authorities.add(new SimpleGrantedAuthority("ROLE_PRODUCT"));
   }
   ```



### 3.2.3 在web安全表达式中引用自定义Bean授权

1. 自定义授权类

   ```java
   @Component
   public class MyAuthService {
       /**
        * 检查用户是否有对应的访问权限
        *
        * @param authentication 登录用户
        * @param request 请求对象
        * @return
        */
       public boolean check(Authentication authentication, HttpServletRequest request) {
           User user = (User) authentication.getPrincipal();
           // 获取用户所有权限
           Collection<GrantedAuthority> authorities = user.getAuthorities();
           // 获取用户名
           String username = user.getUsername();
           // 如果用户名为admin,则不需要认证
           if (username.equalsIgnoreCase("admin")) {
               return true;
           } else {
               // 循环用户的权限, 判断是否有ROLE_ADMIN权限, 有返回true
               for (GrantedAuthority authority : authorities) {
                   String role = authority.getAuthority();
                   if ("ROLE_ADMIN".equals(role)) {
                       return true;
                   }
               }
           }
           return false;
       }
   }
   ```

2. 配置类

   ```java
   //使用自定义Bean授权
   http.authorizeRequests()
       .antMatchers("/user/**")
       .access("@myAuthService.check(authentication,request)");
   ```

3. 若授权时还需要对路径变量需判断

   ```java
   /**
    * 检查用户是否有对应的访问权限
    *
    * @param authentication 登录用户
    * @param request 请求对象
    * @param id 参数ID
    * @return
    */
   public boolean check(Authentication authentication, HttpServletRequest request, Integer id) {
       // 举个例子
       if (id > 10) {
           return false;
       }
       return true;
   }
   ```

   ```java
   //使用自定义Bean授权,并携带路径参数
   http.authorizeRequests()
       .antMatchers("/user/delete/{id}")
       .access("@myAuthorizationService.check(authentication,request,#id)");
   ```



### 3.2.4 Method安全表达式

向上面那样配的话，不同的接口不同的权限那么就要在http.authorizeRequests配置很多比较复杂，spring security提供了4种注解分别是`@PreAuthorize` `@PostAuthorize` `@PreFilter` `@PostFilter`

1. 开启方法级别的注解配置

   在security配置类中添加注解

   ```java
   @EnableGlobalMethodSecurity(prePostEnabled = true)//开启注解支持
   @Configuration
   public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
   ```

2. 在方法上使用注解`使用这样的方式就比上面少了secuirty配置类当中的配置和自定义的授权类`

   `ProAuthorize` 注解适合进入方法前进行权限验证

   ```java
   @PreAuthorize("hasRole('ADMIN')")
   @RequestMapping("/findAll")
   public String findAll(Model model) {
       List<UserVo> userList = userService.list();
       model.addAttribute("userList", userList);
       return "user_list";
   }
   
   @PreAuthorize("#id < 10")
   @RequestMapping("/update/{id}")
   public String update(@PathVariable Integer id, Model model) {
       UserVo user = userService.getById(id);
       model.addAttribute("user", user);
       return "user_update";
   }
   ```

   `@PostAuthorize:` 在方法执行后在进行权限验证，适合验证带有返回值的权限，Spring EL提供返回对象能够在表达式语言中获取到返回对象`returnObject`

   ```java
   @PostAuthorize("returnObject.username==authentication.principal.username")
   @GetMapping("/{id}")
   @ResponseBody
   public UserVo getById(@PathVariable Integer id) {
       //获取认证信息
       Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
       // 判断认证信息是否来源于RememberMe
       if (RememberMeAuthenticationToken.class.isAssignableFrom(authentication.getClass())) {
           throw new RememberMeAuthenticationException("认证信息来源于 RememberMe,请重新登录");
       }
       UserVo user = userService.getById(id);
       return user;
   }
   ```

   `@PreFilter`: 可以用来对集合类型的参数进行过滤，将不符合条件的元素剔除集合

   ```java
   // 过滤出偶数的，也就是剔除奇数的
   @PreFilter(filterTarget = "ids",value = "filterObject%2==0")
   @GetMapping("/delByIds")
   public String delByIds(@RequestParam(value = "id") List<Integer> ids) {
       for (Integer id : ids) {
           System.out.println(id);
       }
       return "redirect:/user/findAll";
   }
   ```

   `@PostFilter`: 可以用来对集合类型的返回值进行过滤，将不符合的元素剔除集合

   ```java
   // 返回列表元素ID为偶数的保留
   @PostFilter("filterObject.id%2 == 0")
   @RequestMapping("/findAllTOJson")
   @ResponseBody
   public List<UserVo> findAllTOJson() {
       List<UserVo> userList = userService.list();
       return userList;
   }
   ```

   ![image-20220630131654436]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220630131654436.png)



## 3.3 基于数据库的RBAC数据模型

​	回顾上面内容，第一在自定义产生secuirty用户的地方，我们去给用户添加权限写死或者在数据库查出来放进去。第二在用户访问资源时权限的校验通过配置资源URL加自定义授权器进行判断或者直接在资源上使用注解让security去判断用户和资源。这样的方式仅仅适合权限少的情况，如果系统权限不是只有一个两个权限就需要设计权限模型了。

​	我们开发一个系统，必然面临权限控制的问题，不同的用户具有不同的访问、操作、数据权限。形成理论的权限控制模型有：自主访问控制（DAC: Discretionary Access Control）、强制访问控制（MAC: Mandatory Access Control）、基于属性的权限验证（ABAC:Attribute-Based Access Control）等。最常被开发者使用也是相对易用、通用的就是RBAC权限模型（Role-Based Access Control）

### 3.3.1 RBAC权限模型简介

RBAC权限模型（Role-Based Access Control）即：基于角色的权限控制。模型中有几个关键的术语：

* 用户：系统接口及访问的操作者
* 权限：能够访问某接口或者做某操作的授权资格
* 角色：具有一类一批权限的总称

RBAC权限模型核心授权逻辑如下：

* 某用户是什么角色？
* 某用户具有什么权限？
* 通过角色对应的权限推导出用户权限

### 3.3.2 RBAC的演化进程

1. 用户与权限直接关联

   ![image-20220630133551238]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220630133551238.png)

   想到权限控制，人们最先想到的一定是用户与权限直接关联的模式，简单地说就是：某个用户具有某些权限。如图：

   * 张三具有所有权限它可能是一个超级管理员
   * 李四,王五 具有添加商品和审核商品的权限有可能是一个普通业务员

   这种模型能够清晰的表达用户与权限之间的关系，足够简单。但同时也存在问题：

   * 现在用户是张三、李四，王五以后随着人员增加，每一个用户都需要重新授权
   * 操作人员的他的权限发生变更后,需要对每个一个用户重新授予新的权限

2. 用户角色关联

   ![image-20220630133937458]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220630133937458.png)

   这样只需要维护角色和权限之间的关系就可以了. 如果业务员的权限发生变更, 只需要变动业务员角色和权限之前的关系进行维护就可以了. 用户和权限就分离开来了. 如下图

   ![image-20220630134028657]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220630134028657.png)

   

   

### 3.3.3 基于RBAC设计权限表结构

   * 一个用户有一个或多个角色
   * 一个角色可以被多个用户拥有
   * 一个角色有多种权限
   * 一个权限属于多个角色

![image-20220630134241850]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220630134241850.png)

   

### 3.3.4 基于Spring Security实现RBAC权限管理

1. 动态查询数据库中用户应用的权限

   ```java
   /**
    * 根据用户ID查询权限
    *
    * @param id
    * @return
    */
   @Select("SELECT p.*  FROM t_permission p,t_role_permission rp,t_role r,t_user_role ur,t_user u " +
           "WHERE p.id = rp.PID AND rp.RID = r.id AND r.id = ur.RID AND ur.UID = u.id AND u.id =#{id}")
   List<Permission> findByUserId(Integer id);
   ```

2. 给登录用户授权

   ```java
   // 先声明一个权限集合, 因为构造方法里面不能传入null
   Collection<GrantedAuthority> authorities = new ArrayList<>();
   // 查询用户对应所有权限
   List<Permission> permissions = permissionService.findByUserId(user.getId());
   for (Permission permission : permissions) {
       // 授权
       authorities.add(new SimpleGrantedAuthority(permission.getPermissionTag()));
   }
   ```

3. 设置访问权限

   ```java
   // 查询数据库所有权限列表
   List<Permission> permissions = permissionService.list();
   for (Permission permission : permissions) {
       //添加请求权限
       http.authorizeRequests().antMatchers(permission.getPermissionUrl()).hasAuthority(permission.getPermissionTag());
   }
   // 设置自定义权限不足信息.
   http.exceptionHandling().accessDeniedHandler(myAuthHandle);
   ```

这样就完成了动态的权限管理，第一部分在产生Security用户实体的service当中进行关联查询给当前用户填入权限。第二部分配置各资源权限：从数据库获取权限信息并配置资源path与对应权限。

总而言之如果是简单的认证限制就可以之前想之前一样简单写死配置在SecurityConfiguration或者配置在各个方法。否则就可以使用当前的设计进行权限管理。



## 3.4 基于页面标签的权限控制

​	`简单记下吧，也不常用了`

​	在jsp页面或者thymeleaf模板页面中我们可以使用spring security提供的权限标签来进行权限控制.要想使用thymeleaf为SpringSecurity提供的标签属性，首先需要引入thymeleaf-extras-springsecurity依赖支持。

1. 在pom 文件中的引入springsecurity的标签依赖thymeleaf-extras-springsecurity5

   ```xml
   <!--添加thymeleaf为SpringSecurity提供的标签 依赖 -->
   <dependency>
       <groupId>org.thymeleaf.extras</groupId>
       <artifactId>thymeleaf-extras-springsecurity5</artifactId>
       <version>3.0.4.RELEASE</version>
   </dependency>
   ```

2. 在html文件里面声明使用

   ```html
   <!DOCTYPE html>
   <html xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
   ```



### 3.4.1 常用SpringSecurity的标签属性介绍

>判断用户是否已经登陆认证，引号内的参数必须是isAuthenticated()，sec:authorize="isAuthenticated()"
>
>获得当前用户的用户名，引号内的参数必须是name，sec:authentication=“name”
>
>判断当前用户是否拥有指定的权限。引号内的参数为权限的名称，sec:authorize=“hasRole(‘role’)”



### 3.4.2 SpringSecurity标签的使用

```html
<html>
 <head></head>
 <body>
  <div class="leftnav"> 
   <div class="leftnav-title"> 
    <div sec:authorize="isAuthenticated()"> 
     <span sec:authentication="name"></span> 
     <img src="images/y.jpg" class="radius-circle rotate-hover" height="50" alt="" />
    </div> 
   </div> 
   <div sec:authorize="hasAuthority('user:findAll')"> 
    <h2><span class="icon-user"></span>系统管理</h2> 
    <ul style="display:block"> 
     <li><a href="/user/findAll" target="right"><span class="icon-caretright">
        <!--
span-->用户管理</span></a></li> 
     <li><a href="javascript:void(0)" onclick="toCors()" target="right"> <span class="icon-caret-right"></span>跨域测试</a></li> 
    </ul> 
   </div> 
   <div sec:authorize="hasAuthority('product:findAll')"> 
    <h2><span class="icon-pencil-square-o"></span>数据管理</h2> 
    <ul> 
     <li><a href="/product/findAll" target="right"><span class="iconcaret-
right"></span>商品管理</a></li> 
    </ul> 
   </div> 
  </div>
 </body>
</html>
```



# 四、源码分析

## 4.1 过滤器链加载源码

### 4.1.1 过滤器加载流程分析

在上面第二部分的时候说springSecurity中主要功能是由过滤器链来完成的, 那么spring boot 是如何加载这个流程的呢?

![image-20220701113050215]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220701113050215.png)

### 4.1.2 过滤器链加载流程源码分析

1. spring boot启动自动配置中会加载spring.factories文件, 在文件中有对应针对Spring Security的过滤器链的配置信息

   ![image-20220705151908666]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220705151908666.png)

   ```properties
   # 安全过滤器自动配置
   org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration
   ```

2. SecurityFilterAutoConfiguration类

   ```java
   @Configuration(proxyBeanMethods = false)
   @ConditionalOnWebApplication(type = Type.SERVLET)
   @EnableConfigurationProperties({SecurityProperties.class}) //Security配置类
   @ConditionalOnClass({AbstractSecurityWebApplicationInitializer.class, SessionCreationPolicy.class})
   @AutoConfigureAfter({SecurityAutoConfiguration.class}) //当前类加载完成后 加载SecurityAutofiguration
   public class SecurityFilterAutoConfiguration {
   	...   
   }
   ```

   主要两个部分第一加载security配置，当中会有两个东西一个一个是默认用户信息、一个是过滤器链的组建

   ![image-20220705153332732]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220705153332732.png)

   ![image-20220705154304776]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220705154304776.png)

   第二就是随后去加载的`SecurityAutoConfiguration`，其中导入了web的安全配置`WebSecurityEnablerConfiguration.class`，如下

3. SecurityAutoConfiguration类

   ```java
   @Configuration(proxyBeanMethods = false)
   @ConditionalOnClass({DefaultAuthenticationEventPublisher.class})
   @EnableConfigurationProperties({SecurityProperties.class})
   @Import({SpringBootWebSecurityConfiguration.class, WebSecurityEnablerConfiguration.class, SecurityDataConfiguration.class})//web安全启用配置
   public class SecurityAutoConfiguration {
       public SecurityAutoConfiguration() {
       }
   
       @Bean
       @ConditionalOnMissingBean({AuthenticationEventPublisher.class})
       public DefaultAuthenticationEventPublisher authenticationEventPublisher(ApplicationEventPublisher publisher) {
           return new DefaultAuthenticationEventPublisher(publisher);
       }
   }
   ```

4. WebSecurityEnablerConfiguration类

   ```java
   @Configuration(proxyBeanMethods = false)
   @ConditionalOnBean({WebSecurityConfigurerAdapter.class})
   @ConditionalOnMissingBean(name = {"springSecurityFilterChain"})
   @ConditionalOnWebApplication(type = Type.SERVLET)
   @EnableWebSecurity
   public class WebSecurityEnablerConfiguration {
       public WebSecurityEnablerConfiguration() {
       }
   }
   ```

   ![image-20220705154834123]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220705154834123.png)

   @EnableWebSecurity注解有两个作用：1: 加载了WebSecurityConfiguration配置类, 配置安全认证策略。2: 加载了AuthenticationConfiguration 配置的认证信息。

5. WebSecurityConfiguration类

   在webSecurityConfiguration当中生成了过滤器链

   ```java
   @Configuration(proxyBeanMethods = false)
   public class WebSecurityConfiguration implements ImportAware, BeanClassLoaderAware {     ...       
       // springSecurity过滤器声明
       @Bean(name = {"springSecurityFilterChain"})
       public Filter springSecurityFilterChain() throws Exception {
           boolean hasConfigurers = this.webSecurityConfigurers != null && !this.webSecurityConfigurers.isEmpty();
           if (!hasConfigurers) {
               WebSecurityConfigurerAdapter adapter = (WebSecurityConfigurerAdapter)this.objectObjectPostProcessor.postProcess(new WebSecurityConfigurerAdapter() {
               });
               this.webSecurity.apply(adapter);
           }
   
           return (Filter)this.webSecurity.build();
       }
       ...
   }
       
   ```



## 4.2 认证流程源码

### 4.2.1 认证流程分析

​	在整个过滤器链中, UsernamePasswordAuthenticationFilter是来处理整个用户认证的流程的, 所以下面我们主要针对用户认证来看下源码是如何实现的?

![image-20220701115926889]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220701115926889.png)

### 4.2.2 认证流程源码跟踪

* UsernamePasswordAuthenticationFilter

  ```java
  public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
      // 1.检查是否是Post请求
      if (this.postOnly && !request.getMethod().equals("POST")) {
          throw new AuthenticationServiceException("Authentication method not supported: " + request.getMethod());
      } else {
          // 2.获取用户名密码
          String username = this.obtainUsername(request);
          String password = this.obtainPassword(request);
          if (username == null) {
              username = "";
          }
  
          if (password == null) {
              password = "";
          }
          username = username.trim();
          // 3.创建AuthenticationToken，此时是未认证状态
          UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
          this.setDetails(request, authRequest);
          // 4.调用AuthenticationManager进行认证
          return this.getAuthenticationManager().authenticate(authRequest);
      }
  }
  ```

  UsernamePasswordAuthenticationToken

  ```java
  public UsernamePasswordAuthenticationToken(Object principal, Object credentials) {
      super((Collection)null);
      this.principal = principal;
      this.credentials = credentials;
      this.setAuthenticated(false);
  }
  ```

* AuthenticationManager-->ProviderManager-->AbstractUserDetailsAuthenticationProvider

  ```java
  public Authentication authenticate(Authentication authentication) throws AuthenticationException {
      Assert.isInstanceOf(UsernamePasswordAuthenticationToken.class, authentication, () -> {
          return this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.onlySupports", "Only UsernamePasswordAuthenticationToken is supported");
      });
      // 1.获取用户名
      String username = this.determineUsername(authentication);
      // 2.尝试从缓存中提取
      boolean cacheWasUsed = true;
      UserDetails user = this.userCache.getUserFromCache(username);
      if (user == null) {
          cacheWasUsed = false;
          try {
              // 检索User
              user = this.retrieveUser(username, (UsernamePasswordAuthenticationToken)authentication);
          } 
          ...
      }
  
      try {
          // 4.认证前检查user状态
          this.preAuthenticationChecks.check(user);
          // 5.附加认证检查状态
          this.additionalAuthenticationChecks(user, (UsernamePasswordAuthenticationToken)authentication);
      } catch (AuthenticationException var7) {
          if (!cacheWasUsed) {
              throw var7;
          }
  
          cacheWasUsed = false;
          user = this.retrieveUser(username, (UsernamePasswordAuthenticationToken)authentication);
          this.preAuthenticationChecks.check(user);
          this.additionalAuthenticationChecks(user, (UsernamePasswordAuthenticationToken)authentication);
      }
  	// 6.认证后检查user状态
      this.postAuthenticationChecks.check(user);
      if (!cacheWasUsed) {
          this.userCache.putUserInCache(user);
      }
  
      Object principalToReturn = user;
      if (this.forcePrincipalAsString) {
          principalToReturn = user.getUsername();
      }
  	// 7.创建认证成功的UsernamePasswordAuthenticationToken并将认证状态设置未true
      return this.createSuccessAuthentication(principalToReturn, authentication, user);
  }
  ```

  其中retrieveUser方法

  ```java
  protected final UserDetails retrieveUser(String username, UsernamePasswordAuthenticationToken authentication) throws AuthenticationException {
      this.prepareTimingAttackProtection();
  
      try {
          // 调用自定义UserDetailsService的loadUserByUsername方法
          UserDetails loadedUser = this.getUserDetailsService().loadUserByUsername(username);
          if (loadedUser == null) {
              throw new InternalAuthenticationServiceException("UserDetailsService returned null, which is an interface contract violation");
          } else {
              return loadedUser;
          }
      } 
      ...
  }
  ```

  其中additionalAuthenticationChecks方法

  ```java
  protected void additionalAuthenticationChecks(UserDetails userDetails, UsernamePasswordAuthenticationToken authentication) throws AuthenticationException {
      if (authentication.getCredentials() == null) {
          this.logger.debug("Failed to authenticate since no credentials provided");
          throw new BadCredentialsException(this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.badCredentials", "Bad credentials"));
      } else {
          // 1.获取前端密码
          String presentedPassword = authentication.getCredentials().toString();
          // 2.与数据库密码进行对比
          if (!this.passwordEncoder.matches(presentedPassword, userDetails.getPassword())) {
              this.logger.debug("Failed to authenticate since password does not match stored value");
              throw new BadCredentialsException(this.messages.getMessage("AbstractUserDetailsAuthenticationProvider.badCredentials", "Bad credentials"));
          }
      }
  }
  ```

* AbstractAuthenticationProcessingFilter--doFilter方法

  ```java
  private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
      if (!this.requiresAuthentication(request, response)) {
          chain.doFilter(request, response);
      } else {
          try {
              // 1.调用子类方法
              Authentication authenticationResult = this.attemptAuthentication(request, response);
              if (authenticationResult == null) {
                  return;
              }
  			// 2.session策略验证
              this.sessionStrategy.onAuthentication(authenticationResult, request, response);
              if (this.continueChainBeforeSuccessfulAuthentication) {
                  chain.doFilter(request, response);
              }
  			// 3.成功身份验证
              this.successfulAuthentication(request, response, chain, authenticationResult);
          } catch (InternalAuthenticationServiceException var5) {
              this.logger.error("An internal error occurred while trying to authenticate the user.", var5);
              this.unsuccessfulAuthentication(request, response, var5);
          } catch (AuthenticationException var6) {
              this.unsuccessfulAuthentication(request, response, var6);
          }
  
      }
  }
  ```

  successfulAuthentication方法

  ```java
  protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {
      SecurityContext context = SecurityContextHolder.createEmptyContext();
      // 1.将认证的用户放入SecurityContext中
      context.setAuthentication(authResult);
      SecurityContextHolder.setContext(context);
      this.securityContextRepository.saveContext(context, request, response);
      if (this.logger.isDebugEnabled()) {
          this.logger.debug(LogMessage.format("Set SecurityContextHolder to %s", authResult));
      }
  	// 检查是不是记住我
      this.rememberMeServices.loginSuccess(request, response, authResult);
      if (this.eventPublisher != null) {
          this.eventPublisher.publishEvent(new InteractiveAuthenticationSuccessEvent(authResult, this.getClass()));
      }
  	// 3.调用自定义的MyAuthHandle的onAuthenticationSuccess方法
      this.successHandler.onAuthenticationSuccess(request, response, authResult);
  }
  ```



## 4.3 记住我流程源码

整个过滤器链中，RememberMeAuthenticationFilter是来处理记住我用户认证的流程的, 所以下面我们主要针对记住我看下源码是如何实现的?

### 4.3.1 记住我流程分析

![image-20220825115847345]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220825115847345.png)

### 4.3.2 记住我流程源码跟踪

* AbstractAuthenticationProcessingFilter --》 successfulAuthentication方法

  ```java
  protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {
      SecurityContext context = SecurityContextHolder.createEmptyContext();
      // 1.将认证的用户放入SecurityContext中
      context.setAuthentication(authResult);
      SecurityContextHolder.setContext(context);
      this.securityContextRepository.saveContext(context, request, response);
      if (this.logger.isDebugEnabled()) {
          this.logger.debug(LogMessage.format("Set SecurityContextHolder to %s", authResult));
      }
  	// 检查是不是记住我
      this.rememberMeServices.loginSuccess(request, response, authResult);
      if (this.eventPublisher != null) {
          this.eventPublisher.publishEvent(new InteractiveAuthenticationSuccessEvent(authResult, this.getClass()));
      }
  	// 3.调用自定义的MyAuthHandle的onAuthenticationSuccess方法
      this.successHandler.onAuthenticationSuccess(request, response, authResult);
  }
  ```

  loginSuccess方法 ---》onLoginSuccess

  ```java
  protected void onLoginSuccess(HttpServletRequest request, HttpServletResponse response, Authentication successfulAuthentication) {
      // 1.获取用户名
      String username = successfulAuthentication.getName();
      this.logger.debug(LogMessage.format("Creating new persistent login for user %s", username));
      // 2.创建persistenToken
      PersistentRememberMeToken persistentToken = new PersistentRememberMeToken(username, this.generateSeriesData(), this.generateTokenData(), new Date());
  
      try {
          // 3.插入数据库
          this.tokenRepository.createNewToken(persistentToken);
          // 4.写入浏览器cookie
          this.addCookie(persistentToken, request, response);
      } catch (Exception var7) {
          this.logger.error("Failed to save persistent token ", var7);
      }
  
  }
  ```

* RememberMeAuthenticationFilter

  ```java
  private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
      if (SecurityContextHolder.getContext().getAuthentication() != null) {
          this.logger.debug(LogMessage.of(() -> {
              return "SecurityContextHolder not populated with remember-me token, as it already contained: '" + SecurityContextHolder.getContext().getAuthentication() + "'";
          }));
          chain.doFilter(request, response);
      } else {
          // 1.检查是是否记住我，如果是完成自动登录
          Authentication rememberMeAuth = this.rememberMeServices.autoLogin(request, response);
          if (rememberMeAuth != null) {
              try {
                  // 2.调用authenticationManager再次认证
                  rememberMeAuth = this.authenticationManager.authenticate(rememberMeAuth);
                  SecurityContext context = SecurityContextHolder.createEmptyContext();
                  // 3.将认证的用户在重新放入SecurityContext中
                  context.setAuthentication(rememberMeAuth);
                  SecurityContextHolder.setContext(context);
                  this.onSuccessfulAuthentication(request, response, rememberMeAuth);
  				...
              } 
              ...
          }
  		// 调用下一个过滤器
          chain.doFilter(request, response);
      }
  }
  ```

  其中autoLogin方法

  ```java
  public final Authentication autoLogin(HttpServletRequest request, HttpServletResponse response) {
      // 1.获取rememberMeCookie
      String rememberMeCookie = this.extractRememberMeCookie(request);
      // 2.检查是否存在
      if (rememberMeCookie == null) {
          return null;
      } else {
          this.logger.debug("Remember-me cookie detected");
          if (rememberMeCookie.length() == 0) {
              this.logger.debug("Cookie was empty");
              this.cancelCookie(request, response);
              return null;
          } else {
              try {
                  // 3.解码Cookie
                  String[] cookieTokens = this.decodeCookie(rememberMeCookie);
                  // 4.根据cookie完成自动登录
                  UserDetails user = this.processAutoLoginCookie(cookieTokens, request, response);
                  // 5.检查user状态
                  this.userDetailsChecker.check(user);
                  this.logger.debug("Remember-me cookie accepted");
                  // 6. 创建认证成功的RememberMeAuthenticationToken并将认证状态设置为true
                  return this.createSuccessfulAuthentication(request, user);
              } 
  			...
              this.cancelCookie(request, response);
              return null;
          }
      }
  }
  ```

  proccessAutoLoginCookie方法

  ```java
  protected UserDetails processAutoLoginCookie(String[] cookieTokens, HttpServletRequest request, HttpServletResponse response) {
      if (cookieTokens.length != 2) {
          throw new InvalidCookieException...
      } else {
          // 1.获取系列码和token
          String presentedSeries = cookieTokens[0];
          String presentedToken = cookieTokens[1];
          // 2.根据token去数据库中查询
          PersistentRememberMeToken token = this.tokenRepository.getTokenForSeries(presentedSeries);
          if (token == null) {
              ...
          } else {
              ...
              // 3.在创建一个新的token
              PersistentRememberMeToken newToken = new PersistentRememberMeToken(token.getUsername(), token.getSeries(), this.generateTokenData(), new Date());
  
              try {
                  // 4.修改数据库token信息
                  this.tokenRepository.updateToken(newToken.getSeries(), newToken.getTokenValue(), newToken.getDate());
                  // 5. 写入浏览器
                  this.addCookie(newToken, request, response);
              } catch (Exception var9) {
                  ...
              }
  			// 6.根据用户名调用UserDetailsService查询UserDetail
              return this.getUserDetailsService().loadUserByUsername(token.getUsername());
          }
      }
  }
  ```



## 4.4 CSRF流程源码

在整个过滤器链中, CsrfFilter是起到csrf防护的, 所以下面我们主要针对记住我看下源码是如何实现的?

### CSRF流程分析

![image-20220825125005446]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220825125005446.png)

### 3.4.2 CSRF源码流程跟踪

* Csrfilter

  ```java
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
      request.setAttribute(HttpServletResponse.class.getName(), response);
      // 1.取出token
      CsrfToken csrfToken = this.tokenRepository.loadToken(request);
      boolean missingToken = csrfToken == null;
      if (missingToken) {
        	// 2.如果没有token就重新生成token
          csrfToken = this.tokenRepository.generateToken(request);
          this.tokenRepository.saveToken(csrfToken, request, response);
      }
  	// 3.将csrfToken值放入request域中
      request.setAttribute(CsrfToken.class.getName(), csrfToken);
      request.setAttribute(csrfToken.getParameterName(), csrfToken);
      // 4.匹配请求是否为post请求，否则放行
      if (!this.requireCsrfProtectionMatcher.matches(request)) {
          if (this.logger.isTraceEnabled()) {
              this.logger.trace("Did not protect against CSRF since request did not match " + this.requireCsrfProtectionMatcher);
          }
  
          filterChain.doFilter(request, response);
      } else {
          String actualToken = request.getHeader(csrfToken.getHeaderName());
          if (actualToken == null) {
              // 5.从request请求参数中取出csrfToken
              actualToken = request.getParameter(csrfToken.getParameterName());
          }
  		// 6.匹配两个token是否相等
          if (!equalsConstantTime(csrfToken.getToken(), actualToken)) {
              this.logger.debug(LogMessage.of(() -> {
                  return "Invalid CSRF token found for " + UrlUtils.buildFullRequestUrl(request);
              }));
              AccessDeniedException exception = !missingToken ? new InvalidCsrfTokenException(csrfToken, actualToken) : new MissingCsrfTokenException(actualToken);
              this.accessDeniedHandler.handle(request, response, (AccessDeniedException)exception);
          } else {
              // 7.如果相等则放行
              filterChain.doFilter(request, response);
          }
      }
  }
  ```

​		

## 4.5 授权流程源码

在整个过滤器链中，FilterSecurityInterceptor是来处理整个用户授权流程的，也是距离用户API最后一个非常重要的过滤器链，因此下面针对用户授权看下源码是如何实现的？

### 4.5.1 授权流程分析

![image-20220825131729597]( https://yournotes.oss-cn-beijing.aliyuncs.com/post/document/springsecurity/image-20220825131729597.png)

**AffirmativeBased (基于肯定)** ： 一票通过权

**ConsensusBased（基于共识）**：赞成票多于反对票则通过，反对多余赞成抛出AccessDeniedException

**UnanimousBased（基于一致）**：一票否决权

### 4.5.2 授权流程源码跟踪

* FilterSecurityInterceptor

  ```java
  public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
      this.invoke(new FilterInvocation(request, response, chain));
  }
  /*------------*/
  public void invoke(FilterInvocation filterInvocation) throws IOException, ServletException {
      if (this.isApplied(filterInvocation) && this.observeOncePerRequest) {
          filterInvocation.getChain().doFilter(filterInvocation.getRequest(), filterInvocation.getResponse());
      } else {
          if (filterInvocation.getRequest() != null && this.observeOncePerRequest) {
              filterInvocation.getRequest().setAttribute("__spring_security_filterSecurityInterceptor_filterApplied", Boolean.TRUE);
          }
  		// 前置调用
          InterceptorStatusToken token = super.beforeInvocation(filterInvocation);
  
          try {
              filterInvocation.getChain().doFilter(filterInvocation.getRequest(), filterInvocation.getResponse());
          } finally {
              super.finallyInvocation(token);
          }
  		// 后置调用
          super.afterInvocation(token, (Object)null);
      }
  }
  ```

  AbstractSecurityInterceptor的beforeInvocation方法

  ```java
  protected InterceptorStatusToken beforeInvocation(Object object) {
      Assert.notNull(object, "Object was null");
      if (!this.getSecureObjectClass().isAssignableFrom(object.getClass())) {
          ...
      } else {
          // 1.获取security的系统配置权限
          Collection<ConfigAttribute> attributes = this.obtainSecurityMetadataSource().getAttributes(object);
          if (CollectionUtils.isEmpty(attributes)) {
  			...
          } else {
              ...
  			// 2.获取用户认证信息
              Authentication authenticated = this.authenticateIfRequired();
              if (this.logger.isTraceEnabled()) {
                  this.logger.trace(LogMessage.format("Authorizing %s with attributes %s", object, attributes));
              }
              // 3.调用决策管理器，无权限则抛出accessDeniedException让ExceptionTranslationFilter捕获
              this.attemptAuthorization(object, attributes, authenticated);
  			...
          }
      }
  }
  ```

  attemptAuthoriztion-->AffirmativeBase的decide方法

  ```java
  public void decide(Authentication authentication, Object object, Collection<ConfigAttribute> configAttributes) throws AccessDeniedException {
      int deny = 0;
      Iterator var5 = this.getDecisionVoters().iterator();
  
      while(var5.hasNext()) {
          AccessDecisionVoter voter = (AccessDecisionVoter)var5.next();
          int result = voter.vote(authentication, object, configAttributes);
          // 一票否决
          switch(result) {
              case -1:
                  ++deny;
                  break;
              case 1:
                  return;
          }
      }
  
      if (deny > 0) {
          throw new AccessDeniedException(this.messages.getMessage("AbstractAccessDecisionManager.accessDenied", "Access is denied"));
      } else {
          this.checkAllowIfAllAbstainDecisions();
      }
  }
  ```

* ExceptionTranslationFilter

  ```java
  private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
      try {
          // 1.调用下一个过滤器即FilterSecurityInterceptor
          chain.doFilter(request, response);
      } catch (IOException var7) {
          throw var7;
      } catch (Exception var8) {
          Throwable[] causeChain = this.throwableAnalyzer.determineCauseChain(var8);
          RuntimeException securityException = (AuthenticationException)this.throwableAnalyzer.getFirstThrowableOfType(AuthenticationException.class, causeChain);
          // 2.捕获FilterSecurityInterceptor并判断异常类型
          if (securityException == null) {
              securityException = (AccessDeniedException)this.throwableAnalyzer.getFirstThrowableOfType(AccessDeniedException.class, causeChain);
          }
  
          if (securityException == null) {
              this.rethrow(var8);
          }
  
          if (response.isCommitted()) {
              throw new ServletException("Unable to handle the Spring Security Exception because the response is already committed.", var8);
          }
  		// 3.如果是AccessDeniedException异常则处理Spring Security异常
          this.handleSpringSecurityException(request, response, chain, (RuntimeException)securityException);
      }
  
  }
  ```

  handleSpringSecuirtyException方法

  ```java
  private void handleSpringSecurityException(HttpServletRequest request, HttpServletResponse response, FilterChain chain, RuntimeException exception) throws IOException, ServletException {
      if (exception instanceof AuthenticationException) {
          this.handleAuthenticationException(request, response, chain, (AuthenticationException)exception);
      } else if (exception instanceof AccessDeniedException) {
          this.handleAccessDeniedException(request, response, chain, (AccessDeniedException)exception);
      }
  
  }
  
  private void handleAuthenticationException(HttpServletRequest request, HttpServletResponse response, FilterChain chain, AuthenticationException exception) throws ServletException, IOException {
      this.logger.trace("Sending to authentication entry point since authentication failed", exception);
      this.sendStartAuthentication(request, response, chain, exception);
  }
  
  private void handleAccessDeniedException(HttpServletRequest request, HttpServletResponse response, FilterChain chain, AccessDeniedException exception) throws ServletException, IOException {
      Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
      boolean isAnonymous = this.authenticationTrustResolver.isAnonymous(authentication);
      if (!isAnonymous && !this.authenticationTrustResolver.isRememberMe(authentication)) {
          if (this.logger.isTraceEnabled()) {
              this.logger.trace(LogMessage.format("Sending %s to access denied handler since access is denied", authentication), exception);
          }
  
          this.accessDeniedHandler.handle(request, response, exception);
      } else {
          if (this.logger.isTraceEnabled()) {
              this.logger.trace(LogMessage.format("Sending %s to authentication entry point since access is denied", authentication), exception);
          }
  
          this.sendStartAuthentication(request, response, chain, new InsufficientAuthenticationException(this.messages.getMessage("ExceptionTranslationFilter.insufficientAuthentication", "Full authentication is required to access this resource")));
      }
  
  }
  ```

  
