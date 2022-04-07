---
title: LeetCode初级算法之字符串：8.字符串转换整数 (atoi)
date: 2020-12-04 20:33:57
tags: LeetCode日常
---

#### 字符串转换整数 (atoi)

题目地址：https://leetcode-cn.com/problems/string-to-integer-atoi/

请你来实现一个 atoi 函数，使其能将字符串转换成整数。<!--more-->

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：

* 如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。

* 假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。

* 该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。

**注意：**假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。

在任何情况下，若函数不能进行有效的转换时，请返回 0 。

**提示：**

本题中的空白字符只包括空格字符 ' ' 。
假设我们的环境只能存储 32 位大小的有符号整数，那么其数值范围为 [−231,  231 − 1]。如果数值超过这个范围，请返回  INT_MAX (231 − 1) 或 INT_MIN (−231) 。

**示例 1:**

> 输入: "42"
> 输出: 42

**示例 2:**

> 输入: "   -42"
> 输出: -42
> 解释: 第一个非空白字符为 '-', 它是一个负号。
>      我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。

**示例 3:**

> 输入: "4193 with words"
> 输出: 4193
> 解释: 转换截止于数字 '3' ，因为它的下一个字符不为数字。

**示例 4:**

> 输入: "words and 987"
> 输出: 0
> 解释: 第一个非空字符是 'w', 但它不是数字或正、负号。因此无法执行有效的转换。

**示例 5:**

> 输入: "-91283472332"
> 输出: -2147483648
> 解释: 数字 "-91283472332" 超过 32 位有符号整数范围。 因此返回 INT_MIN (−231) 。





**解法一**

严格来说这个问题其实没有太多考察算法的内容不涉及数据结构、算法思维的选取。它是一个数据的效验筛选，更多的像开发场景中的一些原始数据的处理参数的筛选。所以不难但有较多的判断语句涉及一些细节问题需要仔细对比

**主体**

1. 去掉前导空格
2. 处理正负号
3. 识别数字

**边界**

1. 整数溢出
2. 索引越界

**细节**

1. 无效转换返回0

```java
public int myAtoi(String str) {
    char[] chars = str.toCharArray();
    int n = chars.length;
    int index = 0;
    //1.去掉前导空格
    while (index < n && chars[index] == ' ') {
        index++;
    }
    //2.符号处理
    int sign = 1;
    if(index < n && chars[index] == '+'){
        index++;
    }else if(index < n && chars[index] == '-'){
        sign = -1;
        index++;
    }
    //3.拼接数字
    int result = 0;
    while (index < n && chars[index]-'0' >= 0 && chars[index]-'0' <= 9) {
        int num = chars[index] - '0';
        if (result > (Integer.MAX_VALUE - num) / 10) {
            // 本来应该是 result * 10 + num > Integer.MAX_VALUE
            // 但是 *10 和 + num 都有可能越界,所以在上面逆向判断
            return sign < 0 ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        }
        result = result * 10 + num;
        index++;
    }
    return result * sign;
}
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E6%95%B4%E6%95%B0%20(atoi)/1.png)

**解法二：正则表达式**

这样一个筛选串的过程我们除了自己去去遍历判断，也可以使用正则表达式的方式。用到Java类库的相关工具但本质上还是和解法一是一样的。需要引入java.util.regex包下的Pattern与Matcher

```java
public int myAtoi(String str) {
    str = str.trim();
    Pattern pattern = Pattern.compile("[-+]??[0-9]++");
    Matcher matcher = pattern.matcher(str);
    if (matcher.lookingAt()) {
        String num = str.substring(0, matcher.end());
        try {
            return Integer.parseInt(num);
        } catch (NumberFormatException nfe) {
            return num.charAt(0) == '-' ? Integer.MIN_VALUE : Integer.MAX_VALUE;
        }
    }
    return 0;
}
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E6%95%B4%E6%95%B0%20(atoi)/2.png)

**解法三：有限状态自动机**

正则表达的所匹配的所有字符串构成都可以用有限自动机识别，其实上面解法的每个过程判定就是一个有限自动机的每个状态。从去除空格阶段到取符号阶段到数字阶段到结束。和KMP一样都可解决字符串匹配相关问题

状态机里面有 4 个概念：

* State ，状态。一个状态机至少要包含两个状态。
* Event ，事件。事件就是执行某个操作的触发条件或者口令。
* Action ，动作。事件发生以后要执行动作。
* Transition ，变换。也就是从一个状态变化为另一个状态。

套到这道题里就是我们的程序在每个时刻有一个状态 `s` ，每次从序列中输入一个字符 `c` ，并根据字符 `c` 转移到下一个状态 `s'` 。这样，我们只需要建立一个覆盖所有情况的从 `s` 与 `c` 映射到 `s'` 的表格即可解决题目中的问题。

答案中给出的示例图是这样的：

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E6%95%B4%E6%95%B0%20(atoi)/4.png)

用图表来表示：

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E6%95%B4%E6%95%B0%20(atoi)/3.png)

我又画了下面一张图和上面两个同义。（0表示状态start，1表示状态signed，2表示状态int_number，3表示状态end，'其他'表示除[空格、正负号、数字]之外的字符）

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E6%95%B4%E6%95%B0%20(atoi)/5.png)

意思就是说依次输入字符。首先字符是进入状态0（start）如果字符是空格那么下个仍然进入状态0.如果是其他则进入状态3（end）结束处理。那我们就可以实现这样一个自动机

```java
class Automata{
    //下次的状态
    private int state=0;
    //状态表
    private int[][] table={{0,1,2,3},{3,3,2,3},{3,3,2,3},{3,3,3,3}};
    //字符的四种情况
    public int getCharState(char c){
        if(c==' ')return 0;
        if(c=='+'|| c=='-')return 1;
        if(Character.isDigit(c))return 2;
        return 3;
    }
    int result = 0; //结果
    int sign = 1; //符号
    public void input(char c){
        //通过这次的字符设定下个状态
        state=table[state][getCharState(c)];
        //state == 0 直接过
        if(state==1 &&c=='-') sign=-1;
        if(state==2){
            int num = c - '0';
            //除了通过当前状态和字符来判断下个去往状态
            //溢出发生时其实也应该把状态设为3（end）
            if (result > (Integer.MAX_VALUE - num) / 10) {
                result = sign < 0 ? Integer.MIN_VALUE : Integer.MAX_VALUE;
                sign = 1;
                state = 3;
            }else result = result * 10 + num;
        }
        //state == 3 直接过
    }
}
```
然后就是使用我们的自动机解题啦
```java
public int myAtoi(String str) {
    Automata auto=new Automata();
    char[] chars=str.toCharArray();
    for(char c:chars){
        auto.input(c);
    }
    return auto.sign*auto.result;
}
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E6%95%B4%E6%95%B0%20(atoi)/6.png)

或者state为3这边也直接结束不去掉用空方法

```java
public int myAtoi(String str) {
    Automata auto=new Automata();
    char[] chars=str.toCharArray();
    int i = 0;
    while(i<chars.length && auto.state != 3){
        auto.input(chars[]);
        i++;
    }
    return auto.sign*auto.result;
}
```

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E6%95%B4%E6%95%B0%20(atoi)/7.png)

#### 总结

就这题本身来讲是比较简单的，主要是处理细节问题可能很多小伙伴在像解法一的处理过程中没有组织清晰导致虽然能解出题但有很多代码是冗余的或者重复判断了的地方。最终解法一其实也不过两个if而已没有像传闻中那样说很多ifelse，三个处理第一个空格处理是循环处理，第二符号只有首位所以单个if，第三个数字也是循环处理。因此三个必要的判断两个是while一个是if。最后还加上一个溢出的判断。除此之外这题也有学习到的地方，第一是对正则的回顾，第二是引出有限状态自动机这样一个计算机思想也是等于又温习下离散数学。对于这题没有什么算法思维理清思路书写下来就是解法一的这样一个过程。因此这一题的意图可能就是让我们学习到有限状态自动机封装这样一个机器或者规则往这个规则里输入语言串它去给我们输出相应的结果。

