---
title: LC76_minWindowOC
date: 2020-07-14 23:43:25
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/minimum-window-substring/

<!--more-->

# 解法



```java
class Solution {
    public String minWindow(String s, String t) {
        if (s == null) {
            return "";
        }

        //needs存储t的<字符,出现次数>,windows存储<s中与t中字符相同的字符,出现次数>
        Map<Character, Integer> needs = new HashMap<>();
        Map<Character, Integer> windows = new HashMap<>();

        for (int i = 0; i < t.length(); i++) {
            //needs.getOrDefault(t.charAt(i),0)+1 含义是：needs如果包含t.charAt(i)，
            //则取出值+1;不包含取0+1
            char c = t.charAt(i);
            needs.put(c, needs.getOrDefault(c, 0)+1);
        }
        int left = 0, right = 0;
        int count = 0; //count代表符合要求的字符个数
        int minLen = Integer.MAX_VALUE;
        int start=0,end=0; // 记录位置
        while (right < s.length()) {
            // 获取字符
            char temp = s.charAt(right);
            // 如果是t中字符，在windows里添加，累计出现的次数
            if (needs.containsKey(temp)) {
                windows.put(temp, windows.getOrDefault(temp, 0)+1);
                // 注意：Integer不能用==比较，要用compareTo
                if (windows.get(temp).equals(needs.get(temp))) {
                    // 字符temp出现次数符合要求，count代表符合要求的字符个数
                    count++;
                }
            }
            //优化到不满足情况，right继续前进找可行解
            right++;
            //符合要求的字符个数正好是t中所有字符，获得一个可行解
            while(count == needs.size()) {
                // 更新结果
                if (right-left<minLen) {
                    start = left;
                    end = right;
                    minLen = end - start;
                }
                // 开始进行优化 缩小区间 删除s[left]
                char c = s.charAt(left);
                //当前删除的字符包含于t,更新Windows中对应出现的次数，如果更新后的次数<t中出现的次数，符合要求的字符数减1，下次判断count==needs.size()时不满足情况，
                if (needs.containsKey(c)) {
                    windows.put(c, windows.getOrDefault(c, 1)-1);
                    if (windows.get(c) < needs.get(c)) {
                        count--;
                    }
                }
                left++;
            }
        }
        // 返回覆盖的最小串
        return minLen==Integer.MAX_VALUE ?"":s.substring(start, end);
    }
}
```

