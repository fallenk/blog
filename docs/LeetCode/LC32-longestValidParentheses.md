---
title: LC32_longestValidParentheses
date: 2020-07-04 15:05:46
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/longest-valid-parentheses/

<!--more-->



# 解法

错误解法: dp不熟练！！思路不清晰

```
public static int longestValidParentheses(String s) {
        if (s==null||s.length()==0) {
            return 0;
        }
        int len = s.length();
        int[] countLeft = new int[len];
        int left = 0;
        for (int i = 0; i < len; i++) {
            if (s.charAt(i) == '(') {
                left++;
                countLeft[i] = -1; //不用
            } else {
                if (left > 0) {
                    countLeft[i] = left;
                    left--;
                } else {
                    left = 0;
                    countLeft[i] = left;
                }
            }
        }

        int[] dp = new int[len+1];
        int max = Integer.MIN_VALUE;
        boolean isStart = false;
        dp[0] = 0;
        for (int i = 1; i < dp.length; i++) {
            if (!isStart) {
                if (s.charAt(i-1)== '(') {
                    dp[i] = dp[i-1];
                } else {
                    if (countLeft[i-1] > 0) {
                        dp[i] = dp[i-1]+2;
                    } else {
                        dp[i] = dp[i-1];
                        isStart = true;
                    }
                }  
            } else {
                dp[i] = 0;
                isStart = false;
            }
            max = Math.max(dp[i], max);
        }
        
        return max;
    }
```



```java
public static int longestValidParentheses1(String s) {
        if (s==null||s.length()==0) {
            return 0;
        }
        int len = s.length();
        int[] dp = new int[len]; // dp[i]代表 i结尾的有效串的长度
        int maxans = 0;
        dp[0] = 0;
        for(int i=1; i<len; i++) {
            if(s.charAt(i) == ')') {
                if(s.charAt(i-1) == '(') {
                    dp[i] = (i>=2?dp[i-2]:0) + 2;
                } else if ((i-dp[i-1]-1>=0) && s.charAt(i-dp[i-1]-1)=='(') {
                    dp[i] = dp[i-1]+2 + (i-dp[i-1]-2>=0?dp[i-dp[i-1]-2]:0);
                }
            }
            maxans = Math.max(maxans, dp[i]);
        }
        return maxans;
    }
```



```java
/**
     * 栈，保证栈底元素 一定是最后一个未匹配的右括号
     * @param s
     * @return
     */
    public static int longestValidParentheses(String s) {
        int maxans = 0;
        Deque<Integer> stack = new LinkedList<>();
        stack.push(-1);
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                stack.push(i);
            } else {
                stack.pop();
                if (stack.isEmpty()) {
                    stack.push(i);
                } else {
                    maxans = Math.max(maxans, i-stack.peek());
                }
            }
        }
        return maxans;
    }
```



```java
public static int longestValidParenthesesX(String s) {
        int left = 0, right =0, maxLenght = 0;
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '(') {
                left++;
            } else {
                right++;
            }
            if (left == right) {
                maxLenght = Math.max(maxLenght, 2*right);
            } else if (right > left) {
                left = right = 0;
            }
        }
        left = right = 0;
        for (int i = s.length()-1; i >=0 ; i--) {
            if (s.charAt(i) == '(') {
                left++;
            } else {
                right++;
            }
            if (left == right) {
                maxLenght = Math.max(maxLenght, 2*left);
            } else if (left > right) {
                left = right = 0;
            }
        }
        return maxLenght;
    }

```

