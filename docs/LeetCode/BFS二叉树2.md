---
title: BFS二叉树2
date: 2020-06-16 22:38:39
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/binary-tree-level-order-traversal/

<!--mroe-->



# 解法

```java
public List<List<Integer>> levelOrder(TreeNode root) {
    Queue<TreeNode> queue = new LinkedList<>();
    List<List<Integer>> res = new ArrayList<>();
    if (root != null) queue.add(root);
    while (!queue.isEmpty()) {
        List<Integer> tmp = new ArrayList<>();
        for (int i=queue.size(); i>0; i--) {
            TreeNode node = queue.poll();
            tmp.add(node.val);
            if (node.left != null) queue.add(node.left);
            if (node.right != null) queue.add(node.right);
        }
        res.add(tmp);
    }
    return res;
}
```