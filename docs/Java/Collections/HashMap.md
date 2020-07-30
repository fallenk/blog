# HashMap

## **谨记**

首先你应当记住的：**不管你传不传参数，不管你传入的长度为多少，在你用HashMap的时候，他的长度都是2的n次方，且最大长度为2的30次方**

## **最大长度**

在HashMap的源码中，最大长度这个常量值是这样定义的

```java
/**
     * The maximum capacity, used if a higher value is implicitly specified
     * by either of the constructors with arguments.
     * MUST be a power of two <= 1<<30.
     */
    static final int MAXIMUM_CAPACITY = 1 << 30;
```

这个值用在哪里呢？

- resize()函数，这个是用来扩容的
- tableSizeFor()，这个也是用来扩容的   这个是返回一个比输入值大的或者等于的最小的 2的n次方
- 构造函数中
- putEntries()，存放一组HashMap元素时，不是存放单个

## **为什么table长度一定是2的n次方**

注意，源码中他们采用了**延迟初始化操作**，也就是table只有在用到的时候才初始化，如果你不对他进行put等操作的话，table的长度永远为"零"

主要有两个函数保证了他的长度为2的n次方

- tableSizeFor()
- resize()

```java
static final int tableSizeFor(int cap) {
    int n = cap - 1;
    n |= n >>> 1;
    n |= n >>> 2;
    n |= n >>> 4;
    n |= n >>> 8;
    n |= n >>> 16;
    return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
 详解如下：
先来分析有关n位操作部分：先来假设n的二进制为01xxx...xxx。接着
对n右移1位：001xx...xxx，再位或：011xx...xxx
对n右移2为：00011...xxx，再位或：01111...xxx
此时前面已经有四个1了，再右移4位且位或可得8个1
同理，有8个1，右移8位肯定会让后八位也为1。
综上可得，该算法让最高位的1后面的位全变为1。
最后再让结果n+1，即得到了2的整数次幂的值了。  int 是4字节 32位
现在回来看看第一条语句：
int n = cap - 1;
让cap-1再赋值给n的目的是令找到的目标值大于或等于原值。例如二进制1000，十进制数值为8。如果不对它减1而直接操作，
将得到答案10000，即16。显然不是结果。减1后二进制为111，再进行操作则会得到原来的数值1000，即8。
```

所以他返回的值为 1（比0大的或者等于的最小的2的n次方 就是1）；

然后这个时候假如我们第一次 put一个元素，这个时候table数组就要开始初始化了，他就会执行这个resize函数



**resize**

oldThreshold等于之前使用 tableSizeFor 的返回值，也就是 1；所以table一经初始化长度就为1**（但是还要说明一点,执行完 resize函数之后 table.length等于1，threshold等于0。看我上面的分析，newThreshold=(int)0.75)就等于0**

但是我们执行的是存入元素的操作，所以存完之后，就要++size(因为之前里面没有元素)，这个时候 size就等于1，他就大于 threshold。所以他又要执行resize函数进行扩容操作。执行完之后，table.length 就等于2了，threshold就等于 1了。

table的扩容全部都是乘以 2（左移一位），而且table.length也一直等于2的n次方，即(table.length &(table.length-1)) == 0



## **计算方便**

- 当容量一定是2^n时，h & (length - 1) == h % length
- 扩容后计算新位置，非常方便，相比 JDK1.7

## **JDK 1.8改动**

在 JDK1.8 中，HashMap有了挺大的改动，包括

- 元素迁移算法(旧的到新的数组)
- 使用红黑树
- 链表为尾插法

其中我重点讲下元素迁移算法，JDK1.8的时候

首先看下java代码以及我的注释，

```java
// 将原来数组中的所有元素都 copy进新的数组
if(oldTab != null){
    for (int j = 0; j < oldCap; j++) {
        Entry e;

        if((e = oldTab[j]) != null){
            oldTab[j] = null;

            // 说明还没有成链，数组上只有一个
            if(e.next == null){
                // 重新计算 数组索引 值
                newTable[e.h & (newCap-1)] = e;

            }
            // 判断是否为树结构
            //else if (e instanceof TreeNode)


            // 如果不是树，只是链表,即长度还没有大于 8 进化成树
            else{
                // 扩容后，如果元素的 index 还是原来的。就使用这个lo前缀的
                Entry loHead=null, loTail =null;

                // 扩容后  元素index改变，那么就使用 hi前缀开头的
                Entry hiHead = null, hiTail = null;
                Entry next;
                do {
                    next = e.next;
                    //这个非常重要，也比较难懂，
                    // 将它和原来的长度进行相与，就是判断他的原来的hash的上一个  bit 位是否为 1。
                    //以此来判断他是在相同的索引还是table长度加上原来的索引
                    if((e.h & oldCap) == 0){
                        // 如果 loTail == null ,说明这个 位置上是第一次添加，没有哈希冲突
                        if(loTail == null)
                            loHead = e;
                        else
                            loTail.next = e;
                        loTail = e;
                    }
                    else{
                        if(hiTail == null)
                            loHead = e;
                        else
                            hiTail.next = e;
                        hiTail = e ;
                    }

                }while ((e = next) != null);


                if(loTail != null){
                    loTail.next = null;
                    newTable[j] = loHead;
                }

                // 新的index 等于原来的 index+oldCap
                else {

                    hiTail.next = null;
                    newTable[j+oldCap] = hiHead;
                }

            }
        }

    }
}
```

我们看到上面源码的最后一句，newTable[j+oldCap] = hiHead;意思就是哪怕我们的元素从旧的数组迁移到新的数组，我们也不需要重新计算他的hash和新数组长度相与的值，只需要直接将现在的索引值+原来数组的长度即可  // 将它和原来的长度进行相与，就是判断他的原来的hash的上一个  bit 位是否为 1。



**计算索引需要**

我们注意到上面的源代码中，判断扩容后元素位置需不需要改变的时候，我们使用到了这个判断

if((e.h & oldCap) == 0)，

如果为0，那么就不需要改变，使用旧的索引即可；如果为1，那么就需要使用新的索引

为啥会这样呢？

- **如果元素的索引要变那么** **hash&(newTable.length-1)****一定是和** **hash&(oldTable.length-1)+oldTable.length****相等**
- **因为table的长度一定是2的n次方，也就是oldCap 一定是2的n次方，也就是说 oldCap有且只有一位是1，而且这个位置在最高位；**

## **对比1.7扩容**

我们来对比JDK1.7 的方式，他如果要扩容，并且扩容后计算元素的索引的话要使用 indexFor函数



````java
/** 
     * Returns index for hash code h. 
     */  
    static int indexFor(int h, int length) {  
        // assert Integer.bitCount(length) == 1 : "length must be a non-zero power of 2";  
        return h & (length-1);  
    }  
````

也就是要把元素的hash值重新再和新的数组长度-1 再相与一次，会比较麻烦而且不优雅，完全没有我看到1.8计算方式的那种惊艳感。