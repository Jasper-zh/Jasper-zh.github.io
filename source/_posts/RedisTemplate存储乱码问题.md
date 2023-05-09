---
title: RedisTemplate存储乱码问题
date: 2021-08-28 15:14:38
tags: 日常问题
categories: 
- 日常
- 问题
---

### 前言

> 这个东西以前也是经常处理，但是懒得记，今天弄自己网盘项目的登录用Redis也遇到这个，记录下自己常用的解决方式

### 问题

比如像下面，存值存一个字符串类型

![image-20210828155129033](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210828155129033.png)

![image-20210828155105000](https://gitee-imagehost.oss-cn-beijing.aliyuncs.com/image_host/image-20210828155105000.png)

出现这个问题的原因是两边序列化不一致。



### 解决方式

1. 配置RedisTemplate的序列化方式

   * 如果key和value都是存字符串类型则设置序列化为StringRedisSerializer

   ```java
   RedisSerializer stringSerializer = new StringRedisSerializer();
   redisTemplate.setKeySerializer(stringSerializer);
   redisTemplate.setValueSerializer(stringSerializer);
   ```

   * 如果key参数放字符串，value放对象

   ```java
   redisTemplate.setKeySerializer(new StringRedisSerializer());
   redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
   ```

   * 也可以在bean里配置

   ```java
   @Configuration
   public class RedisConfig {
       @Autowired
       private RedisTemplate redisTemplate;
       @Bean
       public RedisTemplate redisTemplateInit() {
           RedisSerializer stringSerializer = new StringRedisSerializer();
           redisTemplate.setKeySerializer(stringSerializer);
           redisTemplate.setValueSerializer(stringSerializer);
           return redisTemplate;
       }
   }
   ```

2. 除了配置序列化方式之外，如果是都存字符串可以在注入RedisTemplate时直接加上泛型

   ```java
   @Autowired
   RedisTemplate<String,String> redisTemplate;
   ```

   

### 总结

自己一般是直接加泛型解决乱码问题，即使存值类型是对象含义也是转为json字符串来存。所以基本上key,value都是字符串类型。

