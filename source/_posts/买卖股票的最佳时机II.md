---
title: LeetCode初级算法之数组：122.买卖股票的最佳时机II
date: 2020-09-10 12:32:43
tags: LeetCode日常
categories: 
- 算法
- LeetCode
---

#### 买卖股票的最佳时机 II

题目地址：https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。<!--more-->

**注意：**你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例 1:**

```java
输入: [7,1,5,3,6,4]
输出: 7
解释: 
在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 
这笔交易所能获得利润 -1 + 5 = 4 。
随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 
这笔交易所能获得利润 -3 + 5 = 3 。
```

**示例 2:**

```
输入: [1,2,3,4,5]
输出: 4
解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
注意：你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```

**示例 3:**

```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```



---

#### 问题信息

输入：数组（表示几天的股价）

输出：整数（不同买进卖出的组合找到最大利润）

额外条件：买和卖可以多次但必须交替进行

#### 思考

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BA%20II/1.png)

可多次买进卖出但必须交替保持一次交易，累计最大的场景实际就为波差之和。对于例1来说就是2-3天的差加上4-5天的差

**示例 4:**

```
输入: [7,1,2,4,2,4,3]
输出: 5
解释：如下图2-4，5-6两段时间的价格差之和即位最高利润
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BA%20II/2.png)

那么也就是需要两个指针一个标记低位，一个往后扫描每次移动并与之前比较。当扫描指针当前比前一个要小则移动低位指针到当前扫描指针。

#### 总结代码

```java
int i = 0;//低位指针
int nums = 0;//利润
for(int j = 1; j < prices.length; j++){
    /*
    当出现下降则用前一个高峰减低位指针，再移动低位指针
    但到数组结束有可能不出现低位一直增长就有else if
    */
    if(prices[j]<prices[j-1]){
        nums += prices[j-1]-prices[i];
        i = j;
    }else if(j == prices.length){
        nums += prices[j]-prices[i];
    }
}
return nums;
```

