---
title: maintain6&robust7
date: 2019-05-16 22:40:37
tags: software Construction
categories: software Construction
---

> 部分内容摘录自哈工大软件构造讲义

# 可维护性的度量和构造原则

> 如何度量可维护性以及在这些度量的基础上设立构造原则

## *可维护性的一些衡量

* 圈复杂度：代码运行的结构复杂度（循环、跳转）
* 代码行数：某个ADT太多行可能意味着功能繁多，可以再细分
* 可维护性指数
* 继承的层次数：越深越难维护
* 类之间的耦合度：耦合度高难以单独维护

## *模块化编程

> 把系统分成小的模块，每个模块可以被独立地设计、实现、测试、复用、有独立的意义

模块化编程要求：

1. 高内聚：模块内部
2. 低耦合：模块间
3. 分离关注点
4. 信息隐藏

### *模块化的五个度量

1. 可分解性：将问题分解为各个可独立解决的子问题，使模块之间的依赖关系显式化和最小化
2. 可组合性： 可容易的将模块组合起来形成新的系统，使模块可在不同的环境下复用
3. 可理解性： 每个子模块都可被系统设计者容易的理解
4. 可持续性：小的变化将只影响一小部分模块，而不会影响整个体系结构
5. 出现异常后的保护：运行时的不正常将局限于小范围模块内

### *模块化设计的五个原则

1. 直接映射（Direct Mapping）：模块的结构与现实世界中问题领域的结构保持一致
2. 尽可能少的接口（Few Interfaces）：模块应尽可能少的与其他模块通讯
3. 尽可能小的接口（Small Interfaces）： 如果两个模块通讯，那么它们应交换尽可能少的信息
4. 显式接口（Explicit Interfaces）：当 A 与 B 通讯时，应明显的发生在 A 与 B 的接口之间
5. 信息隐藏（Information Hiding）： 经常可能发生变化的设计决策应尽可能隐藏在抽象接口后面

## 聚合度与耦合度

* **耦合度（Coupling）**：衡量模块之间的依赖度，包括---1.模块间接口的数量 2. 每个接口的复杂度
  * 一个例子：HTML、CSS、JavaScript之间的耦合
* **聚合度（Cohesion）**：一个模块内部的方法和责任的相关度

![maintain6-robust7\Coupling-Cohesion.JPG](maintain6-robust7\Coupling-Cohesion.JPG)

## SOLID

> 五个面向对象的设计原则

1. 单一责任原则SRP：一个类，一个责任 
2. 开放-封闭原则OCP
   * 对扩展性的开放：模块可表现出新的行为以满足需求的变化
   * 对修改的封闭：但模块自身的代码是不应被修改的
   * 关键的解决方案 ：抽象技术 

![maintain6-robust7\OCP.JPG](maintain6-robust7\OCP.JPG)

1. Liskov 替换原则LSP：
   * 子类型必须能够替换其基类型
   *  派生类必须能够通过其基类的接口使用
2. 依赖转置原则DIP： 抽象的模块不应依赖于具体的模块，具体应依赖于抽象 
3. 接口聚合原则ISP： 客户端不应被强制使用于它们不需要的方法， 胖接口可**分解**为多个小的接口

# 面向可维护性的设计模式

## Creational patterns

### 工厂模式  ---  Factory Method pattern

* 背景：当 client 不知道要创建哪个具体类的实例，或者不想在 client 代码中指明要具体创建的实例
* 思想：实例化对象的工作推迟到了专门负责创建对象的工厂类中

![maintain6-robust7\factory_pattern_uml_diagram.jpg](maintain6-robust7\factory_pattern_uml_diagram.jpg)

### 抽象工厂模式  ---  Abstract Factory

* 背景：client要一套电脑配件，不想单独一个一个生产（鼠标键盘什么的组成一套），抽象工厂就可以将这些套件打包好，负责生产不同品牌的一条电脑配件（但是client还是一次创建一个而不是一套）
* 思想：提供接口以创建**一组**相关 / 相互依赖的对象
* 各产品创建过程对 client 可见，但**“搭配”不能改变**

![maintain6-robust7\AbstractFactory.jpg](maintain6-robust7\AbstractFactory.jpg)

### 构造器模式  ---  Builder

* 背景：创建一个完整的产品，由多个部分组成， client 不需了解每个部分是怎么创建、各个部分怎么组合
* 将一个产品的不同部分封装在一个ADT里面，这些部分还待定，由一个director来使用builder构造器来根据需要构造好client需要的产品后返回给client
* 将一个复杂对象的**构造方法**与对象内部的具体表示分离出来，且不强调复杂对象内部各部分的“次序”

```java
  /* "Product" */
  class Pizza {
    private String dough = "";
    private String sauce = "";
    private String topping = "";

    public void setDough(String dough) {  this.dough = dough;}
    public void setSauce(String sauce) { this.sauce = sauce;}
    public void setTopping(String topping) {this.topping = topping;}
  }

    /* "Abstract Builder" */
  abstract class PizzaBuilder {
    protected Pizza pizza;
    public Pizza getPizza() {
      return pizza;
    }
    public void createNewPizzaProduct() {
      pizza = new Pizza();
    }
    public abstract void buildDough();
    public abstract void buildSauce();
    public abstract void buildTopping();
  }

  /* "Director" */
  class Waiter {
    private PizzaBuilder pizzaBuilder;

    public void setPizzaBuilder(PizzaBuilder pb) {
      pizzaBuilder = pb;
    }

    public Pizza getPizza() {
      return pizzaBuilder.getPizza();
    }

    public void constructPizza() {
      pizzaBuilder.createNewPizzaProduct();
      pizzaBuilder.buildDough();
      pizzaBuilder.buildSauce();
      pizzaBuilder.buildTopping();
    }
  }
```

## Structural patterns

### 桥接模式  ---  Bridge

* 思想：有一个作为桥接的接口，使得实体类的功能独立于接口实现类。这两种类型的类可被结构化改变而互不影响

![maintain6-robust7\bridge_pattern_uml_diagram.jpg](maintain6-robust7\bridge_pattern_uml_diagram.jpg)

有一个作为桥接实现的 *DrawAPI* 接口和实现了 *DrawAPI* 接口的实体类 *RedCircle*、*GreenCircle*。***Shape* 是一个抽象类**，将使用 *DrawAPI* 的对象

注：**Strategy模式**是帮助前者临时使用后者中的“算法”，前者无需永久保存后者的实例，而**桥接模式**强调一个类 A 的对象中有其他类 B 的对象作为其组成部分，但 A 的对象具体绑定到 B 的哪个具体子类的实现？在运行时通过 delegation 加以组合，并永久保存这种 delegation 关系

### 代理模式  ---  Proxy

* 背景：某个对象不想让client 直接使用（因为代价高或不安全），故设置 proxy ，在二者之间建立防火墙

![maintain6-robust7\Proxy.JPG](maintain6-robust7\Proxy.JPG)

### 组合模式  ---  Composite

* 思想：在同类型的对象之间建立起树型层次结构，一个上层对象可包含多个下层对象
* **使用场景：**部分、整体场景，如树形菜单，文件、文件夹的管理

![maintain6-robust7\composite_pattern_uml_diagram.jpg](maintain6-robust7\composite_pattern_uml_diagram.jpg)

一个员工可能管理多个下属员工，这些下属员工又管理一些下属员工 ---> 树形结构

## Behavioral patterns

### 观察者模式  ---  Observer

思想：定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新

![](maintain6-robust7\observer_pattern_uml_diagram.jpg)

比如：偶像是subject，粉丝是observer，粉丝可以调用偶像的attach方法加入到偶像的粉丝list，而偶像在每次setState方法的时候都调用自己的notifyAll方法，来调用list里面的每个粉丝的update方法，让粉丝知道自己的实时情况

### 访问者模式  ---  Visitor

* 在特定 ADT 上执行某种特定操作，但该操作**不在 ADT 内部实现**，而是 delegate 到独立的 visitor 对象，ADT要做的就是**accept**这些负责实现操作的ADT就行，客户端可灵活扩展 / 改变 visitor 的操作算法，而不影响 ADT

```Java
  /* Abstract element interface (visitable) */
  public interface ItemElement {
    public int accept(ShoppingCartVisitor visitor);
  }
  /* Concrete element */
  public class Book implements ItemElement{
     private double price;
     ...
     int accept(ShoppingCartVisitor visitor) {
     visitor.visit(this);
     }
  }
  /* Abstract visitor interface */
  public interface ShoppingCartVisitor {
     int visit(Book book);
  }
```

### 中介模式  ---  Mediator

* 思想：多个对象之间要进行交互，不是直接交互，而是通过 mediator ，实现消息的广播
* 例子：多个用户可以向聊天室发送消息，聊天室向所有的用户显示消息，用户只需要负责发送和接收消息即可

与observer区别：observer为一个的被观察者和一群观察者，而Mediator模式中各个object的地位对等，通过一个Mediator来交互

### *命令模式  ---  Command pattern

* 思想：将“指令”封装为对象，指令的所有细节对 client 隐藏，在指令内对具体的 ADT 发出动作（调用 ADT 的细节操作）
* client只需要知道指令，调用指令的execute()方法即可

```java 
public interface Command{
  public void execute();
}
//Concrete Command
public class LightOnCommand implements Command{
  //reference to the light
  Light light;
  public LightOnCommand(Light light){
    this.light = light;
  }
  public void execute(){
    light.switchOn();
  }
}
//Receiver
public class Light{
  private boolean on;
  public void switchOn(){
    on = true;
  }
  public void switchOff(){
    on = false;
  }
}
//Invoker
public class RemoteControl{
  private Command command;
  public void setCommand(Command c){
    this.command = c;
  }
  public void pressButton(){
    command.execute();
  }
}
```

### *职责链模式  ---  Chain of responsibility、

* 客户端只需在流水线的入口发出请求即可，请求自动在
  流水线上传递，直到被处理
* 每个 handler 只需要维护“下一个” handler 即可，自己能处理就处理，否则找下一个handler 去处理 request

![maintain6-robust7\handle-chain.JPG](maintain6-robust7\handle-chain.JPG)

### *状态模式  ---  State pattern

不同状态下进行同一个动作，实现方式不同，比如早上**吃东西**，吃的是早餐，睡眼朦胧，晚上**吃东西**吃的是晚餐，精神状态也较好（对我而言）

![maintain6-robust7\state_pattern_uml_diagram.jpg](maintain6-robust7\state_pattern_uml_diagram.jpg)

### 备忘录模式  ---  Memento Pattern

* 思想：记住对象的历史状态，以便于“回滚”
*  ADT 自己不维护各个备忘录，而是交给 Caretaker 来负责，它管理着所有的备忘录（加入新的、从中取出旧的）

![maintain6-robust7\memento_pattern_uml_diagram.jpg](maintain6-robust7\memento_pattern_uml_diagram.jpg)

# 语法驱动的构造 -- 正则表达式

见形式语言与自动机



# *健壮性与正确性

> 软件构造最关键的质量特性

* 健壮性：系统在不正常输入或不正常外部环境下仍能够表现正常的程度（尽可能保持软件运行而不是总是退出）
* 正确性：程序按照 spec 加以执行的能力，是最重要的质量指标（永不给用户错误的结果）

# 错误与异常处理

## 错误与异常的种类

![maintain6-robust7\exceptions.JPG](maintain6-robust7\exceptions.JPG)

## 异常的分类

* 所有异常和错误都继承自一个Throwable接口
* 异常可以分为：**运行时异常 和 其他异常**
  * 运行时异常：由程序员在代码里处理不当造成
  * 其他异常：由外部原因造成
* **Unchecked exception**（Error + RuntimeException）：可以不处理，编译没问题，但执行时出现就导致程序失败
* **Checked exception**：必须捕获并指定错误处理器 handler ，否则编译无法通过
  * 如果 client 端对某种异常无能为力，可以把它转变为一个 unchecked exception ，程序被挂起并返回客户端异常信息

![maintain6-robust7\checked-uncheckedEXC.JPG](maintain6-robust7\checked-uncheckedEXC.JPG)

## 声明Checked异常  ---  throws

> 异常也是 spec 的一部分，在 post-condition 中刻画

* 异常的来源
  *  你所调用的其他方法抛出了一个 checked exception—— 从其他函数传来的异常
  *  当前方法检测到错误并使用 throws 抛出了一个 checked exception—— 你自己造出的异常
* java处理异常的机制：**抛出异常以及捕获异常** ，一个方法所能捕捉的异常，一定是Java代码在某处所抛出的异常。简单地说，异常总是**先被抛出，后被捕捉的**
*  一旦抛出异常，方法不会再将控制权返回给调用它的 client ，因此也无需考虑返回错误代码
* 如果不希望强制每个方法都声明某个可能遇到的异常，可以让那个异常继承RuntimeException
* 可以只throws异常，交给调用者处理，原则是**尽量在自己这里处理，实在不行就往上传**

### rethrow异常

可以在 catch 里抛出异常，将一个异常转换为另一种异常，更方便 client 端获取错误信息并处理，但这么做的时候最好保留“根原因”

```Java
try {
  access the database
}
catch (SQLException e) {
  throw new ServletException("database error: " + e.getMessage());
}
```

### finally

异常抛出时，方法中正常执行的代码被终止，而如果异常发生前曾申请过某些资源，可以用**finally**语句处理
finally代码块在所有catch块的后面，无论异常有没有被捕获，都会执行finally块里面的语句

## assertion

assertion用来保证正确性，只有在要求代码某处必须正确不应该有错误的情况下才使用，如果是可能预期会发生的错误，则使用exception

可以来检测：

* pre-condition
* post-condition
* 代码不应该到达的地方
* ......

# 测试

## 黑盒测试

用于检查代码的功能，不关心内部实现细节

* 根据**spec**中的**pre-condition**和**post-condition**来准备测试的策略和写测试用例
* 针对每个输入数据需要满足的约束条件，划分等价类来进行测试

## 白盒测试

* 关注代码的**覆盖度**，测试难度和测试效果：**路径覆盖 > 分支覆盖 > 语句覆盖**
* 考虑内部实现细节

## 边界测试

* 如整数和负数的边界：0
* 数子类型的最大值和最小值：int的integer.max和0
* collection集合类：size = 0、第一个和最后一个元素

## 测试document的书写

整体测试类document的书写，要写明测试划分的等价类，然后这样划分的原因

![maintain6-robust7\testClass.JPG](maintain6-robust7\testClass.JPG)

每个方法的document的书写

![maintain6-robust7\testMethod.JPG](maintain6-robust7\testMethod.JPG)

![maintain6-robust7\testInstance.JPG](maintain6-robust7\testInstance.JPG)