# 图的总结

## 图的表示

LeetCode 207

```java
List<List<Integer>> adjacency = new ArrayList<>(numCourses);

HashSet<Integer>[] adj = new HashSet[numCourses];

// 此外
class Node {
    public int val;
    public List<Node> neighbors;
    public Node() {
        val = 0;
        neighbors = new ArrayList<>();
    }
    public Node(int x) {
        val = x;
        neighbors = new ArrayList<>();
    }
    public Node(int x, ArrayList<Node> neighbors) {
        val = x;
        this.neighbors = neighbors;
    }
}

Queue<Integer> queue = new LinkedList<Integer>();

Stack<Integer> stack = new LinkedList<Integer>();
Deque<Integer> stack = new ArrayDeque<>();

List<Integer> list = new ArrayList<>();
```

## 图的遍历

对图进行 dfs 和 bfs 即可。这里简单总结下 dfs 和 dfs。

bfs 递归。可以想想二叉树中如何递归的进行层序遍历。
bfs 非递归。一般用队列存储。
dfs 递归。最常用，如二叉树的先序遍历。
dfs 非递归。一般用 stack。



从一个节点出发，遍历图，还需要一个二维数组，或者HashMap作为记录已被访问过的节点。

如LeetCode 133

```java
 HashMap<Node, Node> visited = new HashMap();
```

数据的存储结构