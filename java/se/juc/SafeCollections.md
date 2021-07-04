# 线程安全集合类概述

## 线程安全的集合分为`三`大类
### （1）遗留的线程安全集合
#### 比如 HashTable、Vector。HashTable就是在HashMap的基础上加了Synchronized，Vector也是在List的基础上加了Synchronized。因为效率不高，所以被废弃了。
### （2）使用Collection修饰的线程安全集合
- Collections.synchronizedCollection
- Collections.synchronizedList
- Collections.synchronizedMap
- Collections.synchronizedSet
- Collections.synchronizedNavigableMap
- Collections.synchronizedNavigableSet
- Collections.synchronizedSortMap
- Collections.synchronizedSortSet

以 `synchronizedMap`为例
```java
public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m) {
    return new SynchronizedMap<>(m);
}
```
synchronizedMap它实现了Map接口，并重写了对应的get、set等方法（装饰器模式），虽然说是重写，但是99%都是一样的，1%不同点在于在get、set等方法会加锁控制。但是区别于HashTable把方法锁住不同，synchronizedMap的锁粒度更小，它只锁对象。在锁保护的对象里面，get、set等方法还是操作原来的Map。
![](/others/pictures/SynchronizedMap.jpg)
<font color=red>本质</font>上与HashTable没啥区别，只是降低了锁粒度，性能还无法达到更高的层次
### （3）<font color=red>★</font>JUC包下面的集合
- Blocking类
- CopyOnWrite类
- Concurrent类

### Blocking类
- Blocking类大部分实现基于`锁`，并提供用来阻塞的方法
  - 条件不满足时阻塞住，比如`ReentrantLock`
  - 典型例子就是阻塞队列。当队列为空时，调用take方法；队列满时，调用put方法，需要阻塞线程等待
- CopyOnWrite类用于`读多写少`的场景
  - 修改时拷贝
- Concurrent类【建议采用】
  - 很多操作基于cas，可以提供较高的吞吐量，性能较高
  - 采用多把锁，提高并发度
  - 弱一致性（缺点）
    - 遍历时。当利用迭代器进行遍历时，如果容器发生了修改，迭代器仍可以继续进行遍历，这时的内容是旧的。这样是不会报错，称为(`fail-save`)
    - 读取时。由上可知，由于内容不是最新的，那么读取到的很可能是旧值。
    - 对于`非安全`的容器来说，遍历时发生了修改，将会直接报错（`ConcurrentModificationException`），称为(`fail-fast`)。

## 线程安全的集合类使用注意事项
- 单步操作是线程安全的，但是`组合操作线程不安全`
  - 例如以下代码。get、put是线程安全的，但是组合后整体代码是线程不安全的。
```java
// 错误代码
Map map = new ConcurrentHashMap<>();
Object o = map.get(k);
// do ...
map.put(k, o);

// 解决方法一：synchronized【不推荐】
Map map = new ConcurrentHashMap<>();
synchronized(map) {
  Object o = map.get(k);
  // do ...
  map.put(k, o);
}

// 解决方法二：computeIfAbsent 如果缺少k，则生成v，并插入，这三个操作是原子性的。内部也是采用了sync加了锁，但不是锁住了map而是锁住了链表头，详见源码
Map map = new ConcurrentHashMap<>();
map.computeIfAbsent(k, (...vars)-> {
    // do sth produce v
    // return v
  });
```