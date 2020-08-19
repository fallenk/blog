# 图论算法1

## **图论基础知识**

​	图是由**结点（Vertex）**和**边（Edge）**组成的抽象的数据结构。结点和结点之间靠边连接起来。

### 图的分类 1

- 无向图（Undirected Graph），是我们主要研究的部分，我们可**以把无向图看成一种特殊的有向图**；
- 有向图（Directed Graph），有向图具有不对称的特性。

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588993656021-251eefc5-952a-45db-a0ab-ba72b4eea5a3.jpeg?x-oss-process=image%2Fresize%2Cw_1252" alt="img" style="zoom:33%;" />

### 图的分类 2

- 无权图（Unweighted Graph）
- 有权图（Weighted Graph）

<img src="https://cdn.nlark.com/yuque/0/2020/png/1354172/1588993656127-f30bb1a0-fe9e-469e-863f-432832ff8866.png" alt="img" style="zoom:33%;" />

### 图的连通性

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588993655991-277549c7-f6b7-4636-b57f-76420a52b63b.jpeg?x-oss-process=image%2Fresize%2Cw_714" alt="img" style="zoom:50%;" />

上面这张图的连通分量为 3 。

### 简单图（Simple Graph）

- 无自环边（self-loop）；
- 并且无平行边的图（parallel-edges）

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588993656010-4b637b3e-2d6b-4442-8455-a14b91b229b8.jpeg?x-oss-process=image%2Fresize%2Cw_848" alt="img" style="zoom:50%;" />

我们的学习从简单图开始。

### 图的表示

本章仅介绍无权图。图这种数据结构可以使用「邻接矩阵」和「邻接表」来表示。

### 邻接矩阵

无向图的邻接矩阵表示是一个对称矩阵。

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588993660175-ee1f720e-f62c-4c5d-9551-16d5f51e7668.jpeg?x-oss-process=image%2Fresize%2Cw_1252" alt="img" style="zoom:50%;" />

### 邻接表

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588993909600-dee85dcc-f2e9-4412-8d9c-070bba27b5ae.jpeg?x-oss-process=image%2Fresize%2Cw_1252" alt="img" style="zoom:50%;" />

### 稀疏图与稠密图

- 邻接表适合表示**稀疏图（Sparse Graph）**，边较少；
- 邻接矩阵适合表示**稠密图（Dense Graph）**，边较多。

我们的图是实现了如下接口的 Java 类。

Java 代码：

```java
// 图的接口，统一表示稀疏图与稠密图
public interface Graph {

    // 返回顶点的个数
    int V();

    // 返回边的条数
    int E();

    // 在顶点 v 和 w 之间添加一条表
    void addEdge(int v, int w);

    // 顶点 v 与 顶点 w 是否有边连接
    boolean hasEdge(int v, int w);

    // 显示这个图中的元素
    void show();

    // 得到顶点 v 的所有邻居顶点
    Iterable<Integer> adj(int v);
}
```

### 编写相邻结点迭代器

为了方便遍历一个点的所有邻居结点，我们需要实现一个迭代器。

### 稀疏图（邻接表）的相邻点迭代器

由稀疏图（邻接表）的表示决定了返回一个结点的所有相邻结点是十分容易的。

Java 代码：

```java
public Iterable<Integer> adj(int v) {
    assert v >= 0 && v < n;
    return g[v];
}
```

### 稠密图（邻接矩阵）的相邻点迭代器

由稠密图（邻接矩阵）得到一个结点的所有相邻结点只须遍历邻接矩阵中的一行，把值为 `True` 的所有顶点拿到即可。

Java 代码：

```java
public Iterable<Integer> adj(int v){
    assert v >= 0 && v < n;
  	Vector<Integer> adjV = new Vector<Integer>();
  	for(int i=0; i<n; i++) {
      	if(g[v][i]) {
          	adjV.add(i);
        }
    }
  	return adjV;
}
```

我们将图的数据以一定格式保存在一个文件中，因此我们要编写一个工具类读这个图文件。

### 从一个文件读出一个图

我们图的信息存在一个文件中，然后通过读取这个文件的内容得到图的顶点和边的信息，为此我们编写了如下代码。

### 编写一个读图的工具类

例 1：文件 testG1.txt 的内容如下，其中第 1 行的第 1 个数表示这张图有多少个顶点，第 2 个数表示这张图有多少条边。

第 2 行直至最后一行表示的是所有的边，以两个顶点来表示一条边。

```java
13,13
0,5
4,3
0,1
9,12
6,4
5,4
0,2
11,12
9,10
0,6
7,8
9,11
5,3
```

如果它表示一个无向图，那么画出来就是：

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588994043842-4f9726d6-b800-4ee1-9119-1092094cb084.jpeg?x-oss-process=image%2Fresize%2Cw_1252" alt="img" style="zoom:50%;" />

用邻接表表示是这样的：

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588993662703-1432d240-039a-47f6-bbe5-bc8b9cb965ca.jpeg" alt="img" style="zoom:33%;" />

用邻接矩阵表示是这样的：

<img src="https://cdn.nlark.com/yuque/0/2020/jpeg/1354172/1588993663506-bcd87234-3eb5-4bcf-ba40-1b0a5445ef5b.jpeg" alt="img" style="zoom:50%;" />

参考 https://www.yuque.com/liweiwei1419/algo/nybhkd