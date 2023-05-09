---
title: HashMap
date: 2020-08-12 08:22:39
tags: Java
categories: 
- JAVA
---

#### HashMap概述

>基于哈希表的 Map 接口的实现。此实现提供所有可选的映射操作，并允许使用 null 值和 null 键。（除了非同步和允许使用 null 之外，HashMap 类与 Hashtable 大致相同。）此类不保证映射的顺序，<!--more-->特别是它不保证该顺序恒久不变。此实现假定哈希函数将元素适当地分布在各桶之间，可为基本操作（get 和 put）提供稳定的性能。迭代 collection 视图所需的时间与 HashMap 实例的“容量”（桶的数量）及其大小（键-值映射关系数）成比例。所以，如果迭代性能很重要，则不要将初始容量设置得太高（或将加载因子设置得太低）。——百度百科

HashMap是Java程序员使用频率最高的用于映射(键值对)处理的数据类型（键值对集合）。随着JDK版本的更新，JDK1.8对HashMap底层的实现进行了优化，例如引入红黑树的数据结构和扩容的优化等。文章先基于1.7描述，最后再提1.8与之更改的地方。

```java
HashMap<String,String> hashMap = new HashMap();
hashMap.put("张三","男");
hashMap.get("张三");
```

那么它里面存的元素就key和value么？（它其实里面封装成一个entry可以看到除了key与value之外还有next属性，所以HaspMap里的元素不仅仅含有键值对还有指向下一节点的元素的信息）

```java
static class Entry<K,V> implements Map.Entry<K,V> {
      final K key;//Key-value结构的key
      V value;//存储值
      Entry<K,V> next;//指向下一个链表节点
      final int hash;//哈希值
}
```



#### HashMap的数据结构

在Java编程语言中，最基本的结构就是两种，一个是数组，另外一个是模拟指针（引用），所有的数据结构都可以用这两个基本结构来构造的，HashMap也不例外。HashMap底层就是一个数组结构，数组中的每一项又是一个链表。在1.8之后当链表长度大于等于8时转为红黑树（自平衡的二叉树这里不详细展开，以后有机会再讲）。 

那么它是如何去存储？（每个元素存进来如何找到内置数组的位置）

```java
hashMap.put("战三","work");
hashMap.put("里的","energer");
hashMap.put("约翰","cookie");
....
```

假如和ArrayList一样初始化一个index变量值为0作为下标，当arrayList.add("a")时将元素存在内置数组array[index]再index自增。即每次往后添加元素。至到数组满了再依次从第一个往后添加链。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/HashMap1.png)

在这种情况下我们去hashMap.get("约翰")，怎么去找到。很显然没办法定位。只能从数组第一位开始找比较entry的key值如不同再下一个比较直到找到相同的key。那么数量很多而且对应的entry位置比较偏后查找是及其费时的。虽然添加没毛病速度也快但查找是遍历比较所以是不合理的。

在HashMap当中实际上存储时它会去给key进行类似hash得到的hashCode与数组的容量取模的得到的就是数组的位置在这样的位置存下。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/HashMap2.png)

在使用key查找时也是对查找的key进行相同的hash直接定位到当初存的位置。这样的一个位置在数组是随机的，不同的code取模有可能会出现相同的位置即形成链表。然后在链表当中找到对应key的entry返回。下面伪代码描述了它的get方法的一个大概意思。直接找到数组所在的entry如果不是就是该entry的next。如果还不是就是next的next直到找到对应key的entry。

```java
get(key){
    int hashcode = key.hashCode();
    int index = hashcode%table.length;
    Entry entry = table[index];
    ...
    return entry;
}
```

那么在这里所谓的链表就是在逻辑上理解为它们在数组同一个位置，实际上就是先找到数组的这个位置得到了一个entry1然后这个entry1里的next属性引用的就是一个entry2，通过这个entry2的next就能找到entry3。这样的一个链表，基于找到第一个（数组中的entry）然后通过next字段一个一个的引用。下图与上图相对应

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/HashMap3 .png)

上图数组存的entry3变量引用堆中的0x003的一个entry对象，这个对象里的next属性引用了地址0x004的entry对象。put时它是去怎样去插入的。当发生碰撞是找到这个链表的最下的entry把它的next=null换成当前插入的entry地址，还是把当前插入的entry的next改为第一个就是数组中的table[index]。也就是头插还是尾插。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/HashMap4.png)

那么怎样去存下面使用伪代码说明大概流程（实际源码还有判断空判断key是否重复先忽略）

假设是尾插

```java
put(key,value){
    int code = key.hashCode();
    int index = code%table.length;
    Entry e = table[index];
    /*
    1.数组当前位置空
    table[index] = new Entry(key,value,null);
    2.当前位置有entry就将它的next设为当前创建的entry
    table[index].next = new Entry(key,value,null);
    3.当前有entry并且next也有
    table[index].next.next = new Entry(key,value,null);
    遍历链表找到最后一个entry也就是next为空的entry,将它的next设成当前创建的entry
    */
    while(e!=null){
		e = e.next;
    }
    e.next = new Entry(key,value,null);
}
```

假设是头插

```java
put(key,value){
    int code = key.hashCode();
    int index = code%table.length;
    //1.new当前的entry,并将next设成现在第一个也就是数组上的table[index]
    //2.将当前的entry设成第一个就是放在数组上。
    //它的next就是以前的第一个entry对象,现在把当前对象放数组
    table[index] = new entry(key,value,table[index]);
}
```

两种方式首先头插法明显要快。直接插然后下移只要一句代码而尾插需要遍历。所以我们在jdk1.7当中是的确使用的头插法，但在1.8之后修改成尾插法下面会提。



#### 容量问题

```java
public class HashMap<K,V>
    extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable
{
    // 初始默认容量 16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

    // 最大容量值
    static final int MAXIMUM_CAPACITY = 1 << 30;

    // 默认加载因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

    ......
}
```
默认容量16，加载因子0.75。通过构造方法可以自己传入容量与加载因子。但值得一提的是容量一定是2的指数幂

```java
/**
* Inflates the table.
*/
private void inflateTable(int toSize) {
    // Find a power of 2 >= toSize
    int capacity = roundUpToPowerOf2(toSize);
    threshold = (int) Math.min(capacity * loadFactor, MAXIMUM_CAPACITY+1);
    table = new Entry[capacity];
    initHashSeedAsNeeded(capacity);
}
```

 为什么它的容量一定要求为2的指数幂呢。桶数组用来去定位存储，hashCode与容量取模定位。确实不管是不是2的指数幂都能进行取模运算，因此这个要求大概是与效率有关。可以看到源码得index的方式

```java
//并不是index = h % length
indexFor(h,length){
    index = h & (length - 1);
}

```

那么就可能是在容量位2的指数幂的情况下通过这样的运算就能代替取模运算，并且位运算与减运算效率高于除运算。下面可以试一下：

```java
//假如入容量16，一个随机hashCode
hashCode 1011 0010 0111
length   0000 0000 1111
index    0000 0000 0111
```

```java
//假如入容量10，一个随机hashCode
hashCode 1011 0010 0111
length   0000 0000 1001
index    0000 0000 0001
```

通过与运算结果肯定是小于的。所以结果不会越界，但如果容量不为2的指数幂有某些下标是永远取不到的，在例子二中比容量1001（10）小的是0-9也就是全部的下标。但111（7），101（5），11（3）永远取不到。所以只有满足2的指数幂减一与运算的结果才是具有全部下标的可能性和取模实际一样。扩容之后所有元素也要遍历重新定位这个时候也要进行大量的这个运算所以采用这个h&(length-1)效率要更高。



#### 加载因子

第二就是加载因子为何是0.75，这个大家可能都知道等桶数组满了再扩容哈希碰撞一定挺多的很容易长链1.8会生成红黑树，但装到一半就扩容就空位太多很浪费空间，0.75也就是这样的一个折中的选择。总而言之就是这样规定。也是通过统计出来的比较好的结果。下面这段注释也说明了取0.75发生8次碰撞的概率已经是一亿分之六红黑树并不会那么容易生成那样就够了，其实关于为什么是0.75就把他当做一个折中的选择。这里包括下面注释内容其实上并没有说明，这个东西泊松分布和红黑树为什么在设定在链表到8的时候生成有关系下面会讲

```java
* Because TreeNodes are about twice the size of regular nodes, we
* use them only when bins contain enough nodes to warrant use
* (see TREEIFY_THRESHOLD). And when they become too small (due to
* removal or resizing) they are converted back to plain bins.  In
* usages with well-distributed user hashCodes, tree bins are
* rarely used.  Ideally, under random hashCodes, the frequency of
* nodes in bins follows a Poisson distribution
* (http://en.wikipedia.org/wiki/Poisson_distribution) with a
* parameter of about 0.5 on average for the default resizing
* threshold of 0.75, although with a large variance because of
* resizing granularity. Ignoring variance, the expected
* occurrences of list size k are (exp(-0.5) * pow(0.5, k) /
* factorial(k)). The first values are:
*
* 0:    0.60653066
* 1:    0.30326533
* 2:    0.07581633
* 3:    0.01263606
* 4:    0.00157952
* 5:    0.00015795
* 6:    0.00001316
* 7:    0.00000094
* 8:    0.00000006
```



#### 扩容问题

扩容源码(1.7)

```java
void resize(int newCapacity)
{
    Entry[] oldTable = table;
    int oldCapacity = oldTable.length;
    ......
    //创建一个新的Hash Table
    Entry[] newTable = new Entry[newCapacity];
    //将Old Hash Table上的数据迁移到New Hash Table上
    transfer(newTable);
    table = newTable;
    threshold = (int)(newCapacity * loadFactor);
}
```

```java
//put时当达到容量的0.75，进行扩容resize方法创建新数组后就是调用transfer
void transfer(Entry[] newTable, boolean rehash) {
        int newCapacity = newTable.length;//新数组的长度
        for (Entry<K,V> e : table) {//遍历旧数组
            while(null != e) {//遍历旧数组中的每个链表结点
                Entry<K,V> next = e.next;//next指向当前遍历结点e的下一个结点
                if (rehash) {//再hash
                    e.hash = null == e.key ? 0 : hash(e.key);
                }
                int i = indexFor(e.hash, newCapacity);//重新计算当前元素在新数组中的位置
                /*********关键的 3行代码（头插法移动元素）*********/
                e.next = newTable[i];
                newTable[i] = e;
                e = next;
            }
        }
    }
```

put时当达到阈值后进行扩容也就是执行resize方法创建新数组，再执行transfer方法里面双循环遍历数组与链表，重新定位放入新桶数组，在1.7的扩容会有死环问题，这里先根据源码的扩容步骤作出了如下的扩容步骤图

**扩容流程**

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/1.png)

假设上图数组正在扩容，首先循环到null != e的时候才能执行while的内容，e是遍历table数组的元素所以e = first，next = e.next这时代码中的next = sec。

然后求e.hash重新计算使用indexFor得到新数组的下标i（假设为3，第4个位置），让e.next改为newTable[i]的引用，再将newTable[i]改为当前e也就是fist，也就是头插法

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/2.png)

代码第三步e = next 将变量e的引用原先是fist改为next的引用也就是e = sec。因此while里面就是遍历链表的元素赋值给e，在判断e是否为空当前e是sec，再进行同样的操作（定位，头插，移位）

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/3.png)

现在新数组就变成图中右下的样子了，可以看到就是sec的next变成first，first的next为空比起原先数组的关系就是反过来的。因为头插嘛第一个先插过去后面的插过去就更靠前第一个就是最下面。接下来e就是sec的next也就是遍历到第三个了然后没有下一个while结束，for循环下一个数组元素。就这样循环往复遍历所有的元素转移到新数组。

**死环**问题就是在并发的情况下做这些扩容步骤出现的问题，通过上述演示单线程的一个扩容流程。假设现在两个线程同时对一个hashmap扩容。那么同时访问resize方法两个线程里面都创建了一个自己线程里的新数组，然后再执行transfer方法共同执行到while里的第一句next = e.next时的情况如下图。两个线程里都有自己的e与next还有新数组。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/4.png)

这时线程1停在这里。线程2继续全部执行完（while循环两次直到e为空），这时线程2的新数组已经移完原来的两个元素

第一次循环

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/5.png)

第二次循环

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/6.png)

这时候线程1又往后开始执行了。其实问题就在于此时堆中的fisrt对象和sec已经被改了。first对象里的next属性现在其实是空，然后sec里的next值为first。按照步骤

第一步完成后e（first）到新数组，之后e改为next（sec）。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/7.png)



到这一步问题就来了，处理完first之后现在e引用sec，再次while循环 得next = e.next。现在e.next = first而不是null，就出现循环了。处理完sec到时候e再引用first再循环一遍之后e才为空

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/8.png)

将sec移过去然后next为first（其实它的next本来就是first了因为开始那个线程已经改了因此前面next就不等于null，而是first）。导致接下来e 不是null，是first。然后再进while循环e =first，next=null。这时first就要插在sec上就是first的next改为sec。就形成了死循环 

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/HashMap/9.png)

总结这个问题产生就是因为头插法导致从旧数组到新数组的时候链表方向会反过来，再因为并发的问题开始读取的是first并且next是sec。但中途却被别的线程改了因为扩容next反过来。它还拿着之前的循环一次再取就是有问题了本来应该链表结束了为0，结果可以连到fisrt。



#### JDK8的优化

1. resize 扩容优化
2. 解决了resize时多线程死循环问题
3. 引入了红黑树，目的是避免单条链表过长而影响查询效率

关于resize优化，在1.7上面扩容源码里拿到元素重新类似取模运算h & (length-1)得到位置，实际上在扩容是原来的两倍，这个时候取模结果就是原来的位置或原来的位置加原数组长度就这两种情况。

```java
//扩容前长度16
length-1   0000 0000 1111
hashCode1  1011 0010 0111  （111）3
hashCode2  0001 0101 0111  （111）3
```

```java
//扩容后长度32
length-1   0000 0001 1111
hashCode1  1011 0010 0111  （111）3
hashCode2  1101 0101 0111  （10111）19
```

所以hashcode之前没用到的高一位要么是0要么是1。新数组容量是以前两倍高位多了个1，hashcode如果高一位是0和以前就没有区别，如果是1就多了1 一个原数组大小。从以前的111要么没有要么还是111要么是10111就是看那个高位所以我们只需要判断h & length的结果。就看那一个位就可以判断它的新下标是不变还是加上老数组容量

```java
//扩容前长度16
length     0000 0001 0000
hashCode1  1011 0010 0111  （0000 0000）0
hashCode2  0001 0101 0111  （0001 0000）16
```

```java
//这段代码放在遍历原数组里面
if((h&length)=0){
	当前遍历的下标i就是新数组下标
}else{
	当前遍历的i+oldTable.length就是新下标
}
```

1.8源码当中也就是这样去确定新的下标位置

```java
Node<K,V> loHead = null, loTail = null;
Node<K,V> hiHead = null, hiTail = null;
Node<K,V> next;
do {
    next = e.next;
    //判断是低指针还是高指针
    if ((e.hash & oldCap) == 0) {
        if (loTail == null)
            loHead = e;
        else
            loTail.next = e;
        loTail = e;
    }
    else {
        if (hiTail == null)
            hiHead = e;
        else
            hiTail.next = e;
        hiTail = e;
    }
} while ((e = next) != null);
if (loTail != null) {
    loTail.next = null;
    newTab[j] = loHead;
}
if (hiTail != null) {
    hiTail.next = null;
    newTab[j + oldCap] = hiHead;
}
```

在上述源码中，除了新坐标的计算外。内部定义的两组共四个指针（低头、低尾、高头、高尾）低高区分新下标是保持原样的还是增加的和上面提的一样不需要rehash，无论是低指针还是高指针都有两个头尾指针head、tail，标记头尾也是解决1.7当中由于体位倒置会出现的死循环问题