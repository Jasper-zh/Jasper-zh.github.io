---
title: LeetCode初级算法之字符串：28.实现 strStr()
date: 2020-12-06 16:29:28
tags: LeetCode日常
---

### 实现 strStr() 函数

题目地址：https://leetcode-cn.com/problems/implement-strstr/

给定一个 haystack 字符串和一个 needle 字符串，在 haystack 字符串中找出 needle 字符串出现的第一个位置 (从0开始)。如果不存在，则返回  -1。<!--more-->

**示例 1:**

> 输入: haystack = "hello", needle = "ll"
> 输出: 2

**示例 2:**

> 输入: haystack = "aaaaa", needle = "bba"
> 输出: -1

**说明:**
当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与C语言的 strstr() 以及 Java的 indexOf() 定义相符。

### 解法一：暴力解法

本题就是要实现一个indexOf函数，首先想到的就是双指针在两个串中比较。

暴力解法（BF）就依次扫描如果有相同的同步继续，出现不同就中断了，模式串回到起点主串回到下个开头点。也就是说主串的长度会遍历完，剩下的看模式串扫到什么时候中断。在最差的情况下每次模式串遍历到最后一个中断主串最末端才匹配到那就是O(n*m)

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AE%9E%E7%8E%B0%20strStr()/1.gif)

**边界**

* 索引越界

**细节**

* 循环结束条件
* haystack空返回0
* haystack比needle短返回-1


```java
public int strStr(String haystack, String needle) {
    char[] hay = haystack.toCharArray();
    char[] need = needle.toCharArray();
    int h = hay.length;
    int n = need.length;
    int i=0,j=0;
    while(j < h && i < n){
        if(hay[j] == need[i]){
            i++;
            j++;
        }else {
            j=j-i+1;
            i=0;
        }
    }
    if(i >= n) return j-i;
    else return -1;
}
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AE%9E%E7%8E%B0%20strStr()/1.png)

### 解法二：截取子串

除了两个指针依次比较之外，判断一个串是否含另一个串直接去依次截取目标长度的子串，判断有无相等的子串。这里截取我直接用的substring方法，这个也实现过很多次了，怎么写都一样但我们一定要知道它的实现是怎样的才能客观的分析它的复杂度，这里它就是一次遍历。因此下面代码的解法也是一个两层的遍历

```java
public int strStr(String haystack, String needle) {
    int n = needle.length(); 
    int h = haystack.length();
	for (int i = 0; i < h - n + 1; i++) {
        if (haystack.substring(i, i + n).equals(needle)) {
            return i;
        }
    }
	return -1;
}
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AE%9E%E7%8E%B0%20strStr()/2.png)

其实这种解法和解法一是一模一样只不过前者把截取和比较直接写了出来，它比解法一优的点只是少遍历了后面短的部分一个是h * (?)另一个是（h-n+1）* (?)，是因为两层遍历分开写可以这样做。外面遍历子串的开头，里面再遍历子串与模式串是否相等。而解法一放到了一个循环也做到了这个逻辑

### 总结

字符串匹配算法算是一个比较经典的算法，也是在计算机领域实际应用超多的算法。上面无论是解一还是解二其实都是全遍历比较。许多科学家都发明了更多减少比较次数的方式，比如RK算法、BM算法以及KMP算法。这些在这里暂时不介绍。之后在其他系列会出单篇讲解这类以及其他的算法。

