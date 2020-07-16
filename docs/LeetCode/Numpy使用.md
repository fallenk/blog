---
title: Numpy使用
date: 2018-09-28 19:52:39
tags: [编程, 代码]
category: Python学习
---

# Numpy教程
Python中用于科学计算的核心库。`numpy（Numerical Python）`提供了python对**多维数组对象**的支持：`ndarray`，具有矢量运算能力，快速、节省空间。numpy支持高级大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。

<!--more-->
# 基础篇
NumPy的主要对象是同种元素的多维数组。这是一个所有的元素都是一种类型、通过一个正整数元组索引的元素表格(通常是元素是数字)。在NumPy中维度(dimensions)叫做轴(axes)，轴的个数叫做秩(rank)。
NumPy的数组类被称作 ndarray 。通常被称作数组。注意numpy.array和标准Python库类array.array并不相同，后者只处理一维数组和提供少量功能。更多重要ndarray对象属性有：

- ndarray.ndim
数组轴的个数，在python的世界中，轴的个数被称作秩
- ndarray.shape
数组的维度。这是一个指示数组在每个维度上大小的整数元组。例如一个n排m列的矩阵，它的shape属性将是(n,m),这个元组的长度显然是秩，即维度或者ndim属性
- ndarray.size
数组元素的总个数，等于shape属性中元组元素的乘积。
- ndarray.dtype
一个用来描述数组中元素类型的对象，可以通过创造或指定dtype使用标准Python类型。另外NumPy提供它自己的数据类型。
- ndarray.itemsize
数组中每个元素的字节大小。例如，一个元素类型为float64的数组itemsiz属性值为8(=64/8),又如，一个元素类型为complex32的数组item属性为4(=32/8).
- ndarray.data
包含实际数组元素的缓冲区，通常我们不需要使用这个属性，因为我们总是通过索引来使用数组中的元素。

## 一个例子：


```python
from numpy import *
a = arange(15).reshape(3, 5)
print(a)
print(type(a))
print('a.ndim',a.ndim)
print('a.shape', a.shape)
print('a.size', a.size)
print('a.dtype', a.dtype)
print('a.itemsize', a.itemsize)
print('a.data',a.data)
```

    [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]]
    <class 'numpy.ndarray'>
    a.ndim 2
    a.shape (3, 5)
    a.size 15
    a.dtype int64
    a.itemsize 8
    a.data <memory at 0x7f1a641e17e0>


## 创建数组
  
### 使用 array 函数
使用 array 函数从常规的Python列表和元组创造数组。所创建的数组类型由原序列中的元素类型推导而来。


```python
#from numpy  import * # 申明为*，不需要前缀
import numpy as np
a = np.array([[1, 2, 3, 4],[5, 6,7,8],[10, 2, 3,4]])
b = array([1.2, 3.5, 5.1])
print('a',a)
print('b',b)
# 一个常见的错误包括用多个数值参数调用array而不是提供一个由数值组成的列表作为一个参数。 c = array(1, 2, 3, 4) wrong
# 数组将序列包含序列转化成二维的数组，序列包含序列包含序列转化成三维数组等等。
c = array([(1, 2, 3, 4), (4, 5, 6, 7)])
print('c:',c)
# 数组类型可以在创建时显示指定
d = array([1, 2, 3, 4], dtype=complex)
print('d', d)
```

    a [[ 1  2  3  4]
     [ 5  6  7  8]
     [10  2  3  4]]
    b [ 1.2  3.5  5.1]
    c: [[1 2 3 4]
     [4 5 6 7]]
    d [ 1.+0.j  2.+0.j  3.+0.j  4.+0.j]


### 使用占位符创建
1. 通常，数组的元素开始都是未知的，但是它的大小已知。因此，NumPy提供了一些使用占位符创建数组的函数。这最小化了扩展数组的需要和高昂的运算代价。
2. 函数zero创建一个全是0的数组，函数ones创建一个全1的数组，函数empty创建一个内容随机并且依赖与内存状态的数组。默认创建的数组类型(dtype)都是float64。


```python
print(np.zeros((3, 4)))
print(np.ones((2, 4)))
print(np.empty((1, 2)))
```

    [[ 0.  0.  0.  0.]
     [ 0.  0.  0.  0.]
     [ 0.  0.  0.  0.]]
    [[ 1.  1.  1.  1.]
     [ 1.  1.  1.  1.]]
    [[  6.90466352e-310   6.90466352e-310]]


### 使用arange 函数
为了创建一个数列，NumPy提供一个类似arange的函数返回数组而不是列表:


```python
print(np.arange(10, 30, 5))
print(np.arange(0, 3, 0.3))
```

    [10 15 20 25]
    [ 0.   0.3  0.6  0.9  1.2  1.5  1.8  2.1  2.4  2.7]


### 使用 lineapce 函数
当`arange`使用浮点数参数时，由于有限的浮点数精度，通常无法预测获得的元素个数。因此，最好使用函数`linspace`去接收我们想要的元素个数来代替用`range`来指定步长。


```python
from numpy import pi
print(np.linspace(0, 2, 9)) # 9 numbers 0 to 2
x = np.linspace(0, 2*pi, 10)
f = np.sin(x)
print(f)
```

    [ 0.    0.25  0.5   0.75  1.    1.25  1.5   1.75  2.  ]
    [  0.00000000e+00   6.42787610e-01   9.84807753e-01   8.66025404e-01
       3.42020143e-01  -3.42020143e-01  -8.66025404e-01  -9.84807753e-01
      -6.42787610e-01  -2.44929360e-16]


其它函数array, zeros, zeros_like, ones, ones_like, empty, empty_like, arange, linspace, rand, randn, fromfunction, fromfile, 赋值拷贝。
## 打印数组
当你打印一个数组，NumPy以类似嵌套列表的形式显示它，但是呈以下布局：
- 最后的轴从左到右打印
- 次后的轴从顶向下打印
- 剩下的轴从顶向下打印，每个切片通过一个空行与下一个隔开

一维数组被打印成行，二维数组成矩阵，三维数组成矩阵列表。


```python
a = np.arange(6)# 1d array
print(a)
b = np.arange(12).reshape(4,3)# 2d array
print(b)
c = np.arange(24).reshape(2,3,4)# 3d array
print(c)
# 如果一个数组用来打印太大了，NumPy自动省略中间部分而只打印角落
print(np.arange(10000))
# 禁用NumPy的这种行为并强制打印整个数组，你可以设置printoptions参数来更改打印选项。
print(np.set_printoptions(threshold=np.nan))
```

    [0 1 2 3 4 5]
    [[ 0  1  2]
     [ 3  4  5]
     [ 6  7  8]
     [ 9 10 11]]
    [[[ 0  1  2  3]
      [ 4  5  6  7]
      [ 8  9 10 11]]
    
     [[12 13 14 15]
      [16 17 18 19]
      [20 21 22 23]]]
    [   0    1    2 ..., 9997 9998 9999]


## 基本运算
数组的算术运算是按元素的。新的数组被创建并且被结果填充。NumPy中的乘法运算符 * 指示按元素计算，矩阵乘法可以使用 dot 函数或创建矩阵对象实现。


```python
a = np.array([1, 2, 3, 4])
b = np.arange(4)
print('b',b)
c = a-b
print('c',c)
print('b**2',b**2)
print('10*np.sin(a)',10*np.sin(a))
print('a<35',a<35)
```

    b [0 1 2 3]
    c [1 1 1 1]
    b**2 [0 1 4 9]
    10*np.sin(a) [ 8.41470985  9.09297427  1.41120008 -7.56802495]
    a<35 [ True  True  True  True]



```python
# NumPy中的乘法运算符*指示按元素计算，矩阵乘法可以使用dot函数或创建矩阵对象实现
import numpy
print(numpy.arange(6))
A = np.array([[1, 1,],
           [0, 1]])
B = np.array([[2, 0],
           [3, 4]])
print('A*B',A*B)
print('A.dot(B)',A.dot(B))
print('np.dot(A, B)',np.dot(A, B))
```

    [0 1 2 3 4 5]
    A*B [[2 0]
     [0 4]]
    A.dot(B) [[5 4]
     [3 4]]
    np.dot(A, B) [[5 4]
     [3 4]]



```python
# 有些操作符像+=和*=被用来更改已存在数组而不创建一个新的数组。
a = np.ones((2, 3), dtype=int)
b = np.random.random((2, 3))
a *= 3
print(a)
b += a
print(b)
# print(a += b)
```

    [[3 3 3]
     [3 3 3]]
    [[ 3.77601215  3.67874255  3.09996677]
     [ 3.33283587  3.62279173  3.90198359]]



```python
# 当运算的是不同类型的数组时，结果数组靠近更普遍和精确的已知数组(这种行为叫做upcast)。
a=np.ones(3, dtype=np.int32)
print(a)
b = np.linspace(0,pi,3)
print(b.dtype.name)
c = a+b
print('c',c)
print('c dtype name',c.dtype.name)
d = np.exp(c*1j)
print(d)
print('d dtype name', d.dtype.name) # 许多非数组运算，如计算数组所有元素之和，被作为ndarray类的方法实现
e = np.random.random((2, 3))
print('e:',e)
print('e.sum:%s e.max:%s e.min:%s'%(e.sum(),e.max(),e.min()))
```

    [1 1 1]
    float64
    c [ 1.          2.57079633  4.14159265]
    c dtype name float64
    [ 0.54030231+0.84147098j -0.84147098+0.54030231j -0.54030231-0.84147098j]
    d dtype name complex128
    e: [[ 0.48800489  0.55796234  0.16459517]
     [ 0.40017365  0.41096132  0.01136693]]
    e.sum:2.03306428928 e.max:0.557962337045 e.min:0.0113669291358



```python
# 这些运算默认应用到数组好像它就是一个数字组成的列表，无关数组的形状。然而，指定axis参数你可以吧运算应用到数组指定的轴上：
b = np.arange(12).reshape(3, 4)
print('b',b)
print('b.sum(axis=0):',b.sum(axis=0)) # sum of each column
print('b.sum(axis=1):',b.sum(axis=1))# sum of each row
print('b.min(axis=0):',b.min(axis=0))
print('b.min(axis=1):',b.min(axis=1))
print('b.max(axis=0):',b.max(axis=0))
print('b.max(axis=1):',b.max(axis=1))
print('b.cumsum(axis=0):',b.cumsum(axis=0))
print('b.cumsum(axis=1):',b.cumsum(axis=1))
```

    b [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]]
    b.sum(axis=0): [12 15 18 21]
    b.sum(axis=1): [ 6 22 38]
    b.min(axis=0): [0 1 2 3]
    b.min(axis=1): [0 4 8]
    b.max(axis=0): [ 8  9 10 11]
    b.max(axis=1): [ 3  7 11]
    b.cumsum(axis=0): [[ 0  1  2  3]
     [ 4  6  8 10]
     [12 15 18 21]]
    b.cumsum(axis=1): [[ 0  1  3  6]
     [ 4  9 15 22]
     [ 8 17 27 38]]


## 通用函数(ufunc)
NumPy提供常见的数学函数如sin,cos和exp。在NumPy中，这些叫作“通用函数”(ufunc)。在NumPy里这些函数作用**按数组的元素**运算，产生一个数组作为输出。


```python
B = np.arange(3)
print(B)
print(np.exp(B))
print(np.sqrt(B))
C = np.array([2., -1., 4.])
d = np.add(B, C)
print(d)
```

    [0 1 2]
    [ 1.          2.71828183  7.3890561 ]
    [ 0.          1.          1.41421356]
    [ 2.  0.  6.]


更多函数`all, alltrue, any, apply along axis, argmax, argmin, argsort, average, bincount, ceil, clip, conj, conjugate, corrcoef, cov, cross, cumprod, cumsum, diff, dot, floor, inner, inv, lexsort, max, maximum, mean, median, min, minimum, nonzero, outer, prod, re, round, sometrue, sort, std, sum, trace, transpose, var, vdot, vectorize, where` 

## 索引，切片和迭代(Indexing, Slicing and Iterating)
一维 数组可以被索引、切片和迭代，就像 列表 和其它Python序列。
一维数组的索引：与Python的列表索引功能相似


```python
a = np.arange(10)**2
print(a)
print(a[2])
print(a[2:5])
a[:6:2]=-1000 # equivalent to a[0:6:2] = -1000; from start to position 6, exclusive, set every 2nd element to -1000
print(a)
print(a[::-1])#reverse a
for i in a:
    print(i**(1/3.))
```

    [ 0  1  4  9 16 25 36 49 64 81]
    4
    [ 4  9 16]
    [-1000     1 -1000     9 -1000    25    36    49    64    81]
    [   81    64    49    36    25 -1000     9 -1000     1 -1000]
    nan
    1.0
    nan
    2.08008382305
    nan
    2.92401773821
    3.30192724889
    3.65930571002
    4.0
    4.32674871092


    /opt/conda/lib/python3.6/site-packages/ipykernel_launcher.py:9: RuntimeWarning: invalid value encountered in power
      if __name__ == '__main__':


多维 数组可以每个轴有一个索引。这些索引由一个逗号分割的元组给出。(二维数组: 纵轴,横轴)
多维数组的索引：
```
arr[r1:r2, c1:c2]
arr[1,1] 等价 arr[1][1]
[:] 代表某个维度的数据
```


```python
def f(x, y):
    return 10*x+y
b = fromfunction(f, (5, 4), dtype=int32)
print(b)
print('b[2,3]:',b[2,3])
print('b[0:5, 1]:',b[0:5, 1])# each row in the second column of b
print('b[:, 1]:',b[:, 1])# equivalent to the previous example
print('b[1:3, : ]:',b[1:3, :])# each column in the second and third row of b
print('b[-1]:',b[-1])# 当少于轴数的索引被提供时，确失的索引被认为是整个切片：b[-1,:]
```

    [[ 0  1  2  3]
     [10 11 12 13]
     [20 21 22 23]
     [30 31 32 33]
     [40 41 42 43]]
    b[2,3]: 23
    b[0:5, 1]: [ 1 11 21 31 41]
    b[:, 1]: [ 1 11 21 31 41]
    b[1:3, : ]: [[10 11 12 13]
     [20 21 22 23]]
    b[-1]: [40 41 42 43]


1. `b[i]`中括号中的表达式被当作i和一系列:，来代表剩下的轴。NumPy也允许你使用“点”像`b[i,...]`。
2. dot(…)代表许多产生一个完整的索引元组必要的分号。如果x是秩为5的数组(即它有5个轴)，那么:
    1. `x[1,2,…]` 等同于 `x[1,2,:,:,:]`,
    2. `x[…,3]` 等同于 `x[:,:,:,:,3]`
    3. `x[4,…,5,:]` 等同 `x[4,:,:,5,:]`.


```python
c = np.array([[[0, 1, 2],
               [10, 12, 13]],
              [[100, 101, 102],
               [110, 111, 112]]])# a 3d array (two stacked 2D array)
print(c.shape)
print(c[1,...])# same as c[1,:,:] or c[1]
print(c[...,2])# same as c[:,:,2]
```

    (2, 2, 3)
    [[100 101 102]
     [110 111 112]]
    [[  2  13]
     [102 112]]



```python
# 迭代多维数组是就第一个轴而言的:
for i in b:
    print(i)
for i in c:
    print(i)
# 然而，如果一个人想对每个数组中元素进行运算，我们可以使用flat属性，该属性是数组元素的一个迭代器:
for element in b.flat:
    print(element)
for element in c.flat:
    print(element)
```

    [0 1 2 3]
    [10 11 12 13]
    [20 21 22 23]
    [30 31 32 33]
    [40 41 42 43]
    [[ 0  1  2]
     [10 12 13]]
    [[100 101 102]
     [110 111 112]]
    0
    1
    2
    3
    10
    11
    12
    13
    20
    21
    22
    23
    30
    31
    32
    33
    40
    41
    42
    43
    0
    1
    2
    10
    12
    13
    100
    101
    102
    110
    111
    112


更多操作 `Indexing, Indexing (reference), newaxis, ndenumerate, indices`

## 形状操作（shape manipulation）
### 改变数组的形状
一个数组的形状由它每个轴上的元素个数给出：


```python
a = np.floor(10*np.random.random((3,4)))
print(a)
print(a.shape)
# 一个数组的形状可以被多种命令修改, 下面三个命令返回的修改后的array,并不是修改原来的array
print('a.ravel():',a.ravel()) # return the flatten array
print('orginal a:',a)
print('a.reshape(6, 2):',a.reshape(6, 2))# returns the array with a modified shape
print('orginal a:',a)
print('a.T:',a.T)# return the array, transposed
print('orginal a:',a)
print('a.T.shape:',a.T.shape)
print('a.shape:',a.shape)
print('a.transpose():',a.transpose())
```

    [[ 2.  6.  1.  9.]
     [ 3.  9.  1.  5.]
     [ 2.  4.  0.  2.]]
    (3, 4)
    a.ravel(): [ 2.  6.  1.  9.  3.  9.  1.  5.  2.  4.  0.  2.]
    orginal a: [[ 2.  6.  1.  9.]
     [ 3.  9.  1.  5.]
     [ 2.  4.  0.  2.]]
    a.reshape(6, 2): [[ 2.  6.]
     [ 1.  9.]
     [ 3.  9.]
     [ 1.  5.]
     [ 2.  4.]
     [ 0.  2.]]
    orginal a: [[ 2.  6.  1.  9.]
     [ 3.  9.  1.  5.]
     [ 2.  4.  0.  2.]]
    a.T: [[ 2.  3.  2.]
     [ 6.  9.  4.]
     [ 1.  1.  0.]
     [ 9.  5.  2.]]
    orginal a: [[ 2.  6.  1.  9.]
     [ 3.  9.  1.  5.]
     [ 2.  4.  0.  2.]]
    a.T.shape: (4, 3)
    a.shape: (3, 4)
    a.transpose(): [[ 2.  3.  2.]
     [ 6.  9.  4.]
     [ 1.  1.  0.]
     [ 9.  5.  2.]]


由`ravel()`展平的数组元素的顺序通常是`“C风格”`的，就是说，最右边的索引变化得最快，所以元素`a[0,0]`之后是`a[0,1]`。如果数组被改变形状`(reshape)`成其它形状，数组仍然是“C风格”的。NumPy通常创建一个以这个顺序保存数据的数组，所以`ravel()`将总是不需要复制它的参数。但是如果数组是通过切片其它数组或有不同寻常的选项时，它可能需要被复制。函数`reshape()`和`ravel()`还可以被同过一些可选参数构建成`FORTRAN`风格的数组，即最左边的索引变化最快。

`reshape`函数改变参数形状并返回它，而`resize`函数改变数组自身,原本函数。
如果在改变形状操作中一个维度被给做-1，其维度将自动被计算


```python
print('original a', a)
b = a.reshape(2, 6)
print('b:',b)
print('original a:',a)
a.resize((6,2))
print(a)
print(a.reshape(4, -1))
```

    original a [[ 2.  6.]
     [ 1.  9.]
     [ 3.  9.]
     [ 1.  5.]
     [ 2.  4.]
     [ 0.  2.]]
    b: [[ 2.  6.  1.  9.  3.  9.]
     [ 1.  5.  2.  4.  0.  2.]]
    original a: [[ 2.  6.]
     [ 1.  9.]
     [ 3.  9.]
     [ 1.  5.]
     [ 2.  4.]
     [ 0.  2.]]
    [[ 2.  6.]
     [ 1.  9.]
     [ 3.  9.]
     [ 1.  5.]
     [ 2.  4.]
     [ 0.  2.]]
    [[ 2.  6.  1.]
     [ 9.  3.  9.]
     [ 1.  5.  2.]
     [ 4.  0.  2.]]


See also
ndarray.shape, reshape, resize, ravel

### 组合(stack)不同的数组
几种方法可以沿不同轴将数组堆叠在一起


```python
a = np.floor(10*np.random.random((2, 2)))
print('original a:',a)
b = np.floor(1*np.random.random((2,2)))
print('original b:', b)

# stack two arraies on vertical row_stack函数，另一方面，将一维数组以行组合成二维数组。
print(np.vstack((a, b)))
#stack two arraies on horizon 
# 对那些维度比二维更高的数组，hstack沿着第二个轴组合，vstack沿着第一个轴组合,concatenate允许可选参数给出组合时沿着的轴。
print(np.hstack((a, b)))
```

    original a: [[ 4.  3.]
     [ 4.  5.]]
    original b: [[ 0.  0.]
     [ 0.  0.]]
    [[ 4.  3.]
     [ 4.  5.]
     [ 0.  0.]
     [ 0.  0.]]
    [[ 4.  3.  0.  0.]
     [ 4.  5.  0.  0.]]



```python
# 函数column_stack以列将一维数组合成二维数组，它等同与vstack对一维数组。
#print(np.column_stack((a, b))) # with 2D arrays
a = np.array([2., 4.])
b = np.array([3., 8.])
print('np.column_stack((a, b)):',np.column_stack((a, b))) # return with 2D arrays 二维数组 一列列合并 旧列作为新行
print('np.hstack((a, b)):',np.hstack((a, b))) # return a different result 行合并
print('np.vstack((a, b)):',np.vstack((a, b)))# return a different array 列合并 
print('a[:, newaxis]:',a[:,newaxis]) # 允许生成2维数组
print('np.column_stack((a[:,newaxis],b[:,newaxis])):',np.column_stack((a[:,newaxis],b[:,newaxis])))
print(np.hstack((a[:,newaxis], b[:,newaxis])))# result is same
```

    np.column_stack((a, b)): [[ 2.  3.]
     [ 4.  8.]]
    np.hstack((a, b)): [ 2.  4.  3.  8.]
    np.vstack((a, b)): [[ 2.  4.]
     [ 3.  8.]]
    a[:, newaxis]: [[ 2.]
     [ 4.]]
    np.column_stack((a[:,newaxis],b[:,newaxis])): [[ 2.  3.]
     [ 4.  8.]]
    [[ 2.  3.]
     [ 4.  8.]]



```python
# Note
# 在复杂情况下，r_[]和c_[]对创建沿着一个方向组合的数很有用，它们允许范围符号(“:”):
# 当使用数组作为参数时，r_和c_的默认行为和vstack和hstack很像，但是允许可选的参数给出组合所沿着的轴的代号。
print(np.r_[1:4,0,4])
```

    [1 2 3 0 4]


### 将一个数组分割(split)成几个小数组
使用`hsplit`你能将数组沿着它的水平轴分割，或者指定返回相同形状数组的个数，或者指定在哪些列后发生分割:


```python
a=np.floor(10*np.random.random((2, 12)))
print(a)
print(np.hsplit(a, 3))  # Split a into 3
print(np.hsplit(a, (3, 4)))# Split a after the third and the fourth column
# vsplit沿着纵向的轴分割，array split允许指定沿哪个轴分割。
```

    [[ 4.  9.  3.  1.  3.  6.  7.  6.  4.  3.  2.  8.]
     [ 4.  0.  2.  1.  0.  1.  1.  7.  8.  1.  8.  4.]]
    [array([[ 4.,  9.,  3.,  1.],
           [ 4.,  0.,  2.,  1.]]), array([[ 3.,  6.,  7.,  6.],
           [ 0.,  1.,  1.,  7.]]), array([[ 4.,  3.,  2.,  8.],
           [ 8.,  1.,  8.,  4.]])]
    [array([[ 4.,  9.,  3.],
           [ 4.,  0.,  2.]]), array([[ 1.],
           [ 1.]]), array([[ 3.,  6.,  7.,  6.,  4.,  3.,  2.,  8.],
           [ 0.,  1.,  1.,  7.,  8.,  1.,  8.,  4.]])]


## 复制与视图（Copies and Views）
当运算和处理数组时，它们的数据有时被拷贝到新的数组有时不是。这通常是新手的困惑之源。这有三种情况:
### 完全不拷贝
简单的赋值不拷贝数组对象或它们的数据。


```python
a = np.arange(12)
b = a # no new object is created
print(b is a) # a and b are two names for the same ndarray object
b.shape = 3,4
print(a.shape)
# Python 传递不定对象作为参考，所以函数调用不拷贝数组。
def f(x):
    print(id(x))
print(id(a)) # id is a unique identifier of an object
print(f(a))
```

    True
    (3, 4)
    139751323982384
    139751323982384
    None


### 视图(view)和浅复制
不同的数组对象分享同一个数据。视图方法`view()`**创造一个新的数组对象**指向同一数据。


```python
print(a)
c = a.view()
print('c:',c)
print('c is a:',c is a)
print('c.base is a:',c.base is a) # c is a view of the data owned by a
print('c.flags.owndata:',c.flags.owndata)
print('a.flags.owndata:',c.flags.owndata)
c.shape=2,6
print(c.shape)
print(a.shape)
c[0,4] = 1234
print('c:',c)
print('a:',a)
```

    [[   0    1    2    3]
     [1234    5    6    7]
     [   8    9   10   11]]
    c: [[   0    1    2    3]
     [1234    5    6    7]
     [   8    9   10   11]]
    c is a: False
    c.base is a: True
    c.flags.owndata: False
    a.flags.owndata: False
    (2, 6)
    (3, 4)
    c: [[   0    1    2    3 1234    5]
     [   6    7    8    9   10   11]]
    a: [[   0    1    2    3]
     [1234    5    6    7]
     [   8    9   10   11]]



```python
# 切片数组返回它的一个视图：
s = a[:, 1:3]# spaces add for clarity; could be also wirtten as "s=[:,1:3]"
print(s)
s[:]=10 # s[:] is a view of s. Note the difference between s=10 and s[:]=10
print(s)
print(a)
```

    [[10 10]
     [10 10]
     [10 10]]
    [[10 10]
     [10 10]
     [10 10]]
    [[10 10 10  3]
     [10 10 10  7]
     [10 10 10 11]]


### 深复制(deep copy)
这个复制方法完全复制**数组和它的数据**。


```python
print(a)
d = a.copy() # a new array object created
print(d)
print(d is a)
print(d.base is a)
d[0, 0] = 999
print(d)
print(a)
```

    [[10 10 10  3]
     [10 10 10  7]
     [10 10 10 11]]
    [[10 10 10  3]
     [10 10 10  7]
     [10 10 10 11]]
    False
    False
    [[999  10  10   3]
     [ 10  10  10   7]
     [ 10  10  10  11]]
    [[10 10 10  3]
     [10 10 10  7]
     [10 10 10 11]]


## 函数和方法(method)总览
这是个NumPy函数和方法分类排列目录。这些名字链接到[NumPy示例](https://docs.scipy.org/doc/numpy/reference/routines.html#routines),你可以看到这些函数起作用

- 创建数组

`arange, array, copy, empty, empty_like, eye, fromfile, fromfunction, identity, linspace, logspace, mgrid, ogrid, ones, ones_like, r, zeros, zeros_like`
- 转化

`ndarray.astype, atleast_1d, atleast_2d, atleast_3d, mat`
- 操作

`array_split, column_stack, concatenate, diagonal, dsplit, dstack, hsplit, hstack, ndarray.item, newaxis, ravel, repeat, reshape, resize, squeeze, swapaxes, take, transpose, vsplit, vstack`
- 询问

`all, any, nonzero, where`
- 排序

`argmax, argmin, argsort, max, min, ptp, searchsorted, sort`
- 运算

`choose, compress, cumprod, cumsum, inner, fill, imag, prod, put, putmask, real, sum`
- 基本统计

`cov, mean, std, var`
- 基本线性代数

`cross, dot, outer, svd, vdot`

# 进阶
## 广播法则(rule)

- 广播法则能使通用函数有意义地处理不具有相同形状的输入。
- 广播第一法则是，如果所有的输入数组维度不都相同，一个**“1”**将被重复地添加在维度较小的数组上直至所有的数组拥有一样的维度。
- 广播第二法则确定长度为1的数组沿着特殊的方向表现地好像它有沿着那个方向最大形状的大小。对数组来说，沿着那个维度的数组元素的值理应相同。
- 应用广播法则之后，所有数组的大小必须匹配。[更多网址](https://docs.scipy.org/doc/numpy/user/basics.broadcasting.html)
 
We will add the vector v to each row of the matrix x,
storing the result in the matrix y

对两个数组使用广播机制要遵守下列规则：

1. 如果数组的秩不同，使用1来将秩较小的数组进行扩展，直到两个数组的尺寸的长度都一样。
2. 如果两个数组在某个维度上的长度是一样的，或者其中一个数组在该维度上长度为1，那么我们就说这两个数组在该维度上是相容的。
3. 如果两个数组在所有维度上都是相容的，他们就能使用广播。
4. 如果两个输入数组的尺寸不同，那么注意其中较大的那个尺寸。因为广播之后，两个数组的尺寸将和那个较大的尺寸一样。
5. 在任何一个维度上，如果一个数组的长度为1，另一个数组长度大于1，那么在该维度上，就好像是对第一个数组进行了复制

## 花哨的索引和索引技巧
NumPy比普通Python序列提供更多的索引功能。除了索引整数和切片，正如我们之前看到的，数组可以被整数数组和布尔数组索引。
### 通过数组索引


```python
a = np.arange(12)**2
print('a:',a)
i = np.array([1, 1, 3, 7, 8])
print(a[i]) # the elements of a at the positions i
j = np.array([[3, 5],[7, 9]]) # a bidimensional array of indices
print(a[j]) # the same shape as j 
```

    a: [  0   1   4   9  16  25  36  49  64  81 100 121]
    [ 1  1  9 49 64]
    [[ 9 25]
     [49 81]]



```python
# 当被索引数组a是多维的时，每一个唯一的索引数列指向a的第一维。以下示例通过将图片标签用调色版转换成色彩图像展示了这种行为。
palette = np.array([[0,0,0], #black
                   [255,0,0],#red
                   [0, 255, 0],#green
                   [0,0,255], # blue
                   [255,255, 2555]#white
                  ])
image = np.array([[0, 1, 2, 0],
                 [0, 3, 4, 0]]) # each value corresponds to a color in the palette
print(palette[image])  # the color(2, 4, 3) color image
```




    array([[[   0,    0,    0],
            [ 255,    0,    0],
            [   0,  255,    0],
            [   0,    0,    0]],
    
           [[   0,    0,    0],
            [   0,    0,  255],
            [ 255,  255, 2555],
            [   0,    0,    0]]])




```python
# 我们也可以给出不不止一维的索引，每一维的索引数组必须有相同的形状。
a = np.arange(12).reshape(3, 4)
print(a)
i = np.array([[0, 1],
              [1, 2]])
j = np.array([[2, 1],
              [3, 3]])
print('a[i,j]:',a[i,j])
print('a[i,2]:',a[i,2])
print('a[:,j]:',a[:,j])
```

    [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]]
    a[i,j]: [[ 2  5]
     [ 7 11]]
    a[i,2]: [[ 2  6]
     [ 6 10]]
    a[:,j]: [[[ 2  1]
      [ 3  3]]
    
     [[ 6  5]
      [ 7  7]]
    
     [[10  9]
      [11 11]]]



```python
# 自然，我们可以把i和j放到序列中(比如说列表)然后通过list索引。
l = [i,j]
print(l)
# 然而，我们不能把i和j放在一个数组中，因为这个数组将被解释成索引a的第一维。
s = array( [i,j] ) # not we want
a[s]
```

    [array([[0, 1],
           [1, 2]]), array([[2, 1],
           [3, 3]])]



```python
# 另一个常用的数组索引用法是搜索时间序列最大值
time = np.linspace(20, 145, 5)                 # time scale
data = np.sin(arange(20)).reshape(5,4)         # 4 time-dependent series
print(time)
print(data)
ind = data.argmax(axis=0)                  # index of the maxima for each series
print(ind)
time_max = time[ind]                       # times corresponding to the maxima
print(time_max)
data_max = data[ind, range(data.shape[1])] # => data[ind[0],0], data[ind[1],1]...
print(data_max)
print(np.all(data_max == data.max(axis=0)))
```

    [  20.     51.25   82.5   113.75  145.  ]
    [[ 0.          0.84147098  0.90929743  0.14112001]
     [-0.7568025  -0.95892427 -0.2794155   0.6569866 ]
     [ 0.98935825  0.41211849 -0.54402111 -0.99999021]
     [-0.53657292  0.42016704  0.99060736  0.65028784]
     [-0.28790332 -0.96139749 -0.75098725  0.14987721]]
    [2 0 3 1]
    [  82.5    20.    113.75   51.25]
    [ 0.98935825  0.84147098  0.99060736  0.6569866 ]
    True



```python
# 你也可以使用数组索引作为目标来赋值：
a = np.arange(5)
print(a)
a[[1,3,4]] = 0
print(a)
# 然而，当一个索引列表包含重复时，赋值被多次完成，保留最后的值：
a = np.arange(5)
a[[0,0,2]]=[1,2,3]
print(a)
# 这足够合理，但是小心如果你想用Python的+=结构，可能结果并非你所期望：
a = np.arange(5)
a[[0,0,2]]+=1
print(a)
# 即使0在索引列表中出现两次，索引为0的元素仅仅增加一次。这是因为Python要求a+=1和a=a+1等同。
```

    [0 1 2 3 4]
    [0 0 2 0 0]
    [2 1 3 3 4]
    [1 1 3 3 4]


### 通过布尔数组索引
当我们使用整数数组索引数组时，我们提供一个索引列表去选择。通过布尔数组索引的方法是不同的我们显式地选择数组中我们想要和不想要的元素。

我们能想到的使用布尔数组的索引最自然方式就是使用和原数组一样形状的布尔数组。


```python
a = np.arange(12).reshape(3, 4)
b = a > 4
print(b) # b is a boolean with a's shape
print(a[b]) # 1d array with the selected elements
# 这个属性在赋值时非常有用：
a[b] = 0                                   # All elements of 'a' higher than 4 become 0
print(a)
```

    [[False False False False]
     [False  True  True  True]
     [ True  True  True  True]]
    [ 5  6  7  8  9 10 11]
    [[0 1 2 3]
     [4 0 0 0]
     [0 0 0 0]]



```python
# 例子
import numpy as np
import matplotlib.pyplot as plt
def mandelbrot(h,w, maxit=20):
    """Returns an image of the Mandelbrot fractal of size (h,w)."""
    y,x = np.ogrid[ -1.4:1.4:h*1j, -2:0.8:w*1j ]
    c = x+y*1j
    z = c
    divtime = maxit + np.zeros(z.shape, dtype=int)

    for i in range(maxit):
        z = z**2 + c
        diverge = z*np.conj(z) > 2**2            # who is diverging
        div_now = diverge & (divtime==maxit)  # who is diverging now
        divtime[div_now] = i                  # note when
        z[diverge] = 2                        # avoid diverging too much
        return divtime
plt.imshow(mandelbrot(400,400))
plt.show()
```


![png](output_58_0.png)


第二种通过布尔来索引的方法更近似于整数索引；对数组的每个维度我们给一个一维布尔数组来选择我们想要的切片。


```python
a = np.arange(12).reshape(3, 4)
b1 = np.array([False,True,True]) # first dim selection
b2 = np.array([True,False,True,False])       # second dim selection
print(a)
print('a[b1,:]:',a[b1,:])
print('a[b1]:',a[b1])
print('a[:,b2]:',a[:,b2]) # selecting columns
print('a[b1,b2]:',a[b1,b2])
```

    [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]]
    a[b1,:]: [[ 4  5  6  7]
     [ 8  9 10 11]]
    a[b1]: [[ 4  5  6  7]
     [ 8  9 10 11]]
    a[:,b2]: [[ 0  2]
     [ 4  6]
     [ 8 10]]
    a[b1,b2]: [ 4 10]



```python
import numpy as np
x = np.array([[1,2,3], [4,5,6], [7,8,9], [10, 11, 12]])
v = np.array([1, 0, 1])
y = x + v  # Add v to each row of x using broadcasting
print(y)
```

    [[ 2  2  4]
     [ 5  5  7]
     [ 8  8 10]
     [11 11 13]]


## `ix_()`函数
`ix_`函数可以为了获得多元组的结果而用来结合不同向量。例如，如果你想要用所有向量a、b和c元素组成的三元组来计算`a+b*c`：


```python
a = np.array([2,3,4,5])
b = np.array([8,5,4])
c = np.array([5,4,6,8,3])
ax,bx,cx = np.ix_(a,b,c)
print('ax:',ax)
print('bx:',bx)
print('cx:',cx)
print(ax.shape, bx.shape, cx.shape)
result = ax+bx*cx
print('result:',result)
print(result[3,2,4])
print(a[3]+b[2]*c[4])
```

    ax: [[[2]]
    
     [[3]]
    
     [[4]]
    
     [[5]]]
    bx: [[[8]
      [5]
      [4]]]
    cx: [[[5 4 6 8 3]]]
    (4, 1, 1) (1, 3, 1) (1, 1, 5)
    result: [[[42 34 50 66 26]
      [27 22 32 42 17]
      [22 18 26 34 14]]
    
     [[43 35 51 67 27]
      [28 23 33 43 18]
      [23 19 27 35 15]]
    
     [[44 36 52 68 28]
      [29 24 34 44 19]
      [24 20 28 36 16]]
    
     [[45 37 53 69 29]
      [30 25 35 45 20]
      [25 21 29 37 17]]]
    17
    17



```python
# 你也可以实行如下简化：
def ufunc_reduce(ufct, *vectors):
    vs = ix_(*vectors)
    r = ufct.identity
    for v in vs:
        r = ufct(r,v)
    return r
# 然后这样使用它：
ufunc_reduce(add,a,b,c)
# 这个reduce与ufunc.reduce(比如说add.reduce)相比的优势在于它利用了广播法则，避免了创建一个输出大小乘以向量个数的参数数组。
```




    array([[[15, 14, 16, 18, 13],
            [12, 11, 13, 15, 10],
            [11, 10, 12, 14,  9]],
    
           [[16, 15, 17, 19, 14],
            [13, 12, 14, 16, 11],
            [12, 11, 13, 15, 10]],
    
           [[17, 16, 18, 20, 15],
            [14, 13, 15, 17, 12],
            [13, 12, 14, 16, 11]],
    
           [[18, 17, 19, 21, 16],
            [15, 14, 16, 18, 13],
            [14, 13, 15, 17, 12]]])



### 用字符串索引
See Structured arrays.

# 线性代数
## 简单数组运算
参考numpy文件夹中的linalg.py获得更多信息


```python
import numpy as np
a = np.array([[1.0, 2.0], [3.0, 4.0]])
print(a)
print(a.T)
print(np.linalg.inv(a))
u = np.eye(2) # unit 2x2 matrix; "eye" represents "I"
print(u)
j = np.array([[0.0, -1.0], [1.0, 0.0]])
print(np.dot (j, j)) # matrix product
print(np.trace(u))
y = np.array([[5.], [7.]])
print(np.linalg.solve(a, y))
print(np.linalg.eig(j))
```

    [[ 1.  2.]
     [ 3.  4.]]
    [[ 1.  3.]
     [ 2.  4.]]
    [[-2.   1. ]
     [ 1.5 -0.5]]
    [[ 1.  0.]
     [ 0.  1.]]
    [[-1.  0.]
     [ 0. -1.]]
    2.0


## 矩阵类
这是一个关于矩阵类的简短介绍。


```python
A = np.matrix('1.0 2.0;3.0 4.0')
print(A)
print(type(A))
print(A.T) # transpose
X = np.matrix('5.0 7.0')
Y = X.T
print(Y)
print(A*Y) # matrix multiplication
print(A.I) # inverse
```

    [[ 1.  2.]
     [ 3.  4.]]
    <class 'numpy.matrixlib.defmatrix.matrix'>
    [[ 1.  3.]
     [ 2.  4.]]
    [[ 5.]
     [ 7.]]
    [[ 19.]
     [ 43.]]
    [[-2.   1. ]
     [ 1.5 -0.5]]


### 索引：比较矩阵和二维数组
注意NumPy中数组和矩阵有些重要的区别。NumPy提供了两个基本的对象：一个N维数组对象和一个通用函数对象。其它对象都是建构在它们之上的。特别的，矩阵是继承自NumPy数组对象的二维数组对象。对数组和矩阵，索引都必须包含合适的一个或多个这些组合：整数标量、省略号(ellipses)、整数列表;布尔值，整数或布尔值构成的元组，和一个一维整数或布尔值数组。矩阵可以被用作矩阵的索引，但是通常需要数组、列表或者其它形式来完成这个任务。

像平常在Python中一样，索引是从0开始的。传统上我们用矩形的行和列表示一个二维数组或矩阵，其中沿着0轴的方向被穿过的称作行，沿着1轴的方向被穿过的是列。

让我们创建数组和矩阵用来切片：


```python
A=np.arange(12)
print(A)
A.shape=(3, 4)
print(A)
M = np.mat(A.copy())
print(M)
print(type(A), type(M))
```

    [ 0  1  2  3  4  5  6  7  8  9 10 11]
    [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]]
    [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]]
    <class 'numpy.ndarray'> <class 'numpy.matrixlib.defmatrix.matrix'>


现在，让我们简单的切几片。基本的切片使用切片对象或整数。例如，`A[:]`和`M[:]`的求值将表现得和Python索引很相似。然而要注意很重要的一点就是NumPy切片数组不创建数据的副本;切片提供统一数据的视图。


```python
print(A[:],A[:].shape)
print(M[:],M[:].shape)
# 现在有些和Python索引不同的了：你可以同时使用 逗号 分割索引来沿着多个轴索引。
print(A[:,1]); print(A[:,1].shape)
print(M[:,1]); print(M[:,1].shape)
```

    [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]] (3, 4)
    [[ 0  1  2  3]
     [ 4  5  6  7]
     [ 8  9 10 11]] (3, 4)
    [1 5 9]
    (3,)
    [[1]
     [5]
     [9]]
    (3, 1)


 注意最后两个结果的不同。**对二维数组使用一个冒号产生一个一维数组，然而矩阵产生了一个二维矩阵**。例如，一个`M[2,:]`切片产生了一个形状为(1,4)的矩阵，相比之下，一个数组的切片总是产生一个最低可能维度11的数组。例如，如果C是一个三维数组，`C[...,1]`产生一个二维的数组而`C[1,:,1]`产生一个一维数组。从这时开始，如果相应的矩阵切片结果是相同的话，我们将只展示数组切片的结果。
 
 假如我们想要一个数组的第一列和第三列，一种方法是使用列表切片：


```python
print(A[:,[1,3]])
#稍微复杂点的方法是使用take()方法(method):
print(A[:,].take([1,3],axis=1))
# 如果我们想跳过第一行，我们可以这样：
print(A[1:,].take([1,3],axis=1))
# 或者我们仅仅使用A[1:,[1,3]]。还有一种方法是通过矩阵向量积(叉积)。
print(A[ix_((1,2),(1,3))])
# 现在让我们做些更复杂的。比如说我们想要保留第一行大于1的列。一种方法是创建布尔索引：
print(A[0,:]>1)
print(A[:,A[0,:]>1])
```

    [[ 1  3]
     [ 5  7]
     [ 9 11]]
    [[ 1  3]
     [ 5  7]
     [ 9 11]]
    [[ 5  7]
     [ 9 11]]
    [[ 5  7]
     [ 9 11]]
    [False False  True  True]
    [[ 2  3]
     [ 6  7]
     [10 11]]



```python
# 就是我们想要的！但是索引矩阵没这么方便。
M = np.mat(A.copy())
print(M[0,:]>1)
# print(M[:,M[0,:]>1])
# 这个过程的问题是用“矩阵切片”来切片产生一个矩阵12，但是矩阵有个方便的A属性，它的值是数组呈现的。所以我们仅仅做以下替代：
print(M[:,M.A[0,1]>1])
print(M[:,M.A[0,:]>1])
# 如果我们想要在矩阵两个方向有条件地切片，我们必须稍微调整策略，代之以：
print(A[A[:,0]>2,A[0,:]>1])
print(M[M.A[:,0]>2,M.A[0,:]>1])
# 我们需要使用向量积ix_:
print(A[ix_(A[:,0]>2,A[0,:]>1)])
print(M[ix_(M.A[:,0]>2,M.A[0,:]>1)])
```

    [[False False  True  True]]
    []
    [[ 2  3]
     [ 6  7]
     [10 11]]
    [ 6 11]
    [[ 6 11]]
    [[ 6  7]
     [10 11]]
    [[ 6  7]
     [10 11]]


# 技巧和提示
## “自动”改变形状
更改数组的维度，你可以省略一个尺寸，它将被自动推导出来。


```python
a = np.arange(30)
a.shape = 2,-1,3  # -1 means "whatever is needed"
print(a.shape)
print(a)
```

    (2, 5, 3)
    [[[ 0  1  2]
      [ 3  4  5]
      [ 6  7  8]
      [ 9 10 11]
      [12 13 14]]
    
     [[15 16 17]
      [18 19 20]
      [21 22 23]
      [24 25 26]
      [27 28 29]]]


## 向量组合(stacking)
我们如何用两个相同尺寸的行向量列表构建一个二维数组？在MATLAB中这非常简单：如果x和y是两个相同长度的向量，你仅仅需要做`m=[x;y]`。在NumPy中这个过程通过函数`column_stack、dstack、hstack和vstack来`完成，取决于你想要在那个维度上组合。例如：


```python
x=np.arange(0, 10, 2)
y = np.arange(5)
m = vstack([x,y])
print(m)
xy = hstack([x,y])
print(xy)
```

    [[0 2 4 6 8]
     [0 1 2 3 4]]
    [0 2 4 6 8 0 1 2 3 4]


## 直方图(histogram)
NumPy中histogram函数应用到一个数组返回一对变量：直方图数组和箱式向量。注意：`matplotlib`也有一个用来建立直方图的函数(叫作hist,正如matlab中一样)与NumPy中的不同。主要的差别是pylab.hist自动绘制直方图，而`numpy.histogram`仅仅产生数据。


```python
import numpy
import pylab
# Build a vector of 10000 normal deviates with variance 0.5^2 and mean 2
mu, sigma = 2, 0.5
v = numpy.random.normal(mu,sigma,10000)
# Plot a normalized histogram with 50 bins
pylab.hist(v, bins=50, normed=1)       # matplotlib version (plot)
pylab.show()
# Compute the histogram with numpy and then plot it
(n, bins) = numpy.histogram(v, bins=50, normed=True)  # NumPy version (no plot)
pylab.plot(.5*(bins[1:]+bins[:-1]), n)
pylab.show()
```




