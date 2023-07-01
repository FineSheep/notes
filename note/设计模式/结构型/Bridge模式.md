# Bridge模式

​		Bridge的意思是桥梁。桥梁的功能是将河流的两侧连接i起来，Bridge模式的作用也是将两样东西连接起来，他们分别是**类的功能层次结构**和**类的实现层次结构**。

​		bridge模式的作用是在“类的功能层次结构”和“类的实现层次结构”之间搭建桥梁。

## 类的层次结构的两个作用

### 希望增加新功能

​		假设有一个类someting，当我们想在Something中增加新功能时，会编写一个Something具体的子类，即SomethingGood类。这样就构成了一个类的层次结构。

![image-20221212103618433](D:/图/3150/image-20221212103618433.png)

- 父类具有基本功能
- 在子类中新增加功能

​		这是**类的功能层次结构**。

> 类的层次结构不应过深。

### 希望增加新的实现

​		父类的任务是通过声明抽象方法的方式定义接口，而子类的任务是实现抽象方法。

​		当子类ConcreteClass实现父类AbstractClass类的抽象方法时，他们之间就构成了一个层次结构。

![image-20221212104128722](D:/图/3150/image-20221212104128722.png)

​		但是，这里的层次结构并非用于增加功能，它的真正作用的帮助我们实现任务分担。

- **父类通过声明抽象方法来定义接口**
- **子类通过实现具体的方法来实现接口**



​		这是**类的实现层次结构**。

### 层次结构的混杂与分离

​		我们需要将类的功能层次结构与类的实现层次结构**分离为两个独立的层次结构**。（当结构只有一层时，功能层次与实现层次时混杂在一起的，很容易时=使层次结构变得复杂，不易理解）。如果只是简单的分开，两者之间必然会缺少联系，所以我们还需要建立一座桥梁。

## 实例程序

### 类的一览表

| 层次     | 名字              | 说明                 |
| -------- | ----------------- | -------------------- |
| 功能层次 | Display           | 负责显示的类         |
| 功能层次 | CountDisplay      | 增加显示规定次数功能 |
| 实现层次 | DisplayImpl       | 负责显示的类         |
| 实现层次 | StringDisplayImpl | 用字符串显示         |
|          | Main              | 测试程序             |

### 功能层次结构

> Display类

​		Display类的功能时抽象的，负责显示一些东西。

​		impl字段保存的实现Display功能的实例。该实例通过构造方法传递保存。

​		open、print、close是Display提供的接口，这3个方法调用了impl的实现方法，这样Display的接口就被转换成了DisplayImpl的接口。

```java
public class Display {
    private DisplayImpl impl;

    public Display(DisplayImpl impl) {
        this.impl = impl;
    }

    public void open() {
        impl.rawOpen();
    }

    public void print() {
        impl.rawPrint();
    }

    public void close() {
        impl.rawClose();
    }

    public final void display() {
        open();
        print();
        close();
    }
}
```

> CountDisplay类

​		在Display的基础上增加了一个新功能。在显示功能的基础上增加了显示规定次数的功能。

```java
public class CountDisplay extends Display {
    public CountDisplay(DisplayImpl impl) {
        super(impl);
    }

    public void multiDisplay(int times){
        open();
        for (int i = 0; i < times; i++) {
            print();
        }
        close();
    }
}
```

### 类的实现层次结构

> DisplayImpl类

​		抽象类，声明了rawOpen、rawPrint、rowClose这3个抽象方法。

```java
public abstract class DisplayImpl {
    public abstract void rawOpen();
    public abstract void rawPrint();
    public abstract void rawClose();
}
```

> StringDisplayImpl类

​		这是真正的实现，继承DisplayImpl类，作为子类来使用其3个方法。

```java
public class StringDisplayImpl extends DisplayImpl {

    private String string;
    private int width;

    public StringDisplayImpl(String string) {
        this.string = string;
        this.width = string.getBytes().length;
    }

    @Override
    public void rawOpen() {
        printLine();
    }

    @Override
    public void rawPrint() {
        System.out.println("|" + string + "|");
    }

    @Override
    public void rawClose() {
        printLine();
    }

    private void printLine() {
        System.out.print("+");
        for (int i = 0; i < width; i++) {
            System.out.print("-");
        }
        System.out.println("+");

    }
}
```

> Main

```java
public class Main {
    public static void main(String[] args) {
        Display d1 = new Display(new StringDisplayImpl("hello China"));
        Display d2 = new CountDisplay(new StringDisplayImpl("hello world"));
        CountDisplay d3 = new CountDisplay(new StringDisplayImpl("hello world"));
        d1.display();
        d2.display();
        d3.display();
        d3.multiDisplay(5);
    }
}
```

> 显示结果

```
+-----------+
|hello China|
+-----------+
+-----------+
|hello world|
+-----------+
+-----------+
|hello world|
+-----------+
+-----------+
|hello world|
|hello world|
|hello world|
|hello world|
|hello world|
+-----------+
```

## 登场角色

- Abstraction（抽象化）（Display）

  该角色位于功能层次结构的最上层，使用Implement角色的方法定义基本功能。该角色保存Implement实例。

- RefinedAbstraction（改善后的抽象化）（CountDisplay）

  在Abstraction基础上增加新功能

- Implement（实现者）（DisplayImpl）

  该角色位于实现层次结构的最上层，它定义用于实现Abstraction角色的接口。

- ConcreteImplement（具体实现者）（StringDisplayImpl）

  实现Implement角色定义的接口。