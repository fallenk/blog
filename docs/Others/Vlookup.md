# 需求

Vlookup教程

http://www.360doc.com/content/19/0717/02/7972755_849259811.shtml

https://www.zhihu.com/question/34419318



两张Excel清理数据



python Dataframe 数据清洗 从一个表中去掉在另一个表中出现的行

## **1. 例子**

直接进入正题，现在我有2个表格

表格链接：[https://pan.baidu.com/s/1HNZZXRhw7W1oCv6P0ZWexg](https://link.zhihu.com/?target=https%3A//pan.baidu.com/s/1HNZZXRhw7W1oCv6P0ZWexg) 密码：m4tx

A表格：uid + 昵称

![img](https://picb.zhimg.com/80/v2-b9a9236b948f8b54189b491814723590_1440w.jpg)

B表格：uid + 图片数量pic

![img](https://pic1.zhimg.com/80/v2-cb8316b55860fca76732b977bd2fc431_1440w.jpg)

它们拥有共同的列：uid，其包含关系是

![img](https://pic1.zhimg.com/80/v2-e94fd237d4002fe02a0bfc0e5c888b8a_1440w.jpg)

现在，我有2个需求：

1. **去掉B中图片数量（pic）小于10的人（所在行）**
2. **从A表中去掉在B中出现的人（行）**

相对来说，第一个需求比较容易满足，就像在excel中筛选一样，很容易实现，但第二个需求想在excel中实现，却是要用到vlook等查找函数，然后再进行筛选。这里我就先简单讲一下，如何在excel中实现第二个需求，然后再讲如何通过python的Dataframe来程序化操作。

1. 在A表中新建一列is_exist，在其下第一个单元格C1写下公式：

=VLOOKUP(A2,'[uid-pic.xlsx]Sheet1'!$B$1:$C$4357,2,0)

其中，第一个参数A2代表共同的键uid，第二个参数代表整个 B表格，第三个参数2代表需要获取的值是B表格的第二列，最后一个参数0代表精确匹配。

![img](https://pic3.zhimg.com/80/v2-4994a77f52e2531d30e7d83fc8e0fdd6_1440w.jpg)

第一个没有找到（值为NA），把鼠标放在第一个单元格C1的右下角，当出现一个黑色的小十字时，双击，就可以将公式铺满整列。

![img](https://picb.zhimg.com/80/v2-d0f4518f0ae706dcc11ca57ac8a4409c_1440w.jpg)

可以看到，有些单元格找到了，有些是NA，那些找到了的人，就证明在另一个表（B）中已存在，这时候我们就可以使用excel的筛选功能，筛选出那些值为NA的。

![img](https://pic1.zhimg.com/80/v2-80728e5513c0e4d217916b5bbfa5706c_1440w.jpg)

------

以上就是纯粹使用excel达到需求，现在演示如何使用python来程序化操作

1. 将数据读入Dataframe

```python3
import pandas as pd

df_a = pd.read_excel("uid-nickname.xlsx")
df_b = pd.read_excel("uid-pic.xlsx")
```

如果仅仅只是为了验证代码和函数的功能，大家也可以使用读取剪切板的方法，分别复制A和B的Excel数据，然后执行

```text
pd.read_clipboard()
```

就可以获取到剪贴板上的内容了，有些时候，图方便，不想那么麻烦输入Excel文件的路径，就使用这种方法读入数据。

\2. 筛选数据

这里就需要用到Dataframe的条件选择语法，也称为布尔索引。简而言之，就是先得到一列布尔值的索引，然后来选择原df，具体操作如下：

需求1：**去掉B中图片数量（pic）小于10的人（所在行）**

```text
t = df_b['pic'] >= 10
```

输入如上的代码，可以得到如下的

```text
In[32]: type(t)
Out[32]: pandas.core.series.Series
In[33]: t.head(10)
Out[33]: 
0    False
1     True
2     True
3     True
4    False
5     True
6     True
7     True
8     True
9     True
Name: pic, dtype: bool
```

可以看到，t其实是一个Series，它的元素的布尔值，分别对应B表中的pic是否大于等于10，下面再执行

```text
df_b[t]
```

就可以得到所有B表中pic大于等于10的行（小于10的就被筛掉了），综合起来，这个需求就一行代码

```text
df_b_filter = df_b[df_b['pic'] >= 10]
```

然后再将这个df输出到Excel文件中即可

```text
df_b_filter.to_excel("uid-pic-filter.xlsx")
```

需求2：**从A表中去掉在B中出现的人（行）**

上面我们已经通过实例了解到条件筛选是怎么回事，下面这个需求2就迎刃而解了。这次不再是比较值的大小，而是是否存在的问题，Dataframe中有一个函数isin()与之对应.

```text
df_a['uid'].isin(df_b['uid'])
```

上面这段代码也会得到一个Series，每一个元素对应A表的uid是否在B表中，如果存在，就是True，不存在，就是False。

如果我们之间像上面一样使用如下代码

```text
df_a_filter = df_a[df_a['uid'].isin(df_b['uid'])]
```

那么得到的结果就是A表中，且在B表中出现了的人。现在，我们只需要取反即可（因为没有isnotin（）这样的函数），语法就是在上面的Series前加一个~符号

```text
df_a_filter = df_a[~ df_a['uid'].isin(df_b['uid'])]
```

这样得到的结果，就是A表中，且排除了在B中出现的结果。

---------------------------------------------------------------------------------------完

**总结：**

1. **如果是数字，条件筛选可以直接使用>=<这样的符号**
2. **如果是集合，可以使用isin() 这样的函数，取反使用符号~**
3. **关于isin()， 还有更高级的用法，那就是传入字典做参数（刚刚的例子传入的参数相当于是list），字典的key对应A表中的columns，感兴趣的同学可以自行查阅资料。**

https://link.zhihu.com/?target=https%3A//blog.csdn.net/qq_28219759/article/details/49160073