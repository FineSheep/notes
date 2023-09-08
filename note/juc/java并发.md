---
视频：尚硅谷周阳
笔记来源：https://blog.csdn.net/dolpin_ink/category_11847910.html
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

