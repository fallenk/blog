---
title: 删除链表的倒数第N个节点
date: 2020-06-16 10:49:31
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list

<!--more-->



# 解法

## 方法一：两次遍历算法

### **思路**

简化成另一个问题：删除从列表开头数起的第(L-n+1)个节点，其中L是列表的长度，只要找到列表长度即可。

### 算法

首先我们将**添加一个哑结点作为辅助**，该结点位于列表头部。**哑结点用来简化某些极端情况**，例如列表中只含有一个结点，或需要删除列表的头部。

在第一次遍历中，我们找出列表的长度 L，然后设置一个指向哑结点的指针，并移动它遍历列表，直至它到达第 (L - n)个结点那里。我们把第 (L - n)个结点的 `next` 指针重新链接至第 (L - n + 2)结点，完成这个算法。

```java
class ListNode {
  int val;
  ListNode next;
  ListNode(int val) {
    this.val = val;
  }
}
public ListNode removeNthFromEnd(ListNode head, int n) {
  ListNode dummy = new ListNode(0);
  dummy.next = head;
  int length = 0;
  ListNode first = head;
  while(first != null) {
    length++;
    first = first.next;
  }
  length -= n;
  first = dummy;
  while(length>0) {
    length--;
    first = first.next;
  }
  first.next = first.next.next;
  return dummy.next;
}
```

## 方法二：一次遍历算法

算法

上述算法可以优化为**只使用一次遍历**。我们可以使用两个指针而不是一个指针。第一个指针从列表的开头向前移动 n+1 步，而第二个指针将从列表的开头出发。现在，这两个指针被 n 个结点分开。我们通过同时移动两个指针向前来保持这个恒定的间隔，直到第一个指针到达最后一个结点。此时第二个指针将指向从最后一个结点数起的第 n 个结点。我们重新链接第二个指针所引用的结点的 next 指针指向该结点的下下个结点。

```java
public ListNode removeNthNodeFromEnd(ListNode head, int n) {
	ListNode dummy = new ListNode(0);
	dummy.next = head;
	ListNode first = dummy;
	ListNode second = dummy;
	for(int i=1; i<=n+1; i++) {
		first = first.next;
	}
	while(first != null) {
		first = first.next;
		second = second.next;
	}
	second.next = second.next.next;
	return dummy.next;
}
```

