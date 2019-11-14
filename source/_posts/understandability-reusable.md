---
title: understandability&reusable
date: 2019-05-14 16:13:09
tags: software Construction
categories: software Construction
---

# 面向可理解性的软件构造

> 关注internal quality中的understandability

## 衡量可理解性的指标

* 可读性：命名规范、注释 / 说明、内聚性、方法是否容易理解
* 命名：1.长度：自描述性+简洁、2.命名独特性比例
* 代码：1.代码复杂度、2.代码行数
* 注释：注释的密度等

## 注释

**四种注释**： Title and Introductory comments（位于类、方法等开头）、Block comments、 Single-Line / Trailing / End-Of-Line comments

* 代码显然的地方注释就不用再写注释了
* 应该描述做了什么，而不是怎么做

## 编码规范

* 命名、代码布局 / 缩进、数据声明方式、文件组织方式、 etc
* **包**的组织原则（了解）
  * 复用/发布等价原则：复用的粒度应等价于发布的粒度
  * 共同封闭原则： 一个包的变化将会影响包里所有的 类，而不会影响到其他的包
  * 共同复用原则：如果复用了其中一个类， 那么就应复用所有的类
  * 无圈依赖原则 (ADP)：不允许在包依赖图中出现任何圈 / 回路（通过加入新的包等方式解决）
  * 稳定依赖原则 (SDP)：被依赖者(应该更抽象)应更稳定于依赖者
  * 稳定抽象原则 (SAP)：一个包是稳定的，那么它就应该尽可能抽象

# 可复用性的衡量

> 探讨可复用的软件应该“长什么样”
> * 源代码级别的复用
> * 模块级别的复用：类 / 抽象类 / 接口
> * 库级别的复用： API/ 包
> * 系统级别的复用：框架

* 开发可复用软件可能成本更高，且性能降低，为了普适场景降低针对性
* 衡量可复用性：复用的频繁度 + 复用的代价

## 不同层面的复用

> 最主要的复用在**代码**层面，但软件构造的任何实体都可能被复用(需求、spec、数据、测试等)

* 复用的**类别**：
  * 白盒复用：源代码可见，<u>可修改和扩展</u>（ 但需要对其内部充分的了解）
  * 黑盒复用：源代码不可见，<u>不能修改</u>（只能通过 API 接口来使用，简单但适应性差）
* 复用的**层面**
  * 源代码层面复用：复制粘贴
  * 模块级别的复用：类 / 抽象类 / 接口，（复用类：继承、委托）
  * 库级别的复用： API/ 包
  * 系统级别的复用：框架---一组具体类、抽象类、及其之间的连接**关系**，调用开发者的代码
* **黑盒框架与白盒框架**
  * **白盒框架**：
    * 通过***继承和覆盖***方法进行扩展
    * 需要了解超类的实现
    * 通用设计模式：模板方式
  * **黑盒框架**：
    * 基于***委派/组合***的方式
    * 只需要了解接口
    * 通用设计模式：策略模式(根据需要调用某个模块)，观察者模式

## 面向复用的软件构造技术

> 如何设计可复用的类：继承与重写、重载、参数多态与泛型编程、行为子类型与 Liskov 替换原则、组合与委托

##  Liskov 替换原则

* 在可以使用 a 的场景，都可以用 c1 和 c2 代替而不会有任何问题（可以用子类替代父类）
`Animal a = new Animal();Animal c1 = new Cat();Cat c2 = new Cat();`
* 规则：更强的不变量、 更弱的前置条件、更强的后置条件
* LSP的一个定义是强行为子类型化
* LSP依赖于以下的限制：
  * 前置条件不能强化
  * 后置条件不能弱化
  * 不变量要保持
  * 子类型方法参数：逆变
  * 子类型方法的返回值：协变
  * 异常类型：协变
* **协变（Covariance）**：父类型 -> 子类型：越来越具体specific意味着**异常**和**返回值类型**不变或变得更具体
* **反协变、逆变（Contravariance）：**父类型 -> 子类型：越来越具体 specific 意味着**参数类型**要相反的变化，要不变或越来越抽象
* 注：子类**访问权限**不能比父类中被重写的方法的访问权限更低
* Java中的协变例子：数组

![Covariance](understandability-reusable\Covariance.JPG)

* 泛型中的LSP：
  * 泛型存在类型擦除（ List<String> is not a subtype of List&lt;Object>），运行时泛型类型都变成对应的原生类型
  * 编译器会先做静态检查（运行时由于擦除掉无法检查）来确定泛型类型是否对应来保证安全

![Type erasure](understandability-reusable\Type erasure.JPG)

* 泛型通配符 ‘ ? ’ (如List<?>)：比如当要写一个不依赖于类型参数的方法时(List<？>里面的元素都是object)
* List&lt;Number>是List<?>的子类型； List&lt;Number>是List<? extends Object>的子类型；List&lt;Object>是List<? super String>的子类型
* PECS(producer-extends(get方法安全,add不安全)、consumer-super(get方法不安全,add安全))

![wildcards](understandability-reusable\wildcards.JPG)

## Comparator与和Comparable

### Comparator&lt;T&gt;

为某个类写一个专门的比较器，实现 **Comparator** 接口并 override **compare()** 方法，通过传入**待比较的类和对应的比较器**给collection 或 array 的sort方法，

```java
public class EdgeComparator implements Comparator<Edge>{
Vertex s, t;
double weight;
    ...
@Override
public int compare(Edge o1, Edge o2){...}
    
public void sort(List<Edge> edges) {
   EdgeComparator comparator = new EdgeComparator();
   Collections.sort(edges, comparator);//注意要传入比较器才能比较
}
```

### Comparable&lt;T>

某个类实现了**Comparable**接口，然后 override **compareTo()** 方法，表示这个类本身就是可比较的，在数组或集合中排序时，直接传入这个类即可

> 与使用 Comparator 的区别：不需要构建新的 Comparator 类，比较代码放在 ADT 内部

```Java
public class Edge implements Comparable<Edge> {
Vertex s, t;
double weight;
...
@override
public int compareTo(Edge o) {...}
    
public void sort(List<Edge> edges) {
   Collections.sort(edges);
}
```

## 委派Delegation

*  委派 / 委托：一个对象请求另一个对象的功能
* Delegation vs Inheritance ：委派的耦合度更低，更灵活， 一个类不需要继承另一个类的全部方法的时候，通过委托机制调用部分方法
* “委托”发生在 object 层面，而“继承”发生在 class 层面
* 委派的分类：
  1. 关联-“**has-a**”：对象之间的长期静态关系，其中一个类会把另一个类作为成员一部分
  ![Association](understandability-reusable\Association.JPG)
  2. 依赖-“**uses-a**”：对象之间动态的临时的通信关系， 
  3.  组合-"**is_part_of**"：部分与整体关系，彼此不可分(心脏、肺与人)，所包含的部分在ADT内部自己创建
  4.  聚合-"**is_part_of**"：部分与整体关系，彼此可分，所包含的部分由外部创建，之间用外部的(传参)
  5. 继承-"**is a kind of**"：一般与特殊的关系
*  继承>>组合>>聚合>>关联>>依赖

![UML](understandability-reusable\UML.JPG)

# 面向复用的设计模式

> * structural patterns：Adapter、Decorator、Facade
> * behavioral patterns：Strategy、template method、iterator

## Structural Patterns

### Adaptor  ---  加个“适配器”以便于复用

比如某个LegacyRectangle类的display()方法只能用 “**x, y, w宽, h高**” 四个参数来打印矩形，但是现在client调用的shape类只能用**x1,y1,x2,y2**两个对角顶点来打印，接口不适配，需要加一个适配器rectangle

```Java
  interface Shape {
    void display(int x1, int y1, int x2, int y2);
  }
  class Rectangle implements Shape {
    void display(int x1, int y1, int x2, int y2) {
      new LegacyRectangle().display(x1, y1, x2 - x1, y2 - y1);
    }
  }
  class LegacyRectangle {
    void display(int x1, int y1, int w, int h) {...}
  }
  class Client {
    Shape shape = new Rectangle();
    public display() {
    shape.display(x1, y1, x2, y2);
    }
  }
```

![understandability-reusable\adaptor.JPG](understandability-reusable\adaptor.JPG)

### Decorator  ---  装饰器模式

* 当需要对对象进行动态的扩展组合的时候使用
* 方案：实现一个通用接口作为要扩展的对象，将主要功能委托给基础对象，然后添加功能，以递归的方式实现
* Java中decorator的例子：unmodifiableSet( Set<T> set)
* **不管怎么装饰，永远是属于原始 ADT的子类型**

```Java
public interface IceCream { // 顶层接口
    void AddTopping();
  }
  public class PlainIceCream implements IceCream { // 基础实现，无填加的冰激凌
    @Override
    public void AddTopping() {
    System.out.println("Plain IceCream ready for some toppings!");
    }
  }
  /* 装饰器基类 */
  public abstract class ToppingDecorator implements IceCream {
    protected IceCream input;

    public ToppingDecorator(IceCream i) {
      this.input = i;
    }

    public abstract void AddTopping(); // 留给具体装饰器实现
  }

  public class CandyTopping extends ToppingDecorator {
    public CandyTopping(IceCream i) {
      super(i);//调用父类的构造方法
    }

    public void AddTopping() {
      input.AddTopping(); // decorate others first
      System.out.println("Candy Topping added！");
    }
  }

```

![understandability-reusable\decorator.JPG](understandability-reusable\decorator.JPG)

## Façade 简单窗口

* 调用者需要一个简化的接口来调用复杂系统的整体功能

![facade](understandability-reusable\facade.JPG)

## Behavioral patterns

### Strategy  ---  策略模式

* 针对特定任务存在多种算法，调用者需要根据上下文环境动态的选择和切换
* 策略模式用**委托**机制实现不同完整算法的调用( 接口+ 多态)

![understandability-reusable\Strategy.JPG](understandability-reusable\Strategy.JPG)

### Template Method  ---  模板模式

* 做事情的步骤一样，但具体方法不同
* 方案： 共性的步骤在抽象类内公共实现，差异化的步骤在各个子类中实现
* 模板模式用**继承+ 重写**的方式实现算法的不同部分
  * 要提供一个统一的算法方法，final 的，按次序调用一系列代表算法步骤的 abstract 方法
  * 要提供一组 abstract 方法，分别代表算法的某个步骤

![understandability-reusable\Template .JPG](understandability-reusable\Template .JPG)

### Iterator  ---  一种面向迭代的策略模式

* 问题：客户端需要以统一 的、与元素类型无关的方式访问容器中的所有元素
* 注：一般把iterator迭代器写成内部类

```Java
public interface Iterator<E> {//Iterator和Iterable的接口
    boolean hasNext();
    E next();
    void remove()
  }
  public interface Iterable<T> {Iterator<T> iterator(); // Getting an Iterator
}
```

![understandability-reusable\iterator.JPG](understandability-reusable\iterator.JPG)

```Java
public class Pair<E> implements Iterable<E>{
   @override 
   public Iterator<E> iterator() {
    return new PairIterator();
   }
   private class PairIterator implements Iterator<E>{
       @Override 
       hasNext()
       @Override 
       next()}
}
```

