---
title: 在排序数组中查找数字 I
date: 2020-06-19 20:42:09
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/



# 解法

```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums==null || nums.length==0) return 0;
        // 搜索右边界
        int len = nums.length;
        int i=0, j= len-1;
        while(i<=j) {
            int mid = i+(j-i)/2;
            if(nums[mid]<target) {
                i = mid+1;
            } else if(nums[mid]>target){
                j = mid-1;
            } else {
                i= mid+1;
            }
        }
        int right = i;
        if(j >= 0 && nums[j] != target) return 0;
        i=0;
        j = right;
        while(i<=j) {
            int mid = i+(j-i)/2;
            if(nums[mid] < target) {
                i = mid+1;
            } else if(nums[mid] > target) {
                j = mid-1;
            } else {
                j = mid-1;
            }
        }
        int left = j;
        return right-left-1;

    }
}
```



```java
由于数组 nums 中元素都为整数，因此可以分别二分查找 target 和 target - 1的右边界，将两结果相减并返回即可。

class Solution {
    public int search(int[] nums, int target) {
        return helper(nums, target) - helper(nums, target - 1);
    }
    int helper(int[] nums, int tar) {
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] <= tar) i = m + 1;
            else j = m - 1;
        }
        return i;
    }
}
```

