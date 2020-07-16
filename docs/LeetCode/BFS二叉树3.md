---
title: BFS二叉树3
date: 2020-06-16 22:55:04
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof

<!--more-->

# 解法

```java
class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
Queue<TreeNode> queue = new LinkedList<>();
        List<List<Integer>> res = new ArrayList<>();
        boolean flag = true;
        if (root != null) queue.add(root);
        while (!queue.isEmpty()) {
            List<Integer> list = new ArrayList<>();
            for (int i= queue.size(); i>0; i--) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) queue.add(node.left);
                if (node.right != null) queue.add(node.right);
            }
//            if (res.size() %2 == 1) Collections.reverse(list);
            // 以上都是正常打印代码，和上一题一样；
            // 判断是否转置（true不转置，false转置）
            if(!flag)
                list = reverse(list);
            // 每遍历一层将flag取反，实现一层转置，下一层不转置；
            flag = !flag;
            res.add(list);
        }
        return res;
    }
    // 转置数组
    public List<Integer> reverse(List<Integer> list){
        List<Integer> res = new ArrayList<>();
        for(int i = list.size()-1; i >= 0; i --){
            res.add(list.get(i));                     //转置
        }
        return res;
    }
}
```

