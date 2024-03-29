---
title: Synchronized简述
date: 2020-07-20
tags: Java
categories: 
- JAVA
---

#### Synchronized概念

>Java语言的关键字，可用来给对象和方法或者代码块加锁，当它锁定一个方法或者一个代码块的时候，同一时刻最多只有一个线程执行这段代码。当两个并发线程访问同一个对象object中的这个加锁同步<!--more-->代码块时，一个时间内只能有一个线程得到执行。另一个线程必须等待当前线程执行完这个代码块以后才能执行该代码块。然而，当一个线程访问object的一个加锁代码块时，另一个线程仍可以访问该object中的非加锁代码块。——**百度百科**

synchronized锁定的是一个对象而不是代码块相当于对门加锁，房间里面做什么自定义。但只有拿到这个锁定的对象才能进入房间才能做接下来自定义的操作。使用同一把锁也就是同一个对象，那么一个线程拿走了就可以进入执行，其他的线程就拿不到进不了房间，直到那个线程执行完毕释放对象，那么其他线程再去共同竞争那一个对象，这样来达到一个互斥的效果避免共享数据错误。在字节码层表现monitorenter加锁和monitorexit释放锁。

```java
//源代码
synchronized (this) {
	int i = 1;
}
```

```java
//反汇编
3: monitorenter  //加锁
4: iconst_1
5: istore_1
6: aload_1
6: monitorexit //释放锁
```



#### 对象的内存结构

了解对象加锁原理那么首先需要了解在JVM（hotspot）当中一个对象的内存结构

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/Synchronized/对象内存结构.png)

>1.对象头
>
>对象头分为MarkWord与Class对象指针，其中MarkWord标识了对象运行时的各种属性与状态值。哈希码、GC分代 年状 态标志、线程持有的锁、偏向线程ID、偏向时间戳等。（哈希，GC信息，锁信息）
>Class对象指针：这段空间是用来保存自己的父类通过类加载器加载到内存后生成的类的Class对象的地址，这样就可以通过对象知道它是属于哪个类的。
>
>2.实例数据
>
>实例数据是保存对象真正有效的数据，也就是对象的各种字段信息，其中也包括从父类中继承的字段，都保存在这里，当然方法不在这里，在类中。而且它的内存具体分配结构是受jvm分配策略与字段在源码中的顺序来决定的，默认是相同宽度的字段分配在一起，在这个前提下父类字段在子类字段前面,而且子类中较窄的字段也可能被分配到父类中的间隙中。
>
>3.对齐填充
>
>因为hotspot虚拟机64位的内存管理系统要求内存的起始地址必须是8个字节的整数倍，所以这段填充就是为了保证地址是8个字节的整数倍。

**markword**在这里就是记录着各种信息64位的二进制串其中三位标记着锁信息

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/Synchronized/对象头.png)



#### 自旋锁CAS

CAS：Compare and Swap，即比较再交换。

jdk5增加了并发包java.util.concurrent.*,其下面的类使用CAS算法实现了区别于synchronouse同步锁的一种乐观锁。JDK 5之前Java语言是靠synchronized关键字保证同步的，这是一种独占锁，也是是悲观锁。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/Synchronized/CAS.png)



#### Synchronized锁升级

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/Synchronized/锁升级.png)

给对象加锁，实际上是改变的是对象的markword内容。通过标志位的一些记录来判断当前的锁信息，从下面例子打印结果可以看到锁标记位（第一个字节后三位）由001变为000，对象创建没有开启偏向锁进而加锁直接升级自旋锁。

```java
Object o = new Object()；
System.out.println(ClassLayout.parselnstonce(o).toPrintable())； 
    
synchronized (o) {
	System.out.println(ClassLayout.porselnstonce(o).toPrintable())； 
}
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/Synchronized/markword.png)

早期synchronized属于重量级锁申请和注销必须得向内核层申请。但有些需要同步的地方大多数情况下也确实只有一个线程访问。这样的情况下仍然去找操作系统申请锁资源浪费比例大。所以1.6之后对Synchronized进行了优化有偏移锁与自旋锁。

**偏向锁**：这个锁会偏向于第一个获得它的线程，在接下来的执行过程中，假如该锁没有被其他线程所获取，没有其他线程来竞争该锁，那么持有偏向锁的线程将永远不需要进行同步操作。也就是说:在此线程之后的执行过程中，如果再次进入或者退出同一段同步块代码，并不再需要去进行**加锁**或者**解锁**操作，而是会做以下的步骤：

1. Load-and-test，也就是简单判断一下当前线程id是否与Markword当中的线程id是否一致。
2. 如果一致，则说明此线程已经成功获得了锁，继续执行下面的代码。
3. 如果不一致，则要检查一下对象是否还是可偏向，即“是否偏向锁”标志位的值。
4. 如果还未偏向，则利用CAS操作来竞争锁（保证原子操作），也即是第一次获取锁时的操作。

如果此对象已经偏向了，并且不是偏向自己，则说明存在了**竞争**。此时可能就要根据另外线程的情况，可能是重新偏向，也有可能是做偏向撤销，但大部分情况下就是升级成**轻量级锁**了。

**锁撤销**

1. 在一个安全点停止拥有锁的线程。
2. 遍历线程栈，如果存在锁记录的话，需要修复锁记录和Markword，使其变成无锁状态。
3. 唤醒当前线程，将当前锁升级成轻量级锁。

所以，如果某些同步代码块大多数情况下都是有两个及以上的线程竞争的话，那么偏向锁就会是一种累赘，对于这种情况，我们可以一开始就把偏向锁这个默认功能给关闭。

**轻量级锁**

锁撤销升级为轻量级锁之后，那么对象的Markword也会进行相应的的变化。下面先简单描述下锁撤销之后，升级为轻量级锁的过程：

1. 线程在自己的栈桢中创建锁记录 LockRecord。
2. 将锁对象的对象头中的MarkWord复制到线程的刚刚创建的锁记录中。
3. 将锁记录中的Owner指针指向锁对象。
4. 将锁对象的对象头的MarkWord替换为指向锁记录的指针。

**重量级锁**

轻量级锁膨胀之后，就升级为重量级锁了（默认一个线程循环10次或者线程数量超过cpu一半的核数）。重量级锁是依赖对象内部的monitor锁来实现的，而monitor又依赖操作系统的MutexLock(互斥锁)来实现的，所以重量级锁也被成为**互斥锁**，加锁解锁向内核层申请相对麻烦但不占用cpu资源。