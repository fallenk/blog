

KMP 算法

[https://www.youtube.com/watch?v=3IFxpozBs2I&ab_channel=%E9%BB%84%E6%B5%A9%E6%9D%B0](https://www.youtube.com/watch?v=3IFxpozBs2I&ab_channel=黄浩杰)



```java
package palindrome;

import java.util.Arrays;
import java.util.Scanner;

public class KMPTest {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        String text = in.nextLine();
        String str = in.nextLine();

        char[] pattern = str.toCharArray();
        int[] prefix = new KMPTest().prefixTable(pattern);
        System.out.println(Arrays.toString(prefix));
        new KMPTest().searchKMP(text.toCharArray(), pattern, prefix);
    }

    private void searchKMP (char[] text, char[] pattern, int[] prefix){
        // text[i]  len = m
        // pattern[j] len = n
        int m = text.length, n = pattern.length;
        int i=0, j = 0;
        while (i < m) {
            if (j == n-1 && text[i] == pattern[j]) {
                System.out.printf("Founded pos: %d\n", i-j);
                j = prefix[j];
            }
            if (text[i] == pattern[j]) {
                i++; j++;
            } else {
                j = prefix[j];
                if (j == -1) { // 回到开头
                    i++; j++;
                }
            }
        }
    }

    private int[] prefixTable(char[] pattern) {
        int len = pattern.length;
        int[] prefix = new int[len];
        prefix[0] = 0;
        int i = 1;
        int subLen = 0; //pattern字符串上 i位置上的前缀子串长度, 也是subLen指针  ----subLen----i---
        while (i < len) {
            if (pattern[i] == pattern[subLen]) {
                subLen++;
                prefix[i] = subLen;
                i++;
            } else {
                if (subLen == 0) {
                    prefix[i] = subLen;
                    i++;
                } else {
                    subLen = prefix[subLen-1]; // 回退
                }
            }
        }

        return movePrefixTable(prefix);
    }
    private int[] movePrefixTable(int[] prefix) {

        int len = prefix.length;
        for (int i = len-1; i >0 ; i--) {
            prefix[i] = prefix[i-1];
        }
        prefix[0] = -1;
        return prefix;
    }

    private void prefix_table2(char[] pattern, int[] prefix) {
        int n = pattern.length;
        prefix[0] = 0;
        int i = 1;
        int len = 0; // 匹配前缀的子串 长度
        while (i < n) {
            if (pattern[i] == pattern[len]) {
                len++;
                prefix[i] = len;
                i++;
            } else {
                if (len > 0) {
                    len = prefix[len-1];
                } else  {
                    prefix[i] = len;
                    i++;
                }
            }
        }
    }
}
```