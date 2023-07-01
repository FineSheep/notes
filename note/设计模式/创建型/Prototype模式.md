# Prototype模式

​			在JAVA中通常通过new关键字生成实例。像这样使用new来生成实例时，必须指定类名。但有时需要根据现有情况下来生成新的实例。

1. **对象种类繁多，无法将他们整合到一个类中**

   ​	需要处理的对象太多，如果分别作为一个类，必须编写很多个类文件。

2. **难以根据类生成实例**

   ​	生成实例的过程太复杂，很难根据类来生成实例。

3. **解耦框架与生成的实例**

   ​	生成实例的框架不依赖具体的类。

## 事例程序

> 类和接口一览表

| 名字         | 说明                               |
| ------------ | ---------------------------------- |
| Product      | 声明抽象方法use和createClone的接口 |
| Manager      | 调用createClone方法复制实例的类    |
| MessageBox   | 实现use和createClone               |
| UnderlinePen | 实现use和createClone               |
| Main         | 测试程序                           |

​		虽然Manager类会调用createClone方法，但对拘役要复制哪一个类一无所知。不过们只要实现了product接口的类，调用它的createClone方法就可复制出新的实例。

​		MessageBox和UnderlinePen类是两个实现了Product接口的类。只要事先将两个类注册到manager中就可以随时复制新的实例。

> 类图

![](D:/图/3150/prototype.drawio.png)

## 代码清单

> Product接口

​		product接口是复制功能的接口，继承了**java.lang.Cloneable**。
​		use方法是使用的方法，具体使用交给子类。
​		createClone方法用于复制实例的方法。

```java
public interface Product extends Cloneable{

    public abstract void use(String s);
    public abstract Product createClone();
}
```

> Manager类

​		Manager类使用product接口来复制实例。
​		showCase保存“名字”和“实例”之间的对应关系。
​		register会将收到的名字和product接口注册到showcase中。这里的product具体什么类型是不知道的，但有一点可以确认，它肯定是实现了product接口的类的实例。
​		在product和manager中没有出现messageBox和underlinePen，这就意味着魔门可以独立修改product和manager。**一旦使用了别的类，就意味着该类与其他类精密的耦合在一起了。**product是连接manager与其他具体类的桥梁。

```java
public class Manager {
    private HashMap<String, Product> showCase = new HashMap<>();

    public void register(String name, Product proto) {
        showCase.put(name, proto);
    }

    public Product create(String protoName) {
        Product product = showCase.get(protoName);
        return product.createClone();
    }
}
```

> MessageBox类

​		use使用效果如下：

```
***************
* Hello,world *
***************
```

​		createClone方法用于复制自己。它内部调用的clone方法是JAVA定义的方法，用于复制自己。该类实现了Clonable接口我们才能调用这个方法，否则会抛出CloneNotSupportedException异常。因为MessageBox实现了Product，而Product继承了Clonable，所以不会抛异常。**需要注意的是：**Clonable本身并没有定义深任何方法，只是告诉程序可以调用clone方法。

​		当其它类要求复制实例时，必须调用createClone这样的方法，然后在内部调用clone方法。

```java
@Data
public class MessageBox implements Product {
    private char decoChar;

    public MessageBox(char decoChar) {
        this.decoChar = decoChar;
    }

    @Override
    public void use(String s) {
        int length = s.getBytes().length;
        for (int i = 0; i < length + 4; i++) {
            System.out.print(decoChar);
        }
        System.out.println("");
        System.out.println(decoChar+" "+s+" "+decoChar);
        for (int i = 0; i < length+4; i++) {
            System.out.print(decoChar);
        }
        System.out.println("");
    }

    @Override
    public Product createClone() {
        Product p =null;

        try {
            p =(Product) this.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return p;
    }
}
```

> UnderlinePen类

​		use方法：

```
"Hello,world"
 ~~~~~~~~~~~
```

```java
@Data
public class UnderLinePen implements Product {
    private char ulChar;

    public UnderLinePen(char ulChar) {
        this.ulChar = ulChar;
    }

    @Override
    public void use(String s) {
        int length = s.getBytes().length;
        System.out.println("\"" + s + "\"");
        System.out.print(" ");
        for (int i = 0; i < length; i++) {
            System.out.print(ulChar);
        }
        System.out.println("");
    }

    @Override
    public Product createClone() {
        Product p = null;

        try {
            p = (Product) this.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return p;
    }
}

```

>  Main类

```java
public class Main {
    public static void main(String[] args) {
        //准备
        Manager manager = new Manager();
        UnderLinePen upen = new UnderLinePen('~');
        MessageBox mbox = new MessageBox('*');
        MessageBox sbox = new MessageBox('/');
        manager.register("strong message", upen);
        manager.register("warning box", mbox);
        manager.register("slash box", sbox);

        //生成
        Product p1 = manager.create("strong message");
        p1.use("Hello,world");
        Product p2 = manager.create("warning box");
        p2.use("Hello,world");
        Product p3 = manager.create("slash box");
        p3.use("Hello,world");
    }
}
```

​		结果：

```
"Hello,world"
 ~~~~~~~~~~~
***************
* Hello,world *
***************
///////////////
/ Hello,world /
///////////////
```

## 登场角色

- 原型

  负责定义用于复制现有实例来生成新势力的方法。----product

- 具体原型

  负责实现复制现有实例并生成新实例的方法。----MessageBox和UnderlinePen

- 使用者

  负责使用复制方法生成新实例。----Manager

### 扩展

> 浅克隆：创建一个新对象，新对象的属性和原来对象完全相同，对于非基本类型属性，仍指向原有属性所指向的对象的内存地址。
>
> 深克隆：创建一个新对象，属性中引用的其他对象也会被克隆，不再指向原有对象地址。