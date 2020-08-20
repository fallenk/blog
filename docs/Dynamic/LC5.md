# 题目

https://leetcode-cn.com/problems/longest-palindromic-substring



# 解法

暴力 

```java
class Solution {
    public String longestPalindrome(String s) {
         int len = s.length();
        if (s==null || len == 0) {
            return "";
        }
        String res = s.substring(0,1);
        for (int i = 0; i < len; i++) {
            int l = i;
            int r = l+1;
            String str = palindrome(s, l, l);
            String str2 = palindrome(s, l, r);
            if (str.length() > str2.length()) {
                str2 = str;
            }
            if (res.length() < str2.length()) {
                res = str2;
            }
        }
        // System.out.println(res);
        return res;
    }

    private String palindrome(String s, int start, int end) {
        //        String str = "";
        while (start>=0 && end <s.length() && s.charAt(start) == s.charAt(end)) {
//            str = s.substring(start, end+1);//不用每次都记录 暴力改良版
            start--;
            end++;
        } // 结束时  s.charAt(start) != s.charAt(end)
        return s.substring(start+1, end);
    }
}
```

## 动态规划

