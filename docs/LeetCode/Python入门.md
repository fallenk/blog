---
title: Python入门
date: 2018-09-28 19:49:34
tags: [编程, 代码]
category: Python学习
---

# Python入门
## 概念
### 定位
一种高级的，动态类型的多范型编程语言，写法近似伪代码，面向对象。哲学：“优雅”、“明确”、“简单”，简单优雅，尽量写容易看明白的代码，尽量写少的代码。

<!--more-->
### 缺点
1. 运行速度慢，和C程序相比非常慢。Python为解释型语言
2. 源代码不能加密
## 安装
安装Python 3.6，mac默认安转Python2.7，可使用Homebrew，在命令行`homebrew install python3`，进行安装
## 第一个Python程序
使用sublime3开发，新建文件hello.py

> 输入


```python
print('hello, world')
```

    hello, world


   > 输出


```python
name = input()
print(name)
```

**运行**  
cd到当前目录下， 使用 `python3 filename.py`

## 基础
### 数据类型和变量
- 整数: 程序中的表示方法和数学上的写法一致,例如:1,100,-8080,0
- 浮点数: `1.23e9`
- 字符串: 以单引号'或双引号"括起来的任意文本 `'I\'m \"OK\"!'`
- 布尔值: True、False,注意大小写，使用 `and`, `not`和`or`,代表与或非,如：`True or True`,`True and False`,`not True`
- 空值: 特殊的值, 使用`None`表示
> 变量

1. 变量在程序中就是用一个变量名表示，同一个变量可以反复赋值，而且可以是不同类型的变量。
2. 这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错
3. 最后，理解变量在计算机内存中的表示也非常重要。


```python
a = "abc"
```

Python解释器干了两件事情：
- 在内存中创建了一个'ABC'的字符串；
- 在内存中创建了一个名为a的变量，并把它指向'ABC'。即python中变量名可以理解为一个指针。


```python
a = 'ABC'
b = a
a = 'XYZ'
print('a:', a)
print('b:', b)
```

    a: XYZ
    b: ABC


**执行过程**
1. 执行a = 'ABC'，解释器创建了字符串'ABC'和变量a，并把a指向'ABC'。
2. 执行b = a，解释器创建了变量b，并把b指向a指向的字符串'ABC'。
3. 执行a = 'XYZ'，解释器创建了字符串'XYZ'，并把a的指向改为'XYZ'，但b并没有更改。
4. 所以，最后打印变量b的结果自然是'ABC'了。

> 常量

常量就是不能变的变量,Python根本没有任何机制保证PI不会被改变，所以，用全部大写的变量名表示常量只是一个习惯上的用法
在Python中，有两种除法，一种除法是`/`


```python
10 / 3
```




    3.3333333333333335



`/`除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数：
还有一种除法是`//`，称为地板除，两个整数的除法仍然是整数：


```python
10 // 3
```




    3



余数运算，可以得到两个整数相除的余数：


```python
10 % 3
```




    1



#### 小结
- Python支持多种数据类型，在计算机内部，可以把任何数据都看成一个“对象”，而变量就是在程序中用来指向这些数据对象的，对变量赋值就是把数据和变量给关联起来。
- 对变量赋值x = y是把变量x指向真正的对象，该对象是变量y所指向的。随后对变量y的赋值不影响变量x的指向。
- 注意：Python的整数没有大小限制，而某些语言的整数根据其存储长度是有大小限制的，例如Java对32位整数的范围限制在-2147483648-2147483647。
- Python的浮点数也没有大小限制，但是超出一定范围就直接表示为inf（无限大）。

### 字符串和编码
#### 字符编码
在计算机内存中，统一使用Unicode编码，即两个字节表示一个字符。当需要保存到硬盘或者需要传输的时候，就转换为UTF-8编码。
#### Python的字符串
字符串是以Unicode编码的，也就是说，Python的字符串支持多语言。对于单个字符的编码，Python提供了`ord()`函数获取字符的整数表示，`chr()`函数把编码转换为对应的字符
#### 格式化
采用的格式化方式和C语言是一致的，用%实现，举例如下：


```python
'Hello, %s' % 'world'
```




    'Hello, world'




```python
'Hi, %s, you have $%d.' % ('Michael', 1000000)
```




    'Hi, Michael, you have $1000000.'



`%`运算符就是用来格式化字符串的。在字符串内部，`%s`表示用字符串替换，`%d`表示用整数替换，有几个`%?`占位符，后面就跟几个变量或者值，顺序要对应好。如果只有一个`%?`，括号可以省略。

### 使用list和tuple
#### list
Python内置的一种数据类型是列表：list。list是一种有序的集合，可以随时添加和删除其中的元素。


```python
classmates = ['Michael', 'Bob', 'Tracy']
classmates
```




    ['Michael', 'Bob', 'Tracy']



取最后一个元素，除了计算索引位置外，还可以用`-1`做索引，直接获取最后一个元素。 list是一个可变的有序表，所以，可以往list中追加，插入，删除，修改等。
list是一个可变的有序表，所以，可以往list中追加元素到末尾：


```python
classmates.append('Adam')
print('Append: Adam:', classmates)
classmates.insert(1, 'Jack')
print("insert Jack", classmates)
classmates.pop(2) # 替换某个数字
print("pop", classmates)
classmates[-1] = 'last'
print("classmates[-1]", classmates)
```

    Append: Adam: ['Michael', 'Jack', 'Tracy', 'Adam', 'last', 'Adam']
    insert Jack ['Michael', 'Jack', 'Jack', 'Tracy', 'Adam', 'last', 'Adam']
    pop ['Michael', 'Jack', 'Tracy', 'Adam', 'last', 'Adam']
    classmates[-1] ['Michael', 'Jack', 'Tracy', 'Adam', 'last', 'last']



```python
# list里面的元素的数据类型也可以不同，比如：
L = ['Apple', 123, True]
L
```




    ['Apple', 123, True]



#### tuple
另一种有序列表叫元组：`tuple`。`tuple`和`list`非常类似，但是`tuple`一旦初始化就不能修改
它也没有`append()`，`insert()`这样的方法。其他获取元素的方法和list是一样的，你可以正常地使用`classmates[0]`，`classmates[-1]`，但不能赋值成另外的元素。


```python
classmates = ('Michael', 'Bob', 'Tracy')
print("classmates:", classmates)
```

    classmates: ('Michael', 'Bob', 'Tracy')


因为tuple不可变，所以代码更安全。如果可能，能用tuple代替list就尽量用tuple。

tuple的陷阱：当你定义一个tuple时，在定义的时候，tuple的元素就必须被确定下来，比如：


```python
t = (1, 2)
print(t)
x = () # 如果要定义一个空的tuple，可以写成()：
x
```

    (1, 2)





    ()



但是，要定义一个只有1个元素的tuple，如果你这么定义：


```python
t = (1)
print(t)
```

    1


定义的不是tuple，是1这个数！这是因为括号()既可以表示tuple，又可以表示数学公式中的小括号，这就产生了歧义，因此，Python规定，这种情况下，按小括号进行计算，计算结果自然是1。

所以，只有1个元素的tuple定义时必须加一个逗号,，来消除歧义：


```python
t = (1,) # Python在显示只有1个元素的tuple时，也会加一个逗号,，以免你误解成数学计算意义上的括号。
t
```




    (1,)



最后来看一个“可变的”tuple：


```python
t = ('a', 'b', ['A', 'B'])
t[2][0] = "X"
t[2][1] = "Y"
print(t)
```

    ('a', 'b', ['X', 'Y'])


这个tuple定义的时候有3个元素，分别是'a'，'b'和一个list。不是说tuple一旦定义后就不可变了吗？怎么后来又变了？

变量作为指针；表面上看，tuple的元素确实变了，但其实变的不是tuple的元素，而是list的元素。tuple一开始指向的list并没有改成别的list，所以，tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向'a'，就不能改成指向'b'，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的！

#### 条件判断
在Python程序中，用if语句实现：


```python
age = 12
if age >= 18 and age <= 60:
    print('your age is', age)
    print('adult')
elif age >= 60:
    print('your age is', age)
    print('old')
else:
    print('your age is', age)
    print('teenage')
```

    your age is 12
    teenage


根据Python的缩进规则,注意不要少写了冒号`:`, if语句执行有个特点，它是从上往下判断，如果在某个判断上是True，把该判断对应的语句执行后，就忽略掉剩下的elif和else
**再议 input**
这是因为input()返回的数据类型是str，str不能直接和整数比较，必须先把str转换成整数。Python提供了int()函数来完成这件事情：


```python
s = input('birth: ')
birth = int(s) # int() 转成数字型
if birth < 2000:
    print('00前')
else:
    print('00后')
```

    birth: 1943
    00前


#### 循环
`for x in ...`循环就是把每个元素代入变量x，然后执行缩进块的语句。
第二种循环是while循环，只要条件满足，就不断循环，条件不满足时退出循环


```python
sum = 0
for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
    print(x)
    sum = sum + x
print(sum)

list(range(5))# list 调用
```

    1
    2
    3
    4
    5
    6
    7
    8
    9
    10
    55





    [0, 1, 2, 3, 4]




```python
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```

    2500


### 使用dict和set
#### dict
Python内置了字典：dict的支持，dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。


```python
d={"Maichel": 89, "Adele": "ss", "John":"dd"}
print(d["Maichel"])
print(d["Adele"])
print(d["John"])
```

    89
    ss
    dd


1. 要避免key不存在的错误，有两种办法，一是通过in判断key是否存在
2. 二是通过dict提供的get()方法，如果key不存在，可以返回None，或者自己指定的value


```python
print('Thomas' in d)
d.get('Thomas', -1)
```

    False





    -1




```python
#要删除一个key，用pop(key)方法，对应的value也会从dict中删除：
d.pop('John')
print(d)
```

    {'Maichel': 89, 'Adele': 'ss'}


使用key-value存储结构的dict在Python中非常有用，选择不可变对象作为key很重要，最常用的key是字符串。这是因为dict根据key来计算value的存储位置，如果每次计算相同的key得出的结果不同，那dict内部就完全混乱了。这个通过key计算位置的算法称为哈希算法（Hash）。要保证hash的正确性，作为key的对象就不能变。在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list是可变的，就不能作为key。

#### set
set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。
要创建一个set，需要提供一个list作为输入集合：


```python
s = set([1, 2, 3])
s
```




    {1, 2, 3}




```python
sets = set([2, 4, 5])
sets.add(4) # 通过add(key)方法可以添加元素到set中，可以重复添加，但不会有效果
sets.add(1)
sets.remove(5) # 通过remove(key)方法可以删除元素
sets
```




    {1, 2, 4}




```python
# set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作：
s1 = set([1, 2, 3])
s2 = set([2, 3, 4])
print(s1 & s2)
print(s1 | s2)
```

    {2, 3}
    {1, 2, 3, 4}


#### 再议不可变对象
str是不变对象，而list是可变对象。
对于可变对象，比如list，对list进行操作，list内部的内容是会变化的，比如：


```python
a = ['c', 'b', 'a']
a.sort()
print(a)
```

    ['a', 'b', 'c']


而对于不可变对象，比如str，对str进行操作：


```python
a = 'abc'
b = a.replace('a', 'A')
print(b)
print(a)
```

    Abc
    abc


1. 要始终牢记的是，a是变量，而'abc'才是字符串对象！有些时候，我们经常说，对象a的内容是'abc'，但其实是指，a本身是一个变量，它指向的对象的内容才是'abc'：  a-------> 'abc';
2. 当我们调用a.replace('a', 'A')时，实际上调用方法replace是作用在字符串对象'abc'上的，而这个方法虽然名字叫replace，但却没有改变字符串'abc'的内容。相反，replace方法创建了一个新字符串'Abc'并返回，如果我们用变量b指向该新字符串，就容易理解了，变量a仍指向原有的字符串'abc'，但变量b却指向新字符串'Abc'了;
3. 所以，对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。

### 函数

#### 调用函数 
    要调用一个函数，需要知道函数的名称和参数,可以从Python的官方网站查看文档，另外从也可以在交互式命令行通过`help(abs)`查看`abs函数`的帮助信息。内置的abs(),绝对值函数；


```python
print(abs(-4))
print(int('3'))
print(float('12.34'))
print(str(12.34))
print(bool(0))
```

    4
    3
    12.34
    12.34
    False


#### 定义函数
1. 在Python中，定义一个函数要使用def语句，依次写出函数名、括号、括号中的参数和冒号`:`，然后，在缩进块中编写函数体，函数的返回值用return语句返回。
2. 请注意，函数体内部的语句在执行时，一旦执行到return时，函数就执行完毕，并将结果返回。因此，函数内部通过条件判断和循环可以实现非常复杂的逻辑。如果没有return语句，函数执行完毕后也会返回结果，只是结果为`None`。`return None`可以简写为`return`。
3. 如果你已经把`my_abs()`的函数定义保存为`abstest.py`文件了，那么，可以在该文件的当前目录下启动Python解释器，用`from abstest import my_abs`来导入`my_abs()`函数，注意abstest是文件名（不含.py扩展名）

**空函数**
1. 如果想定义一个什么事也不做的空函数，可以用pass语句：
2. pass语句什么都不做，那有什么用？实际上pass可以用来作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个pass，让代码能运行起来。


```python
def op():
    pass
```

**参数检查**
 对参数类型做检查，只允许整数和浮点数类型的参数。数据类型检查可以用内置函数`isinstance()`实现：


```python
def my_abs(x):
    if not isinstance(x, (int, float)):
        raise TypeError("bad operand type")
    if x < 0:
        x = -x
    else:
        x = x
    return x

my_abs(-3)
my_abs('a')
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-68-e53a6b326d38> in <module>()
          9 
         10 my_abs(-3)
    ---> 11 my_abs('a')
    

    <ipython-input-68-e53a6b326d38> in my_abs(x)
          1 def my_abs(x):
          2     if not isinstance(x, (int, float)):
    ----> 3         raise TypeError("bad operand type")
          4     if x < 0:
          5         x = -x


    TypeError: bad operand type


**返回多个值**
比如在游戏中经常需要从一个点移动到另一个点，给出坐标、位移和角度，就可以计算出新的新的坐标：


```python
import math
def move(x, y, step, angle):
    nx = x+step*math.cos(angle)
    ny = y - step*math.sin(angle)
    return nx, ny
x, y = move(100, 100, 60, math.pi / 6)
print(x, y)
```

    151.96152422706632 70.0


但其实这只是一种假象，Python函数返回的仍然是单一值;原来返回值是一个tuple！但是，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，Python的函数返回多值其实就是返回一个tuple，但写起来更方便。

**小结**
- 定义函数时，需要确定函数名和参数个数；
- 如果有必要，可以先对参数的数据类型做检查；
- 函数体内部可以用return随时返回函数结果；
- 函数执行完毕也没有return语句时，自动return None。
- 函数可以同时返回多个值，但其实就是一个tuple。

#### 函数的参数
1. 定义函数的时候，我们把参数的名字和位置确定下来，函数的接口定义就完成了.
2. 除了正常定义的**必选参数**外，还可以使用**默认参数**、**可变参数**和**关键字参数**，使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。

**位置参数**
1. 修改后的power(x, n)函数有两个参数：x和n，这两个参数都是位置参数，调用函数时，传入的两个值按照位置顺序依次赋给参数x和n。


```python
def power(x, n):
    sum = 1
    while n > 0:
        sum = sum * x
        n = n - 1
    return sum
power(2, 3)
```




    8



**默认参数**
1. 由于我们经常计算x2，所以，完全可以把第二个参数n的默认值设定为2
2. 设置默认参数时，有几点要注意：
    1. 是必选参数在前，默认参数在后，否则Python的解释器会报错
    2. 是如何设置默认参数:当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数
3. 定义默认参数要牢记一点：默认参数必须指向不变对象！


```python
def calc(numbers):
    sum = 0
    for n in numbers:
        sum = sum +n*n
    return sum
# 但是调用的时候，需要先组装出一个list或tuple：
print(calc([1,2,3]))
print(calc((1, 2, 3)))
```

    14
    14


**可变参数**
    1. 可变参数就是传入的参数个数是可变的，可以是1个、2个到任意个，还可以是0个。
    2. 给定一组数字a，b，c……，请计算a2 + b2 + c2 + ……， 要定义出这个函数，我们必须确定输入的参数。由于参数个数不确定，我们首先想到可以把a，b，c……作为一个list或tuple传进来，这样，函数可以定义如下：


```python
# 函数的参数改为可变参数：
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
print(calc(1, 2, 3))
print(calc(1, 2, 3, 4))
```

    14
    30


1. 定义可变参数和定义一个list或tuple参数相比，仅仅在参数前面加了一个*号。
2. 在函数内部，参数numbers接收到的是一个tuple，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括0个参数

如果已经有一个list或者tuple，要调用一个可变参数怎么办？可以这样做：


```python
nums = [1, 2, 3]
calc(nums[0], nums[1], nums[2]) # 过于繁琐
calc(*nums) # 允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去
```




    14



**关键字参数**
1. 可变参数允许你传入0个或任意个参数，这些可变参数在函数调用时自动组装为一个tuple。
2. 而关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。请看示例：


```python
def person(name, age, **kw):
    return ("name:", name, "age:", age, "others:",kw)
# 函数person除了必选参数name和age外，还接受关键字参数kw。在调用该函数时，可以只传入必选参数：
print(person("Maichel", 12))
print(person('Bob', 35, city='Beijing'))
print(person("AoA", 30, job="Engineer", city="HZ"))  # 它可以扩展函数的功能,

extra={"Job":"Engineer", "City": "HZ"}
print(person("COC", 22, **extra))
```

    ('name:', 'Maichel', 'age:', 12, 'others:', {})
    ('name:', 'Bob', 'age:', 35, 'others:', {'city': 'Beijing'})
    ('name:', 'AoA', 'age:', 30, 'others:', {'job': 'Engineer', 'city': 'HZ'})
    ('name:', 'COC', 'age:', 22, 'others:', {'Job': 'Engineer', 'City': 'HZ'})


 `**extra`表示把`extra`这个dict的所有`key-value`用关键字参数传入到函数的`**kw`参数，kw将获得一个dict，注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra

**命名关键字参数**
    1. 对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数。至于到底传入了哪些，就需要在函数内部通过kw检查。
    2. 仍以person()函数为例，我们希望检查是否有city和job参数


```python
def person(name, age, **kw):
    if 'city' in kw:
        pass
    if 'job' in kw:
        pass
    print('name:', name, 'age:', age, 'other:', kw)
# 但是调用者仍可以传入不受限制的关键字参数：
person('Jack', 24, city='Beijing', addr='Chaoyang', zipcode=123456)
```

    name: Jack age: 24 other: {'city': 'Beijing', 'addr': 'Chaoyang', 'zipcode': 123456}



```python
# 如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。这种方式定义的函数如下：
def person(name, age, *, job, city):
    print(name, age, city, job)
# 和关键字参数**kw不同，命名关键字参数需要一个特殊分隔符*，*后面的参数被视为命名关键字参数。
person('Jack', 24, city='Beijing', job='Engineer')
#如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了：
def person2(name, age, *args, city, job):
    print(name, age, args, city, job)
```

    Jack 24 Beijing Engineer



```python
# 命名关键字参数必须传入参数名，这和位置参数不同。如果没有传入参数名，调用将报错：
person('Jack', 24, 'Beijing', 'Engineer')
# 由于调用时缺少参数名city和job，Python解释器把这4个参数均视为位置参数，但person()函数仅接受2个位置参数。
# 命名关键字参数可以有缺省值，从而简化调用
def person(name, age, *, city='Beijing', job):
    print(name, age, city, job)
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-89-d3e3a315b65a> in <module>()
          1 # 命名关键字参数必须传入参数名，这和位置参数不同。如果没有传入参数名，调用将报错：
    ----> 2 person('Jack', 24, 'Beijing', 'Engineer')
    

    TypeError: person() takes 2 positional arguments but 4 were given


**参数组合**
    1. 定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。
    2. 但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。


```python
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)
def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)
print(f1(1, 2))
print(f1(1, 2, c=3))
print(f1(1, 2, 3, 'a', 'b'))
f1(1, 2, 3, 'a', 'b', x=99)
f2(1, 2, d=99, ext=None)
```

    a = 1 b = 2 c = 0 args = () kw = {}
    None
    a = 1 b = 2 c = 3 args = () kw = {}
    None
    a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}
    None
    a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
    a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}


**小结**
    1. Python的函数具有非常灵活的参数形态，既可以实现简单的调用，又可以传入非常复杂的参数。
    2. 默认参数一定要用不可变对象，如果是可变对象，程序运行时会有逻辑错误！
    3. 要注意定义可变参数和关键字参数的语法： 
        1. `*args`是可变参数，args接收的是一个tuple；
        2. `**kw`是关键字参数，kw接收的是一个dict。
    4. 以及调用函数时如何传入可变参数和关键字参数的语法：
        1. 可变参数既可以直接传入：`func(1, 2, 3)`，又可以先组装`list或tuple`，再通过`*args`传入：`func(*(1, 2, 3))`；
        2. 关键字参数既可以直接传入：func(a=1, b=2)，又可以先组装dict，再通过`**kw`传入：`func(**{'a': 1, 'b': 2})`。
        3. 使用`*args`和`**kw`是Python的习惯写法，当然也可以用其他参数名，但最好使用习惯用法。
        4. 命名的关键字参数是为了限制调用者可以传入的参数名，同时可以提供默认值。
        5. 定义命名的关键字参数在没有可变参数的情况下不要忘了写分隔符*，否则定义的将是位置参数。

#### 递归函数
    1. 在函数内部，可以调用其他函数。如果一个函数在内部调用自身本身，这个函数就是递归函数。
    2. 递归函数注意 递归公式与递归结束条件

## 高级特性
### 切片
取前3个元素，用一行代码就可以完成切片：


```python
L=["AA","vvv", "dddd","ddd"]
print(L[0:3]) #左闭右开[) 
```

    ['AA', 'vvv', 'dddd']


前4个数，每两个取一个：


```python
L=["AA","vvv", "dddd","ddd"]
print(L[0:3])
print(L[-2:])#记住倒数第一个元素的索引是-1。
print(L[-2:-1])
print(L[-1:-2])
```

    ['AA', 'vvv', 'dddd']
    ['dddd', 'ddd']
    ['dddd']
    []


反向：


```python
L=["AA","vvv", "dddd","ddd"]
L[::-1]# 反向取值
```




    ['ddd', 'dddd', 'vvv', 'AA']



tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple：


```python
(0, 1, 2, 3, 4)[:3]
```




    (0, 1, 2)



字符串'xxx'也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串


```python
'ABCDEF'[::-2]
```




    'FDB'



### 迭代
如果给定一个list或tuple，我们可以通过for循环来遍历这个list或tuple，这种遍历我们称为迭代（Iteration）。Python的for循环不仅可以用在list或tuple上，还可以作用在其他可迭代对象上.只要是可迭代对象，无论有无下标，都可以迭代，如dict，str等


```python
dict = {"a":1, "b":2, "c":{"hello":4, "sky": "full"}}
for k,v in dict.items():
    print(k,v)
for key in dict:
    print(key)
for value in dict.values():
    print(value)
```

    a 1
    b 2
    c {'hello': 4, 'sky': 'full'}
    a
    b
    c
    1
    2
    {'hello': 4, 'sky': 'full'}


1. 当我们使用for循环时，只要作用于一个可迭代对象，for循环就可以正常运行，而我们不太关心该对象究竟是list还是其他数据类型。
2. 那么，如何判断一个对象是可迭代对象呢？方法是通过collections模块的Iterable类型判断:


```python
from collections import Iterable
print(isinstance("ABC", Iterable))
print(isinstance(123, Iterable))
# 要对list实现类似Java那样的下标循环，Python内置的enumerate函数可以把一个list变成索引-元素对，
# 这样就可以在for循环中同时迭代索引和元素本身：
for i, value in enumerate(["a","d", "f"]):
    print(i, value)
for (x, y) in [(1, 1,), (2, 3),(4, 5)]:
    print(x, y)
```

    True
    False
    0 a
    1 d
    2 f
    1 1
    2 3
    4 5


### 列表生成式
列表生成式即List Comprehensions，是Python内置的非常简单却强大的可以用来**创建list**的生成式。
生成`list [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`可以用`list(range(1, 11))`：
可以用:


```python
# 写列表生成式时，把要生成的元素x * x放到(前面放输出结果)，后面跟for循环，就可以把list创建出来，十分有用，多写几次，很快就可以熟悉这种语法。
# for循环(后面放内容，判断条件)还可以加上if判断，这样我们就可以筛选出仅偶数的平方
[x*x for x in range(1, 11) if x%2 == 0]
```




    [4, 16, 36, 64, 100]



### 生成器
如果列表元素可以按照某种算法推算出来，那我们可以在循环的过程中不断推算出后续的元素。这样就不必创建完整的list，从而节省大量的空间。
一边循环一边计算的机制，称为生成器：generator。
第一种方法很简单，只要把一个列表生成式的`[]`改成`()`，就创建了一个generator：


```python
L = [x*x for x in range(10)]    
print(L)
L2 = (x*x for x in range(10))# 可以直接打印出list的每一个元素，要一个一个打印出来，可以通过next()函数获得generator的下一个返回值：
print(L2)
next(L2)
next(L2) # ，generator保存的是算法，每次调用next(g)，就计算出g的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出StopIteration的错误。
```

    [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
    <generator object <genexpr> at 0x7f82a028aa98>





    1




```python
# 正确的方法是使用for循环，因为generator也是可迭代对象：
L = (x*x for x in range(10))
for item in L:
    print(item)
```

    0
    1
    4
    9
    16
    25
    36
    49
    64
    81


定义generator的另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator：


```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print(b)
        a, b = b, a+b 
        n = n + 1
    return 'done'
# 其中a, b = b, a+b 
# 相当于 t = (b, a+b) t是一个元组，元素不变
# a = t[0] 
# b = t[1]
fib(5)
```

    1
    1
    2
    3
    5





    'done'




```python
# 可以看出，fib函数实际上是定义了斐波拉契数列的推算规则，可以从第一个元素开始，推算出后续任意的元素，这种逻辑其实非常类似generator。
# 上面的函数和generator仅一步之遥。要把fib函数变成generator，只需要把print(b)改为yield b就可以了
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a+b
        n = n+1
    return 'done'
# 如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator：
f = fib(6)
f
```




    <generator object fib at 0x7fdad92d8a98>



generator和函数的执行流程不一样。函数是顺序执行，遇到return语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用next()的时候执行，遇到`yield语句`返回，再次执行时`从上次返回的yield语句处`继续执行。


```python
def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)
o = odd()
next(o)
next(o)
next(o)
next(o)
```

    step 1
    step 2
    step 3



    ---------------------------------------------------------------------------

    StopIteration                             Traceback (most recent call last)

    <ipython-input-4-35e8ec08e813> in <module>()
         10 next(o)
         11 next(o)
    ---> 12 next(o)
    

    StopIteration: 


1. 可以看到，odd不是普通函数，而是generator，在执行过程中，遇到yield就中断，下次又继续执行。执行3次yield后，已经没有yield可以执行了，所以，第4次调用next(o)就报错。
2. 回到fib的例子，我们在循环过程中不断调用yield，就会不断中断。当然要给循环设置一个条件来退出循环，不然就会产生一个无限数列出来。
3. 同样的，把函数改成generator后，我们基本上从来不会用next()来获取下一个返回值，而是直接使用for循环来迭代：


```python
for n in fib(6):
    print(n)
```

    1
    1
    2
    3
    5
    8


但是用for循环调用`generator`时，发现拿不到`generator`的`return`语句的返回值。如果想要拿到返回值，必须捕获`StopIteration`错误，返回值包含在`StopIteration`的`value`中：


```python
g = fib(6)
# 格式 generator
while True:
    try:
        x = next(g)
        print("g:",x)
    except StopIteration as e:
        print("Generator return value:",e.value)
        break
```

    g: 1
    g: 1
    g: 2
    g: 3
    g: 5
    g: 8
    Generator return value: done



```python
def triangles():# 杨辉triangles
    currentLine = [1]

    while True:
        yield currentLine
        next1 = [0] + currentLine
        next2 = currentLine + [0]
        i = 0   # i 表示list各项的下标，用于将两个list各项数值相加。list长度不变
        currentLine = []
        while i < len(next1):
            currentLine.append(next1[i] + next2[i])
            i = i+1
# 方法二
def triangles ():
    List = [1]
    while True:
        yield List
        List =[1] + [value + List[index - 1] for index, value in enumerate(List) if index - 1 >= 0] + [1]
n = 0
for i in triangles():
    print(i)
    n = n+1
    if n == 10:
        break
```

    [1]
    [1, 1]
    [1, 2, 1]
    [1, 3, 3, 1]
    [1, 4, 6, 4, 1]
    [1, 5, 10, 10, 5, 1]
    [1, 6, 15, 20, 15, 6, 1]
    [1, 7, 21, 35, 35, 21, 7, 1]
    [1, 8, 28, 56, 70, 56, 28, 8, 1]
    [1, 9, 36, 84, 126, 126, 84, 36, 9, 1]


### 迭代器
我们已经知道，可以直接作用于for循环的数据类型有以下几种：
    1. 集合数据类型，如`list、tuple、dict、set、str`等；
    2. `generator`，包括生成器和带yield的`generator function`。
这些可以直接作用于for循环的对象统称为可迭代对象：Iterable。
可以使用`isinstance()`判断一个对象是否是Iterable对象。
生成器都是`Iterator`对象，但`list、dict、str`虽然是`Iterable`，却不是`Iterator`。

把`list、dict、str`等Iterable变成Iterator可以使用`iter()`函数


```python
from collections import Iterable
from collections import Iterator
print(isinstance([], Iterable))
print(isinstance(12, Iterable))
print(isinstance([], Iterator))
print(isinstance(iter((x for x in [1, 2, 3])), Iterator))
```

    True
    False
    False
    True


为什么list、dict、str等数据类型不是Iterator？
    这是因为Python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算。
    Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

**小结**
- 凡是可作用于for循环的对象都是`Iterable`类型；
- 凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列；
- 集合数据类型如`list、dict、str`等是`Iterable`但不是`Iterator`，不过可以通过`iter()`函数获得一个`Iterator`对象。
- Pythonfor循环本质上就是通过不断调用next()函数实现的

## 函数式编程
1. 函数是Python内建支持的一种封装，我们通过把大段代码拆成函数，通过一层一层的函数调用，就可以把复杂任务分解成简单的任务，这种分解可以称之为面向过程的程序设计。函数就是面向过程的程序设计的基本单元。
2. 函数式编程（请注意多了一个“式”字）——Functional Programming，虽然也可以归结到面向过程的程序设计，但其思想更接近数学计算。函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。
3. 函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！Python对函数式编程提供部分支持。由于Python允许使用变量，因此，Python不是纯函数式编程语言。


### 高阶函数
高阶函数英文叫Higher-order function。什么是高阶函数？我们以实际代码为例子，一步一步深入概念。

1. **变量可以指向函数**: 函数本身也可以赋值给变量，即：变量可以指向函数


```python
print(abs)# build-in function
# 可见，abs(-10)是函数调用，而abs是函数本身。
# 要获得函数调用结果，我们可以把结果赋值给变量：
x = abs(-10)
print(x)
# 如果一个变量指向了一个函数，那么，可否通过该变量来调用这个函数？用代码验证一下：
f = abs
print(f(-12))
```

    <built-in function abs>
    10
    12


2. **函数名也是变量**: 函数名其实就是指向函数的变量,对于abs()这个函数，完全可以把函数名abs看成变量，它**指向一个可以计算绝对值的函数**！如果把abs指向其他对象，会有什么情况发生？`abs = 10, abs(-10)`报错
3. **传入函数**: 既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。


```python
def add(x, y , f):
    return f(x) + f(y)
add(-1, -4, f)
# 把函数作为参数传入，这样的函数称为高阶函数，函数式编程就是指这种高度抽象的编程范式。
```




    5



#### map/reduce
1. Python内建了map()和reduce()函数。
2. 我们先看map。map()函数接收两个参数，一个是函数，一个是Iterable，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回。
3. `map()`传入的第一个参数是f，即函数对象本身。由于结果r是一个`Iterator`，`Iterator`是惰性序列，因此通过`list()`函数让它把整个序列都计算出来并返回一个`list`。


```python
list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```




    ['1', '2', '3', '4', '5', '6', '7', '8', '9']



`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算，其效果就是：`reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)`


```python
from functools import reduce
def add(x, y):
    return 10*x +y
print('1',reduce(add, [2, 3, 4, 5]))
#from functools import reduce
```

    1 2345



```python
# 我们就可以写出把str转换为int的函数
from functools import reduce
def add(x, y):
    return 10*x + y
def char2num(c):
    digits = {'0':0, '1':1, '2':2, '3':3, '4':4, '5':5, '6':6, '7':7, '8':8, '9':9}
    return digits[c]
reduce(add, map(char2num, '124363'))
```




    124363




```python
from functools import reduce
DIGITS = {'0':0, '1':1, '2':2, '3':3, '4':4, '5':5, '6':6, '7':7, '8':8, '9':9}
def char2int(s):
    def add(x, y):
        return 10*x + y
    def char2num(s):
        return DIGITS[s]
    return reduce(add, map(char2num, s))
char2int('45609876543')
# =>用lambda函数进一步简化成：
def char2int2(s):
    def char2num(c):
        return DIGITS[c]
    return reduce(lambda x, y : 10*x+y, map(char2num, s))
char2int2('456098765431')
```




    456098765431



#### filter
1. Python内建的`filter()`函数用于过滤序列。
2. 和`map()`类似，`filter()`也接收一个函数和一个序列。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。
3. `filter()`把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。


```python
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 可见用filter()这个高阶函数，关键在于正确实现一个“筛选”函数。
#注意到filter()函数返回的是一个Iterator，也就是一个惰性序列，所以要强迫filter()完成计算结果，需要用list()函数获得所有结果并返回list。
```




    [1, 5, 9, 15]




```python
# 先构造一个从3开始的奇数序列：
def _odd_iter(): #这是一个生成器，并且是一个无限序列。
    n = 1
    while True:
        n = n+2
        yield n
def _not_divisiable(n):# 定义一个筛选函数：
    return lambda x: x%n > 0
def primes():#定义一个生成器，不断返回下一个素数：
    yield 2
    it = _odd_iter()# 初始序列
    while True:
        n = next(it) # 返回序列的第一个数
        yield n
        it = filter(_not_divisiable(n), it)
for n in primes():
    if n < 10:
        print(n)
    else:
        break
```

    2
    3
    5
    7


#### sorted
sorted()函数也是一个高阶函数，它还可以接收一个key函数来实现自定义的排序，例如按绝对值大小排序：


```python
print(sorted([2, -4, 5, -9], key=abs))
print(sorted(['alsd', 'Affne', 'ieur', 'Zeir'],key=str.lower, reverse=True))
```

    [2, -4, 5, -9]
    ['Zeir', 'ieur', 'alsd', 'Affne']


### 返回函数
#### 函数作为返回值
高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。


```python
def sum_calc(*args):
    sum = 0
    for n in args:
        sum = sum+n
    return sum
# 如果不需要立刻求和，而是在后面的代码中，根据需要再计算怎么办？可以不返回求和的结果，而是返回求和的函数：
def lazy_sum(*args):
    def sum_calc():
        sum = 0
        for n in args:
            sum = sum + n
            return sum
        return sum
    return sum_calc
f = lazy_sum(1, 4, 5, 9)
print(f)
print(f())
```

    <function lazy_sum.<locals>.sum_calc at 0x7fdad89a6488>
    1


在这个例子中，我们在函数lazy_sum中又定义了函数sum，并且，内部函数sum可以引用外部函数lazy_sum的参数和局部变量，当lazy_sum返回函数sum时，相关参数和变量都保存在返回的函数中，这种称为“闭包（Closure）”的程序结构拥有极大的威力。


```python
# 请再注意一点，当我们调用lazy_sum()时，每次调用都会返回一个新的函数，即使传入相同的参数：
f1 = lazy_sum(1, 2, 3, 5, 6)
f2 = lazy_sum(1, 2, 3, 5, 6)
f1 == f2# f1()和f2()的调用结果互不影响
```




    False



#### 闭包
注意到返回的函数在其定义内部引用了局部变量args，所以，当一个函数A返回了一个函数B后，其内部的局部变量还被新函数B引用，所以，闭包用起来简单，实现起来可不容易。


```python
def count():
    fs = []
    for i in range(1, 4):
        def f():
            return i*i
        fs.append(f)
    return fs
f1, f2, f3 = count()
print(f1())
print(f2())
print(f3())
```

    9
    9
    9


1. 全部都是9！原因就在于返回的函数引用了变量i，但它并非立刻执行。等到3个函数都返回时，它们所引用的变量i已经变成了3，因此最终结果为9。
2. 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。
3. 如果一定要引用循环变量怎么办？方法是再创建一个函数，用该函数的参数绑定循环变量当前的值，无论该循环变量后续如何更改，已绑定到函数参数的值不变：


```python
def count():
    def f(j):
        def g():
            return j*j
        return g
    fs =[]
    for i in range(1, 4):
        fs.append(f(i))
    return fs
f1, f2, f3 = count()
print(f1())
print(f2())
print(f3())
```

    1
    4
    9


### 匿名函数
关键字`lambda`表示匿名函数，冒号前面的x表示函数参数。

匿名函数有个限制，就是只能有一个表达式，不用写return，返回值就是该表达式的结果。

用匿名函数有个好处，因为函数没有名字，不必担心函数名冲突。


```python
list(map(lambda x:x*x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
```




    [1, 4, 9, 16, 25, 36, 49, 64, 81]




```python
# 匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数：
f = lambda x: x*x
print(f)
print(f(5))
```

    <function <lambda> at 0x7fdad89a6950>
    25


### 装饰器
由于函数也是一个对象，而且函数对象可以被赋值给变量，所以，通过变量也能调用该函数。
质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator.


```python
def now():
    print('2018-10-10')
f = now
f()
# 函数对象有一个__name__属性，可以拿到函数的名字：
print(now.__name__)
print(f.__name__)
```

    2018-10-10
    now
    now


假设我们要增强now()函数的功能，比如，在函数调用前后自动打印日志，但又不希望修改now()函数的定义，这种在代码运行期间动态增加功能的方式，称之为“装饰器”（Decorator）。

本质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator，可以定义如下：


```python
def log(func):#高阶函数 返回打印函数
    def wrapper(*args, **kw):# 打印函数 打印日志，返回传入的函数
        print("wrapper call %s()" % func.__name__)
        return func(*args, **kw)
    return wrapper
# 观察上面的log，因为它是一个decorator，所以接受一个函数作为参数，并返回一个函数。
# 我们要借助Python的@语法，把decorator置于函数的定义处：

@log
def now():
    print("2018-10-1")
    
now()
```

    wrapper call now()
    2018-10-1


分析：
1. 调用`now()`函数，不仅会运行`now()`函数本身，还会在运行`now()`函数前打印一行日志：
2. 把`@log`放到`now()`函数的定义处，相当于执行了语句：`now = log(now)`
3. 由于`log()`是一个decorator，返回一个函数，所以，原来的`now()`函数仍然存在，只是现在同名的now变量指向了新的函数，
4. 于是调用`now()`将执行新函数，即在`log()`函数中返回的`wrapper()`函数。
5. `wrapper()`函数的参数定义是`(*args, **kw)`，因此，`wrapper()`函数可以接受任意参数的调用。
6. 在`wrapper()`函数内，首先打印日志，再紧接着调用原始函数。

如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂。比如，要自定义log的文本


```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print("%s %s()" % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print('2018-10-10')

now()
```

    excute now()
    2018-10-10


**分析**
和两层嵌套的decorator相比，3层嵌套的效果是这样的：now = log('execute')(now)

我们来剖析上面的语句，首先执行`log('execute')`，返回的是decorator函数，再调用返回的函数，参数是now函数，返回值最终是wrapper函数。

以上两种decorator的定义都没有问题，但还差最后一步。因为我们讲了函数也是对象，它有__name__等属性，但你去看经过decorator装饰之后的函数，它们的__name__已经从原来的'now'变成了'wrapper'。

因为返回的那个wrapper()函数名字就是'wrapper'，所以，需要把原始函数的__name__等属性复制到wrapper()函数中，否则，有些依赖函数签名的代码执行就会出错。
不需要编写`wrapper.__name__ = func.__name__`这样的代码，Python内置的`functools.wraps`就是干这个事的，所以，一个完整的decorator的写法如下：


```python
import functools
def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print("wrapper %s()"%func.__name__)
        return func(*args, **kw)
    return wrapper
@log
def now():
    print('2018003')
now()

# 针对带参数
def log2(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print("%s %s()"%(text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
@log2('fff')
def now2():
    print('fjnef')
now()
```

    wrapper now()
    2018003
    wrapper now()
    2018003


**小结**
1. 在面向对象（OOP）的设计模式中，decorator被称为装饰模式。OOP的装饰模式需要通过继承和组合来实现，而Python除了能支持OOP的decorator外，直接从语法层次支持decorator。Python的decorator可以用函数实现，也可以用类实现。
2. decorator可以增强函数的功能，定义起来虽然有点复杂，但使用起来非常灵活和方便。

请编写一个decorator，能在函数调用的前后打印出'begin call'和'end call'的日志。


```python
import functools, time
def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print("begin call")
        result = func(*args, **kw)
        print("end call")
        return result
    return wrapper

# @log
# def fn(x, y):
#     time.sleep(0.5)
#     return x + y
# fn(2, 3)

def logger(text):
    # 有三种方法可以进行判断:
    # if(hasattr(text,'__call__')):
    # if(type(text)!=type('')):
    # if(callable(text)):
    if(callable(text)):
        @functools.wraps(text)
        def wrapper(*args, **kw):
            print("%s!!"%(text.__name__))
            return text(*args, **kw)
        return wrapper
    def decorator(func):
        @functools.wraps(text)
        def wrapper(*args, **kw):
            print("%s say: %s"% (func.__name__, text))
            return func(*args, **kw)
        return wrapper
    return decorator

@logger
def fn(x, y):
    return x+y

@logger("use execute")
def fn2(x, y):
    return x*y
print(fn(1, 2))
print(fn2(3, 4))
```

    fn!!
    3
    fn2 say: use execute
    12


### 偏函数
Python的functools模块提供了很多有用的功能，其中一个就是偏函数（Partial function）。要注意，这里的偏函数和数学意义上的偏函数不一样。当函数的参数个数太多，需要简化时，使用functools.partial可以创建一个新的函数，这个新函数可以固定住原函数的部分参数，从而在调用时更简单。


```python
print(int('12345'))
print(int("12345", base = 8))
print(int("12345", 16))
print(int('101010', base=2))#字符串转成数字101010，1010101按照二进制转成10进制显示
# functools.partial就是帮助我们创建一个偏函数的，不需要我们自己定义int2()，可以直接使用下面的代码创建一个新的函数int2：
# 简单总结functools.partial的作用就是，把一个函数的某些参数给固定住（也就是设置默认值），返回一个新的函数，调用这个新函数会更简单。
```

    12345
    5349
    74565
    42



```python
import functools
int2 = functools.partial(int, base=2)
int2('100010')
#当函数的参数个数太多，需要简化时，使用functools.partial可以创建一个新的函数，这个新函数可以固定住原函数的部分参数，从而在调用时更简单。
```




    34



## 模块
1. 为了编写可维护的代码，我们把很多函数分组，分别放到不同的文件里，这样，每个文件包含的代码就相对较少，很多编程语言都采用这种组织代码的方式。在Python中，一个.py文件就称之为一个模块（Module）。
2. 模块是一组Python代码的集合，可以使用其他模块，也可以被其他模块使用。

**优势：**
1. 最大的好处是大大提高了代码的可维护性。其次，编写代码不必从零开始。当一个模块编写完毕，就可以被其他地方引用。我们在编写程序的时候，也经常引用其他模块，包括Python内置的模块和来自第三方的模块。
2. 使用模块还可以避免函数名和变量名冲突。相同名字的函数和变量完全可以分别存在不同的模块中，因此，我们自己在编写模块时，不必考虑名字会与其他模块冲突。但是也要注意，尽量不要与内置函数名字冲突。

创建自己的模块时，要注意：

- 模块名要遵循Python变量命名规范，不要使用中文、特殊字符；
- 模块名不要和系统模块名冲突，最好先查看系统是否已存在该模块，检查方法是在Python交互环境执行import abc，若成功则说明系统存在此模块。

引入了包以后，只要顶层的包名不与别人冲突，那所有模块都不会与别人冲突。现在，`abc.py`模块的名字就变成了`mycompany.abc`，类似的，`xyz.py`的模块名变成了`mycompany.xyz`。
请注意，每一个包目录下面都会有一个`__init__.py`的文件，这个文件是必须存在的，否则，Python就把这个目录当成普通目录，而不是一个包。`__init__.py`可以是空文件，也可以有Python代码，因为`__init__.py`本身就是一个模块，而它的模块名就是`mycompany`。
类似的，可以有多级目录，组成多级层次的包结构。比如如下的目录结构：

```
mycompany
 ├─ web
 │  ├─ __init__.py
 │  ├─ utils.py
 │  └─ www.py
 ├─ __init__.py
 ├─ abc.py
 └─ xyz.py
 ```

### 使用
就是导入该模块，例如`import os`


```python
#!/usr/bin/env python3 # 第1行和第2行是标准注释，第1行注释可以让这个hello.py文件直接在Unix/Linux/Mac上运行，
#_*_ coding:utf-8 _*_ # 第2行注释表示.py文件本身使用标准UTF-8编码；

'a test module'# 第4行是一个字符串，表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释；
__author__ = "fallenk liu" #第6行使用__author__变量把作者写进去，
import sys # 导入sys模块后，我们就有了变量sys指向该模块，利用sys这个变量，就可以访问sys模块的所有功能。

def test():
    args = sys.argv # sys模块有一个argv变量，用list存储了命令行的所有参数。argv至少有一个元素，因为第一个参数永远是该.py文件的名称，
    if len(args) == 1:
        print("Hello world")
    elif len(args) == 2:
        print('Hello, %s!' % args[1])
    else:
        print("Too many arguments")

if __name__ == "__main__": # 当我们在命令行运行hello模块文件时，Python解释器把一个特殊变量__name__置为__main__，
    test()# 而如果在其他地方导入该hello模块时，if判断将失败，
          #因此，这种if测试可以让一个模块通过命令行运行时执行一些额外的代码，最常见的就是运行测试 
```

    Too many arguments


**作用域**
1. 仅仅在模块内部使用。在Python中，是通过_前缀来实现的
2. 类似`__xxx__`这样的变量是特殊变量，可以被直接引用，但是有特殊用途，比如上面的`__author__`，`__name__`就是特殊变量，hello模块定义的文档注释也可以用特殊变量`__doc__`访问，我们自己的变量一般不要用这种变量名；
3. 类似`_xxx`和`__xxx`这样的函数或变量就是非公开的（private），不应该被直接引用，比如`_abc`，`__abc`等；

### 安装第三方模块
1. 安装第三方模块，是通过包管理工具pip完成的
2. 注意：Mac或Linux上有可能并存Python 3.x和Python 2.x，因此对应的pip命令是pip3。
安装常用模块
1. 使用Anaconda, 即可在终端输入import numpy
2. 模块搜索路径: 默认情况下，Python解释器会搜索当前目录、所有已安装的内置模块和第三方模块，搜索路径存放在sys模块的path变量中


```python
import sys
sys.path
```




    ['',
     '/opt/conda/lib/python36.zip',
     '/opt/conda/lib/python3.6',
     '/opt/conda/lib/python3.6/lib-dynload',
     '/opt/conda/lib/python3.6/site-packages',
     '/opt/conda/lib/python3.6/site-packages/Mako-1.0.7-py3.6.egg',
     '/opt/conda/lib/python3.6/site-packages/cycler-0.10.0-py3.6.egg',
     '/opt/conda/lib/python3.6/site-packages/IPython/extensions',
     '/home/jovyan/.ipython']



## 面向对象编程
面向对象编程——Object Oriented Programming，简称OOP，是一种程序设计思想。OOP把对象作为程序的基本单元，一个对象包含了数据和操作数据的函数。

- 面向过程的程序设计把计算机程序视为一系列的命令集合，即一组函数的顺序执行。为了简化程序设计，面向过程把函数继续切分为子函数，即把大块函数通过切割成小块函数来降低系统的复杂度。
- 而面向对象的程序设计把计算机程序视为一组对象的集合，而每个对象都可以接收其他对象发过来的消息，并处理这些消息，计算机程序的执行就是一系列消息在各个对象之间传递。

在Python中，所有数据类型都可以视为对象，当然也可以自定义对象。自定义的对象数据类型就是面向对象中的类（Class）的概念。
我们以一个例子来说明面向过程和面向对象在程序流程上的不同之处。


```python
# 假设我们要处理学生的成绩表，为了表示一个学生的成绩，面向过程的程序可以用一个dict表示：
std1 = { 'name': 'Michael', 'score': 98 }
std2 = { 'name': 'Bob', 'score': 81 }
# 而处理学生成绩可以通过函数实现，比如打印学生的成绩：
def print_score(std):
    print("%s %s"%(std["name"], std["score"]))
```

如果采用面向对象的程序设计思想，我们首选思考的不是程序的执行流程，而是Student这种数据类型应该被视为一个对象，这个对象拥有name和score这两个属性（Property）。如果要打印一个学生的成绩，首先必须创建出这个学生对应的对象，然后，给对象发一个print_score消息，让对象自己把自己的数据打印出来。


```python
class Student(object):
    def __init__(self, name, score):
        self.name = name
        self.score = score
    def print_score(self):
        print("%s is %s"%(self.name, self.score))
        
# 给对象发消息实际上就是调用对象对应的关联函数，我们称之为对象的方法（Method）。面向对象的程序写出来就像这样：
bart = Student("bart Simspn", 19)
lisa = Student("lisa Simspn", 30)
bart.print_score()
lisa.print_score()
```

    bart Simspn is 19
    lisa Simspn is 30


**总结**

面向对象的设计思想是从自然界中来的，因为在自然界中，类（Class）和实例（Instance）的概念是很自然的。Class是一种抽象概念，比如我们定义的Class——Student，是指学生这个概念，而实例（Instance）则是一个个具体的Student，比如，Bart Simpson和Lisa Simpson是两个具体的Student。

所以，面向对象的设计思想是抽象出Class，根据Class创建Instance。

面向对象的抽象程度又比函数要高，因为一个Class既包含数据，又包含操作数据的方法。

### 类和实例
面向对象最重要的概念就是类（Class）和实例（Instance），必须牢记类是抽象的模板，比如Student类，而实例是根据类创建出来的一个个具体的“对象”，每个对象都拥有相同的方法，但各自的数据可能不同。

1. 以Student类为例，在Python中，定义类是通过`class`关键字：
2. `class`后面紧接着是类名，即Student，类名通常是大写开头的单词，紧接着是`(object)`，表示该类是从哪个类继承下来的，继承的概念我们后面再讲，通常，如果没有合适的继承类，就使用object类，这是所有类最终都会继承的类。
3. 定义好了Student类，就可以根据Student类创建出Student的实例，创建实例是通过`类名+()`实现的


```python
class Student(object):
    pass
bart = Student()
print(Student)
print(bart)
# 可以看到，变量bart指向的就是一个Student的实例，后面的0x10a67a590是内存地址，每个object的地址都不一样，而Student本身则是一个类。
```

    <class '__main__.Student'>
    <__main__.Student object at 0x7fdad8998eb8>



```python
class Studetn(object):
    def __init__(self, name, score):
        self.name = name
        self.score = score
```

1. 由于类可以起到模板的作用，因此，可以在创建实例的时候，把一些我们认为必须绑定的属性强制填写进去。通过定义一个特殊的__init__方法，在创建实例的时候，就把name，score等属性绑上去： 
2. 特殊方法“__init__”前后分别有两个下划线！！！
3. 注意到__init__方法的第一个参数永远是self，表示创建的实例本身，
4. 因此，在__init__方法内部，就可以把各种属性绑定到self，因为self就指向创建的实例本身。
5. 有了__init__方法，在创建实例的时候，就不能传入空的参数了，必须传入与__init__方法匹配的参数，但self不需要传，Python解释器自己会把实例变量传进去：
6. 和普通的函数相比，在类中定义的函数只有一点不同，**就是第一个参数永远是实例变量self**，并且，调用时，不用传递该参数。除此之外，类的方法和普通函数没有什么区别，所以，你仍然可以用默认参数、可变参数、关键字参数和命名关键字参数。

#### 数据封装
1. 面向对象编程的一个重要特点就是数据封装。在上面的Student类中，每个实例就拥有各自的name和score这些数据。我们可以通过函数来访问这些数据.
2. 封装的另一个好处是可以给Student类增加新的方法，比如get_grade

**小结**
1. 类是创建实例的模板，而实例则是一个一个具体的对象，各个实例拥有的数据都互相独立，互不影响；
2. 方法就是与实例绑定的函数，和普通函数不同，方法可以直接访问实例的数据；
3. 通过在实例上调用方法，我们就直接操作了对象内部的数据，但无需知道方法内部的实现细节。
4. 和静态语言不同，Python允许对实例变量绑定任何数据，也就是说，对于两个实例变量，虽然它们都是同一个类的不同实例，但拥有的变量名称都可能不同：

### 访问限制
1. 在Class内部，可以有属性和方法，而外部代码可以通过直接调用实例变量的方法来操作数据，这样，就隐藏了内部的复杂逻辑
2. 如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线`__`，在Python中，实例的变量名如果以`__`开头，就变成了一个私有变量（private），只有内部可以访问，外部不能访问，所以，我们把Student类改一改：


```python
class Student(object):
    def __init__(self, name, score):
        self.__name = name
        self.__score = score
    def print_score(self):
        print("%s is %s" %(self.__name, self.__score))
    def get_score(self):
        return self.__score
    def set_score(self, score):
        if score >= 0 and score <= 100:
            self.__score = score
        else:
            raise ValueError("bad value")
```

### 继承和多态
在OOP程序设计中，当我们定义一个class的时候，可以从某个现有的class继承，新的class称为子类（Subclass），而被继承的class称为基类、父类或超类（Base class、Super class）。
1. 最大的好处是子类获得了父类的全部功能。
2. 当子类和父类都存在相同的run()方法时，我们说，子类的run()覆盖了父类的run()，在代码运行的时候，总是会调用子类的run()。这样，我们就获得了继承的另一个好处：多态。
多态：
多态的好处就是，当我们需要传入Dog、Cat、Tortoise……时，我们只需要接收Animal类型就可以了，因为Dog、Cat、Tortoise……都是Animal类型，然后，按照Animal类型进行操作即可。由于Animal类型有run()方法，因此，传入的任意类型，只要是Animal类或者子类，就会自动调用实际类型的run()方法，这就是多态的意思。

对于一个变量，我们只需要知道它是Animal类型，无需确切地知道它的子类型，就可以放心地调用run()方法，而具体调用的run()方法是作用在Animal、Dog、Cat还是Tortoise对象上，由运行时该对象的确切类型决定，这就是多态真正的威力：调用方只管调用，不管细节，而当我们新增一种Animal的子类时，只要确保run()方法编写正确，不用管原来的代码是如何调用的。这就是著名的“开闭”原则：
1. 对扩展开放：允许新增Animal子类；
2. 对修改封闭：不需要修改依赖Animal类型的run_twice()等函数。

**静态语言 vs 动态语言**
1. 对于静态语言（例如Java）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者它的子类，否则，将无法调用run()方法。
2. 对于Python这样的动态语言来说，则不一定需要传入Animal类型。我们只需要保证传入的对象有一个run()方法就可以了。


```python
class Timer(object):
    def run(self):
        print("start...")
```

1. 这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。
2. Python的“file-like object“就是一种鸭子类型。对真正的文件对象，它有一个read()方法，返回其内容。但是，许多对象，只要有read()方法，都被视为“file-like object“。许多函数接收的参数就是“file-like object“，你不一定要传入真正的文件对象，完全可以传入任何实现了read()方法的对象。

**小结**
继承可以把父类的所有功能都直接拿过来，这样就不必重零做起，子类只需要新增自己特有的方法，也可以把父类不适合的方法覆盖重写。

动态语言的鸭子类型特点决定了继承不像静态语言那样是必须的。

### 获取对象信息
当我们拿到一个对象的引用时，如何知道这个对象是什么类型、有哪些方法呢？
1. 使用type()
    - 首先，我们来判断对象类型，使用type()函数：
    - 基本类型都可以用type()判断：


```python
print(type(123))
print(type(None))
# 如果一个变量指向函数或者类，也可以用type()判断：
print(type(abs))
def fn():
    pass
print(type(fn))# type()返回对应的class类型
```

    <class 'int'>
    <class 'NoneType'>
    <class 'builtin_function_or_method'>
    <class 'function'>



```python
**使用isinstance()**
1. 对于class的继承关系来说，使用type()就很不方便。我们要判断class的类型，可以使用isinstance()函数。
2. `isinstance()`判断的是一个对象是否是该类型本身，或者位于该类型的父继承链上。
3. 总是优先使用isinstance()判断类型，可以将指定类型及其子类“一网打尽”。
```


```python
print(isinstance('a', str))
print(isinstance([1, 2, 3], (list, tuple)))
```

    True
    True


**使用dir()**
1. 如果要获得一个对象的所有属性和方法，可以使用`dir()`函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法：


```python
dir("ABC")
```

类似`__xxx__`的属性和方法在Python中都是有特殊用途的，比如`__len__`方法返回长度。在Python中，如果你调用`len()`函数试图获取一个对象的长度，实际上，在`len()`函数内部，它自动去调用该对象的`__len__()`方法，所以，下面的代码是等价的


```python
print(len('ABC'))
print("ABC".__len__())
```

    3
    3



```python
# 我们自己写的类，如果也想用len(myObj)的话，就自己写一个__len__()方法：
class MyDog(object):
    def __len__(self):
        return 100
dog = MyDog()
len(dog)
# 我们自己写的类，如果也想用len(myObj)的话，就自己写一个__len__()方法：
"ABC".lower()
```




    'abc'




```python
class MyObject(object):
    def __init__(self):
        self.x = 9
    def power(self):
        return self.x*self.x
obj = MyObject()
# dir(obj)
# 配合getattr()、setattr()以及hasattr()，我们可以直接操作一个对象的状态：
hasattr(obj, 'x') # 有属性'x'吗？
obj.x
hasattr(obj, 'y')# 有属性'y'吗？
setattr(obj, 'y', 19) # 设置一个属性'y'
obj.y
# 可以传入一个default参数，如果属性不存在，就返回默认值：
getattr(obj, 'z', 404) # 获取属性'z'，如果不存在，返回默认值404
# 也可以获得对象的方法：
hasattr(obj, 'power') # 有属性'power'吗？
getattr(obj, 'power') # 获取属性'power'
fn = getattr(obj, 'power') # 获取属性'power'并赋值到变量fn
fn
fn()
```




    81



**小结**
通过内置的一系列函数，我们可以对任意一个Python对象进行剖析，拿到其内部的数据。要注意的是，只有在不知道对象信息的时候，我们才会去获取对象信息。如果可以直接写：


```python
sum = obj.x + obj.y # 不要 sum = getattr(obj, 'x') + getattr(obj, 'y')
```

### 实例属性和类属性
1. 由于Python是动态语言，根据类创建的实例可以任意绑定属性。
2. 给实例绑定属性的方法是**通过实例变量，或者通过self变量**：


```python
class Student(object):
    def __init__(self, name):
        self.name = name
s = Student('Bob')
s.score = 90

```


```python
# Student类本身需要绑定一个属性呢？可以直接在class中定义属性，这种属性是类属性，归Student类所有：
class Student(object):
    name = 'Student'
# 从上面的例子可以看出，在编写程序的时候，千万不要对实例属性和类属性使用相同的名字，
# 因为相同名称的实例属性将屏蔽掉类属性，但是当你删除实例属性后，再使用相同的名称，访问到的将是类属性。
```

**小结**
实例属性属于各个实例所有，互不干扰；

类属性属于类所有，所有实例共享一个属性；

不要对实例属性和类属性使用相同的名字，否则将产生难以发现的错误。

## 面向对象高阶编程
1. 数据封装、继承和多态只是面向对象程序设计中最基础的3个概念。在Python中，面向对象还有很多高级特性，允许我们写出非常强大的功能。
2. 多重继承、定制类、元类等概念

### 使用__slots__
正常情况下，当我们定义了一个class,创建了一个class实例，我们可以给实例绑定任何属性和方法，即动态语言的灵活性：


```python
# 先定义class
class Student(object):
    pass
# 然后，尝试给实例绑定一个属性：
s = Student()
s.name = 'Maichel'
print(s.name)
# 尝试给实例绑定一个方法：
def set_age(self, age):
    self.age = age
from types import MethodType
s.set_age = MethodType(set_age, s)# 给实例绑定一个方法
s.set_age(23) # 调用实例方法
print(s.age)
# 但是，给一个实例绑定的方法，对另一个实例是不起作用的
```

    Maichel
    23



```python
# 为了给所有实例都绑定方法，可以给class绑定方法：
def set_score(self, score):
    self.score = score
Student.set_score = set_score
# 给class绑定方法后，所有实例均可调用：
s.set_score(10)
print(s.score)
s2 = Student()
s2.set_score(12)
print(s2.score)
# 通常情况下，上面的set_score方法可以直接定义在class中，但动态绑定允许我们在程序运行的过程中动态给class加上功能，这在静态语言中很难实现。
```

    10
    12


使用__slots__:
1. 限制实例的属性，只允许对Student实例添加name和age属性。
2. 为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class实例能添加的属性：


```python
class Student(object):
    __slots__ = ("name", "age", "score")# 用tuple定义允许绑定的属性名称
s = Student()
s.age = 10
print(s.age)
s.score = 20
print(s.words)
# 由于'score'没有被放到__slots__中，所以不能绑定score属性，试图绑定score将得到AttributeError的错误。
# 使用__slots__要注意，__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的：
```

    10



    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-166-35f3a467de63> in <module>()
          5 print(s.age)
          6 s.score = 20
    ----> 7 print(s.words)
          8 # 由于'score'没有被放到__slots__中，所以不能绑定score属性，试图绑定score将得到AttributeError的错误。
          9 # 使用__slots__要注意，__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的：


    AttributeError: 'Student' object has no attribute 'words'



```python
class GrandStudent(Student):
    pass
s2 = GrandStudent()
s2.score = 30
print(s2.score)
# 除非在子类中也定义__slots__，这样，子类实例允许定义的属性就是自身的__slots__加上父类的__slots__。
```

    30


### 使用@property
在绑定属性时，如果我们直接把属性暴露出去，虽然写起来很简单，但是，没办法检查参数，导致可以把成绩随便改：


```python
s = Student()
s.score = 9999
```


```python
# 这显然不合逻辑。为了限制score的范围，可以通过一个set_score()方法来设置成绩，
# 再通过一个get_score()来获取成绩，这样，在set_score()方法里，就可以检查参数：
class Student(object):
    def get_score(self):
        return self._score
#     def set_score(self, score):
#         self._score = score
    def set_score(self, value):
        if not isinstance(value, int):
            raise ValueError("score must be an integer!")
        if value > 100 or value < 0:
            raise ValueError("score must between 0-100!")
        self._score = value
# 现在，对任意的Student实例进行操作，就不能随心所欲地设置score了：
s = Student()
s.set_score(60)
s.get_score()
s.set_score(9999)
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-172-9bcb4485b1da> in <module>()
         16 s.set_score(60)
         17 s.get_score()
    ---> 18 s.set_score(9999)
    

    <ipython-input-172-9bcb4485b1da> in set_score(self, value)
         10             raise ValueError("score must be an integer!")
         11         if value > 100 or value < 0:
    ---> 12             raise ValueError("score must between 0-100!")
         13         self._score = value
         14 # 现在，对任意的Student实例进行操作，就不能随心所欲地设置score了：


    ValueError: score must between 0-100!


但是，上面的调用方法又略显复杂，没有直接用属性这么直接简单。
1. 有没有既能检查参数，又可以用类似属性这样简单的方式来访问类的变量呢？
2. 装饰器（decorator）可以给函数动态加上功能,对于类的方法，装饰器一样起作用。Python内置的`@property`装饰器就是负责把一个方法变成属性调用的：


```python
class Student(object):
    @property
    def score(self):
        return self._score
    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError("score must be integer!")
        if value < 0 or value > 100:
            raise ValueError("score must between 0-100!")
        self._score = value
```

把一个`getter`方法变成属性，只需要加上`@property`就可以了，此时，`@property`本身又创建了另一个装饰器` @score.setter ` ，负责把一个`setter`方法变成属性赋值，于是，我们就拥有一个可控的属性操作：


```python
s = Student()
s.score = 50 # OK,实际转化为s.set_score(50)
s.score# OK, 实际转为s.get_score()
s.score = 9999
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-175-d69d8b502813> in <module>()
          2 s.score = 50 # OK,实际转化为s.set_score(50)
          3 s.score# OK, 实际转为s.get_score()
    ----> 4 s.score = 9999
    

    <ipython-input-173-7a33d5731e5a> in score(self, value)
          8             raise ValueError("score must be integer!")
          9         if value < 0 or value > 100:
    ---> 10             raise ValueError("score must between 0-100!")
         11         self._score = value


    ValueError: score must between 0-100!


1. 注意到这个神奇的`@property` ，我们在对实例属性操作的时候，就知道该属性很可能不是直接暴露的，而是通过`getter`和`setter`方法来实现的。
2. 还可以定义只读属性，只定义`getter`方法，不定义`setter`方法就是一个只读属性：


```python
class Student(object):
    @property
    def birth(self):
        return self._birth
    @birth.setter
    def birth(self, value):
        if not isinstance(value, int):
            raise ValueError("birth must be integer!")
        self._birth = value
    @property
    def age(self):
        return 2018 - self._birth
s = Student()
s.birth = 1999
s.birth
s.age
```




    19



上面的birth是可读写属性，而age就是一个只读属性，因为age可以根据birth和当前时间计算出来。

**小结**
@property广泛应用在类的定义中，可以让调用者写出简短的代码，同时保证对参数进行必要的检查，这样，程序运行时就减少了出错的可能性。

### 多重继承
继承是面向对象编程的一个重要的方式，因为通过继承，子类就可以扩展父类的功能。

回忆一下Animal类层次的设计，假设我们要实现以下4种动物：
- Dog - 狗狗；
- Bat - 蝙蝠；
- Parrot - 鹦鹉；
- Ostrich - 鸵鸟。
按照分类有多种不同的分类,正确的做法是采用多重继承。首先，主要的类层次仍按照哺乳类和鸟类设计：


```python
class Animal(object):
    pass
class Mammal(Animal):
    pass
class Bird(Animal):
    pass
class Dog(Mammal):
    pass
class Bat(Mammal):
    pass
class Parrot(Bird):
    pass
class Ostrich(Bird):
    pass
# 现在，我们要给动物再加上Runnable和Flyable的功能，只需要先定义好Runnable和Flyable的类：
class Runnable(object):
    def run(self):
        print("Running ...")
class Flyable(object):
    def fly(self):
        print("Flying ...")
# 对于需要Runnable功能的动物，就多继承一个Runnable，例如Dog：通过多重继承，一个子类就可以同时获得多个父类的所有功能。
class Dog(Mammal, Runnable):
    pass
class Ostrich(Bird, Flyable):
    pass
```

#### MixIn
1. 在设计类的继承关系时，通常，**主线都是单一继承下来的**，例如，Ostrich继承自Bird。但是，如果需要“混入”额外的功能，通过多重继承就可以实现，比如，让Ostrich除了继承自Bird外，再同时继承Runnable。这种设计通常称之为MixIn。
2. 为了更好地看出继承关系，我们把`Runnable`和`Flyabl`e改为`RunnableMixIn`和`FlyableMixIn`。类似的，你还可以定义出肉食动物`CarnivorousMixIn`和植食动物`HerbivoresMixIn` ，让某个动物同时拥有好几个MixIn：

```
class Dog(Mammal, RunnableMixIn, CarnivorousMixIn):
    pass
```

MixIn的目的就是给一个类增加多个功能，这样，在设计类的时候，我们优先考虑通过多重继承来组合多个MixIn的功能，而不是设计多层次的复杂的继承关系。

Python自带的很多库也使用了MixIn。举个例子，Python自带了TCPServer和UDPServer这两类网络服务，而要同时服务多个用户就必须使用多进程或多线程模型，这两种模型由ForkingMixIn和ThreadingMixIn提供。通过组合，我们就可以创造出合适的服务来。

比如，编写一个多进程模式的TCP服务，定义如下：


```python
class MyTCPServer(TCPServer, ForkingMixIn):
    pass
# 编写一个多线程模式的UDP服务，定义如下：
class MyUDP(UDPServer, ThreadingMixIn):
    pass
# 如果你打算搞一个更先进的协程模型，可以编写一个CoroutineMixIn：
class MYTCPServer(TCPServer, CoroutineMixIn):
    pass
# 这样一来，我们不需要复杂而庞大的继承链，只要选择组合不同的类的功能，就可以快速构造出所需的子类。
```

小结
由于Python允许使用多重继承，因此，MixIn就是一种常见的设计。

只允许单一继承的语言（如Java）不能使用MixIn的设计。

### 定制类
看到类似`__slots__`这种形如`__xxx__`的变量或者函数名就要注意，这些在Python中是有特殊用途的。

`__slots__`我们已经知道怎么用了，`__len__()`方法我们也知道是为了能让class作用于`len()`函数。

除此之外，Python的class中还有许多这样有特殊用途的函数，可以帮助我们定制类。

 `__str__`
 
我们先定义一个Student类，打印一个实例：


```python
class Student(object):
    def __init__(self, name):
        self.__name = name
print(Student("Maichel"))
```

    <__main__.Student object at 0x7fdad89f06d8>



```python
# 打印出一堆<__main__.Student object at 0x7fdad89f06d8>，不好看。
# 能打印得好只需要,定义好__str__()方法，返回一个好看的字符串就可以了：
class Student(object):
    def __init__(self, name):
        self._name = name
    def __str__(self):
        return "Student object (name %s)"% self._name
print(Student("Maichels"))
```

    Student object (name Maichels)



```python
# 这样打印出来的实例，不但好看，而且容易看出实例内部重要的数据。

# 但是发现直接敲变量不用print，打印出来的实例还是不好看：
s = Student("Maicherls")
s
```




    <__main__.Student at 0x7fdad89f0ac8>



这是因为直接显示变量调用的不是`__str__()` ，而是`__repr__()` ，两者的区别是`__str__()`返回用户看到的字符串，而`__repr__()`返回程序开发者看到的字符串，也就是说，`__repr__()`是为调试服务的。

解决办法是再定义一个`__repr__()`。但是通常`__str__()`和`__repr__()`代码都是一样的，所以，有个偷懒的写法：


```python
class Student(object):
    def __init__(self, name):
        self.name = name
    def __str__(self):
        return 'Student object (name=%s)' % self.name
    __repr__ = __str__
```

__iter__

如果一个类想被用于`for ... in`循环，类似list或tuple那样，就必须实现一个`__iter__()`方法，该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的`__next__()`方法拿到循环的下一个值，直到遇到`StopIteration`错误时退出循环。

我们以斐波那契数列为例，写一个Fib类，可以作用于for循环：


```python
class Fib(object):
    def __init__(self):
        self.a, self.b = 0, 1# 初始化两个计数器a，b
    def __iter__(self):
        return self# 实例本身就是迭代对象，故返回自己
    def __next__(self):
        self.a, self.b = self.b, self.a+self.b
        if self.a > 20: # 退出循环的条件
            raise StopIteration()
        return self.a# 返回下一个值
for n in Fib():
    print(n)
```

    1
    1
    2
    3
    5
    8
    13


__getitem__

Fib实例虽然能作用于for循环，看起来和list有点像，但是，把它当成list来使用还是不行，比如，取第5个元素,会报错；
要表现得像list那样按照下标取出元素，需要实现`__getitem__()`方法：


```python
class Fib(object):
    def __getitem__(self, n):
        if isinstance(n, int): # n是索引
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a
        if isinstance(n, slice): # n是切片
            start = n.start
            stop = n.stop
            if start is None:
                start = 0
            a, b = 1, 1
            L = []
            for x in range(stop):
                if x >= start:
                    L.append(a)
                a, b = b, a + b
            return L
f = Fib()
f[0]
f[9]
```




    55



__getattr__

错误信息很清楚地告诉我们，没有找到score这个attribute。要避免这个错误，除了可以加上一个score属性外，那就是写一个`__getattr__()`方法，动态返回一个属性。修改如下：


```python
class Student(object):

    def __init__(self):
        self.name = 'Michael'

    def __getattr__(self, attr):
        if attr=='score':
            return 99
```

此外，注意到任意调用如s.abc都会返回None，这是因为我们定义的`__getattr__`默认返回就是None。要让class只响应特定的几个属性，我们就要按照约定，抛出`AttributeError`的错误：


```python
class Student(object):

    def __getattr__(self, attr):
        if attr=='age':
            return lambda: 25
        raise AttributeError('\'Student\' object has no attribute \'%s\'' % attr)
```

这实际上可以把一个类的所有属性和方法调用全部动态化处理了，不需要任何特殊手段。

这种完全动态调用的特性有什么实际作用呢？作用就是，可以针对完全动态的情况作调用。
利用完全动态的`__getattr__`，我们可以写出一个链式调用：


```python
class Chain():
    def __init__(self, path=''):
        self._path = path
    def __getattr__(self, path):
        return Chain("%s/%s"% (self._path, path))
    def __str__(self):
        return self._path
    __repr__ = __str__
Chain().status.user.timeline.list
```




    /status/user/timeline/list



 `__call__`
 
一个对象实例可以有自己的属性和方法，当我们调用实例方法时，我们用instance.method()来调用。能不能直接在实例本身上调用呢？在Python中，答案是肯定的。
任何类，只需要定义一个`__call__()`方法，就可以直接对实例进行调用。请看示例：


```python
class Student(object):
    def __init__(self, name):
        self.name = name
    def __call__(self):
        print("My name is %s"%self.name) 
s = Student('Michael')
s()
```

    My name is Michael


`__call__()`还可以定义参数。对实例进行直接调用就好比对一个函数进行调用一样，所以你完全可以把对象看成函数，把函数看成对象，因为这两者之间本来就没啥根本的区别。

如果你把对象看成函数，那么函数本身其实也可以在运行期动态创建出来，因为类的实例都是运行期创建出来的，这么一来，我们就模糊了对象和函数的界限。

那么，怎么判断一个变量是对象还是函数呢？其实，更多的时候，我们需要判断一个对象是否能被调用，能被调用的对象就是一个Callable对象，比如函数和我们上面定义的带有`__call__()`的类实例：


```python
callable(Student("name"))
callable([1, 2, 4])
```




    False




```python
class Chain(object):
  def __init__(self,path='GET '):
    self._path = path

  def __getattr__(self,path):
    return Chain('%s/%s' % (self._path,path))
  def __call__(self,args):
    return Chain('%s/%s' % (self._path,args))

  def __str__(self):
    return self._path
  __repr__ = __str__

print(Chain().users('michael').repos)
```

    GET /users/michael/repos


### 使用枚举类
1. 定义常量时,大写整数定义
2. 为这样的枚举类型定义一个class类型，然后，每个常量都是class的一个唯一实例。Python提供了Enum类来实现这个功能：


```python
from enum import Enum
Month = Enum("Month",("Jan", "Feb", "Mar", "Apr", "May","Jun","Jul","Aug","Sep","Oct","Nov","Dec"))
# 这样我们就获得了Month类型的枚举类，可以直接使用Month.Jan来引用一个常量，或者枚举它的所有成员：
for name, member in Month.__members__.items():
    print(name,"=>",member,",", member.value)
# value属性则是自动赋给成员的int常量，默认从1开始计数。
```

    Jan => Month.Jan , 1
    Feb => Month.Feb , 2
    Mar => Month.Mar , 3
    Apr => Month.Apr , 4
    May => Month.May , 5
    Jun => Month.Jun , 6
    Jul => Month.Jul , 7
    Aug => Month.Aug , 8
    Sep => Month.Sep , 9
    Oct => Month.Oct , 10
    Nov => Month.Nov , 11
    Dec => Month.Dec , 12



```python
# 如果需要更精确地控制枚举类型，可以从Enum派生出自定义类：
from enum import Enum,unique
@unique
class Weekday(Enum):
    Sun = 0
    Mon = 1
    Tue = 2
    Wen = 3
    Thu = 4
    Fri = 5
    Sat = 6
day1 = Weekday.Mon
print(day1)
print(Weekday.Tue)
print(Weekday(1))
print(Weekday['Fri'])
for name, member in Weekday.__members__.items():
    print(name,'=>',member,',',member.value)
```

    Weekday.Mon
    Weekday.Tue
    Weekday.Mon
    Weekday.Fri
    Sun => Weekday.Sun , 0
    Mon => Weekday.Mon , 1
    Tue => Weekday.Tue , 2
    Wen => Weekday.Wen , 3
    Thu => Weekday.Thu , 4
    Fri => Weekday.Fri , 5
    Sat => Weekday.Sat , 6


### 使用元类
`type()`

 动态语言和静态语言最大的不同，就是函数和类的定义，不是编译时定义的，而是运行时动态创建的。
 比方说我们要定义一个`Hello`的class，就写一个`hello.py`模块：


```python
class Hello(object):
    def hello(self, name="world"):
        return ("Hello, %s"% name)
# 当Python解释器载入hello模块时，就会依次执行该模块的所有语句，执行结果就是动态创建出一个Hello的class对象，测试如下：
h = Hello()
print(h.hello("worldss"))
print(type(Hello))
print(type(h))
# type()函数可以查看一个类型或变量的类型，Hello是一个class，它的类型就是type，而h是一个实例，它的类型就是class Hello。
# 我们说class的定义是运行时动态创建的，而创建class的方法就是使用type()函数
# type()函数既可以返回一个对象的类型，又可以创建出新的类型，
# 比如，我们可以通过type()函数创建出Hello类，而无需通过class Hello(object)...的定义：
```

    Hello, worldss
    <class 'type'>
    <class '__main__.Hello'>



```python
def fn(self, name='world'): # 先定义函数
    print('Hello, %s.' % name)
Hello = type('Hello',(object,), dict(hello=fn))# 创建Hello class
h = Hello()
print(h.hello())
print(type(Hello))
print(type(h))
```

    Hello, world.
    None
    <class 'type'>
    <class '__main__.Hello'>


**总结**

要创建一个class对象，type()函数依次传入3个参数：
1. class的名称；
2. 继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；
3. class的方法名称与函数绑定，这里我们把函数fn绑定到方法名hello上。

通过`type()`函数创建的类和直接写class是完全一样的，因为Python解释器遇到`class`定义时，仅仅是扫描一下`class`定义的语法，然后调用`type()`函数创建出class。

正常情况下，我们都用class Xxx...来定义类，但是，`type()`函数也允许我们动态创建出类来，也就是说，动态语言本身支持运行期动态创建类，这和静态语言有非常大的不同，要在静态语言运行期创建类，必须构造源代码字符串再调用编译器，或者借助一些工具生成字节码实现，本质上都是动态编译，会非常复杂。

`metaclass`
除了使用`type()`动态创建类以外，要控制类的创建行为，还可以使用metaclass。

metaclass，直译为元类，简单的解释就是：

当我们定义了类以后，就可以根据这个类创建出实例，所以：**先定义类，然后创建实例**

但是如果我们想创建出类呢？那就必须根据metaclass创建出类，所以：先定义metaclass，然后创建类。

连接起来就是：**先定义metaclass，就可以创建类，最后创建实例**。

所以，metaclass允许你创建类或者修改类。换句话说，你可以把类看成是metaclass创建出来的“实例”。

metaclass是Python面向对象里最难理解，也是最难使用的魔术代码。正常情况下，你不会碰到需要使用metaclass的情况，所以，以下内容看不懂也没关系，因为基本上你不会用到。

我们先看一个简单的例子，这个metaclass可以给我们自定义的MyList增加一个add方法：

定义ListMetaclass，按照默认习惯，metaclass的类名总是以Metaclass结尾，以便清楚地表示这是一个metaclass：


```python
# metaclass是类的模板，所以必须从`type`类型派生：
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        attrs['add'] = lambda self, value: self.append(value)
        return type.__new__(cls, name, bases, attrs)
# 有了ListMetaclass，我们在定义类的时候还要指示使用ListMetaclass来定制类，传入关键字参数metaclass：
class MyList(list, metaclass=ListMetaclass):
    pass
```

分析：
当我们传入关键字参数metaclass时，魔术就生效了，它指示Python解释器在创建MyList时，要通过ListMetaclass.__new__()来创建，在此，我们可以修改类的定义，比如，加上新的方法，然后，返回修改后的定义。
`__new__()`方法接收到的参数依次是：

1. 当前准备创建的类的对象；
2. 类的名字；
3. 类继承的父类集合；
4. 类的方法集合。

测试一下MyList是否可以调用add()方法：


```python
L = MyList()
L.add(1)
print(L)
```

    [1]


动态修改有什么意义？直接在MyList定义中写上add()方法不是更简单吗？正常情况下，确实应该直接写，通过metaclass修改纯属变态。

但是，总会遇到需要通过metaclass修改类定义的。ORM就是一个典型的例子。

ORM全称“Object Relational Mapping”，即对象-关系映射，就是把关系数据库的一行映射为一个对象，也就是一个类对应一个表，这样，写代码更简单，不用直接操作SQL语句。

要编写一个ORM框架，所有的类都只能动态定义，因为只有使用者才能根据表的结构定义出对应的类来。

## 错误、调试和测试
BUG:
1. 程序编写有问题造成
2. 用户输入造成
3. 完全无法在程序运行过程中预测,如IO读写中断，网络抓取数据中断

**解决**
1. Python内置了一套异常处理机制，来帮助我们进行错误处理。
2. 此外，我们也需要跟踪程序的执行，查看变量的值是否正确，这个过程称为调试。Python的pdb可以让我们以单步方式执行代码。
3. 最后，编写测试也很重要。有了良好的测试，就可以在程序修改后反复运行，确保程序输出符合我们编写的测试。

### 错误处理
在程序运行的过程中，如果发生了错误，可以事先约定返回一个错误代码，这样，就可以知道是否有错，以及出错的原因。在操作系统提供的调用中，返回错误码非常常见。比如打开文件的函数`open()`，成功时返回文件描述符（就是一个整数），出错时返回`-1`。

用错误码来表示是否出错十分不便，因为函数本身应该返回的正常结果和错误码混在一起，造成调用者必须用大量的代码来判断是否出错：sub->parent。
1. 一旦出错，还要一级一级上报，直到某个函数可以处理该错误（比如，给用户输出一个错误信息）。
2. 所以高级语言通常都内置了一套`try...except...finally...`的错误处理机制，Python也不例外。


```python
# try的例子
try:
    print("try...")
    r = 10/0
    print("result %s"%r)
except ZeroDivisionError as e:
    print("except", e)
finally:
    print("finally")
print("end")

```

    try...
    except division by zero
    finally
    end


1. 当我们认为某些代码可能会出错时，就可以用try来运行这段代码，如果执行出错，则后续代码不会继续执行，
2. 而是直接跳转至错误处理代码，即except语句块，执行完except后，如果有finally语句块，则执行finally语句块，至此，执行完毕。
3. 从输出可以看到，当错误发生时，后续语句print('result:', r)不会被执行，except由于捕获到ZeroDivisionError，因此被执行。
4. 最后，finally语句被执行。然后，程序继续按照流程往下走。
5. 由于没有错误发生，所以except语句块不会被执行，但是finally如果有，则一定会被执行（可以没有finally语句）。

你还可以猜测，错误应该有很多种类，如果发生了不同类型的错误，应该由不同的except语句块处理。没错，可以有多个except来捕获不同类型的错误：


```python
try:
    print('try...')
#    r = 10 / int('a') 
    r = 10/int('2')
    print('result:', r)
except ValueError as e:
    print("ValueError:",e)
except ZeroDivisionError as e:
    print("ZeroDivisionError",e)
else:
    print("else no error")
finally:
    print("finally")
print("END")

#int()函数可能会抛出ValueError，所以我们用一个except捕获ValueError，用另一个except捕获ZeroDivisionError。
# 此外，如果没有错误发生，可以在except语句块后面加一个else，当没有错误发生时，会自动执行else语句.
```

    try...
    result: 5.0
    else no error
    finally
    END


另外：
1. Python的错误其实也是class，所有的错误类型都继承自`BaseException`，所以在使用except时需要注意的是，它不但捕获该类型的错误，还把其子类也“一网打尽”。
2. 所有的错误都是从`BaseException`派生出来的
3. 使用`try...except`捕获错误还有一个巨大的好处，就是可以跨越多层调用，比如函数`main()`调用`foo()` ，`foo()`调用`bar()` ，结果`bar()`出错了，这时，只要`main()`捕获到了，就可以处理：


```python
def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2
def main(): 
    try:
        bar('0')
    except Exception as e:
        print("Error:",e)
    finally:
        print("finally")
main()
```

    Error: division by zero
    finally



```python
# 调用栈
# 如果错误没有被捕获，它就会一直往上抛，最后被Python解释器捕获，打印一个错误信息，然后程序退出。来看看err.py
# err.py:
def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2

def main():
    bar('0')

main()
# 出错的时候，一定要分析错误的调用栈信息，才能定位错误的位置。
```


    ---------------------------------------------------------------------------

    ZeroDivisionError                         Traceback (most recent call last)

    <ipython-input-241-6643cba01984> in <module>()
         11     bar('0')
         12 
    ---> 13 main()
         14 # 出错的时候，一定要分析错误的调用栈信息，才能定位错误的位置。


    <ipython-input-241-6643cba01984> in main()
          9 
         10 def main():
    ---> 11     bar('0')
         12 
         13 main()


    <ipython-input-241-6643cba01984> in bar(s)
          6 
          7 def bar(s):
    ----> 8     return foo(s) * 2
          9 
         10 def main():


    <ipython-input-241-6643cba01984> in foo(s)
          3 # err.py:
          4 def foo(s):
    ----> 5     return 10 / int(s)
          6 
          7 def bar(s):


    ZeroDivisionError: division by zero



```python
# 记录错误
# 如果不捕获错误，自然可以让Python解释器来打印出错误堆栈，但程序也被结束了。
# 既然我们能捕获错误，就可以把错误堆栈打印出来，然后分析错误原因，同时，让程序继续执行下去。
# Python内置的logging模块可以非常容易地记录错误信息：
import logging
def foo(s):
    return 10 / int(s)
def bar(s):
    return foo(s) * 2
def main():
    try:
        bar('0')
    except Exception as e:
        logging.exception(e)
main()
print('END')
```

    ERROR:root:division by zero
    Traceback (most recent call last):
      File "<ipython-input-242-53643402f45a>", line 12, in main
        bar('0')
      File "<ipython-input-242-53643402f45a>", line 9, in bar
        return foo(s) * 2
      File "<ipython-input-242-53643402f45a>", line 7, in foo
        return 10 / int(s)
    ZeroDivisionError: division by zero


    END


通过配置，logging还可以把错误记录到日志文件里，方便事后排查。

**抛出错误**
1. 因为错误是class，捕获一个错误就是捕获到该class的一个实例。因此，错误并不是凭空产生的，而是有意创建并抛出的。
2. Python的内置函数会抛出很多类型的错误，我们自己编写的函数也可以抛出错误。
3. 如果要抛出错误，首先根据需要，可以定义一个错误的class，选择好继承关系，然后，用`raise`语句抛出一个错误的实例：


```python
class FooError(ValueError):
    pass

def foo(s):
    n = int(s)
    if n==0:
        raise FooError("invalid value: %s"% s)
    return 10/n
foo('0')
```


    ---------------------------------------------------------------------------

    FooError                                  Traceback (most recent call last)

    <ipython-input-243-fe4ac86bdc8f> in <module>()
         13         raise FooError("invalid value: %s"% s)
         14     return 10/n
    ---> 15 foo('0')
    

    <ipython-input-243-fe4ac86bdc8f> in foo(s)
         11     n = int(s)
         12     if n==0:
    ---> 13         raise FooError("invalid value: %s"% s)
         14     return 10/n
         15 foo('0')


    FooError: invalid value: 0


只有在必要的时候才定义我们自己的错误类型。如果可以选择Python已有的内置的错误类型（比如ValueError，TypeError），尽量使用Python内置的错误类型。

最后，我们来看另一种错误处理的方式：


```python
def foo(s):
    n = int(s)
    if n==0:
        raise ValueError("invalid value %s"%n)
    return 10/n

def bar(s):
    try:
        foo(s)
    except ValueError as e:
        print('ValueError!')
        raise
bar('0')
```

    ValueError!



    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-244-8a2347aaf190> in <module>()
         11         print('ValueError!')
         12         raise
    ---> 13 bar('0')
    

    <ipython-input-244-8a2347aaf190> in bar(s)
          7 def bar(s):
          8     try:
    ----> 9         foo(s)
         10     except ValueError as e:
         11         print('ValueError!')


    <ipython-input-244-8a2347aaf190> in foo(s)
          2     n = int(s)
          3     if n==0:
    ----> 4         raise ValueError("invalid value %s"%n)
          5     return 10/n
          6 


    ValueError: invalid value 0


在`bar()`函数中，我们明明已经捕获了错误，但是，打印一个`ValueError!`后，又把错误通过`raise`语句抛出去了。

其实这种错误处理方式相当常见。捕获错误目的只是记录一下，便于后续追踪。但是，由于当前函数不知道应该怎么处理该错误，所以，最恰当的方式是继续往上抛，让顶层调用者去处理。好比一个员工处理不了一个问题时，就把问题抛给他的老板，如果他的老板也处理不了，就一直往上抛，最终会抛给CEO去处理。

`raise`语句如果不带参数，就会把当前错误原样抛出。此外，在`except`中`raise`一个`Error`，还可以把一种类型的错误转化成另一种类型：


```python
try:
    10 / 0
except ZeroDivisionError:
    raise ValueError('input error!')
```


    ---------------------------------------------------------------------------

    ZeroDivisionError                         Traceback (most recent call last)

    <ipython-input-245-80c7eb55a8db> in <module>()
          1 try:
    ----> 2     10 / 0
          3 except ZeroDivisionError:


    ZeroDivisionError: division by zero

    
    During handling of the above exception, another exception occurred:


    ValueError                                Traceback (most recent call last)

    <ipython-input-245-80c7eb55a8db> in <module>()
          2     10 / 0
          3 except ZeroDivisionError:
    ----> 4     raise ValueError('input error!')
    

    ValueError: input error!


### 调试
程序能一次写完并正常运行的概率很小，基本不超过`1%`。总会有各种各样的bug需要修正。有的bug很简单，看看错误信息就知道，有的bug很复杂，我们需要知道出错时，哪些变量的值是正确的，哪些变量的值是错误的，因此，需要一整套调试程序的手段来修复bug。

第一种方法简单直接粗暴有效，就是用`print()`把可能有问题的变量打印出来看看;第二种断言


```python
def foo(s):
    n = int(s)
#     print("...n=%s"%n)
    assert n!=0, "n is zero" #assert的意思是，表达式n != 0应该是True，否则，根据程序运行的逻辑，后面的代码肯定会出错。
    return 10/n
def bar():
    return foo('0')
bar()
# 用print()最大的坏处是将来还得删掉它，想想程序里到处都是print()，运行结果也会包含很多垃圾信息。所以，我们又有第二种方法。
# 断言
# 凡是用print()来辅助查看的地方，都可以用断言（assert）来替代：
# 程序中如果到处充斥着assert，和print()相比也好不到哪去。不过，启动Python解释器时可以用-O参数来关闭assert：python -O err.py
# 关闭后，你可以把所有的assert语句当成pass来看。
```




    10.0



**logging**

1. 把`print()`替换为`logging`是第3种方式，和`assert`比，`logging`不会抛出错误，而且可以输出到文件;
2. `logging.info()`就可以输出一段文本。运行，发现除了ZeroDivisionError，没有任何信息。


```python
import logging
logging.basicConfig(level=logging.INFO)# 在import logging之后添加一行配置再试试：
s = '1'
n = int(s)
logging.info("n=%d"%n)
print(10/n)
```

    10.0


这就是`logging`的好处，它允许你指定记录信息的级别，有`debug，info，warning，error`等几个级别，当我们指定`level=INFO`时，`logging.debug`就不起作用了。同理，指定`level=WARNING`后，`debug`和`info`就不起作用了。这样一来，你可以放心地输出不同级别的信息，也不用删除，最后统一控制输出哪个级别的信息。

`logging`的另一个好处是通过简单的配置，一条语句可以同时输出到不同的地方，比如`console`和文件。

**pdb**

第4种方式是启动Python的调试器pdb，让程序以单步方式运行，可以随时查看运行状态。我们先准备好程序：

```
s = '0'
n = int(s)
print(10 / n)
# 然后启动
$ python -m pdb err.py
> /Users/michael/Github/learn-python3/samples/debug/err.py(2)<module>()
-> s = '0'
```

1. 以参数`-m pdb`启动后，pdb定位到下一步要执行的代码`-> s = '0'` 。输入命令l来查看代码:
```
(Pdb) l
  1     # err.py
  2  -> s = '0'
  3     n = int(s)
  4     print(10 / n)
```
2. 输入命令n可以单步执行代码：

```
(Pdb) n
> /Users/michael/Github/learn-python3/samples/debug/err.py(3)<module>()
-> n = int(s)
(Pdb) n
> /Users/michael/Github/learn-python3/samples/debug/err.py(4)<module>()
-> print(10 / n)
```
3. 任何时候都可以输入命令`p 变量名`来查看变量：
```
(Pdb) p s
'0'
(Pdb) p n
0
```
4. 输入命令q结束调试，退出程序：
`(Pdb) q`

**pdb.set_trace()**
1. 这个方法也是用pdb，但是不需要单步执行，我们只需要`import pdb`，然后，在可能出错的地方放一个`pdb.set_trace()`，就可以设置一个断点：

```
# err.py
import pdb

s = '0'
n = int(s)
pdb.set_trace() # 运行到这里会自动暂停
print(10 / n)
```
2. 运行代码，程序会自动在`pdb.set_trace()`暂停并进入pdb调试环境，可以用命令`p`查看变量，或者用命令`c`继续运行：

```
$ python err.py 
> /Users/michael/Github/learn-python3/samples/debug/err.py(7)<module>()
-> print(10 / n)
(Pdb) p n
0
(Pdb) c
Traceback (most recent call last):
  File "err.py", line 7, in <module>
    print(10 / n)
ZeroDivisionError: division by zero
```

### 单元测试
“测试驱动开发”（TDD：Test-Driven Development），单元测试就不陌生。

单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。
比如对函数abs()，我们可以编写出以下几个测试用例：
- 输入正数，比如1、1.2、0.99，期待返回值与输入相同；
- 输入负数，比如-1、-1.2、-0.99，期待返回值与输入相反；
- 输入0，期待返回0；
- 输入非数值类型，比如None、[]、{}，期待抛出TypeError。

把上面的测试用例放到一个测试模块里，就是一个完整的单元测试。

如果单元测试通过，说明我们测试的这个函数能够正常工作。如果单元测试不通过，要么函数有bug，要么测试条件输入不正确，总之，需要修复使单元测试能够通过。

**小结**

单元测试可以有效地测试某个程序模块的行为，是未来重构代码的信心保证。

单元测试的测试用例要覆盖常用的输入组合、边界条件和异常。

单元测试代码要非常简单，如果测试代码太复杂，那么测试代码本身就可能有bug。

单元测试通过了并不意味着程序就没有bug了，但是不通过程序肯定有bug。

### 文档测试
经常阅读Python的官方文档，可以看到很多文档都有示例代码。
可以把这些示例代码在Python的交互式环境下输入并执行，结果与文档中的示例代码显示的一致。

这些代码与其他说明可以写在注释中，然后，由一些工具来自动生成文档。既然这些代码本身就可以粘贴出来直接运行，那么，可不可以自动执行写在注释中的这些代码呢？

答案是肯定的。

当我们编写注释时，如果写上这样的注释：


```python
def abs(n):
    '''
    Function to get absolute value of number.

    Example:

    >>> abs(1)
    1
    >>> abs(-1)
    1
    >>> abs(0)
    0
    '''
    return n if n >= 0 else (-n)
```

无疑更明确地告诉函数的调用者该函数的期望输入和输出。

并且，Python内置的“文档测试”（doctest）模块可以直接提取注释中的代码并执行测试。

doctest严格按照Python交互式命令行的输入和输出来判断测试结果是否正确。只有测试异常的时候，可以用...表示中间一大段烦人的输出。

## IO编程
1. IO在计算机中指Input/Output，也就是输入和输出。由于程序和运行时数据是在内存中驻留，由CPU这个超快的计算核心来执行，涉及到数据交换的地方，通常是磁盘、网络等，就需要IO接口。
2. IO编程中，Stream（流）是一个很重要的概念，可以把流想象成一个水管，数据就是水管里的水，但是只能单向流动。Input Stream就是数据从外面（磁盘、网络）流进内存，Output Stream就是数据从内存流到外面去。对于浏览网页来说，浏览器和新浪服务器之间至少需要建立两根水管，才可以既能发数据，又能收数据。
3. 存在速度严重不匹配：同步和异步的区别就在于是否等待IO执行的结果。
### 文件读写
1. 读写文件是最常见的IO操作。Python内置了读写文件的函数，用法和C是兼容的。
2. 读写文件前，我们先必须了解一下，在磁盘上读写文件的功能都是由操作系统提供的，现代操作系统不允许普通的程序直接操作磁盘，所以，读写文件就是请求操作系统打开一个文件对象（通常称为文件描述符），然后，通过操作系统提供的接口从这个文件对象中读取数据（读文件），或者把数据写入这个文件对象（写文件）。

**读文件**

1. 要以读文件的模式打开一个文件对象，
    - 使用Python内置的`open()`函数，传入文件名和标示符：`f = open('/Users/michael/test.txt', 'r')`
    - 标示符'r'表示读，这样，我们就成功地打开了一个文件。
    - 如果文件不存在，open()函数就会抛出一个IOError的错误，并且给出错误码和详细的信息告诉你文件不存在
2. 如果文件打开成功，接下来，调用`read()`方法可以一次读取文件的全部内容，Python把内容读到内存，用一个`str`对象表示：
    ```
    >>> f.read()
    'Hello, world!'
    ```
3. 最后一步是调用`close()`方法关闭文件。文件使用完毕后必须关闭，因为文件对象会占用操作系统的资源，并且操作系统同一时间能打开的文件数量也是有限的：`>>> f.close()`
    - 由于文件读写时都有可能产生IOError，一旦出错，后面的f.close()就不会调用。
    - 所以，为了保证无论是否出错都能正确地关闭文件，我们可以使用`try ... finally`来实现：
    ```
    try:
        f = open('/path/to/file', 'r')
        print(f.read())
    finally:
        if f:
            f.close()
    ```
    - 改进：但是每次都这么写实在太繁琐，所以，Python引入了`with语句`来自动帮我们调用`close()`方法：
    ```
    with open('/path/to/file', 'r') as f:
    print(f.read())
    ```
    这和前面的try ... finally是一样的，但是代码更佳简洁，并且不必调用f.close()方法。
    - 调用`read()`会一次性读取文件的全部内容，如果文件有10G，内存就爆了，所以，要保险起见，可以反复调用`read(size)`方法，每次最多读取`size`个字节的内容。
    - 另外，调用`readline()`可以每次读取一行内容，调用`readlines()`一次读取所有内容并按行返回list。因此，要根据需要决定怎么调用。
    如果文件很小，read()一次性读取最方便；如果不能确定文件大小，反复调用read(size)比较保险；如果是配置文件，调用readlines()最方便：
    ```
    for line in f.readlines():
    print(line.strip()) # 把末尾的'\n'删掉
    ```

**file-like Object**

像`open()`函数返回的这种有个`read()`方法的对象，在Python中统称为`file-like Object`。除了file外，还可以是内存的字节流，网络流，自定义流等等。`file-like Object`不要求从特定类继承，只要写个read()方法就行。

`StringIO`就是在内存中创建的`file-like Object`，常用作临时缓冲。

二进制文件

前面讲的默认都是读取文本文件，并且是UTF-8编码的文本文件。要读取二进制文件，比如图片、视频等等，用`'rb'`模式打开文件即可：
```
>>> f = open('/Users/michael/test.jpg', 'rb')
>>> f.read()
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节
```

字符编码

要读取非UTF-8编码的文本文件，需要给`open()`函数传入`encoding`参数，例如，读取GBK编码的文件：
```
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')
>>> f.read()
'测试'
```

**写文件**

写文件和读文件是一样的，唯一区别是调用`open()`函数时，传入标识符`'w'`或者`'wb'`表示写文本文件或写二进制文件。
### StringIO和BytesIO
StringIO
- 很多时候，数据读写不一定是文件，也可以在内存中读写。
- StringIO顾名思义就是在内存中读写str。
- 要把str写入StringIO，我们需要先创建一个StringIO，然后，像文件一样写入即可
```
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())
hello world!
```
BytesIO
- StringIO操作的只能是str，如果要操作二进制数据，就需要使用BytesIO。
- BytesIO实现了在内存中读写bytes，我们创建一个BytesIO，然后写入一些bytes：
```
>>> from io import BytesIO
>>> f = BytesIO()
>>> f.write('中文'.encode('utf-8'))
6
>>> print(f.getvalue())
b'\xe4\xb8\xad\xe6\x96\x87'
```

### 操作文件和目录
如果我们要操作文件、目录，可以在命令行下面输入操作系统提供的各种命令来完成。比如dir、cp等命令。

如果要在Python程序中执行这些目录和文件的操作怎么办？其实操作系统提供的命令只是简单地调用了操作系统提供的接口函数，Python内置的os模块也可以直接调用操作系统提供的接口函数。

打开Python交互式命令行，我们来看看如何使用**os模块的基本功能**：
```
>>> import os
>>> os.name # 操作系统类型
'posix'
```
**环境变量**
在操作系统中定义的环境变量，全部保存在os.environ这个变量中，可以直接查看:
```
>>> os.environ
environ({'VERSIONER_PYTHON_PREFER_32_BIT': 'no', 'TERM_PROGRAM_VERSION': '326', 'LOGNAME': 'michael', 'USER': 'michael', 'PATH': '/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/opt/X11/bin:/usr/local/mysql/bin', ...})
```

操作文件和目录的函数一部分放在os模块中，一部分放在os.path模块中，这一点要注意一下。查看、创建和删除目录可以这么调用：
```
# 查看当前目录的绝对路径:
>>> os.path.abspath('.')
'/Users/michael'
# 在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:
>>> os.path.join('/Users/michael', 'testdir')
'/Users/michael/testdir'
# 然后创建一个目录:
>>> os.mkdir('/Users/michael/testdir')
# 删掉一个目录:
>>> os.rmdir('/Users/michael/testdir')
```

### 序列化
在程序运行的过程中，所有的变量都是在内存中,比如，定义一个dict：
`d = dict(name='Bob', age=20, score=88)`
可以随时修改变量，比如把`name`改成`'Bill'`，但是一旦程序结束，变量所占用的内存就被操作系统全部回收。如果没有把修改后的`'Bill'`存储到磁盘上，下次重新运行程序，变量又被初始化为`'Bob'`。

我们把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫`pickling`，在其他语言中也被称之为`serialization，marshalling，flattening`等等，都是一个意思。
序列化之后，就可以把序列化后的内容写入磁盘，或者通过网络传输到别的机器上。

反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化，即`unpickling`。

Python提供了`pickle模块`来实现序列化。

首先，我们尝试把一个对象序列化并写入文件：


```python
import pickle
d = dict(name='Bob', age=20, score=88)
pickle.dumps(d)
```




    b'\x80\x03}q\x00(X\x04\x00\x00\x00nameq\x01X\x03\x00\x00\x00Bobq\x02X\x03\x00\x00\x00ageq\x03K\x14X\x05\x00\x00\x00scoreq\x04KXu.'



`pickle.dumps()`方法把任意对象序列化成一个`bytes`，然后，就可以把这个`bytes`写入文件。或者用另一个方法`pickle.dump()`直接把对象序列化后写入一个`file-like Object`：
```
>>> f = open('dump.txt', 'wb')
>>> pickle.dump(d, f)
>>> f.close()
```
看看写入的`dump.txt`文件，一堆乱七八糟的内容，这些都是`Python`保存的对象内部信息。
当我们要把对象从磁盘读到内存时，可以先把内容读到一个bytes，然后用`pickle.loads()方`法反序列化出对象，也可以直接用`pickle.load()`方法从一个`file-like Object`中直接反序列化出对象。我们打开另一个Python命令行来反序列化刚才保存的对象：
```
>>> f = open('dump.txt', 'rb')
>>> d = pickle.load(f)
>>> f.close()
>>> d
{'age': 20, 'score': 88, 'name': 'Bob'}
```

**JSON**
如果我们要在不同的编程语言之间传递对象，就必须把对象序列化为标准格式，比如XML，但更好的方法是序列化为JSON，因为JSON表示出来就是一个字符串，可以被所有语言读取，也可以方便地存储到磁盘或者通过网络传输。JSON不仅是标准格式，并且比XML更快，而且可以直接在Web页面中读取，非常方便。JSON标准规定JSON编码是UTF-8

JSON表示的对象就是标准的`JavaScript`语言的对象。
Python内置的json模块提供了非常完善的Python对象到JSON格式的转换。我们先看看如何把Python对象变成一个JSON：


```python
import json
d = dict(name="bob", age=19, score=100)
json.dumps(d)
```




    '{"name": "bob", "age": 19, "score": 100}'



`dumps()`方法返回一个str，内容就是标准的JSON。类似的，`dump()`方法可以直接把JSON写入一个`file-like Object`。

要把JSON反序列化为Python对象，用`loads()`或者对应的`load()`方法，前者把JSON的字符串反序列化，后者从`file-like Object`中读取字符串并反序列化：


```python
json_str = '{"age": 20, "score": 88, "name": "Bob"}'
json.loads(json_str)
```




    {'age': 20, 'score': 88, 'name': 'Bob'}



**JSON进阶**
Python的`dict`对象可以直接序列化为JSON的`{}`，不过，很多时候，我们更喜欢用class表示对象，比如定义Student类，然后序列化：


```python
import json
class Student(object):
    def __init__(self, name, age, score):
        self.name = name
        self.age = age
        self.score = score
s = Student('Bob', 18, 100)
print(json.dumps(s))
```

    {"name": "Bob", "age": 18, "score": 100}


1. 错误的原因是Student对象不是一个可序列化为JSON的对象。
2. 看`dumps()`方法的参数列表，可以发现，除了第一个必须的obj参数外，`dumps()`方法还提供了一大堆的可选参数：
3. 这些可选参数就是让我们来定制JSON序列化。前面的代码之所以无法把Student类实例序列化为JSON，是因为默认情况下，`dumps()`方法不知道如何将Student实例变为一个JSON的`{}`对象。
4. 可选参数default就是把任意一个对象变成一个可序列为JSON的对象，我们只需要为Student专门写一个转换函数，再把函数传进去即可：
5. 这样，Student实例首先被student2dict()函数转换成dict，然后再被顺利序列化为JSON：


```python
def student2dict(std):
    return {
        'name': std.name,
        'age': std.age,
        'score': std.score
    }
print(json.dumps(s, default=student2dict))
```

    {"name": "Bob", "age": 18, "score": 100}



```python
# 不过，下次如果遇到一个Teacher类的实例，照样无法序列化为JSON。我们可以偷个懒，把任意class的实例变为dict：
print(json.dumps(s, default=lambda obj: obj.__dict__))
# 因为通常class的实例都有一个__dict__属性，它就是一个dict，用来存储实例变量。也有少数例外，比如定义了__slots__的class。
#同样的道理，如果我们要把JSON反序列化为一个Student对象实例，loads()方法首先转换出一个dict对象，
# 然后，我们传入的object_hook函数负责把dict转换为Student实例：
```

    {"name": "Bob", "age": 18, "score": 100}



```python
def dict2student(d):
    return Student(d['name'], d['age'], d['score'])
json_str = '{"age": 20, "score": 88, "name": "Bob"}'
print(json.loads(json_str, object_hook=dict2student))
```

    <__main__.Student object at 0x7fdad92e2a20>


小结
Python语言特定的序列化模块是pickle，但如果要把序列化搞得更通用、更符合Web标准，就可以使用json模块。

json模块的`dumps()`和`loads()`函数是定义得非常好的接口的典范。当我们使用时，只需要传入一个必须的参数。但是，当默认的序列化或反序列机制不满足我们的要求时，我们又可以传入更多的参数来定制序列化或反序列化的规则，既做到了接口简单易用，又做到了充分的扩展性和灵活性

## 进程和线程
总结一下就是，多任务的实现有3种方式：
- 多进程模式；
- 多线程模式；
- 多进程+多线程模式。

同时执行多个任务通常各个任务之间并不是没有关联的，而是需要相互通信和协调，有时，任务1必须暂停等待任务2完成后才能继续执行，有时，任务3和任务4又不能同时执行，所以，多进程和多线程的程序的复杂度要远远高于我们前面写的单进程单线程的程序。
Python既支持多进程，又支持多线程，我们会讨论如何编写这两种多任务程序。

**小结**

线程是最小的执行单元，而进程由至少一个线程组成。如何调度进程和线程，完全由操作系统决定，程序自己不能决定什么时候执行，执行多长时间。

多进程和多线程的程序涉及到同步、数据共享的问题，编写起来更复杂。

## 正则表达式
- 字符串是编程时涉及到的最多的一种数据结构，对字符串进行操作的需求几乎无处不在。比如判断一个字符串是否是合法的Email地址，虽然可以编程提取@前后的子串，再分别判断是否是单词和域名，但这样做不但麻烦，而且代码难以复用。
- 正则表达式是一种用来匹配字符串的强有力的武器。它的设计思想是用一种描述性的语言来给字符串定义一个规则，凡是符合规则的字符串，我们就认为它“匹配”了，否则，该字符串就是不合法的。
- 所以我们判断一个字符串是否是合法的Email的方法是：
    1. 创建一个匹配Email的正则表达式；
    2. 用该正则表达式去匹配用户的输入来判断是否合法。

因为正则表达式也是用字符串表示的，所以，我们要首先了解如何用字符来描述字符。
1. 在正则表达式中，如果直接给出字符，就是精确匹配。用`\d`可以匹配一个数字，`\w`可以匹配一个字母或数字，所以：
    - `'00\d'`可以匹配`'007`'，但无法匹配`'00A'`；
    - `'\d\d\d'`可以匹配`'010'`；
    - `'\w\w\d'`可以匹配`'py3'`；
2. `.`可以匹配任意字符，所以：
    `'py.'`可以匹配`'pyc'` 、 `'pyo'` 、`'py!'`等等。
3. 要匹配变长的字符，在正则表达式中，用`*`表示任意个字符（包括0个），用`+`表示至少一个字符，用`?`表示0个或1个字符，用`{n}`表示n个字符，用`{n,m}`表示`n-m`个字符：

来看一个复杂的例子：`\d{3}\s+\d{3,8}`。
我们来从左到右解读一下：
1. `\d{3}`表示匹配3个数字，例如`'010'`；
2. `\s`可以匹配一个空格（也包括Tab等空白符），所以`\s+`表示至少有一个空格，例如匹配`' '`，`' '`等；
3. `\d{3,8}`表示`3-8`个数字，例如`'1234567'`
综合起来，上面的正则表达式可以匹配以任意个空格隔开的带区号的电话号码。

如果要匹配`'010-12345'`这样的号码呢？由于`'-'`是特殊字符，在正则表达式中，要用`'\'`转义，所以，上面的正则是`\d{3}\-\d{3,8}`。

但是，仍然无法匹配`'010 - 12345'`，因为带有空格。所以我们需要更复杂的匹配方式。

**进阶**

要做更精确地匹配，可以用`[]`表示范围，比如：
    - `[0-9a-zA-Z\_]`可以匹配一个数字、字母或者下划线；
    - `[0-9a-zA-Z\_]+`可以匹配至少由一个数字、字母或者下划线组成的字符串，比如`'a100'，'0_Z'，'Py3000'`等等；
    - `[a-zA-Z\_][0-9a-zA-Z\_]*`可以匹配由字母或下划线开头，后接任意个由一个数字、字母或者下划线组成的字符串，也就是Python合法的变量；
    - `[a-zA-Z\_][0-9a-zA-Z\_]{0, 19}`更精确地限制了变量的长度是1-20个字符（前面1个字符+后面最多19个字符）。

1. `A|B`可以匹配A或B，所以`(P|p)ython`可以匹配'Python'或者'python'。
2. `^`表示行的开头，`^\d`表示必须以数字开头。
3. `$`表示行的结束，`\d$`表示必须以数字结束。
4. 你可能注意到了，`py`也可以匹配`'python'`，但是加上`^py$`就变成了整行匹配，就只能匹配`'py'`了。

**re模块**

Python提供re模块，包含所有正则表达式的功能。由于Python的字符串本身也用`\`转义，所以要特别注意：


```python
s = 'ABC\\-001' # Python的字符串
# 对应的正则表达式字符串变成：
# 'ABC\-001'
# 因此我们强烈建议使用Python的r前缀，就不用考虑转义的问题了：
s = r'ABC\-001' # Python的字符串
# 对应的正则表达式字符串不变：
# 'ABC\-001'
```


```python
# 先看看如何判断正则表达式是否匹配：
import re
print(re.match(r'^\d{3}\-\d{3,8}$', '010-12345'))
print(re.match(r'^\d{3}\-\d{3, 8}$', '010 12345'))
# match()方法判断是否匹配，如果匹配成功，返回一个Match对象，否则返回None。常见的判断方法就是：
test = '用户输入的字符串'
if re.match(r'正则表达式', test):
    print('ok')
else:
    print('failed')
```

    <_sre.SRE_Match object; span=(0, 9), match='010-12345'>
    None
    failed


**切分字符串**
用正则表达式切分字符串比用固定的字符更灵活


```python
import re
print('a b   c'.split(' '))
print(re.split(r'\s+', 'a b  c'))
print(re.split(r'[\s\,]+', 'a,b, c  d'))
print(re.split(r'[\s\,\;]+', 'a,b;; c  d'))
```

    ['a', 'b', '', '', 'c']
    ['a', 'b', 'c']
    ['a', 'b', 'c', 'd']
    ['a', 'b', 'c', 'd']


分组
除了简单地判断是否匹配之外，正则表达式还有提取子串的强大功能。用`()`表示的就是要提取的分组（Group）。比如：

`^(\d{3})-(\d{3,8})$`分别定义了两个组，可以直接从匹配的字符串中提取出区号和本地号码：


```python
m = re.match(r'^(\d{3})-(\d{3,8})$', '010-12345')
print(m)
print(m.group(0))
print(m.group(1))
print(m.group(2))
# 如果正则表达式中定义了组，就可以在Match对象上用group()方法提取出子串来。
# 注意到group(0)永远是原始字符串，group(1)、group(2)……表示第1、2、……个子串。
# 提取子串非常有用。
```

    <_sre.SRE_Match object; span=(0, 9), match='010-12345'>
    010-12345
    010
    12345


贪婪匹配

最后需要特别指出的是，正则匹配默认是贪婪匹配，也就是匹配尽可能多的字符。举例如下，匹配出数字后面的`0`：


```python
re.match(r'^(\d+)(0*)$', '102300').group()
# 由于\d+采用贪婪匹配，直接把后面的0全部匹配了，结果0*只能匹配空字符串了。
# 必须让\d+采用非贪婪匹配（也就是尽可能少匹配），才能把后面的0匹配出来，加个?就可以让\d+采用非贪婪匹配：
re.match(r'^(\d+?)(0*)$', '102300').groups()
```




    ('1023', '00')



编译
- 当我们在Python中使用正则表达式时，re模块内部会干两件事情：
- 编译正则表达式，如果正则表达式的字符串本身不合法，会报错；
- 用编译后的正则表达式去匹配字符串。
- 如果一个正则表达式要重复使用几千次，出于效率的考虑，我们可以预编译该正则表达式，接下来重复使用时就不需要编译这个步骤了，直接匹配：


```python
import re
# 编译:
re_telephone = re.compile(r'^(\d{3})-(\d{3,8})$')
# 使用：
print(re_telephone.match('010-12345').groups())
print(re_telephone.match('010-8086').groups())
# 编译后生成Regular Expression对象，由于该对象自己包含了正则表达式，所以调用对应的方法时不用给出正则字符串。
```

    ('010', '12345')
    ('010', '8086')


## 常用内建模块
Python之所以自称`“batteries included”`，就是因为内置了许多非常有用的模块，无需额外安装和配置，即可直接使用。
### datetime
datetime是Python处理日期和时间的标准库。
#### 获取当前日期和时间

我们先看如何获取当前日期和时间：


```python
from datetime import datetime
now = datetime.now()
print(now)
print(type(now))
```

    2018-05-08 12:22:23.845776
    <class 'datetime.datetime'>


注意到`datetime`是模块，`datetime`模块还包含一个`datetime`类，通过`from datetime import datetime`导入的才是`datetime`这个类。

如果仅导入`import datetime`，则必须引用全名`datetime.datetime`。

`datetime.now()`返回当前日期和时间，其类型是`datetime`。

#### 获取指定日期和时间
要指定某个日期和时间，我们直接用参数构造一个datetime：


```python
from datetime import datetime
dt = datetime(2018, 4, 10, 12,30)
print(dt)
```

    2018-04-10 12:30:00


#### datetime转换为timestamp
在计算机中，时间实际上是用数字表示的。我们把`1970年1月1日 00:00:00 UTC+00:00`时区的时刻称为`epoch time`，记为0（1970年以前的时间timestamp为负数），当前时间就是相对于`epoch time的秒数`，称为`timestamp`。即`timestamp = 0 = 1970-1-1 00:00:00 UTC+0:00`
对应的北京时间是：`timestamp = 0 = 1970-1-1 08:00:00 UTC+8:00`

把一个`datetime`类型转换为`timestamp`只需要简单调用`timestamp()`方法：


```python
from datetime import datetime
dt = datetime(2018, 4, 19, 12, 20) # 用指定日期时间创建datetime
dt.timestamp() # 把datetime转换为timestamp
```




    1524140400.0



注意Python的timestamp是一个浮点数。如果有小数位，小数位表示毫秒数。

某些编程语言（如Java和JavaScript）的timestamp使用整数表示毫秒数，这种情况下只需要把timestamp除以1000就得到Python的浮点表示方法。
#### timestamp转换为datetime
要把timestamp转换为datetime，使用datetime提供的`fromtimestamp()`方法：


```python
from datetime import datetime
t = 1524140400.0
print(datetime.fromtimestamp(t))
```

    2018-04-19 12:20:00


注意到timestamp是一个浮点数，它没有时区的概念，而datetime是有时区的。上述转换是在timestamp和本地时间做转换。

本地时间是指当前操作系统设定的时区。例如北京时区是东8区，则本地时间实际上是实际上就是UTC+8:00时区的时间：timestamp也可以直接被转换到UTC标准时区的时间：


```python
from datetime import datetime
t = 1524140400.0
print(datetime.fromtimestamp(t))# 本地
print(datetime.utcfromtimestamp(t))# UTC
```

    2018-04-19 12:20:00
    2018-04-19 12:20:00


#### str转换为datetime
很多时候，用户输入的日期和时间是字符串，要处理日期和时间，首先必须把str转换为datetime。转换方法是通过`datetime.strptime()`实现，需要一个日期和时间的格式化字符串：


```python
from datetime import datetime
cday = datetime.strptime('2018-2-2 19:01:03', '%Y-%m-%d %H:%M:%S')
print(cday)
```

    2018-02-02 19:01:03


#### datetime转换为str
如果已经有了datetime对象，要把它格式化为字符串显示给用户，就需要转换为str，转换方法是通过`strftime()`实现的，同样需要一个日期和时间的格式化字符串：


```python
from datetime import datetime
now = datetime.now()
print(now.strftime("%a, %b %d %H:%M"))
```

    Tue, May 08 12:46


#### 本地时间转换为UTC时间
#### 时区转换

## 常用第三方模块
除了内建的模块外，Python还有大量的第三方模块。

基本上，所有的第三方模块都会在`PyPI - the Python Package Index`上注册，只要找到对应的模块名字，即可用pip安装。

此外，在安装第三方模块一节中，我们强烈推荐安装Anaconda，安装后，数十个常用的第三方模块就已经就绪，不用pip手动安装。

本章介绍常用的第三方模块。

### Pillow
`PIL：Python Imaging Library`，已经是Python平台事实上的图像处理标准库了。PIL功能非常强大，但API却非常简单易用。

由于PIL仅支持到Python 2.7，加上年久失修，于是一群志愿者在PIL的基础上创建了兼容的版本，名字叫`Pillow`，支持最新Python 3.x，又加入了许多新特性，因此，我们可以直接安装使用Pillow。

#### 安装Pillow
如果安装了Anaconda，Pillow就已经可用了。否则，需要在命令行下通过pip安装：`pip3 install pillow`
#### 操作图像
来看看最常见的图像缩放操作，只需三四行代码：


```python
from PIL import Image
# 打开一个jpg图像文件，注意是当前路径:
im = Image.open('test.jpg')
# 获得图像尺寸:
w, h = im.size
print('Original image size: %sx%s' % (w, h))
# 缩放到50%:
im.thumbnail((w//2, h//2))
print('Resize image to: %sx%s' % (w//2, h//2))
# 把缩放后的图像用jpeg格式保存:
im.save('thumbnail.jpg', 'jpeg')
```


```python
# 其他功能如切片、旋转、滤镜、输出文字、调色板等一应俱全。
# 比如，模糊效果也只需几行代码：
from PIL import Image, ImageFilter

# 打开一个jpg图像文件，注意是当前路径:
im = Image.open('test.jpg')
# 应用模糊滤镜:
im2 = im.filter(ImageFilter.BLUR)
im2.save('blur.jpg', 'jpeg')
```

### requests
- 我们已经讲解了Python内置的urllib模块，用于访问网络资源。但是，它用起来比较麻烦，而且，缺少很多实用的高级功能。
- 更好的方案是使用requests。它是一个Python第三方库，处理URL资源特别方便。
#### 安装requests
如果安装了Anaconda，requests就已经可用了。否则，需要在命令行下通过pip安装：`pip install requests`
#### 使用requests
要通过GET访问一个页面，只需要几行代码：
```
>>> import requests
>>> r = requests.get('https://www.douban.com/') # 豆瓣首页
>>> r.status_code
200
>>> r.text
r.text
'<!DOCTYPE HTML>\n<html>\n<head>\n<meta name="description" content="提供图书、电影、音乐唱片的推荐、评论和...'
```
对于带参数的URL，传入一个dict作为params参数：
```
>>> r = requests.get('https://www.douban.com/search', params={'q': 'python', 'cat': '1001'})
>>> r.url # 实际请求的URL
'https://www.douban.com/search?q=python&cat=1001'
>>> r.encoding
'utf-8'
```
无论响应是文本还是二进制内容，我们都可以用content属性获得bytes对象：
```
>>> r.content
b'<!DOCTYPE html>\n<html>\n<head>\n<meta http-equiv="Content-Type" content="text/html; charset=utf-8">\n...'
```
requests的方便之处还在于，对于特定类型的响应，例如JSON，可以直接获取：
```
>>> r = requests.get('https://query.yahooapis.com/v1/public/yql?q=select%20*%20from%20weather.forecast%20where%20woeid%20%3D%202151330&format=json')
>>> r.json()
{'query': {'count': 1, 'created': '2017-11-17T07:14:12Z', ...
```
需要传入HTTP Header时，我们传入一个dict作为headers参数：
```
>>> r = requests.get('https://www.douban.com/', headers={'User-Agent': 'Mozilla/5.0 (iPhone; CPU iPhone OS 11_0 like Mac OS X) AppleWebKit'})
>>> r.text
'<!DOCTYPE html>\n<html>\n<head>\n<meta charset="UTF-8">\n <title>豆瓣(手机版)</title>...'
```
要发送POST请求，只需要把get()方法变成post()，然后传入data参数作为POST请求的数据：
```
>>> r = requests.post('https://accounts.douban.com/login', data={'form_email': 'abc@example.com', 'form_password': '123456'})
```
requests默认使用`application/x-www-form-urlencoded`对POST数据编码。如果要传递JSON数据，可以直接传入json参数：
```
params = {'key': 'value'}
r = requests.post(url, json=params) # 内部自动序列化为JSON
```
类似的，上传文件需要更复杂的编码格式，但是requests把它简化成files参数：
```
>>> upload_files = {'file': open('report.xls', 'rb')}
>>> r = requests.post(url, files=upload_files)
```
在读取文件时，注意务必使用'rb'即二进制模式读取，这样获取的bytes长度才是文件的长度。

把post()方法替换为put()，delete()等，就可以以PUT或DELETE方式请求资源。

除了能轻松获取响应内容外，requests对获取HTTP响应的其他信息也非常简单。例如，获取响应头：
```
>>> r.headers
{Content-Type': 'text/html; charset=utf-8', 'Transfer-Encoding': 'chunked', 'Content-Encoding': 'gzip', ...}
>>> r.headers['Content-Type']
'text/html; charset=utf-8'
```

### virtualenv
在开发Python应用程序的时候，系统安装的Python3只有一个版本：3.6。所有第三方的包都会被pip安装到Python3的`site-packages`目录下。

如果我们要同时开发多个应用程序，那这些应用程序都会共用一个Python，就是安装在系统的`Python 3`。如果应用A需要`jinja 2.7`，而应用B需要`jinja 2.6`怎么办？

这种情况下，每个应用可能需要各自拥有一套“独立”的Python运行环境。`virtualenv`就是用来为一个应用创建一套“隔离”的Python运行环境。

首先，我们用pip安装`virtualenv`： `pip3 install virtualenv`

然后，假定我们要开发一个新的项目，需要一套独立的Python运行环境，可以这么做：
1. 第一步，创建目录：
```
Mac:~ michael$ mkdir myproject
Mac:~ michael$ cd myproject/
Mac:myproject michael$
```
2. 第二步，创建一个独立的Python运行环境，命名为`venv`：
```
Mac:myproject michael$ virtualenv --no-site-packages venv
Using base prefix '/usr/local/.../Python.framework/Versions/3.4'
New python executable in venv/bin/python3.4
Also creating executable in venv/bin/python
Installing setuptools, pip, wheel...done.
```
3. 命令`virtualenv`就可以创建一个独立的Python运行环境，我们还加上了参数`--no-site-packages` ，这样，已经安装到系统Python环境中的所有第三方包都不会复制过来，这样，我们就得到了一个不带任何第三方包的“干净”的Python运行环境。
4. 新建的Python环境被放到当前目录下的venv目录。有了`venv`这个Python环境，可以用`source进入该环境`：
```
Mac:myproject michael$ source venv/bin/activate
(venv)Mac:myproject michael$
```
注意到命令提示符变了，有个`(venv)`前缀，表示当前环境是一个名为`venv`的Python环境。

5. 下面正常安装各种第三方包，并运行python命令：
```
(venv)Mac:myproject michael$ pip install jinja2
...
Successfully installed jinja2-2.7.3 markupsafe-0.23
(venv)Mac:myproject michael$ python myapp.py
...
```
在`venv`环境下，用pip安装的包都被安装到venv这个环境下，系统Python环境不受任何影响。也就是说，venv环境是专门针对myproject这个应用创建的。
6. 退出当前的venv环境，使用`deactivate`命令：
```
(venv)Mac:myproject michael$ deactivate 
Mac:myproject michael$
```
此时就回到了正常的环境，现在pip或python均是在系统Python环境下执行。

完全可以针对每个应用创建独立的Python运行环境，这样就可以对每个应用的Python环境进行隔离。

`virtualenv`是如何创建“独立”的Python运行环境的呢？原理很简单，就是把系统Python复制一份到virtualenv的环境，用命令`source venv/bin/activate`进入一个`virtualenv`环境时，`virtualenv`会修改相关环境变量，让命令python和pip均指向当前的virtualenv环境。

### 图形界面
Python支持多种图形界面的第三方库，包括：`Tk,wxWidgets,Qt,GTK`等等。

但是Python自带的库是支持Tk的`Tkinter`，使用Tkinter，无需安装任何包，就可以直接使用。本章简单介绍如何使用Tkinter进行GUI编程。
Tkinter
我们来梳理一下概念：

我们编写的Python代码会调用内置的Tkinter，Tkinter封装了访问Tk的接口；

Tk是一个图形库，支持多个操作系统，使用Tcl语言开发；

Tk会调用操作系统提供的本地GUI接口，完成最终的GUI。

所以，我们的代码只需要调用Tkinter提供的接口就可以了。

**第一个GUI程序**

使用Tkinter十分简单，我们来编写一个GUI版本的“Hello, world!”。

1. 第一步是导入Tkinter包的所有内容,
2. 第二步是从`Frame`派生一个`Application`类，这是所有Widget的父容器
    1. 在GUI中，每个`Button、Label、输入框`等，都是一个Widget。Frame则是可以容纳其他Widget的Widget，所有的Widget组合起来就是一棵树。
    2.`pack()`方法把Widget加入到父容器中，并实现布局。`pack()`是最简单的布局，`grid()`可以实现更复杂的布局。
    3.在`createWidgets()`方法中，我们创建一个Label和一个Button，当`Button`被点击时，触发`self.quit()`使程序退出。
3. 第三步，实例化Application，并启动消息循环,GUI程序的主线程负责监听来自操作系统的消息，并依次处理每一条消息。因此，如果消息处理非常耗时，就需要在新线程中处理。


```python
from tkinter import*
class Application(Frame):
    def __init__(self, master=None):
        Frame.__init__(self, master)
        self.pack()
        self.createWidgets()
    def createWidgtes(self):
        self.helloLabel = Label(self, text="Hello Label")
        self.helloLabel.pack()
        self.quitButton = Button(self, text="Quit", command=self.quit)
        self.quitButton.pack()
app=Application()
# 设置窗口标题:
app.master.title("Hello world")
# 主消息循环:
app.mainloop()
```

输入文本

我们再对这个GUI程序改进一下，加入一个文本框，让用户可以输入文本，然后点按钮后，弹出消息对话框。

当用户点击按钮时，触发`hello()`，通过`self.nameInput.get()`获得用户输入的文本后，使用`tkMessageBox.showinfo()`可以弹出消息对话框。


