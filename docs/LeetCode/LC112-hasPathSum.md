---
title: LC112_hasPathSum
date: 2020-07-07 10:45:56
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/path-sum/

<!--more-->

# 解法

核心思想是对树进行一次遍历，在遍历时记录从根节点到当前节点的路径和，以防止重复计算。

做为树的递归题目是非常有套路可循的，因为树有两个分支，所以在递归里也有两个分支，一般是通过 递归 A（||，&&）递归 B 来实现分支的。只要明白了这一点，递归函数就不会很难设计。



```java
public class LC112_hasPathSum {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root==null) {
            return false;
        }
        
        return dfs(root, sum);
    }
    // in: 树  out: 是否是路径;  判断路径
    private boolean dfs(TreeNode root, int sum) {
        if (root == null) {
            return false;
        }
       int current = sum - root.val;
       if (root.left==null && root.right==null&&current == 0) {
           return true;
        }
        if(root.left==null && root.right==null &&current != 0) {
            return false;
        }
        boolean isleft = dfs(root.left, current);
        boolean isright = dfs(root.right, current);
        return isleft||isright;
    }
}
```

比较当前的root的value == sum

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) {
            return false;
        }
        if (root.left == null && root.right == null) {
            return sum == root.val;
        }
        return hasPathSum(root.left, sum - root.val) || hasPathSum(root.right, sum - root.val);
    }
}
```

回溯
这里的回溯指 利用 DFS 找出从根节点到叶子节点的所有路径，只要有任意一条路径的 和 等于 sum，就返回 True。

下面的代码并非是严格意义上的回溯法，因为没有重复利用 path 变量。

