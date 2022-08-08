---
title: 【hashcode】  
author: Maid  
date: 2020-12-20 17:05  
categories: 散列表  
tags: [散列表]  [hashcode]

---
<details><summary>目录</summary>

- [HashMap](#hashmap)
  - [HashMap 的数据结构](#hashmap-的数据结构)
  - [HashMap 的工作原理](#hashmap-的工作原理)
  - [HashMap中put方法的过程](#hashmap中put方法的过程)
  - [HashMap的容量](#hashmap的容量)
    - [table数组的容量](#table数组的容量)
    - [链表/红黑树的容量](#链表红黑树的容量)
  - [hash 的实现](#hash-的实现)
    - [为什么使用异或运算符？](#为什么使用异或运算符)
- [解决hash冲突](#解决hash冲突)
  - [解决Hash冲突的三种办法](#解决hash冲突的三种办法)
  - [链地址法和开放地址法的优缺点](#链地址法和开放地址法的优缺点)
    - [开放地址法：](#开放地址法)
    - [链地址法：](#链地址法)
  - [ThreadLocalMap 采用开放地址法原因](#threadlocalmap-采用开放地址法原因)
- [红黑树](#红黑树)
  - [红黑树的特点](#红黑树的特点)
  - [拉链法导致的链表过深问题为什么不用二叉查找树代替，而选择红黑树？为什么不一直使用红黑树](#拉链法导致的链表过深问题为什么不用二叉查找树代替而选择红黑树为什么不一直使用红黑树)
- [jdk8中对HashMap做了哪些改变](#jdk8中对hashmap做了哪些改变)
- [HashMap与其他集合数据类型比较](#hashmap与其他集合数据类型比较)
  - [HashMap，LinkedHashMap，TreeMap 有什么区别](#hashmaplinkedhashmaptreemap-有什么区别)
  - [HashMap & TreeMap & LinkedHashMap 使用场景](#hashmap--treemap--linkedhashmap-使用场景)
  - [HashMap 和 HashTable 有什么区别](#hashmap-和-hashtable-有什么区别)
  - [Java 中的另一个线程安全的与 HashMap 极其类似的类是什么？同样是线程安全，它与 HashTable 在线程同步上有什么不同](#java-中的另一个线程安全的与-hashmap-极其类似的类是什么同样是线程安全它与-hashtable-在线程同步上有什么不同)
  - [HashMap & ConcurrentHashMap 的区别](#hashmap--concurrenthashmap-的区别)
  - [为什么 ConcurrentHashMap 比 HashTable 效率要高](#为什么-concurrenthashmap-比-hashtable-效率要高)
  - [针对 ConcurrentHashMap 锁机制具体分析（JDK 1.7 VS JDK 1.8）](#针对-concurrenthashmap-锁机制具体分析jdk-17-vs-jdk-18)
  - [ConcurrentHashMap 在 JDK 1.8 中，为什么要使用内置锁 synchronized 来代替重入锁 ReentrantLock](#concurrenthashmap-在-jdk-18-中为什么要使用内置锁-synchronized-来代替重入锁-reentrantlock)
  - [ConcurrentHashMap 简单介绍](#concurrenthashmap-简单介绍)
    - [重要的常量：](#重要的常量)
    - [数据结构：](#数据结构)
    - [存储对象时（put() 方法）：](#存储对象时put-方法)
    - [扩容方法](#扩容方法)
    - [获取对象时（get()方法）：](#获取对象时get方法)
    - [ConcurrentHashMap 的并发度是什么](#concurrenthashmap-的并发度是什么)

</details>

---
# HashMap
## HashMap 的数据结构
A：哈希表结构（链表散列：数组+链表）实现，结合数组和链表的优点。当链表长度超过 8 时，链表转换为红黑树。
```java
transient Node<K,V>\[\] table;
```
## HashMap 的工作原理
HashMap 底层是 hash 数组和单向链表实现，数组中的每个元素都是链表，由 Node 内部类（实现 Map.Entry接口）实现，HashMap 通过 put & get 方法存储和获取。  
存储对象时，将 K/V 键值传给 put() 方法：

1. 调用 hash(K) 方法计算 K 的 hash 值，然后结合数组长度，计算得数组下标；
2. 调整数组大小（当容器中的元素个数大于 capacity * loadfactor 时，容器会进行扩容resize 为 2n）；
3. 
   - 如果 K 的 hash 值在 HashMap 中不存在，则执行插入，若存在，则发生碰撞；
   - 如果 K 的 hash 值在 HashMap 中存在，且它们两者 equals 返回 true，则更新键值对；
   - 如果 K 的 hash 值在 HashMap 中存在，且它们两者 equals 返回 false，则插入链表的尾部（尾插法）或者红黑树中（树的添加方式）。

> （JDK 1.7 之前使用头插法、JDK 1.8 使用尾插法）（注意：当碰撞导致链表大于 TREEIFY_THRESHOLD = 8 时，就把链表转换成红黑树）

获取对象时，将 K 传给 get() 方法：
1. 调用 hash(K) 方法（计算 K 的 hash 值）从而获取该键值所在链表的数组下标；
2. 顺序遍历链表，equals()方法查找相同 Node 链表中 K 值对应的 V 值。  

hashCode 是`定位`的，存储位置；equals是`定性`的，比较两者是否相等。

## HashMap中put方法的过程
调用哈希函数获取Key对应的hash值，再计算其数组下标；  
如果没有出现哈希冲突，则直接放入数组；如果出现哈希冲突，则以链表的方式放在链表后面；  
如果链表长度超过阀值( TREEIFY THRESHOLD==8)，就把链表转成红黑树，链表长度低于6，就把红黑树转回链表;   
如果结点的key已经存在，则替换其value即可；  
如果集合中的键值对大于12，调用resize方法进行数组扩容。”  

## HashMap的容量
hHashMap的容量分为table数组的容量和链表/红黑树的容量  
### table数组的容量
1. table 数组大小是由 capacity 这个参数确定的，默认是16，也可以构造时传入，最大限制是1<<30；
2. loadFactor 是装载因子，主要目的是用来确认table 数组是否需要动态扩展，默认值是0.75，比如table 数组大小为 16，装载因子为 0.75 时，threshold 就是12，当 table 的实际大小超过 12 时，table就需要动态扩容；
3. 扩容时，调用 resize() 方法，将 table 长度变为原来的两倍（注意是 table 长度，而不是 threshold）。数组扩容的过程：创建一个新的数组，其容量为旧数组的两倍，并重新计算旧数组中结点的存储位置。结点在新数组中的位置只有两种，原下标位置或原下标+旧数组的大小。
4. 如果数据很大的情况下，扩展时将会带来性能的损失，在性能要求很高的地方，这种损失很可能很致命。

### 链表/红黑树的容量
如果链表长度超过阀值( TREEIFY THRESHOLD==8)，就把链表转成红黑树，链表长度低于6，就把红黑树转回链表;   


## hash 的实现
JDK 1.8 中，是通过 `hashCode()` 的高 16 位异或低 16 位实现的：`(h = k.hashCode()) ^ (h >>> 16)`，主要是从速度，功效和质量来考虑的，减少系统的开销，而且每一位都参与了运算也不会造成因为高位没有参与下标的计算，从而引起的碰撞。
而且沿着

### 为什么使用异或运算符？
保证了对象的 hashCode 的 32 位值只要有一位发生改变，整个 hash() 返回值就会改变。尽可能的减少碰撞。

# 解决hash冲突
## 解决Hash冲突的三种办法
- 链表法: 是将相同hash值的对象组成一个链表放在hash值对应的槽位； 
- 开放定址法: 解决冲突的做法是：当冲突发生时，使用某种探查(亦称探测)技术在散列表中形成一个探查(测)序列。
沿此序列逐个单元地查找，直到找到给定的关键字，或者碰到一个开放的地址(即该地址单元为空)为止（若要插入，在探查到开放的地址，则可将待插入的新结点存人该地址单元）。 （`ThreadLocalMap`）

- 拉链法: 解决冲突的做法是： 将所有关键字为同义词的结点链接在同一个单链表中。若选定的散列表长度为m，则可将散列表定义为一个由m个头指针组成的指针数组T[0..m-1]。凡是散列地址为i的结点，均插入到以T[i]为头指针的单链表中。T中各分量的初值均应为空指针。在拉链法中，装填因子α可以大于1，但一般均取α≤1。拉链法适合未规定元素的大小。 (`HashMap`)

## 链地址法和开放地址法的优缺点

### 开放地址法：
1. 容易产生堆积问题，不适于大规模的数据存储。
2. 散列函数的设计对冲突会有很大的影响，插入时可能会出现多次冲突的现象。
3. 删除的元素是多个冲突元素中的一个，需要对后面的元素作处理，实现较复杂。

### 链地址法：
1. 处理冲突简单，且无堆积现象，平均查找长度短。
2. 链表中的结点是动态申请的，适合构造表不能确定长度的情况。
3. 删除结点的操作易于实现。只要简单地删去链表上相应的结点即可。
4. 指针需要额外的空间，故当结点规模较小时，开放定址法较为节省空间。

## ThreadLocalMap 采用开放地址法原因

1. ThreadLocal 中看到一个属性 HASH_INCREMENT = 0x61c88647 ，0x61c88647 是一个神奇的数字，让哈希码能均匀的分布在2的N次方的数组里, 即 Entry[] table，关于这个神奇的数字google 有很多解析，这里就不重复说了
2. ThreadLocal 往往存放的数据量不会特别大（而且key 是弱引用又会被垃圾回收，及时让数据量更小），这个时候开放地址法简单的结构会显得更省空间，同时数组的查询效率也是非常高，加上第一点的保障，冲突概率也低


# 红黑树
## 红黑树的特点
- 每个节点非红即黑
- 根节点总是黑色的
- 如果节点是红色的，则它的子节点必须是黑色的（反之不一定）
- 每个叶子节点都是黑色的空节点（NIL节点）
- 从根节点到叶节点或空子节点的每条路径，必须包含相同数目的黑色节点（即相同的黑色高度）

## 拉链法导致的链表过深问题为什么不用二叉查找树代替，而选择红黑树？为什么不一直使用红黑树
之所以选择红黑树是为了解决二叉查找树的缺陷，二叉查找树在特殊情况下会变成一条线性结构（这就跟原来使用链表结构一样了，造成很深的问题），遍历查找会非常慢。推荐：面试问红黑树，我脸都绿了。

而红黑树在插入新数据后可能需要通过左旋，右旋、变色这些操作来保持平衡，引入红黑树就是为了查找数据快，解决链表查询深度的问题，我们知道红黑树属于平衡二叉树，但是为了保持“平衡”是需要付出代价的，但是该代价所损耗的资源要比遍历线性链表要少，所以当长度大于8的时候，会使用红黑树，如果链表长度很短的话，根本不需要引入红黑树，引入反而会慢。

# jdk8中对HashMap做了哪些改变
在java 1.8中，如果链表的长度超过了8，那么链表将转换为红黑树。（桶的数量必须大于64，小于64的时候只会扩容）  
发生hash碰撞时，java 1.7 会在链表的头部插入，而java 1.8会在链表的尾部插入  
在java 1.8中，Entry被Node替代(换了一个马甲。  


# HashMap与其他集合数据类型比较
## HashMap，LinkedHashMap，TreeMap 有什么区别
HashMap 参考其他问题；
LinkedHashMap 保存了记录的插入顺序，在用 Iterator 遍历时，先取到的记录肯定是先插入的；遍历比 HashMap 慢；
TreeMap 实现 SortMap 接口，能够把它保存的记录根据键排序（默认按键值升序排序，也可以指定排序的比较器）

## HashMap & TreeMap & LinkedHashMap 使用场景
一般情况下，使用最多的是 HashMap。
HashMap：在 Map 中插入、删除和定位元素时；

TreeMap：在需要按自然顺序或自定义顺序遍历键的情况下；

LinkedHashMap：在需要输出的顺序和输入的顺序相同的情况下。

## HashMap 和 HashTable 有什么区别
1. HashMap 是线程不安全的，HashTable 是线程安全的；
2. 由于线程安全，所以 HashTable 的效率比不上 HashMap；
3. HashMap最多只允许一条记录的键为null，允许多条记录的值为null，而 HashTable不允许；
4. HashMap 默认初始化数组的大小为16，HashTable 为 11，前者扩容时，扩大两倍，后者扩大两倍+1；
5. HashMap 需要重新计算 hash 值，而 HashTable 直接使用对象的 hashCode

## Java 中的另一个线程安全的与 HashMap 极其类似的类是什么？同样是线程安全，它与 HashTable 在线程同步上有什么不同
ConcurrentHashMap 类（是 Java并发包 java.util.concurrent 中提供的一个线程安全且高效的 HashMap 实现）。

HashTable 是使用 synchronize 关键字加锁的原理（就是对对象加锁）；

而针对 ConcurrentHashMap，在 JDK 1.7 中采用 分段锁的方式；JDK 1.8 中直接采用了CAS（无锁算法）+ synchronized。

## HashMap & ConcurrentHashMap 的区别
除了加锁，原理上无太大区别。另外，HashMap 的键值对允许有null，但是ConCurrentHashMap 都不允许。

## 为什么 ConcurrentHashMap 比 HashTable 效率要高
HashTable 使用一把锁（锁住整个链表结构）处理并发问题，多个线程竞争一把锁，容易阻塞；
ConcurrentHashMap
JDK 1.7 中使用分段锁（ReentrantLock + Segment + HashEntry），相当于把一个 HashMap 分成多个段，每段分配一把锁，这样支持多线程访问。锁粒度：基于 Segment，包含多个 HashEntry。
JDK 1.8 中使用 CAS + synchronized + Node + 红黑树。锁粒度：Node（首结
点）（实现 Map.Entry）。锁粒度降低了。

## 针对 ConcurrentHashMap 锁机制具体分析（JDK 1.7 VS JDK 1.8）
JDK 1.7 中，采用分段锁的机制，实现并发的更新操作，底层采用数组+链表的存储结构，包括两个核心静态内部类 Segment 和 HashEntry。
1. Segment 继承 ReentrantLock（重入锁） 用来充当锁的角色，每个 Segment 对象守护每个散列映射表的若干个桶；
2. HashEntry 用来封装映射表的键-值对；
3. 每个桶是由若干个 HashEntry 对象链接起来的链表

JDK 1.8 中，采用Node + CAS + Synchronized来保证并发安全。取消类 Segment，直接用 table 数组存储键值对；当 HashEntry 对象组成的链表长度超过 TREEIFY_THRESHOLD 时，链表转换为红黑树，提升性能。底层变更为数组 + 链表 + 红黑树。

## ConcurrentHashMap 在 JDK 1.8 中，为什么要使用内置锁 synchronized 来代替重入锁 ReentrantLock
1. 粒度降低了；
2. JVM 开发团队没有放弃 synchronized，而且基于 JVM 的 synchronized 优化空间更大，更加自然。
3. 在大量的数据操作下，对于 JVM 的内存压力，基于 API 的 ReentrantLock 会开销更多的内存。
   
## ConcurrentHashMap 简单介绍
### 重要的常量：
```java
private transient volatile int sizeCtl;
```
- 当为负数时，-1 表示正在初始化，-N 表示 N - 1 个线程正在进行扩容；
- 当为 0 时，表示 table 还没有初始化；
- 当为其他正数时，表示初始化或者下一次进行扩容的大小。

### 数据结构：
- Node 是存储结构的基本单元，继承 HashMap 中的 Entry，用于存储数据；
- TreeNode 继承 Node，但是数据结构换成了二叉树结构，是红黑树的存储结构，用于红黑树中存储数据；
- TreeBin 是封装 TreeNode 的容器，提供转换红黑树的一些条件和锁的控制。

### 存储对象时（put() 方法）：

- 如果没有初始化，就调用 initTable() 方法来进行初始化；  
- 如果没有 hash 冲突就直接 CAS 无锁插入；  
- 如果需要扩容，就先进行扩容；  
- 如果存在 hash 冲突，就加锁来保证线程安全，两种情况：一种是链表形式就直接遍历到尾端插入，一种是红黑树就按照红黑树结构插入；
- 如果该链表的数量大于阀值 8，就要先转换成红黑树的结构，break 再一次进入循环
- 如果添加成功就调用 addCount() 方法统计 size，并且检查是否需要扩容。

### 扩容方法 
- transfer()：默认容量为 16，扩容时，容量变为原来的两倍。
- helpTransfer()：调用多个工作线程一起帮助进行扩容，这样的效率就会更高。

### 获取对象时（get()方法）：

计算 hash 值，定位到该 table 索引位置，如果是首结点符合就返回；  
如果遇到扩容时，会调用标记正在扩容结点 ForwardingNode.find()方法，查找该结点，匹配就返回；  
以上都不符合的话，就往下遍历结点，匹配就返回，否则最后就返回 null。

### ConcurrentHashMap 的并发度是什么
程序运行时能够同时更新 ConccurentHashMap 且不产生锁竞争的最大线程数。默认为 16，且可以在构造函数中设置。  
当用户设置并发度时，ConcurrentHashMap 会使用大于等于该值的最小2幂指数作为实际并发度（假如用户设置并发度为17，实际并发度则为32）


