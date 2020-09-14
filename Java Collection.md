>  摘录自公众号【码农田小齐】，用于自己CS记录和学习的，如有侵权，请联系我哟~

<img src="https://i.loli.net/2020/09/05/R9BW1jKlLYO6nHC.png" alt="img" style="zoom: 67%;" />

**Java集合，也称作容器，主要是由两大接口（Interface）派生出来的：**

- Collection 存放单一元素
- Map 存放key-value键值对

#### Collection

定义很多了方法，并也会继承到各个子接口和实现类里；操作集合——增删改查`CRUD`

> Create, Read, Update, Delete

| 功能 |                       方法                        |
| :--: | :-----------------------------------------------: |
|  增  |   add(E e) / addAll(Collection<? extends E> c)    |
|  删  |   remove(Object o) / removeAll(Collection<?> c)   |
|  改  |    Collection Interface没有,增和删就可以完成了    |
|  查  | contains(Object o) / containsAll(Collection<?> c) |
| 其他 |          isEmpty() / size() / toArray()           |

* `add()` 方法传入的数据类型必须是 Object，所以当写入基本数据类型的时候，会做自动装箱 auto-boxing 和自动拆箱 unboxing。

#### List（有序、可重复）

ArrayList底层是数组实现，LinkedList是链表，因此两者在增删查改操作上效率会不一样。

而数组和链表的最大区别就是**数组是可以随机访问的（random access）**。因此在【改查】功能上，ArrayList效率更高。

至于【增删】，数组由于物理上的连续性，需要移动后续的元素，在尾部还好； 但链表可以直接断开和下个元素的连接，直接插入新元素或者移除旧元素。

因此：

- 改查选择 ArrayList；
- 增删在尾部的选择 ArrayList；
- 其他情况下，如果时间复杂度一样，推荐选择 ArrayList，因为 overhead 更小，或者说内存使用更有效率。

| 功能 |          方法          | ArrayList | LinkedList |
| :--- | :--------------------: | :-------: | :--------: |
| 增   | add(E e)  - 尾部加元素 |   O(1)    |    O(1)    |
| 增   |  add(int index, E e)   |   O(n)    |    O(n)    |
| 删   |   remove(int index)    |   O(n)    |    O(n)    |
| 删   |      remove(E e)       |   O(n)    |    O(n)    |
| 改   |  set(int index, E e)   |   O(1)    |    O(n)    |
| 查   |     get(int index)     |   O(1)    |    O(n)    |

##### Vector

Vector 和 ArrayList 一样，也是继承自 java.util.AbstractList，底层也是用数组来实现的。

但是现在已经被弃用了，因为它加了太多的 synchronized！

任何好处都是有代价的，线程安全的成本就是效率低，在某些系统里很容易成为瓶颈，所以现在大家不再在数据结构的层面加 synchronized，而是把这个任务转移给程序员==

##### Vector 和 ArrayList 的区别是什么？

- Vectors are synchronized, ArrayLists are not;
  - 线程安全：如果多线程同步访问ArrayList，我们需要在外部synchronize修改list的代码块

- Data Growth Methods
  - Vector默认两倍扩容，ArrayList扩容1.5倍

#### Queue & Deque

Java 中的 这个 Queue 接口稍微有点坑，一般来说队列的语义都是**先进先出**（FIFO）的。

但是这里有个例外，就是 PriorityQueue，也叫 **heap**，并不按照进去的时间顺序出来，而是按照规定的优先级出去，并且它的操作并不是 O(1) 的，时间复杂度的计算稍微有点复杂，后面补充。

##### Queue

Queue有两组API：

| 功能 |  抛异常   |                       返回值                       |
| :--- | :-------: | :------------------------------------------------: |
| 增   |  add(e)   | offer(e) -当达到最大容量且不会扩容时，return false |
| 删   | remove()  |           poll() - 队列空了，return null           |
| 瞧   | element() |           peek() - 队列空了，return null           |

##### Deque

Deque 是两端都可以进出的，那自然是有针对 First 端的操作和对 Last 端的操作，那每端都有两组，一组抛异常，一组返回特殊值：  

| 功能 |           抛异常            |           返回值            |
| :--- | :-------------------------: | :-------------------------: |
| 增   |   addFirst(e)/ addLast(e)   | offerFirst(e)/ offerLast(e) |
| 删   | removeFirst()/ removeLast() |   pollFirst()/ pollLast()   |
| 瞧   |    getFirst()/ getLast()    |   peekFirst()/ peekLast()   |

以上：

- 用的时候用同一组API，前后要统一     
- Queue 和 Deque 的这些 API 都是 $O(1)$的时间复杂度，准确来说是均摊时间复杂度。

实现类：LinkedList、ArrayDeque、PriorityQueue

- 如果想实现「普通队列 - 先进先出」的语义，就使用 LinkedList 或者 ArrayDeque 来实现；
- 如果想实现「优先队列」的语义，就使用 PriorityQueue；
- 如果想实现「栈」的语义，就使用 ArrayDeque。

📕 实现普通队列，如何选择用LinkedList还是ArrayDeque？

Linkesd structures are possibly the worst structure to iterate with a cache miss on each element. On top of it they consume way more memory.

> **cache miss：** 内存是一块一块读取的，满了就替换，链表访问特性是不连续的，所以链表大概率读进去的块有效的很少，因此会常发生缓存不命中

如果

推荐使用ArrayDeque,因为效率更高，LinkedList会有其他额外的开销（overhead）

📕 那 ArrayDeque 和 LinkedList 的区别有哪些呢？

1. ArrayDeque 是一个可扩容的数组，LinkedList 是链表结构；
2. ArrayDeque 里不可以存 null 值，但是 LinkedList 可以；
3. ArrayDeque 在操作头尾端的增删操作时更高效，但是 LinkedList 只有在当要移除中间某个元素且已经找到了这个元素后的移除才是 O(1) 的；
4. ArrayDeque 在内存使用方面更高效。

所以，只要不是必须要存 null 值，就选择 ArrayDeque 吧！

##### heap

堆是一种特殊的队列——PriorityQueue，详情参见【Java Heap】

#### Set（无序、不重复）

Set 的常用实现类有三个：

**HashSet**: 采用 Hashmap 的 key 来储存元素，主要特点是无序的，基本操作都是 O(1) 的时间复杂度，很快。

**LinkedHashSet**: 这个是一个 HashSet + LinkedList 的结构，特点就是既拥有了 O(1) 的时间复杂度，又能够保留插入的顺序。

**TreeSet**: 采用红黑树结构，特点是可以有序，可以用自然排序或者自定义比较器来排序；缺点就是查询速度没有 HashSet 快。

那每个 Set 的**底层实现**其实就是对应的 Map：

**数值放在 map 中的 key 上，value 上放了个 PRESENT，是一个静态的 Object，相当于 place holder，每个 key 都指向这个 object。**