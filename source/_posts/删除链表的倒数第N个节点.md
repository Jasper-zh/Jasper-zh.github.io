---
title: LeetCode初级算法之链表：19.删除链表的倒数第N个节点
date: 2020-12-13 17:58:39
tags: LeetCode日常
categories: 
- 算法
- LeetCode
---

### 删除链表的倒数第N个节点

> 题目地址：https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。<!--more-->

**示例：**
```
给定一个链表: 1->2->3->4->5, 和 n = 2.
当删除了倒数第二个节点后，链表变为 1->2->3->5.
```
**说明：**
给定的 n 保证是有效的。

**进阶：**
你能尝试使用一趟扫描实现吗？


### 解法一：倒数转正数
很自然的是我只能通过头节点**head**多次的**next**，找到要被删除的节点，但我们获取的定位是**倒数**第几个。直观的就是我们通过总长度减去倒数就是我们的next**次数**
```java
public ListNode removeNthFromEnd(ListNode head, int n){
    // 1.统计链表长度
    int count = 0;
    ListNode node = head;
    while(node != null){
        count++;
        node = node.next;
    }
    //重置node
    node = head;
    // 2.找到被删除节点的前一个
    for(int i = 0; i < count - n - 1; i++){
        node = node.next;
    }
    // 3.删除操作
    if(count == n){
        //如果是头节点
        head = head.next;
    }else{
        node.next = node.next.next;
    }
    return head;
}
```
![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9/1.png)
现在我们是完成了这样一个思路，但有个地方可以做一个调整，也是要养成的一个习惯。对于一个链表如下图：
![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9/2.png)
它是按某种应用逻辑存储的内容，内容都会有操作有修改的时候，因此为了方便操作以及操作的安全性。通常都会在真正存储数据的链头之前加一个节点它的值和内容无关，用这个额外的节点来做真正的链表节点。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9/3.png)

我们第三步删除操作之所以这样分开操作就是因为，我们拿到的是删除节点之前的节点因此删除是头结点的话需要单独处理，这下就不需要了。最后我们去返回数据链的头，也就是真正头的next
```java
public ListNode removeNthFromEnd(ListNode head, int n){
    //加一个头结点
    ListNode listHead = new ListNode(0,head);
    // 1.统计链表长度
    int count = 0;
    ListNode node = head;
    while(node != null){
        count++;
        node = node.next;
    }
    // 重置node
    node = listHead;
    // 2.找到被删除节点的前一个(比之前多一次)
    for(int i = 0; i < count - n; i++){
        node = node.next;
    }
    // 3.删除操作
    node.next = node.next.next;
    return listHead.next;
}
```
时间O（n），空间O（1）
### 解法二：栈
无论是倒序啊还是倒数都可以想到栈这样一个东西，上个解法我们完整遍历一次统计长度，把倒数转为正数再进行正数次数的next取被删除节点的前驱节点，这次一样进行完整遍历都放到我们的栈里面。

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9/1.gif)

之后再进行倒数次数的出栈操作，之后剩下的栈顶即是被删除节点的前驱节点



![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9/2.gif)

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    // 创建真头结点
    ListNode listHead = new ListNode(0, head);
    // 创建栈
    Deque<ListNode> stack = new LinkedList<ListNode>();
    // 从真头结点开始存入
    ListNode node = listHead;
    while (node != null) {
        stack.push(node);
        node = node.next;
    }
    // 依次弹出
    for (int i = 0; i < n; i++) {
        stack.pop();
    }
    // 取栈顶即被删节点的前驱节点
    node = stack.peek();
    // 删除操作
    node.next = node.next.next;
    return listHead.next;
}
```
时间O（n），创建了栈空间O（n）

### 解法三：一次遍历

两个解法都用到了两次遍历，那么我们有没有方法可以在一次遍历中完成呢？

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9/1.jpg)

它就是我们处理链表的经典方式快慢指针,我们用两个指针，快指针领先n次（倒数次数），慢指针在起点，同时迭代。当快指针到了终点，那慢指针岂不是到了倒数第n个。fast起点可以取后一格那么slow就能拿到倒数第n个的前一个节点

![](https://gitee-blogimage.oss-cn-beijing.aliyuncs.com/blogImage/%E5%88%A0%E9%99%A4%E9%93%BE%E8%A1%A8%E7%9A%84%E5%80%92%E6%95%B0%E7%AC%ACN%E4%B8%AA%E8%8A%82%E7%82%B9/3.gif)

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode listHead = new ListNode(0, head);
    // 取的是被删除节点的前驱节点，fast初始化多一格
    ListNode fast = head;
    ListNode slow = listHead;
    int i = 0;
    while (fast != null) {
        // fast先移动n，之后在同步移动
        fast = fast.next;
        if(i>=n) slow = slow.next;
        i++;
    }
    // 直到fast遍历完，slow就停在倒数第n个的前个
    slow.next = slow.next.next;
    return listHead.next;
}
```
时间O（n）,空间O（1）

### 总结

三种解法前两种解法比较常规就顺题意推导正着找和反着找，第三种快慢指针是链表比较常用的一种方式。比如有环，追击问题等等。

