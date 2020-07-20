# 题目

输入输出 Scanner



总结： 输入的数据是什么类型，就用什么类型 ， int - nextInt; string -> nextLine();  整体用hasNext();

```java
Scanner in = new Scanner(System.in);
while (in.hasNext()) {
    String[] strs = in.nextLine().split(" ");
    int i = Integer.parseInt(strs[0]); // 字符串转int
    String str = String.valueOf(i);  // int 转字符串
}

import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner s = new Scanner(System.in);
        while(s.hasNext()){ //接收下一项
            String[] str = s.nextLine().split(" "); // 接收当前行输入
            Arrays.sort(str);
            for(int i = 0;i<str.length;i++){     
                if(i==str.length-1){
                    System.out.println(str[i]);
                }else{
                    System.out.print(str[i]+" ");
                }
            }
             
        }
    }
}
```

2
1 5
10 20
当使用in.next()，接收第一个字符时；2之后的回车不被接收；如果后续使用了in.nextLine()，则会接收这个回车，因此可以考虑使用in.nextInt();



```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int t = in.nextInt();
        while(in.hasNext()) {  //错误示范
           for(int i=0; i<t; i++){
               String[] strs = in.nextLine().split(" ");
               int sum =0;
               for(String temp : strs) {
                   sum += Integer.parseInt(temp);
               }
               System.out.println(sum);
           }
        }
    }
}
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int t = in.nextInt();
        while(in.hasNext()) {
           for(int i=0; i<t; i++){
               int a = in.nextInt(); //对应的类型接收
               int b = in.nextInt();
               System.out.println(a+b);
           }
        }
    }
}
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int t = Integer.parseInt(in.nextLine()); //使用nextLine接收
        while(in.hasNext()) {
           for(int i=0; i<t; i++){
              String[] strs = in.nextLine().split(" ");
              int sum = Integer.parseInt(strs[0]) + Integer.parseInt(strs[1]);
               System.out.println(sum);
           }
        }
    }
}


Scanner类
获取用户的输入
Scanner s = new Scanner(System.in);

演示一个最简单的数据输入，并通过 Scanner 类的 next() 与 nextLine() 方法获取输入的字符串，在读取前我们一般需要 使用 hasNext 与 hasNextLine 判断是否还有输入的数据：
使用 next 方法：
import java.util.*
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);// 从键盘接收数据
        System.out.println("next方式接收：");
        if(in.hasNext()) { // 判断是否还有输入
            String str1 = in.next();
            System.out.println("输入的数据为："+str1);
        }
        in.close();
    }
}
$ javac ScannerDemo.java
$ java ScannerDemo
next方式接收：
runoob com
输入的数据为：runoob //可以看到 com 字符串并未输出，接下来我们看 nextLine。

使用 nextLine 方法：
import java.util.Scanner;
 
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);        // 从键盘接收数据
        System.out.println("nextLine方式接收：");
        if (scan.hasNextLine()) {        // 判断是否还有输入
            String str2 = scan.nextLine();
            System.out.println("输入的数据为：" + str2);
        }
        scan.close();
    }
}
$ javac ScannerDemo.java
$ java ScannerDemo
nextLine方式接收：
runoob com
输入的数据为：runoob com

next() 与 nextLine() 区别
next():
1、一定要读取到有效字符后才可以结束输入。
2、对输入有效字符之前遇到的空白，next() 方法会自动将其去掉。
3、只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符。
next() 不能得到带有空格的字符串。
nextLine()：
1、以Enter为结束符,也就是说 nextLine()方法返回的是输入回车之前的所有字符。
2、可以获得空白。

这里要说明一下java中，hasNextLine和hasNext的区别。
先说结论：
采用has xxxx的话，后面也要用next xxxx。比如前面用hasNextLine，那么后面要用 nextLine 来处理输入。
Scanner类用于获取键盘输入（是一个基于正则表达式的文本扫描器），它可以从文件、字符串、输入流中解析出基本类型值和字符串值。Scanner类提供了多个构造器，不同的构造器可以接收文件、字符串和输入流作为数据源，用于从文件、字符串和输入流中解析数据。
Scanner类主要提供了两个方法来扫描输入：
hasNextXx()：是否还有下一个输入项，其中Xxx可以是Int、Long等代表基本数据类型的字符串。如果只是判断是否包含下一个字符串，则直接使用hasNext()。
nextXxx()：获取下一个输入项。Xxx的含义同上。
如果要输入 int 或 float 类型的数据，在 Scanner 类中也有支持，但是在输入之前最好先使用 hasNextXxx() 方法进行验证，再使用 nextXxx() 来读取：
import java.util.Scanner;
 
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        // 从键盘接收数据
        int i = 0;
        float f = 0.0f;
        System.out.print("输入整数：");
        if (scan.hasNextInt()) {
            // 判断输入的是否是整数
            i = scan.nextInt();
            // 接收整数
            System.out.println("整数数据：" + i);
        } else {
            // 输入错误的信息
            System.out.println("输入的不是整数！");
        }
        System.out.print("输入小数：");
        if (scan.hasNextFloat()) {
            // 判断输入的是否是小数
            f = scan.nextFloat();
            // 接收小数
            System.out.println("小数数据：" + f);
        } else {
            // 输入错误的信息
            System.out.println("输入的不是小数！");
        }
        scan.close();
    }
}

以下实例我们可以输入多个数字，并求其总和与平均数，每输入一个数字用回车确认，通过输入非数字来结束输入并输出执行结果：
import java.util.Scanner;
 
class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        double sum = 0;
        int m = 0;
        while (scan.hasNextDouble()) {
            double x = scan.nextDouble();
            m = m + 1;
            sum = sum + x;
        }
        System.out.println(m + "个数的和为" + sum);
        System.out.println(m + "个数的平均值是" + (sum / m));
        scan.close();
    }
}
12
23
15
21.4
end
4个数的和为71.4
4个数的平均值是17.85


链接：https://ac.nowcoder.com/acm/contest/5646/A
来源：牛客网
计算a+b
输入描述:
输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据包括多组。
输出描述:
输出a+b的结果
import java.util.*;
public class Main() {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while(sc.hasNextLine()) {
            String[] strs = sc.nextLine().split(" ");
            int sum = 0;
            for (int i=0; i<strs.length; i++){
                sum += Integer.parseInt(strs[i]); // string to int
            }
            System.out.println(sum);
        }
    }
}


链接：https://ac.nowcoder.com/acm/contest/5646/B
来源：牛客网
计算a+b
输入描述:
输入第一行包括一个数据组数t(1 <= t <= 100)
接下来每行包括两个正整数a,b(1 <= a, b <= 10^9)
输出描述:
输出a+b的结果
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            int a = in.nextInt();
            int b = in.nextInt();
            if (a == 0 && b ==0) {
                return;
            }
            System.out.println(a+b);
        }
    }
}



链接：https://ac.nowcoder.com/acm/contest/5646/D
来源：牛客网

计算一系列数的和
输入描述:
输入数据包括多组。
每组数据一行,每行的第一个整数为整数的个数n(1 <= n <= 100), n为0的时候结束输入。
接下来n个正整数,即需要求和的每个正整数。
输出描述:
每组数据输出求和的结果
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while(in.hasNext()) {
            int t = in.nextInt();
            if (t == 0) {
                return;
            }
            int sum = 0;
            for (int i=0; i<t; i++) {
                sum += in.nextInt();
            }
            System.out.println(sum);
        }
    }
}


链接：https://ac.nowcoder.com/acm/contest/5646/E
来源：牛客网

计算一系列数的和
输入描述:
输入的第一行包括一个正整数t(1 <= t <= 100), 表示数据组数。
接下来t行, 每行一组数据。
每行的第一个整数为整数的个数n(1 <= n <= 100)。
接下来n个正整数, 即需要求和的每个正整数。
输出描述:
每组数据输出求和的结果
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int t = 0;
        int n = 0;

        while(in.hasNext()) {
            t = in.nextInt();
            for(int i=0; i<t; i++) {
                n = in.nextInt();
                        int sum = 0;
                 for(int j=0; j<n; j++) {
                     sum += in.nextInt();
                 }
                System.out.println(sum);
            }
        }
    }
}
import java.util.*;
 
public class Main {
    public static void main(String args[]){
        Scanner input = new Scanner(System.in);
        int t = input.nextInt();
        input.nextLine(); // 接收第一行的空格
        for (int i = 0; i < t; i++){
            String[] str = input.nextLine().split(" ");
            int n = Integer.parseInt(str[0]);
            int[] nums = new int[n];
            for (int j = 0; j < n; j++){
                nums[j] = Integer.parseInt(str[j + 1]);
            }
            int sum = 0;
            for (int k = 0; k < n; k++){
                sum += nums[k];
            }
            System.out.println(sum);
        }
    }
}


链接：https://ac.nowcoder.com/acm/contest/5646/G
来源：牛客网

输入描述:
输入数据有多组, 每行表示一组输入数据。
每行不定有n个整数，空格隔开。(1 <= n <= 100)。
输出描述:
每组数据输出求和的结果
import java.util.*;
public class Main {
    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        while(in.hasNextLine()) {
            String[] strs = in.nextLine().split(" ");
            int sum = 0;
            for(String str : strs) {
                sum += Integer.parseInt(str);
            }
            System.out.println(sum);
        }
    }
}


链接：https://ac.nowcoder.com/acm/contest/5646/H
来源：牛客网

输入描述:
输入有两行，第一行n
第二行是n个空格隔开的字符串
输出描述:
输出一行排序后的字符串，空格隔开，无结尾空格
import java.util.*;
public class Main {
    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        String[] strs = new String[n];
        for(int i=0; i<n; i++) {
            strs[i] = in.next();
        }
        Arrays.sort(strs);
        for(int i=0; i<n; i++) {
             if(i==0){
                  System.out.print(strs[0]); 
                }else{
                     System.out.print(" "+strs[i]);
                }
        }
    }
}

import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while(in.hasNext()){
            int n = in.nextInt();
            in.nextLine();
            //ArrayList<String> arr = new ArrayList<>();
            String[] strs = in.nextLine().split(" ");
            Arrays.sort(strs);
            //System.out.println(arr.toString());
            for(int i=0; i<n; i++){
                if (i==0){
                   System.out.print(strs[i]);
                } else {
                     System.out.print(" "+strs[i]);
                }                
            }
        }
    }
}



链接：https://ac.nowcoder.com/acm/contest/5646/I
来源：牛客网

对输入的字符串进行排序后输出
输入描述:
多个测试用例，每个测试用例一行。
每行通过空格隔开，有n个字符，n＜100
输出描述:
对于每组测试用例，输出一行排序过的字符串，每个字符串通过空格隔开
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        while(in.hasNextLine()){
            String[] strs = in.nextLine().split(" ");
            Arrays.sort(strs);
            for(int i=0; i<strs.length; i++) {
                if (i==0) {
                    System.out.print(strs[i]);
                } else {
                    System.out.print(" "+strs[i]);
                }
            }
            System.out.println();
        }
    }
}
```

