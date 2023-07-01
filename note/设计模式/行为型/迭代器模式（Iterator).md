## 迭代器模式（Iterator)

> 事例:把书放在书架上,依次显示

![iterator.drawio](D:/图/3150/iterator.drawio.png)

> 类、接口一览表

| 名字              | 说明           |
| ----------------- | -------------- |
| Aggregate         | 表示集合的接口 |
| Iterator          | 遍历集合的接口 |
| Book              | 表示书的类     |
| BookShelf         | 表示书架的类   |
| BookShelfIterator | 遍历书架的类   |
| Main              | 测试程序的入口 |

### 代码清单

> Aggregate接口

```java
public interface Aggregate {

    Iterator iterator();
}
```

​		在Aggregate接口中声明iterator方法，该方法会生成一个用于遍历集合的迭代器。

​		想要遍历集合中的元素时，可以调用iterator方法生成一个实现Iterator接口类的实例。

> Iterator接口

```java
public interface Iterator {

    boolean hasNext();
    Object next();
}

```

​		声明两个方法，即判断是否存在下一个元素的hasNext方法，和获取下一个元素的next方法。

​		hasNext方法的返回值是boolean，当集合中存在下一个元素时，该方法返回true；当集合中不存在下一个元素时，即遍历到集合末尾时，该方法返回false。该方法用于循环终止。

​		next方法，返回值object，表示返回集合中的一个元素，除此之外，为了下次调用正确的返回下一个元素，next还将迭代器移动至下一个处理元素。		

> Book类

```java
@Data
public class Book {

    private String name;

    public Book(String name) {
        this.name = name;
    }
}
```

>  BookShelf类

​		将该类作为集合的处理类，所以需要实现Aggregate接口。需实现iterator方法。

```java
@Data
public class BookShelf implements Aggregate {
    private ArrayList<Book> bookList;
    public BookShelf() {
        this.bookList = new ArrayList();
    }
    public void appendBook(Book book) {
        bookList.add(book);
    }
    public int getLength() {
        return bookList.size();
    }
    public Book getBookAt(int index) {
        return bookList.get(index);
    }
    @Override
    public Iterator iterator() {
        return new BookShelfIterator(this);
    }
}
```

​		bookList是书的集合。

​		iterator方法生成并返回BookShelfIterator，外界想遍历书架时调用这个方法。

> BookShelfIterator类

```java
public class BookShelfIterator implements Iterator {

    private BookShelf bookShelf;
    private int index = 0;

    public BookShelfIterator(BookShelf bookShelf) {
        this.bookShelf = bookShelf;
    }

    @Override
    public boolean hasNext() {
        if (index < bookShelf.getBookList().size()){
            return true;
        }
        return false;
    }

    @Override
    public Object next() {
        Book book = bookShelf.getBookAt(index);
        index++;
        return book;
    }
}
```

​		BookShelfIterator需要发挥Iterator的作用，所以实现Iterator接口。

​		bookShelf表示需要遍历的书架，index表示当前所指下标。

​		hasNext判断当前书架是否还有下一本书。

​		next方法返回当前所指书的实例，同时需要指向下一本书。

> Main

```java
public class Main {
    public static void main(String[] args) {
        BookShelf bookShelf = new BookShelf();
        bookShelf.appendBook(new Book("1"));
        bookShelf.appendBook(new Book("2"));
        bookShelf.appendBook(new Book("3"));
        bookShelf.appendBook(new Book("4"));
        bookShelf.appendBook(new Book("5"));
        bookShelf.appendBook(new Book("6"));
        bookShelf.appendBook(new Book("7"));
        Iterator iterator = bookShelf.iterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }
    }
}
```

### 登场角色

> Iterator（抽象迭代器）

​		定义访问和遍历聚合元素的接口，通常包含 hasNext()、next() 等方法。 

> ConcreteIteratot（具体迭代器）

​		实现抽象迭代器接口中所定义的方法，完成对聚合对象的遍历，记录遍历的当前位置。 

> Aggregate（抽象集合）

​		定义创建Iterator角色的接口。

> ConcreteAggregate（具体集合）

​		实现Aggregate所定义的接口，创建具体的Iterator对象，即ConcreteIteratot对象。