---
title: 包含min的栈
date: 2020-06-16 11:36:19
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof

<!--more-->

# 解法



```java
class MinStack {

    Stack<Integer> A, B;
    public MinStack() {
        A = new Stack<>();
        B = new Stack<>();
    }
    public void push(int x) {
        A.add(x);
        if(B.empty() || B.peek() >= x)
            B.add(x);
    }
    public void pop() {
        if(A.pop().equals(B.peek()))
            B.pop();
    }
    public int top() {
        return A.peek();
    }
    public int min() {
        return B.peek();
    }
}
```



