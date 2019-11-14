---
title: ADT&OOP
date: 2019-05-10 15:45:22
tags: software Construction
categories: software Construction
---

> 部分内容摘录自哈工大软件构造讲义
>

# 数据类型和类型检验

---

## 编程语言中的数据类型

* 数据类型：值的集合和对这些值进行的操作
* 变量：用特定数据类型定义，可存储满足类型约束的值
* Java中的数据类型：
  * 基本数据类型(栈中)：int, long, boolean, double, char
  * 对象数据类型(堆中)：String, BigInteger等

![JAVAdataType](ADT-OOP\JAVAdataType.JPG)

* JAVA中所有的对象类型的根都是Object类，**除了object类以外**的类都有一个父类
* 基本类型都有包装类，一般只在定义集合时用，可以自动转换

## 静态vs动态数据类型 && 类型检查

**Java是静态类型语言** -> 在编译阶段进行类型检查（静态检查），可以提早发现bug
动态类型语言 -> 在运行阶段进行类型检查（动态检查）

* 静态检查范围 (关于“类型”的检查，不考虑值)：语法错误、类名/函数名错误、参数数目错误、参数类型错误、返回值类型错误
* 动态检查范围 (关于“值”的检查)：非法参数值(x/y中y=0)、非法返回值、数组越界、空指针

## Mutability and Immutability

**改变一个变量：**将该变量指向另一个值的存储空间  <=> **改变一个变量的值：**将该变量当前指向的值的存储空间中写入一个新的值

* 不变性是重要的设计原则（使用**final**）
  * final 类无法派生子类
  * final 变量无法改变值 / 引用
  * final 方法无法被子类重写

**不变对象**：一旦被创建，始终指向同一个值 / 引用 <=> **可变对象**：拥有方法可以修改自己的值 / 引用

* 使用**不可变类型更安全**，但是对其频繁修改会产生大量的临时拷贝，产生垃圾 -> 使用**可变类型来提高效率**
* 可变类型可以**使用防御式拷贝来保证安全**，但也可能产生大量的拷贝垃圾
* 安全的使用可变类型：局部变量，不会涉及共享；只有一个引用, 如果有多个引用（别名），使用可变类型就非常不安全

## Snapshot Diagram

* 基本类型的值：直接用箭头指向值

![Snapshot Diagram primal](ADT-OOP\Snapshot Diagram primal.JPG)

* 对象类型的值：用箭头指向一个圈，圈里面有成员变量的名字

![Snapshot Diagram Obj](ADT-OOP\Snapshot Diagram Obj.JPG)

* 不可变对象要用双线椭圆

![Snapshot Diagram ImmuObj](ADT-OOP\Snapshot Diagram ImmuObj.JPG)

* 不可变引用要用双线箭头（final int id）

![Snapshot Diagram ImmuPoint](ADT-OOP\Snapshot Diagram ImmuPoint.JPG)

* 数组的画法

![Snapshot Diagram array](ADT-OOP\Snapshot Diagram array.JPG)

## 复杂数据类型：数组和集合

* List：元素都是object，有序存放
* set：无序存放，不能有重复元素
* map：键值对，keys不能重复

List，set，map都是抽象接口，用的时候用户选择具体的对象，如ArrayList,hashMap等

___

### 迭代器

* 迭代器是一个依次遍历集合类并返回其中对象的类
* 迭代器有两个方法：
  * next() : 是mutator方法，返回集合的下一个对象
  * hasNext() : 判断集合是否有下一个对象
* 迭代器**迭代的时候不能改变集合中的元素**，否则迭代器的index会指向错误的元素，比如迭代删除的时候最好使用迭代器自己有的remove()方法

---

### Java中的不可变类型

* 基本类型和它们的包装类都是**不可变**的
* Java中的**collection类型都是可变的** => 如何使得集合不可变：可以用包装类**Collections.unmodifiableList()**，这样集合就只能看不能改了，然而包装类只能在运行阶段获得，编译阶段无法检查，如果在编译阶段尝试修改一个不可变集合类，在运行时才会抱错

---

### Null references

* Java中，objects和arrays的指向(除了原生类型)可以为null
* 原始数据类型不能为null，否则编译器会报错
* null类型的值非常麻烦而且不安全，不推荐使用

# 设计规约(Specification)

> Java中的方法：包含方法声明，方法参数（不可变），返回值

## 规约(Specification)

如Java的API，代码是给编译器看的，而文档中的设计决策是供自己和别人参考的

* 规约扮演防火墙的角色，类似黑盒包装代码
* 根据规约判断函数是否等价

## 前置条件和后置条件

* **前置条件**：对客户端的约束，在使用方法时必须满足的条件（前置条件不满足时，开发者可以让方法做任何事情(failing fast)）
* **后置条件**：对开发者的约束，前置条件满足时方法结束必须满足的条件
* 契约：如果前置条件满足了，后置条件必须满足

## 区分规约

> 规约的确定性、规约的陈述性、规约的强度

* 规约的强度 S2 >=S1 :  （就可以用S2代替S1）
  * S2的前置条件更弱
  * S2后置条件更强
* 越强的规约，意味着 implementor 的自由度和责任越重，而 client 的责任越轻。
* 确定的规约(Deterministic)：给定一个满足 precondition 的输入，其输出是唯一的、明确的
* 欠定的规约(Under-deterministic)：同一个输入可以有多个输出
* **规约表**：规约确定一个范围，每个点表示满足这个规约的一个函数实现，规约越强，区域越小

![Diagramming specification](ADT-OOP\Diagramming specification.JPG)

* 设计规约的原则：1.内聚：功能单一易理解 2.信息足够：避免歧义 3.不能太弱或太强，太强程序猿麻烦，太弱客户不好用 4.多使用抽象类型

# ADT (抽象数据类型)

> 前面讲“数据类型”及其特性、方法和操作的“规约”及其特性，这里讲数据和操作复合起来，构成 ADT

## 抽象

* 抽象数据类型：**由一组操作所刻画的数据类型**，关注作用于数据上的操作，例如Bool类型可以由多种实现的可能(int,bit,String)但是只关心操作（传统的类型定义：关注数据的具体表示）
* ADT是由操作定义的，与内部实现无关

## 区分类型和操作

* 类型区分
  * 可变类型的对象：提供了可改变其内部数据的值的操作
  * 不变数据类型： 其操作不改变内部值，而是构造新的对象
* 操作区分
  * Creators构造器 t* → T：构造方法如new ArrayList() 或 Arrays.asList()，工厂方法，String.valueOf(Object Obj)与String.valueOf(Object Obj)
  * Producers生产器 T+, t* → T：如String的concat()方法 ->用两个String生成一个String
  * Observers观察器T+, t* → t：List的size()方法
  * Mutators变值器 T+, t* → void | t | T：List的add()方法，通常的返回值为void

  > Each T is the abstract type itself;
  > Each t is some other type.

> 注： Map.keySet() 、BufferedReader.readLine()是mutator

## 设计抽象类型

**设计原则：**

* 设计简洁、一致的操作，配套spec
*  要足以支持 client 对数据所做的所有操作需要，且用操作满足 client 需要的难度要低
*  要么抽象、要么具体，不要混合 --- 要么针对抽象应用设计，要么针对具体应用的设计

## 表示独立性（Representation Independence）

表示独立性： client 使用 ADT 时无需考虑其内部如何实现， ADT 内部表示的变化不应影响外部 spec 和客户端。

## 不变量

* 不变量：在任何时候总是 true，如immutability，由ADT负责，与 client 端的任何行为无关
* 表示泄漏会破坏不变量 -> 用防御式拷贝
* 保持不变性和避免表示泄漏，是 ADT 最重要的一个 Invariant 

## RI（Rep Invariant）和AF（Abstraction Function）

* **R(Rep values)**：是ADT的表示，用来用具体的实现表示抽象的空间
* **A(Abstract values)**：抽象值构成的空间，是client看到的值，可能和Rep相差很大，比如可以用数组(R)来表示无界整数(A)
* R与A的**关系**：满射但是不一定单射，也就是不是所有的Rep都映射到A，但是每个A都一定被映射到

![R&A](ADT-OOP\R&A.JPG)

* 抽象函数AF： R 和 A 之间映射关系的函数，AF : R → A

![RI&AF](ADT-OOP\RI&AF.JPG)

* 即使是同样的 R 、同样的 RI ，也可能有不同的 AF ，即“解释不同”
* 在所有可能改变 rep 的方法内都要检查RI
* 不同的方法可能支持不同的RI/AF，即：执行该方法之后， RI 是否仍然可以保持

### 有益的可变性（Beneficent mutation）

* 对 immutable 的 ADT 来说，它在 A 空间的 abstract value 应是不变的。 但其内部表示的 R 空间中的取值则可以是变化的

### Documenting AF and RI

* 在代码中用注释形式记录 AF 和 RI
* RI ：针对 Rep 的每一个 field以及多个 fields 之间的关系
* AF ：给出 client 看到的 A 值是什么，是对每一个 Rep 值的“数学运算”
* 注：ADT 的规约里只能使用 client 可见的内容来撰写，包括参数、返回值、异常等，如果规约里需要提及“值”，只能使用 A 空间中的“值”，不应谈及任何 R 空间中的任何值

### ADT invariants replace preconditions
相当于将复杂的 precondition 封装到了 ADT 内部

![ADT invariants replace preconditions](ADT-OOP\ADT invariants replace preconditions.JPG)



# OOP (面向对象编程)

> 上一节叙述的是ADT理论，这一节叙述ADT的具体实现技术
>
> * 封装与信息隐藏
> * 继承与重写
> * 多态、子类型、重载
> * 静态与动态分派

## 基本概念：对象、类

**对象：状态（state） + 动作（behavior）**
> Dogs have state (name, color, breed, hungry) and behavior (barking,fetching, wagging tail)

**类：方法（methods） + 属性（fields）**
> 类是封装好的，像一个模型；对象是由类实例化的，也就像一个具体的样本

静态成员变量、静态方法 ***VS*** 实例成员变量、实例方法 --> 静态的用static声明

## 接口

接口（Interface）：是一系列函数声明，没有函数体，由实现类implement接口

* 一个类可以实现多个接口（implement A,B,C ...）
* 接口之间可以继承与扩展（多继承  Interface3 Extends Interface0, Interface1, interface……）
* **Java中，API由接口和类共同确定**：接口定义API，类具体实现API
* 一个接口可以有多种实现（不同实现性能不同、实现策略不同如：HashSet、TreeSet）

问题：接口定义中没有包含 constructor ，不同实现constructor名字可能不同，客户端需要知道该接口的某个具体实现类的名字 --> 在接口中定义静态工厂方法（static factory）

## 封装和信息隐藏

> 内部数据和实现细节的隐藏程度是模块化设计质量的最重要的标准

* API同实现分离，模块间只通过API通讯
* 使用**private**：只能通过声明这个属性的类中访问 (这个类中的所有区域)
* 尽量使用接口类型声明变量，客户端仅使用接口中定义的方法，无法直接访问属性

## 继承和重写

### 重写(overriding)

重写是子类对父类的**允许访问的方法**的实现过程进行重新编写, 返回值和形参都不能改变。
**即外壳不变，核心重写**

* 重写方法不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常（父：检查异常 IOException 子不能：Exception）
* 访问权限不能比父类中被重写的方法的访问权限更低
* 声明为final的方法不能被重写

> * final属性：防止重新赋值
> * final方法：防止重写
> * final类：防止继承

* 重写之后，可以利用 super() 来使用了父类型中被重写的函数的功能
* 子类构造方法通过super()调用父类的构造方法：super();必须在子类构造方法第一行

### 抽象类

* 如果某些操作是所有子类型都共有，但彼此有差别，可以在父类型中设计抽象方法，在各子类型中重写
* 所有子类型完全相同的操作，放在父类型中实现，子类型中无需重写。
* Concrete class --> Abstract Class --> Interface (继承链)

## 多态、子类型、重载

### 多态

1. 特殊多态：一个函数可以有多个同名实现，使用方法重载 （ print()、print(int a) ）
2. 参数化多态：一个类型名字可以代表多个类型，使用泛型
3. 子类型多态（包含多态）：使用接口和抽象类（strategy设计模式）

### 1.重载(overloading)

重载指多个方法具有同样的名字，但(必须)有不同的参数列表或返回值类型
* client 可用不同的参数列表，调用同样的函数
* 重载是静态多态，在编译阶段即可得知要具体执行哪个方法（重写在运行阶段才能确定，属于动态检查）
* 子类重载了父类的方法后，子类仍然继承了被重载的方法
注：重写发生在运行时，重载发生在编译时（在编译时就能决定使用哪个方法）

### 2.参数化多态--泛型编程

参数多态性（泛型）：方法正对多种类型时具有同样的行为，可以使用统一的类型表达多种类型

* 在运行时根据传入的具体类型决定
* **泛型方法**
  * 要在方法声明中加入自己的泛型声明<>
  * 静态方法不能使用泛型类定义的泛型变量，只能自己变成泛型方法

![Generic](ADT-OOP\Generic.JPG)

* **泛型接口**
  * 实现类可以是泛型的(到运行时再确定)，也可以是非泛型的
* 类型通配符：使用 ? 代替具体的类型参数，**只在使用泛型的时候出现**，不能在定义中出现

![GenericMatch](ADT-OOP\GenericMatch.JPG)

### 3.子类型多态

* B是A的子类型，要求B的spec至少要和A的一样强（强化pre弱化post）

## 一些重要的Object方法

* equals()：自反、对称、传递
* hashCode()：equals满足则hashcode也要满足
* toString()：重写来有效描述一个object



# ADT与OOP中的“等价性”

> * 很多场景下，需要判定两个对象是否“相等”，例如：判断某个Collection 中是否包含特定元素。
> * == 和 equals() 有和区别？如何为自定义 ADT 正确实现 equals() ？

## 等价关系

* ***不可变类型的等价性***
  * **用AF判断**：如果**AF映射到同样的结果**，则等价(**不可变ADT的等价性基本判断方式**)
  * **用外部观察结果判断**：对两个对象**调用任何相同的操作**，都会得到相同的结果，则等价（只有构造器的ADT没办法使用这个方法判断等价性）
* ***引用等价性与对象等价性***
  * == ：**引用等价性**（如果两个引用指向同一个对象(在内存中地址相同)，则满足==）
  * equals() ：**对象等价性**（用来判断对象是否等价，自定义ADT时需要重写）
  * 对基本数据类型用==，对象数据类型用equals
  * 重写equals注意写@override和instanceof判断
  * a.equals(null)一定要返回false，两个等价的对象每次调用equals方法都返回true，除非被mutate
  * equals方法应该满足**自反、传递、对称性**，否则是错误的
  * 重写hashCode()方法：1.调用object.hashCode() 2.把要比较的要素放到一个array里面，调用Arrays.hashCode(new array{a1,a2,a3...})
* ***可变类型的等价性***
  * **观察等价性**：在不改变状态的情况下，两个 mutable 对象是否看起来一致（调用除了mutator之外的方法，包括第三者的方法）
  * **行为等价性**：调用**两个对象自己的任何方法**都显示出一致的结果
  * 注意：如果某个 mutable 的对象(比如list)包含在 Set 集合类中，当其发生改变后，集合类的行为不确定(list.add() 后这个list的hashcode就改变了，但是在set里面桶的位置没变，调用set.contains(list)方法的时候就会到新的hashcode对应桶里面找，找不到)
  * 对可变类型，大多数时候实现行为等价性即可(观察等价性可能会破坏RI)(判断内存地址一致性)，不可变类型必须重写equals和hashcode方法