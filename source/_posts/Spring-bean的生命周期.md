---
title: Spring bean的生命周期
date: 2020-08-29 16:08:13
tags: java框架
---

##### 概述

Spring是一个轻量级控制反转(IoC)和面向切面(AOP)的容器框架。因此关于bean的生成过程生命周期是重要的知识点。也是在对Spring的认识的重要考察点。在传统的Java应用中，bean的生命周期很简单<!--more-->，使用Java关键字 new 进行Bean 的实例化（实例化时可使用有参构造方法进行初始化，或者实例化后使用set方法进行初始化），然后该Bean 就能够使用了。一旦bean不再被使用，则由Java自动进行垃圾回收。相比之下，Spring管理Bean的生命周期就复杂多了，正确理解Bean 的生命周期非常重要，因为Spring对Bean的管理可扩展性非常强。因此在这里做一个梳理分析bean的生命周期。



##### bean怎么生成

bean即是一个对象，那么我们怎么去生成一个对象呢。比如创建User类的对象那么首先是需要一个User class，然后直接去new最后再完成赋值初始化对象

```java
@Component
class User{
    @Autowired
    private Dog dog;
    private String beanName;
    ...
}
```

```java
User user = new User();
user.setDog(new Dog());
user.setBeanName("user");
```

那现在使用Spring这个bean是交由Spring去生成的，我们都是从Spring容器去拿到

```java
User user = context.getBean("user",User.class)
```

因此它已经生成好了我们才能拿到。那么它是怎么生成的呢，难道是在源码里去new User()么？那肯定不会这样去写它是通过反射去调用构造方法得到的，这个时候就是最初的对象userBean里面的属性还没赋值。所以之后还会进行依赖注入将创建好的对象里面的属性进行赋值也是通过反射的方式越过私有直接赋值。

```java
Field field = userBean.getClass().getDeclaredField("dog");
field.setAccessible(true);
field.set(userBean, dogBean);
```

到这里好像就完整的初始化好了需要的对象，但实际上是不够的，依赖注入并没有办法给上面User类的beanName赋值。它只能在池中找到类型一样的bean或者和属性名相同beanName的bean，拿这样的bean进赋值。但这里属性赋值的是当前bean的beanName而不是匹配其他bean。在这里可以通过依赖注入就初始化的属性它们的赋的值就是已经被Spring管理的bean。但有些属性它们是在Spring单例池找不到的，它们可能是某个特定的字符串，有可能是某些特殊的对象。

这个时候Spring也提供了方式去传入，依赖注入之后判断是否实现aware接口（BeanNameAware、BeanFactoryAware....）。如果有则调用接口对应的set方法并传入对应数据。因此当bean类当中有某些属性是非单例池中的无法通过依赖注入赋值，但这些值是Spring中具有的就可以通过Spring提供的一些aware接口的方式让spring自己去传入那些值然后我们在方法体中定义赋值。

```java
@Component
class User implements BeanNameAware{
    @Autowired
    private Dog dog;
    private String beanName;
    /*
    方法在依赖注入后会被执行
    并且将当前beanName作为参数传入
    方法体自定义（这里赋值给上面属性）
    */
    @override
    public void setBeanName(String name){
        this.beanName = name;
    }
    ...
}
```

除此之外还有既不是bean容器中的，也不是Spring含有的一些数据。那么就不能依赖注入，也没有aware传入。它需要你自己传入比如查询数据库赋值或者其他的自定义操作取值然后赋值。这就有第四个阶段初始化

```java
@Component
class User implements InitializingBean{
    @Autowired
    private Dog dog;
    private String userName;
    private int age;
    /*
    方法在aware处理完后会被执行
    方法体自定义（这里赋值给上面属性）
    */
    @override
    public void afterPropertiesSet() throws Exception{
        //this.userName = "张三";
        //this.age = 18;
    }
    ...
}
```

像上面两个属性username与age就是不能通过autowired或者awrare完成赋值的。spring提供InitializingBean初始化接口，当bean的生成从新建到依赖注入再到aware判断处理之后若有实现InitializingBean接口则去调用初始化方法afterPropertiesSet()在这里进行自定义的属性赋值，或者其他操作。

到此为止（class——新生对象——依赖注入——aware——初始化），bean从新建到最后属性都是完善的。但最终存在单例池中当中的对象可能是代理对象而不是原对象，否则做的那些切面都是无意义的。得到一个bean然后调用其方法，它可能是真正那个bean的代理对象其方法比原方法做了扩展。因此在初始化之后还会有一步就是aop判断是否要生成代理对象最终才得到的bean放入单例池

这里就出来了**狭义的生命周期**

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/Spring-bean生命周期/1.png)





##### 广义的生命周期

实际上上面的生命周期还是存在问题的，aop之后得到最终的bean它是直接返回还是存进单例池是取决于这个bean它的作用域scope

```xml
<bean id="user" class="com.hao.User" scope="prototype"/>
```

默认是单例但也有多例的存在像上面配置的，实际上在Spring当中是存在一个bean的包装类BeanDefinition。它当中的各个字段就包含着bean的各种信息比如bean实际的class类、创建的工厂、初始化方法、作用域、是否懒加载等等。

```java
BeanDefinition
    1.scope
    2.initMethod
    3.byName byType
    4.dependsOn
    5.beanClass
    ....  
```

bean的创建初始化到最终根据这些信息来完成的，因此我们并不是直接通过class得到新生对象，而是先有BeanDefinition。根据BeanDefinition里的beanClass属性完成创建新生bean，而新建对象需要工厂所以有了该bean的BeanDefinition后再由BeanFactory完成创建bean对象

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/Spring-bean生命周期/2.png)

那么BeanDefinition的beanClass就是配置信息中的class，在生成新生对象之前如果去改变了BeanDefinition的beanClass属性那么之后新建对象就不是以前那个class的实例了。BeanFactory也是提供了后置处理器BeanFactoryPostProcessor就可以完成在对象创建前做一些操作。BeanFactory组建完毕里面有所有的BeanDefinition在通过这些BeanDefinition创建各个bean对象存在Spring容器中。在创建之前就会判断有没有后置处理器BeanFactoryPostProcessor。没有就直接创建对象，有则执行完后置处理器中的方法再创建

```java
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {

    /**
     * 主要是用来自定义修改持有的bean
     * @param beanFactory
     * @throws BeansException
     */

    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        String[] names = beanFactory.getBeanDefinitionNames();
        // 获取了所有的bean名称列表
        for(int i=0; i<names.length; i++){
            String name = names[i];
            BeanDefinition beanDefinition = beanFactory.getBeanDefinition(name);
            System.out.println(name + " bean properties: " + beanDefinition.getPropertyValues().toString());
            
        }
    }
}
```

执行到后置处理器，beanFactory包含全部的BeanDefinition所以可以获取BeanDefinition做自定义修改，也可以往beanFactory当中注册bean（mybatis自动生成的mapper代理类就是这样进到了Spring容器）。









