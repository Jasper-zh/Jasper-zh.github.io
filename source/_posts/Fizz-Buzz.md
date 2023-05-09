---
title: LeetCode初级算法之数学问题：412.Fizz Buzz
date: 2021-05-22 11:18:18
tags: LeetCode日常
categories: 
- 算法
- LeetCode
---

#### 题目信息
>题目地址：https://leetcode-cn.com/problems/fizz-buzz/

写一个程序，输出从 1 到 n 数字的字符串表示。

1. 如果 n 是3的倍数，输出“Fizz”；
2. 如果 n 是5的倍数，输出“Buzz”；
3. 如果 n 同时是3和5的倍数，输出 “FizzBuzz”。

**示例：**
```java
n = 15,

返回:
[
    "1",
    "2",
    "Fizz",
    "4",
    "Buzz",
    "Fizz",
    "7",
    "8",
    "Fizz",
    "Buzz",
    "11",
    "Fizz",
    "13",
    "14",
    "FizzBuzz"
]
```
#### 题解一
这个题解的话直接按照意思来就可以了
```java
public List<String> fizzBuzz(int n) {
	List<String> res = new ArrayList();
	for (int i = 1; i <= n; i++){
		if(i%3==0 && i%5==0){
			res.add("FizzBuzz");
		} else if(i%3==0){
			res.add("Fizz");
		} else if(i%5==0){
			res.add("Buzz");
		} else{
			res.add(String.valueOf(i));
		}
	}
	return res;
}
```
#### 题解二
这题解题确实是简单，但题可能是希望考虑到代码的黏性减少代码重复降低耦合，所以官方解题也举了例子（假设题目条件不仅仅两个）。<br>
这边也写一下吧，就是说有几种情况就我们就自己手动组合后列举，代码量就会大全都是if<br>
**示例**
```java
/*
 * 假设有三个条件了 
 * i = 3 Three
 * i = 5 Five
 * i = 7 Seven
 * 那么会组合出8种分支
 */
if(i%3==0 && i%5==0 && i%7==0){
	res.add("ThreeFiveSeven");
} else if(i%3==0 && i%5==0){
	res.add("ThreeFive");
} else if(i%3==0 && i%7==0){
	res.add("ThreeSeven");
} else if(i%5==0 && i%7==0){
	res.add("FiveSeven");
} else if(i%3==0){
	res.add("Three");
} else if(i%5==0){
	res.add("Five");
} else if(i%7==0){
	res.add(Seven);
} else {
	res.add(String.valueOf(i));
}
```
如果更多一点，那这个if分支就太大量了。其实很简单我们平时写代码即使我们不太注重这个随意的去写，也不会像上面这样写。举个例子：<br>
**示例**
```java
/*
 * str开头若是'a' 执行a方法
 * str结尾若是'b' 执行b方法
 * 和题目一样两个都满足当然是都要执行的
 */
if(str.charAt(0) == 'a'){
	a();
}
if(str.charAt(length-1) == 'b'){
	b();
}
```
一般会像上面这样两个单if，而不是如下：
```java
if(str.charAt(0) == 'a' && str.charAt(length-1) == 'b'){
	a();
	b();
} else if(str.charAt(0) == 'a'){
	a();
} else if(str.charAt(length-1) == 'b'){
	b();
}
```
所以这题也是一样
```java
public List<String> fizzBuzz(int n) {
	List<String> res = new ArrayList();
	for (int i = 1; i <= n; i++){
		String str = "";
		if (i%3 == 0) {
			str += "Fizz";
		}
		if (i%5 == 0) {
			str += "Buzz";
		}
		if (str.equals("")) {
			str += String.valueOf(i);
		}
		res.add(str);
	}
	return res;
}
```

#### 题解三
像题解二我们使用单if之后，不去手动组合。分支数量会下降，并且结构更清晰（避免嵌套或者多分支），虽然倍率下降但还是会随着条件的增多而增多if。所以若是有很多条件的情况下不妨去存在容器里把**很多if变成循环+if**
```java
Map<Integer,String> map = new HashMap();
map.put(3,"Fizz");
map.put(5,"Buzz");
```
```java
for (Integer key : map.keySet()) {
	if (num % key == 0) {
		str += map.get(key);
	}
}
```
所以代码如下：
```java
public List<String> fizzBuzz(int n) {
	Map<Integer,String> map = new HashMap();
	map.put(3,"Fizz");
	map.put(5,"Buzz");
	List<String> res = new ArrayList();
	for (int i = 1; i <= n; i++){
		String str = "";
		for (Integer key : map.keySet()) {
			if (i % key == 0) {
				str += map.get(key);
			}
		}
		if (str.equals("")) {
			str += String.valueOf(i);
		}
		res.add(str);
	}
	return res;
}
```

#### 总结
这题本身是简单的，但主要是怎么去处理变得通用，就像在过去我们学习javaEE去改写通用servlet一样，把n个方法名的判断通过泛型给免去了，这里也是一样n个if不可能都写出来换成循环遍历n个if，其实还有改进的点，就比如像在题解二举得例子如果说这两个条件都满足所以两个方法都执行，但是它们需要有先后顺序么？如果实际的逻辑是需要的那么解法二可以手动调整单if顺序，但解法三不行因为hashmap的keySet()方法取是乱序的，但可以使用TreeMap或LinkMap.TreeMap是按key升序，而LinkMap是按put的顺序