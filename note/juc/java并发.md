---
视频：尚硅谷周阳
笔记来源：https://blog.csdn.net/dolpin_ink/category_11847910.html
（我只是大自然的搬运工）
---

## 基础知识

### 并发包

- `java.util.concurrent`
- `java.util.concurrent.atomic`
- `java.util.concurrent.locks`

![1](D:\notes\note\juc\java并发.assets\51083f7652764efd9b58758bf684f2f8.png)

### start线程

```java
//start
    public synchronized void start() {
        /**
         * This method is not invoked for the main method thread or "system"
         * group threads created/set up by the VM. Any new functionality added
         * to this method in the future may have to also be added to the VM.
         *
         * A zero status value corresponds to state "NEW".
         */
        if (threadStatus != 0)
            throw new IllegalThreadStateException();

        /* Notify the group that this thread is about to be started
         * so that it can be added to the group's list of threads
         * and the group's unstarted count can be decremented. */
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
                /* do nothing. If start0 threw a Throwable then
                  it will be passed up the call stack */
            }
        }
    }
```

```java
private native void start0();//start0是一个native方法
```

- native调用了本地方法，我们可以通过下载官网OpenJDK查看其源码
  - thread.c

    java线程是通过start的方法启动执行的，主要内容在native方法start0中

    Openjdk的写JNI一般是一一对应的，Thread.java对应的就是Thread.c

    start0其实就是JVM_StartThread。此时查看源代码可以看到在jvm.h中找到了声明，jvm.cpp中有实现。

- jvm.cpp

![在这里插入图片描述](D:\notes\note\juc\java并发.assets\2b1d3dd9dde4414b85cd42c71377ae16.png)

- thread.cpp

  - 终于在这里调用了**操作系统的线程启动**，`os::start_thread（thread);`

  ![在这里插入图片描述](D:\notes\note\juc\java并发.assets\2b5576a3b91a428e87bb3bdc00472320.png)

### 概念

####  锁

#### 并发和并行

- 并发

  是在同一实体上的多个事件，是在同一台处理器上“同时”处理多个任务，同一时刻，其实是只有一个事件在发生。

- 并行

  是在不同实体上的多个事件，是在多台处理器上同时处理多个任务，同一时刻，大家都真的在做事情，你做你的，我做我的

![image-20230907155038006](D:\notes\note\juc\java并发.assets\image-20230907155038006.png)

#### 进程、线程、管程

- 进程

  系统中运行的一个应用程序就是一个进程，每一个进程都有它自己的内存空间和系统资源。

- 线程

  也被称为轻量级进程，在同一个进程内基本会有1一个或多个线程，是大多数操作系统进行调度的基本单元。

- 管程

  Monitor（监视器），也就是我们平时说的锁

  Monitor其实是一种同步机制，他的义务是保证（同一时间）只有一个线程可以访问被保护的数据和代码。

  JVM中同步是基于进入和退出监视器对象(Monitor,管程对象)来实现的，每个对象实例都会有一个Monitor对象，

  Monitor对象会和Java对象一同创建并销毁，它底层是由C++语言来实现的。进程VS线程

  

  进程和线程的最大不同在于进程基本上是独立的，而线程不一定，线程共享**方法区和堆**，线程私有**虚拟机栈**、**本地方法栈**和**程序计数器**

### 用户线程和守护线程

#### 用户线程

是系统的工作线程，它会完成这个程序需要完成的业务操作

#### 守护线程

是一种特殊的线程，为其他线程服务的，在后台默默地完成一些系统性的服务，比如垃圾回收线程。

```java
public class DaemonDemo
{
public static void main(String[] args)
{
    Thread t1 = new Thread(() -> {
        System.out.println(Thread.currentThread().getName()+"\t 开始运行，"+(Thread.currentThread().isDaemon() ? "守护线程":"用户线程"));
        while (true) {

        }
    }, "t1");
    //线程的daemon属性为true表示是守护线程，false表示是用户线程
    //---------------------------------------------
    t1.setDaemon(true);
    //-----------------------------------------------
    t1.start();
    //3秒钟后主线程再运行
    try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }

    System.out.println("----------main线程运行完毕");
}

}

```

- 未加`t1.setDaemon(true);`，默认是用户线程，他会继续运行，所以灯亮着
- 加了`t1.setDaemon(true);`是守护线程，当用户线程main方法结束后自动退出了

- 守护线程作为一个服务线程，没有服务对象就没有必要继续运行了，如果用户线程全部结束了，意味着程序需要完成的业务操作已经结束了，系统可退出了。假如当系统只剩下守护线程的时候，java虚拟机会**自动退出**。
- setDaemon(true)方法必须在start()之前设置，否则报IIIegalThreadStateException异常

##  CompletableFuture

### Future和Callable

- Future接口(**FutureTask实现类**)定义了操作**异步任务执行一些方法**，如获取异步任务的执行结果、取消任务的执行、判断任务是否被取消、判断任务执行是否完毕等。（异步：可以被叫停，可以被取消）
- 一句话：Future接口可以为主线程开一个分支任务，专门为主线程处理耗时和费力的复杂业务。

![在这里插入图片描述](D:\notes\note\juc\java并发.assets\fee3f7bf5747426b80fce4c3aeb16aaf.png)

### FutureTask实现类

- 在源码可以看到，他既继承了`RunnableFuture`接口，也在构造方法中实现了`Callable`接口（有返回值、可抛出异常）和`Runnable`接口

![在这里插入图片描述](D:\notes\note\juc\java并发.assets\b7add81201c4466c873593f9a7adbc05.png)

![在这里插入图片描述](D:\notes\note\juc\java并发.assets\0173aadfb388465f9b078eda7d724923.png)

```java
public class CompletableFutureDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<String> futureTask = new FutureTask<>(new MyThread());
        Thread t1 = new Thread(futureTask,"t1");
        t1.start();
        System.out.println(futureTask.get());//接收返回值
    }
}

class MyThread implements Callable<String>{

    @Override
    public String call() throws Exception {
        System.out.println("-----come in call() ----异步执行");
        return "hello Callable 返回值";
    }
}
//结果
//-----come in call() ----异步执行
//hello Callable 返回值


```

### Future到CompletableFuture

#### Future优点

- **future**+**线程池**异步多线程任务配合，能显著提高程序的执行效率。
- 方案一，3个任务1个main线程处理，大概**1130ms**

![在这里插入图片描述](D:\notes\note\juc\java并发.assets\2bfd3b3588e849b3ac9425d708d28da9.png)

#### Future缺点

- **get()阻塞**

  ​	一旦调用get()方法，不管是否计算完成，都会导致阻塞（所以一般get方法放到最后）

- **isDone()轮询**

  ​	利用if(futureTask.isDone())的方式使得他在结束之后才get(),但是也会消耗cpu

#### Future现状

- 回调通知

  前面的**isDone()**方法耗费cpu资源，一般应该还是利用回调函数，在Future结束时自动调用该回调函数。应对Future的完成时间，完成了可以告诉我，也就是我们的回调通知 

- 创建异步任务

  Future+线程池配合
  多个任务前后依赖可以组合处理（水煮鱼）
  想将多个异步任务的计算结果组合起来，后一个异步任务的计算结果需要前一个异步任务的值，将两个或多个异步计算合成一个异步计算，这几个异步计算相互独立，同时后面这个又依赖前一个处理的结果
  比如买鱼-加料-烹饪

- 对计算速度选最快完成的（并返回结果）
  当Future集合中某个任务最快结束时，返回结果，返回第一名处理结果。

### CompletableFuture基本介绍

阻塞的方式和异步编程的设计理念相违背，而轮询的方式会消耗无畏的CPU资源。因此，JDK8设计出CompletableFuture

#### CompletableFuture

```java
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {}
```

![在这里插入图片描述](D:\notes\note\juc\java并发.assets\2b26ca27b971441eb1bb8abad49fb32b.png)

- 在Java 8中， Complet able Future提供了非常强大的Future的扩展功能， 可以帮助我们简化异步编程的复杂性， 并且提供了函数式编程的能力， 可以通过回调的方式处理计算结果， 也提供了转换和组合Complet able Future的方法。
- 它可能代表一个明确完成的Future， 也有可能代表一个完成阶段(Completion Stage) ， 它支持在计算完成以后触发一些函数或执行某些动作。

- 它实现了`Future`和`Completion Stage`接口

#### CompletionStage

- Completion Stage代表异步计算过程中的**某一个阶段**， 一个阶段完成以后可能会触发另外一个阶段
- 一个阶段的计算执行可以是一个Function， Consumer或者Runnable。比如：stage.then Apply(x->square(x) ) .then Accept(x->System.out.print(x) ) .then Run() ->System.out.print In() )，一个阶段的执行可能是被单个阶段的完成触发，也可能是由多个阶段一起触发

#### 核心的四个静态方法（分为两组）

- 利用核心的四个静态方法创建一个异步操作 | 不建议用new
- 关键就是 |有没有返回值|是否用了线程池|
- 参数说明

  - 没有指定Executor的方法，直接使用默认的**ForkJoinPool.commPool()**作为它的线程池执行异步代码。
  - 如果指定线程池，则使用我们定义的或者特别指定的线程池执行异步代码。


##### runAsync无返回值

- runAsync

```java
public static CompletableFuture<Void> runAsync(Runnable runnable
```

```java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Void> voidCompletableFuture = CompletableFuture.runAsync(() -> {
            System.out.println(Thread.currentThread().getName());
            //停顿几秒线程
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        System.out.println(voidCompletableFuture.get());
    }
}
//ForkJoinPool.commonPool-worker-9 //默认的线程池
//null --- 没有返回值

```

- runAsync+线程池

```java
public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)
```

```java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ExecutorService executorService = Executors.newFixedThreadPool(3);//加入线程池

        CompletableFuture<Void> voidCompletableFuture = CompletableFuture.runAsync(() -> {

            System.out.println(Thread.currentThread().getName());
            //停顿几秒线程
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },executorService);
        System.out.println(voidCompletableFuture.get());
    }
}
//pool-1-thread-1   ----指定的线程池
//null ----没有返回值
```

##### supplyAsync有返回值

- supplyAsync

```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) 
```

```java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ExecutorService executorService = Executors.newFixedThreadPool(3);//加入线程池

        CompletableFuture<String> objectCompletableFuture = CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName());
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "helllo supplyasync";
        });
        System.out.println(objectCompletableFuture.get());
    }
}
//ForkJoinPool.commonPool-worker-9---------默认的线程池
//helllo supplyasync-------------supplyasync有返回值了
```

- supplyAsync+线程池

```java
public class CompletableFutureBuildDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ExecutorService executorService = Executors.newFixedThreadPool(3);//加入线程池

        CompletableFuture<String> objectCompletableFuture = CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName());
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "helllo supplyasync";
        },executorService);
        System.out.println(objectCompletableFuture.get());
    }
}
//ForkJoinPool.commonPool-worker-9---------默认的线程池
//helllo supplyasync-------------supplyasync有返回值了
```

#### 使用演示

- `CompletableFuture`可以完成`Future`的功能

```java
public class CompletableFutureUseDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<Object> objectCompletableFuture = CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+"----副线程come in");
            int result = ThreadLocalRandom.current().nextInt(10);//产生一个随机数
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("1秒钟后出结果"+result);
            return result;
        });

        System.out.println(Thread.currentThread().getName()+"线程先去忙其他任务");
        System.out.println(objectCompletableFuture.get());
    }
}
//main线程先去忙其他任务
//ForkJoinPool.commonPool-worker-9----副线程come in
//1秒钟后出结果6
//6

```

- `CompletableFuture`通过`whenComplete`来**减少阻塞和轮询**（自动回调）

```java
public class CompletableFutureUseDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+"--------副线程come in");
            int result = ThreadLocalRandom.current().nextInt(10);//产生随机数
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return result;
        }).whenComplete((v,e) -> {//没有异常,v是值，e是异常
            if(e == null){
                System.out.println("------------------计算完成，更新系统updataValue"+v);
            }
        }).exceptionally(e->{//有异常的情况
            e.printStackTrace();
            System.out.println("异常情况"+e.getCause()+"\t"+e.getMessage());
            return null;
        });

        //线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭：暂停3秒钟线程
        System.out.println(Thread.currentThread().getName()+"线程先去忙其他任务");
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
//ForkJoinPool.commonPool-worker-9--------副线程come in（这里用的是默认的ForkJoinPool）
//main线程先去忙其他任务
//------------------计算完成，更新系统updataValue3

```

- 假如换用自定义线程池

```java
public class CompletableFutureUseDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService threadPool = Executors.newFixedThreadPool(3);
        CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+"--------副线程come in");
            int result = ThreadLocalRandom.current().nextInt(10);//产生随机数
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return result;
        },threadPool).whenComplete((v,e) -> {//没有异常,v是值，e是异常
            if(e == null){
                System.out.println("------------------计算完成，更新系统updataValue"+v);
            }
        }).exceptionally(e->{//有异常的情况
            e.printStackTrace();
            System.out.println("异常情况"+e.getCause()+"\t"+e.getMessage());
            return null;
        });

        //线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭：暂停3秒钟线程
        System.out.println(Thread.currentThread().getName()+"线程先去忙其他任务");
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
//pool-1-thread-1--------副线程come in
//main线程先去忙其他任务
//------------------计算完成，更新系统updataValue6

```

- 异常情况的展示，设置一个异常 `int i = 10 / 0 ;`

```java
public class CompletableFutureUseDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService threadPool = Executors.newFixedThreadPool(3);
        CompletableFuture.supplyAsync(()->{
            System.out.println(Thread.currentThread().getName()+"--------副线程come in");
            int result = ThreadLocalRandom.current().nextInt(10);//产生随机数
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("-----结果---异常判断值---"+result);
            //---------------------异常情况的演示--------------------------------------
            if(result > 2){
                int i  = 10 / 0 ;//我们主动的给一个异常情况
            }
            //------------------------------------------------------------------
            return result;
        },threadPool).whenComplete((v,e) -> {//没有异常,v是值，e是异常
            if(e == null){
                System.out.println("------------------计算完成，更新系统updataValue"+v);
            }
        }).exceptionally(e->{//有异常的情况
            e.printStackTrace();
            System.out.println("异常情况"+e.getCause()+"\t"+e.getMessage());
            return null;
        });

        //线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭：暂停3秒钟线程
        System.out.println(Thread.currentThread().getName()+"线程先去忙其他任务");
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
//pool-1-thread-1--------副线程come in
//main线程先去忙其他任务
//-----结果---异常判断值---4                (这里 4 >2了，直接抛出异常)
//异常情况java.lang.ArithmeticException: / by zero  java.lang.ArithmeticException: / by zero
//java.util.concurrent.CompletionException: java.lang.ArithmeticException: / by zero
//  at java.util.concurrent.CompletableFuture.encodeThrowable(CompletableFuture.java:273)
//  at java.util.concurrent.CompletableFuture.completeThrowable(CompletableFuture.java:280)
//  at java.util.concurrent.CompletableFuture$AsyncSupply.run(CompletableFuture.java:1592)
//  at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
//  at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
//  at java.lang.Thread.run(Thread.java:748)
//Caused by: java.lang.ArithmeticException: / by zero
//  at com.zhang.admin.controller.CompletableFutureUseDemo.lambda$main$0(CompletableFutureUseDemo.java:19)
//  at java.util.concurrent.CompletableFuture$AsyncSupply.run(CompletableFuture.java:1590)
//  ... 3 more

```

#### 优点总结

- 异步任务结束时，会**自动回调**某个对象的方法；
- 主线程设置好毁掉后，不再关心异步任务的执行，异步任务之间可以顺序执行
- 异步任务出错时，会自动回调某个对象的方法。

函数式接口

任何**接口**，如果只包含**唯一一个**抽象方法，那么它就是一个**函数式接口**。对于函数式接口，我们可以通过**lambda表达式**来创建该接口的对象。

常见的函数式接口：

1. Runnable

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

2. Function

```java
@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);
}
```

3. Consumer

```java
@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);
}

```

4. Supplier

```java
@FunctionalInterface
public interface Supplier<T> {

    /**
     * Gets a result.
     *
     * @return a result
     */
    T get();
}

```

5. Biconsumer(Bi代表两个的意思，我们要传入两个参数，在上面的案例中是v和e)

```java
@FunctionalInterface
public interface BiConsumer<T, U> {
    void accept(T t, U u);

}

```

| 函数式接口名称 | 方法名称 | 参数     | 返回值   |
| -------------- | -------- | -------- | -------- |
| Runnable       | run      | 无参数   | 无返回值 |
| Function       | apply    | 1个参数  | 有返回值 |
| Consume        | accept   | 1个参数  | 无返回值 |
| Supplier       | get      | 没有参数 | 有返回值 |
| Biconsumer     | accept   | 2个参数  | 无返回值 |

### 常用API

获得结果和触发计算

- 获取结果
  - **public T get()** 不见不散，容易阻塞
  - **public T get(long timeout,TimeUnit unit)** 过时不候，超过时间会爆异常
  - **public T join()** 类似于get()，区别在于是否需要抛出异常
  - **public T getNow(T valueIfAbsent)**
    - 没有计算完成的情况下，给一个替代结果
    - 立即获取结果不阻塞
      - 计算完，返回计算完成后的结果
      - 没算完，返回设定的valueAbsent(直接返回了备胎值xxx)
- 主动触发计算
  - `public boolean complete(T value) `是否立即打断get()方法返回括号值
    - (执行要2s，等待只有1s，所以还没执行完就被打断了。返回true表示打断了获取这个过程，直接返回了备胎值complete；如果没打断，返回false 和原来的abc)

```java
public class CompletableFutureAPIDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        CompletableFuture<String> uCompletableFuture = CompletableFuture.supplyAsync(() -> {
            try {
                TimeUnit.SECONDS.sleep(2);//执行需要2秒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "abc";
        });

        try {
            TimeUnit.SECONDS.sleep(1);//等待需要1秒
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
       // System.out.println(uCompletableFuture.getNow("xxx"));//执2-等1 返回xxx
        System.out.println(uCompletableFuture.complete("completeValue")+"\t"+uCompletableFuture.get());//执2-等1 返回true+备胎值completeValue
    }
}

```

#### 对计算结果进行处理

- `thenApply `计算结果存在在依赖关系，使得线程串行化。因为依赖关系，所以一旦有异常，直接叫停。

```java
public class CompletableFutureDemo2
{
public static void main(String[] args) throws ExecutionException, InterruptedException
{
    //当一个线程依赖另一个线程时用 thenApply 方法来把这两个线程串行化,
    CompletableFuture.supplyAsync(() -> {
        //暂停几秒钟线程
        try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
        System.out.println("111");
        return 1024;
    }).thenApply(f -> {
        System.out.println("222");
        return f + 1;
    }).thenApply(f -> {
        //int age = 10/0; // 异常情况：那步出错就停在那步。
        System.out.println("333");
        return f + 1;
    }).whenCompleteAsync((v,e) -> {
        System.out.println("*****v: "+v);
    }).exceptionally(e -> {
        e.printStackTrace();
        return null;
    });

    System.out.println("-----主线程结束，END");

    // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
    try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
}
}
//-----正常情况
//111
//222
//333
//----计算结果： 6

//-----异常情况
//111
//异常.....

```

- `handle `类似于thenApply，但是有异常的话仍然可以往下走一步。

```java
public class CompletableFutureDemo2
{

    public static void main(String[] args) throws ExecutionException, InterruptedException
    {
        //当一个线程依赖另一个线程时用 handle 方法来把这两个线程串行化,
        // 异常情况：有异常也可以往下一步走，根据带的异常参数可以进一步处理
        CompletableFuture.supplyAsync(() -> {
            //暂停几秒钟线程
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            System.out.println("111");
            return 1024;
        }).handle((f,e) -> {
            int age = 10/0;//异常语句
            System.out.println("222");
            return f + 1;
        }).handle((f,e) -> {
            System.out.println("333");
            return f + 1;
        }).whenCompleteAsync((v,e) -> {
            System.out.println("*****v: "+v);
        }).exceptionally(e -> {
            e.printStackTrace();
            return null;
        });

        System.out.println("-----主线程结束，END");

        // 主线程不要立刻结束，否则CompletableFuture默认使用的线程池会立刻关闭:
        try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
    }
}
//-----异常情况
//111
//333
//异常，可以看到多走了一步333

```

#### 对计算结果进行消费

- 接收任务的处理结果，并**消费处理，无返回结果**|**消费型函数式接口**，之前的是Function
- thenAccept

```java
public static void main(String[] args) throws ExecutionException, InterruptedException
{
    CompletableFuture.supplyAsync(() -> {
        return 1;
    }).thenApply(f -> {
        return f + 2;
    }).thenApply(f -> {
        return f + 3;
    }).thenApply(f -> {
        return f + 4;
    }).thenAccept(r -> System.out.println(r));
}
//6
//消费一下，直接得到6

```

> 补充：
>
> - `thenRun`
>   - thenRun(Runnable runnable)
>   - 任务A执行完执行B，并且B不需要A的结果
> - `thenAccept`
>   - thenAccept(Consumer action)
>   - 任务A执行完执行B，B需要A的结果，但是任务B无返回值
> - `thenApply`
>   - thenApply(Function fn)
>   - 任务A执行完执行B，B需要A的结果，同时任务B有返回值
>
> ```java
>  
> System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenRun(() -> {}).join());
> //null 
> 
> System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenAccept(resultA -> {}).join());
> //resultA打印出来的 null因为没有返回值
> 
> System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenApply(resultA -> resultA + " resultB").join());
> //resultAresultB 返回值
> ```

#### CompleteFuture和线程池说明（非常重要）

- 上面的几个方法都有普通版本和**后面加Async**的版本

- 以`thenRun`和`thenRunAsync`为例，有什么区别？

- 先看结论

  1. 没有传入自定义线程池，都用默认线程池ForkJoinPool
  2. 传入了一个自定义线程池如果你执行第一个任务的时候，传入了一个自定义线程池
     - 调用`thenRun`方法执行第二个任务的时候，则第二个任务和第一个任务是用同一个线程池
     - 调用`thenRunAsync`执行第二个任务的时候，则第一个任务使用的是**你自己**传入的线程池，第二个任务使用的是**ForkJoin线程池**
  3. 也有可能处理太快，系统优化切换原则，直接使用main线程处理（把sleep去掉）

  ```java
  //2-1
  public class CompletableFutureAPIDemo {
      public static void main(String[] args) throws ExecutionException, InterruptedException {
          ExecutorService threadPool = Executors.newFixedThreadPool(5);
          CompletableFuture<Void> completableFuture = CompletableFuture.supplyAsync(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("1号任务"+"\t"+Thread.currentThread().getName());
              return "abcd";
          },threadPool).thenRun(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("2号任务"+"\t"+Thread.currentThread().getName());
          }).thenRun(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("3号任务"+"\t"+Thread.currentThread().getName());
          }).thenRun(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("4号任务"+"\t"+Thread.currentThread().getName());
          });
      }
  }
  //1号任务  pool-1-thread-1
  //2号任务  pool-1-thread-1
  //3号任务  pool-1-thread-1
  //4号任务  pool-1-thread-1
  ```

  ```java
  //2-2
  public class CompletableFutureAPIDemo {
      public static void main(String[] args) throws ExecutionException, InterruptedException {
          ExecutorService threadPool = Executors.newFixedThreadPool(5);
          CompletableFuture<Void> completableFuture = CompletableFuture.supplyAsync(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("1号任务"+"\t"+Thread.currentThread().getName());
              return "abcd";
          },threadPool).thenRunAsync(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("2号任务"+"\t"+Thread.currentThread().getName());
          }).thenRun(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("3号任务"+"\t"+Thread.currentThread().getName());
          }).thenRun(()->{
              try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("4号任务"+"\t"+Thread.currentThread().getName());
          });
      }
  }
  //1号任务  pool-1-thread-1
  //2号任务  ForkJoinPool.commonPool-worker-9---这里另起炉灶重新调用了默认的ForkJoinPool
  //3号任务  ForkJoinPool.commonPool-worker-9
  //4号任务  ForkJoinPool.commonPool-worker-9
  
  ```

  ```java
  public class CompletableFutureAPIDemo {
      public static void main(String[] args) throws ExecutionException, InterruptedException {
          ExecutorService threadPool = Executors.newFixedThreadPool(5);
          CompletableFuture<Void> completableFuture = CompletableFuture.supplyAsync(()->{
  //            try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("1号任务"+"\t"+Thread.currentThread().getName());
              return "abcd";
          },threadPool).thenRun(()->{
             // try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("2号任务"+"\t"+Thread.currentThread().getName());
          }).thenRun(()->{
            //  try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("3号任务"+"\t"+Thread.currentThread().getName());
          }).thenRun(()->{
              //try {TimeUnit.MILLISECONDS.sleep(20);} catch (InterruptedException e) {e.printStackTrace();}
              System.out.println("4号任务"+"\t"+Thread.currentThread().getName());
          });
      }
  }
  //1号任务  1号任务  pool-1-thread-1
  //2号任务  main
  //3号任务  main
  //4号任务  main
  
  ```

- 源码

```java
//CompletableFuture.java 2009行
public CompletableFuture<Void> thenRun(Runnable action) {//传入值是一样的
        return uniRunStage(null, action);
    }

    public CompletableFuture<Void> thenRunAsync(Runnable action) {
        return uniRunStage(asyncPool, action);//但是这里有个异步的线程池asyncPool
    }





    //进入asyncPool
    private static final boolean useCommonPool =
        (ForkJoinPool.getCommonPoolParallelism() > 1);//一般大于1都是成立的

    /**
     * Default executor -- ForkJoinPool.commonPool() unless it cannot
     * support parallelism.
     */
    private static final Executor asyncPool = useCommonPool ?
        ForkJoinPool.commonPool() : new ThreadPerTaskExecutor();//所以这里会调用forkJoin线程池

```

#### 对计算速度进行选用

- `applyToEither`方法，快的那个掌权

```java
public class CompletableFutureDemo2 {
    public static void main(String[] args) throws ExecutionException, InterruptedException
    {
        CompletableFuture<String> play1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            //暂停几秒钟线程
            try { TimeUnit.SECONDS.sleep(2); } catch (InterruptedException e) { e.printStackTrace(); }
            return "play1 ";
        });

        CompletableFuture<String> play2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            return "play2";
        });

        CompletableFuture<String> thenCombineResult = play1.applyToEither(play2, f -> {//对计算速度进行选用
            return f + " is winner";
        });

        System.out.println(Thread.currentThread().getName() + "\t" + thenCombineResult.get());
    }
}
//ForkJoinPool.commonPool-worker-9  ---come in 
//ForkJoinPool.commonPool-worker-2  ---come in 
//main  play2 is winner

```

#### 对计算结果进行合并

- `thenCombine` 合并
  - 两个CompletionStage任务都完成后，最终能把两个任务的结果一起交给thenCombine来处理
  - 先完成的先等着，等待其它分支任务

```java
public class CompletableFutureDemo2
{
    public static void main(String[] args) throws ExecutionException, InterruptedException
    {
        CompletableFuture<Integer> completableFuture1 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            return 10;
        });

        CompletableFuture<Integer> completableFuture2 = CompletableFuture.supplyAsync(() -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            return 20;
        });

        CompletableFuture<Integer> thenCombineResult = completableFuture1.thenCombine(completableFuture2, (x, y) -> {
            System.out.println(Thread.currentThread().getName() + "\t" + "---come in ");
            return x + y;
        });
        
        System.out.println(thenCombineResult.get());
    }
}
//30

```

## java锁

### 乐观锁和悲观锁

#### 悲观锁

- **悲观锁**认为自己在使用数据的时候一定有别的线程来修改数据，因此在获取数据的时候会**先加锁**，确保数据不会被别的线程修改。
- 悲观锁的**实现方式**
  - `synchronized`关键字
  - `Lock`的实现类都是悲观锁
- 适合写操作多的场景，先加锁可以保证写操作时数据正确。显示的锁定之后再操作同步资源。

```java
//=============悲观锁的调用方式
public synchronized void m1()
{
    //加锁后的业务逻辑......
}

// 保证多个线程使用的是同一个lock对象的前提下
ReentrantLock lock = new ReentrantLock();
public void m2() {
    lock.lock();
    try {
        // 操作同步资源
    }finally {
        lock.unlock();
    }
}

```

#### 乐观锁

- **乐观锁**认为自己在使用数据时不会有别的线程修改数据，所以**不会添加锁**，只是在更新数据的时候去**判断之前有没有别的线程更新了这个数据**。如果这个数据没有被更新，当前线程将自己修改的数据成功写入。如果数据已经被其他线程更新，则根据不同的实现方式执行不同的操作
- 乐观锁的**实现方式**
  1. 版本号机制Version。（只要有人提交了就会修改版本号，可以解决ABA问题）
     - **ABA问题**：再CAS中想读取一个值A，想把值A变为C，不能保证读取时的A就是赋值时的A，中间可能有个线程将A变为B再变为A。
     - **解决方法**：Juc包提供了一个`AtomicStampedReference`，原子更新带有版本号的引用类型，通过控制版本值的变化来解决ABA问题。
  2. 最常采用的是CAS算法，Java原子类中的递增操作就通过CAS自旋实现的。
- 适合读操作多的场景，不加锁的性能特点能够使其操作的性能大幅提升。

```java
//=============乐观锁的调用方式
// 保证多个线程使用的是同一个AtomicInteger
private AtomicInteger atomicInteger = new AtomicInteger();
atomicInteger.incrementAndGet();

```

### 8锁案例

```java
class Phone{
    public static synchronized void sendEmail(){
        try {TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}
        System.out.println("-------------sendEmail");

    }
    public  synchronized void sendSMS(){//static
        System.out.println("-------------sendSMS");
    }
    public void hello(){
        System.out.println("-------------hello");
    }
}

/**
 * - 题目：谈谈你对多线程锁的理解，8锁案例说明
 * - 口诀：线程 操作 资源类
 * 1. 标准访问有ab两个线程，请问先打印邮件还是短信?邮件
 * 2. a里面故意停3秒？邮件
 * 3. 添加一个普通的hello方法，请问先打印邮件还是hello？hello
 * 4. 有两部手机，请问先打印邮件（这里有个3秒延迟）还是短信?短信
 * 5.有两个静态同步方法（synchroized前加static,3秒延迟也在），有1部手机，先打印邮件还是短信？邮件
 * 6.两个手机，有两个静态同步方法（synchroized前加static,3秒延迟也在），有1部手机，先打印邮件还是短信？邮件
 * 7.一个静态同步方法，一个普通同步方法，请问先打印邮件还是手机？短信
 * 8.两个手机，一个静态同步方法，一个普通同步方法，请问先打印邮件还是手机？短信
 */
public class lock8 {
    public static void main(String[] args) {
        Phone phone = new Phone();
        Phone phone2 = new Phone();
        new Thread(()->{
            phone.sendEmail();
        },"a").start();

        //暂停毫秒，保证a线程先启动
        try {TimeUnit.MILLISECONDS.sleep(200);} catch (InterruptedException e) {e.printStackTrace();}

        new Thread(()->{
            phone.sendSMS();
        },"b").start();
    }
}

```

#### 原理

- 1.2中
  - 一个对象里面如果有多个synchronized方法，某一时刻内，只要一个线程去调用其中的一个synchronized方法了，其他的线程都只能是等待，换句话说，某一个时刻内，只能有唯一的一个线程去访问这些synchronized方法，**锁的是当前对象this**，被锁定后，其它的线程都不能 进入到当前对象的其他synchronized方法
- 3中
  - hello并未和其他`synchronized`修饰的方法产生争抢
- 4 中
  - 锁在两个不同的对象/两个不同的资源上，不产生竞争条件
- 5.6中`static`+`synchronized` - **类锁** ****` phone = new Phone();`中 加到了左边的Phone上
  - 对于普通同步方法，锁的是**当前实例对象**，通常指this，具体的一部部手机，所有的普通同步方法用的都是同一把锁→实例对象本身。
  - 对于**静态**同步方法，锁的是当前**类的Class对象**，如Phone，class唯一的一个模板。
  - 对于同步方法块，锁的是synchronized**括号内的对象**。`synchronized(o)`
- 7.8中一个加了**对象锁**，一个加了**类锁**，不产生竞争条件

#### 8锁-3个体现

8种锁的案例实际体现在3个地方-相当于总结：

- 作用域**实例方法**，当前实例加锁，进入同步代码块前要获得当前实例的锁。
- 作用于**代码块**，对括号里配置的对象加锁。
- 作用于**静态方法**，当前类加锁，进去同步代码前要获得当前类对象的锁

### 字节码角度分析synchronized实现

#### 反编译

- 文件反编译`javap -c ***.class`文件反编译，-c表示对代码进行反汇编
- 假如需要更多信息 `javap -v ***.class` ，-v即-verbose输出附加信息（包括行号、本地变量表、反汇编等详细信息）

#### 同步代码块

```java
/**
 * 锁同步代码块
 */
public class LockSyncDemo {
    Object object = new Object();

    public void m1(){
        synchronized (object){
            System.out.println("-----hello synchronized code block");
        }
    }

    public static void main(String[] args) {

    }
}

```

从target中找到LockSyncDemo.class文件，右键，open in terminal，然后`javap -c LockSyncDemo.class`

```java
public class com.zhang.admin.controller.LockSyncDemo {
  java.lang.Object object;

  public com.zhang.admin.controller.LockSyncDemo();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: aload_0
       5: new           #2                  // class java/lang/Object
       8: dup
       9: invokespecial #1                  // Method java/lang/Object."<init>":()V
      12: putfield      #3                  // Field object:Ljava/lang/Object;
      15: return

  public void m1();
    Code:
       0: aload_0
       1: getfield      #3                  // Field object:Ljava/lang/Object;
       4: dup
       5: astore_1
       6: monitorenter        //**注****------进入锁
       7: getstatic     #4                  // Field java/lang/System.out:Ljava/io/PrintStream;
      10: ldc           #5                  // String -----hello synchronized code block
      12: invokevirtual #6                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      15: aload_1
      16: monitorexit        // **注**------退出锁
      17: goto          25
      20: astore_2
      21: aload_1
      22: monitorexit        //**注**-----这里又有一个exit???????以防出现异常，保证能够释放锁
      23: aload_2
      24: athrow
      25: return
    Exception table:
       from    to  target type
           7    17    20   any
          20    23    20   any

  public static void main(java.lang.String[]);
    Code:
       0: return
}


```

总结

- synchronized同步代码块，实现使用的是`moniterenter`和`moniterexit`指令（`moniterexit`可能有两个）
- 那一定是一个enter两个exit吗？（不一样，如果主动throw一个RuntimeException，发现一个enter，一个exit，还有两个athrow）

#### synchronized普通同步方法

```java
/**
 * 锁普通的同步方法
 */
public class LockSyncDemo {

    public synchronized void m2(){
        System.out.println("------hello synchronized m2");
    }

    public static void main(String[] args) {

    }
}

```

```java
.....
public synchronized void m2();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_SYNCHRONIZED //请注意该标志
    Code:
      stack=2, locals=1, args_size=1
         0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #3                  // String ------hello synchronized m2
         5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 11: 0
        line 12: 8
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       9     0  this   Lcom/zhang/admin/controller/LockSyncDemo;
......

```

- 总结
  - 调用指令将会检查方法的****访问标志是否被设置。如果设置了，执行线程会将先持有monitore然后再执行方法，最后在方法完成（无论是正常完成还是非正常完成）时释放monitor

#### synchronized静态同步方法

```java
/**
 * 锁静态同步方法
 */
public class LockSyncDemo {

    public synchronized void m2(){
        System.out.println("------hello synchronized m2");
    }

    public static synchronized void m3(){
        System.out.println("------hello synchronized m3---static");
    }


    public static void main(String[] args) {

    }
}


```

```java
 ......
 public static synchronized void m3();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_STATIC, ACC_SYNCHRONIZED //访问标志 区分该方法是否是静态同步方法
    Code:
      stack=2, locals=0, args_size=0
         0: getstatic     #2                  // Field java/lang/System.out:Ljava/io/PrintStream;
         3: ldc           #5                  // String ------hello synchronized m3---static
         5: invokevirtual #4                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
         8: return
      LineNumberTable:
        line 15: 0
        line 16: 8
......

```

- 总结
  - **ACC_STATIC**访问标志区分该方法是否是静态同步方法。