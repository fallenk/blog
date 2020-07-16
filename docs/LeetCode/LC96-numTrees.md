---
title: LC96_numTrees
date: 2020-07-15 22:21:41
tags: [LeetCode]
---



# 题目

https://leetcode-cn.com/problems/unique-binary-search-trees/

<!--more-->



# 解法

~~~
public int numTrees(int n) {
        if (n < 0) {
            return -1;
        }
        if (n <= 1) {
            return 1;
        }

        int[] dp = new int[n+1]; //
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i < n+1; i++) {
            for (int j = 1; j <= i; j++) {
                dp[i] += dp[j-1] * dp[i-j];
            }
        }
        return dp[n];
    }
~~~



