---
title: LeetCode初级算法-数组-两个数组的交集 II
date: 2020-09-20 11:32:48
tags: LeetCode
---

#### 两个数组的交集 II

给定两个数组，编写一个函数来计算它们的交集。<!--more-->

**示例 1：**

```
输入：nums1 = [1,2,2,1], nums2 = [2,2]
输出：[2,2]
```

**示例 2:**

```
输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]
输出：[4,9]
```

**说明：**

- 输出结果中每个元素出现的次数，应与元素在两个数组中出现次数的最小值一致。
- 我们可以不考虑输出结果的顺序。

**进阶：**

- 如果给定的数组已经排好序呢？你将如何优化你的算法？
- 如果 *nums1* 的大小比 *nums2* 小很多，哪种方法更优？
- 如果 *nums2* 的元素存储在磁盘上，内存是有限的，并且你不能一次加载所有的元素到内存中，你该怎么办？



---

#### 题目信息

输入：两个整数数组

输出：交集数组

额外：不考虑顺序

#### 思考

就相当于是数学集合求交集，很容易想到的就是双指针扫描比较判断是否存入结果。对于这样的方式就选择先排序再比较。

```
nums1 = [4,5,9], nums2 = [4,4,8,9,9]

nums1[0] == nums2[0] true 存4 
nums1[1] == nums2[1] false且> nums1指针不变
nums1[1] == nums2[2] false且< nums2指针不变
nums1[2] == nums2[2] false且> nums1指针不变
nums1[2] == nums2[3] true 存9 
nums1已遍历完毕结束结果为[4,9]
```

```java
public int[] intersect(int[] nums1, int[] nums2) {
    //排序
	Arrays.sort(nums1);
    Arrays.sort(nums2);
    //两个指针
    int i = 0;
    int j = 0;
    //结果数组及其存储下标
    int[] result = new int[nums1.length];
    int index = 0;
    while(i<nums1.length && j<nums2.length){
    	if(nums1[i] == nums2[j]){
            result[index] = nums1[i];
            i++;
            j++;
            index++;
        }else if(nums1[i] > nums2[j]){
            j++;
        }else{
            i++;
        }
    }
    return Arrays.copyOfRange(result,0,index);
}
```

空间复杂度O(n)，时间复杂度O(nlogn+mlogm)。

和前面写过的几道题一样都是除了双指针扫描之外还可以使用hash表来解决并且时间复杂度要比扫描比较的方式优。这里找到相同的值同样用hash表记录然后另一个再到hash表里比对。这样时间复杂度为O(n+m)

```java
 public int[] intersect(int[] nums1, int[] nums2) {
    int[] result = new int[nums1.length];
    int index = 0;
    Map<Integer,Integer> map = new HashMap();
    for(int num1 : nums1){
        int count = map.getOrDefault(num1,0)+1;
        map.put(num1,count);
    }
    for(int num2 : nums2){
		int count = map.getOrDefault(num2, 0);
            if (count > 0) {
                result[index] = num2;
                index++;
                count--;
                if (count > 0) {
                    map.put(num2, count);
                } else {
                    map.remove(num2);
                }
            }
    }
    return Arrays.copyOfRange(result,0,index);
}
```

#### 总结

两种方法一种两个数组排序使用双指针同步比较，第二种先将一个数组统计到hash表另一个再对照查找存不存在，对于进阶里面的三条第一条是已经排好序那么遍历直接比较即可时间复杂度O(n+m)，如果两数组长度差距大的话首先空间优化创建的result数组用小的那个容量创建其他就是对使用hash表的方式有影响用短的数组记录。最后一种nums2过大就是内存不过并不能直接全部排序比较，那就使用hash表的方式，hash表统计完nums1然后nums2只需要一个一个对照即可不需要全部纳入。