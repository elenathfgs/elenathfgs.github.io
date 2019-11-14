---
title: GC8-Thread10
date: 2019-06-19 20:46:36
tags: software Construction
categories: software Construction
---

# 内存管理与垃圾回收

* 基于**堆和栈的内存分配都是动态内存分配**
* static内存分配：在编译时就确定所有对象的分配，运行时无法改变

## JVM的内存分配

![(GC8-Thread10\JVMmemory.JPG)](GC8-Thread10\JVMmemory.JPG)

* 所有**局部的基本数据类型**都在**栈**上创建
* 所有**对象**都在**堆**上创建
*  **多线程之间**传递数据，是通过**复制而非引用**，如果两个线程调用同一个对象上的某个方法，它们分别保留该方法的局部变量的拷贝

![JVMmemory2](GC8-Thread10\JVMmemory2.JPG)

## 垃圾回收

从 root 对象开始进行有向图的搜索，将图分为 root 可达部分和 root 不可达部分，后者将被进行内存回收

## 垃圾回收的算法!

### 1.引用计数 --- Reference counting

基本思想：为每个object存储一个**计数器count**，当有一个**新的reference指向它的时候，则count++**，当其他的reference和它断开的时候则count--，当**count==0的时候就回收这个object**（不过还得考虑这个obj还有指向其他obj的情况，只有它指向的obj的count都为0（都被回收了）才能回收这个）

优点：简单，计算分散，“幽灵时间最短-->0”(count的计算)
缺点：不全面（会漏掉循环引用的对象）、占用额外内存空间

### 2.标记 - 清除 --- Mark-Sweep

基本思想：
*  mark 阶段：从根出发，能到达的标记为live，为每个 object 设定状态位 (live/dead) 并记录
* sweep阶段：将标记为 dead 的对象进行清理

优点：略
缺点：要停止程序执行来 Mark 和 Sweep ，导致**幽灵时间过长**，也**降低程序本身的性能**，内存也碎片化

### 3.标记 - 整理 --- Mark-Compact

在标记清理后将有标记的alive的对象放在一起

![](GC8-Thread10\mark-conpact.JPG)

优点：避免碎片化
缺点：时间消耗太长

### 4.复制 --- Copying

与 mark-compact 的区别在于：不是在同一个区域内进行整理，而是将 live 对象全部复制到另一个区域

优点：很好地解决碎片化问题
缺点：占用空间（但是只需要两倍空间区域即可（换着用））、耗时间、活得长的大对象可能会多次复制

## JVM中的垃圾回收

> Java GC 将堆分为不同的区域，各区域采用不同的 GC 策略，以提高 GC 的效率

![JVMheap](GC8-Thread10\JVMheap.JPG)

* 针对**年轻代**：只有一小部分对象可较长时间存活，故采用 copy 算法减少 GC 代价，满了则使用 minor GC
* 针对**年老代**：这里的对象有很高的幸存度，使用 Mark-Sweep 或 Mark-Compact 算法， old generation 满了，则启动 full GC（perm也是）
* 只有当某个区域不能再为对象分配内存时（满），才启动 GC

### JVM中GC的参数设置

1. 初始和最大的 heap 尺寸： Java –Xms 1024M –Xmx 2048M
2. 设置 young generation 的尺寸：-XX: NewSize=&lt;n&gt;[g|m|k]； -XX: MaxNewSize=&lt;n>[g|m|k]
3. old generation 的尺寸不需要设置，根据其他各参数的取值可计算得到（注，old的最大尺寸为堆最大 - young最大）

# 代码调优的设计模式

## 单例模式 --- Singleton Pattern

* 只创建**一个对象**，然后**复用**：确保类只有一个实例，并提供一个全局访问点
* 类负责管理其唯一实例

```Java
public class Singleton {
  private static Singleton instance = new Singleton();//已经创建好
  private Singleton() {…} //clients can’t access the constructor
  public static Singleton getInstance() {
    return instance;
  }
  /**这样可以节约空间 --》需要时再创建实例
  public static Singleton getInstance() {
    if (instance == null)
      instance = new Singleton();
    return instance;
  }
  */
  // other operations and data
}
//Client code
Singleton s = Singleton.getInstance();
```

* 如：  对于系统的核心组件和经常使用的对象

## 享元模式 --- Flyweight Pattern

> 应用程序使用 了 大量的对象 ，存储成本很高，但是对象的大多数状态是外部状态， 一 旦删除了外部状态，可以用相对较少的共享对象替代

* flyweight 是一个共享对象，可在多个情境下共享使用
  * 内在状态：不变的状态，可以共享，保存下来
  * 外在状态：状态是不固定的，使用时需要计算，不可共享

![GC8-Thread10\flyweight.JPG](GC8-Thread10\flyweight.JPG)

```Java
  public class AlienFactory {
    private Map<String, Alien> alienMap = new HashMap<>();

    public void saveAlien(String index, Alien alien) {
      alienMap.put(index, alien);
    }

    public Alien getAlien(String index) {
      return alienMap.get(index);
    }
  }

  public class LittleAlien implements Alien {
    private String shape = "Little Shape"; // intrinsic state

    public String getShape() {
      return shape;
    }

    public Color getColor(int madLevel) {// extrinsic state
      if (madLevel == 0)
        return Color.Red;
      else if (madLevel == 1)
        return Color.Blue;
      else
        return Color.Green;
    }
  }

AlienFactory factory = new AlienFactory();
factory.saveAlien("LargeAlien", new LargeAlien());
factory.saveAlien("LittleAlien", new LittleAlien());

Alien a = factory.getAlien("LargeAlien");
System.out.println("Alien of type LargeAlien is " + a.getShape());
System.out.println("Alien of type LargeAlien is " + a.getColor(0).toString());
```

## 原型模式 --- Prototype Pattern

* 通过复制已有原型对象新创建对象
* 目标：**创建或初始化对象代价高时**，可通过此模式创建相似对象，降低开销

![GC8-Thread10\protoType.JPG](GC8-Thread10\protoType.JPG)

需要指定的对象时，从manager的list里面取需要的对象，调用该对象的clone方法得到一个新的同样的对象（最好是**重写的deep copy**）

## 对象池模式 --- Object Pool Pattern

* 重复使用，避免重新创建
* 场景：对象被频繁创建、使用，然后销毁。每个**对象的生命期都很短**。**同一时刻内，系统中存在的对象数也比较小**

# 并发

* 进程：拥有独立的（虚拟）内存空间，重量级
* 线程：共享进程的内存空间，是进程的一个执行单元，需要同步和锁机制

![GC8-Thread10\thread.JPG](GC8-Thread10\thread.JPG)

## 竞争条件

数据共享：作用于同一个 mutable 数据上的多个线程，彼此之间存在对该数据的访问竞争并导致 interleaving 

# 线程安全

## 线程安全策略

1. Confinement  -- 限制数据共享
2. Immutability -- 共享不可变数据
3. Threadsafe data type -- 共享线程安全的可变数据
4. Synchronization -- 同步机制：通过锁的机制共享线程不安全的可变数据，变并行为串行

### Confinement

核心思想：线程之间不共享数据

### Immutability

使用不可变数据类型和不可变引用，避免多线程之间的 race condition
相比于Confinement， Immutability 允许有全局 rep ，但是只能是 immutable 的

### Threadsafe data type

集合类一般都是线程不安全的，可以用decorator：
`private static Map<Integer,Boolean> cache = Collections.synchronizedMap(new HashMap<>());`

### Synchronization 

* 任何共享的 mutable 变量 / 对象必须被 lock 所保护
* monitor pattern 中， ADT 所有方法都被同一个 synchronized(this) 所保护

### 死锁

```Java
T1: synchronized(a){ synchronized(b){ … } }
T2: synchronized(b){ synchronized(a){ … } }
```

解决方法：
1.lock ordering：

![GC8-Thread10\order.JPG](GC8-Thread10\order.JPG)

2.coarser locking  --- 用一个新的锁来锁住可能有死锁的地方

![locking](GC8-Thread10\coarser locking.JPG)

## 注释线程安全策略

![GC8-Thread10\thread safe.JPG](GC8-Thread10\thread safe.JPG)

