---
title: Java快排和二分
date: 2020-06-08 16:39:23
tags: [LeetCode]
---

# 快排

> 要点：两端开始“探测”，设置的**基准数是最左边的数，所以需要让哨兵j先出动**， 分治想法

<!--more-->

```java
public class QuickSort {
    public static void main(String[] args) {
        int[] arr = new int[]{10,7,2,4,7,62,3,4,2,1,8,100,19};
        QuickSort qs = new QuickSort();
        int[] res = qs.quickSort(arr, 0, arr.length-1);
        for (int i=0; i<arr.length; i++) {
            System.out.println(res[i]);
        }
    }
    public int[] quickSort(int[] arr, int start, int end) {
       if (start > end) {
           return null;
       }
       if (start == end) {
           return arr;
       }
       int len = arr.length;
       int index = partition(arr, start, end);
       if (index > start) quickSort(arr, start, index-1);
       if (index < end) quickSort(arr, index+1, end);
        return arr;
    }

    private int partition(int[] arr, int start, int end) {
        if (arr == null || start > end || arr.length == 0){
            return -1;
        }
        int baseVal = arr[start];
        int i,j;
        for (i=start, j=end;i<j; ) {
           while (arr[j] > baseVal && i <j) {
               j--;
           }
           while (arr[i] <= baseVal && i <j) {
               i++;
           }

           int temp=0;
           if (arr[i]>baseVal && arr[j] <=baseVal) {
                temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
           }
        }
        int temp = arr[start];
        arr[start] = arr[i];
        arr[i] = temp;
        return i;
    }
}
```



# 二分

> 要点 边界条件

```java
private int binarySearch(int[] nums, int target) {
    if (nums.length == 0) {
        return 0;
    }
    int len = nums.length;
    int l = 0, r = len-1;
    while (l<=r) {
        int m = (l+r)/2;
        int middle= l+ (r-l)/2 ;
        if (target == nums[m]) {
            return m;
        } else if(target > nums[m]) {
            l = m+1;
        } else {
            r = m-1;
        }
    }
    return -1;
}
```

