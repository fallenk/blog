---
title: LeetCode-排序
date: 2019-05-14 21:40:02
tags: [刷题, 排序]
category: [LeetCode]
---

# 思想

如何写程序：**分析程序代码：入口，分叉逻辑(各个分支情况列举)，出口考虑(各种边界条件,特别是=的情况)**

控制结束的条件，初始，各个分支情况流程逻辑；结束时的条件:(正常结束，异常结束，边界值)

<!--more-->

快速选择：用于求解Kth Element问题，也就是第K个元素的问题。

可以使用快速排序的partition()进行实现。需要先打乱数组，否则最坏情况下时间复杂度是$O\left(N^{2}\right)$.

# 堆

用于求解TopK Elements问题，也是就是K个最小元素的问题。可以维护一个**大小为K的最小堆**，最小堆中的元素就是最小元素。**最小堆要用大顶堆来实现**，大顶堆表示堆顶元素是堆中最大元素。这是因为我们要得到k个最小的元素，因此当遍历到一个新的元素时，需要**知道这个新元素是否比堆中的最大元素更小，更小的话就把堆中的最大元素去除，并将新元素添加到堆中**。所以我们需要很容易的得到最大元素并移除最大元素，大顶堆可以很好的满足这个要求。

堆也是用于与求解Kth Element问题，**得到了大小为k的最小堆之后，因为使用了大顶堆来实现，因此堆顶元素就是第K大的元素**。

**快速选择**也可以求解TopK Elements问题，**因为找到Kth Element之后，遍历一次数组，所有小于等于KthElement的元素都是TopK Elements。**

可以看到，快速选择和堆排序都可以求解 Kth Element 和 TopK Elements 问题。第K个元素和前K元素

## [215. Kth Largest Element in an Array (Medium)](https://leetcode.com/problems/kth-largest-element-in-an-array/description/)

Kth Element

Find the **k**th largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Note:** 
You may assume k is always valid, 1 ≤ k ≤ array's length.

题目描述：找到倒数第 k 个的元素。

solution 1：

```C++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());
        return nums[nums.size() - k];
    }
};	
```

solution 2:

```C++
class Solution {
	public:
			int findKthLargest(vector<int>& nums, int k) {
					priority_queue<int> q(nums.begin(), nums.end());
					for(int i = 0; i < k, i++) {
							q.pop();
					}
					return q.top();
			}
};
```

熟悉快排：核心思想是**每次都要先找一个中枢点Pivot**，**然后遍历其他所有的数字**，像这道题从大往小排的话，就把大于中枢点的数字放到左半边，把小于中枢点的放在右半边，这样中枢点是整个数组中第几大的数字就确定了，虽然左右两部分各自不一定是完全有序的，但是并不影响本题要求的结果，因为左半部分的所有值都大于右半部分的任意值，所以我们求出中枢点的位置，如果正好是k-1，那么直接返回该位置上的数字；如果大于k-1，说明要求的数字在左半部分，更新右边界，再求新的中枢点位置；反之则更新右半部分，求中枢点的位置；

# [桶排序](https://cyc2018.github.io/CS-Notes/#/notes/Leetcode 题解 - 排序?id=桶排序)

## [1. 出现频率最多的 k 个元素](https://cyc2018.github.io/CS-Notes/#/notes/Leetcode 题解 - 排序?id=_1-出现频率最多的-k-个元素)

347. Top K Frequent Elements

     Given a non-empty array of integers, return the **k** most frequent elements.

```Java
public List<Integer> topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> frequencyForNum = new HashMap<>();
    for (int num : nums) {
        frequencyForNum.put(num, frequencyForNum.getOrDefault(num, 0) + 1);
    }
    List<Integer>[] buckets = new ArrayList[nums.length + 1];
    for (int key : frequencyForNum.keySet()) {
        int frequency = frequencyForNum.get(key);
        if (buckets[frequency] == null) {
            buckets[frequency] = new ArrayList<>();
        }
        buckets[frequency].add(key);
    }
    List<Integer> topK = new ArrayList<>();
    for (int i = buckets.length - 1; i >= 0 && topK.size() < k; i--) {
        if (buckets[i] == null) {
            continue;
        }
        if (buckets[i].size() <= (k - topK.size())) {
            topK.addAll(buckets[i]);
        } else {
            topK.addAll(buckets[i].subList(0, k - topK.size()));
        }
    }
    return topK;
}
```

设置若干个桶，每个桶存储出现频率相同的数。桶的下标表示数出现的频率，即第 i 个桶中存储的数出现的频率为 i。把数都放到桶之后，从后向前遍历桶，最先得到的 k 个数就是出现频率最多的的 k 个数。

```java
public List<Integer> topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> frequencyForNum = new HashMap<>();
    for (int num : nums) {
        frequencyForNum.put(num, frequencyForNum.getOrDefault(num, 0) + 1);
    }
    List<Integer>[] buckets = new ArrayList[nums.length + 1];
    for (int key : frequencyForNum.keySet()) {
        int frequency = frequencyForNum.get(key);
        if (buckets[frequency] == null) {
            buckets[frequency] = new ArrayList<>();
        }
        buckets[frequency].add(key);
    }
    List<Integer> topK = new ArrayList<>();
    for (int i = buckets.length - 1; i >= 0 && topK.size() < k; i--) {
        if (buckets[i] == null) {
            continue;
        }
        if (buckets[i].size() <= (k - topK.size())) {
            topK.addAll(buckets[i]);
        } else {
            topK.addAll(buckets[i].subList(0, k - topK.size()));
        }
    }
    return topK;
}
```

## [2. 按照字符出现次数对字符串排序](https://cyc2018.github.io/CS-Notes/#/notes/Leetcode 题解 - 排序?id=_2-按照字符出现次数对字符串排序)

[451. Sort Characters By Frequency (Medium)](https://leetcode.com/problems/sort-characters-by-frequency/description/)

Given a string, sort it in decreasing order based on the frequency of characters.

```java
public String frequencySort(String s) {
    Map<Character, Integer> frequencyForNum = new HashMap<>();
    for (char c : s.toCharArray())
        frequencyForNum.put(c, frequencyForNum.getOrDefault(c, 0) + 1);

    List<Character>[] frequencyBucket = new ArrayList[s.length() + 1];
    for (char c : frequencyForNum.keySet()) {
        int f = frequencyForNum.get(c);
        if (frequencyBucket[f] == null) {
            frequencyBucket[f] = new ArrayList<>();
        }
        frequencyBucket[f].add(c);
    }
    StringBuilder str = new StringBuilder();
    for (int i = frequencyBucket.length - 1; i >= 0; i--) {
        if (frequencyBucket[i] == null) {
            continue;
        }
        for (char c : frequencyBucket[i]) {
            for (int j = 0; j < i; j++) {
                str.append(c);
            }
        }
    }
    return str.toString();
}
```

