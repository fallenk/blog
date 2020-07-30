# [ConcurrentHashMap 1.7](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_1-concurrenthashmap-17)

### [存储结构](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_1-存储结构)

![Java 7 ConcurrentHashMap 存储结构](https://snailclimb.gitee.io/javaguide/docs/java/collection/images/image-20200405151029416.png)


​        

```java
Segment 是 ConcurrentHashMap 的一个内部类
static final class Segment<K,V> extends ReentrantLock implements Serializable {
        private static final long serialVersionUID = 2249069246763182397L;    
        // 和 HashMap 中的 HashEntry 作用一样，真正存放数据的桶
        transient volatile HashEntry<K,V>[] table;
        transient int count;
        transient int modCount;
        transient int threshold;
        final float loadFactor;
}
```

![image-20200729234759494](/Users/liulifeng/Desktop/面经/images/54.png)

HashEntry和 HashMap 非常类似，唯一的区别就是其中的核心数据如 value ，以及链表都是 volatile 修饰的，保证了获取时的可见性。



原理上来说：ConcurrentHashMap 采用了分段锁技术，其中 Segment 继承于 ReentrantLock。不会像 HashTable 那样不管是 put 还是 get 操作都需要做同步处理，理论上 ConcurrentHashMap 支持 CurrencyLevel (Segment 数组数量)的线程并发。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment。





## 2. 初始化

总结一下在 Java 7 中 ConcurrnetHashMap 的初始化逻辑。

1. 必要参数校验。
2. 校验并发级别 concurrencyLevel 大小，如果大于最大值，重置为最大值。无惨构造**默认值是 16.**
3. 寻找并发级别 concurrencyLevel 之上最近的 **2 的幂次方**值，作为初始化容量大小，**默认是 16**。
4. 记录 segmentShift 偏移量，这个值为【容量 = 2 的N次方】中的 N，在后面 Put 时计算位置时会用到。**默认是 32 - sshift = 28**.
5. 记录 segmentMask，默认是 ssize - 1 = 16 -1 = 15.
6. **初始化 segments[0]**，**默认大小为 2**，**负载因子 0.75**，**扩容阀值是 2\*0.75=1.5**，插入第二个值时才会进行扩容。

## [3. put](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_3-put)

接着上面的初始化参数继续查看 put 方法源码。



上面的源码分析了 ConcurrentHashMap 在 put 一个数据时的处理流程，下面梳理下具体流程。

1. 计算要 put 的 key 的位置，获取指定位置的 Segment。

2. 如果指定位置的 Segment 为空，则初始化这个 Segment.

   **初始化 Segment 流程：**

   1. 检查计算得到的位置的 Segment 是否为null.
   2. 为 null 继续初始化，使用 Segment[0] 的容量和负载因子创建一个 HashEntry 数组。
   3. 再次检查计算得到的指定位置的 Segment 是否为null.
   4. 使用创建的 HashEntry 数组初始化这个 Segment.
   5. 自旋判断计算得到的指定位置的 Segment 是否为null，使用 CAS 在这个位置赋值为 Segment.

3. Segment.put 插入 key,value 值。



上面探究了获取 Segment 段和初始化 Segment 段的操作。最后一行的 Segment 的 put 方法还没有查看，继续分析。



由于 **Segment 继承了 ReentrantLock，所以 Segment 内部可以很方便的获取锁**，put 流程就用到了这个功能。

1. tryLock() 获取锁，获取不到使用 **`scanAndLockForPut`** 方法继续获取。

2. 计算 put 的数据要放入的 index 位置，然后获取这个位置上的 HashEntry 。

3. 遍历 put 新元素，为什么要遍历？因为这里获取的 HashEntry 可能是一个空元素，也可能是链表已存在，所以要区别对待。

   如果这个位置上的 **HashEntry 不存在**：

   1. 如果当前容量大于扩容阀值，小于最大容量，**进行扩容**。
   2. 直接头插法插入。

   如果这个位置上的 **HashEntry 存在**：

   1. 判断链表当前元素 Key 和 hash 值是否和要 put 的 key 和 hash 值一致。一致则替换值
   2. 不一致，获取链表下一个节点，直到发现相同进行值替换，或者链表表里完毕没有相同的。
      1. 如果当前容量大于扩容阀值，小于最大容量，**进行扩容**。
      2. 直接链表头插法插入。

4. 如果要插入的位置之前已经存在，替换后返回旧值，否则返回 null.

这里面的第一步中的 scanAndLockForPut 操作这里没有介绍，这个方法做的操作就是不断的自旋 `tryLock()` 获取锁。当自旋次数大于指定次数时，使用 `lock()` 阻塞获取锁。在自旋时顺表获取下 hash 位置的 HashEntry。



## [4. 扩容 rehash](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_4-扩容-rehash)

ConcurrentHashMap 的扩容只会扩容到原来的两倍。老数组里的数据移动到新的数组时，位置要么不变，要么变为 index+ oldSize，参数里的 node 会在扩容之后使用链表**头插法**插入到指定位置。

// 计算新的位置，新的位置只可能是不便或者是老的位置+老的容量。            

int idx = e.hash & sizeMask;



## [5. get](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_5-get)

到这里就很简单了，get 方法只需要两步即可。

1. 计算得到 key 的存放位置。
2. 遍历指定位置查找相同 key 的 value 值。



# [2. ConcurrentHashMap 1.8](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_2-concurrenthashmap-18)

## [1. 存储结构](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_1-存储结构-1)

![Java8 ConcurrentHashMap 存储结构（图片来自 javadoop）](https://snailclimb.gitee.io/javaguide/docs/java/collection/images/java8_concurrenthashmap.png)

可以发现 Java8 的 ConcurrentHashMap 相对于 Java7 来说变化比较大，不再是之前的 **Segment 数组 + HashEntry 数组 + 链表**，而是 **Node 数组 + 链表 / 红黑树**。当冲突链表达到一定长度时，链表会转换成红黑树。

## [2. 初始化 initTable](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_2-初始化-inittable)

从源码中可以发现 ConcurrentHashMap 的初始化是通过**自旋和 CAS** 操作完成的。里面需要注意的是变量 `sizeCtl` ，它的值决定着当前的初始化状态。

1. -1 说明正在初始化
2. -N 说明有N-1个线程正在进行扩容
3. 表示 table 初始化大小，如果 table 没有初始化
4. 表示 table 容量，如果 table　已经初始化。



## [3. put](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_3-put-1)

直接过一遍 put 源码。



1. 根据 key 计算出 hashcode 。
2. 判断是否需要进行初始化。
3. 即为当前 key 定位出的 Node，如果为空表示当前位置可以写入数据，**利用 CAS 尝试写入，失败则自旋保证成功**。
4. 如果当前位置的 `hashcode == MOVED == -1`,则需要进行扩容。
5. 如果都不满足，则**利用 synchronized 锁写入数据**。
6. 如果数量大于 `TREEIFY_THRESHOLD` 则要转换为红黑树。

## [4. get](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_4-get)

get 流程比较简单，直接过一遍源码。

总结一下 get 过程：

1. 根据 hash 值计算位置。
2. 查找到指定位置，如果头节点就是要找的，直接返回它的 value.
3. 如果头节点 hash 值小于 0 ，说明正在扩容或者是红黑树，查找之。
4. 如果是链表，遍历查找之。

总结：

总的来说 ConcruuentHashMap 在 Java8 中相对于 Java7 来说变化还是挺大的，





## [3. 总结](https://snailclimb.gitee.io/javaguide/#/docs/java/collection/ConcurrentHashMap?id=_3-总结)

Java7 中 ConcruuentHashMap 使用的分段锁，也就是每一个 Segment 上同时只有一个线程可以操作，每一个 **Segment 都是一个类似 HashMap 数组的结构，它可以扩容，它的冲突会转化为链表。但是 Segment 的个数一但初始化就不能改变**。

Java8 中的 ConcruuentHashMap 使用的 Synchronized 锁加 CAS 的机制。结构也由 Java7 中的 **Segment 数组 + HashEntry 数组 + 链表** 进化成了 **Node 数组 + 链表 / 红黑树**，Node 是类似于一个 HashEntry 的结构。它的冲突再达到一定大小时会转化成红黑树，在冲突小于一定数量时又退回链表。

有些同学可能对 Synchronized 的性能存在疑问，其实 Synchronized 锁自从引入锁升级策略后，性能不再是问题，有兴趣的同学可以自己了解下 Synchronized 的**锁升级**。