---
title: LeetCode-双指针
date: 2019-05-11 20:35:56
tags: [刷题, 双指针]
category: [LeetCode]
---

# 思想

双指针主要用于**遍历数组**，两个指针指向不同的元素，从而**协同完成任务**。

如何写程序：**分析程序代码：入口，分叉逻辑(各个分支情况列举)，出口考虑(各种边界条件,特别是=的情况)**

控制结束的条件，初始，各个分支情况流程逻辑；结束时的条件: 正常结束，异常结束，边界值考虑

<!--more-->

## [167,Two sum](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)

Given an array of integers that is already **sorted in ascending order**, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.

```C++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int index1 = 0, index2 = numbers.size()-1;
        vector<int> list;
        while (index1 < index2) {
            
            int sum = numbers[index1] + numbers[index2];
            if(sum == target) {
                list.push_back(index1+1);
                list.push_back(index2+1);
                return list;
            } else if(sum > target) {
                index2--;
            } else if (sum < target) {
                index1++;
            }
        }
        return list;
    }
};
```

解： 使用双指针，一个指针指向值较小的元素，一个指针指向值较大的元素。指向较小元素的指针从头向尾遍历，指向较大元素的指针从尾向头遍历。指针 不一定是真的指针，标示符；

##[633. Sum of Square Numbers (Easy)](https://leetcode.com/problems/sum-of-square-numbers/description/)

两数平方和

Given a non-negative integer `c`, your task is to decide whether there're two integers `a` and `b` such that $a^{2}+b^{2}=c$.

初始化想法，但是边界的条件，int 的范围不够

```C++
class Solution {
public:
    bool judgeSquareSum(int c) {
        int i = 0;
        int j = (int)sqrt(c);
        while (i <= j) {
            int sqrtSum = i*i +j*j;
            if(sqrtSum == c) {
                return true;
            } else if(sqrtSum > c){
                j--;
            } else {
                i++;
            }
        }
        return false;
    }
};
```

双指针 不一定是针对数据，主要需要协同工作.注意 int的取值范围；改进之后如下：

```C++
class Solution {
public:
    bool judgeSquareSum(int c) {
        int i = 0;
        int j = sqrt(c);
        
        while(i <= j) {
            int squreI = c - j*j;
            if(squreI == i*i) {
                return true;
            } else if(i*i > squreI) {
                j--;
            } else {
                i++;
            }
        }
        return false;
        
    }
};	
```





## [345. Reverse Vowels of a String](https://leetcode.com/problems/reverse-vowels-of-a-string/description/)

反转字符串中的元音字符

Write a function that takes a string as input and reverse only the vowels of a string.

**Note:**
The vowels does not include the letter "y".

```
class Solution {
public:
    string reverseVowels(string s) {
        int front = 0, rare = s.length()-1;
        
        while(front < rare) {
            if(isVowels(s[front]) == false) {
                front++;
            }
            if(isVowels(s[rare]) == false) {
                rare--;
            }
            if( isVowels(s[front])&& isVowels(s[rare])) {
                char c = s[front];
                s[front] = s[rare];
                s[rare] = c;
                
                front++;
                rare--;
    
            }
            
            
        }
        // cout << s;
        return s;
    }
    
    bool isVowels(char c) {
        if(c=='a' ||c=='e' ||c=='i' ||c=='o' ||c=='u' ||c=='A' ||c=='E' ||c=='I' ||c=='O' ||c=='U') {
            return true;
        }
        return false;
    }
};
```

## [680. Valid Palindrome II (Easy)](https://leetcode.com/problems/valid-palindrome-ii/description/)

回文字符串

Given a non-empty string `s`, you may delete **at most** one character. Judge whether you can make it a palindrome.

**Note:**

1. The string will only contain lowercase characters a-z. The maximum length of the string is 50000.

```C++
class Solution {
public:
    bool validPalindrome(string s) {
        int start = 0, end = s.length()-1;
        int flag = 0; // >=2 -> no
        char c;
        while(start < end) {
            if(s[start] == s[end]) {
                start++;
                end--;
                
            } else {
                if(s[start] == s[end-1]) {
                    c = s[end];
                    end--;
                    flag++;
                    
                }else if(s[start+1] == s[end]) {
                    c = s[start];
                    start++;
                    flag++;
                } else if(s[start] != s[end-1]&&s[start+1] != s[end]){
                    flag = 2;
                    
                }
                
                if(flag >= 2) {
                    break;
                }                
            }
            
        }
        if(flag >= 2) {
            return false;
        } else {
           return true; 
        }
    }
};
```

注： 分类情况，写清楚，分类清楚，代码的逻辑才会写的不会有问题。

本题 的算法数据没有，实现步骤没有问题，入口，各种分支逻辑，出口边界条件；但是算法的 思想，没有考虑全面性，要是不存在。

```
class Solution {
public:
    bool validPalindrome(string s) {
        int start = 0, end = s.length()-1;
        int flag = 0; // >=2 -> no
        char c;
        while(start <= end && s[start] == s[end]) {
            start++;
            end--;
            
        }
        if(start > end) { //索引相遇表示回文串
            return true;
        } else { //未相遇
            string s1 = s.substr(start, end-start);
            string s2 = s.substr(start+1, end-start);
            return isPalindrome(s1) || isPalindrome(s2);
        }
    }
    
    // 判断回文串
    bool isPalindrome(string s) {
        int left = 0;
        int right = s.length()-1;
        while(left <= right && s[left] == s[right]) {
            left++;
            right--;
        }
        if(left > right) {
            return true;
        } else {
            return false;
        }
    }
};
```



## [88. Merge Sorted Array (Easy)](https://leetcode.com/problems/merge-sorted-array/description/)

归并两个有序数组

Given two sorted integer arrays *nums1* and *nums2*, merge *nums2* into *nums1* as one sorted array.

**Note:**

- The number of elements initialized in *nums1* and *nums2* are *m* and *n* respectively.
- You may assume that *nums1* has enough space (size that is greater or equal to *m* + *n*) to hold additional elements from *nums2*.

```C++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        
        int index = n+m-1;
        int index1 = m-1;
        int index2 = n-1;
        
        while(index1>=0&&index2 >=0) {
            if(nums2[index2] > nums1[index1]) {
                nums1[index] = nums2[index2];
                index--;
                index2--;
            } else {
                nums1[index] = nums1[index1];
                index--;
                index1--;
            }
        }
        while(index2>=0) {
            nums1[index] = nums2[index2];
            index--;
            index2--;
        }
    }
};
```

注：双指针，协同完成一个任务，确定开始，逻辑流程，结束条件。从开始麻烦，可换个角度考虑，从尾部开始。

## [141. Linked List Cycle (Easy)](https://leetcode.com/problems/linked-list-cycle/description/)

判断链表是否存在环

Given a linked list, determine if it has a cycle in it.

To represent a cycle in the given linked list, we use an integer `pos` which represents the position (0-indexed) in the linked list where tail connects to. If `pos` is `-1`, then there is no cycle in the linked list.

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if(head == NULL) return false;
        ListNode* slow = head;
        ListNode* fast = head;
        
        // while(slow != NULL && fast != NULL) {
        //     slow = slow->next;
        //     fast = fast->next->next;
        //     if(slow == fast) {
        //         return true;
        //     }
        // } 
      	// slow->next不能保证fast->next不为空啊
        while(fast->next != NULL && fast->next->next != NULL) {
            slow = slow->next;
            fast = fast->next->next;
            
            if(slow == fast) {
                return true;
            }
        }
        return false;
    }
};
```

注：判断链表回环问题，主要是判断尾部是否为空但是这样会死循环；采用快慢指针。

## [524. Longest Word in Dictionary through Deleting (Medium)](https://leetcode.com/problems/longest-word-in-dictionary-through-deleting/description/)

最长子序列

Given a string and a string dictionary, find the longest string in the dictionary that can be formed by deleting some characters of the given string. If there are more than one possible results, return the longest word with the smallest lexicographical order. If there is no possible result, return the empty string.

**Example 1:**

```
Input:
s = "abpcplea", d = ["ale","apple","monkey","plea"]

Output: 
"apple"
```

**Note:**

1. All the strings in the input will only contain lower-case letters.
2. The size of the dictionary won't exceed 1,000.
3. The length of all the strings in the input won't exceed 1,000.

分析程序代码：入口，分叉逻辑(各个分支情况列举)，出口考虑(各种边界条件)

```C++
class Solution {
public:
    string findLongestWord(string s, vector<string>& d) {
        int sIndex = 0, dIndex = 0;
        
        string maxString = d[0];
        for(int i = 0; i < d.size(); i++) {
            sIndex = 0;
            dIndex = 0;
            while(sIndex < s.length() && dIndex < d[i].length()) {
                if(s[sIndex] == d[i][dIndex]) {
                    sIndex++;
                    dIndex++;
                } else {
                    sIndex++;
                }
                
                // 是否存在  dIndex未走完,存在则抛弃
                if(sIndex == s.length()-1 && dIndex != d[i].length()-1) {
                    // sIndex = 0;
                    // dIndex = 0;
                    break;
                }
                
                if(sIndex == s.length()-1 || dIndex == d[i].length()-1) { //匹配最长最小子串
                    if(maxString.length() < d[i].length()) {
                        maxString = d[i];
                    } else if(maxString.length() == d[i].length()){
                        if(maxString > d[i]) {
                            maxString = d[i];
                        }
                    }
                    // sIndex = 0;
                    // dIndex = 0;  
                }
            }
        }
        return maxString;
    }
};
```

注：控制的变量最好放在一处控制，不要分散开来。

控制结束的条件，初始，各个分支情况流程逻辑；结束时的条件，正常结束，异常结束，边界值

```C++
class Solution {
public:
    string findLongestWord(string s, vector<string>& d) {
        int sIndex = 0, dIndex = 0;
        
        string maxString = "";
        for(int i = 0; i < d.size(); i++) {
            sIndex = 0;
            dIndex = 0;
            while(sIndex < s.length() && dIndex < d[i].length()) {
                if(s[sIndex] == d[i][dIndex]) {
                    sIndex++;
                    dIndex++;
                } else {
                    sIndex++;
                }
  
            }
            if(dIndex == d[i].length()) { //匹配最长最小子串
                    if(maxString.length() < d[i].length()) {
                        maxString = d[i];
                    } else if(maxString.length() == d[i].length()){
                        if(maxString > d[i]) {
                            maxString = d[i];
                        }
                    }
            }
        }
        return maxString;
    }
};	
```