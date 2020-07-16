---
title: BFS二叉树
date: 2020-06-16 22:25:35
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof

<!--more-->

# 解法

```java
public int[] levelOrder(TreeNode root) {
        if (root == null) return new int[0];

        Queue<TreeNode> queue = new LinkedList<TreeNode>(){{ add(root); }};
        ArrayList<Integer> ans = new ArrayList<>();
        while (!queue.isEmpty()) {
            TreeNode node = queue.poll();
            ans.add(node.val);
            if (node.left != null) queue.add(node.left);
            if (node.right != null) queue.add(node.right);
        }
        int[] res = new int[ans.size()];
        for (int i=0; i<ans.size(); i++) {
            res[i] = ans.get(i);
        }
        return res;
    }
}
```

