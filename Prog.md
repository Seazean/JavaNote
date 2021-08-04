# JUC

## 进程

### 概述

进程：程序是静止的，进程实体的运行过程就是进程，是系统进行资源分配的基本单位

进程的特征：并发性、异步性、动态性、独立性、结构性

**线程**：线程是属于进程的，是一个基本的 CPU 执行单元，是程序执行流的最小单元。线程是进程中的一个实体，是被系统独立调度和分配的基本单位，线程本身不拥有系统资源，只拥有一点在运行中必不可少的资源，但它可与同属一个进程的其他线程共享进程所拥有的全部资源。

关系：一个进程可以包含多个线程，这就是多线程，比如看视频是进程，图画、声音、广告等就是多个线程

线程的作用：使多道程序更好的并发执行，提高资源利用率和系统吞吐量，增强操作系统的并发性能

**并发并行**：

* 并行：在同一时刻，有多个指令在多个 CPU 上同时执行
* 并发：在同一时刻，有多个指令在单个 CPU 上交替执行

**同步异步**：

* 需要等待结果返回，才能继续运行就是同步
* 不需要等待结果返回，就能继续运行就是异步



参考视频：https://www.bilibili.com/video/BV16J411h7Rd（推荐观看）

笔记的整体内容依据视频编写，并且随着不断的学习补充了很多新知识



***



### 对比

线程进程对比：

* 进程基本上相互独立的，而线程存在于进程内，是进程的一个子集

* 进程拥有共享的资源，如内存空间等，供其内部的线程共享

* 进程间通信较为复杂

  同一台计算机的进程通信称为 IPC（Inter-process communication）

  * 信号量：信号量是一个计数器，用于多进程对共享数据的访问，解决同步相关的问题并避免竞争条件
  * 共享存储：多个进程可以访问同一块内存空间，需要使用信号量用来同步对共享存储的访问
  * 管道通信：管道是用于连接一个读进程和一个写进程以实现它们之间通信的一个共享文件，pipe文件
    * 匿名管道(Pipes) ：用于具有亲缘关系的父子进程间或者兄弟进程之间的通信，只支持半双工通信
    * 命名管道(Names Pipes)：以磁盘文件的方式存在，可以实现本机任意两个进程通信，遵循FIFO
  * 消息队列：内核中存储消息的链表，由消息队列标识符标识，能在不同进程之间提供全双工通信，对比管道：
    * 匿名管道存在于内存中的文件；命名管道存在于实际的磁盘介质或者文件系统；消息队列存放在内核中，只有在内核重启（操作系统重启）或者显示地删除一个消息队列时，该消息队列才被真正删除
    * 读进程可以根据消息类型有选择地接收消息，而不像 FIFO 那样只能默认地接收

  不同计算机之间的进程通信，需要通过网络，并遵守共同的协议，例如 HTTP

  * 套接字：与其它通信机制不同的是，它可用于不同机器间的进程通信

* 线程通信相对简单，因为它们共享进程内的内存，一个例子是多个线程可以访问同一个共享变量

  Java中的通信机制：volatile、等待/通知机制、join方式、InheritableThreadLocal、MappedByteBuffer

* 线程更轻量，线程上下文切换成本一般上要比进程上下文切换低





***





## 线程

### 创建线程

#### 三种方式

运行一个程序就是开启一个进程，在进程中创建线程的方式有三种:

1. 直接定义一个类继承线程类 Thread，重写 run() 方法，创建线程对象，调用线程对象的 start() 方法启动线程
2. 定义一个线程任务类实现 Runnable 接口，重写 run() 方法，创建线程任务对象，把线程任务对象包装成线程对象， 调用线程对象的 start() 方法启动线程
3. 实现 Callable 接口



***



#### Thread

Thread创建线程方式：创建线程类，匿名内部类方式

* **start() 方法底层其实是给 CPU 注册当前线程，并且触发 run() 方法执行**
* 线程的启动必须调用 start() 方法，如果线程直接调用 run() 方法，相当于变成了普通类的执行，此时将只有主线程在执行该线程
* 建议线程先创建子线程，主线程的任务放在之后，否则主线程（main）永远是先执行完

Thread构造器：

* `public Thread()`
* `public Thread(String name)`

```java
puclic class ThreadDemo{
    public static void main(String[] args) {
        Thread t = new MyThread();
        t.start();
       	for(int i = 0 ; i < 100 ; i++ ){
            System.out.println("main线程"+i)
        }
        //   main线程输出放在上面 就变成有先后顺序了
    }
}
class MyThread extends Thread{
    @Override
    public void run() {
        for(int i = 0 ; i < 100 ; i++ ){
            System.out.println("子线程输出："+i)
        }
    }
}
```

继承 Thread 类的优缺点：

* 优点：编码简单
*  缺点：线程类已经继承了Thread类无法继承其他类了，功能不能通过继承拓展（单继承的局限性）



***



#### Runnable

Runnable创建线程方式：创建线程类，匿名内部类方式

**Thread类本身也是实现了Runnable接口**

Thread的构造器：

* `public Thread(Runnable target)`
* `public Thread(Runnable target, String name)`

```java
public class ThreadDemo {
    public static void main(String[] args) {
        Runnable target = new MyRunnable();
        Thread t1 = new Thread(target,"1号线程");
		t1.start();
        Thread t2 = new Thread(target);//Thread-0
    }
}

public class MyRunnable implements Runnable{
    @Override
    public void run() {
        for(int i = 0 ; i < 10 ; i++ ){
            System.out.println(Thread.currentThread().getName()+"->"+i);
        }
    }
}
```

* 缺点：代码复杂一点。

* 优点：

  1. 线程任务类只是实现了Runnable接口，可以继续继承其他类，避免了单继承的局限性

  2. 同一个线程任务对象可以被包装成多个线程对象

  3. 适合多个多个线程去共享同一个资源

  4. 实现解耦操作，线程任务代码可以被多个线程共享，线程任务代码和线程独立

  5. 线程池可以放入实现Runnable或Callable线程任务对象

​     

****



#### Callable

实现 Callable 接口：

1. 定义一个线程任务类实现 Callable 接口，申明线程执行的结果类型
2. 重写线程任务类的 call 方法，这个方法可以直接返回执行的结果
3. 创建一个 Callable 的线程任务对象
4. 把 Callable 的线程任务对象包装成一个未来任务对象
5. 把未来任务对象包装成线程对象
6. 调用线程的 start() 方法启动线程

`public FutureTask(Callable<V> callable)`：未来任务对象，在线程执行完后**得到线程的执行结果**

* 其实就是 Runnable 对象，这样被包装成未来任务对象

`public V get()`：同步等待 task 执行完毕的结果

* 如果在线程中获取另一个线程执行结果，会阻塞等待，用于线程同步

优缺点：

* 优点：同 Runnable，并且能得到线程执行的结果
* 缺点：编码复杂

```java
public class ThreadDemo {
    public static void main(String[] args) {
        Callable call = new MyCallable();
        FutureTask<String> task = new FutureTask<>(call);
        Thread t = new Thread(task);
        t.start();
        try {
            String s = task.get(); // 获取call方法返回的结果（正常/异常结果）
            System.out.println(s);
        }  catch (Exception e) {
            e.printStackTrace();
        }
    }

public class MyCallable implements Callable<String> {
    @Override//重写线程任务类方法
    public String call() throws Exception {
        return Thread.currentThread().getName() + "->" + "Hello World";
    }
}
```



***



### 运行原理

JVM 中由堆、栈、方法区所组成

Java Virtual Machine Stacks（Java 虚拟机栈）：每个线程启动后，虚拟机就会为其分配一块栈内存

* 每个栈由多个栈帧（Frame）组成，对应着每次方法调用时所占用的内存
* 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法

线程上下文切换（Thread Context Switch）：一些原因导致 cpu 不再执行当前线程，转而执行另一个线程

* 线程的 cpu 时间片用完
* 垃圾回收
* 有更高优先级的线程需要运行
* 线程自己调用了 sleep、yield、wait、join、park、synchronized、lock 等方法

程序计数器（Program Counter Register）：记住下一条 JVM 指令的执行地址，是线程私有的

当 Context Switch 发生时，需要由操作系统保存当前线程的状态，并恢复另一个线程的状态，包括程序计数器、虚拟机栈中每个栈帧的信息，如局部变量、操作数栈、返回地址等

Java 创建的线程是内核级线程，**线程的调度是在内核态运行的，而线程中的代码是在用户态运行**，所以线程切换（状态改变）会导致用户与内核态转换，这是非常消耗性能

Java 中 main 方法启动的是一个进程也是一个主线程，main 方法里面的其他线程均为子线程



***



### 线程API

#### API

Thread 类 API：

| 方法                                        | 说明                                                         |
| ------------------------------------------- | ------------------------------------------------------------ |
| public void start()                         | 启动一个新线程；Java虚拟机调用此线程的run方法                |
| public void run()                           | 线程启动后调用该方法                                         |
| public void setName(String name)            | 给当前线程取名字                                             |
| public void getName()                       | 获取当前线程的名字<br />线程存在默认名称：子线程是Thread-索引，主线程是main |
| public static Thread currentThread()        | 获取当前线程对象，代码在哪个线程中执行                       |
| public static void sleep(long time)         | 让当前线程休眠多少毫秒再继续执行<br />**Thread.sleep(0)** : 让操作系统立刻重新进行一次cpu竞争 |
| public static native void yield()           | 提示线程调度器让出当前线程对CPU的使用                        |
| public final int getPriority()              | 返回此线程的优先级                                           |
| public final void setPriority(int priority) | 更改此线程的优先级，常用1 5 10                               |
| public void interrupt()                     | 中断这个线程，异常处理机制                                   |
| public static boolean interrupted()         | 判断当前线程是否被打断，清除打断标记                         |
| public boolean isInterrupted()              | 判断当前线程是否被打断，不清除打断标记                       |
| public final void join()                    | 等待这个线程结束                                             |
| public final void join(long millis)         | 等待这个线程死亡millis毫秒，0意味着永远等待                  |
| public final native boolean isAlive()       | 线程是否存活（还没有运行完毕）                               |
| public final void setDaemon(boolean on)     | 将此线程标记为守护线程或用户线程                             |



***



#### run start

run：称为线程体，包含了要执行的这个线程的内容，方法运行结束，此线程随即终止。直接调用 run 是在主线程中执行了 run，没有启动新的线程，需要顺序执行

start：使用 start 是启动新的线程，此线程处于就绪（可运行）状态，通过新的线程间接执行 run 中的代码

说明：**线程控制资源类**

**面试问题**：run() 方法中的异常不能抛出，只能 try/catch

* 因为父类中没有抛出任何异常，子类不能比父类抛出更多的异常
* 异常不能跨线程传播回 main() 中，因此必须在本地进行处理



***



#### sleep yield

sleep：

* 调用 sleep 会让当前线程从 Running 进入 `Timed Waiting` 状态（阻塞）
* sleep() 方法的过程中，线程不会释放对象锁
* 其它线程可以使用 interrupt 方法打断正在睡眠的线程，这时 sleep 方法会抛出 InterruptedException
* 睡眠结束后的线程未必会立刻得到执行
* 建议用 TimeUnit 的 sleep 代替 Thread 的 sleep 来获得更好的可读性

yield：

* 调用 yield 会让提示线程调度器让出当前线程对CPU的使用
* 具体的实现依赖于操作系统的任务调度器
* **会放弃 CPU 资源，锁资源不会释放**



***



#### priority

线程优先级会提示（hint）调度器优先调度该线程，但这仅仅是一个提示，调度器可以忽略它

如果 cpu 比较忙，那么优先级高的线程会获得更多的时间片，但 cpu 闲时，优先级几乎没作用



***



#### join

public final void join()：等待这个线程结束

原理：调用者轮询检查线程 alive 状态，t1.join()等价于：

```java
synchronized (t1) {
    // 调用者线程进入 t1 的 waitSet 等待, 直到 t1 运行结束
    while (t1.isAlive()) {
    	t1.wait(0);
    }
}
```

* join 方法是被 synchronized 修饰的，本质上是一个对象锁，其内部的 wait 方法调用也是释放锁的，但是**释放的是当前线程的对象锁，而不是外面的锁**

* t1 会强占 CPU 资源，直至线程执行结束，当调用某个线程的 join 方法后，该线程抢占到 CPU 资源，就不再释放，直到线程执行完毕

线程同步：

* join 实现线程同步，因为会阻塞等待另一个线程的结束，才能继续向下运行
  * 需要外部共享变量，不符合面向对象封装的思想
  * 必须等待线程结束，不能配合线程池使用
* Future 实现（同步）：get() 方法阻塞等待执行结果
  * main 线程接收结果
  * get 方法是让调用线程同步等待

```java
public class Test {
    static int r = 0;
    public static void main(String[] args) throws InterruptedException {
        test1();
    }
    private static void test1() throws InterruptedException {
        Thread t1 = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            r = 10;
        });
        t1.start();
        t1.join();//不等待线程执行结束，输出的10
        System.out.println(r);
    }
}
```



***



#### interrupt

##### 打断线程

`public void interrupt()`：中断这个线程，异常处理机制
`public static boolean interrupted()`：判断当前线程是否被打断，，打断返回 true，清除打断标记
`public boolean isInterrupted()`：判断当前线程是否被打断，不清除打断标记

* sleep，wait，join 方法都会让线程进入阻塞状态，打断进程**会清空打断状态** (false)

  ```java
  public static void main(String[] args) throws InterruptedException {
      Thread t1 = new Thread(()->{
          try {
              Thread.sleep(1000);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
      }, "t1");
      t1.start();
      Thread.sleep(500);
      t1.interrupt();
      System.out.println(" 打断状态: {}" + t1.isInterrupted());// 打断状态: {}false
  }
  ```

* 打断正常运行的线程：不会清空打断状态（true）

  ```java
  public static void main(String[] args) throws Exception {
      Thread t2 = new Thread(()->{
          while(true) {
              Thread current = Thread.currentThread();
              boolean interrupted = current.isInterrupted();
              if(interrupted) {
                  System.out.println(" 打断状态: {}" + interrupted);//打断状态: {}true
                  break;
              }
          }
      }, "t2");
      t2.start();
      Thread.sleep(500);
      t2.interrupt();
  }
  ```



***



##### 打断park

park 作用类似 sleep，打断 park 线程，不会清空打断状态（true）

```java
public static void main(String[] args) throws Exception {
    Thread t1 = new Thread(() -> {
        System.out.println("park...");
        LockSupport.park();
        System.out.println("unpark...");
        Sout("打断状态：" + Thread.currentThread().isInterrupted());//打断状态：true
    }, "t1");
    t1.start();
    Thread.sleep(2000);
    t1.interrupt();
}
```

如果打断标记已经是 true, 则 park 会失效，

```java
LockSupport.park();
System.out.println("unpark...");
LockSupport.park();//失效，不会阻塞
System.out.println("unpark...");//和上一个unpark同时执行
```

可以修改获取打断状态方法，使用 `Thread.interrupted()`，清除打断标记

LockSupport 类在 同步 → park-un 详解



***



##### 终止模式

终止模式之两阶段终止模式：Two Phase Termination

目标：在一个线程 T1 中如何“优雅”终止线程 T2？”优雅“指的是给 T2 一个后置处理器

错误思想：

* 使用线程对象的 stop() 方法停止线程：stop 方法会真正杀死线程，如果这时线程锁住了共享资源，当它被杀死后就再也没有机会释放锁，其它线程将永远无法获取锁
* 使用 System.exit(int) 方法停止线程：目的仅是停止一个线程，但这种做法会让整个程序都停止

两阶段终止模式图示：

<img src="https://gitee.com/seazean/images/raw/master/Java/JUC-两阶段终止模式.png" style="zoom: 67%;" />

打断线程可能在任何时间，所以需要考虑在任何时刻被打断的处理方法：

```java
public class Test {
    public static void main(String[] args) throws InterruptedException {
        TwoPhaseTermination tpt = new TwoPhaseTermination();
        tpt.start();
        Thread.sleep(3500);
        tpt.stop();
    }
}
class TwoPhaseTermination {
    private Thread monitor;
    //启动监控线程
    public void start() {
        monitor = new Thread(new Runnable() {
            @Override
            public void run() {
                while (true) {
                    Thread thread = Thread.currentThread();
                    if (thread.isInterrupted()) {
                        System.out.println("后置处理");
                        break;
                    }
                    try {
                        Thread.sleep(1000);//睡眠
                        System.out.println("执行监控记录");//在此被打断不会异常
                    } catch (InterruptedException e) {//在睡眠期间被打断
                        e.printStackTrace();
                        //重新设置打断标记
                        thread.interrupt();
                    }
                }
            }
        });
        monitor.start();
    }
    //停止监控线程
    public void stop() {
        monitor.interrupt();
    }
}
```



***



#### daemon

`public final void setDaemon(boolean on)`：如果是 true ，将此线程标记为守护线程 

线程**启动前**调用此方法：

```java
Thread t = new Thread() {
    @Override
    public void run() {
        System.out.println("running");
    }
};
// 设置该线程为守护线程
t.setDaemon(true);
t.start();
```

用户线程：平常创建的普通线程

守护线程：服务于用户线程，只要其它非守护线程运行结束了，即使守护线程代码没有执行完，也会强制结束

说明：当运行的线程都是守护线程，Java 虚拟机将退出，因为普通线程执行完后，守护线程不会继续运行下去

常见的守护线程：

* 垃圾回收器线程就是一种守护线程
* Tomcat 中的 Acceptor 和 Poller 线程都是守护线程，所以 Tomcat 接收到 shutdown 命令后，不会等
  待它们处理完当前请求



***



#### 不推荐方法

不推荐使用的方法，这些方法已过时，容易破坏同步代码块，造成线程死锁：

| 方法                        | 功能                 |
| --------------------------- | -------------------- |
| public final void stop()    | 停止线程运行         |
| public final void suspend() | 挂起（暂停）线程运行 |
| public final void resume()  | 恢复线程运行         |



***



### 线程状态

进程的状态参考操作系统：创建态、就绪态、运行态、阻塞态、终止态

线程由生到死的完整过程（生命周期）：当线程被创建并启动以后，它既不是一启动就进入了执行状态，也不是一直处于执行状态，在 API 中 `java.lang.Thread.State` 这个枚举中给出了六种线程状态：

| 线程状态                | 导致状态发生条件                                             |
| ----------------------- | ------------------------------------------------------------ |
| NEW(新建)               | 线程刚被创建，但是并未启动，还没调用 start 方法，只有线程对象，没有线程特征 |
| Runnable(可运行)        | 线程可以在 java 虚拟机中运行的状态，可能正在运行自己代码，也可能没有，这取决于操作系统处理器，调用了 t.start() 方法：就绪（经典叫法） |
| Blocked(锁阻塞)         | 当一个线程试图获取一个对象锁，而该对象锁被其他的线程持有，则该线程进入 Blocked 状态；当该线程持有锁时，该线程将变成 Runnable 状态 |
| Waiting(无限等待)       | 一个线程在等待另一个线程执行一个（唤醒）动作时，该线程进入 Waiting 状态，进入这个状态后不能自动唤醒，必须等待另一个线程调用 notify 或者 notifyAll 方法才能唤醒 |
| Timed Waiting(计时等待) | 有几个方法有超时参数，调用将进入 Timed Waiting 状态，这一状态将一直保持到超时期满或者接收到唤醒通知。带有超时参数的常用方法有 Thread.sleep 、Object.wait |
| Teminated(被终止)       | run 方法正常退出而死亡，或者因为没有捕获的异常终止了 run 方法而死亡 |

![](https://gitee.com/seazean/images/raw/master/Java/JUC-线程6种状态.png)

* NEW --> RUNNABLE：当调用 t.start() 方法时，由 NEW --> RUNNABLE

* RUNNABLE <--> WAITING：

  * 调用 obj.wait() 方法时

    调用 obj.notify()、obj.notifyAll()、t.interrupt()：

    * 竞争锁成功，t 线程从 WAITING --> RUNNABLE
    * 竞争锁失败，t 线程从 WAITING --> BLOCKED

  * 当前线程调用 t.join() 方法，注意是当前线程在t 线程对象的监视器上等待

  * 当前线程调用 LockSupport.park() 方法

* RUNNABLE <--> TIMED_WAITING：调用 obj.wait(long n) 方法、当前线程调用 t.join(long n) 方法、当前线程调用 Thread.sleep(long n)

* RUNNABLE <--> BLOCKED：t 线程用 synchronized(obj) 获取了对象锁时竞争失败



***



### 查看线程

windows：

* 任务管理器可以查看进程和线程数，也可以用来杀死进程
* tasklist 查看进程
* taskkill 杀死进程

linux：

* ps -ef 查看所有进程
* ps -fT -p <PID> 查看某个进程（PID）的所有线程
* kill 杀死进程
* top 按大写 H 切换是否显示线程
* top -H -p <PID> 查看某个进程（PID）的所有线程

Java：

* jps 命令查看所有 Java 进程
* jstack <PID> 查看某个 Java 进程（PID）的所有线程状态
* jconsole 来查看某个 Java 进程中线程的运行情况（图形界面）





***





## 同步

### 临界区

临界资源：一次仅允许一个进程使用的资源成为临界资源

临界区：访问临界资源的代码块

竞态条件：多个线程在临界区内执行，由于代码的执行序列不同而导致结果无法预测，称之为发生了竞态条件

一个程序运行多个线程本身是没有问题的，多个线程访问共享资源会出现问题：

* 多个线程读共享资源也没有问题
* 在多个线程对共享资源读写操作时发生指令交错，就会出现问题

为了避免临界区的竞态条件发生（解决线程安全问题）：

* 阻塞式的解决方案：synchronized，Lock
* 非阻塞式的解决方案：原子变量

管程（monitor）：由局部于自己的若干公共变量和所有访问这些公共变量的过程所组成的软件模块，保证同一时刻只有一个进程在管程内活动，即管程内定义的操作在同一时刻只被一个进程调用（由编译器实现）

**synchronized：对象锁，保证了临界区内代码的原子性**，采用互斥的方式让同一时刻至多只有一个线程能持有对象锁，其它线程获取这个对象锁时会阻塞，保证拥有锁的线程可以安全的执行临界区内的代码，不用担心线程上下文切换

互斥和同步都可以采用 synchronized 关键字来完成，区别：

* 互斥是保证临界区的竞态条件发生，同一时刻只能有一个线程执行临界区代码
* 同步是由于线程执行的先后、顺序不同、需要一个线程等待其它线程运行到某个点

性能：

* 线程安全，性能差
* 线程不安全性能好，假如开发中不会存在多线程安全问题，建议使用线程不安全的设计类



***



### syn-ed

#### 基本使用

##### 同步代码块

锁对象：理论上可以是任意的唯一对象

synchronized 是可重入、不公平的重量级锁

原则上：

* 锁对象建议使用共享资源
* 在实例方法中建议用 this 作为锁对象，锁住的 this 正好是共享资源
* 在静态方法中建议用类名 .class 字节码作为锁对象
  * 因为静态成员属于类，被所有实例对象共享，所以需要锁住类
  * 锁住类以后，类的所有实例都相当于同一把锁，参考线程八锁

格式：

```java
synchronized(锁对象){
	// 访问共享资源的核心代码
}
```

实例：

```java
public class demo {
    static int counter = 0;
    //static修饰，则元素是属于类本身的，不属于对象  ，与类一起加载一次，只有一个
    static final Object room = new Object();
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                synchronized (room) {
                    counter++;
                }
            }
        }, "t1");
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                synchronized (room) {
                    counter--;
                }
            }
        }, "t2");
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(counter);
    }
}
```



***



##### 同步方法

作用：把出现线程安全问题的核心方法锁起来，每次只能一个线程进入访问

用法：直接给方法加上一个修饰符 synchronized

```java
//同步方法
修饰符 synchronized 返回值类型 方法名(方法参数) { 
	方法体；
}
//同步静态方法
修饰符 static synchronized 返回值类型 方法名(方法参数) { 
	方法体；
}
```

同步方法底层也是有锁对象的：

* 如果方法是实例方法：同步方法默认用this作为的锁对象

  ```java
  public synchronized void test() {} //等价于
  public void test() {
      synchronized(this) {}
  }
  ```

* 如果方法是静态方法：同步方法默认用类名.class作为的锁对象

  ```java
  class Test{
  	public synchronized static void test() {}
  }
  //等价于
  class Test{
      public void test() {
          synchronized(Test.class) {}
  	}
  }
  ```

面向对象实例：

```java
public class Demo {
    public static void main(String[] args) throws InterruptedException {
        Room room = new Room();
        Thread t1 = new Thread(() -> {
            for (int j = 0; j < 5000; j++) {
                room.increment();
            }
        }, "t1");
        Thread t2 = new Thread(() -> {
            for (int j = 0; j < 5000; j++) {
                room.decrement();
            }
        }, "t2");
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        System.out.println(room.get());
    }
}

class Room {
    int value = 0;
    public synchronized void increment() {
        value++;
    }
    public synchronized void decrement() {
        value--;
    }
    public synchronized int get() {
        return value;
    }
}
```



***



##### 线程八锁

所谓的“线程八锁”，其实就是考察 synchronized 锁住的是哪个对象，直接百度搜索

说明：主要关注锁住的对象是不是同一个

* 锁住类对象，所有类的实例的方法都是安全的
* 锁住 this 对象，只有在当前实例对象的线程内是安全的，如果有多个实例就不安全

线程不安全：因为锁住的不是同一个对象，线程 1 调用 a 方法锁住的类对象，线程 2 调用 b 方法锁住的 n2 对象，不是同一个对象

```java
class Number{
    public static synchronized void a(){
		Thread.sleep(1000);
        System.out.println("1");
    }
    public synchronized void b() {
        System.out.println("2");
    }
}
public static void main(String[] args) {
    Number n1 = new Number();
    Number n2 = new Number();
    new Thread(()->{ n1.a(); }).start();
    new Thread(()->{ n2.b(); }).start();
}
```

线程安全：因为 n1 调用 a() 方法，锁住的是类对象，n2 调用 b() 方法，锁住的也是类对象，所以线程安全

```java
class Number{
    public static synchronized void a(){
		Thread.sleep(1000);
        System.out.println("1");
    }
    public static synchronized void b() {
        System.out.println("2");
    }
}
public static void main(String[] args) {
    Number n1 = new Number();
    Number n2 = new Number();
    new Thread(()->{ n1.a(); }).start();
    new Thread(()->{ n2.b(); }).start();
}
```





***



#### 锁原理

##### Monitor

Monitor 被翻译为监视器或管程

每个 Java 对象都可以关联一个 Monitor 对象，如果使用 synchronized 给对象上锁（重量级）之后，该对象头的
Mark Word 中就被设置指向 Monitor 对象的指针，这就是重量级锁

* Mark Word结构：

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-Monitor-MarkWord结构32位.png)

* 64位虚拟机Mark Word：

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-Monitor-MarkWord结构64位.png)

工作流程：

* 开始时 Monitor 中 Owner 为 null
* 当 Thread-2 执行 synchronized(obj) 就会将 Monitor 的所有者 Owner 置为 Thread-2，Monitor中只能有一
  个 Owner，**obj对象的Mark Word指向Monitor**
  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-Monitor工作原理1.png" style="zoom:67%;" />
* 在 Thread-2 上锁的过程中，Thread-3、Thread-4、Thread-5也来执行 synchronized(obj)，就会进入
  EntryList BLOCKED（双向链表）
* Thread-2 执行完同步代码块的内容，然后唤醒 EntryList 中等待的线程来竞争锁，竞争是**非公平的**
* WaitSet 中的Thread-0，是以前获得过锁，但条件不满足进入 WAITING 状态的线程（wait-notify ）

![](https://gitee.com/seazean/images/raw/master/Java/JUC-Monitor工作原理2.png)

注意：

* synchronized 必须是进入同一个对象的 monitor 才有上述的效果
* 不加 synchronized 的对象不会关联监视器，不遵从以上规则



****



##### 字节码

代码：

```java
public static void main(String[] args) {
    Object lock = new Object();
    synchronized (lock) {
        System.out.println("ok");
    }
}
```

```java
0: 	new				#2		// new Object
3: 	dup
4: 	invokespecial 	#1 		// invokespecial <init>:()V，非虚方法
7: 	astore_1 				// lock引用 -> lock
8: 	aload_1					// lock （synchronized开始）
9: 	dup						// 一份用来初始化，一份用来引用
10: astore_2 				// lock引用 -> slot 2
11: monitorenter 			// 将 lock对象 MarkWord 置为 Monitor 指针
12: getstatic 		#3		// System.out
15: ldc 			#4		// "ok"
17: invokevirtual 	#5 		// invokevirtual println:(Ljava/lang/String;)V
20: aload_2 				// slot 2(lock引用)
21: monitorexit 			// 将 lock对象 MarkWord 重置, 唤醒 EntryList
22: goto 30
25: astore_3 				// any -> slot 3
26: aload_2 				// slot 2(lock引用)
27: monitorexit 			// 将 lock对象 MarkWord 重置, 唤醒 EntryList
28: aload_3
29: athrow
30: return
Exception table:
    from to target type
      12 22 25 		any
      25 28 25 		any
LineNumberTable: ...
LocalVariableTable:
    Start Length Slot Name Signature
    	0 	31 		0 args [Ljava/lang/String;
    	8 	23 		1 lock Ljava/lang/Object;
```

说明：

* 通过异常 **try-catch 机制**，确保一定会被解锁
* 方法级别的 synchronized 不会在字节码指令中有所体现



***



#### 锁升级

##### 升级过程

**synchronized 是可重入、不公平的重量级锁**，所以可以对其进行优化

```java
无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁	//随着竞争的增加，只能锁升级，不能降级
```

![](https://gitee.com/seazean/images/raw/master/Java/JUC-锁升级过程.png)



***



##### 偏向锁

偏向锁的思想是偏向于让第一个获取锁对象的线程，这个线程之后重新获取该锁不再需要同步操作：

* 当锁对象第一次被线程获得的时候进入偏向状态，标记为101，同时使用 CAS 操作将线程 ID 记录到Mark Word。如果 CAS 操作成功，这个线程以后进入这个锁相关的同步块，查看这个线程 ID 是自己的就表示没有竞争，就不需要再进行任何同步操作

* 当有另外一个线程去尝试获取这个锁对象时，偏向状态就宣告结束，此时撤销偏向（Revoke Bias）后恢复到未锁定状态或轻量级锁状态

<img src="https://gitee.com/seazean/images/raw/master/Java/JUC-Monitor-MarkWord结构64位.png" style="zoom: 67%;" />

一个对象创建时：

* 如果开启了偏向锁（默认开启），那么对象创建后，markword 值为 0x05 即最后 3 位为 101，这时它的
  thread、epoch、age 都为 0

* 偏向锁是默认是延迟的，不会在程序启动时立即生效，如果想避免延迟，可以加VM参数 `-XX:BiasedLockingStartupDelay=0` 来禁用延迟

  JDK 8 延迟 4s 开启偏向锁原因：在刚开始执行代码时，会有好多线程来抢锁，如果开偏向锁效率反而降低

* 如果禁用了偏向锁，那么对象创建后，markword 值为 0x01 即最后 3 位为 001，这时它的 hashcode、age 都为 0，**第一次用到 hashcode 时才会赋值**，添加 VM 参数 `-XX:-UseBiasedLocking` 禁用偏向锁

撤销偏向锁的状态：

* 调用对象的 hashCode：偏向锁的对象 MarkWord 中存储的是线程 id，调用 hashCode 导致偏向锁被撤销
  * 轻量级锁会在锁记录中记录 hashCode
  * 重量级锁会在 Monitor 中记录 hashCode
* 当有其它线程使用偏向锁对象时，会将偏向锁升级为轻量级锁
* 调用 wait/notify



**批量撤销**：如果对象被多个线程访问，但没有竞争，这时偏向了线程 T1 的对象仍有机会重新偏向 T2，重偏向会重置对象的 Thread ID

* 批量重偏向：当撤销偏向锁阈值超过 20 次后，jvm会觉得是不是偏向错了，于是在给这些对象加锁时重新偏向至加锁线程

* 批量撤销：当撤销偏向锁阈值超过 40 次后，jvm会觉得自己确实偏向错了，根本就不该偏向。于是整个类的所有对象都会变为不可偏向的，新建的对象也是不可偏向的



***



##### 轻量级锁

一个对象有多个线程要加锁，但加锁的时间是错开的（没有竞争），那么可以使用轻量级锁来优化，轻量级锁对使用者是透明的（不可见）

可重入锁：**线程可以进入任何一个它已经拥有的锁所同步着的代码块，可重入锁最大的作用是避免死锁**

轻量级锁在没有竞争时（锁重入时），每次重入仍然需要执行 CAS 操作，Java 6 才引入的偏向锁来优化

锁重入实例：

```java
static final Object obj = new Object();
public static void method1() {
    synchronized( obj ) {
        // 同步块 A
        method2();
    }
}
public static void method2() {
    synchronized( obj ) {
    	// 同步块 B
    }
}
```

* 创建锁记录(Lock Record)对象，每个线程的栈帧都会包含一个锁记录的结构，存储锁定对象的Mark Word

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-轻量级锁原理1.png)

* 让锁记录中Object reference指向锁对象，并尝试用CAS替换Object的Mark Word，将Mark Word的值存
  入锁记录

* 如果CAS替换成功，对象头中存储了锁记录地址和状态 00（轻量级锁） ，表示由该线程给对象加锁
  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-轻量级锁原理2.png)

* 如果CAS失败，有两种情况：

  * 如果是其它线程已经持有了该 Object 的轻量级锁，这时表明有竞争，进入锁膨胀过程
  * 如果是自己执行了 synchronized 锁重入，就添加一条 Lock Record 作为重入的计数

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-轻量级锁原理3.png)

* 当退出 synchronized 代码块（解锁时）

  * 如果有取值为 null 的锁记录，表示有重入，这时重置锁记录，表示重入计数减1
  * 如果锁记录的值不为 null，这时使用 cas 将 Mark Word 的值恢复给对象头
    * 成功，则解锁成功
    * 失败，说明轻量级锁进行了锁膨胀或已经升级为重量级锁，进入重量级锁解锁流程



***



##### 锁膨胀

在尝试加轻量级锁的过程中，CAS 操作无法成功，可能是其它线程为此对象加上了轻量级锁（有竞争），这时需要进行锁膨胀，将轻量级锁变为**重量级锁**

* 当 Thread-1 进行轻量级加锁时，Thread-0 已经对该对象加了轻量级锁

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-重量级锁原理1.png)

* Thread-1 加轻量级锁失败，进入锁膨胀流程：为 Object 对象申请 Monitor 锁，让Object 对象头指向重量级锁地址，Monitor 的 Owner 置为 Thread-0，然后自己进入 Monitor 的 EntryList BLOCKED

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-重量级锁原理2.png)

* 当Thread-0退出同步块解锁时，使用 cas 将 Mark Word 的值恢复给对象头，失败，这时进入重量级解锁流程，即按照 Monitor 地址找到 Monitor 对象，设置 Owner 为 null，唤醒 EntryList 中 BLOCKED 线程





***



#### 锁优化

##### 自旋锁

**重量级锁竞争**时，尝试获取锁的线程不会立即阻塞，可以使用**自旋**来进行优化，采用循环的方式去尝试获取锁

注意：

* 自旋占用 CPU 时间，单核 CPU 自旋就是浪费，多核 CPU 自旋才能发挥优势
* 自旋失败的线程会进入阻塞状态

优点：不会进入阻塞状态，减少线程上下文切换的消耗

缺点：当自旋的线程越来越多时，会不断的消耗CPU资源

自旋锁情况：

* 自旋成功的情况：
      <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-自旋成功.png" style="zoom: 80%;" />

* 自旋失败的情况：

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-自旋失败.png" style="zoom:80%;" />

自旋锁说明：

* 在 Java 6 之后自旋锁是自适应的，比如对象刚刚的一次自旋操作成功过，那么认为这次自旋成功的可能性会
  高，就多自旋几次；反之，就少自旋甚至不自旋，比较智能
* Java 7 之后不能控制是否开启自旋功能，由JVM控制

```java
//手写自旋锁
public class SpinLock {
    // 泛型装的是Thread，原子引用线程
    AtomicReference<Thread> atomicReference = new AtomicReference<>();

    public void lock() {
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName() + " come in");

        //开始自旋，期望值为null，更新值是当前线程
        while (!atomicReference.compareAndSet(null, thread)) {
            Thread.sleep(1000);
            System.out.println(thread.getName() + " 正在自旋");
        }
        System.out.println(thread.getName() + " 自旋成功");
    }

    public void unlock() {
        Thread thread = Thread.currentThread();

        //线程使用完锁把引用变为null
		atomicReference.compareAndSet(thread, null);
        System.out.println(thread.getName() + " invoke unlock");
    }

    public static void main(String[] args) throws InterruptedException {
        SpinLock lock = new SpinLock();
        new Thread(() -> {
            //占有锁
            lock.lock();
            Thread.sleep(10000); 

            //释放锁
            lock.unlock();
        },"t1").start();

        // 让main线程暂停1秒，使得t1线程，先执行
        Thread.sleep(1000);

        new Thread(() -> {
            lock.lock();
            lock.unlock();
        },"t2").start();
    }
}
```





***



##### 锁消除

锁消除是指对于被检测出不可能存在竞争的共享数据的锁进行消除

锁消除主要是通过逃逸分析来支持，如果堆上的共享数据不可能逃逸出去被其它线程访问到，那么就可以把它们当成私有数据对待，也就可以将它们的锁进行消除（同步消除：JVM内存分配）



***



##### 锁粗化

对相同对象多次加锁，导致线程发生多次重入，频繁的加锁操作就会导致性能损耗，可以使用锁粗化方式优化

如果虚拟机探测到一串零碎的操作都对同一个对象加锁，将会把加锁的范围扩展（粗化）到整个操作序列的外部

* 一些看起来没有加锁的代码，其实隐式的加了很多锁：

  ```java
  public static String concatString(String s1, String s2, String s3) {
      return s1 + s2 + s3;
  }
  ```

* String 是一个不可变的类，编译器会对 String 的拼接自动优化。在 JDK 1.5 之前，转化为StringBuffer对象的连续 append() 操作，每个append() 方法中都有一个同步块

  ```java
  public static String concatString(String s1, String s2, String s3) {
      StringBuffer sb = new StringBuffer();
      sb.append(s1);
      sb.append(s2);
      sb.append(s3);
      return sb.toString();
  }
  ```

扩展到第一个 append() 操作之前直至最后一个 append() 操作之后，只需要加锁一次就可以



****



#### 多把锁

多把不相干的锁：一间大屋子有两个功能：睡觉、学习，互不相干。现在一人要学习，一人要睡觉，如果只用一间屋子（一个对象锁）的话，那么并发度很低

将锁的粒度细分：

* 好处，是可以增强并发度
* 坏处，如果一个线程需要同时获得多把锁，就容易发生死锁 

解决方法：准备多个对象锁

```java
public static void main(String[] args) {
    BigRoom bigRoom = new BigRoom();
    new Thread(() -> { bigRoom.study(); }).start();
    new Thread(() -> { bigRoom.sleep(); }).start();
}
class BigRoom {
    private final Object studyRoom = new Object();
    private final Object sleepRoom = new Object();

    public void sleep() throws InterruptedException {
        synchronized (sleepRoom) {
            System.out.println("sleeping 2 小时");
            Thread.sleep(2000);
        }
    }

    public void study() throws InterruptedException {
        synchronized (studyRoom) {
            System.out.println("study 1 小时");
            Thread.sleep(1000);
        }
    }
}
```



***



#### 活跃性

##### 死锁

###### 形成

死锁：多个线程同时被阻塞，它们中的一个或者全部都在等待某个资源被释放，由于线程被无限期地阻塞，因此程序不可能正常终止

java 死锁产生的四个必要条件：

1. 互斥条件，即当资源被一个线程使用（占有）时，别的线程不能使用
2. 不可剥夺条件，资源请求者不能强制从资源占有者手中夺取资源，资源只能由资源占有者主动释放
3. 请求和保持条件，即当资源请求者在请求其他的资源的同时保持对原有资源的占有
4. 循环等待条件，即存在一个等待循环队列：p1 要 p2 的资源，p2 要 p1 的资源，形成了一个等待环路

四个条件都成立的时候，便形成死锁。死锁情况下打破上述任何一个条件，便可让死锁消失

```java
public class Dead {
    public static Object resources1 = new Object();
    public static Object resources2 = new Object();
    public static void main(String[] args) {
        new Thread(() -> {
            // 线程1：占用资源1 ，请求资源2
            synchronized(resources1){
                System.out.println("线程1已经占用了资源1，开始请求资源2");
                Thread.sleep(2000);//休息两秒，防止线程1直接运行完成。
                //2秒内线程2肯定可以锁住资源2
                synchronized (resources2){
                    System.out.println("线程1已经占用了资源2");
                }
        }).start();
        new Thread(() -> {
            // 线程2：占用资源2 ，请求资源1
            synchronized(resources2){
                System.out.println("线程2已经占用了资源2，开始请求资源1");
                Thread.sleep(2000);
                synchronized (resources1){
                    System.out.println("线程2已经占用了资源1");
                }
            }}
        }).start();
    }
}
```

面向对象写法：

```java
public class DeadLock {
    static String lockA = "lockA";
    static String lockB = "lockB";
    public static void main(String[] args) {
        new Thread(new HoldLockThread(lockA, lockB)).start();
        new Thread(new HoldLockThread(lockB, lockA)).start();
    }
}
class HoldLockThread implements Runnable {
    private String lockA;
    private String lockB;

    public HoldLockThread(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName() + " 持有" + lockA + "，尝试获得" + lockB);
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName() + " 持有" + lockB + "，尝试获得" + lockA);
            }
        }
    }
}
```



###### 定位

定位死锁的方法：

* 使用 jps 定位进程 id，再用 `jstack id` 定位死锁，找到死锁的线程去查看源码，解决优化

  ```sh
  "Thread-1" #12 prio=5 os_prio=0 tid=0x000000001eb69000 nid=0xd40 waiting formonitor entry [0x000000001f54f000]
  	java.lang.Thread.State: BLOCKED (on object monitor)
  #省略    
  "Thread-1" #12 prio=5 os_prio=0 tid=0x000000001eb69000 nid=0xd40 waiting for monitor entry [0x000000001f54f000]
  	java.lang.Thread.State: BLOCKED (on object monitor)
  #省略
  
  Found one Java-level deadlock:
  ===================================================
  "Thread-1":
      waiting to lock monitor 0x000000000361d378 (object 0x000000076b5bf1c0, a java.lang.Object),
      which is held by "Thread-0"
  "Thread-0":
      waiting to lock monitor 0x000000000361e768 (object 0x000000076b5bf1d0, a java.lang.Object),
      which is held by "Thread-1"
      
  Java stack information for the threads listed above:
  ===================================================
  "Thread-1":
      at thread.TestDeadLock.lambda$main$1(TestDeadLock.java:28)
      - waiting to lock <0x000000076b5bf1c0> (a java.lang.Object)
      - locked <0x000000076b5bf1d0> (a java.lang.Object)
      at thread.TestDeadLock$$Lambda$2/883049899.run(Unknown Source)
      at java.lang.Thread.run(Thread.java:745)
  "Thread-0":
      at thread.TestDeadLock.lambda$main$0(TestDeadLock.java:15)
      - waiting to lock <0x000000076b5bf1d0> (a java.lang.Object)
      - locked <0x000000076b5bf1c0> (a java.lang.Object)
      at thread.TestDeadLock$$Lambda$1/495053715
  ```

* linux 下可以通过 top 先定位到 CPU 占用高的 Java 进程，再利用 `top -Hp 进程id` 来定位是哪个线程，最后再用 jstack <pid>的输出来看各个线程栈

* 避免死锁：避免死锁要注意加锁顺序

* 可以使用 jconsole 工具，在 `jdk\bin` 目录下



***



##### 活锁

活锁：指的是任务或者执行者没有被阻塞，由于某些条件没有满足，导致一直重复尝试—失败—尝试—失败的过程

两个线程互相改变对方的结束条件，最后谁也无法结束：

```java
class TestLiveLock {
    static volatile int count = 10;
    static final Object lock = new Object();
    public static void main(String[] args) {
        new Thread(() -> {
            // 期望减到 0 退出循环
            while (count > 0) {
                Thread.sleep(200);
                count--;
                System.out.println("线程一count:" + count);
            }
        }, "t1").start();
        new Thread(() -> {
            // 期望超过 20 退出循环
            while (count < 20) {
                Thread.sleep(200);
                count++;
                System.out.println("线程二count:"+ count);
            }
        }, "t2").start();
    }
}
```



***



##### 饥饿

饥饿：一个线程由于优先级太低，始终得不到 CPU 调度执行，也不能够结束



***



### wait-ify

#### 基本使用

需要获取对象锁后才可以调用`锁对象.wait()`，notify 随机唤醒一个线程，notifyAll 唤醒所有线程去竞争 CPU

Object 类 API：

```java
public final void notify():唤醒正在等待对象监视器的单个线程。
public final void notifyAll():唤醒正在等待对象监视器的所有线程。
public final void wait():导致当前线程等待，直到另一个线程调用该对象的notify()方法或 notifyAll()方法。
public final native void wait(long timeout):有时限的等待, 到n毫秒后结束等待，或是被唤醒
```

对比 sleep()：

* 原理不同：sleep() 方法是属于 Thread 类，是线程用来控制自身流程的，使此线程暂停执行一段时间而把执行机会让给其他线程；wait() 方法属于 Object 类，用于线程间通信
* 对锁的处理机制不同：调用 sleep() 方法的过程中，线程不会释放对象锁，当调用 wait() 方法的时候，线程会放弃对象锁，进入等待此对象的等待锁定池，但是都会释放 CPU
* 使用区域不同：wait() 方法必须放在**同步控制方法和同步代码块（先获取锁）**中使用，sleep() 方法则可以放在任何地方使用

底层原理：

* Owner 线程发现条件不满足，调用 wait 方法，即可进入 WaitSet 变为 WAITING 状态
* BLOCKED 和 WAITING 的线程都处于阻塞状态，不占用 CPU 时间片
* BLOCKED 线程会在 Owner 线程释放锁时唤醒
* WAITING 线程会在 Owner 线程调用 notify 或 notifyAll 时唤醒，但唤醒后并不意味者立刻获得锁，仍需进入
  EntryList 重新竞争

![](https://gitee.com/seazean/images/raw/master/Java/JUC-Monitor工作原理2.png)



***



#### 代码优化

虚假唤醒：notify 只能随机唤醒一个 WaitSet 中的线程，这时如果有其它线程也在等待，那么就可能唤醒不了正确的线程

解决方法：采用 notifyAll

notifyAll 仅解决某个线程的唤醒问题，使用 if + wait 判断仅有一次机会，一旦条件不成立，无法重新判断

解决方法：用 while + wait，当条件不成立，再次 wait

```java
@Slf4j(topic = "c.demo")
public class demo {
    static final Object room = new Object();
    static boolean hasCigarette = false;    //有没有烟
    static boolean hasTakeout = false;

    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            synchronized (room) {
                log.debug("有烟没？[{}]", hasCigarette);
                while (!hasCigarette) {//while防止虚假唤醒
                    log.debug("没烟，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("有烟没？[{}]", hasCigarette);
                if (hasCigarette) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小南").start();

        new Thread(() -> {
            synchronized (room) {
                Thread thread = Thread.currentThread();
                log.debug("外卖送到没？[{}]", hasTakeout);
                if (!hasTakeout) {
                    log.debug("没外卖，先歇会！");
                    try {
                        room.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.debug("外卖送到没？[{}]", hasTakeout);
                if (hasTakeout) {
                    log.debug("可以开始干活了");
                } else {
                    log.debug("没干成活...");
                }
            }
        }, "小女").start();


        Thread.sleep(1000);
        new Thread(() -> {
        // 这里能不能加 synchronized (room)？
            synchronized (room) {
                hasTakeout = true;
				//log.debug("烟到了噢！");
                log.debug("外卖到了噢！");
                room.notifyAll();
            }
        }, "送外卖的").start();
    }
}
```





****



### park-un

LockSupport 是用来创建锁和其他同步类的线程阻塞原语

LockSupport 类方法：

* `LockSupport.park()`：暂停当前线程，原语
* `LockSupport.unpark(暂停的线程对象)`：恢复某个线程的运行

```java
public static void main(String[] args) {
    Thread t1 = new Thread(() -> {
        System.out.println("start...");//1
		Thread.sleep(1000);//Thread.sleep(3000)
        //先park再unpark 和 先unpark再park效果一样，都会直接恢复线程的运行
        System.out.println("park...");//2
        LockSupport.park();
        System.out.println("resume...");//4
    },"t1");
    t1.start();
   	Thread.sleep(2000);
    System.out.println("unpark...");//3
    LockSupport.unpark(t1);
}
```

LockSupport 出现就是为了增强 wait & notify 的功能：

* wait，notify 和 notifyAll 必须配合 Object Monitor 一起使用，而 park、unpark 不需要
* park & unpark以线程为单位来阻塞和唤醒线程，而notify 只能随机唤醒一个等待线程，notifyAll是唤醒所有等待线程
* **park & unpark 可以先 unpark**，而 wait & notify 不能先 notify。类比生产消费，先消费发现有产品就消费，没有就等待；先生产就直接产生商品，然后线程直接消费
* wait 会释放锁资源进入等待队列，park 不会释放锁资源，只负责阻塞当前线程，会释放 CPU

原理：

* 先 park：
  1. 当前线程调用 Unsafe.park() 方法
  2. 检查 _counter ，本情况为 0，这时获得 _mutex 互斥锁
  3. 线程进入 _cond 条件变量阻塞
  4. 调用 Unsafe.unpark(Thread_0) 方法，设置 _counter 为 1
  5. 唤醒 _cond 条件变量中的 Thread_0，Thread_0 恢复运行，设置 _counter 为 0

![](https://gitee.com/seazean/images/raw/master/Java/JUC-park原理1.png)

* 先 unpark：

  1. 调用 Unsafe.unpark(Thread_0) 方法，设置 _counter 为 1
  2. 当前线程调用 Unsafe.park() 方法
  3. 检查 _counter ，本情况为 1，这时线程无需阻塞，继续运行，设置 _counter 为 0

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-park原理2.png)





***



### 安全分析

成员变量和静态变量：

* 如果它们没有共享，则线程安全
* 如果它们被共享了，根据它们的状态是否能够改变，分两种情况：
  * 如果只有读操作，则线程安全
  * 如果有读写操作，则这段代码是临界区，需要考虑线程安全问题

局部变量：

* 局部变量是线程安全的
* 局部变量引用的对象不一定线程安全（逃逸分析）：
  * 如果该对象没有逃离方法的作用访问，它是线程安全的（每一个方法有一个栈帧）
  * 如果该对象逃离方法的作用范围，需要考虑线程安全问题（暴露引用）

常见线程安全类：String、Integer、StringBuffer、Random、Vector、Hashtable、java.util.concurrent 包

* 线程安全的是指，多个线程调用它们同一个实例的某个方法时，是线程安全的

* 每个方法是原子的，但多个方法的组合不是原子的，只能保证调用的方法内部安全：

  ```java
  Hashtable table = new Hashtable();
  // 线程1，线程2
  if( table.get("key") == null) {
  	table.put("key", value);
  }
  ```

不可变类线程安全：String、Integer 等都是不可变类，因为内部的状态不可以改变，因此方法是线程安全

* replace等方法底层是新建一个对象，复制过去

  ```java
  Map<String,Object> map = new HashMap<>();	//线程不安全
  String S1 = "...";							//线程安全
  final String S2 = "...";					//线程安全
  Date D1 = new Date();						//线程不安全
  final Date D2 = new Date();	//线程不安全，final让D2引用的对象不能变，但对象的内容可以变
  ```

抽象方法如果有参数，被重写后行为不确定可能造成线程不安全，被称之为外星方法：`public abstract foo(Student s);`

无状态类线程安全



***



### 同步模式

#### 保护性暂停

##### 单任务版

Guarded Suspension，用在一个线程等待另一个线程的执行结果

* 有一个结果需要从一个线程传递到另一个线程，让它们关联同一个 GuardedObject
* 如果有结果不断从一个线程到另一个线程那么可以使用消息队列（见生产者/消费者）
* JDK 中，join 的实现、Future 的实现，采用的就是此模式

![](https://gitee.com/seazean/images/raw/master/Java/JUC-保护性暂停.png)

```java
public static void main(String[] args) {
    GuardedObject object = new GuardedObjectV2();
    new Thread(() -> {
        sleep(1);
        object.complete(Arrays.asList("a", "b", "c"));
    }).start();
    
    Object response = object.get(2500);
    if (response != null) {
        log.debug("get response: [{}] lines", ((List<String>) response).size());
    } else {
        log.debug("can't get response");
    }
}

class GuardedObject {
    private Object response;
    private final Object lock = new Object();

    //获取结果
    //timeout :最大等待时间
    public Object get(long millis) {
        synchronized (lock) {
            // 1) 记录最初时间
            long begin = System.currentTimeMillis();
            // 2) 已经经历的时间
            long timePassed = 0;
            while (response == null) {
                // 4) 假设 millis 是 1000，结果在 400 时唤醒了，那么还有 600 要等
                long waitTime = millis - timePassed;
                log.debug("waitTime: {}", waitTime);
                //经历时间超过最大等待时间退出循环
                if (waitTime <= 0) {
                    log.debug("break...");
                    break;
                }
                try {
                    lock.wait(waitTime);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                // 3) 如果提前被唤醒，这时已经经历的时间假设为 400
                timePassed = System.currentTimeMillis() - begin;
                log.debug("timePassed: {}, object is null {}",
                        timePassed, response == null);
            }
            return response;
        }
    }

    //产生结果
    public void complete(Object response) {
        synchronized (lock) {
            // 条件满足，通知等待线程
            this.response = response;
            log.debug("notify...");
            lock.notifyAll();
        }
    }
}
```



##### 多任务版

多任务版保护性暂停：

![](https://gitee.com/seazean/images/raw/master/Java/JUC-保护性暂停多任务版.png)

```java
public static void main(String[] args) throws InterruptedException {
    for (int i = 0; i < 3; i++) {
        new People().start();
    }
    Thread.sleep(1000);
    for (Integer id : Mailboxes.getIds()) {
        new Postman(id, id + "号快递到了").start();
    }
}

@Slf4j(topic = "c.People")
class People extends Thread{
    @Override
    public void run() {
        // 收信
        GuardedObject guardedObject = Mailboxes.createGuardedObject();
        log.debug("开始收信i d:{}", guardedObject.getId());
        Object mail = guardedObject.get(5000);
        log.debug("收到信id:{}，内容:{}", guardedObject.getId(),mail);
    }
}

class Postman extends Thread{
    private int id;
    private String mail;
    //构造方法
    @Override
    public void run() {
        GuardedObject guardedObject = Mailboxes.getGuardedObject(id);
        log.debug("开始送信i d:{}，内容:{}", guardedObject.getId(),mail);
        guardedObject.complete(mail);
    }
}

class  Mailboxes {
    private static Map<Integer, GuardedObject> boxes = new Hashtable<>();
    private static int id = 1;

    //产生唯一的id
    private static synchronized int generateId() {
        return id++;
    }

    public static GuardedObject getGuardedObject(int id) {
        return boxes.remove(id);
    }

    public static GuardedObject createGuardedObject() {
        GuardedObject go = new GuardedObject(generateId());
        boxes.put(go.getId(), go);
        return go;
    }

    public static Set<Integer> getIds() {
        return boxes.keySet();
    }
}
class GuardedObject {
    //标识，Guarded Object
    private int id;//添加get set方法
}
```



****



#### 顺序输出

顺序输出 2  1 

```java
public static void main(String[] args) throws InterruptedException {
    Thread t1 = new Thread(() -> {
        while (true) {
            //try { Thread.sleep(1000); } catch (InterruptedException e) { }
            // 当没有许可时，当前线程暂停运行；有许可时，用掉这个许可，当前线程恢复运行
            LockSupport.park();
            System.out.println("1");
        }
    });
    Thread t2 = new Thread(() -> {
        while (true) {
            System.out.println("2");
            // 给线程 t1 发放『许可』（多次连续调用 unpark 只会发放一个『许可』）
            LockSupport.unpark(t1);
            try { Thread.sleep(500); } catch (InterruptedException e) { }
        }
    });
    t1.start();
    t2.start();
}
```



***



#### 交替输出

连续输出 5 次 abc

```java
public class day2_14 {
    public static void main(String[] args) throws InterruptedException {
        AwaitSignal awaitSignal = new AwaitSignal(5);
        Condition a = awaitSignal.newCondition();
        Condition b = awaitSignal.newCondition();
        Condition c = awaitSignal.newCondition();
        new Thread(() -> {
            awaitSignal.print("a", a, b);
        }).start();
        new Thread(() -> {
            awaitSignal.print("b", b, c);
        }).start();
        new Thread(() -> {
            awaitSignal.print("c", c, a);
        }).start();

        Thread.sleep(1000);
        awaitSignal.lock();
        try {
            a.signal();
        } finally {
            awaitSignal.unlock();
        }
    }
}

class AwaitSignal extends ReentrantLock {
    private int loopNumber;

    public AwaitSignal(int loopNumber) {
        this.loopNumber = loopNumber;
    }
    //参数1：打印内容  参数二：条件变量  参数二：唤醒下一个
    public void print(String str, Condition condition, Condition next) {
        for (int i = 0; i < loopNumber; i++) {
            lock();
            try {
                condition.await();
                System.out.print(str);
                next.signal();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                unlock();
            }
        }
    }
}
```



***



### 异步模式

#### 传统版

异步模式之生产者/消费者：

```java
class ShareData {
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void increment() throws Exception{
        // 同步代码块，加锁
        lock.lock();
        try {
            // 判断  防止虚假唤醒
            while(number != 0) {
                // 等待不能生产
                condition.await();
            }
            // 干活
            number++;
            System.out.println(Thread.currentThread().getName() + "\t " + number);
            // 通知 唤醒
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement() throws Exception{
        // 同步代码块，加锁
        lock.lock();
        try {
            // 判断 防止虚假唤醒
            while(number == 0) {
                // 等待不能消费
                condition.await();
            }
            // 干活
            number--;
            System.out.println(Thread.currentThread().getName() + "\t " + number);
            // 通知 唤醒
            condition.signalAll();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}

public class TraditionalProducerConsumer {
	public static void main(String[] args) {
        ShareData shareData = new ShareData();
        // t1线程，生产
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
            	shareData.increment();
            }
        }, "t1").start();

        // t2线程，消费
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
				shareData.decrement();
            }
        }, "t2").start(); 
    }
}
```



#### 改进版

异步模式之生产者/消费者：

* 消费队列可以用来平衡生产和消费的线程资源，不需要产生结果和消费结果的线程一一对应
* 生产者仅负责产生结果数据，不关心数据该如何处理，而消费者专心处理结果数据
* 消息队列是有容量限制的，满时不会再加入数据，空时不会再消耗数据
* JDK 中各种阻塞队列，采用的就是这种模式

![](https://gitee.com/seazean/images/raw/master/Java/JUC-生产者消费者模式.png)

```java
public class demo {
    public static void main(String[] args) {
        MessageQueue queue = new MessageQueue(2);
        for (int i = 0; i < 3; i++) {
            int id = i;
            new Thread(() -> {
                queue.put(new Message(id,"值"+id));
            }, "生产者" + i).start();
        }
        
        new Thread(() -> {
            while (true) {
                try {
                    Thread.sleep(1000);
                    Message message = queue.take();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"消费者").start();
    }
}

//消息队列类，Java间线程之间通信
class MessageQueue {
    private LinkedList<Message> list = new LinkedList<>();//消息的队列集合
    private int capacity;//队列容量
    public MessageQueue(int capacity) {
        this.capacity = capacity;
    }

    //获取消息
    public Message take() {
        //检查队列是否为空
        synchronized (list) {
            while (list.isEmpty()) {
                try {
                    sout(Thread.currentThread().getName() + ":队列为空，消费者线程等待");
                    list.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            //从队列的头部获取消息返回
            Message message = list.removeFirst();
            sout(Thread.currentThread().getName() + "：已消费消息--" + message);
            list.notifyAll();
            return message;
        }
    }

    //存入消息
    public void put(Message message) {
        synchronized (list) {
            //检查队列是否满
            while (list.size() == capacity) {
                try {
                    sout(Thread.currentThread().getName()+":队列为已满，生产者线程等待");
                    list.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            //将消息加入队列尾部
            list.addLast(message);
            sout(Thread.currentThread().getName() + ":已生产消息--" + message);
            list.notifyAll();
        }
    }
}

final class Message {
    private int id;
    private Object value;
	//get set
}
```



***



#### 阻塞队列

```java
public static void main(String[] args) {
    ExecutorService consumer = Executors.newFixedThreadPool(1);
    ExecutorService producer = Executors.newFixedThreadPool(1);
    BlockingQueue<Integer> queue = new SynchronousQueue<>();
    producer.submit(() -> {
        try {
            System.out.println("生产...");
            Thread.sleep(1000);
            queue.put(10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
    consumer.submit(() -> {
        try {
            System.out.println("等待消费...");
            Integer result = queue.take();
            System.out.println("结果为:" + result);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
}
```





****





## 内存

### JMM

#### 内存模型

Java 内存模型是 Java MemoryModel（JMM），本身是一种**抽象的概念**，实际上并不存在，描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段，静态字段和构成数组对象的元素）的访问方式

作用：

* 屏蔽各种硬件和操作系统的内存访问差异，实现让 Java 程序在各种平台下都能达到一致的内存访问效果
* 规定了线程和内存之间的一些关系

根据JMM的设计，系统存在一个主内存（Main Memory），Java 中所有变量都存储在主存中，对于所有线程都是共享的；每条线程都有自己的工作内存（Working Memory），工作内存中保存的是主存中某些**变量的拷贝**，线程对所有变量的操作都是先对变量进行拷贝，然后在工作内存中进行，不能直接操作主内存中的变量；线程之间无法相互直接访问，线程间的通信（传递）必须通过主内存来完成

![](https://gitee.com/seazean/images/raw/master/Java/JMM内存模型.png)

主内存和工作内存：

* 主内存：计算机的内存，也就是经常提到的8G内存，16G内存，存储所有共享变量的值
* 工作内存：存储该线程使用到的共享变量在主内存的的值的副本拷贝



**jvm和jmm之间的关系**：

* jmm中的主内存、工作内存与jvm中的Java堆、栈、方法区等并不是同一个层次的内存划分，这两者基本上是没有关系的，如果两者一定要勉强对应起来：
  * 主内存主要对应于Java堆中的对象实例数据部分，而工作内存则对应于虚拟机栈中的部分区域
  * 从更低层次上说，主内存直接对应于物理硬件的内存，工作内存对应寄存器和高速缓存



***



#### 内存交互

Java 内存模型定义了 8 个操作来完成主内存和工作内存的交互操作：

<img src="https://gitee.com/seazean/images/raw/master/Java/JMM-内存交互.png" style="zoom: 67%;" />

* lock：将一个变量标识为被一个线程独占状态
* unclock：将一个变量从独占状态释放出来，释放后的变量才可以被其他线程锁定
* read：把一个变量的值从主内存传输到工作内存中
* load：在 read 之后执行，把 read 得到的值放入工作内存的变量副本中
* use：把工作内存中一个变量的值传递给执行引擎，每当遇到一个使用到变量的操作时都要使用该指令
* assign：把从执行引擎接收到的一个值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的操作时，都要使用该指令
* store：把工作内存的一个变量的值传送到主内存中
* write：在 store 之后执行，把 store 得到的值放入主内存的变量中



***



#### 三大特性

##### 可见性

可见性：是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值

存在可见性问题的根本原因是由于缓存的存在，线程持有的是共享变量的副本，无法感知其他线程对于共享变量的更改，导致读取的值不是最新的

main 线程对 run 变量的修改对于 t 线程不可见，导致了 t 线程无法停止：

```java
static boolean run = true;	//添加volatile
public static void main(String[] args) throws InterruptedException {
    Thread t = new Thread(()->{
        while(run){
        // ....
        }
	});
    t.start();
    sleep(1);
    run = false; // 线程t不会如预想的停下来
}
```

原因：

* 初始状态， t 线程刚开始从主内存读取了 run 的值到工作内存
* 因为 t 线程要频繁从主内存中读取 run 的值，JIT 编译器会将 run 的值缓存至自己工作内存中的高速缓存中，
  减少对主存中 run 的访问，提高效率
* 1 秒之后，main 线程修改了 run 的值，并同步至主存，而 t 是从自己工作内存中的高速缓存中读取这个变量
  的值，结果永远是旧值

![](https://gitee.com/seazean/images/raw/master/Java/JMM-可见性例子.png)



***



##### 原子性

原子性：不可分割，完整性，也就是说某个线程正在做某个具体业务时，中间不可以被分割，需要具体完成，要么同时成功，要么同时失败，保证指令不会受到线程上下文切换的影响 

定义原子操作的使用规则：

1. 不允许一个线程无原因地（没有发生过任何assign操作）把数据从工作内存同步会主内存中
2. 一个新的变量只能在主内存中诞生，不允许在工作内存中直接使用一个未被初始化（assign或者load）的变量，即对一个变量实施use和store操作之前，必须先自行assign和load操作
3. 一个变量在同一时刻只允许一条线程对其进行lock操作，但lock操作可以被同一线程重复执行多次，多次执行lock后，只有**执行相同次数的unlock**操作，变量才会被解锁，**lock和unlock必须成对出现**
4. 如果对一个变量执行lock操作，将会清空工作内存中此变量的值，在执行引擎使用这个变量之前需要重新执行load或assign操作初始化变量的值
5. 如果一个变量事先没有被lock操作锁定，则不允许对它执行unlock操作，也不允许去unlock一个被其他线程锁定的变量
6. 对一个变量执行unlock操作之前，必须先把此变量同步到主内存中（执行store和write操作）



***



##### 有序性

有序性：在本线程内观察，所有操作都是有序的；在一个线程观察另一个线程，所有操作都是无序的，无序是因为发生了指令重排序

CPU 的基本工作是执行存储的指令序列，即程序，程序的执行过程实际上是不断地取出指令、分析指令、执行指令的过程，为了提高性能，编译器和处理器会对指令重排，一般分为以下三种：

```java
源代码 -> 编译器优化的重排 -> 指令并行的重排 -> 内存系统的重排 -> 最终执行指令
```

现代 CPU 支持多级指令流水线，几乎所有的冯•诺伊曼型计算机的 CPU，其工作都可以分为 5 个阶段：取指令、指令译码、执行指令、访存取数和结果写回，可以称之为**五级指令流水线**。这时 CPU 可以在一个时钟周期内，同时运行五条指令的**不同阶段**（每个线程不同的阶段），本质上流水线技术并不能缩短单条指令的执行时间，但变相地提高了指令地吞吐率

处理器在进行重排序时，必须要考虑**指令之间的数据依赖性**

* 单线程环境也存在指令重排，由于存在依赖，最终执行结果和代码顺序的结果一致
* 多线程环境中线程交替执行，由于编译器优化重排，两个线程中使用的变量能否保证一致性是无法确定的





***



### cache

#### 缓存机制

##### 缓存结构

在计算机系统中，CPU 高速缓存（CPU Cache，简称缓存）是用于减少处理器访问内存所需平均时间的部件；在存储体系中位于自顶向下的第二层，仅次于 CPU 寄存器；其容量远小于内存，但速度却可以接近处理器的频率

CPU 处理器速度远远大于在主内存中的，为了解决速度差异，在它们之间架设了多级缓存，如 L1、L2、L3 级别的缓存，这些缓存离CPU越近就越快，将频繁操作的数据缓存到这里，加快访问速度

<img src="https://gitee.com/seazean/images/raw/master/Java/JMM-CPU缓存结构.png" style="zoom: 50%;" />

| 从 cpu 到 | 大约需要的时钟周期               |
| --------- | -------------------------------- |
| 寄存器    | 1 cycle (4GHz 的 CPU 约为0.25ns) |
| L1        | 3~4 cycle                        |
| L2        | 10~20 cycle                      |
| L3        | 40~45 cycle                      |
| 内存      | 120~240 cycle                    |



##### 缓存使用

当处理器发出内存访问请求时，会先查看缓存内是否有请求数据，如果存在（命中），则不经访问内存直接返回该数据；如果不存在（失效），则要先把内存中的相应数据载入缓存，再将其返回处理器

缓存之所以有效，主要因为程序运行时对内存的访问呈现局部性（Locality）特征。既包括空间局部性（Spatial Locality），也包括时间局部性（Temporal Locality），有效利用这种局部性，缓存可以达到极高的命中率。



***



#### 伪共享

**缓存以缓存行 cache line 为单位，每个缓存行对应着一块内存**，一般是 64 byte（8 个 long），在CPU从主存获取数据时，以 cache line 为单位加载，于是相邻的数据会一并加载到缓存中

缓存会造成数据副本的产生，即同一份数据会缓存在不同核心的缓存行中，CPU 要保证数据的一致性，需要做到某个 CPU 核心更改了数据，其它 CPU 核心对应的**整个缓存行必须失效**，这就是伪共享

<img src="https://gitee.com/seazean/images/raw/master/Java/JUC-内存伪共享.png" style="zoom: 67%;" />

解决方法：

* padding：通过填充，让数据落在不同的 cache line 中

* @Contended：原理参考 无锁 → Addr → 优化机制 → 伪共享

Linux查看CPU缓存行：

* 命令：`cat /sys/devices/system/cpu/cpu0/cache/index0/coherency_line_size64`
* 内存地址格式：[高位组标记] [低位索引] [偏移量]



***



#### 缓存一致

缓存一致性：当多个处理器运算任务都涉及到同一块主内存区域的时候，将可能导致各自的缓存数据不一样

**MESI**（Modified Exclusive Shared Or Invalid）是一种广泛使用的支持写回策略的缓存一致性协议，CPU 中每个缓存行（caceh line）使用4种状态进行标记（使用额外的两位 (bit) 表示)：

* M：被修改（Modified)

  该缓存行只被缓存在该 CPU 的缓存中，并且是被修改过的，与主存中的数据不一致 (dirty)，该缓存行中的内存需要在未来的某个时间点（其它 CPU 读取主存中相应数据之前）写回（ write back ）主存

  当被写回主存之后，该缓存行的状态会变成独享 (exclusive) 状态。

* E：独享的（Exclusive)

  该缓存行只被缓存在该 CPU 的缓存中，它是未被修改过的 (clear)，与主存中数据一致，该状态可以在任何时刻有其它 CPU 读取该内存时变成共享状态 (shared)

  当 CPU 修改该缓存行中内容时，该状态可以变成 Modified 状态

* S：共享的（Shared)

  该状态意味着该缓存行可能被多个 CPU 缓存，并且各个缓存中的数据与主存数据一致 (clear)，当有一个 CPU 修改该缓存行中，其它 CPU 中该缓存行变成无效状态 (Invalid)

* I：无效的（Invalid）

  该缓存是无效的，可能有其它 CPU 修改了该缓存行

解决方法：各个处理器访问缓存时都遵循一些协议，在读写时要根据协议进行操作，协议主要有MSI、MESI等



****



#### 处理机制

单核 CPU 处理器会自动保证基本内存操作的原子性

多核 CPU 处理器，每个 CPU 处理器内维护了一块内存，每个内核内部维护着一块缓存，当多线程并发读写时，就会出现缓存数据不一致的情况。处理器提供：

* 总线锁定：当处理器要操作共享变量时，在 BUS 总线上发出一个 LOCK 信号，其他处理器就无法操作这个共享变量，该操作会导致大量阻塞，从而增加系统的性能开销
* 缓存锁定：当处理器对缓存中的共享变量进行了操作，其他处理器有嗅探机制，将该共享变量的缓存失效，其他线程读取时会重新从主内存中读取最新的数据，基于 MESI 缓存一致性协议来实现

有如下两种情况处理器不会使用缓存锁定：

* 当操作的数据跨多个缓存行，或没被缓存在处理器内部，则处理器会使用总线锁定

* 有些处理器不支持缓存锁定，比如：Intel 486 和 Pentium 处理器也会调用总线锁定

总线机制：

* 总线嗅探：每个处理器通过嗅探在总线上传播的数据来检查自己缓存值是否过期了，当处理器发现自己的缓存对应的内存地址数据被修改，就将当前处理器的缓存行设置为无效状态，当处理器对这个数据进行操作时，会重新从内存中把数据读取到处理器缓存中

* 总线风暴：由于 volatile 的 MESI 缓存一致性协议，需要不断的从主内存嗅探和 CAS 循环，无效的交互会导致总线带宽达到峰值；因此不要大量使用 volatile 关键字，使用 volatile、syschonized 都需要根据实际场景



***



### volatile

#### 基本特性

volatile 是 Java 虚拟机提供的**轻量级**的同步机制（三大特性）

- 保证可见性
- 不保证原子性
- 保证有序性（禁止指令重排）

性能：volatile修饰的变量进行读操作与普通变量几乎没什么差别，但是写操作相对慢一些，因为需要在本地代码中插入很多内存屏障来保证指令不会发生乱序执行，但是开销比锁要小




***



#### 解决重排

**volatile 修饰的变量，可以禁用指令重排**

**synchronized 无法禁止指令重排和处理器优化**，为什么可以保证有序性？
加了锁之后，只能有一个线程获得到了锁，获得不到锁的线程就要阻塞，所以同一时间只有一个线程执行，相当于单线程，而单线程的指令重排是没有问题的

指令重排实例：

* example 1：

  ```java
  public void mySort() {
  	int x = 11;	//语句1
  	int y = 12;	//语句2  谁先执行效果一样
  	x = x + 5;	//语句3
  	y = x * x;	//语句4
  }
  ```

  执行顺序是：1 2 3 4、2 1 3 4、1 3 2 4
  指令重排也有限制不会出现：4321，语句4需要依赖于 y 以及 x 的申明，因为存在数据依赖，无法首先执行

* example 2：

  ```java
  int num = 0;
  boolean ready = false;
  // 线程1 执行此方法
  public void actor1(I_Result r) {
      if(ready) {
      	r.r1 = num + num;
      } else {
      	r.r1 = 1;
      }
  }
  // 线程2 执行此方法
  public void actor2(I_Result r) {
  	num = 2;
  	ready = true;
  }
  ```

  情况一：线程1 先执行，ready = false，结果为 r.r1 = 1
  情况二：线程2 先执行 num = 2，但还没执行 ready = true，线程1 执行，结果为 r.r1 = 1
  情况三：线程2 先执行 ready = true，线程1 执行，进入 if 分支结果为 r.r1 = 4
  情况四：线程2 执行 ready = true，切换到线程1，进入 if 分支为 r.r1 = 0，再切回线程2 执行 num = 2
  			   发生指令重排



****



#### 底层原理

使用 volatile 修饰的共享变量，总线会开启 CPU 总线嗅探机制来解决 JMM 缓存一致性问题，也就是共享变量在多线程中可见性的问题，实现 MESI 缓存一致性协议

底层是通过汇编 lock 前缀指令，共享变量加了 lock 前缀指令，在线程修改完共享变量后，会马上执行 store 和 write 操作写回主存。在执行 store 操作前，会先执行缓存锁定的操作，其他的 CPU 上运行的线程根据 CPU 总线嗅探机制会修改其共享变量为失效状态，读取时会重新从主内存中读取最新的数据

lock 前缀指令就相当于内存屏障，Memory Barrier（Memory Fence）

* 对 volatile 变量的写指令后会加入写屏障
* 对 volatile 变量的读指令前会加入读屏障

内存屏障有三个作用：

- 确保对内存的读-改-写操作原子执行
- 阻止屏障两侧的指令重排序
- 强制把缓存中的脏数据写回主内存，让缓存行中相应的数据失效

保证可见性：

* 写屏障（sfence，Store Barrier）保证在该屏障之前的，对共享变量的改动，都同步到主存当中

  ```java
  public void actor2(I_Result r) {
      num = 2;
      ready = true; // ready 是 volatile 赋值带写屏障
      // 写屏障
  }
  ```

* 读屏障（lfence，Load Barrier）保证在该屏障之后的，对共享变量的读取，加载的是主存中最新数据

  ```java
  public void actor1(I_Result r) {
      // 读屏障
      // ready 是 volatile 读取值带读屏障
      if(ready) {
      	r.r1 = num + num;
      } else {
      	r.r1 = 1;
      }
  }
  ```

  <img src="https://gitee.com/seazean/images/raw/master/Java/JMM-volatile保证可见性.png" style="zoom:67%;" />

* 全能屏障：mfence（modify/mix Barrier），兼具 sfence 和 lfence 的功能

保证有序性：

* 写屏障会确保指令重排序时，不会将写屏障之前的代码排在写屏障之后
* 读屏障会确保指令重排序时，不会将读屏障之后的代码排在读屏障之前

不能解决指令交错：

* 写屏障仅仅是保证之后的读能够读到最新的结果，但不能保证其他的读跑到写屏障之前

* 有序性的保证也只是保证了本线程内相关代码不被重排序

  ```java
  volatile i = 0;
  new Thread(() -> {i++});
  new Thread(() -> {i--});
  ```

  i++反编译后的指令：

  ```java
  0: iconst_1			//当int取值 -1~5 时，JVM采用iconst指令将常量压入栈中
  1: istore_1			//将操作数栈顶数据弹出，存入局部变量表的 slot 1
  2: iinc		1, 1	
  ```

  <img src="https://gitee.com/seazean/images/raw/master/Java/JMM-volatile不能保证原子性.png" style="zoom: 67%;" />



***



#### 双端检锁

##### 检锁机制

Double-Checked Locking：双端检锁机制

DCL（双端检锁）机制不一定是线程安全的，原因是有指令重排的存在，加入volatile可以禁止指令重排

```java
public final class Singleton {
    private Singleton() { }
    private static Singleton INSTANCE = null;
    
    public static Singleton getInstance() {
        if(INSTANCE == null) { // t2
            // 首次访问会同步，而之后的使用没有 synchronized
            synchronized(Singleton.class) {
                if (INSTANCE == null) { // t1
                    INSTANCE = new Singleton();
                }
            }
        }
        return INSTANCE;
    }
}
```

不锁INSTANCE的原因：

* INSTANCE 要重新赋值
* INSTANCE 是null，线程加锁之前需要获取对象的引用，null没有引用

实现特点： 

* 懒惰初始化
* 首次使用 getInstance() 才使用 synchronized 加锁，后续使用时无需加锁
* 第一个 if 使用了 INSTANCE 变量，是在同步块之外，但在多线程环境下会产生问题



***



##### DCL问题

getInstance 方法对应的字节码为：

```java
0: 	getstatic 		#2 		// Field INSTANCE:Ltest/Singleton;
3: 	ifnonnull 		37
6: 	ldc 			#3 		// class test/Singleton
8: 	dup
9: 	astore_0
10: monitorenter
11: getstatic 		#2 		// Field INSTANCE:Ltest/Singleton;
14: ifnonnull 27
17: new 			#3 		// class test/Singleton
20: dup
21: invokespecial 	#4 		// Method "<init>":()V
24: putstatic 		#2 		// Field INSTANCE:Ltest/Singleton;
27: aload_0
28: monitorexit
29: goto 37
32: astore_1
33: aload_0
34: monitorexit
35: aload_1
36: athrow
37: getstatic 		#2 		// Field INSTANCE:Ltest/Singleton;
40: areturn
```

* 17 表示创建对象，将对象引用入栈 
* 20 表示复制一份对象引用，引用地址
* 21 表示利用一个对象引用，调用构造方法初始化对象
* 24 表示利用一个对象引用，赋值给 static INSTANCE

步骤 21 和步骤 24 之间不存在数据依赖关系，而且无论重排前后，程序的执行结果在单线程中并没有改变，因此这种重排优化是允许的

* 关键在于 0:getstatic 这行代码在 monitor 控制之外，可以越过 monitor 读取INSTANCE 变量的值
* 当其他线程访问 instance 不为 null 时，由于 instance 实例未必已初始化，那么 t2 拿到的是将是一个未初
  始化完毕的单例返回，这就造成了线程安全的问题

![](https://gitee.com/seazean/images/raw/master/Java/JMM-DCL出现的问题.png)



***



##### 解决方法

指令重排只会保证串行语义的执行一致性（单线程），但并不会关系多线程间的语义一致性

引入volatile，来保证出现指令重排的问题，从而保证单例模式的线程安全性：

```java
private static volatile SingletonDemo INSTANCE = null;
```



***



### ha-be

happens-before 先行发生

Java 内存模型具备一些先天的“有序性”，即不需要通过任何同步手段（volatile、synchronized等）就能够得到保证的安全，这个通常也称为 happens-before 原则，它是可见性与有序性的一套规则总结

不符合 happens-before 规则，JMM 并不能保证一个线程的可见性和有序性

1. 程序次序规则 (Program Order Rule)：一个线程内，逻辑上书写在前面的操作先行发生于书写在后面的操作 ，因为多个操作之间有先后依赖关系，则不允许对这些操作进行重排序

2. 锁定规则 (Monitor Lock Rule)：一个 unLock 操作先行发生于后面（时间的先后）对同一个锁的 lock 操作，所以线程解锁 m 之前对变量的写（锁内的写），对于接下来对 m 加锁的其它线程对该变量的读可见

3. **volatile变量规则** (Volatile Variable Rule)：对 volatile 变量的写操作先行发生于后面对这个变量的读

4. 传递规则 (Transitivity)：具有传递性，如果操作A先行发生于操作B，而操作B又先行发生于操作C，则可以得出操作A先行发生于操作C

5. 线程启动规则 (Thread Start Rule)：Thread对象的start()方法先行发生于此线程中的每一个操作

   ```java
   static int x = 10;//线程 start 前对变量的写，对该线程开始后对该变量的读可见
   new Thread(()->{	System.out.println(x);	},"t1").start();
   ```

6. 线程中断规则 (Thread Interruption Rule)：对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生

7. 线程终止规则 (Thread Termination Rule)：线程中所有的操作都先行发生于线程的终止检测，可以通过Thread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行

8. 对象终结规则（Finaizer Rule）：一个对象的初始化完成（构造函数执行结束）先行发生于它的finalize()方法的开始



***



### 设计模式

#### 终止模式

终止模式之两阶段终止模式：停止标记用 volatile 是为了保证该变量在多个线程之间的可见性

```java
class TwoPhaseTermination {
    //监控线程
    private Thread monitor;
    //停止标记
    private volatile boolean stop = false;;

    //启动监控线程
    public void start() {
        monitor = new Thread(() -> {
            while (true) {
                Thread thread = Thread.currentThread();
                if (stop) {
                    System.out.println("后置处理");
                    break;
                }
                try {
                    Thread.sleep(1000);//睡眠
                    System.out.println(thread.getName() + "执行监控记录");
                } catch (InterruptedException e) {
                   	System.out.println("被打断，退出睡眠");
                }
            }
        });
        monitor.start();
    }

    //停止监控线程
    public void stop() {
        stop = true;
        monitor.interrupt();//让线程尽快退出Timed Waiting
    }
}
//测试
public static void main(String[] args) throws InterruptedException {
    TwoPhaseTermination tpt = new TwoPhaseTermination();
    tpt.start();
    Thread.sleep(3500);
    System.out.println("停止监控");
    tpt.stop();
}
```



****



#### Balking

Balking （犹豫）模式用在一个线程发现另一个线程或本线程已经做了某一件相同的事，那么本线程就无需再做
了，直接结束返回

```java
public class MonitorService {
    // 用来表示是否已经有线程已经在执行启动了
    private volatile boolean starting = false;
    public void start() {
        System.out.println("尝试启动监控线程...");
        synchronized (this) {
            if (starting) {
            	return;
            }
            starting = true;
        }
        // 真正启动监控线程...
    }
}
```

对比保护性暂停模式：保护性暂停模式用在一个线程等待另一个线程的执行结果，当条件不满足时线程等待

例子：希望 doInit() 方法仅被调用一次，下面的实现出现的问题：

* 当 t1 线程进入 init() 准备 doInit()，t2 线程进来，initialized 还为f alse，则 t2 就又初始化一次
* volatile 适合一个线程写，其他线程读的情况，这个代码需要加锁

```java
public class TestVolatile {
    volatile boolean initialized = false;
    
    void init() {
        if (initialized) {
            return;
        }
    	doInit();
    	initialized = true;
    }
    private void doInit() {
    }
}
```





****





## 无锁

### CAS

#### 实现原理

无锁编程：lock free

CAS的全称是Compare-And-Swap，是**CPU并发原语**

* CAS并发原语体现在Java语言中就是sun.misc.Unsafe类的各个方法，调用UnSafe类中的CAS方法，JVM会实现出CAS汇编指令，这是一种完全依赖于硬件的功能，通过它实现了原子操作
* CAS是一种系统原语，原语属于操作系统范畴，是由若干条指令组成 ，用于完成某个功能的一个过程，并且**原语的执行必须是连续的，执行过程中不允许被中断**，也就是说CAS是一条CPU的原子指令，不会造成所谓的数据不一致的问题，所以 CAS 是线程安全的

底层原理：CAS 的底层是 `lock cmpxchg` 指令(X86 架构)，在单核和多核 CPU 下都能够保证比较交换的原子性

* 程序是在单核处理器上运行，会省略 lock 前缀，单处理器自身会维护处理器内的顺序一致性，不需要 lock 前缀的内存屏障效果

* 程序是在多核处理器上运行，会为 cmpxchg 指令加上 lock 前缀（lock cmpxchg）。当某个核执行到带 lock 的指令时，CPU 会执行**总线锁定或缓存锁定**，当这个核把此指令执行完毕，这个过程不会被线程的调度机制所打断，保证了多个线程对内存操作的原子性

作用：比较当前工作内存中的值和主物理内存中的值，如果相同则执行规定操作，否者继续比较直到主内存和工作内存的值一致为止

CAS特点：

* CAS 体现的是**无锁并发、无阻塞并发**，没有使用 synchronized，所以线程不会陷入阻塞，线程不需要频繁切换状态（上下文切换，系统调用）
* CAS 是基于乐观锁的思想

CAS缺点：

- 循环时间长，开销大（因为执行的是do while，如果比较不成功一直在循环，最差的情况某个线程一直取到的值和预期值都不一样，就会无限循环导致饥饿），**使用CAS线程数不要超过CPU的核心数**
- 只能保证一个共享变量的原子操作
  - 对于一个共享变量执行操作时，可以通过循环CAS的方式来保证原子操作
  - 对于多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候只能用锁来保证原子性
- 引出来ABA问题





***



#### 乐观锁

CAS与Synchronized总结：

* Synchronized是从悲观的角度出发：
  总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁。（**共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程**），因此Synchronized我们也将其称之为悲观锁。jdk中的ReentrantLock也是一种悲观锁，**性能较差**
* CAS是从乐观的角度出发:
  总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据。**如果别人修改过，则获取现在最新的值。如果别人没修改过，直接修改共享数据的值**，CAS这种机制我们也可以将其称之为乐观锁。**综合性能较好**！





***



### Atomic

#### 常用API

常见原子类：AtomicInteger、AtomicBoolean、AtomicLong

构造方法：

* `public AtomicInteger()`：初始化一个默认值为0的原子型Integer
* `public AtomicInteger(int initialValue)`：初始化一个指定值的原子型Integer

常用API：

| 方法                                  | 作用                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| public final int get()                | 获取AtomicInteger的值                                        |
| public final int getAndIncrement()    | 以原子方式将当前值加1，返回的是自增前的值                    |
| public final int incrementAndGet()    | 以原子方式将当前值加1，返回的是自增后的值                    |
| public final int getAndSet(int value) | 以原子方式设置为newValue的值，返回旧值                       |
| public final int addAndGet(int data)  | 以原子方式将输入的数值与实例中的值相加并返回<br />实例：AtomicInteger里的value |



***



#### 原理分析

**AtomicInteger原理**：自旋锁  + CAS 算法

CAS算法：有3个操作数（内存值V， 旧的预期值A，要修改的值B）

* 当旧的预期值A == 内存值V   此时可以修改，将V改为B
* 当旧的预期值A !=  内存值V   此时不能修改，并重新获取现在的最新值，重新获取的动作就是自旋 

分析getAndSet方法：

* AtomicInteger：

  ```java
  public final int getAndSet(int newValue) {
      /**
      * this: 		当前对象
      * valueOffset:	内存偏移量，内存地址
      */
      return unsafe.getAndSetInt(this, valueOffset, newValue);
  }
  ```

  valueOffset：表示该变量值在内存中的偏移地址，Unsafe就是根据内存偏移地址获取数据

  ```java
  valueOffset = unsafe.objectFieldOffset
                  (AtomicInteger.class.getDeclaredField("value"));
  //调用本地方法   -->
  public native long objectFieldOffset(Field var1);
  ```

* unsafe类：

  ```java
  //val1: AtomicInteger对象本身  var2: 该对象值得引用地址 var4: 需要变动的数
  public final int getAndSetInt(Object var1, long var2, int var4) {
      int var5;
      do {
          //var5: 用var1和var2找到的内存中的真实值
          var5 = this.getIntVolatile(var1, var2);
      } while(!this.compareAndSwapInt(var1, var2, var5, var4));
  
      return var5;
  }
  ```

  var5：从主内存中拷贝到工作内存中的值（每次都要从主内存拿到最新的值到自己的本地内存），然后执行`compareAndSwapInt()`再和主内存的值进行比较，假设方法返回false，那么就一直执行 while方法，直到期望的值和真实值一样，修改数据

* 变量value用volatile修饰，保证了多线程之间的内存可见性，避免线程从自己的工作缓存中查找变量

  ```java
  private volatile int value
  ```

  CAS 必须借助 volatile 才能读取到共享变量的最新值来实现**比较并交换**的效果

分析 getAndUpdate 方法：

* getAndUpdate：

  ```java
  public final int getAndUpdate(IntUnaryOperator updateFunction) {
      int prev, next;
      do {
          prev = get();	//当前值，cas的期望值
          next = updateFunction.applyAsInt(prev);//期望值更新到该值
      } while (!compareAndSet(prev, next));//自旋
      return prev;
  }
  ```

  函数式接口：可以自定义操作逻辑

  ```java
  AtomicInteger a = new AtomicInteger();
  a.getAndUpdate(i -> i + 10);
  ```

* compareAndSet：

  ```java
  public final boolean compareAndSet(int expect, int update) {
      /**
      * this: 		当前对象
      * valueOffset:	内存偏移量，内存地址
      * expect:		期望的值
      * update: 		更新的值
      */
      return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
  }
  ```

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-compareAndSet方法.png" style="zoom:67%;" />



***



#### 原子引用

原子引用：对 Object 进行原子操作，提供一种读和写都是原子性的对象引用变量

原子引用类：AtomicReference、AtomicStampedReference、AtomicMarkableReference

AtomicReference类：

* 构造方法：`AtomicReference<T> atomicReference = new AtomicReference<T>();`

* 常用API：

  `public final boolean compareAndSet(V expectedValue, V newValue)`：CAS 操作
  `public final void set(V newValue)`：将值设置为 newValue 
  `public final V get()`：返回当前值

```java
public class AtomicReferenceDemo {
    public static void main(String[] args) {
        Student s1 = new Student(33,"z3");
        
        //创建原子引用包装类
        AtomicReference<Student> atomicReference = new AtomicReference<>();
        //设置主内存共享变量为s1
        atomicReference.set(s1);

        //比较并交换，如果现在主物理内存的值为z3，那么交换成l4
        while (true) {
            Student s2 = new Student(44,"l4");
            if (atomicReference.compareAndSet(s1, s2)) {
                break;
            }
        }
        System.out.println(atomicReference.get());
    }
}

class Student {
    private int id;
    private String name;
    //。。。。
}
```



***



#### 原子数组

原子数组类：AtomicIntegerArray、AtomicLongArray、AtomicReferenceArray

AtomicIntegerArray类方法：

```java
/**
*   i		the index
* expect 	the expected value
* update 	the new value
*/
public final boolean compareAndSet(int i, int expect, int update) {
    return compareAndSetRaw(checkedByteOffset(i), expect, update);
}
```



***



#### 原子更新器

原子更新器类：AtomicReferenceFieldUpdater、AtomicIntegerFieldUpdater、AtomicLongFieldUpdater

利用字段更新器，可以针对对象的某个域（Field）进行原子操作，只能配合 volatile 修饰的字段使用，否则会出现异常`IllegalArgumentException: Must be volatile type`

常用API：
`static <U> AtomicIntegerFieldUpdater<U> newUpdater(Class<U> c, String fieldName)`：构造
`abstract boolean compareAndSet(T obj, int expect, int update)`：CAS

```java
public class UpdateDemo {
    private volatile int field;
    
    public static void main(String[] args) {
        AtomicIntegerFieldUpdater fieldUpdater = AtomicIntegerFieldUpdater
            		.newUpdater(UpdateDemo.class, "field");
        UpdateDemo updateDemo = new UpdateDemo();
        fieldUpdater.compareAndSet(updateDemo, 0, 10);
        System.out.println(updateDemo.field);//10
    }
}
```



***



#### 原子累加器

原子累加器类：LongAdder、DoubleAdder、LongAccumulator、DoubleAccumulator

LongAdder和LongAccumulator区别：

相同点：

* LongAddr与LongAccumulator类都是使用非阻塞算法CAS实现的，
* LongAddr类是LongAccumulator类的一个特例，只是LongAccumulator提供了更强大的功能，可以自定义累加规则，当accumulatorFunction为null时就等价于LongAddr

不同点：

* 调用casBase时，LongAccumulator使用function.applyAsLong(b = base, x)来计算，LongAddr使用casBase(b = base, b + x)来计算
* LongAccumulator类功能更加强大，构造方法参数中

  * accumulatorFunction是一个双目运算器接口，可以指定累加规则，比如累加或者相乘，其根据输入的两个参数返回一个计算值，LongAdder内置累加规则
  * identity则是LongAccumulator累加器的初始值，LongAccumulator可以为累加器提供非0的初始值，而LongAdder只能提供默认的0




***



### Adder

#### 优化CAS

LongAdder是Java8提供的类，跟AtomicLong有相同的效果，但对CAS机制进行了优化，尝试使用分段CAS以及自动分段迁移的方式来大幅度提升多线程高并发执行CAS操作的性能

CAS底层实现是在一个循环中不断地尝试修改目标值，直到修改成功。如果竞争不激烈修改成功率很高，否则失败率很高，失败后这些重复的原子性操作会耗费性能（导致大量线程**空循环，自旋转**）

优化核心思想：数据分离，将AtomicLong的单点的**更新压力分担到各个节点**，在低并发的时候直接更新，可以保障和AtomicLong的性能基本一致，而在高并发的时候通过分散提高了性能



***



#### 优化机制

##### 分段机制

分段 CAS 机制：

* 在发生竞争时，创建Cell数组用于将不同线程的操作离散（通过hash等算法映射）到不同的节点上
* 设置多个累加单元（会根据需要扩容，最大为CPU核数），Therad-0 累加 Cell[0]，而 Thread-1 累加 Cell[1] 等，最后将结果汇总
* 在累加时操作的不同的 Cell 变量，因此减少了 CAS 重试失败，从而提高性能



***



##### 分段迁移

自动分段迁移机制：某个Cell的value执行CAS失败，就会自动寻找另一个Cell分段内的value值进行CAS操作

```java
// 累加单元数组, 懒惰初始化
transient volatile Cell[] cells;
// 基础值, 如果没有竞争, 则用 cas 累加这个域
transient volatile long base;
// 在 cells 创建或扩容时, 置为 1, 表示加锁
transient volatile int cellsBusy;
```

Cells占用内存是相对比较大的，是惰性加载的，在无竞争的情况下直接更新base域，在第一次发生竞争的时候（CAS失败）就会创建一个大小为2的cells数组，每次扩容都是加倍

扩容数组等行为只能有一个线程执行，因此需要一个锁，这里通过 CAS 更新 cellsBusy 来实现一个简单的lock

CAS锁：

```java
// 不要用于实践！！！
public class LockCas {
    private AtomicInteger state = new AtomicInteger(0);
    public void lock() {
        while (true) {
            if (state.compareAndSet(0, 1)) {
                break;
            }
    	}
    }
    public void unlock() {
        System.out.println("unlock...");
        state.set(0);
    }
}
```



***



##### 伪共享

Cell为累加单元：数组访问索引是通过Thread里的threadLocalRandomProbe域取模实现的，这个域是ThreadLocalRandom更新的

```java
@sun.misc.Contended static final class Cell {
    volatile long value;
    Cell(long x) { value = x; }
    // 最重要的方法, 用来 cas 方式进行累加, prev 表示旧值, next 表示新值
    final boolean cas(long prev, long next) {
    	return UNSAFE.compareAndSwapLong(this, valueOffset, prev, next);
    }
    // 省略不重要代码
}
```

@sun.misc.Contended注解：防止缓存行伪共享

Cell 是数组形式，**在内存中是连续存储的**，一个 Cell 为 24 字节（16 字节的对象头和 8 字节的 value），每一个 cache line 为 64 字节，因此缓存行可以存下 2 个的 Cell 对象，当Core-0 要修改 Cell[0]、Core-1 要修改 Cell[1]，无论谁修改成功都会导致对方 Core 的缓存行失效，需要重新去主存获取

![](https://gitee.com/seazean/images/raw/master/Java/JUC-伪共享1.png)

@sun.misc.Contended：在使用此注解的对象或字段的前后各增加 128 字节大小的padding，使用2倍于大多数硬件缓存行让 CPU 将对象预读至缓存时占用不同的缓存行，这样就不会造成对方缓存行的失效

![](https://gitee.com/seazean/images/raw/master/Java/JUC-伪共享2.png)





***



#### 成员方法

* add：累加方法

  ```java
  public void add(long x) {
      // as 为累加单元数组  b 为基础值  x 为累加值
      Cell[] as; long b, v; int m; Cell a;
      // 1. as 有值, 表示已经发生过竞争, 进入 if
      // 2. cas 给 base 累加时失败了, 表示 base 发生了竞争, 进入 if
      if ((as = cells) != null || !casBase(b = base, b + x)) {
          // uncontended 表示 cell 没有竞争
          boolean uncontended = true;
          if (
              // as 还没有创建
              as == null || (m = as.length - 1) < 0 ||
              // 当前线程对应的 cell 还没有创建
              (a = as[getProbe() & m]) == null ||
              // 当前线程的cell累加失败，a为当前线程的cell
              !(uncontended = a.cas(v = a.value, v + x))
              //uncontended = false代表有竞争
          ) {
              // 进入 cell 数组创建、cell 创建的流程
              longAccumulate(x, null, uncontended);
          }
      }
  }
  ```

* longAccumulate：cell数组创建

  ```java
  							// x  			null 			false
  final void longAccumulate(long x, LongBinaryOperator fn, boolean w...ed) {
      int h;
      // 当前线程还没有对应的 cell, 需要随机生成一个 h 值用来将当前线程绑定到 cell
      if ((h = getProbe()) == 0) {
          ThreadLocalRandom.current(); // 初始化 probe
          h = getProbe();	//h 对应新的 probe 值, 用来对应 cell
          wasUncontended = true;
      }
      //collide 为 true 表示需要扩容
      boolean collide = false; 
      for (;;) {
          Cell[] as; Cell a; int n; long v;
          // cells已经创建
          if ((as = cells) != null && (n = as.length) > 0) {
              // 线程对应的cell还没被创建
              if ((a = as[(n - 1) & h]) == null) {
                  // 判断 cellsBusy 是否被锁
                  if (cellsBusy == 0) {   
                      // 创建 cell, 初始累加值为 x
                      Cell r = new Cell(x);  
                      // 为 cellsBusy 加锁,
                      if (cellsBusy == 0 && casCellsBusy()) {
                          boolean created = false;
                          try {
                              Cell[] rs; int m, j;
                              if ((rs = cells) != null &&
                                  (m = rs.length) > 0 &&
                                  rs[j = (m - 1) & h] == null) {
                                  rs[j] = r;
                                  created = true;
                              }
                          } finally {
                              cellsBusy = 0;
                          }
                          if (created)
                              break;// 成功则 break, 否则继续 continue 循环
                          continue;
                      }
                  }
                  collide = false;
              }
              // 有竞争, 改变线程对应的 cell 来重试 cas
              else if (!wasUncontended)
                  wasUncontended = true;
              //cas尝试累加, fn配合LongAccumulator不为null, 配合LongAdder为null
              else if (a.cas(v = a.value, ((fn == null) ? v + x :
                                           fn.applyAsLong(v, x))))
                  break;
              // cells长度已经超过了最大长度或者已经扩容, 改变线程对应的cell来重试cas
              else if (n >= NCPU || cells != as)
                  collide = false;            // At max size or stale
              // collide 为 false 进入此分支, 就不会进入下面的 else if 进行扩容了
              else if (!collide)
                  collide = true;
              //加锁扩容
              else if (cellsBusy == 0 && casCellsBusy()) {
                  try {
                      if (cells == as) {      // Expand table unless stale
                          Cell[] rs = new Cell[n << 1];
                          for (int i = 0; i < n; ++i)
                              rs[i] = as[i];
                          cells = rs;
                      }
                  } finally {
                      cellsBusy = 0;
                  }
                  collide = false;
                  continue;、
              }
              h = advanceProbe(h);
          }
          //还没有 cells, 尝试给 cellsBusy 加锁
          else if (cellsBusy == 0 && cells == as && casCellsBusy()) {
              boolean init = false;
              try { 
                  // 初始化 cells, 最开始长度为2, 填充一个初始累加值为x的cell
                  if (cells == as) {
                      Cell[] rs = new Cell[2];
                      rs[h & 1] = new Cell(x);//填充线程对应的cell
                      cells = rs;
                      init = true;
                  }
              } finally {
                  cellsBusy = 0;
              }
              if (init)
                  break;
          }
          // 上两种情况失败, 尝试给 base 累加
          else if (casBase(v = base, ((fn == null) ? v + x :
                                      fn.applyAsLong(v, x))))
              break;                          // Fall back on using base
      }
  }
  ```

* sum：获取最终结果通过 sum 整合



***



### ABA

ABA问题：当进行获取主内存值时，该内存值在写入主内存时已经被修改了N次，但是最终又改成原来的值

其他线程先把A改成B又改回A，主线程仅能判断出共享变量的值与最初值 A 是否相同，不能感知到这种从 A 改为 B 又 改回 A 的情况，这时CAS虽然成功，但是过程存在问题

* 构造方法
  `public AtomicStampedReference(V initialRef, int initialStamp)`：初始值和初始版本号

* 常用API：
  ` public boolean compareAndSet(V expectedReference, V newReference, int expectedStamp, int newStamp)`：CAS
  `public void set(V newReference, int newStamp)`：设置值和版本号

  `public V getReference()`：返回引用的值
  `public int getStamp()`：返回当前版本号

```java
public static void main(String[] args) {
    AtomicStampedReference<Integer> atomicReference = new AtomicStampedReference<>(100,1);
    int startStamp = atomicReference.getStamp();
    new Thread(() ->{
        int stamp = atomicReference.getStamp();
        atomicReference.compareAndSet(100, 101, stamp, stamp + 1);
        stamp = atomicReference.getStamp();
        atomicReference.compareAndSet(101, 100, stamp, stamp + 1);
    },"t1").start();

    new Thread(() ->{
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        if (!atomicReference.compareAndSet(100, 200, startStamp, startStamp + 1)) {
            System.out.println(atomicReference.getReference());//100
            System.out.println(Thread.currentThread().getName() + "线程修改失败");
        }
    },"t2").start();
}
```





***



### Unsafe

Unsafe是CAS的核心类，由于Java无法直接访问底层系统，需要通过本地 (Native) 方法来访问

Unsafe类存在sun.misc包，其中所有方法都是native修饰的，都是直接调用操作系统底层资源执行相应的任务，基于该类可以直接操作特定的内存数据，其内部方法操作类似C的指针

模拟实现原子整数：

```java
public static void main(String[] args) {
    MyAtomicInteger atomicInteger = new MyAtomicInteger(10);
    if (atomicInteger.compareAndSwap(20)) {
        System.out.println(atomicInteger.getValue());
    }
}

class MyAtomicInteger {
    private static final Unsafe UNSAFE;
    private static final long VALUE_OFFSET;
    private volatile int value;

    static {
        try {
            Field theUnsafe = Unsafe.class.getDeclaredField("theUnsafe");
            theUnsafe.setAccessible(true);
            UNSAFE = (Unsafe) theUnsafe.get(null);
            VALUE_OFFSET = UNSAFE.objectFieldOffset(
                		   MyAtomicInteger.class.getDeclaredField("value"));
        } catch (NoSuchFieldException | IllegalAccessException e) {
            e.printStackTrace();
            throw new RuntimeException();
        }
    }

    public MyAtomicInteger(int value) {
        this.value = value;
    }
    public int getValue() {
        return value;
    }

    public boolean compareAndSwap(int update) {
        while (true) {
            int prev = this.value;
            int next = update;
            //							当前对象  内存偏移量    期望值 更新值
            if (UNSAFE.compareAndSwapInt(this, VALUE_OFFSET, prev, update)) {
                System.out.println("CAS成功");
                return true;
            }
        }
    }
}
```



***



### final

#### 原理

```java
public class TestFinal {
	final int a = 20;
}
```

字节码：

```java
0: aload_0
1: invokespecial #1 // Method java/lang/Object."<init>":()V
4: aload_0
5: bipush 20	//将值直接放入栈中
7: putfield #2 // Field a:I
<-- 写屏障
10: return
```

final 变量的赋值通过 putfield 指令来完成，在这条指令之后也会加入写屏障，保证在其它线程读到它的值时不会出现为 0 的情况

其他线程访问final修饰的变量会复制一份放入栈中，效率更高



***



#### 不可变

不可变：如果一个对象不能够修改其内部状态（属性），那么就是不可变对象

不可变对象线程安全的，因为不存在并发修改，是另一种避免竞争的方式

String 类也是不可变的，该类和类中所有属性都是 final 的

* 类用 final 修饰保证了该类中的方法不能被覆盖，防止子类无意间破坏不可变性保

* 属性用 final 修饰保证了该属性是只读的，不能修改

  ```java
  public final class String
      implements java.io.Serializable, Comparable<String>, CharSequence {
      /** The value is used for character storage. */
      private final char value[];
      //....
  }
  ```

更改 String 类数据时，会构造新字符串对象，生成新的 char[] value，这种通过创建副本对象来避免共享的方式称之为**保护性拷贝（defensive copy）**



***



### State

无状态：成员变量保存的数据也可以称为状态信息，无状态就是没有成员变量

Servlet 为了保证其线程安全，一般不为 Servlet 设置成员变量，这种没有任何成员变量的类是线程安全的



***



### Local

#### 基本介绍

ThreadLocal 类用来提供线程内部的局部变量，这种变量在多线程环境下访问（通过get和set方法访问）时能保证各个线程的变量相对独立于其他线程内的变量，分配在 TLAB

ThreadLocal 实例通常来说都是 `private static` 类型的，属于一个线程的本地变量，用于关联线程和线程上下文。每个线程都会在 ThreadLocal 中保存一份该线程独有的数据，所以是线程安全的

ThreadLocal 作用：

* 线程并发：应用在多线程并发的场景下

* 传递数据：通过ThreadLocal实现在同一线程不同函数或组件中传递公共变量，减少传递复杂度

* 线程隔离：每个线程的变量都是独立的，不会互相影响

对比 synchronized：

|        | synchronized                                                 | ThreadLocal                                                  |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 原理   | 同步机制采用**以时间换空间**的方式，只提供了一份变量，让不同的线程排队访问 | ThreadLocal采用**以空间换时间**的方式，为每个线程都提供了一份变量的副本，从而实现同时访问而相不干扰 |
| 侧重点 | 多个线程之间访问资源的同步                                   | 多线程中让每个线程之间的数据相互隔离                         |



***



#### 基本使用

##### 常用方法

| 方法                       | 描述                         |
| -------------------------- | ---------------------------- |
| ThreadLocal<>()            | 创建 ThreadLocal 对象        |
| protected T initialValue() | 返回当前线程局部变量的初始值 |
| public void set( T value)  | 设置当前线程绑定的局部变量   |
| public T get()             | 获取当前线程绑定的局部变量   |
| public void remove()       | 移除当前线程绑定的局部变量   |

```java
public class MyDemo {

    private static ThreadLocal<String> tl = new ThreadLocal<>();

    private String content;

    private String getContent() {
        // 获取当前线程绑定的变量
        return tl.get();
    }

    private void setContent(String content) {
        // 变量content绑定到当前线程
        tl.set(content);
    }

    public static void main(String[] args) {
        MyDemo demo = new MyDemo();
        for (int i = 0; i < 5; i++) {
            Thread thread = new Thread(new Runnable() {
                @Override
                public void run() {
                    // 设置数据
                    demo.setContent(Thread.currentThread().getName() + "的数据");
                    System.out.println("-----------------------");
                    System.out.println(Thread.currentThread().getName() + "--->" + demo.getContent());
                }
            });
            thread.setName("线程" + i);
            thread.start();
        }
    }
}
```



***



##### 应用场景

ThreadLocal 适用于如下两种场景

- 每个线程需要有自己单独的实例
- 实例需要在多个方法中共享，但不希望被多线程共享

**事务管理**，ThreadLocal方案有两个突出的优势： 

1. 传递数据：保存每个线程绑定的数据，在需要的地方可以直接获取，避免参数直接传递带来的代码耦合问题

2. 线程隔离：各线程之间的数据相互隔离却又具备并发性，避免同步方式带来的性能损失

```java
public class JdbcUtils {
    // ThreadLocal对象，将connection绑定在当前线程中
    private static final ThreadLocal<Connection> tl = new ThreadLocal();
    // c3p0 数据库连接池对象属性
    private static final ComboPooledDataSource ds = new ComboPooledDataSource();
    // 获取连接
    public static Connection getConnection() throws SQLException {
        //取出当前线程绑定的connection对象
        Connection conn = tl.get();
        if (conn == null) {
            //如果没有，则从连接池中取出
            conn = ds.getConnection();
            //再将connection对象绑定到当前线程中，非常重要的操作
            tl.set(conn);
        }
        return conn;
    }
	// ...
}
```

用 ThreadLocal 使 SimpleDateFormat 从独享变量变成单个线程变量：

```java
public class ThreadLocalDateUtil {
    private static ThreadLocal<DateFormat> threadLocal = new ThreadLocal<DateFormat>() {
        @Override
        protected DateFormat initialValue() {
            return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        }
    };

    public static Date parse(String dateStr) throws ParseException {
        return threadLocal.get().parse(dateStr);
    }

    public static String format(Date date) {
        return threadLocal.get().format(date);
    }
}
```



****



#### 底层结构

JDK8 以前：每个 ThreadLocal 都创建一个 Map，然后用线程作为 Map 的 key，要存储的局部变量作为 Map 的 value，这样就能达到各个线程的局部变量隔离的效果

![](https://gitee.com/seazean/images/raw/master/Java/JUC-ThreadLocal数据结构JDK8前.png)

JDK8 以后：每个 Thread 维护一个 ThreadLocalMap，这个 Map 的 key 是 ThreadLocal 实例本身，value 才是真正要存储的值 Object

* 每个 Thread 线程内部都有一个 Map (ThreadLocalMap)
* Map 里面存储 ThreadLocal 对象（key）和线程的变量副本（value）
* Thread 内部的 Map 是由 ThreadLocal 维护的，由 ThreadLocal 负责向 map 获取和设置线程的变量值。
* 对于不同的线程，每次获取副本值时，别的线程并不能获取到当前线程的副本值，形成副本的隔离，互不干扰

![](https://gitee.com/seazean/images/raw/master/Java/JUC-ThreadLocal数据结构JDK8后.png)

JDK8 前后对比：

* 每个 Map 存储的 Entry 数量会变少，因为之前的存储数量由 Thread 的数量决定，现在由 ThreadLocal 的数量决定，在实际编程当中，往往 ThreadLocal 的数量要少于 Thread 的数量
* 当 Thread 销毁之后，对应的 ThreadLocalMap 也会随之销毁，能减少内存的使用



***



#### 成员方法

* set()

  * 获取当前线程，并根据当前线程获取一个Map
  * 获取的Map不为空，则将参数设置到Map中（当前ThreadLocal的引用作为key）
  * 如果Map为空，则给该线程创建 Map，并设置初始值

  ```java
  // 设置当前线程对应的ThreadLocal的值
  public void set(T value) {
      // 获取当前线程对象
      Thread t = Thread.currentThread();
      // 获取此线程对象中维护的ThreadLocalMap对象
      ThreadLocalMap map = getMap(t);
      // 判断map是否存在
      if (map != null)
          // 存在则调用map.set设置此实体entry
          map.set(this, value);
      else
          // 调用createMap进行ThreadLocalMap对象的初始化
          createMap(t, value);
  }
  
  // 获取当前线程Thread对应维护的ThreadLocalMap 
  ThreadLocalMap getMap(Thread t) {
      return t.threadLocals;
  }
  // 创建当前线程Thread对应维护的ThreadLocalMap 
  void createMap(Thread t, T firstValue) {
      //这里的this是调用此方法的threadLocal
      t.threadLocals = new ThreadLocalMap(this, firstValue);
  }
  ```

* get()

  ```java
  // 获取当前线程的 ThreadLocalMap 变量，如果存在则返回值，不存在则创建并返回初始值
  public T get() {
      // 获取当前线程对象
      Thread t = Thread.currentThread();
      // 获取此线程对象中维护的ThreadLocalMap对象
      ThreadLocalMap map = getMap(t);
      // 如果此map存在
      if (map != null) {
          // 以当前的ThreadLocal 为 key，调用getEntry获取对应的存储实体e
          ThreadLocalMap.Entry e = map.getEntry(this);
          // 对e进行判空 
          if (e != null) {
              @SuppressWarnings("unchecked")
              // 获取存储实体 e 对应的 value值
              T result = (T)e.value;
              return result;
          }
      }
      /*初始化 : 有两种情况有执行当前代码
        第一种情况: map不存在，表示此线程没有维护的ThreadLocalMap对象
        第二种情况: map存在, 但是没有与当前ThreadLocal关联的entry*/
      return setInitialValue();
  }
  
  // 初始化
  private T setInitialValue() {
      // 调用initialValue获取初始化的值，此方法可以被子类重写, 如果不重写默认返回null
      T value = initialValue();
      Thread t = Thread.currentThread();
      ThreadLocalMap map = getMap(t);
      // 判断map是否存在
      if (map != null)
          // 存在则调用map.set设置此实体entry
          map.set(this, value);
      else
          // 调用createMap进行ThreadLocalMap对象的初始化中
          createMap(t, value);
      // 返回设置的值value
      return value;
  }
  ```

* remove()

  ```java
  // 删除当前线程中保存的ThreadLocal对应的实体entry
  public void remove() {
      // 获取当前线程对象中维护的ThreadLocalMap对象
      ThreadLocalMap m = getMap(Thread.currentThread());
      // 如果此map存在
      if (m != null)
          // 存在则调用map.remove，this时当前ThreadLocal，以this为key删除对应的实体
          m.remove(this);
  }
  ```

* initialValue()

  作用：返回该线程局部变量的初始值。

  * 延迟调用的方法，在set方法还未调用而先调用了get方法时才执行，并且仅执行1次

  * 该方法缺省（默认）实现直接返回一个``null``

  * 如果想要一个初始值，可以重写此方法， 该方法是一个``protected``的方法，为了让子类覆盖而设计的

  ```java
  protected T initialValue() {
      return null;
  }
  ```

  

***



#### LocalMap

##### 成员属性

ThreadLocalMap是ThreadLocal的内部类，没有实现Map接口，用独立的方式实现了Map的功能，其内部Entry也是独立实现

```java
// 初始容量 —— 2的整次幂
private static final int INITIAL_CAPACITY = 16;

// 存放数据的table，Entry类的定义在下面分析，同样，数组长度必须是2的整次幂。
private Entry[] table;

//数组里面entrys的个数，可以用于判断table当前使用量是否超过阈值
private int size = 0;

// 进行扩容的阈值，表使用量大于它的时候进行扩容。
private int threshold; // Default to 0
```

存储结构 Entry：

* Entry继承WeakReference，key是弱引用，目的是将ThreadLocal对象的生命周期和线程生命周期解绑
* Entry限制只能用ThreadLocal作为key，key为null (entry.get() == null) 意味着key不再被引用，entry也可以从table中清除

```java
static class Entry extends WeakReference<ThreadLocal<?>> {
    Object value;
    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```



***



##### 成员方法

* 构造方法

  ```java
  ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
      // 初始化table，创建一个长度为16的Entry数组
      table = new Entry[INITIAL_CAPACITY];
      // 计算索引
      int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
      // 设置值
      table[i] = new Entry(firstKey, firstValue);
      size = 1;
      // 设置阈值
      setThreshold(INITIAL_CAPACITY);
  }
  ```

* hashcode

  ```java
  private final int threadLocalHashCode = nextHashCode();
  // 通过线程安全的方式操作加减，适合多线程情况下的使用
  private static AtomicInteger nextHashCode =  new AtomicInteger();
  //特殊的hash值
  private static final int HASH_INCREMENT = 0x61c88647;
  
  private static int nextHashCode() {
      return nextHashCode.getAndAdd(HASH_INCREMENT);
  }
  ```

  ThreadLocal 的散列方式称之为 **斐波那契散列**。这里定义了一个AtomicInteger，每次获取当前值并加上HASH_INCREMENT，这个值跟斐波那契数列（黄金分割数）有关，这样做可以尽量避免hash冲突，让哈希码能均匀的分布在2的n次方的数组里

* set()

  ```java
  private void set(ThreadLocal<?> key, Object value) {
      ThreadLocal.ThreadLocalMap.Entry[] tab = table;
      int len = tab.length;
      // 计算索引
      int i = key.threadLocalHashCode & (len-1);
      // 使用线性探测法查找元素
      for (ThreadLocal.ThreadLocalMap.Entry e = tab[i];
           e != null;
           e = tab[i = nextIndex(i, len)]) {
          ThreadLocal<?> k = e.get();
          // ThreadLocal 对应的 key 存在，直接覆盖之前的值
          if (k == key) {
              e.value = value;
              return;
          }
          // key为 null，但是值不为 null，说明之前的 ThreadLocal 对象已经被回收了，
          // 当前数组中的 Entry 是一个陈旧（stale）的元素
          if (k == null) {
              //用新元素替换陈旧的元素，这个方法进行了不少的垃圾清理动作，防止内存泄漏
              replaceStaleEntry(key, value, i);
              return;
          }
      }
  
      //ThreadLocal对应的key不存在并且没有找到陈旧的元素，则在空元素的位置创建一个新的Entry。
      tab[i] = new Entry(key, value);
      int sz = ++size;
      
      // 清除e.get()==null的元素，
      // 如果没有清除任何entry并且当前使用量达到了负载因子所定义，那么进行 rehash
      if (!cleanSomeSlots(i, sz) && sz >= threshold)
          rehash();
  }
  
  // 获取环形数组的下一个索引
  private static int nextIndex(int i, int len) {
      return ((i + 1 < len) ? i + 1 : 0);
  }
  
  ```

  ThreadLocalMap 使用**线性探测法来解决哈希冲突**：

  * 该方法一次探测下一个地址，直到有空的地址后插入，若整个空间都找不到空余的地址，则产生溢出
  * 在探测过程中 ThreadLocal 会释放 key 为 NULL，value 不为 NULL 的脏 Entry对象，防止内存泄漏
  * 假设当前table长度为16，计算出来key的hash值为14，如果table[14]上已经有值，并且其key与当前key不一致，那么就发生了hash冲突，这个时候将14加1得到15，取table[15]进行判断，如果还是冲突会回到0，取table[0]，以此类推，直到可以插入，可以把Entry[]  table看成一个**环形数组**

  线性探测法会出现**堆积问题**，一般采取平方探测法解决

* 扩容：

  rehash 会触发一次全量清理，如果数组长度大于等于长度的(2/3 * 3/4 = 1/2)，则进行 resize

  ```java
  // rehash 条件
  private void setThreshold(int len) {
    threshold = len * 2 / 3;
  }
  // 扩容条件
  private void rehash() {
    expungeStaleEntries();
    if (size >= threshold - threshold / 4)
      resize();
  }
  ```

  Entry 数组为扩容为 原来的2倍 ，重新计算 key 的散列值，如果遇到 key 为 NULL 的情况，会将其 value 也置为 NULL，帮助虚拟机进行GC

  ```java
  // 具体的扩容函数
  private void resize() {
  }
  ```



***



#### 内存泄漏

Memory leak：内存泄漏是指程序中动态分配的堆内存由于某种原因未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果，内存泄漏的堆积终将导致内存溢出

* 如果 key 使用强引用：

  使用完 ThreadLocal ，threadLocal Ref 被回收，但是 threadLocalMap 的 Entry 强引用了 threadLocal，造成 threadLocal 无法被回收，无法完全避免内存泄漏

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ThreadLocal内存泄漏强引用.png" style="zoom:67%;" />

* 如果 key 使用弱引用：

  使用完 ThreadLocal ，threadLocal Ref 被回收，ThreadLocalMap 只持有 ThreadLocal 的弱引用，所以threadlocal 也可以被回收，此时 Entry 中的 key=null。但没有手动删除这个 Entry 或者 CurrentThread 依然运行，依然存在强引用链，value 不会被回收，而这块 value 永远不会被访问到，导致 value 内存泄漏

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ThreadLocal内存泄漏弱引用.png" style="zoom:67%;" />

* 两个主要原因：

  * 没有手动删除这个 Entry
  * CurrentThread 依然运行

根本原因：ThreadLocalMap 是 Thread的一个属性，生命周期跟 Thread 一样长，如果没有手动删除对应 Entry 就会导致内存泄漏

解决方法：使用完 ThreadLocal 中存储的内容后将它 **remove** 掉就可以

ThreadLocal 内部解决方法：在 ThreadLocalMap 中的 set/getEntry 方法中，会对 key 进行判断，如果为 null（ThreadLocal 为 null）的话，会对 Entry 进行垃圾回收。所以**使用弱引用比强引用多一层保障**，就算不调用 remove，也有机会进行 GC



***



#### 变量传递

##### 基本使用

父子线程：**创建子线程的线程是父线程**，比如实例中的 main 线程就是父线程

ThreadLocal 中存储的是线程的局部变量，如果想实现线程间局部变量传递可以使用 InheritableThreadLocal 类

```java
public static void main(String[] args) {
    ThreadLocal<String> threadLocal = new InheritableThreadLocal<>();
    threadLocal.set("父线程设置的值");

    new Thread(() -> System.out.println("子线程输出：" + threadLocal.get())).start();
}
// 子线程输出：父线程设置的值
```



***



##### 实现原理

InheritableThreadLocal 源码：

```java
public class InheritableThreadLocal<T> extends ThreadLocal<T> {
    protected T childValue(T parentValue) {
        return parentValue;
    }
    ThreadLocalMap getMap(Thread t) {
       return t.inheritableThreadLocals;
    }
    void createMap(Thread t, T firstValue) {
        t.inheritableThreadLocals = new ThreadLocalMap(this, firstValue);
    }
}
```

实现父子线程间的局部变量共享需要追溯到 Thread 对象的构造方法：

```java
private void init(ThreadGroup g, Runnable target, String name,
                  long stackSize, AccessControlContext acc,
                  // 该参数默认是 true
                  boolean inheritThreadLocals) {
  // ...
    Thread parent = currentThread();

    //判断父线程（创建子线程的线程）的 inheritableThreadLocals 属性不为 NULL
    if (inheritThreadLocals && parent.inheritableThreadLocals != null) {
        //复制父线程的 inheritableThreadLocals 属性，实现父子线程局部变量共享
        this.inheritableThreadLocals =
            ThreadLocal.createInheritedMap(parent.inheritableThreadLocals); 
    }
    // ..
}
static ThreadLocalMap createInheritedMap(ThreadLocalMap parentMap) {
    return new ThreadLocalMap(parentMap);
}
```

```java
private ThreadLocalMap(ThreadLocalMap parentMap) {
    Entry[] parentTable = parentMap.table;
    int len = parentTable.length;
    setThreshold(len);
    table = new Entry[len];
	// 逐个复制父线程 ThreadLocalMap 中的数据
    for (int j = 0; j < len; j++) {
        Entry e = parentTable[j];
        if (e != null) {
            @SuppressWarnings("unchecked")
            ThreadLocal<Object> key = (ThreadLocal<Object>) e.get();
            if (key != null) {
                // 调用的是 InheritableThreadLocal#childValue(T parentValue)
                Object value = key.childValue(e.value);
                Entry c = new Entry(key, value);
                int h = key.threadLocalHashCode & (len - 1);
                while (table[h] != null)
                    h = nextIndex(h, len);
                table[h] = c;
                size++;
            }
        }
    }
}
```



参考文章：https://blog.csdn.net/feichitianxia/article/details/110495764





***





## 线程池

### 基本概述

线程池：一个容纳多个线程的容器，容器中的线程可以重复使用，省去了频繁创建和销毁线程对象的操作

线程池作用：

1. 降低资源消耗，减少了创建和销毁线程的次数，每个工作线程都可以被重复利用，可执行多个任务
2. 提高响应速度，当任务到达时，如果有线程可以直接用，不会出现系统僵死
3. 提高线程的可管理性，如果无限制的创建线程，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控

线程池的核心思想：**线程复用**，同一个线程可以被重复使用，来处理多个任务

池化技术 (Pool) ：一种编程技巧，核心思想是资源复用，在请求量大时能优化应用性能，降低系统频繁建连的资源开销



***



### 阻塞队列

#### 基本介绍

有界队列和无界队列：

* 有界队列：有固定大小的队列，比如设定了固定大小的 LinkedBlockingQueue，又或者大小为 0

* 无界队列：没有设置固定大小的队列，这些队列可以直接入队，直到溢出，一般不会有到这么大的容量（超过 Integer.MAX_VALUE），所以相当于 “无界”

java.util.concurrent.BlockingQueue 接口有以下阻塞队列的实现：**FIFO 队列** 

- ArrayBlockQueue：由数组结构组成的有界阻塞队列
- LinkedBlockingQueue：由链表结构组成的无界（默认大小 Integer.MAX_VALUE）的阻塞队列
- PriorityBlockQueue：支持优先级排序的无界阻塞队列
- DelayQueue：使用优先级队列实现的延迟无界阻塞队列
- SynchronousQueue：不存储元素的阻塞队列，每一个生产线程会阻塞到有一个put的线程放入元素为止
- LinkedTransferQueue：由链表结构组成的无界阻塞队列
- LinkedBlockingDeque：由链表结构组成的**双向**阻塞队列

与普通队列(LinkedList、ArrayList等)的不同点在于阻塞队列中阻塞添加和阻塞删除方法，以及线程安全：

* 阻塞添加 take()：当阻塞队列元素已满时，添加队列元素的线程会被阻塞，直到队列元素不满时才重新唤醒线程执行
* 阻塞删除 put()：在队列元素为空时，删除队列元素的线程将被阻塞，直到队列不为空再执行删除操作(一般都会返回被删除的元素)



***



#### 核心方法

| 方法类型         | 抛出异常  | 特殊值   | 阻塞   | 超时               |
| ---------------- | --------- | -------- | ------ | ------------------ |
| 插入             | add(e)    | offer(e) | put(e) | offer(e,time,unit) |
| 移除             | remove()  | poll()   | take() | poll(time,unit)    |
| 检查（队首元素） | element() | peek()   | 不可用 | 不可用             |

* 抛出异常组：
  * 当阻塞队列满时：在往队列中 add 插入元素会抛出 IIIegalStateException: Queue full
  * 当阻塞队列空时：再往队列中 remove 移除元素，会抛出 NoSuchException
* 特殊值组：
  * 插入方法：成功 true，失败 false
  * 移除方法：成功返回出队列元素，队列没有就返回 null
* 阻塞组：
  * 当阻塞队列满时，生产者继续往队列里 put 元素，队列会一直阻塞生产线程直到 put 数据或响应中断退出
  * 当阻塞队列空时，消费者线程试图从队列里 take 元素，队列会一直阻塞消费者线程直到队列可用
* 超时退出：当阻塞队列满时，队里会阻塞生产者线程一定时间，超过限时后生产者线程会退出



***



#### 链表队列

##### 入队出队

LinkedBlockingQueue源码：

```java
public class LinkedBlockingQueue<E> extends AbstractQueue<E>
			implements BlockingQueue<E>, java.io.Serializable {
	static class Node<E> {
        E item;
        /**
        * 下列三种情况之一
        * - 真正的后继节点
        * - 自己, 发生在出队时
        * - null, 表示是没有后继节点, 是最后了
        */
        Node<E> next;

        Node(E x) { item = x; }
    }
}
```

入队：

* 初始化链表 `last = head = new Node<E>(null)`，Dummy 节点用来占位，item 为 null
  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-LinkedBlockingQueue初始节点.png" style="zoom:80%;" />
* 当一个节点入队 `last = last.next = node`
  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-LinkedBlockingQueue入队流程.png)

* 再来一个节点入队`last = last.next = node`

出队：出队首节点，先入先出

* 源码：

  ```java
  Node<E> h = head;
  Node<E> first = h.next;
  h.next = h; // help GC
  head = first;
  E x = first.item;// 保存数据
  first.item = null;
  return x;
  ```

* `h = head` -> `first = h.next` 

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-LinkedBlockingQueue出队流程1.png)

* `h.next = h` -> `head = first`

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-LinkedBlockingQueue出队流程2.png)

* `E x = first.item` -> `first.item = null`（**head.item = null**）

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-LinkedBlockingQueue出队流程3.png"  />

  

***



##### 加锁分析

用了两把锁和 dummy 节点：

* 用一把锁，同一时刻，最多只允许有一个线程（生产者或消费者，二选一）执行
* 用两把锁，同一时刻，可以允许两个线程同时（一个生产者与一个消费者）执行
  * 消费者与消费者线程仍然串行
  * 生产者与生产者线程仍然串行

线程安全分析：

* 当节点总数大于 2 时（包括 dummy 节点），putLock 保证的是 last 节点的线程安全，takeLock 保证的是
  head 节点的线程安全，两把锁保证了入队和出队没有竞争

* 当节点总数等于 2 时（即一个 dummy 节点，一个正常节点）这时候，仍然是两把锁锁两个对象，不会竞争

* 当节点总数等于 1 时（就一个 dummy 节点）这时 take 线程会被 notEmpty 条件阻塞，有竞争，会阻塞

  ```java
  // 用于 put(阻塞) offer(非阻塞)
  private final ReentrantLock putLock = new ReentrantLock();
  // 用户 take(阻塞) poll(非阻塞)
  private final ReentrantLock takeLock = new ReentrantLock();
  ```

入队出队：

* put 操作：

  ```java
  public void put(E e) throws InterruptedException {
      if (e == null) throw new NullPointerException();
      int c = -1;
      Node<E> node = new Node<E>(e);
      final ReentrantLock putLock = this.putLock;
      // count 用来维护元素计数
      final AtomicInteger count = this.count;
      putLock.lockInterruptibly();
      try {
      	// 队列满了等待
          while (count.get() == capacity) {
              // 等待不满，就可以生产数据
              notFull.await();
          }
          // 有空位, 入队且计数加一，尾插法
          enqueue(node);
          // 返回自增前的数字
          c = count.getAndIncrement();
          // 除了自己 put 以外, 队列还有空位, 唤醒其他生产put线程
          if (c + 1 < capacity)
              notFull.signal();
      } finally {
          putLock.unlock();
      }
      // 如果还有一个元素，唤醒 take 线程
      if (c == 0)
          signalNotEmpty();
  }
  private void signalNotEmpty() {
      final ReentrantLock takeLock = this.takeLock;
      takeLock.lock();
      try {
          // 调用的是 notEmpty.signal()，而不是 notEmpty.signalAll()，是为了减少竞争
          // 因为只剩下一个元素
          notEmpty.signal();
      } finally {
          takeLock.unlock();
      }
  }
  ```

* take 操作：

  ```java
  public E take() throws InterruptedException {
      E x;
      int c = -1;
      // 元素个数
      final AtomicInteger count = this.count;
      final ReentrantLock takeLock = this.takeLock;
      takeLock.lockInterruptibly();
      try {
          //没有元素可以出队
          while (count.get() == 0) {
              // 等待不空，就可以消费数据
              notEmpty.await();
          }
          // 出队，计数减一，Removes a node from head of queue，FIFO
          x = dequeue();
          // 返回自减前的数子
          c = count.getAndDecrement();
          // 队列还有元素
          if (c > 1)
              // 唤醒其他消费take线程
              notEmpty.signal();
      } finally {
          takeLock.unlock();
      }
      // 如果队列中只有一个空位时, 叫醒 put 线程
  	// 如果有多个线程进行出队, 第一个线程满足 c == capacity, 但后续线程 c < capacity
      if (c == capacity)
          // 调用的是 notFull.signal() 而不是 notFull.signalAll() 是为了减少竞争
          signalNotFull();
      return x;
  }
  ```



***



##### 性能比较

主要列举 LinkedBlockingQueue 与 ArrayBlockingQueue 的性能比较：

* Linked 支持有界，Array 强制有界
* Linked 实现是链表，Array 实现是数组
* Linked 是懒惰的，而 Array 需要提前初始化 Node 数组
* Linked 每次入队会生成新 Node，而 Array 的 Node 是提前创建好的
* Linked 两把锁，Array 一把锁





***



#### 同步队列

与其他BlockingQueue不同，SynchronousQueue是一个不存储元素的BlockingQueue

（待更新）



***



#### 延迟队列

##### 延迟阻塞

DelayQueue 是一个支持延时获取元素的阻塞队列， 内部采用优先队列 PriorityQueue 存储元素，同时元素必须实现 Delayed 接口；在创建元素时可以指定多久才可以从队列中获取当前元素，只有在延迟期满时才能从队列中提取元素

DelayQueue 只能添加（offer/put/add）实现了 Delayed 接口的对象，不能添加 int、String

API：

* `getDelay()`：获取元素在队列中的剩余时间，只有当剩余时间为0时元素才可以出队列。
* `compareTo()`：用于排序，确定元素出队列的顺序

```java
class DelayTask implements Delayed {
    private String name;
    private long time;
    private long start = System.currentTimeMillis();
    // construct set get 

    // 需要实现的接口，获得延迟时间   用过期时间-当前时间
    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert((start + time) - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    // 用于延迟队列内部比较排序   当前时间的延迟时间 - 被比较对象的延迟时间
    @Override
    public int compareTo(Delayed o) {
        DelayTask obj = (DelayTask) o;
        return (int) (this.getDelay(TimeUnit.MILLISECONDS) - o.getDelay(TimeUnit.MILLISECONDS));
    }
}
```



****



##### 优先队列



***



### 操作Pool

#### 创建方法

##### Executor

存放线程的容器：

```java
private final HashSet<Worker> workers = new HashSet<Worker>();
```

构造方法：

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler)
```

参数介绍：

* corePoolSize：核心线程数，定义了最小可以同时运行的线程数量

* maximumPoolSize：最大线程数，当队列中存放的任务达到队列容量时，当前可以同时运行的数量变为最大线程数，创建线程并立即执行最新的任务，与核心线程数之间的差值又叫救急线程数

* keepAliveTime：救急线程最大存活时间，当线程池中的线程数量大于 `corePoolSize` 的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等到`keepAliveTime`时间超过销毁

* unit：`keepAliveTime` 参数的时间单位

* workQueue：阻塞队列，被提交但尚未被执行的任务

* threadFactory：线程工厂，创建新线程时用到，可以为线程创建时起名字

* handler：拒绝策略，线程到达最大线程数仍有新任务时会执行拒绝策略

  RejectedExecutionHandler下有4个实现类：

  * AbortPolicy：让调用者抛出 RejectedExecutionException 异常，默认策略
  * CallerRunsPolicy："调用者运行"的调节机制，将某些任务回退到调用者，从而降低新任务的流量
  * DiscardPolicy：直接丢弃任务，不予任何处理也不抛出异常
  * DiscardOldestPolicy：放弃队列中最早的任务，把当前任务加入队列中尝试再次提交当前任务

  补充：其他框架拒绝策略

  * Dubbo：在抛出 RejectedExecutionException 异常前记录日志，并 dump 线程栈信息，方便定位问题
  * Netty：创建一个新线程来执行任务
  * ActiveMQ：带超时等待（60s）尝试放入队列
  * PinPoint：它使用了一个拒绝策略链，会逐一尝试策略链中每种拒绝策略

工作原理：

![](https://gitee.com/seazean/images/raw/master/Java/JUC-线程池工作原理.png)

1. 创建线程池，这时没有创建线程（**懒惰**），等待提交过来的任务请求，调用execute方法才会创建线程

2. 当调用 execute() 方法添加一个请求任务时，线程池会做如下判断：
   * 如果正在运行的线程数量小于 corePoolSize，那么马上创建线程运行这个任务
   * 如果正在运行的线程数量大于或等于 corePoolSize，那么将这个任务放入队列
   * 如果这时队列满了且正在运行的线程数量还小于 maximumPoolSize，那么会创建非核心线程**立刻运行**这个任务（对于阻塞队列中的任务不公平）
   * 如果队列满了且正在运行的线程数量大于或等于 maximumPoolSize，那么线程池会启动饱和**拒绝策略**来执行
3. 当一个线程完成任务时，会从队列中取下一个任务来执行

4. 当一个线程无事可做超过一定的时间（keepAliveTime）时，线程池会判断：如果当前运行的线程数大于corePoolSize，那么这个线程就被停掉，所以线程池的所有任务完成后最终会收缩到 corePoolSize 大小





***



##### Executors

Executors提供了四种线程池的创建：newCachedThreadPool、newFixedThreadPool、newSingleThreadExecutor、newScheduledThreadPool

* newFixedThreadPool：创建一个拥有 n 个线程的线程池

  ```java
  public static ExecutorService newFixedThreadPool(int nThreads) {
      return new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>());
  }
  ```

  * 核心线程数 == 最大线程数（没有救急线程被创建），因此也无需超时时间
  * LinkedBlockingQueue是一个单向链表实现的阻塞队列，默认大小为`Integer.MAX_VALUE`，也就是无界队列，可以放任意数量的任务，在任务比较多的时候会导致 OOM（内存溢出）
  * 适用于任务量已知，相对耗时的长期任务

* newCachedThreadPool：创建一个可扩容的线程池

  ```java
  public static ExecutorService newCachedThreadPool() {
      return new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60L, TimeUnit.SECONDS,
                                    new SynchronousQueue<Runnable>());
  }
  ```

  * 核心线程数是 0， 最大线程数是 Integer.MAX_VALUE，全部都是救急线程（60s 后可以回收），可能会创建大量线程，从而导致 **OOM**
  * SynchronousQueue 作为阻塞队列，没有容量，对于每一个take的线程会阻塞直到有一个put的线程放入元素为止（类似一手交钱、一手交货）

  * 适合任务数比较密集，但每个任务执行时间较短的情况

* newSingleThreadExecutor：创建一个只有1个线程的单线程池

  ```java
  public static ExecutorService newSingleThreadExecutor() {
      return new FinalizableDelegatedExecutorService
          (new ThreadPoolExecutor(1, 1,0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>()));
  }
  ```

  * 保证所有任务按照**指定顺序执行**，线程数固定为 1，任务数多于 1 时会放入无界队列排队，任务执行完毕，这唯一的线程也不会被释放

  对比：

  * 创建一个单线程串行执行任务，如果任务执行失败而终止那么没有任何补救措施，而线程池还会新建一
    个线程，保证池的正常工作

  * Executors.newSingleThreadExecutor() 线程个数始终为1，不能修改。F...D..ExecutorService 应用的是装饰器模式，只对外暴露了 ExecutorService 接口，因此不能调用 ThreadPoolExecutor 中特有的方法

    原因：父类不能直接调用子类中的方法，需要反射或者创建对象的方式，可以调用子类静态方法

  * Executors.newFixedThreadPool(1) 初始时为1，可以修改。对外暴露的是 ThreadPoolExecutor 对象，可以强转后调用 setCorePoolSize 等方法进行修改

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-newSingleThreadExecutor.png)

***



#### 开发要求

阿里巴巴 Java 开发手册要求：

- **线程资源必须通过线程池提供，不允许在应用中自行显式创建线程**

  - 使用线程池的好处是减少在创建和销毁线程上所消耗的时间以及系统资源的开销，解决资源不足的问题
  - 如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题

- 线程池不允许使用Executors去创建，而是通过 ThreadPoolExecutor 的方式，这样的处理方式更加明确线程池的运行规则，规避资源耗尽的风险

  Executors返回的线程池对象弊端如下：

  - FixedThreadPool 和 SingleThreadPool：
    - 运行的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM
  - CacheThreadPool 和 ScheduledThreadPool：
    - 允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM

创建多大容量的线程池合适？

* 一般来说池中**总线程数是核心池线程数量两倍**，确保当核心池有线程停止时，核心池外有线程进入核心池

* 过小会导致程序不能充分地利用系统资源、容易导致饥饿

* 过大会导致更多的线程上下文切换，占用更多内存
  上下文切换：当前任务在执行完 CPU 时间片切换到另一个任务之前会先保存自己的状态，以便下次再切换回这个任务时，可以再加载这个任务的状态，任务从保存到再加载的过程就是一次上下文切换

核心线程数常用公式：

- **CPU 密集型任务(N+1)：** 这种任务消耗的是 CPU 资源，可以将核心线程数设置为 N (CPU 核心数)+1，比 CPU 核心数多出来的一个线程是为了防止线程发生缺页中断，或者其它原因导致的任务暂停而带来的影响。一旦任务暂停，CPU 就会处于空闲状态，而在这种情况下多出来的一个线程就可以充分利用 CPU 的空闲时间

  CPU 密集型简单理解就是利用 CPU 计算能力的任务比如你在内存中对大量数据进行分析

- **I/O 密集型任务：** 这种系统 CPU 处于阻塞状态，用大部分的时间来处理 I/O 交互，而线程在处理 I/O 的时间段内不会占用 CPU 来处理，这时就可以将 CPU 交出给其它线程使用，因此在 I/O 密集型任务的应用中，我们可以多配置一些线程，具体的计算方法是 2N 或 CPU核数/ (1-阻塞系数)，阻塞系数在0.8~0.9之间

  IO 密集型就是涉及到网络读取，文件读取此类任务 ，特点是 CPU 计算耗费时间相比于等待 IO 操作完成的时间来说很少，大部分时间都花在了等待 IO 操作完成上





***



#### 提交方法

ExecutorService类API：

| 方法                                                         | 说明                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| void execute(Runnable command)                               | 执行任务（Executor类API）                                    |
| Future<?> submit(Runnable task)                              | 提交任务 task()                                              |
| <T> Future<T> submit(Callable<T> task)                       | 提交任务 task，用返回值Future获得任务执行结果                |
| <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks) | 提交 tasks 中所有任务                                        |
| <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks, long timeout, TimeUnit unit) | 提交 tasks 中所有任务，超时时间针对所有task，超时会取消没有执行完的任务，并抛出超时异常 |
| <T> T invokeAny(Collection<? extends Callable<T>> tasks)     | 提交 tasks 中所有任务，哪个任务先成功执行完毕，返回此任务执行结果，其它任务取消 |

execute和submit都属于线程池的方法，对比：

* execute只能提交Runnable类型的任务，而submit既能提交Runnable类型任务也能提交Callable类型任务

* execute会直接抛出任务执行时的异常，submit会吞掉异常，可通过Future的get方法将任务执行时的异常重新抛出



***



#### 关闭方法

ExecutorService类API：

| 方法                                                  | 说明                                                         |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| void shutdown()                                       | 线程池状态变为 SHUTDOWN，等待任务执行完后关闭线程池，不会接收新任务，但已提交任务会执行完 |
| List<Runnable> shutdownNow()                          | 线程池状态变为 STOP，用 interrupt 中断正在执行的任务，直接关闭线程池，不会接收新任务，会将队列中的任务返回， |
| boolean isShutdown()                                  | 不在 RUNNING 状态的线程池，此执行者已被关闭，方法返回 true   |
| boolean isTerminated()                                | 线程池状态是否是 TERMINATED，如果所有任务在关闭后完成，返回true |
| boolean awaitTermination(long timeout, TimeUnit unit) | 调用 shutdown 后，由于调用线程不会等待所有任务运行结束，如果它想在线程池 TERMINATED 后做些事情，可以利用此方法等待 |



***



#### 处理异常

execute会直接抛出任务执行时的异常，submit会吞掉异常，有两种处理方法

方法1：主动捉异常

```java
ExecutorService executorService = Executors.newFixedThreadPool(1);
pool.submit(() -> {
    try {
        System.out.println("task1");
        int i = 1 / 0;
    } catch (Exception e) {
        e.printStackTrace();
    }
});
```

方法2：使用 Future

```java
ExecutorService executorService = Executors.newFixedThreadPool(1);
Future<?> future = pool.submit(() -> {
    System.out.println("task1");
    int i = 1 / 0;
    return true;
});
System.out.println(future.get());
```



***



#### 状态信息

ThreadPoolExecutor 使用 int 的高 3 位来表示线程池状态，低 29 位表示线程数量

| 状态       | 高3位 | 接收新任务 | 处理阻塞任务队列 | 说明                                    |
| ---------- | ----- | ---------- | ---------------- | --------------------------------------- |
| RUNNING    | 111   | Y          | Y                |                                         |
| SHUTDOWN   | 000   | N          | Y                | 不接收新任务，但处理阻塞队列剩余任务    |
| STOP       | 001   | N          | N                | 中断正在执行的任务，并抛弃阻塞队列任务  |
| TIDYING    | 010   | -          | -                | 任务全执行完毕，活动线程为0即将进入终结 |
| TERMINATED | 011   | -          | -                | 终止状态                                |

这些信息存储在一个原子变量 ctl 中，目的是将线程池状态与线程个数合二为一，这样就可以用一次 cas 原子操作
进行赋值

```java
// c为旧值， ctlOf返回结果为新值
ctl.compareAndSet(c, ctlOf(targetState, workerCountOf(c))));
// rs为高3位代表线程池状态， wc为低29位代表线程个数，ctl是合并它们
private static int ctlOf(int rs, int wc) { return rs | wc; }
```

![](https://gitee.com/seazean/images/raw/master/Java/JUC-线程池状态转换图.png)





***



### 任务调度

#### Timer

Timer 实现定时功能，Timer 的优点在于简单易用，但由于所有任务都是由同一个线程来调度，因此所有任务都是串行执行的，同一时间只能有一个任务在执行，前一个任务的延迟或异常都将会影响到之后的任务

```java
private static void method1() {
    Timer timer = new Timer();
    TimerTask task1 = new TimerTask() {
        @Override
        public void run() {
            System.out.println("task 1");
            //int i = 1 / 0;//任务一的出错会导致任务二无法执行
            Thread.sleep(2000);
        }
    };
    TimerTask task2 = new TimerTask() {
        @Override
        public void run() {
            System.out.println("task 2");
        }
    };
    // 使用 timer 添加两个任务，希望它们都在 1s 后执行
	// 但由于 timer 内只有一个线程来顺序执行队列中的任务，因此任务1的延时，影响了任务2的执行
    timer.schedule(task1,1000);//17:45:56 c.ThreadPool [Timer-0] - task 1
    timer.schedule(task2,1000);//17:45:58 c.ThreadPool [Timer-0] - task 2
}
```



***



#### Scheduled

任务调度线程池 ScheduledThreadPoolExecutor 继承 ThreadPoolExecutor：

构造方法：`Executors.newScheduledThreadPool(int corePoolSize)`

```java
public ScheduledThreadPoolExecutor(int corePoolSize) {
    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
          new DelayedWorkQueue());
}
```

常用API：

* `ScheduledFuture<?> schedule(Runnable/Callable<V>, long delay, TimeUnit u)`：延迟执行任务
* `ScheduledFuture<?> scheduleAtFixedRate(Runnable/Callable<V>, long initialDelay, long period, TimeUnit unit)`：定时执行任务，参数为初始延迟时间、间隔时间、单位
* `ScheduledFuture<?> scheduleWithFixedDelay(Runnable/Callable<V>, long initialDelay, long delay, TimeUnit unit)`：定时执行任务，参数为初始延迟时间、间隔时间、单位

基本使用：

* 延迟任务，但是出现异常并不会在控制台打印，也不会影响其他线程的执行

  ```java
  public static void main(String[] args){
      // 线程池大小为1时也是串行执行
      ScheduledExecutorService executor = Executors.newScheduledThreadPool(2);
      // 添加两个任务，都在 1s 后同时执行
      executor.schedule(() -> {
      	System.out.println("任务1，执行时间：" + new Date());
          //int i = 1 / 0;
      	try { Thread.sleep(2000); } catch (InterruptedException e) { }
      }, 1000, TimeUnit.MILLISECONDS);
      
      executor.schedule(() -> {
      	System.out.println("任务2，执行时间：" + new Date());
      }, 1000, TimeUnit.MILLISECONDS);
  }
  ```

* 定时任务 scheduleAtFixedRate：**一个任务的启动到下一个任务的启动**之间只要大于间隔时间，抢占到CPU就会立即执行

  ```java
  public static void main(String[] args) {
      ScheduledExecutorService pool = Executors.newScheduledThreadPool(1);
      System.out.println("start..." + new Date());
      
      pool.scheduleAtFixedRate(() -> {
          System.out.println("running..." + new Date());
          Thread.sleep(2000);
      }, 1, 1, TimeUnit.SECONDS);
  }
  
  /*start...Sat Apr 24 18:08:12 CST 2021
  running...Sat Apr 24 18:08:13 CST 2021
  running...Sat Apr 24 18:08:15 CST 2021
  running...Sat Apr 24 18:08:17 CST 2021
  ```

* 定时任务 scheduleWithFixedDelay：**一个任务的结束到下一个任务的启动之间**等于间隔时间，抢占到CPU就会立即执行，这个方法才是真正的设置两个任务之间的间隔

  ```java
  public static void main(String[] args){
      ScheduledExecutorService pool = Executors.newScheduledThreadPool(3);
      System.out.println("start..." + new Date());
      
      pool.scheduleWithFixedDelay(() -> {
          System.out.println("running..." + new Date());
          Thread.sleep(2000);
      }, 1, 1, TimeUnit.SECONDS);
  }
  /*start...Sat Apr 24 18:11:41 CST 2021
  running...Sat Apr 24 18:11:42 CST 2021
  running...Sat Apr 24 18:11:45 CST 2021
  running...Sat Apr 24 18:11:48 CST 2021
  ```



***



#### 定时任务

让每周四 18:00:00 定时执行任务

```java
public class ThreadPoolDemo04 {
    //每周四 18:00:00 执行定时任务
    public static void main(String[] args) {
        LocalDateTime now = LocalDateTime.now();
        LocalDateTime time = now.withHour(18).withMinute(0).withSecond(0).withNano(0).with(DayOfWeek.THURSDAY);

        //如果当前时间 > 本周周四 ，必须找下周周四
        if (now.compareTo(time) > 0) {
            time = time.plusWeeks(1);
        }

        // initialDelay 当前时间和周四的时间差
        // period 每周的间隔
        Duration between = Duration.between(now, time);
        long initialDelay = between.toMillis();
        long period = 1000 * 60 * 60 * 24 * 7;
        ScheduledExecutorService pool = Executors.newScheduledThreadPool(1);
        pool.scheduleAtFixedRate(() -> {
            System.out.println("running...");
        },initialDelay,period, TimeUnit.MILLISECONDS);
    }
}
```



***



### ForkJoin

Fork/Join：线程池的实现，体现是分治思想，适用于能够进行任务拆分的 cpu 密集型运算，用于**并行计算**

任务拆分：是将一个大任务拆分为算法上相同的小任务，直至不能拆分可以直接求解。跟递归相关的一些计算，如归并排序、斐波那契数列都可以用分治思想进行求解

* Fork/Join 在分治的基础上加入了多线程，把每个任务的分解和合并交给不同的线程来完成，提升了运算效率

* ForkJoin 使用 ForkJoinPool 来启动，是一个特殊的线程池，默认会创建与 cpu 核心数大小相同的线程池
* 任务有返回值继承 RecursiveTask，没有返回值继承 RecursiveAction

```java
public static void main(String[] args) {
    ForkJoinPool pool = new ForkJoinPool(4);
    System.out.println(pool.invoke(new MyTask(5)));
    //拆分  5 + MyTask(4) --> 4 + MyTask(3) -->
}

// 1~ n 之间整数的和
class MyTask extends RecursiveTask<Integer> {
    private int n;

    public MyTask(int n) {
        this.n = n;
    }

    @Override
    public String toString() {
        return "MyTask{" + "n=" + n + '}';
    }

    @Override
    protected Integer compute() {
        // 如果 n 已经为 1，可以求得结果了
        if (n == 1) {
            return n;
        }
        // 将任务进行拆分(fork)
        MyTask t1 = new MyTask(n - 1);
        t1.fork();
        // 合并(join)结果
        int result = n + t1.join();
        return result;
    }
}
```

继续拆分优化：

```java
class AddTask extends RecursiveTask<Integer> {
    int begin;
    int end;
    public AddTask(int begin, int end) {
        this.begin = begin;
        this.end = end;
    }
    
    @Override
    public String toString() {
        return "{" + begin + "," + end + '}';
    }
    
    @Override
    protected Integer compute() {
        // 5, 5
        if (begin == end) {
            return begin;
        }
        // 4, 5  防止多余的拆分  提高效率
        if (end - begin == 1) {
            return end + begin;
        }
        // 1 5
        int mid = (end + begin) / 2; // 3
        AddTask t1 = new AddTask(begin, mid); // 1,3
        t1.fork();
        AddTask t2 = new AddTask(mid + 1, end); // 4,5
        t2.fork();
        int result = t1.join() + t2.join();
        return result;
    }
}
```

ForkJoinPool 实现了**工作窃取算法**来提高 CPU 的利用率：

* 每个线程都维护了一个双端队列，用来存储需要执行的任务
* 工作窃取算法允许空闲的线程从其它线程的双端队列中窃取一个任务来执行
* 窃取的必须是**最晚的任务**，避免和队列所属线程发生竞争，但是队列中只有一个任务时还是会发生竞争



***



### 享元模式

享元模式 (Flyweight pattern)： 用于减少创建对象的数量，以减少内存占用和提高性能，这种类型的设计模式属于结构型模式，它提供了减少对象数量从而改善应用所需的对象结构的方式

异步模式：让有限的工作线程（Worker Thread）来轮流异步处理无限多的任务，也可将其归类为分工模式，典型实现就是线程池

工作机制：享元模式尝试重用现有的同类对象，如果未找到匹配的对象，则创建新对象

自定义连接池：

```java
public static void main(String[] args) {
    Pool pool = new Pool(2);
    for (int i = 0; i < 5; i++) {
        new Thread(() -> {
            Connection con = pool.borrow();
            try {
                Thread.sleep(new Random().nextInt(1000));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            pool.free(con);
        }).start();
    }
}
class Pool {
    //连接池的大小
    private final int poolSize;
    //连接对象的数组
    private Connection[] connections;
    //连接状态数组 0表示空闲  1表示繁忙
    private AtomicIntegerArray states;  //int[] -> AtomicIntegerArray

    //构造方法
    public Pool(int poolSize) {
        this.poolSize = poolSize;
        this.connections = new Connection[poolSize];
        this.states = new AtomicIntegerArray(new int[poolSize]);
        for (int i = 0; i < poolSize; i++) {
            connections[i] = new MockConnection("连接" + (i + 1));
        }
    }

    //使用连接
    public Connection borrow() {
        while (true) {
            for (int i = 0; i < poolSize; i++) {
                if (states.get(i) == 0) {
                    if (states.compareAndSet(i, 0, 1)) {
                        System.out.println(Thread.currentThread().getName() + " borrow " +  connections[i]);
                        return connections[i];
                    }
                }
            }
            //如果没有空闲连接，当前线程等待
            synchronized (this) {
                try {
                    System.out.println(Thread.currentThread().getName() + " wait...");
                    this.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    //归还连接
    public void free(Connection con) {
        for (int i = 0; i < poolSize; i++) {
            if (connections[i] == con) {//判断是否是同一个对象
                states.set(i, 0);//不用cas的原因是只会有一个线程使用该连接
                synchronized (this) {
                    System.out.println(Thread.currentThread().getName() + " free " + con);
                    this.notifyAll();
                }
                break;
            }
        }
    }

}

class MockConnection implements Connection {
    private String name;
    //.....
}
```





****





## 同步器

### AQS

#### 思想

AQS：AbstractQueuedSynchronizer，是阻塞式锁和相关的同步器工具的框架，许多同步类实现都依赖于它

* 用 state 属性来表示资源的状态（分**独占模式和共享模式**），子类需要定义如何维护这个状态，控制如何获取
  锁和释放锁
  * 独占模式是只有一个线程能够访问资源，如 ReentrantLock
  * 共享模式允许多个线程访问资源，如 Semaphore，ReentrantReadWriteLock 是组合式
* 提供了基于 FIFO 的等待队列，类似于 Monitor 的 EntryList（**同步队列：双向，便于出队入队**）
* 条件变量来实现等待、唤醒机制，支持多个条件变量，类似于 Monitor 的 WaitSet（**条件队列：单向**）

AQS 核心思想：

* 如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并将共享资源设置锁定状态

* 被请求的共享资源被占用，AQS 用 CLH 队列实现线程阻塞等待以及被唤醒时锁分配的（park）机制，即将暂时获取不到锁的线程加入到队列中

  CLH 是一种基于单向链表的**高性能、公平的自旋锁**，AQS 是将每条请求共享资源的线程封装成一个 CLH 锁队列的一个结点（Node）来实现锁的分配

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-AQS原理图.png" style="zoom: 80%;" />



***



#### 原理

设计思想：

* 获取锁：

  ```java
  while(state 状态不允许获取) {//tryAcquire(arg)
      if(队列中还没有此线程) {
          入队并阻塞 park unpark
      }
  }
  当前线程出队
  ```

* 释放锁：

  ```java
  if(state 状态允许了) {//tryRelease(arg)
  	恢复阻塞的线程(s)
  }
  ```

AQS 中 state 设计：

* state 使用了 32bit int 来维护同步状态
* state **使用 volatile 修饰配合 cas** 保证其修改时的原子性
* state 表示**线程重入的次数或者许可进入的线程数**
* state API：
  `protected final int getState()`：获取 state 状态
  `protected final void setState(int newState)`：设置 state 状态
  `protected final boolean compareAndSetState(int expect,int update)`：**cas** 设置 state

Node 节点中 waitstate 设计：

* 使用 **volatile 修饰配合 CAS** 保证其修改时的原子性

* 表示 Node 节点的状态，有以下几种状态：

  ```java
  //由于超时或中断，此节点被取消，不会再改变状态
  static final int CANCELLED =  1;
  //此节点后面的节点已（或即将）被阻止（通过park），当前节点在释放或取消时必须唤醒后面的节点
  static final int SIGNAL    = -1;
  //此节点当前在条件队列中
  static final int CONDITION = -2;
  //将releaseShared传播到其他节点
  static final int PROPAGATE = -3;
  ```

阻塞恢复设计：

* 使用 park & unpark 来实现线程的暂停和恢复，因为命令的先后顺序不影响结果
* park & unpark 是针对线程的，而不是针对同步器的，因此控制粒度更为精细
* park 线程可以通过 interrupt 打断

队列设计：

* 使用了 FIFO 先入先出队列，并不支持优先级队列

* 设计时借鉴了 CLH 队列，CLH是一种单向无锁队列

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-AQS队列设计.png" style="zoom: 80%;" />

  ```java
  // node 放入 AQS 队列尾部，返回尾节点的前驱节点
  private Node enq(final Node node) {
      for (;;) {
          Node t = tail;
          // 队列中还没有元素 tail 为 null
          if (t == null) { 
              // 设置 head 为哨兵节点（不对应线程，状态为 0）
              if (compareAndSetHead(new Node()))
                  tail = head;
          } else {
              // 将 node 的 prev 设置为原来的 tail  双向队列
              node.prev = t;
              // 将 tail 从原来的 tail 设置为 node
              if (compareAndSetTail(t, node)) {
                  //将原来的尾节点（哑元节点）的 next 指向新节点
                  t.next = node;
                  return t;
              }
          }
      }
  }
  ```
  
  

***



#### 模板

同步器的设计是基于模板方法模式，该模式是基于”继承“的，主要是为了在不改变模板结构的前提下在子类中重新定义模板中的内容以实现复用代码

* 使用者继承 `AbstractQueuedSynchronizer` 并重写指定的方法
* 将 AQS 组合在自定义同步组件的实现中，并调用其模板方法，这些模板方法会调用使用者重写的方法

AQS 使用了模板方法模式，自定义同步器时需要重写下面几个 AQS 提供的模板方法：

```java
isHeldExclusively()//该线程是否正在独占资源。只有用到condition才需要去实现它。
tryAcquire(int)//独占方式。尝试获取资源，成功则返回true，失败则返回false。
tryRelease(int)//独占方式。尝试释放资源，成功则返回true，失败则返回false。
tryAcquireShared(int)//共享方式。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
tryReleaseShared(int)//共享方式。尝试释放资源，成功则返回true，失败则返回false。
```

* 默认情况下，每个方法都抛出 `UnsupportedOperationException`
* 这些方法的实现必须是内部线程安全的
* AQS 类中的其他方法都是 final ，所以无法被其他类使用，只有这几个方法可以被其他类使用



***



#### 自定义

自定义一个不可重入锁：

```java
class MyLock implements Lock {
    //独占锁 不可重入
    class MySync extends AbstractQueuedSynchronizer {
        @Override
        protected boolean tryAcquire(int arg) {
            if (compareAndSetState(0, 1)) {
                // 加上锁 设置 owner 为当前线程
                setExclusiveOwnerThread(Thread.currentThread());
                return true;
            }
            return false;
        }
        @Override   //解锁
        protected boolean tryRelease(int arg) {
            setExclusiveOwnerThread(null);
            setState(0);//volatile 修饰的变量放在后面，防止指令重排
            return true;
        }
        @Override   //是否持有独占锁
        protected boolean isHeldExclusively() {
            return getState() == 1;
        }
        public Condition newCondition() {
            return new ConditionObject();
        }
    }

    private MySync sync = new MySync();

    @Override   //加锁（不成功进入等待队列等待）
    public void lock() {
        sync.acquire(1);
    }

    @Override   //加锁 可打断
    public void lockInterruptibly() throws InterruptedException {
        sync.acquireInterruptibly(1);
    }

    @Override   //尝试加锁，尝试一次
    public boolean tryLock() {
        return sync.tryAcquire(1);
    }

    @Override   //尝试加锁，带超时
    public boolean tryLock(long time, TimeUnit unit) throws InterruptedException {
        return sync.tryAcquireNanos(1, unit.toNanos(time));
    }
    
    @Override   //解锁
    public void unlock() {
        sync.release(1);
    }
    
    @Override   //条件变量
    public Condition newCondition() {
        return sync.newCondition();
    }
}
```





***



### Re-Lock

#### 锁对比

ReentrantLock 相对于 synchronized 它具备如下特点：

1. 锁的实现：synchronized 是 JVM 实现的，而 ReentrantLock 是 JDK 实现的
2. 性能：新版本 Java 对 synchronized 进行了很多优化，synchronized 与 ReentrantLock 大致相同
3. 使用：ReentrantLock 需要手动解锁，synchronized 执行完代码块自动解锁
4. 可中断：ReentrantLock 可中断，而 synchronized 不行
5. **公平锁**：公平锁是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁
   * ReentrantLock 可以设置公平锁，synchronized 中的锁是非公平的
6. 锁超时：尝试获取锁，超时获取不到直接放弃，不进入阻塞队列
   * ReentrantLock 可以设置超时时间，synchronized 会一直等待
7. 锁绑定多个条件：一个 ReentrantLock 可以同时绑定多个 Condition 对象 
8. 两者都是可重入锁



***



#### 使用锁

构造方法：`ReentrantLock lock = new ReentrantLock();`

ReentrantLock类API：

* `public void lock()`：获得锁
  * 如果锁没有被另一个线程占用，则将锁定计数设置为1。

  * 如果当前线程已经保持锁定，则保持计数增加1 

  * 如果锁被另一个线程保持，则当前线程被禁用线程调度，并且在锁定已被获取之前处于休眠状态

* `public void unlock()`：尝试释放锁
  * 如果当前线程是该锁的持有者，则保持计数递减
  * 如果保持计数现在为零，则锁定被释放
  * 如果当前线程不是该锁的持有者，则抛出异常

基本语法：

```java
// 获取锁
reentrantLock.lock();
try {
    // 临界区
} finally {
	// 释放锁
	reentrantLock.unlock();
}
```



***



#### 公平锁

##### 基本使用

构造方法：`ReentrantLock lock = new ReentrantLock(true)`

```java
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

ReentrantLock 默认是不公平的：

```java
public ReentrantLock() {
    sync = new NonfairSync();
}
```

说明：公平锁一般没有必要，会降低并发度



***



##### 非公原理

###### 加锁

NonfairSync 继承自 AQS

没有竞争：ExclusiveOwnerThread属于 Thread-0，state 设置为1

```java
final void lock() {
    //首先用 cas 尝试（仅尝试一次）将 state 从 0 改为 1, 如果成功表示获得了独占锁
    if (compareAndSetState(0, 1))
        setExclusiveOwnerThread(Thread.currentThread());
    else
        acquire(1);//失败进入
}
```

第一个竞争出现：

```java
public final void acquire(int arg) {    
    // 当 tryAcquire 返回为 false 时, 先调用addWaiter, 接着 acquireQueued    
    if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg))        			selfInterrupt();
}
```

<img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-非公平锁1.png" style="zoom:80%;" />

Thread-1执行：

* CAS 尝试将 state 由 0 改为 1，结果失败（第一次）

* 进入 tryAcquire 逻辑，这时 state 已经是1，结果仍然失败（第二次）

  ```java
  protected final boolean tryAcquire(int acquires) {    
      return nonfairTryAcquire(acquires);
  }
  final boolean nonfairTryAcquire(int acquires) {    
      final Thread current = Thread.currentThread();    
      int c = getState();    
      if (c == 0) {        
          //如果还没有获得锁，尝试用cas获得，这里体现非公平性: 不去检查 AQS 队列        
      	if (compareAndSetState(0, acquires)) {            
              setExclusiveOwnerThread(current);            
              return true;        
           }    
  	}    
     	// 如果已经获得了锁, 线程还是当前线程, 表示发生了锁重入    
  	else if (current == getExclusiveOwnerThread()) {        
          int nextc = c + acquires;        
          if (nextc < 0) // overflow            
              throw new Error("Maximum lock count exceeded");        
          setState(nextc);        
          return true;    
      }    
      return false;//获取失败
  }
  ```

* 接下来进入 addWaiter 逻辑，构造 Node 队列

  * 图中黄色三角表示该 Node 的 waitStatus 状态，其中 0 为默认**正常状态**
  * Node 的创建是懒惰的
  * 其中第一个 Node 称为 **Dummy（哑元）或哨兵**，用来占位，并不关联线程

  ```java
  private Node addWaiter(Node mode) {    
      // 将当前线程关联到一个 Node 对象上, 模式为独占模式   
      // Node.EXCLUSIVE for exclusive, Node.SHARED for shared
      Node node = new Node(Thread.currentThread(), mode);    
      Node pred = tail;    
      // 如果 tail 不为 null, cas 尝试将 Node 对象加入 AQS 队列尾部    
      if (pred != null) {        
          node.prev = pred;        
          if (compareAndSetTail(pred, node)) {            
              pred.next = node;// 双向链表            
              return node;        
          }    
      }    
      enq(node);//添加到尾节点    
      return node;
  }
  ```

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-非公平锁2.png" style="zoom:80%;" />

* 线程进入 acquireQueued 逻辑

  * acquireQueued 会在一个死循环中不断尝试获得锁，失败后进入 park 阻塞

  * 如果当前线程是在 head 节点后，会再次 tryAcquire 尝试获取锁，state 仍为 1 则失败（第三次）

  ```java
  final boolean acquireQueued(final Node node, int arg) {
      boolean failed = true;
      try {
          boolean interrupted = false;
          for (;;) {
              final Node p = node.predecessor();
              // 上一个节点是 head, 表示轮到自己获取锁
              if (p == head && tryAcquire(arg)) {
                  // 获取成功, 设置自己（当前线程对应的 node）为 head
                  setHead(node);
                  p.next = null; // help GC
                  failed = false;
                  return interrupted;
              }
              // 判断是否应当 park，返回false后需要新一轮的循环
              if (shouldParkAfterFailedAcquire(p, node) &&
                  parkAndCheckInterrupt())
                  interrupted = true;
          }
      } finally {
          if (failed)
              cancelAcquire(node);
      }
  }
  ```

  * 进入 shouldParkAfterFailedAcquire 逻辑，将前驱 node，即 head 的 waitStatus 改为 -1，返回 false；waitStatus 为 -1 的节点用来唤醒下一个节点

  ```java
  private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
      int ws = pred.waitStatus;
      if (ws == Node.SIGNAL)
          // 上一个节点都在阻塞, 那么当前线程也阻塞
          return true;
      if (ws > 0) {
          // 上一个节点取消, 那么重构删除前面所有取消的节点, 返回到外层循环重试
          do {
              node.prev = pred = pred.prev;
          } while (pred.waitStatus > 0);
          pred.next = node;
      } else {
          // 设置上一个节点状态为 Node.SIGNAL，返回外层循环重试
          compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
      }
      return false;
  }
  ```

  * shouldParkAfterFailedAcquire 执行完毕回到 acquireQueued ，再次 tryAcquire 尝试获取锁，这时state 仍为 1 获取失败（第四次）
  * 当再次进入 shouldParkAfterFailedAcquire 时，这时其前驱 node 的 waitStatus 已经是 -1，返回 true
  * 进入 parkAndCheckInterrupt， Thread-1 park（灰色表示），再有多个线程经历竞争失败后：

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-非公平锁3.png)

  ```java
  private final boolean parkAndCheckInterrupt() {
      // 阻塞当前线程，如果打断标记已经是 true, 则 park 会失效
      LockSupport.park(this);
      // 判断当前线程是否被打断，清除打断标记
      return Thread.interrupted();
  }
  ```

  



###### 解锁

```java
public void unlock() {
    sync.release(1);
}
```

Thread-0 释放锁，进入 release 流程

* 进入 tryRelease

  * 设置 exclusiveOwnerThread 为 null
  * state = 0

* 当前队列不为 null，并且 head 的 waitStatus = -1，进入 unparkSuccessor 

  ```java
  public final boolean release(int arg) {    
      if (tryRelease(arg)) {       
          // 队列头节点 unpark        
          Node h = head;        
          if (h != null && h.waitStatus != 0)            
              unparkSuccessor(h);        
          return true;    
      }    
      return false;
  }
  ```

  ```java
  protected final boolean tryRelease(int releases) {    
      int c = getState() - releases;    
      if (Thread.currentThread() != getExclusiveOwnerThread())        
          throw new IllegalMonitorStateException();    
      boolean free = false;    
      // 支持锁重入, 只有 state 减为 0, 才释放成功    
      if (c == 0) {        
          free = true;        
          setExclusiveOwnerThread(null);    
      }    
      setState(c);    
      return free;
  }
  ```

* 进入 unparkSuccessor 方法

  * 找到队列中离 head 最近的一个 Node（没取消的），unpark 恢复其运行，本例中即为 Thread-1
  * 回到 Thread-1 的 acquireQueued 流程

  ```java
  private void unparkSuccessor(Node node) {    
      int ws = node.waitStatus;    
      if (ws < 0)        
          // 尝试重置状态为 0        
          compareAndSetWaitStatus(node, ws, 0);    
      // 找到需要 unpark 的节点，头节点的下一个    
      Node s = node.next;    
      // 不考虑已取消的节点    
      if (s == null || s.waitStatus > 0) {        
          s = null;        
          // 从 AQS 队列从后至前找到队列需要 unpark 的节点        
          for (Node t = tail; t != null && t != node; t = t.prev)            
              if (t.waitStatus <= 0)                
                  s = t;    
      }    
      if (s != null)        
          LockSupport.unpark(s.thread);
  }
  ```

* 如果加锁成功（没有竞争），会设置

  * exclusiveOwnerThread 为 Thread-1，state = 1
  * head 指向刚刚 Thread-1 所在的 Node，该 Node 清空 Thread
  * 原本的 head 因为从链表断开，而可被垃圾回收

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-非公平锁4.png)

* 如果这时候有其它线程来竞争（非公平），例如这时有 Thread-4 来了并抢占了锁

  * Thread-4 被设置为 exclusiveOwnerThread，state = 1
  * Thread-1 再次进入 acquireQueued 流程，获取锁失败，重新进入 park 阻塞

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-非公平锁5.png)



***



##### 公平原理

与非公平锁主要区别在于 tryAcquire 方法：先检查 AQS 队列中是否有前驱节点，没有才去 CAS 竞争

```java
static final class FairSync extends Sync {
    private static final long serialVersionUID = -3000897897090466540L;
    final void lock() {
        acquire(1);
    }

    protected final boolean tryAcquire(int acquires) {
        final Thread current = Thread.currentThread();
        int c = getState();
        if (c == 0) {
            // 先检查 AQS 队列中是否有前驱节点, 没有(false)才去竞争
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) {
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        // 锁重入
        return false;
    }
}
```

```java
public final boolean hasQueuedPredecessors() {    
    Node t = tail;     
    Node h = head;    
    Node s;    
    //头尾指向一个节点，列表为空，返回false，    
    return h != t &&        
        // 头尾之间有节点，判断头节点的下一个是不是空        
        // 不是空进入最后的判断，第二个节点的线程是否是本线程        
        ((s = h.next) == null || s.thread != Thread.currentThread());}
```



***



#### 可重入

可重入是指同一个线程如果首次获得了这把锁，那么它是这把锁的拥有者，因此有权利再次获取这把锁，如果不可重入锁，那么第二次获得锁时，自己也会被锁挡住

源码解析参考：`nonfairTryAcquire(int acquires)) `和 `tryRelease(int releases)`

```java
static ReentrantLock lock = new ReentrantLock();
public static void main(String[] args) {	
    method1();
}
public static void method1() {    
    lock.lock();    
    try {        
        System.out.println(Thread.currentThread().getName() + " execute method1");
        method2();    
    } finally {    	
        lock.unlock();    
    }
}
public static void method2() {    
    lock.lock();    
    try {        
        System.out.println(Thread.currentThread().getName() + " execute method2");
    } finally {    	
        lock.unlock();    
    }
}
```

面试题：在 Lock 方法加两把锁会是什么情况呢？

* 加锁两次解锁两次：正常执行
* 加锁两次解锁一次：程序直接卡死，线程不能出来，也就说明**申请几把锁，最后需要解除几把锁**
* 加锁一次解锁两次：运行程序会直接报错

```java
public void getLock() {    
    lock.lock();    
    lock.lock();    
    try {        
        System.out.println(Thread.currentThread().getName() + "\t get Lock");
    } finally {
        lock.unlock();        
        //lock.unlock();    
    }
}
```



****



#### 可打断

##### 基本使用

`public void lockInterruptibly()`：获得可打断的锁

* 如果没有竞争此方法就会获取 lock 对象锁
* 如果有竞争就进入阻塞队列，可以被其他线程用 interrupt 打断

注意：如果是不可中断模式，那么即使使用了 interrupt 也不会让等待中断

```java
public static void main(String[] args) throws InterruptedException {    
    ReentrantLock lock = new ReentrantLock();    
    Thread t1 = new Thread(() -> {        
        try {            
            System.out.println("尝试获取锁");            
            lock.lockInterruptibly();        
        } catch (InterruptedException e) {            
            System.out.println("没有获取到锁，被打断，直接返回");            
            return;        
        }        
        try {            
            System.out.println("获取到锁");        
        } finally {            
            lock.unlock();        
        }    
    }, "t1");    
    lock.lock();    
    t1.start();    
    Thread.sleep(2000);    
    System.out.println("主线程进行打断锁");    
    t1.interrupt();
}
```



##### 实现原理

* 不可打断模式：即使它被打断，仍会驻留在 AQS 队列中，一直要等到获得锁后方能得知自己被打断了

  ```java
  public final void acquire(int arg) {    
      if (!tryAcquire(arg) &&        
          acquireQueued(addWaiter(Node.EXCLUSIVE), arg))//阻塞等待        
          // 如果acquireQueued返回true，打断状态interrupted = true        
          selfInterrupt();
  }
  static void selfInterrupt() {    
      // 知道自己被打断了，需要重新产生一次中断完成中断效果    
      Thread.currentThread().interrupt();
  }
  ```

  ```java
  final boolean acquireQueued(final Node node, int arg) {    
      try {        
          boolean interrupted = false;        
          for (;;) {            
              final Node p = node.predecessor();            
              if (p == head && tryAcquire(arg)) {                
                  setHead(node);                
                  p.next = null; // help GC                
                  failed = false;                
                  // 还是需要获得锁后, 才能返回打断状态                
                  return interrupted;            
              }            
              if (shouldParkAfterFailedAcquire(p, node) &&                
                  parkAndCheckInterrupt())//被打断 返回true                
                  interrupted = true;        
          }
      }
      private final boolean parkAndCheckInterrupt() {    
          // 阻塞当前线程，如果打断标记已经是 true, 则 park 会失效    
          LockSupport.park(this);    
          // 判断当前线程是否被打断，清除打断标记，被打断返回true    
          return Thread.interrupted();
      }
  }
  ```

* 可打断模式：

  ```java
  public void lockInterruptibly() throws InterruptedException {    
      sync.acquireInterruptibly(1);
  }
  public final void acquireInterruptibly(int arg) {    
      if (Thread.interrupted())//被其他线程打断了       
  		throw new InterruptedException();    
      if (!tryAcquire(arg))        
          // 没获取到锁，进入这里        
          doAcquireInterruptibly(arg);
  }
  ```

  ```java
  private void doAcquireInterruptibly(int arg) {    
      final Node node = addWaiter(Node.EXCLUSIVE);    
      boolean failed = true;    
      try {        
          for (;;) {            
              //...            
              if (shouldParkAfterFailedAcquire(p, node)&&parkAndCheckInterrupt())
                  throw new InterruptedException();				
              // 在 park 过程中如果被 interrupt 会抛出异常, 而不会再次进入循环        
          }    
      }
  }
  ```



***



#### 锁超时

##### 基本使用

`public boolean tryLock()`：尝试获取锁，获取到返回true，获取不到直接放弃，不进入阻塞队列

`public boolean tryLock(long timeout, TimeUnit unit)`：在给定时间内获取锁，获取不到就退出

注意：tryLock期间也可以被打断

```java
public static void main(String[] args) {
    ReentrantLock lock = new ReentrantLock();
    Thread t1 = new Thread(() -> {
        try {
            if (!lock.tryLock(2, TimeUnit.SECONDS)) {
                System.out.println("获取不到锁");
                return;
            }
        } catch (InterruptedException e) {
            System.out.println("被打断，获取不到锁");
            return;
        }
        try {
            log.debug("获取到锁");
        } finally {
            lock.unlock();
        }
    }, "t1");
    lock.lock();
    System.out.println("主线程获取到锁");
    t1.start();
    
    Thread.sleep(1000);
    try {
        System.out.println("主线程释放了锁");
    } finally {
        lock.unlock();
    }
}
```



##### 实现原理

* tryLock()

  ```java
  public boolean tryLock() {    
      return sync.nonfairTryAcquire(1);//只尝试一次
  }
  ```

* tryLock(long timeout, TimeUnit unit)

  ```java
  public final boolean tryAcquireNanos(int arg, long nanosTimeout) {    
      if (Thread.interrupted())        
          throw new InterruptedException();    
      //tryAcquire 尝试一次    
      return tryAcquire(arg) || doAcquireNanos(arg, nanosTimeout);
  }
  protected final boolean tryAcquire(int acquires) {    
      return nonfairTryAcquire(acquires);
  }
  ```

  ```java
  private boolean doAcquireNanos(int arg, long nanosTimeout) {    
      if (nanosTimeout <= 0L)        
          return false;    
      final long deadline = System.nanoTime() + nanosTimeout;    
      //...    
      try {        
          for (;;) {   
              //...
              nanosTimeout = deadline - System.nanoTime();            
              if (nanosTimeout <= 0L)	//时间已到                
                  return false;            
              if (shouldParkAfterFailedAcquire(p, node) &&                
                  nanosTimeout > spinForTimeoutThreshold)                
                  LockSupport.parkNanos(this, nanosTimeout);            
              if (Thread.interrupted())                
                  throw new InterruptedException();        
          }    
      }
  }
  ```



##### 哲学家就餐

```java
public static void main(String[] args) {    
    Chopstick c1 = new Chopstick("1");//...  
    Chopstick c5 = new Chopstick("5");    
    new Philosopher("苏格拉底", c1, c2).start();    
    new Philosopher("柏拉图", c2, c3).start();    
    new Philosopher("亚里士多德", c3, c4).start();    
    new Philosopher("赫拉克利特", c4, c5).start();    
    new Philosopher("阿基米德", c5, c1).start();
}
class Philosopher extends Thread {    
    Chopstick left;    
    Chopstick right;    
    public void run() {        
        while (true) {            
            // 尝试获得左手筷子            
            if (left.tryLock()) {                
                try {                    
                    // 尝试获得右手筷子                    
                    if (right.tryLock()) {                        
                        try {							
                            System.out.println("eating...");       
                            Thread.sleep(1000);                        
                        } finally {                            
                            right.unlock();                        
                        }                    
                    }                
                } finally {                    
                    left.unlock();                
                }            
            }        
        }	
    }
}
class Chopstick extends ReentrantLock {    
    String name;    
    public Chopstick(String name) {        
        this.name = name;    
    }    
    @Override    
    public String toString() {        
        return "筷子{" + name + '}';    
    }
}
```



***



#### 条件变量

##### 基本使用

synchronized 的条件变量，是当条件不满足时进入 waitSet 等待；ReentrantLock 的条件变量比 synchronized 强大之处在于，它支持多个条件变量

ReentrantLock 类获取 Condition 对象：`public Condition newCondition()`

Condition类API：

* `void await()`：当前线程从运行状态进入等待状态，释放锁
* `void signal()`：唤醒一个等待在Condition上的线程，但是必须获得与该Condition相关的锁

使用流程：

* **await / signal 前需要获得锁**
* await 执行后，会释放锁进入 conditionObject 等待
* await 的线程被唤醒（打断、超时）去重新竞争 lock 锁
* 竞争 lock 锁成功后，从 await 后继续执行

```java
public static void main(String[] args) throws InterruptedException {    
    ReentrantLock lock = new ReentrantLock();    
    //创建一个新的条件变量    
    Condition condition1 = lock.newCondition();    
    Condition condition2 = lock.newCondition();    
    new Thread(() -> {        
        try {            
            lock.lock();                      
            System.out.println("进入等待");     
            //进入休息室等待  
            condition1.await();            
            System.out.println("被唤醒了");        
        } catch (InterruptedException e) {            
            e.printStackTrace();        
        } finally {            
            lock.unlock();        
        }    
    }).start();    
    Thread.sleep(1000);    
    //叫醒    
    new Thread(() -> {        
        try {            
            lock.lock();            
            //唤醒           
            condition2.signal();        
        } finally {            
            lock.unlock();        
        }    
    }).start();
}
```



##### 实现原理

await流程：

* 开始 Thread-0 持有锁，调用 await，进入 ConditionObject 的 addConditionWaiter 流程

  ```java
  // 等待，直到被唤醒或打断
  public final void await() throws InterruptedException {
      if (Thread.interrupted())
          throw new InterruptedException();
      // 添加一个 Node 至等待队列,
      Node node = addConditionWaiter();
      // 释放节点持有的锁
      int savedState = fullyRelease(node);
      int interruptMode = 0;
      // 如果该节点还没有转移至 AQS 队列, park 阻塞
      while (!isOnSyncQueue(node)) {
          LockSupport.park(this);
          // 如果被打断, 退出等待队列，判断打断模式
          if ((interruptMode = checkInterruptWhileWaiting(node)) != 0)
              break;
      }
      // 退出等待队列后, 还需要获得 AQS 队列的锁
      if (acquireQueued(node, savedState) && interruptMode != THROW_IE)
          interruptMode = REINTERRUPT;
      // 所有已取消的 Node 从队列链表删除
      if (node.nextWaiter != null)
          unlinkCancelledWaiters();
      // 应用打断模式
      if (interruptMode != 0)
          reportInterruptAfterWait(interruptMode);
  }
  ```

  ```java
  // 打断模式 - 在退出等待时重新设置打断状态
  private static final int REINTERRUPT = 1;
  // 打断模式 - 在退出等待时抛出异常
  private static final int THROW_IE = -1;
  ```

* 创建新的 Node 状态为 -2（Node.CONDITION），关联 Thread-0，加入等待队列尾部

  ```java
  // 添加一个 Node 至等待队列
  private Node addConditionWaiter() {
      Node t = lastWaiter;
      // 所有已取消的 Node 从队列链表删除,
      if (t != null && t.waitStatus != Node.CONDITION) {
          unlinkCancelledWaiters();
          t = lastWaiter;
      }
      // 创建一个关联当前线程的新 Node, 添加至队列尾部
      Node node = new Node(Thread.currentThread(), Node.CONDITION);
      if (t == null)
          firstWaiter = node;
      else
          t.nextWaiter = node;
      lastWaiter = node;// 单向链表
      return node;
  }
  ```

  ```java
  private void unlinkCancelledWaiters() {    
      Node t = firstWaiter;   
      Node trail = null;    
      while (t != null) {        
          Node next = t.nextWaiter;        
          // 判断 t 节点不是 CONDITION 节点        
          if (t.waitStatus != Node.CONDITION) {            
              // t 与下一个节点断开            
              t.nextWaiter = null;            
              // 如果第一次循环就进入if语句，说明 t 是首节点            
              if (trail == null)                
                  firstWaiter = next;            
              else                
                  // t 的前节点和后节点相连，删除 t                
                  trail.nextWaiter = next;            
              // t 是尾节点了            
              if (next == null)                
                  lastWaiter = trail;        
          } else           
              trail = t;        
          t = next; // 把 t.next 赋值给 t     
      }
  }
  ```

* 接下来进入 AQS 的 fullyRelease 流程，释放同步器上的锁

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-条件变量1.png)

  ```java
  // 线程可能重入，需要将 state 全部释放
  final int fullyRelease(Node node) {    
      boolean failed = true;    
      try {        
          int savedState = getState();        
          // release -> tryRelease 公平锁解锁，会解锁重入锁        
          if (release(savedState)) {            
              failed = false;            
              return savedState;        
          } else {            
              throw new IllegalMonitorStateException();        
          }    
      } finally {        
          // 没有释放成功，设置为取消状态        
          if (failed)            
              node.waitStatus = Node.CANCELLED;    
      }
  }
  ```

* unpark AQS 队列中的下一个节点，竞争锁，假设没有其他竞争线程，那么 Thread-1 竞争成功

* park 阻塞 Thread-0

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-条件变量2.png)



signal 流程：

* 假设 Thread-1 要来唤醒 Thread-0，进入 ConditionObject 的 doSignal 流程，取得等待队列中第一个 Node，即 Thread-0 所在 Node

  ```java
  // 唤醒 - 必须持有锁才能唤醒, 因此 doSignal 内无需考虑加锁
  public final void signal() {    
      // 调用方法的线程是否是资源的持有线程    
      if (!isHeldExclusively())        
          throw new IllegalMonitorStateException();    
      // 取得等待队列中第一个 Node   
      Node first = firstWaiter;    
      if (first != null)        
          doSignal(first);
  }
  ```

  ```java
  // 唤醒 - 将没取消的第一个节点转移至 AQS 队列尾部
  private void doSignal(Node first) {
      do {
          // 当前节点是尾节点
          if ((firstWaiter = first.nextWaiter) == null)
              lastWaiter = null;
          first.nextWaiter = null;
      // 将等待队列中的 Node 转移至 AQS 队列, 不成功且还有节点则继续循环
      } while (!transferForSignal(first) &&
               (first = firstWaiter) != null);
  }
  private void doSignalAll(Node first) {
      lastWaiter = firstWaiter = null;
      do {
          Node next = first.nextWaiter;
          first.nextWaiter = null;
          transferForSignal(first);
          first = next;
      } while (first != null);
  }
  ```

* 执行 transferForSignal 流程，将该 Node 加入 AQS 队列尾部，将 Thread-0 的 waitStatus 改为 0，Thread-3 的waitStatus 改为 -1

  ```java
  // 如果节点状态是取消, 返回 false 表示转移失败, 否则转移成功
  final boolean transferForSignal(Node node) {
      // 如果状态已经不是 Node.CONDITION, 说明被取消了
      if (!compareAndSetWaitStatus(node, Node.CONDITION, 0))
          return false;
      // 加入 AQS 队列尾部
      Node p = enq(node);
      int ws = p.waitStatus;
      // 上一个节点被取消  上一个节点不能设置状态为 Node.SIGNAL
      if (ws > 0 || !compareAndSetWaitStatus(p, ws, Node.SIGNAL))
          // unpark 取消阻塞, 让线程重新同步状态
          LockSupport.unpark(node.thread);
      return true;
  }
  ```

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantLock-条件变量3.png)

* Thread-1 释放锁，进入 unlock 流程





***



### ReadWrite

#### 读写锁

独占锁：指该锁一次只能被一个线程所持有，对ReentrantLock和Synchronized而言都是独占锁
共享锁：指该锁可以被多个线程锁持有

ReentrantReadWriteLock 其读锁是共享，其写锁是独占

作用：多个线程同时读一个资源类没有任何问题，为了满足并发量，读取共享资源应该同时进行，但是如果一个线程想去写共享资源，就不应该再有其它线程可以对该资源进行读或写

使用规则：

* 加锁解锁格式：

  ```java
  r.lock();
  try {
      // 临界区
  } finally {
  	r.unlock();
  }
  ```

* 读-读 能共存、读-写 不能共存、写-写 不能共存

* 读锁不支持条件变量

* **重入时升级不支持**：持有读锁的情况下去获取写锁会导致获取写锁永久等待，需要先释放读，再去获得写

* **重入时降级支持**：持有写锁的情况下去获取读锁

  ```java
  w.lock();
  try {
      r.lock();// 降级为读锁, 释放写锁, 这样能够让其它线程读取缓存
      try {
          // ...
      } finally{
      	w.unlock();// 要在写锁释放之前获取读锁
      }
  } finally{
  	r.unlock();
  }
  ```

构造方法：
	`public ReentrantReadWriteLock()`：默认构造方法，非公平锁
	`public ReentrantReadWriteLock(boolean fair)`：true 为公平锁

常用API：
	`public ReentrantReadWriteLock.ReadLock readLock()`：返回读锁
	`public ReentrantReadWriteLock.WriteLock writeLock()`：返回写锁
	`public void lock()`：加锁
	`public void unlock()`：解锁
	`public boolean tryLock()`：尝试获取锁

读读并发：

```java
public static void main(String[] args) {
    ReentrantReadWriteLock rw = new ReentrantReadWriteLock();
    ReentrantReadWriteLock.ReadLock r = rw.readLock();
    ReentrantReadWriteLock.WriteLock w = rw.writeLock();

    new Thread(() -> {
        r.lock();
        try {
            Thread.sleep(2000);
            System.out.println("Thread 1 running " + new Date());
        } finally {
            r.unlock();
        }
    },"t1").start();
    new Thread(() -> {
        r.lock();
        try {
            Thread.sleep(2000);
            System.out.println("Thread 2 running " + new Date());
        } finally {
            r.unlock();
        }
    },"t2").start();
}
```



***



#### 缓存应用

缓存更新时，是先清缓存还是先更新数据库

* 先清缓存：可能造成刚清理缓存还没有更新数据库，线程直接查询了数据库更新缓存

* 先更新据库：可能造成刚更新数据库，还没清空缓存就有线程从缓存拿到了旧数据

* 补充情况：查询线程 A 查询数据时恰好缓存数据由于时间到期失效，或是第一次查询

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantReadWriteLock缓存.png" style="zoom:80%;" />

可以使用读写锁进行操作



***



#### 实现原理

##### 加锁原理

读写锁用的是同一个 Sycn 同步器，因此等待队列、state 等也是同一个，原理与 ReentrantLock 加锁相比没有特殊之处，不同是写锁状态占了 state 的低 16 位，而读锁使用的是 state 的高 16 位

* t1 w.lock（写锁），成功上锁 state = 0_1

  ```java
  //lock()  -> sync.acquire(1);
  public final void acquire(int arg) {
      // 尝试获得写锁
      if (!tryAcquire(arg) &&
          // 获得写锁失败，将当前线程关联到一个 Node 对象上, 模式为独占模式
          acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
          selfInterrupt();
  }
  ```

  ```java
  protected final boolean tryAcquire(int acquires) {
      Thread current = Thread.currentThread();
      int c = getState();
      // 获得低 16 位, 代表写锁的 state 计数
      int w = exclusiveCount(c);
      if (c != 0) {
          // c != 0 and w == 0 表示r != 0，有读锁，并且写锁的拥有者不是自己，获取失败
          if (w == 0 || current != getExclusiveOwnerThread())
              return false;
          // 锁重入计数超过低 16 位, 报异常
          if (w + exclusiveCount(acquires) > MAX_COUNT)
              throw new Error("Maximum lock count exceeded");
          // 写锁重入, 获得锁成功
          setState(c + acquires);
          return true;
      }
      // c == 0，没有任何锁，判断写锁是否该阻塞
      if (writerShouldBlock() ||
          !compareAndSetState(c, c + acquires))
          return false;
      // 获得锁成功
      setExclusiveOwnerThread(current);
      return true;
  }
  // 非公平锁 writerShouldBlock 总是返回 false, 无需阻塞
  final boolean writerShouldBlock() {
      return false; 
  }
  // 公平锁会检查 AQS 队列中是否有前驱节点, 没有(false)才去竞争
  final boolean writerShouldBlock() {
      return hasQueuedPredecessors();
  }
  ```

* t2 r.lock（读锁），进入 tryAcquireShared 流程，如果有写锁占据，那么 tryAcquireShared 

  * 返回 -1 表示失败
  * 如果返回 0 表示成功
  * 返回正数表示还有多少后继节点支持共享模式，读写锁返回1

  ```java
  public void lock() {
      sync.acquireShared(1);
  }
  public final void acquireShared(int arg) {
      // tryAcquireShared 返回负数, 表示获取读锁失败
      if (tryAcquireShared(arg) < 0)
          doAcquireShared(arg);
  }
  ```

  ```java
  // 尝试以共享模式获取
  protected final int tryAcquireShared(int unused) {
      Thread current = Thread.currentThread();
      int c = getState();
      // 如果是其它线程持有写锁, 并且写锁的持有者不是当前线程，获取读锁失败
      if (exclusiveCount(c) != 0 && //低 16 位, 代表写锁的 state
          getExclusiveOwnerThread() != current)
          return -1;
      // 高 16 位，代表读锁的 state
      int r = sharedCount(c);
      if (!readerShouldBlock() &&	// 读锁不该阻塞
          r < MAX_COUNT &&		// 小于读锁计数	
          compareAndSetState(c, c + SHARED_UNIT)) {// 尝试增加计数成功
          // ....
          // 读锁加锁成功
          return 1;
      }
      // 与 tryAcquireShared 功能类似, 但会不断尝试 for (;;) 获取读锁, 执行过程中无阻塞
      return fullTryAcquireShared(current);
  }
  // 非公平锁 readerShouldBlock 看 AQS 队列中第一个节点是否是写锁，是则阻塞，反之不阻塞
  // true 则该阻塞, false 则不阻塞
  final boolean readerShouldBlock() {
      return apparentlyFirstQueuedIsExclusive();
  }
  ```

* 进入 sync.doAcquireShared(1) 流程，首先也是调用 addWaiter 添加节点，不同之处在于节点被设置为Node.SHARED 模式而非 Node.EXCLUSIVE 模式，注意此时 t2 仍处于活跃状态

  ```java
  private void doAcquireShared(int arg) {
      // 将当前线程关联到一个 Node 对象上, 模式为共享模式
      final Node node = addWaiter(Node.SHARED);
      boolean failed = true;
      try {
          boolean interrupted = false;
          for (;;) {
              // 获取前驱节点
              final Node p = node.predecessor();
              if (p == head) {
                  // 再一次尝试获取读锁
                  int r = tryAcquireShared(arg);
                  // r >= 0 表示获取成功
                  if (r >= 0) {
                      setHeadAndPropagate(node, r);
                      p.next = null; // help GC
                      if (interrupted)
                          selfInterrupt();
                      failed = false;
                      return;
                  }
              }
              // 是否在获取读锁失败时阻塞
              if (shouldParkAfterFailedAcquire(p, node) &&
                  // park 当前线程
                  parkAndCheckInterrupt())
                  interrupted = true;
          }
      } finally {
          if (failed)
              cancelAcquire(node);
      }
  }
  ```

  ```java
  private void setHeadAndPropagate(Node node, int propagate) {
      Node h = head; 
      // 设置自己为 head 节点
      setHead(node);
      // propagate 表示有共享资源（例如共享读锁或信号量），为 0 就没有资源
      if (propagate > 0 || h == null || h.waitStatus < 0 ||
          (h = head) == null || h.waitStatus < 0) {
          Node s = node.next;
          // 如果是最后一个节点或者是等待共享读锁的节点
          if (s == null || s.isShared())
              // 用来唤醒后继节点
              doReleaseShared();
      }
  }
  ```

  ```java
  private void doReleaseShared() {
      // 如果 head.waitStatus == Node.SIGNAL ==> 0 成功, 下一个节点 unpark
  	// 如果 head.waitStatus == 0 ==> Node.PROPAGATE
      for (;;) {
          Node h = head;
          if (h != null && h != tail) {
              int ws = h.waitStatus;
              if (ws == Node.SIGNAL) {
                  // 因为读锁共享，如果其它线程也在释放读锁，那么需要将 waitStatus 先改为 0
              	// 防止 unparkSuccessor 被多次执行
                  if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))
                      continue;  
                  // 唤醒后继节点
                  unparkSuccessor(h);
              }
              // 如果已经是 0 了，改为 -3，用来解决传播性
              else if (ws == 0 &&
                       !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))
                  continue;                
          }
          if (h == head)                   
              break;
      }
  }
  ```

* 如果没有成功，在 doAcquireShared 内 for (;;) 循环一次，shouldParkAfterFailedAcquire 内把前驱节点的 waitStatus 改为 -1，再 for (;;) 循环一次尝试 tryAcquireShared，不成功在parkAndCheckInterrupt() 处 park

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantReadWriteLock加锁1.png" style="zoom: 80%;" />

* 这种状态下，假设又有t3 r.lock，t4 w.lock，这期间 t1 仍然持有锁，就变成了下面的样子

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantReadWriteLock加锁2.png)



***



##### 解锁原理

* t1 w.unlock， 调用 sync.tryRelease(1) 成功

  ```java
  // sync.release(1) -> tryRelease(1)
  protected final boolean tryRelease(int releases) {
      if (!isHeldExclusively())
          throw new IllegalMonitorStateException();
      int nextc = getState() - releases;
      // 因为可重入的原因, 写锁计数为 0, 才算释放成功
      boolean free = exclusiveCount(nextc) == 0;
      if (free)
          setExclusiveOwnerThread(null);
      setState(nextc);
      return free;
  }
  ```

* 唤醒流程 sync.unparkSuccessor，这时 t2 在 doAcquireShared 的 parkAndCheckInterrupt() 处恢复运行，继续循环，执行 tryAcquireShared 成功则让读锁计数加一

* 接下来 t2 调用 setHeadAndPropagate(node, 1)，它原本所在节点被置为头节点；还会检查下一个节点是否是 shared，如果是则调用 doReleaseShared() 将 head 的状态从 -1 改为 0 并唤醒下一个节点，这时 t3 在 doAcquireShared 内 parkAndCheckInterrupt() 处恢复运行

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantReadWriteLock解锁1.png" style="zoom: 67%;" />

* 下一个节点不是 shared 了，因此不会继续唤醒 t4 所在节点

* t2 进入 sync.releaseShared(1) 中，调用 tryReleaseShared(1) 让计数减一，但计数还不为零
  t3 同样让计数减一，计数为零，进入doReleaseShared() 将头节点从 -1 改为 0 并唤醒下一个节点

  ```java
  public void unlock() {
      sync.releaseShared(1);
  }
  public final boolean releaseShared(int arg) {
      if (tryReleaseShared(arg)) {
          doReleaseShared();
          return true;
      }
      return false;
  }
  ```

  ```java
  protected final boolean tryReleaseShared(int unused) {
      //  
      for (;;) {
          int c = getState();
          int nextc = c - SHARED_UNIT;
          // 读锁的计数不会影响其它获取读锁线程, 但会影响其它获取写锁线程
  		// 计数为 0 才是真正释放
          if (compareAndSetState(c, nextc))
              return nextc == 0;
      }
  }
  ```

* t4 在 acquireQueued 中 parkAndCheckInterrupt 处恢复运行，再次 for (;;) 这次自己是头节点的临节点，并且没有其他节点竞争，tryAcquire(1) 成功，修改头结点，流程结束

  <img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ReentrantReadWriteLock解锁2.png" style="zoom: 67%;" />



***



#### Stamped

StampedLock：读写锁，该类自 JDK 8 加入，是为了进一步优化读性能

特点：

* 在使用读锁、写锁时都必须配合戳使用

* StampedLock 不支持条件变量
* StampedLock **不支持可重入**

基本用法

* 加解读锁：

  ```java
  long stamp = lock.readLock();
  lock.unlockRead(stamp);
  ```

* 加解写锁：

  ```java
  long stamp = lock.writeLock();
  lock.unlockWrite(stamp);
  ```

* 乐观读，StampedLock 支持`tryOptimisticRead()`方法，读取完毕后做一次**戳校验**，如果校验通过，表示这期间没有其他线程的写操作，数据可以安全使用，如果校验没通过，需要重新获取读锁，保证数据安全

  ```java
  long stamp = lock.tryOptimisticRead();
  // 验戳
  if(!lock.validate(stamp)){
  	// 锁升级
  }
  ```

提供一个数据容器类内部分别使用读锁保护数据的 read() 方法，写锁保护数据的 write() 方法：

* 读-读 可以优化
* 读-写 优化读，补加读锁

```java
public static void main(String[] args) throws InterruptedException {
    DataContainerStamped dataContainer = new DataContainerStamped(1);
    new Thread(() -> {
    	dataContainer.read(1000);
    },"t1").start();
    Thread.sleep(500);
    
    new Thread(() -> {
        dataContainer.write(1000);
    },"t2").start();
}

class DataContainerStamped {
    private int data;
    private final StampedLock lock = new StampedLock();

    public int read(int readTime) throws InterruptedException {
        long stamp = lock.tryOptimisticRead();
        System.out.println(new Date() + " optimistic read locking" + stamp);
        Thread.sleep(readTime);
        if (lock.validate(stamp)) {
            Sout(new Date() + " optimistic read finish..." + stamp);
            return data;
        }

        //锁升级
        System.out.println(new Date() + " updating to read lock" + stamp);
        try {
            stamp = lock.readLock();
            System.out.println(new Date() + " read lock" + stamp);
            Thread.sleep(readTime);
            System.out.println(new Date() + " read finish..." + stamp);
            return data;
        } finally {
            System.out.println(new Date() + " read unlock " +  stamp);
            lock.unlockRead(stamp);
        }
    }

    public void write(int newData) {
        long stamp = lock.writeLock();
        System.out.println(new Date() + " write lock " + stamp);
        try {
            Thread.sleep(2000);
            this.data = newData;
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            System.out.println(new Date() + " write unlock " + stamp);
            lock.unlockWrite(stamp);
        }
    }
}
```





***



### Semaphore

#### 信号量

synchronized 可以起到锁的作用，但某个时间段内，只能有一个线程允许执行

Semaphore（信号量）用来限制能同时访问共享资源的线程上限，非重入锁

构造方法：

* `public Semaphore(int permits)`：permits 表示许可线程的数量（state）
* `public Semaphore(int permits, boolean fair)`：fair 表示公平性，如果这个设为 true 的话，下次执行的线程会是等待最久的线程

常用API：

* `public void acquire()`：表示获取许可
* `public void release()`：表示释放许可，acquire()和release()方法之间的代码为同步代码

```java
public static void main(String[] args) {
    // 1.创建Semaphore对象
    Semaphore semaphore = new Semaphore(3);

    // 2. 10个线程同时运行
    for (int i = 0; i < 10; i++) {
        new Thread(() -> {
            try {
                // 3. 获取许可
                semaphore.acquire();
                sout(Thread.currentThread().getName() + " running...");
                Thread.sleep(1000);
                sout(Thread.currentThread().getName() + " end...");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                // 4. 释放许可
                semaphore.release();
            }
        }).start();
    }
}
```



***



#### 实现原理

加锁流程：

* Semaphore 的 permits（state）为 3，这时 5 个线程来获取资源

  ```java
  Sync(int permits) {
      setState(permits);
  }
  ```

  假设其中 Thread-1，Thread-2，Thread-4 CAS 竞争成功，permits 变为 0，而 Thread-0 和 Thread-3 竞争失败，进入 AQS 队列park 阻塞

  ```java
  // acquire() -> sync.acquireSharedInterruptibly(1);
  public final void acquireSharedInterruptibly(int arg) {
      if (Thread.interrupted())
          throw new InterruptedException();
      if (tryAcquireShared(arg) < 0)
          doAcquireSharedInterruptibly(arg);
  }
  
  // tryAcquireShared() -> nonfairTryAcquireShared()
  // 非公平，公平锁会在循环内 hasQueuedPredecessors()方法判断是否有临头节点(第二个节点)
  final int nonfairTryAcquireShared(int acquires) {
      for (;;) {
          int available = getState();
          int remaining = available - acquires;
          // 如果许可已经用完, 返回负数, 表示获取失败,
          if (remaining < 0 ||
              // 如果 cas 重试成功, 返回正数, 表示获取成功
              compareAndSetState(available, remaining))
              return remaining;
      }
  }
  ```

  ```java
  private void doAcquireSharedInterruptibly(int arg) {
      final Node node = addWaiter(Node.SHARED);
      boolean failed = true;
      try {
          for (;;) {
              final Node p = node.predecessor();
              if (p == head) {
                  // 再次尝试获取许可
                  int r = tryAcquireShared(arg);
                  if (r >= 0) {
                      // 成功后本线程出队（AQS）, 所在 Node设置为 head
                      // r 表示可用资源数, 为 0 则不会继续传播
                      setHeadAndPropagate(node, r); //参考 PROPAGATE
                      p.next = null; // help GC
                      failed = false;
                      return;
                  }
              }
              // 不成功, 设置上一个节点 waitStatus = Node.SIGNAL, 下轮进入 park 阻塞
              if (shouldParkAfterFailedAcquire(p, node) &&
                  parkAndCheckInterrupt())
                  throw new InterruptedException();
          }
      } finally {
          if (failed)
              cancelAcquire(node);
      }
  }
  ```

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-Semaphore工作流程1.png)

* 这时 Thread-4 释放了 permits，状态如下

  ```java
  // release() -> releaseShared()
  public final boolean releaseShared(int arg) {    
      if (tryReleaseShared(arg)) {        
          doReleaseShared();        
          return true;    
      }    
      return false;
  }
  protected final boolean tryReleaseShared(int releases) {    
      for (;;) {        
          int current = getState();        
          int next = current + releases;        
          if (next < current)            
              throw new Error("Maximum permit count exceeded");        
          // 释放锁        
          if (compareAndSetState(current, next))            
              return true;    
      }
  }
  private void doReleaseShared() {    
      // PROPAGATE 详解    
      // 如果 head.waitStatus == Node.SIGNAL ==> 0 成功, 下一个节点 unpark	
      // 如果 head.waitStatus == 0 ==> Node.PROPAGATE
  }
  ```

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-Semaphore工作流程2.png)

* 接下来 Thread-0 竞争成功，permits 再次设置为 0，设置自己为 head 节点，unpark 接下来的 Thread-3 节点，但由于 permits 是 0，因此 Thread-3 在尝试不成功后再次进入 park 状态



****



#### PROPAGATE

假设存在某次循环中队列里排队的结点情况为 `head(-1)->t1(-1)->t2(0)`
假设存在将要信号量释放的 T3 和 T4，释放顺序为先 T3 后 T4

```java
// 老版本代码
private void setHeadAndPropagate(Node node, int propagate) {    
    setHead(node);    
    // 有空闲资源    
    if (propagate > 0 && node.waitStatus != 0) {    	
        Node s = node.next;        
        // 下一个        
        if (s == null || s.isShared())            
            unparkSuccessor(node);        
    }
}
```

正常流程：

* T3 调用 releaseShared(1)，直接调用了 unparkSuccessor(head)，head.waitStatus 从 -1 变为 0
* T1 由于 T3 释放信号量被唤醒，然后T4 释放，唤醒 T2

BUG流程：

* T3 调用 releaseShared(1)，直接调用了 unparkSuccessor(head)，head.waitStatus 从 -1 变为 0
* T1 由于 T3 释放信号量被唤醒，调用 tryAcquireShared，返回值为 0（获取锁成功，但没有剩余资源量）
* T1 还没调用setHeadAndPropagate方法，T4 调用 releaseShared(1)，此时 head.waitStatus 为 0（此时读到的 head 和 1 中为同一个head），不满足条件，因此不调用 unparkSuccessor(head)
* T1 获取信号量成功，调用 setHeadAndPropagate(t1.node, 0) 时，因为不满足 propagate > 0（剩余资源量 == 0），从而不会唤醒后继结点， **T2 线程得不到唤醒**



更新后流程：

* T3 调用 releaseShared(1)，直接调用了 unparkSuccessor(head)，head.waitStatus 从 -1 变为 0
* T1 由于 T3 释放信号量被唤醒，调用 tryAcquireShared，返回值为 0（获取锁成功，但没有剩余资源量）

* T1 还没调用setHeadAndPropagate方法，T4 调用 releaseShared()，此时 head.waitStatus 为 0（此时读到的 head 和 1 中为同一个 head），调用 doReleaseShared() 将等待状态置为**PROPAGATE（-3）**
* T1 获取信号量成功，调用 setHeadAndPropagate 时，读到 h.waitStatus < 0，从而调用 doReleaseShared() 唤醒 T2

```java
private void setHeadAndPropagate(Node node, int propagate) {    
    Node h = head;     
    // 设置自己为 head 节点    
    setHead(node);    
    // propagate 表示有共享资源（例如共享读锁或信号量）    
    // head waitStatus == Node.SIGNAL 或 Node.PROPAGATE    
    if (propagate > 0 || h == null || h.waitStatus < 0 ||
        (h = head) == null || h.waitStatus < 0) {        
        Node s = node.next;        
        // 如果是最后一个节点或者是等待共享读锁的节点，做一次唤醒        
        if (s == null || s.isShared())            
            doReleaseShared();    
    
    }}
```

```java
// 唤醒
private void doReleaseShared() {    
    // 如果 head.waitStatus == Node.SIGNAL ==> 0 成功, 下一个节点 unpark	
    // 如果 head.waitStatus == 0 ==> Node.PROPAGATE    
    for (;;) {        
        Node h = head;        
        if (h != null && h != tail) {            
            int ws = h.waitStatus;            
            if (ws == Node.SIGNAL) {            	
                // 防止 unparkSuccessor 被多次执行                
                if (!compareAndSetWaitStatus(h, Node.SIGNAL, 0))                   
                    continue;                  
                // 唤醒后继节点                
                unparkSuccessor(h);            
            }            
            // 如果已经是 0 了，改为 -3，用来解决传播性            
            else if (ws == 0 && !compareAndSetWaitStatus(h, 0, Node.PROPAGATE))      				continue;                        
        }        
        if (h == head)                               
            break;    
    }
}
```





***



### CountDown

CountDownLatch：计数器，用来进行线程同步协作，等待所有线程完成

构造器：

* `public CountDownLatch(int count)`：初始化唤醒需要的 down 几步

常用API：

* `public void await() `：让当前线程等待，必须 down 完初始化的数字才可以被唤醒，否则进入无限等待
* `public void countDown()`：计数器进行减1（down 1）

应用：同步等待多个 Rest 远程调用结束

```java
// LOL 10人进入游戏倒计时
public static void main(String[] args) throws InterruptedException {
    CountDownLatch latch = new CountDownLatch(10);
    ExecutorService service = Executors.newFixedThreadPool(10);
    String[] all = new String[10];
    Random random = new Random();

    for (int j = 0; j < 10; j++) {
        int finalJ = j;//常量
        service.submit(() -> {
            for (int i = 0; i <= 100; i++) {
                Thread.sleep(random.nextInt(100));//随机休眠
                all[finalJ] = i + "%";
                System.out.print("\r" + Arrays.toString(all));// \r代表覆盖
            }
            latch.countDown();
        });
    }
    latch.await();
    System.out.println("\n游戏开始");
    service.shutdown();
}
/*
[100%, 100%, 100%, 100%, 100%, 100%, 100%, 100%, 100%, 100%]
游戏开始
```



***



### CyclicBarrier

CyclicBarrier作用：循环屏障，用来进行线程协作，等待线程满足某个计数，才能触发自己执行

常用方法：

* `public CyclicBarrier(int parties, Runnable barrierAction)`：用于在线程到达屏障parties时，执行barrierAction
  * parties：代表多少个线程到达屏障开始触发线程任务
  * barrierAction：线程任务
* `public int await()`：线程调用 await 方法通知 CyclicBarrier 本线程已经到达屏障

与 CountDownLatch 的区别：CyclicBarrier 是可以重用的

应用：可以实现多线程中，某个任务在等待其他线程执行完毕以后触发

```java
public static void main(String[] args) {
    ExecutorService service = Executors.newFixedThreadPool(2);
    CyclicBarrier barrier = new CyclicBarrier(2, () -> {
        System.out.println("task1 task2 finish...");
    });

    for (int i = 0; i < 3; i++) {// 循环重用
        service.submit(() -> {
            System.out.println("task1 begin...");
            try {
                Thread.sleep(1000);
                barrier.await();    // 2 - 1 = 1
            } catch (InterruptedException | BrokenBarrierException e) {
                e.printStackTrace();
            }
        });

        service.submit(() -> {
            System.out.println("task2 begin...");
            try {
                Thread.sleep(2000);
                barrier.await();    // 1 - 1 = 0
            } catch (InterruptedException | BrokenBarrierException e) {
                e.printStackTrace();
            }
        });
    }
    service.shutdown();
}
```



***



### Exchanger

Exchanger：交换器，是一个用于线程间协作的工具类，用于进行线程间的数据交换

工作流程：两个线程通过 exchange 方法交换数据，如果第一个线程先执行 exchange() 方法，它会一直等待第二个线程也执行 exchange 方法，当两个线程都到达同步点时，这两个线程就可以交换数据

常用方法：

* `public Exchanger()`：创建一个新的交换器
* `public V exchange(V x)`：等待另一个线程到达此交换点
* `public V exchange(V x, long timeout, TimeUnit unit)`：等待一定的时间

```java
public class ExchangerDemo {
    public static void main(String[] args) {
        // 创建交换对象（信使）
        Exchanger<String> exchanger = new Exchanger<>();
        new ThreadA(exchanger).start();
        new ThreadA(exchanger).start();
    } 
}
class ThreadA extends Thread{
    private Exchanger<String> exchanger();
    
    public ThreadA(Exchanger<String> exchanger){
        this.exchanger = exchanger;
    }
    
    @Override
    public void run() {
        try{
            sout("线程A，做好了礼物A，等待线程B送来的礼物B");
            //如果等待了5s还没有交换就死亡（抛出异常）！
            String s = exchanger.exchange("礼物A",5,TimeUnit.SECONDS);
            sout("线程A收到线程B的礼物：" + s);
        } catch (Exception e) {
            System.out.println("线程A等待了5s，没有收到礼物,最终就执行结束了!");
        }
    }
}
class ThreadB extends Thread{
    private Exchanger<String> exchanger;
    
    public ThreadB(Exchanger<String> exchanger) {
        this.exchanger = exchanger;
    }
    
    @Override
    public void run() {
        try {
            sout("线程B,做好了礼物B,等待线程A送来的礼物A.....");
            // 开始交换礼物。参数是送给其他线程的礼物!
            sout("线程B收到线程A的礼物：" + exchanger.exchange("礼物B"));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```







***





## 并发包

### ConHashMap

（待更新）

#### 并发集合

##### 集合对比

三种集合：

* HashMap是线程不安全的，性能好
* Hashtable线程安全基于synchronized，综合性能差，已经被淘汰
* ConcurrentHashMap保证了线程安全，综合性能较好，不止线程安全，而且效率高，性能好

集合对比：

1. Hashtable继承Dictionary类，HashMap、ConcurrentHashMap继承AbstractMap，均实现Map接口
2. Hashtable底层是数组+链表，JDK8以后HashMap和ConcurrentHashMap底层是数组+链表+红黑树
3. HashMap线程非安全，Hashtable线程安全，Hashtable的方法都加了synchronized关来确保线程同步
4. ConcurrentHashMap、Hashtable不允许null值，HashMap允许null值
5. ConcurrentHashMap、HashMap的初始容量为16，Hashtable初始容量为11，填充因子默认都是0.75，两种Map扩容是当前容量翻倍：capacity * 2，Hashtable扩容时是容量翻倍+1：capacity*2 + 1

![ConcurrentHashMap数据结构](https://gitee.com/seazean/images/raw/master/Java/ConcurrentHashMap数据结构.png)

工作步骤：

1. 初始化，使用 cas 来保证并发安全，懒惰初始化 table
2. 树化，当 table.length < 64 时，先尝试扩容，超过 64 时，并且 bin.length > 8 时，会将链表树化，树化过程
   会用 synchronized 锁住链表头
3. put，如果该 bin 尚未创建，只需要使用 cas 创建 bin；如果已经有了，锁住链表头进行后续 put 操作，元素
   添加至 bin 的尾部
4. get，无锁操作仅需要保证可见性，扩容过程中 get 操作拿到的是 ForwardingNode 会让 get 操作在新 table 进行搜索
5. 扩容，扩容时以 bin 为单位进行，需要对 bin 进行 synchronized，但这时其它竞争线程也不是无事可做，它们会帮助把其它 bin 进行扩容
6. size，元素个数保存在 baseCount 中，并发时的个数变动保存在 CounterCell[] 当中，最后统计数量时累加

```java
//需求：多个线程同时往HashMap容器中存入数据会出现安全问题
public class ConcurrentHashMapDemo{
    public static Map<String,String> map = new ConcurrentHashMap();
    
    public static void main(String[] args){
        new AddMapDataThread().start();
        new AddMapDataThread().start();
        
        Thread.sleep(1000 * 5);//休息5秒，确保两个线程执行完毕
        System.out.println("Map大小：" + map.size());//20万
    }
}

public class AddMapDataThread extends Thread{
    @Override
    public void run() {
        for(int i = 0 ; i < 1000000 ; i++ ){
            ConcurrentHashMapDemo.map.put("键："+i , "值"+i);
        }
    }
}
```



****



##### 并发死链

JDK1.7的 HashMap 采用的头插法（拉链法）进行节点的添加，HashMap 的扩容长度为原来的 2 倍

resize() 中 节点（Entry）转移的源代码：

```java
void transfer(Entry[] newTable, boolean rehash) {
    int newCapacity = newTable.length;//得到新数组的长度   
    //遍历整个数组对应下标下的链表，e代表一个节点
    for (Entry<K,V> e : table) {   
        //当e == null时，则该链表遍历完了，继续遍历下一数组下标的链表 
        while(null != e) { 
            //先把e节点的下一节点存起来
            Entry<K,V> next = e.next; 
            if (rehash) {              //得到新的hash值
                e.hash = null == e.key ? 0 : hash(e.key);  
            }
            //在新数组下得到新的数组下标
            int i = indexFor(e.hash, newCapacity);  
             //将e的next指针指向新数组下标的位置
            e.next = newTable[i];   
            //将该数组下标的节点变为e节点
            newTable[i] = e; 
            //遍历链表的下一节点
            e = next;                                   
        }
    }
}
```

B站视频解析：https://www.bilibili.com/video/BV1n541177Ea

文章参考：https://www.jianshu.com/p/c4c4ff869149

JDK 8 虽然将扩容算法做了调整，改用了尾插法，但仍不意味着能够在多线程环境下能够安全扩容，还会出现其它问题（如扩容丢数据）



***



#### JDK8源码

（待更新）

##### 成员属性

1. 扩容阈值

   ```java
   // 默认为 0、当初始化时为 -1、当扩容时为 -(1 + 扩容线程数)
   // 当初始化或扩容完成后，为下一次的扩容的阈值大小，当前数组大小的0.75
   private transient volatile int sizeCtl;
   ```

2. Node节点

   ```java
   static class Node<K,V> implements Map.Entry<K,V> {
       final int hash;
       final K key;
       volatile V val;	// 保证并发的可见性
       volatile Node<K,V> next;
       Node(int hash, K key, V val, Node<K,V> next){//构造方法}
   }
   ```

3. Hash表

   ```java
   transient volatile Node<K,V>[] table;
   private transient volatile Node<K,V>[] nextTable; //扩容时的新 hash 表
   ```

4. 扩容时如果某个 bin 迁移完毕，用 ForwardingNode 作为旧 table bin 的头结点

   ```java
   static final class ForwardingNode<K,V> extends Node<K,V> {
       ForwardingNode(Node<K,V>[] tab) {
           super(MOVED, null, null, null);// MOVE = -1
           this.nextTable = tab;
   	}
   	//super -> Node节点构造方法：Node(int hash, K key, V val, Node<K,V> next)
   }
   ```

5. compute 以及 computeIfAbsent 时，用来占位，计算完成后替换为普通 Node

   ```java
   static final class ReservationNode<K,V> extends Node<K,V>{
       ReservationNode() {
           super(RESERVED, null, null, null);// RESERVED = -3
       }
   }
   ```

6. treebin 的头节点, 存储 root 和 first

   ```java
   static final class TreeBin<K,V> extends Node<K,V>{}
   ```

7. treebin 的节点, 存储 parent、left、right

   ```java
   static final class TreeNode<K,V> extends Node<K,V>{}
   ```

   

***



##### 构造方法

懒惰初始化，在构造方法中仅计算了 table 的大小，在第一次使用时才会真正创建：

```java
public ConcurrentHashMap(int initialCapacity,float loadFactor,int concurrencyLevel){
    // 参数校验
    if (!(loadFactor > 0.0f) || initialCapacity < 0 || concurrencyLevel <= 0)
        throw new IllegalArgumentException();
    // 检验并发级别
    if (initialCapacity < concurrencyLevel)   
        initialCapacity = concurrencyLevel;
    long size = (long)(1.0 + (long)initialCapacity / loadFactor);
    // tableSizeFor 仍然是保证计算的大小是 2^n, 即 16,32,64 ...
    int cap = (size >= (long)MAXIMUM_CAPACITY) ?
        MAXIMUM_CAPACITY : tableSizeFor((int)size);
    this.sizeCtl = cap;
}
```



***



##### 成员方法

1. put()：数组简称（table），链表简称（bin）

   ```java
   public V put(K key, V value) {
       return putVal(key, value, false);
   }
   final V putVal(K key, V value, boolean onlyIfAbsent) {
       // 不允许存null，和hashmap不同
       if (key == null || value == null) throw new NullPointerException();
       // spread 方法会综合高位低位, 具有更好的 hash 性
       int hash = spread(key.hashCode());
       int binCount = 0;
       for (Node<K,V>[] tab = table;;) {
           // f 是链表头节点、fh 是链表头结点的 hash、i 是链表在 table 中的下标
           Node<K,V> f; int n, i, fh;
           if (tab == null || (n = tab.length) == 0)
               // 初始化 table 使用 cas 创建成功, 进入下一轮循环
               tab = initTable();
           // 创建头节点
           else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
               if (casTabAt(tab, i, null, new Node<K,V>(hash, key, value, null)))
                   break;
           }
           // 旧table的某个bin的头节点 hash 为-1，表明正在扩容，可以帮忙扩容
           else if ((fh = f.hash) == MOVED)
               tab = helpTransfer(tab, f);
           else {
               V oldVal = null;
               // 锁住链表头节点
               synchronized (f) {
                   if (tabAt(tab, i) == f) {	// 确认链表头节点没有被移动
                       // 链表
                       if (fh >= 0) {
                           binCount = 1;
                           // 遍历链表   binCount 对应 链表节点的个数
                           for (Node<K,V> e = f;; ++binCount) {
                               K ek;
                               if (e.hash == hash &&
                                   ((ek = e.key) == key ||
                                    (ek != null && key.equals(ek)))) {
                                   oldVal = e.val;
                                   // 是否允许更新旧值
                                   if (!onlyIfAbsent)
                                       e.val = value;
                                   break;
                               }
                               Node<K,V> pred = e;
                               // 最后的节点, 新增 Node 追加至链表尾
                               if ((e = e.next) == null) {
                                   pred.next = new Node<K,V>(hash,key,value,null);
                                   break;
                               }
                           }
                       }
                       // 红黑树
                       else if (f instanceof TreeBin) {
                           Node<K,V> p;
                           binCount = 2;
                           // 检查 key 是否已经在树中, 是,则返回对应的 TreeNode
                           if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                                 value)) != null) {
                               oldVal = p.val;
                               if (!onlyIfAbsent)
                                   p.val = value;
                           }
                       }
                   }
               }
               if (binCount != 0) {
                   // 如果链表长度 >= 树化阈值(8), 进行链表转为红黑树
                   if (binCount >= TREEIFY_THRESHOLD)
                       treeifyBin(tab, i);
                   if (oldVal != null)
                       return oldVal;
                   break;
               }
           }
       }
       addCount(1L, binCount);
       return null;
   }
   ```

2. initTable

   ```java
   private final Node<K,V>[] initTable() {
       Node<K,V>[] tab; int sc;
       while ((tab = table) == null || tab.length == 0) {
           if ((sc = sizeCtl) < 0)
               // 只允许一个线程对表进行初始化，让掉当前线程 CPU 的时间片，
               Thread.yield();
           // 尝试将 sizeCtl 设置为 -1（表示初始化 table）
           else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
               // 获得锁, 其它线程会在 while() 循环中 yield 直至 table 创建
               try {
                   if ((tab = table) == null || tab.length == 0) {
                       int n = (sc > 0) ? sc : DEFAULT_CAPACITY;//16
                       @SuppressWarnings("unchecked")
                       Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                       table = tab = nt;
                       sc = n - (n >>> 2);// 16 - 4;n - n/4 = 0.75n
                   }
               } finally {
                   sizeCtl = sc;
               }
               break;
           }
       }
       return tab;
   }
   ```

3. get

   ```java
   public V get(Object key) {
       Node<K,V>[] tab; Node<K,V> e, p; int n, eh; K ek;
       // spread 方法能确保返回结果是正数
       int h = spread(key.hashCode());
       if ((tab = table) != null && (n = tab.length) > 0 &&
           (e = tabAt(tab, (n - 1) & h)) != null) {
           // 如果头结点已经是要查找的 key
           if ((eh = e.hash) == h) {
               if ((ek = e.key) == key || (ek != null && key.equals(ek)))
                   return e.val;
           }
           // hash 为负数表示该 bin 在扩容中或是 treebin, 这时调用 find 方法来查找
           else if (eh < 0)
               return (p = e.find(h, key)) != null ? p.val : null;
           // 正常遍历链表，用equals比较
           while ((e = e.next) != null) {
               if (e.hash == h &&
                   ((ek = e.key) == key || (ek != null && key.equals(ek))))
                   return e.val;
           }
       }
       return null;
   }
   ```

4. size

   size 计算实际发生在 put，remove 改变集合元素的操作之中

   * 没有竞争发生，向 baseCount 累加计数
   * 有竞争发生，新建 counterCells，向其中的一个 cell 累加计数
     * counterCells 初始有两个 cell
     * 如果计数竞争比较激烈，会创建新的 cell 来累加计数

   ```java
   public int size() {
       long n = sumCount();
       return ((n < 0L) ? 0 :
               (n > (long)Integer.MAX_VALUE) ? Integer.MAX_VALUE :
               (int)n);
   }
   final long sumCount() {
       CounterCell[] as = counterCells; CounterCell a;
       // 将 baseCount 计数与所有 cell 计数累加
       long sum = baseCount;
       if (as != null) {
           for (int i = 0; i < as.length; ++i) {
               if ((a = as[i]) != null)
                   sum += a.value;
           }
       }
   }
   ```

   

***



#### JDK7源码

##### 分段锁

ConcurrentHashMap 对锁粒度进行了优化，**分段锁技术**，将整张表分成了多个数组（Segment），每个数组又是一个类似 HashMap 数组的结构。`ConcurrentHashMap`允许多个修改操作并发进行，并发时锁住的是每个Segment，其他Segment还是可以操作的，这样不同Segment之间就可以实现并发，大大提高效率

底层结构： **Segment 数组 + HashEntry 数组 + 链表**（数组+链表是HashMap的结构）

* 优点：如果多个线程访问不同的 segment，实际是没有冲突的，这与 jdk8 中是类似的

* 缺点：Segments 数组默认大小为16，这个容量初始化指定后就不能改变了，并且不是懒惰初始化

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentHashMap 1.7底层结构.png)





##### 成员方法

1. segment：是一种可重入锁，继承ReentrantLock 

   ```java
   static final class Segment<K,V> extends ReentrantLock implements Serializable {
        transient volatile HashEntry<K,V>[] table; //可以理解为包含一个HashMap
   }
   ```

2. 构造方法

   无参构造：

   ```java
   public ConcurrentHashMap() {
       this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR, DEFAULT_CONCURRENCY_LEVEL);
   }
   ```

   ```java
   // 默认初始化容量
   static final int DEFAULT_INITIAL_CAPACITY = 16;
   // 默认负载因子
   static final float DEFAULT_LOAD_FACTOR = 0.75f;
   // 默认并发级别
   static final int DEFAULT_CONCURRENCY_LEVEL = 16;
   ```

   说明：并发度就是程序运行时能够**同时更新**ConccurentHashMap且不产生锁竞争的最大线程数，实际上就是ConcurrentHashMap中的分段锁个数。如果并发度设置的过小，会带来严重的锁竞争问题；如果并发度设置的过大，原本位于同一个Segment内的访问会扩散到不同的Segment中，**CPU cache命中率**会下降

   ```java
   public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel) {
       // 参数校验
       if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
           throw new IllegalArgumentException();
       // 校验并发级别大小，大于 1<<16，重置为 65536
       if (concurrencyLevel > MAX_SEGMENTS)
           concurrencyLevel = MAX_SEGMENTS;
   	// ssize 必须是 2^n, 即 2, 4, 8, 16 ... 表示了 segments 数组的大小
       int sshift = 0;
       int ssize = 1;
       // 这个循环可以找到 concurrencyLevel 之上最近的 2的次方值 ！！！
       while (ssize < concurrencyLevel) {
           ++sshift;
           ssize <<= 1;
       }
   	// 记录段偏移量 默认是 32 - 4 = 28
       this.segmentShift = 32 - sshift;
   	// 记录段掩码 默认是 15 即 0000 0000 0000 1111
       this.segmentMask = ssize - 1;
       // 最大容量
       if (initialCapacity > MAXIMUM_CAPACITY)
           initialCapacity = MAXIMUM_CAPACITY;
       // c = 容量/ssize ，默认16/16 = 1，计算每个Segment中的类似于HashMap的容量
       int c = initialCapacity / ssize;
       if (c * ssize < initialCapacity)
           ++c; //确保向上取值
       int cap = MIN_SEGMENT_TABLE_CAPACITY;
       // Segment 中的类似于 HashMap 的容量至少是2或者2的倍数
       while (cap < c)
           cap <<= 1;
   	// 创建 segment数组，设置segments[0]
       Segment<K,V> s0 = new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                       (HashEntry<K,V>[])new HashEntry[cap]);
       // 默认大小为 2，负载因子 0.75，扩容阀值是 2*0.75=1.5，插入第二个值时才会进行扩容
       Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
       UNSAFE.putOrderedObject(ss, SBASE, s0);
       this.segments = ss;
   }
   ```

3. put：头插法

   segmentShift 和 segmentMask 的作用是决定将 key 的 hash 结果匹配到哪个 segment，将 hash 值 高位向低位移动 segmentShift 位，结果再与 segmentMask 做位于运算

   ```java
   public V put(K key, V value) {
       Segment<K,V> s;
       if (value == null)
           throw new NullPointerException();
       int hash = hash(key);
       // 计算出 segment 下标
       int j = (hash >>> segmentShift) & segmentMask;
       // 获得 segment 对象, 判断是否为 null, 是则创建该 segment
       if ((s = (Segment<K,V>)UNSAFE.getObject          
            (segments, (j << SSHIFT) + SBASE)) == null)
           // 这时不能确定是否真的为 null, 因为其它线程也发现该 segment 为 null,
   		// 因此在 ensureSegment 里用 cas 方式保证该 segment 安全性
           s = ensureSegment(j);
       // 进入 segment 的put 流程
       return s.put(key, hash, value, false);
   }
   ```

   ```java
   private Segment<K,V> ensureSegment(int k) {
       final Segment<K,V>[] ss = this.segments;
       long u = (k << SSHIFT) + SBASE; 
       Segment<K,V> seg;
       // 判断 u 位置的 Segment 是否为null
       if ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u)) == null) {
           Segment<K,V> proto = ss[0]; // use segment 0 as prototype
           // 获取0号 segment 的 HashEntry<K,V> 初始化长度
           int cap = proto.table.length;
           // 获取0号 segment 的 hash 表里的扩容负载因子，所有的 segment 因子是相同的
           float lf = proto.loadFactor;
           // 计算扩容阀值
           int threshold = (int)(cap * lf);
           // 创建一个 cap 容量的 HashEntry 数组
           HashEntry<K,V>[] tab = (HashEntry<K,V>[])new HashEntry[cap];
           // 再次检查 u 位置的 Segment 是否为null，因为这时可能有其他线程进行了操作
           if ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u)) == null) {
               // 初始化 Segment
               Segment<K,V> s = new Segment<K,V>(lf, threshold, tab);
               // 自旋检查 u 位置的 Segment 是否为null
               while ((seg = (Segment<K,V>)UNSAFE.getObjectVolatile(ss, u))==null) {
                   // 使用CAS 赋值，只会成功一次
                   if (UNSAFE.compareAndSwapObject(ss, u, null, seg = s))
                       break;
               }
           }
       }
       return seg;
   }
   ```

   ConcurrentHashMap 在 put 一个数据时的处理流程：

   * 计算要 put 的 key 的位置，获取指定位置的 Segment
   * 如果指定位置的 Segment 为空，则初始化这个 Segment
     * 检查计算得到的位置的 Segment 是否为null，为 null 继续初始化，使用 Segment[0] 的容量和负载因子创建一个 HashEntry 数组
     * 再次检查计算得到的指定位置的 Segment 是否为null，使用创建的 HashEntry 数组初始化这个 Segment
     * 自旋判断指定位置的 Segment 是否为null，使用 CAS 在这个位置赋值为 Segment
   * Segment.put 插入 key value 值

   segment 继承了可重入锁（ReentrantLock），它的 put 方法：

   ```java
   final V put(K key, int hash, V value, boolean onlyIfAbsent) {
       // 获取 ReentrantLock 独占锁，获取不到，scanAndLockForPut 获取
       // 如果是多核 cpu 最多 tryLock 64 次, 进入 lock 流程
       // 在尝试期间, 还可以顺便看该节点在链表中有没有, 如果没有顺便创建出来
       HashEntry<K,V> node = tryLock() ? null : scanAndLockForPut(key, hash, value);
       
       // 执行到这里 segment 已经被成功加锁, 可以安全执行
       V oldValue;
       try {
           HashEntry<K,V>[] tab = table;
           // 计算要put的数据位置
           int index = (tab.length - 1) & hash;
           // CAS 获取 index 坐标的值
           HashEntry<K,V> first = entryAt(tab, index);
           for (HashEntry<K,V> e = first;;) {
               if (e != null) {
                   // 检查是否 key 已经存在，如果存在，则遍历链表寻找位置，找到后替换
                   K k;
                   if ((k = e.key) == key ||
                       (e.hash == hash && key.equals(k))) {
                       oldValue = e.value;
                       if (!onlyIfAbsent) {
                           e.value = value;
                           ++modCount;
                       }
                       break;
                   }
                   e = e.next;
               }
               else {
                   // first 有值没说明 index 位置已经有值了，有冲突，链表头插法
                   // 之前等待锁时, node 已经被创建, next 指向链表头
                   if (node != null)
                       node.setNext(first);
                   else
                       node = new HashEntry<K,V>(hash, key, value, first);
                   int c = count + 1;
                   // 容量大于扩容阀值，小于最大容量，进行扩容
                   if (c > threshold && tab.length < MAXIMUM_CAPACITY)
                       rehash(node);
                   else
                       // 将 node 作为链表头
                       setEntryAt(tab, index, node);
                   ++modCount;
                   count = c;
                   oldValue = null;
                   break;
               }
           }
       } finally {
           unlock();
       }
       return oldValue;
   }
   ```

4. rehash

   发生在 put 中，因为此时已经获得了锁，因此 rehash 时不需要考虑线程安全

   扩容扩容到原来的两倍，老数组里的数据移动到新的数组时，位置要么不变，要么变为 index+ oldSize，参数里的 node 会在扩容之后使用链表**头插法**插入到指定位置

   ```java
   private void rehash(HashEntry<K,V> node) {
       HashEntry<K,V>[] oldTable = table;
       // 老容量
       int oldCapacity = oldTable.length;
       // 新容量，扩大两倍
       int newCapacity = oldCapacity << 1;
       // 新的扩容阀值 
       threshold = (int)(newCapacity * loadFactor);
       // 创建新的数组
       HashEntry<K,V>[] newTable = (HashEntry<K,V>[]) new HashEntry[newCapacity];
       // 新的掩码，比如2扩容后是4，-1是3，二进制就是11
       int sizeMask = newCapacity - 1;
       // 遍历老数组
       for (int i = 0; i < oldCapacity ; i++) {
           HashEntry<K,V> e = oldTable[i];
           if (e != null) {
               HashEntry<K,V> next = e.next;
               // 计算新的位置，新的位置只可能是不变或者是老的位置+老的容量
               int idx = e.hash & sizeMask;
               // next为空，只有一个节点，直接赋值
               if (next == null)  
                   newTable[idx] = e;
               else { 
                   // 如果是链表
                   HashEntry<K,V> lastRun = e;
                   int lastIdx = idx;
                   // 遍历
                   for (HashEntry<K,V> last = next; last != null; last = last.next) {
                       int k = last.hash & sizeMask;
                       // 与下一个节点位置相等直接继续循环，不相等进入if逻辑块
                       if (k != lastIdx) {
                           // 新位置
                           lastIdx = k;
                           // 把下一个作为新的链表的首部
                           lastRun = last;
                       }
                   }
                   // lastRun 后面的元素位置都是相同的，直接作为链表赋值到新位置
                   newTable[lastIdx] = lastRun;
                   
                   // 遍历剩余元素，头插法到指定 k 位置，需要新建节点
                   for (HashEntry<K,V> p = e; p != lastRun; p = p.next) {
                       V v = p.value;
                       int h = p.hash;
                       int k = h & sizeMask;
                       HashEntry<K,V> n = newTable[k];
                       newTable[k] = new HashEntry<K,V>(h, p.key, v, n);
                   }
               }
           }
       }
       // 头插法插入新的节点，put的节点，因为是put节点超过阈值才扩容
       int nodeIndex = node.hash & sizeMask;
       node.setNext(newTable[nodeIndex]);
       newTable[nodeIndex] = node;
       
       // 替换为新的 HashEntry table
       table = newTable;
   }
   ```

   * 第一个 for 是为了寻找一个节点，该节点后面的所有 next 节点的新位置都是相同的，然后把这个作为一个链表搬迁到新位置
   * 第二个 for 循环是为了把剩余的元素通过头插法插入到指定位置链表

5. get

   计算得到 key 的存放位置、遍历指定位置查找相同 key 的 value 值

   用于存储键值对数据的`HashEntry`，它的成员变量value跟`next`都是`volatile`类型的，这样就保证别的线程对value值的修改，get方法可以马上看到

   ```java
   public V get(Object key) {
       Segment<K,V> s; 
       HashEntry<K,V>[] tab;
       int h = hash(key);
       // u 为 segment 对象在数组中的偏移量
       long u = (((h >>> segmentShift) & segmentMask) << SSHIFT) + SBASE;
       // 计算得到 key 的存放位置
       if ((s = (Segment<K,V>)UNSAFE.getObjectVolatile(segments, u)) != null &&
           (tab = s.table) != null) {
           for (HashEntry<K,V> e = (HashEntry<K,V>) UNSAFE.getObjectVolatile
                    (tab, ((long)(((tab.length - 1) & h)) << TSHIFT) + TBASE);
                e != null; e = e.next) {
               // 如果是链表，遍历查找到相同 key 的 value。
               K k;
               if ((k = e.key) == key || (e.hash == h && key.equals(k)))
                   return e.value;
           }
       }
       return null;
   }
   ```

6. size

   * 计算元素个数前，先不加锁计算两次，如果前后两次结果如一样，认为个数正确返回
   * 如果不一样，进行重试，重试次数超过 3，将所有 segment 锁住，重新计算个数返回

   ```java
   public int size() {
       final Segment<K,V>[] segments = this.segments;
       int size;
       boolean overflow;
       long sum;
       long last = 0L;
       int retries = -1;
       try {
           for (;;) {
               if (retries++ == RETRIES_BEFORE_LOCK) {
                   // 超过重试次数, 需要创建所有 segment 并加锁
                   for (int j = 0; j < segments.length; ++j)
                       ensureSegment(j).lock(); 
               }
               sum = 0L;
               size = 0;
               overflow = false;
               for (int j = 0; j < segments.length; ++j) {
                   Segment<K,V> seg = segmentAt(segments, j);
                   if (seg != null) {
                       sum += seg.modCount;
                       int c = seg.count;
                       if (c < 0 || (size += c) < 0)
                           overflow = true;
                   }
               }
               if (sum == last)
                   break;
               last = sum;
           }
       } finally {
           if (retries > RETRIES_BEFORE_LOCK) {
               for (int j = 0; j < segments.length; ++j)
                   segmentAt(segments, j).unlock();
           }
       }
       return overflow ? Integer.MAX_VALUE : size;
   }
   ```





***



### CopyOnWrite

#### 原理分析

CopyOnWriteArrayList 采用了**写入时拷贝**的思想，增删改操作会将底层数组拷贝一份，更改操作在新数组上执行，不影响其它线程的**并发读，读写分离**

CopyOnWriteArraySet 底层对 CopyOnWriteArrayList 进行了包装，装饰器模式

```java
public CopyOnWriteArraySet() {
    al = new CopyOnWriteArrayList<E>();
}
```

* 存储结构：

  ```java
  private transient volatile Object[] array;//保证了读写线程之间的可见性
  ```

* 新增数据：

  ```java
  public boolean add(E e) {
      final ReentrantLock lock = this.lock;
      lock.lock();
      try {
          // 获取旧的数组
          Object[] elements = getArray();
          int len = elements.length;
          // 拷贝新的数组（这里是比较耗时的操作，但不影响其它读线程）
          Object[] newElements = Arrays.copyOf(elements, len + 1);
          // 添加新元素
          newElements[len] = e;
          // 替换旧的数组
          setArray(newElements);
          return true;
      } finally {
          lock.unlock();
      }
  }
  ```

* 读操作：

  ```java
  public void forEach(Consumer<? super E> action) {
      if (action == null) throw new NullPointerException();
      // 获取数据集合，放入
      Object[] elements = getArray();// 返回当前存储数据的数组
      int len = elements.length;
      for (int i = 0; i < len; ++i) {
          //遍历
          @SuppressWarnings("unchecked") E e = (E) elements[i];
          // 对给定的参数执行此操作
          action.accept(e);
      }
  }
  ```

  适合读多写少的应用场景

* 迭代器：

  CopyOnWriteArrayList 在返回一个迭代器时，会基于创建这个迭代器时内部数组所拥有的数据，创建一个该内部数组当前的快照，然后迭代器遍历的是该快照，而不是内部的数组，所以这种实现方式也存在一定的数据延迟性，即对其他线程并行添加的数据不可见

  ```java
  public Iterator<E> iterator() {
      return new COWIterator<E>(getArray(), 0);
  }
  
  // 迭代器会创建一个底层array的快照，故主类的修改不影响该快照
  static final class COWIterator<E> implements ListIterator<E> {
      // 内部数组快照
      private final Object[] snapshot;
  
  	//...
      // 不支持写操作
      public void remove() {
          throw new UnsupportedOperationException();
      } 
  }
  ```

  

***



#### 弱一致性

##### get方法

数据一致性就是读到最新更新的数据：

* 强一致性：当更新操作完成之后，任何多个后续进程或者线程的访问都会返回最新的更新过的值

* 弱一致性：系统并不保证进程或者线程的访问都会返回最新的更新过的值，也不会承诺多久之后可以读到

<img src="https://gitee.com/seazean/images/raw/master/Java/JUC-CopyOnWriteArrayList弱一致性.png" style="zoom:80%;" />

| 时间点 | 操作                         |
| ------ | ---------------------------- |
| 1      | Thread-0 getArray()          |
| 2      | Thread-1 getArray()          |
| 3      | Thread-1 setArray(arrayCopy) |
| 4      | Thread-0 array[index]        |

Thread-0读到了脏数据



##### 迭代器

```java
public static void main(String[] args) throws InterruptedException {
    CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>();
    list.add(1);
    list.add(2);
    list.add(3);
    Iterator<Integer> iter = list.iterator();
    new Thread(() -> {
        list.remove(0);
        System.out.println(list);[2,3]
    }).start();
    
    Thread.sleep(1000);
    while (iter.hasNext()) {
        System.out.println(iter.next());// 1 2 3
    }
}
```

不一定弱一致性就不好

* 数据库的事务隔离级别都是弱一致性的表现
* 并发高和一致性是矛盾的，需要权衡



***



#### 安全失败

在 java.util 包的集合类就都是快速失败的，而 java.util.concurrent 包下的类都是安全失败

* 快速失败：在 A 线程使用**迭代器**对集合进行遍历的过程中，此时 B 线程对集合进行修改（增删改），或者 A 线程在遍历过程中对集合进行修改，都会导致 A 线程抛出 ConcurrentModificationException 异常
  * AbstractList 类中的成员变量 modCount，用来记录 List 结构发生变化的次数，**结构发生变化**是指添加或者删除至少一个元素的操作，或者是调整内部数组的大小，仅仅设置元素的值不算结构发生变化
  * 在进行序列化或者迭代等操作时，需要比较操作前后 modCount 是否改变，如果改变了抛出 CME 异常
* 安全失败：采用安全失败机制的集合容器，在**迭代器**遍历时不是直接在集合内容上访问的，而是先复制原有集合内容，在复制集合上进行遍历。由于迭代时不是对原集合进行遍历，所以在遍历过程中对原集合所作的修改并不能被迭代器检测到，故不会抛 ConcurrentModificationException 异常



***



### Collections

Collections类是用来操作集合的工具类，提供了集合转换成线程安全的方法：

```java
 public static <T> Collection<T> synchronizedCollection(Collection<T> c) {
     return new SynchronizedCollection<>(c);
 }
public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m) {
    return new SynchronizedMap<>(m);
}
```

源码：底层也是对方法进行加锁

```java
public boolean add(E e) {
    synchronized (mutex) {return c.add(e);}
}
```



***



### SkipListMap

#### 底层结构

跳表 SkipList 是一个**有序的链表**，默认升序，底层是链表加多级索引的结构。跳表可以对元素进行快速查询，类似于平衡树，是一种利用空间换时间的算法

对于单链表，即使链表是有序的，如果查找数据也只能从头到尾遍历链表，所以采用链表上建索引的方式提高效率，跳表的查询时间复杂度是 **O(logn)**，空间复杂度 O(n)

ConcurrentSkipListMap 提供了一种线程安全的并发访问的排序映射表，内部是跳表结构实现，通过 CAS +　volatile 保证线程安全

平衡树和跳表的区别：

* 对平衡树的插入和删除往往很可能导致平衡树进行一次全局的调整；而对跳表的插入和删除，只需要对整个数据结构的局部进行操作
* 在高并发的情况下，保证整个平衡树的线程安全需要一个全局锁；对于跳表则只需要部分锁，拥有更好的性能

![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentSkipListMap数据结构.png)

BaseHeader 存储数据，headIndex 存储索引，纵向上**所有索引指向链表最下面的节点**



***



#### 成员变量

* 标识索引头节点位置

  ```java
  private static final Object BASE_HEADER = new Object();
  ```

* 跳表的顶层索引

  ```java
  private transient volatile HeadIndex<K,V> head;
  ```

* 比较器，为 null 则使用自然排序

  ```java
  final Comparator<? super K> comparator;
  ```

* Node 节点

  ```java
  static final class Node<K, V>{
      final K key;  // key 是 final 的, 说明节点一旦定下来, 除了删除, 不然不会改动 key
      volatile Object value; // 对应的 value
      volatile Node<K, V> next; // 下一个节点
  }
  ```

* 索引节点 Index

  ```java
  static class Index<K, V>{
      final Node<K, V> node; // 索引指向的节点, 
      final Index<K, V> down; // 下边level层的Index，分层索引
      volatile Index<K, V> right; // 右边的Index
  
      // 在index本身和succ之间插入一个新的节点newSucc
      final boolean link(Index<K, V> succ, Index<K, V> newSucc){
          Node<K, V> n = node;
          newSucc.right = succ;
          return n.value != null && casRight(succ, newSucc);
      }
  
      // 将当前的节点 index 设置其的 right 为 succ.right 等于删除 succ 节点
      final boolean unlink(Index<K, V> succ){
          return node.value != null && casRight(succ, succ.right);
      }
  }
  ```

* 头索引节点 HeadIndex

  ```java
  static final class HeadIndex<K,V> extends Index<K,V> {
      final int level;// 标示索引层级，所有的HeadIndex都指向同一个Base_header节点
      HeadIndex(Node<K,V> node, Index<K,V> down, Index<K,V> right, int level) {
          super(node, down, right);
          this.level = level;
      }
  }
  ```



***



#### 成员方法

##### 其他方法

* 构造方法：

  ```java
  public ConcurrentSkipListMap() {
      this.comparator = null;	// comparator为null，使用key的自然序，如字典序
      initialize();
  }
  ```

  ```java
  private void initialize() {
      keySet = null;
      entrySet = null;
      values = null;
      descendingMap = null;
      //初始化索引头节点，Node的Key为null，value为BASE_HEADER对象，下一个节点为null
      //head的分层索引down为null，链表的后续索引right为null，层级level为第一层。
      head = new HeadIndex<K,V>(new Node<K,V>(null, BASE_HEADER, null),
                                null, null, 1);
  }
  ```

* cpr：排序

  ```java
  //　x是比较者，y是被比较者，比较者大于被比较者 返回正数，小于返回负数，相等返回0
  static final int cpr(Comparator c, Object x, Object y) {
      return (c != null) ? c.compare(x, y) : ((Comparable)x).compareTo(y);
  }
  ```



***



##### 添加方法

* findPredecessor()：寻找前驱节点

  从最上层的头索引开始向右查找（链表的后续索引），如果后续索引的节点的Key大于要查找的Key，则头索引移到下层链表，在下层链表查找，以此反复，一直查找到没有下层的分层索引为止，返回该索引的节点。如果后续索引的节点的Key小于要查找的Key，则在该层链表中向后查找。由于查找的key可能永远大于索引节点的 key，所以只能找到目标的前置索引节点。如果遇到空值索引的存在，通过CAS来断开索引

  ```java
  private Node<K,V> findPredecessor(Object key, Comparator<? super K> cmp) {
      if (key == null)
          throw new NullPointerException(); // don't postpone errors
      for (;;) {
          // 1.初始化数据q是head，r是最顶层h的右Index节点
          for (Index<K,V> q = head, r = q.right, d;;) {
              //2.右索引节点不为空，则进行向下查找
              if (r != null) {
                  Node<K,V> n = r.node;
                  K k = n.key;
                  //3.n.value为null说明节点n正在删除的过程中
                  if (n.value == null) {
                      //在index层直接删除r索引节点，用在删除节点中
                      if (!q.unlink(r))
                          break;//重新从 head 节点开始查找，break到步骤1
                      //删除节点r成功，获取新的r节点, 回到步骤 2 
  					//还是从这层索引开始向右遍历, 直到 r == null
                      r = q.right;
                      continue;
                  }
                  //4.若参数key > r.node.key，则继续向右遍历, continue到步骤2处
                  //  若参数key < r.node.key，直接跳到步骤5
                  if (cpr(cmp, key, k) > 0) {
                      q = r;
                      r = r.right;
                      continue;
                  }
              }
              //5.先让d指向q的下一层，判断是否是null，是则说明已经到了数据层，也就是第一层
              if ((d = q.down) == null) 
                  return q.node;
              //6.未到数据层, 进行重新赋值向下扫描
              q = d;	//q指向d
              r = d.right;//r指向q的后续索引节点
          }
      }
  }
  ```

  ```java
  final boolean unlink(Index<K,V> succ) {
      return node.value != null && casRight(succ, succ.right);
      //   this.node = q
  }
  ```

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentSkipListMap-Put流程.png)

* put()

  ```java
  public V put(K key, V value) {
      // 非空判断，value不能为空
      if (value == null)
          throw new NullPointerException();
      return doPut(key, value, false);
  }
  ```

  ```java
  private V doPut(K key, V value, boolean onlyIfAbsent) {
      Node<K,V> z;
      if (key == null)// 非空判断，key不能为空
          throw new NullPointerException();
      Comparator<? super K> cmp = comparator;
      // outer循环，处理并发冲突等其他需要重试的情况
      outer: for (;;) {
          //0.for (;;)
          //1.将 key 对应的前继节点找到, b为前继节点, n是前继节点的next, 
  		//  若没发生条件竞争，最终key在b与n之间 (找到的b在base_level上)
          for (Node<K,V> b = findPredecessor(key, cmp), n = b.next;;) {
              // 2.n不为null时b不是链表的最后一个节点
              if (n != null) {
                  Object v; int c;
                  //3.获取 n 的右节点
                  Node<K,V> f = n.next;
                  //4.条件竞争
  				//  并发下其他线程在b之后插入节点或直接删除节点n, break到步骤0
                  if (n != b.next)              
                      break;
                  //  若节点n已经删除, 则调用helpDelete进行帮助删除
                  if ((v = n.value) == null) {
                      n.helpDelete(b, f);
                      break;
                  }
                  //5.节点b被删除中，则break到步骤0,
  				//  调用findPredecessor帮助删除index层的数据, 
  				//  node层的数据会通过helpDelete方法进行删除
                  if (b.value == null || v == n) 
                      break;
                  //6.若key > n.key，则进行向后扫描
                  //  若key < n.key，则证明key应该存储在b和n之间
                  if ((c = cpr(cmp, key, n.key)) > 0) {
                      b = n;
                      n = f;
                      continue;
                  }
                  //7.key的值和n.key相等，则可以直接覆盖赋值
                  if (c == 0) {
                      // onlyIfAbsent默认false，
                      if (onlyIfAbsent || n.casValue(v, value)) {
                          @SuppressWarnings("unchecked") V vv = (V)v;
                          return vv;//返回被覆盖的值
                      }
                      // cas失败，返回0，重试
                      break;
                  }
                  // else c < 0; fall through
              }
              //8.此时的情况n.key > key > b.key，对应流程图1中的7
              //  创建z节点指向n
              z = new Node<K,V>(key, value, n);
              //9.尝试把b.next从n设置成z
              if (!b.casNext(n, z))
                  // cas失败，返回到步骤0，重试
                  break;
              //10.break outer后, 上面的for循环不会再执行, 而后执行下面的代码
              break outer;
          }
      }
  	// 以上插入节点已经完成，剩下的任务要根据随机数的值来表示是否向上增加层数与上层索引
      
      // 随机数
      int rnd = ThreadLocalRandom.nextSecondarySeed();
      
      //如果随机数的二进制与10000000000000000000000000000001进行与运算为0
      //即随机数的二进制最高位与最末尾必须为0，其他位无所谓，就进入该循环
      //如果随机数的二进制最高位与最末位不为0，不增加新节点的层数
      //11.判断是否需要添加level
      if ((rnd & 0x80000001) == 0) {
          //索引层level，从1开始
          int level = 1, max;
          //12.判断最低位前面有几个1，有几个leve就加几：0..0 0001 1110，这是4个，则1+4=5
          //   最大有30个就是 1 + 30
          while (((rnd >>>= 1) & 1) != 0)
              ++level;
          Index<K,V> idx = null;//最终指向z节点，就是添加的节点 
          HeadIndex<K,V> h = head;//指向头索引节点
          //13.判断level是否比当前最高索引小，图中max为3
          if (level <= (max = h.level)) {
              for (int i = 1; i <= level; ++i)
                  //根据层数level不断创建新增节点的上层索引，索引的后继索引留空
                  //第一次idx为null，也就是下层索引为空，第二次把上次的索引作为下层索引
                  idx = new Index<K,V>(z, idx, null);
              // 循环以后的索引结构
              // index-3	← idx
              //   ↓
              // index-2
              //   ↓
              // index-1
              //   ↓
              //  z-node
          }
          //14.若level > max，则只增加一层index索引层，3+1=4
          else { 
              level = max + 1;
              //创建一个index数组，长度是level+1，假设level是4，创建的数组长度为5
              @SuppressWarnings("unchecked")Index<K,V>[] idxs =
                  (Index<K,V>[])new Index<?,?>[level+1];
              //index[0]的数组slot 并没有使用，只使用 [1,level]这些数组slot了
              for (int i = 1; i <= level; ++i)
                  idxs[i] = idx = new Index<K,V>(z, idx, null);
                		// index-4   ← idx
                      //   ↓
                      // index-3
                      //   ↓
                      // index-2
                      //   ↓
                      // index-1
                      //   ↓
                      //  z-node
              
              for (;;) {
                  h = head;
                  //获取头索引的层数
                  int oldLevel = h.level;
                  // 如果level <= oldLevel，说明其他线程进行了index层增加操作，退出循环
                  if (level <= oldLevel)
                      break;
                  //定义一个新的头索引节点
                  HeadIndex<K,V> newh = h;
                  //获取头索引的节点，就是BASE_HEADER
                  Node<K,V> oldbase = h.node;
                  // 升级baseHeader索引，升高一级，并发下可能升高多级
                  for (int j = oldLevel+1; j <= level; ++j)
                      newh = new HeadIndex<K,V>(oldbase, newh, idxs[j], j);
                  // 执行完for循环之后，baseHeader 索引长这个样子..
                  // index-4             →             index-4	← idx
                  //   ↓                                  ↓
                  // index-3                           index-3     
                  //   ↓                                  ↓
                  // index-2                           index-2
                  //   ↓                                  ↓
                  // index-1                           index-1
                  //   ↓                                  ↓
                  // baseHeader    →    ....      →     z-node
                  
                  //cas成功后，map.head字段指向最新的headIndex，baseHeader的index-4s
                  if (casHead(h, newh)) {
                      //h指向最新的 index-4 节点
                      h = newh;
                      //idx指向z-node的index-3节点，
  					//因为从index-3-index-1的这些z-node索引节点 都没有插入到索引链表
                      idx = idxs[level = oldLevel];
                      break;
                  }
              }
          }
          //15.把新加的索引插入索引链表中，有上述两种情况，一种索引高度不变，另一种是高度加1
          splice: for (int insertionLevel = level;;) {
              //获取头索引的层数， 情况1是3，情况2是4
              int j = h.level;
              for (Index<K,V> q = h, r = q.right, t = idx;;) {
                  //如果头索引为null或者新增节点索引为null，退出插入索引的总循环
                  if (q == null || t == null)
                      //此处表示有其他线程删除了头索引或者新增节点的索引
                      break splice;
                  //头索引的链表后续索引存在，如果是新层则为新节点索引，如果是老层则为原索引
                  if (r != null) {
                      //获取r的节点
                      Node<K,V> n = r.node;
                      //插入的key和n.key的比较值
                      int c = cpr(cmp, key, n.key);
                      //删除空值索引
                      if (n.value == null) {
                          if (!q.unlink(r))
                              break;
                          r = q.right;
                          continue;
                      }
                      //key > n.key，向右扫描
                      if (c > 0) {
                          q = r;
                          r = r.right;
                          continue;
                      }
                  }
                  // 执行到这里，说明key < n.key，判断是否第j层插入新增节点的前置索引
                  if (j == insertionLevel) {
                      // 将新索引节点t插入q r之间
                      if (!q.link(r, t))
                          break; 
                      //如果新增节点的值为null，表示该节点已经被其他线程删除
                      if (t.node.value == null) {
                          findNode(key);
                          break splice;
                      }
                      // 插入层逐层自减，当为最底层时退出循环
                      if (--insertionLevel == 0)
                          break splice;
                  }
  				//其他节点随着插入节点的层数下移而下移
                  if (--j >= insertionLevel && j < level)
                      t = t.down;
                  q = q.down;
                  r = q.right;
              }
          }
      }
      return null;
  }
  ```

* findNode()

  ```java
  private Node<K,V> findNode(Object key) {
      //原理与doGet相同，无非是findNode返回节点，doGet返回value
      if ((c = cpr(cmp, key, n.key)) == 0)
          return n;
  }
  ```




***



##### 获取方法

* get(key)

  寻找 key 的前继节点 b (这时b.next = null || b.next > key, 则说明不存key对应的 Node)

  接着就判断 b, b.next 与 key之间的关系(其中有些 helpDelete操作)

  ```java
  public V get(Object key) {
      return doGet(key);
  }
  ```

* doGet()

  ```java
  private V doGet(Object key) {
      if (key == null)
          throw new NullPointerException();
      Comparator<? super K> cmp = comparator;
      outer: for (;;) {
          //1.找到最底层节点的前置节点
          for (Node<K,V> b = findPredecessor(key, cmp), n = b.next;;) {
              Object v; int c;
              //2.如果该前置节点的链表后续节点为null，说明不存在该节点
              if (n == null)
                  break outer;
              //b → n → f
              Node<K,V> f = n.next;
              //3.如果n不为前置节点的后续节点，表示已经有其他线程删除了该节点
              if (n != b.next) 
                  break;
              //4.如果后续节点的值为null，删除该节点
              if ((v = n.value) == null) {
                  n.helpDelete(b, f);
                  break;
              }
              //5.如果前置节点已被其他线程删除，重新循环
              if (b.value == null || v == n)
                  break;
               //6.如果要获取的key与后续节点的key相等，返回节点的value
              if ((c = cpr(cmp, key, n.key)) == 0) {
                  @SuppressWarnings("unchecked") V vv = (V)v;
                  return vv;
              }
              //7.key < n.key，说明被其他线程删除了，或者不存在该节点
              if (c < 0)
                  break outer;
              b = n;
              n = f;
          }
      }
      return null;
  }
  ```

  

****



##### 删除方法

* remove()

  ```java
  public V remove(Object key) {
      return doRemove(key, null);
  }
  final V doRemove(Object key, Object value) {
      if (key == null)
          throw new NullPointerException();
      Comparator<? super K> cmp = comparator;
      outer: for (;;) {
          //1.找到最底层目标节点的前置节点，b.key < key
          for (Node<K,V> b = findPredecessor(key, cmp), n = b.next;;) {
              Object v; int c;
              //2.如果该前置节点的链表后续节点为null，退出循环
              if (n == null)
                  break outer;
              //b → n → f
              Node<K,V> f = n.next;
              if (n != b.next)                    // inconsistent read
                  break;
              if ((v = n.value) == null) {        // n is deleted
                  n.helpDelete(b, f);
                  break;
              }
              if (b.value == null || v == n)      // b is deleted
                  break;
              //3.key < n.key，说明被其他线程删除了，或者不存在该节点
              if ((c = cpr(cmp, key, n.key)) < 0)
                  break outer;
              //4.key > n.key，继续向后扫描
              if (c > 0) {
                  b = n;
                  n = f;
                  continue;
              }
              //5.到这里是 key = n.key，value是n.value
              if (value != null && !value.equals(v))
                  break outer;
              //6.把n节点的value置空
              if (!n.casValue(v, null))
                  break;
              //7.给n添加一个删除标志mark，mark.next=f，然后把b.next设置为f，成功后n出队
              if (!n.appendMarker(f) || !b.casNext(n, f))
                  //对key对应的index进行删除
                  findNode(key);
              else {
                  //进行操作失败后通过findPredecessor中进行index的删除
                  findPredecessor(key, cmp);
                  if (head.right == null)
                      //进行headIndex 对应的index 层的删除
                      tryReduceLevel();
              }
              @SuppressWarnings("unchecked") V vv = (V)v;
              return vv;
          }
      }
      return null;
  }
  ```

  经过 findPredecessor() 中的 unlink() 后索引已经被删除

  ![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentSkipListMap-remove流程.png)

* appendMarker()

  ```java
  //添加删除标记节点
  boolean appendMarker(Node<K,V> f) {
      //通过CAS生成一个key为null，value为this，next为f的标记节点
      return casNext(f, new Node<K,V>(f));
  }
  ```

* helpDelete()

  ```java
  //将添加了删除标记的节点清除
  void helpDelete(Node<K,V> b, Node<K,V> f) {
      //this节点的后续节点为f，且本身为b的后续节点，一般都是正确的，除非被别的线程删除
      if (f == next && this == b.next) {
          //如果n还还没有被标记
          if (f == null || f.value != f) 
              casNext(f, new Node<K,V>(f));
          else
              //通过CAS，将b的下一个节点n变成f.next，即成为图中的样式
              b.casNext(this, f.next);
      }
  }
  ```

* tryReduceLevel()

  ```java
  private void tryReduceLevel() {
      HeadIndex<K,V> h = head;
      HeadIndex<K,V> d;
      HeadIndex<K,V> e;
      if (h.level > 3 &&
          (d = (HeadIndex<K,V>)h.down) != null &&
          (e = (HeadIndex<K,V>)d.down) != null &&
          e.right == null &&
          d.right == null &&
          h.right == null &&
          //设置头索引
          casHead(h, d) && 
          //重新检查
          h.right != null) 
          //重新检查返回true，说明其他线程增加了索引层级，把索引头节点设置回来
          casHead(d, h);   
  }
  ```



参考文章：https://my.oschina.net/u/3768341/blog/3135659

参考视频：https://www.bilibili.com/video/BV1Er4y1P7k1





***



### NoBlocking

#### 非阻塞队列

并发编程中，需要用到安全的队列，实现安全队列可以使用2种方式：

* 加锁，这种实现方式是阻塞队列
* 使用循环CAS算法实现，这种方式是非阻塞队列

ConcurrentLinkedQueue是一个基于链接节点的无界线程安全队列，采用先进先出的规则对节点进行排序，当添加一个元素时，会添加到队列的尾部，当获取一个元素时，会返回队列头部的元素

补充：ConcurrentLinkedDeque 是双向链表结构的无界并发队列

ConcurrentLinkedQueue使用约定：

1. 不允许null入列
2. 队列中所有未删除的节点的item都不能为null且都能从head节点遍历到
3. 删除节点是将item设置为null，队列迭代时跳过item为null节点
4. head节点跟tail不一定指向头节点或尾节点，可能存在滞后性

ConcurrentLinkedQueue由head节点和tail节点组成，每个节点（Node）由节点元素（item）和指向下一个节点的引用(next)组成，组成一张链表结构的队列

```java
private transient volatile Node<E> head;
private transient volatile Node<E> tail;

private static class Node<E> {
    volatile E item;
    volatile Node<E> next;
    //.....
}
```



***



#### 构造方法

* 无参构造方法：

  ```java
  public ConcurrentLinkedQueue() {
      // 默认情况下head节点存储的元素为空，tail节点等于head节点
      head = tail = new Node<E>(null);
  }
  ```

* 有参构造方法

  ```java
  public ConcurrentLinkedQueue(Collection<? extends E> c) {
      Node<E> h = null, t = null;
      // 遍历节点
      for (E e : c) {
          checkNotNull(e);
          Node<E> newNode = new Node<E>(e);
          if (h == null)
              h = t = newNode;
          else {
              // 单向链表
              t.lazySetNext(newNode);
              t = newNode;
          }
      }
      if (h == null)
          h = t = new Node<E>(null);
      head = h;
      tail = t;
  }
  ```



***



#### 入队方法

与传统的链表不同，单线程入队的工作流程：

* 将入队节点设置成当前队列尾节点的下一个节点
* 更新tail节点，如果tail节点的next节点不为空，则将入队节点设置成tail节点；如果tail节点的next节点为空，则将入队节点设置成tail的next节点，所以tail节点不总是尾节点，**存在滞后性**

```java
public boolean offer(E e) {
    checkNotNull(e);
    // 创建入队节点
    final Node<E> newNode = new Node<E>(e);
	
    // 循环CAS直到入队成功
    for (Node<E> t = tail, p = t;;) {
        // p用来表示队列的尾节点，初始情况下等于tail节点，q 是p的next节点
        Node<E> q = p.next;
        // 判断p是不是尾节点
        if (q == null) {
            // p是尾节点，设置p节点的下一个节点为新节点
            // 设置成功则casNext返回true，否则返回false，说明有其他线程更新过尾节点
            // 继续寻找尾节点，继续CAS
            if (p.casNext(null, newNode)) {
                // 首次添加时，p等于t，不进行尾节点更新，所以所尾节点存在滞后性
                if (p != t)
                    // 将tail设置为新入队的节点，设置失败表示其他线程更新了tail节点
                    casTail(t, newNode); 
                return true;
            }
        }
        else if (p == q)
            // 当tail不指向最后节点时，如果执行出列操作，可能将tail也移除，tail不在链表中 
        	// 此时需要对tail节点进行复位，复位到head节点
            p = (t != (t = tail)) ? t : head;
        else
            // 推动tail尾节点往队尾移动
            p = (p != t && t != (t = tail)) ? t : q;
    }
}
```

图解入队：

![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentLinkedQueue入队操作1.png)

![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentLinkedQueue入队操作2.png)

![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentLinkedQueue入队操作3.png)

当tail节点和尾节点的距离**大于等于1**时(每入队两次)更新tail，可以减少CAS更新tail节点的次数，提高入队效率

线程安全问题：

* 线程1线程2同时入队，无论从哪个位置开始并发入队，都可以循环CAS，直到入队成功，线程安全
* 线程1遍历，线程2入队，所以造成 ConcurrentLinkedQueue 的size是变化，需要加锁保证安全
* 线程1线程2同时出列，线程也是安全的



***



#### 出队方法

出队列的就是从队列里返回一个节点元素，并清空该节点对元素的引用，并不是每次出队都更新head节点

* 当head节点里有元素时，直接弹出head节点里的元素，而不会更新head节点
* 当head节点里没有元素时，出队操作才会更新head节点

**批处理方式**可以减少使用CAS更新head节点的消耗，从而提高出队效率

```java
public E poll() {
    restartFromHead:
    for (;;) {
        // p节点表示首节点，即需要出队的节点
        for (Node<E> h = head, p = h, q;;) {
            E item = p.item;
			// 如果p节点的元素不为null，则通过CAS来设置p节点引用元素为null，成功返回item
            if (item != null && p.casItem(item, null)) {
                if (p != h)
                   	// 对head进行移动
                    updateHead(h, ((q = p.next) != null) ? q : p);
                return item;
            }
           	// 如果头节点的元素为空或头节点发生了变化，这说明头节点被另外一个线程修改了
            // 那么获取p节点的下一个节点，如果p节点的下一节点为null，则表明队列已经空了
            else if ((q = p.next) == null) {
                updateHead(h, p);
                return null;
            }
      		// 第一轮操作失败，下一轮继续，调回到循环前
            else if (p == q)
                continue restartFromHead;
            // 如果下一个元素不为空，则将头节点的下一个节点设置成头节点
            else
                p = q;
        }
    }
}
final void updateHead(Node<E> h, Node<E> p) {
    if (h != p && casHead(h, p))
        // 将旧结点h的next域指向为h
        h.lazySetNext(h);
}
```

在更新完head之后，会将旧的头结点h的next域指向为h，图中所示的虚线也就表示这个节点的自引用，被移动的节点(item为null的节点)会被GC回收

![](https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentLinkedQueue出队操作1.png)

<img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentLinkedQueue出队操作2.png" style="zoom: 67%;" />

<img src="https://gitee.com/seazean/images/raw/master/Java/JUC-ConcurrentLinkedQueue出队操作3.png" style="zoom:67%;" />

如果这时，有一个线程来添加元素，通过tail获取的next节点则仍然是它本身，这就出现了**p == q** 的情况，出现该种情况之后，则会触发执行head的更新，将p节点重新指向为head

参考文章：https://www.jianshu.com/p/231caf90f30b



***



#### 成员方法

* peek()

  peek操作会改变head指向，执行peek()方法后head会指向第一个具有非空元素的节点

  ```java
  // 获取链表的首部元素，只读取而不移除
  public E peek() {
      restartFromHead:
      for (;;) {
          for (Node<E> h = head, p = h, q;;) {
              E item = p.item;
              if (item != null || (q = p.next) == null) {
                  // 更改h的位置为非空元素节点
                  updateHead(h, p);
                  return item;
              }
              else if (p == q)
                  continue restartFromHead;
              else
                  p = q;
          }
      }
  }
  ```

* size()

  用来获取当前队列的元素个数，因为整个过程都没有加锁，在并发环境中从调用size方法到返回结果期间有可能增删元素，导致统计的元素个数不精确

  ```java
  public int size() {
      int count = 0;
      // first()获取第一个具有非空元素的节点，若不存在，返回null
      // succ(p)方法获取p的后继节点，若p == p的后继节点，则返回head
      // 类似遍历链表
      for (Node<E> p = first(); p != null; p = succ(p))
          if (p.item != null)
              // 最大返回Integer.MAX_VALUE
              if (++count == Integer.MAX_VALUE)
                  break;
      return count;
  }
  ```

* remove()

  ```java
  public boolean remove(Object o) {
      // 删除的元素不能为null
      if (o != null) {
          Node<E> next, pred = null;
          for (Node<E> p = first(); p != null; pred = p, p = next) {
              boolean removed = false;
              E item = p.item;
              // 节点元素不为null
              if (item != null) {
                  // 若不匹配，则获取next节点继续匹配
                  if (!o.equals(item)) {
                      next = succ(p);
                      continue;
                  }
                  // 若匹配，则通过CAS操作将对应节点元素置为null
                  removed = p.casItem(item, null);
              }
              // 获取删除节点的后继节点
              next = succ(p);
              // 将被删除的节点移除队列
              if (pred != null && next != null) // unlink
                  pred.casNext(p, next);
              if (removed)
                  return true;
          }
      }
      return false;
  }
  ```







***







# NET

## 介绍

### 网络编程

网络编程，就是在一定的协议下，实现两台计算机的通信的技术

通信一定是基于软件结构实现的:

* C/S 结构 ：全称为Client/Server结构，是指客户端和服务器结构，常见程序有ＱＱ、IDEA等软件。
* B/S 结构 ：全称为Browser/Server结构，是指浏览器和服务器结构。

两种架构各有优势，但是无论哪种架构，都离不开网络的支持。

网络通信的三要素：

1. 协议：计算机网络客户端与服务端通信必须约定和彼此遵守的通信规则，HTTP、FTP、TCP、UDP、SMTP

2. IP地址：互联网协议地址(Internet Protocol Address)，用来给一个网络中的计算机设备做唯一的编号

   * IPv4 ：4个字节，32位组成，192.168.1.1
   * Pv6：可以实现为所有设备分配IP  128位

   * ipconfig：查看本机的IP
     ​      ping 检查本机与某个IP指定的机器是否联通，或者说是检测对方是否在线。
     ​      ping 空格 IP地址 ：ping 220.181.57.216，ping www.baidu.com

   特殊的IP地址： 本机IP地址，**127.0.0.1 == localhost**，回环测试

3. 端口：端口号就可以唯一标识设备中的进程（应用程序）
   端口号：用两个字节表示的整数，的取值范围是0-65535，0-1023之间的端口号用于一些知名的网络服务和应用，普通的应用程序需要使用1024以上的端口号。如果端口号被另外一个服务或应用所占用，会导致当前程序启动失败，报出端口被占用异常

利用**协议+IP地址+端口号** 三元组合，就可以标识网络中的进程了，那么进程间的通信就可以利用这个标识与其它进程进行交互。



参考视频：https://www.bilibili.com/video/BV1kT4y1M7vt



****



### 通信协议

网络通信协议：对计算机必须遵守的规则，只有遵守这些规则，计算机之间才能进行通信

> 应用层：应用程序（QQ,微信,浏览器），可能用到的协议（HTTP,FTP,SMTP）
>
> 传输层：TCP/IP协议 - UDP协议
>
> 网络层  ：IP协议，封装自己的IP和对方的IP和端口
>
> 数据链路层 ： 进入到硬件（网）

TCP/IP协议：传输控制协议 (Transmission Control Protocol)

TCP：面向连接的安全的可靠的传输通信协议

* 在通信之前必须确定对方在线并且连接成功才可以通信
* 例如下载文件、浏览网页等(要求可靠传输)

UDP：用户数据报协议(User Datagram Protocol)，是一个面向无连接的不可靠传输的协议

* 直接发消息给对方，不管对方是否在线，发消息后也不需要确认
* 无线（视频会议，通话），性能好，可能丢失一些数据



****



### Java模型

相关概念：

* 同步：当前线程要自己进行数据的读写操作（自己去银行取钱）
* 异步：当前线程可以去做其他事情（委托别人拿银行卡到银行取钱，然后给你）
* 阻塞：在数据没有的情况下，还是要继续等待着读（排队等待）
* 非阻塞：在数据没有的情况下，会去做其他事情，一旦有了数据再来获取（柜台取款，取个号，然后坐在椅子上做其它事，等号广播会通知你办理） 

Java中的通信模型:

1. BIO表示同步阻塞式通信，服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销，可以通过线程池机制改善。
   同步阻塞式性能极差：大量线程，大量阻塞

2. 伪异步通信：引入线程池，不需要一个客户端一个线程，实现线程复用来处理很多个客户端，线程可控。 
   高并发下性能还是很差：线程数量少，数据依然是阻塞的；数据没有来线程还是要等待

3. NIO表示**同步非阻塞IO**，服务器实现模式为请求对应一个线程，客户端发送的连接会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理

   工作原理：1个主线程专门负责接收客户端，1个线程轮询所有的客户端，发来了数据才会开启线程处理
   同步：线程还要不断的接收客户端连接，以及处理数据
   非阻塞：如果一个管道没有数据，不需要等待，可以轮询下一个管道是否有数据

4. AIO表示异步非阻塞IO，AIO 引入异步通道的概念，采用了 Proactor 模式，有效的请求才启动线程，特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用
   异步：服务端线程接收到了客户端管道以后就交给底层处理IO通信，线程可以做其他事情
   非阻塞：底层也是客户端有数据才会处理，有了数据以后处理好通知服务器应用来启动线程进行处理

各种模型应用场景：

* BIO适用于连接数目比较小且固定的架构，该方式对服务器资源要求比较高，并发局限于应用中，程序简单
* NIO适用于连接数目多且连接比较短（轻操作）的架构，如聊天服务器，并发局限于应用中，编程复杂，JDK 1.4开始支持
* AIO适用于连接数目多且连接比较长（重操作）的架构，如相册服务器，充分调用操作系统参与并发操作，编程复杂，JDK 1.7开始支持





****



## I/O

### IO模型

#### 五种模型

对于一个套接字上的输入操作，第一步是等待数据从网络中到达，当数据到达时被复制到内核中的某个缓冲区。第二步就是把数据从内核缓冲区复制到应用进程缓冲区

Linux 有五种 I/O 模型：

- 阻塞式 I/O
- 非阻塞式 I/O
- I/O 复用（select 和 poll）
- 信号驱动式 I/O（SIGIO）
- 异步 I/O（AIO）

五种模型对比：

* 同步 I/O 包括阻塞式 I/O、非阻塞式 I/O、I/O 复用和信号驱动 I/O ，它们的主要区别在第一个阶段，非阻塞式 I/O 、信号驱动 I/O 和异步 I/O 在第一阶段不会阻塞

- 同步 I/O：将数据从内核缓冲区复制到应用进程缓冲区的阶段（第二阶段），应用进程会阻塞
- 异步 I/O：第二阶段应用进程不会阻塞



***



#### 阻塞式IO

应用进程通过系统调用 recvfrom 接收数据，会被阻塞，直到数据从内核缓冲区复制到应用进程缓冲区中才返回。阻塞不意味着整个操作系统都被阻塞，其它应用进程还可以执行，只是当前阻塞进程不消耗 CPU 时间，这种模型的 CPU 利用率会比较高

recvfrom() 用于接收 Socket 传来的数据，并复制到应用进程的缓冲区 buf 中，把 recvfrom() 当成系统调用

![](https://gitee.com/seazean/images/raw/master/Java/IO模型-阻塞式IO.png)



***



#### 非阻塞式

应用进程通过 recvfrom 调用不停的去和内核交互，直到内核准备好数据。如果没有准备好数据，内核返回一个错误码，过一段时间应用进程再执行 recvfrom 系统调用，在两次发送请求的时间段，进程可以进行其他任务，这种方式称为轮询（polling）

由于 CPU 要处理更多的系统调用，因此这种模型的 CPU 利用率比较低

![](https://gitee.com/seazean/images/raw/master/Java/IO模型-非阻塞式IO.png)



***



#### 信号驱动

应用进程使用 sigaction 系统调用，内核立即返回，应用进程可以继续执行，等待数据阶段应用进程是非阻塞的。当内核数据准备就绪时向应用进程发送 SIGIO 信号，应用进程收到之后在信号处理程序中调用 recvfrom 将数据从内核复制到应用进程中

相比于非阻塞式 I/O 的轮询方式，信号驱动 I/O 的 CPU 利用率更高

![](https://gitee.com/seazean/images/raw/master/Java/IO模型-信号驱动IO.png)



***



#### IO复用

IO 复用模型使用 select 或者 poll 函数等待数据，select 会监听所有注册好的 IO，等待多个套接字中的任何一个变为可读，等待过程会被**阻塞**，当某个套接字准备好数据变为可读时 select 调用就返回，然后调用 recvfrom 把数据从内核复制到进程中

IO 复用让单个进程具有处理多个 I/O 事件的能力，又被称为 Event Driven I/O，即事件驱动 I/O

如果一个 Web 服务器没有 I/O 复用，那么每一个 Socket 连接都要创建一个线程去处理，如果同时有几万个连接，就需要创建相同数量的线程。相比于多进程和多线程技术，I/O 复用不需要进程线程创建和切换的开销，系统开销更小

![](https://gitee.com/seazean/images/raw/master/Java/IO模型-IO复用模型.png)



***



#### 异步IO

应用进程执行 aio_read 系统调用会立即返回，给内核传递描述符、缓冲区指针、缓冲区大小等。应用进程可以继续执行不会被阻塞，内核会在所有操作完成之后向应用进程发送信号。

异步 I/O 与信号驱动 I/O 的区别在于，异步 I/O 的信号是通知应用进程 I/O 完成，而信号驱动 I/O 的信号是通知应用进程可以开始 I/O

![](https://gitee.com/seazean/images/raw/master/Java/IO模型-异步IO模型.png)



****



### 多路复用

#### select

##### 函数

socket 不是文件，只是一个标识符，但是 Unix 操作系统把所有东西都**看作**是文件，所以 socket 说成 file descriptor，也就是 fd

select 允许应用程序监视一组文件描述符，等待一个或者多个描述符成为就绪状态，从而完成 I/O 操作。

```c
int select(int n, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct timeval *timeout);
```

- fd_set 使用 **bitmap 数组**实现，数组大小用 FD_SETSIZE 定义，只能监听少于 FD_SETSIZE 数量的描述符，32位机默认是 1024 个，64位机默认是 2048，可以对进行修改，然后重新编译内核

- fd_set 有三种类型的描述符：readset、writeset、exceptset，对应读、写、异常条件的描述符集合

- n 是监测的 socket 的最大数量

- timeout 为超时参数，调用 select 会一直阻塞直到有描述符的事件到达或者等待的时间超过 timeout

  ```c
  struct timeval{
      long tv_sec; //秒
      long tv_usec;//微秒
  }
  ```

  timeout == null：等待无限长的时间
  tv_sec == 0 && tv_usec == 0：获取后直接返回，不阻塞等待
  tv_sec != 0 || tv_usec != 0：等待指定时间

- 方法成功调用返回结果为就绪的文件描述符个数，出错返回结果为 -1，超时返回结果为 0

Linux 提供了一组宏为 fd_set 进行赋值操作：

```c
int FD_ZERO(fd_set *fdset);			// 将一个fd_set类型变量的所有值都置为0
int FD_CLR(int fd, fd_set *fdset);	// 将一个fd_set类型变量的fd位置为0
int FD_SET(int fd, fd_set *fdset);	// 将一个fd_set类型变量的fd位置为1
int FD_ISSET(int fd, fd_set *fdset);// 判断fd位是否被置为1
```

示例：

```c
sockfd = socket(AF_INET, SOCK_STREAM, 0);
memset(&addr, 0, sizeof(addr)));
addr.sin_family = AF_INET;
addr.sin_port = htons(2000);
addr.sin_addr.s_addr = INADDR_ANY;
bind(sockfd, (struct sockaddr*)&addr, sizeof(addr));//绑定连接
listen(sockfd, 5);//监听5个端口
for(i = 0; i < 5; i++) {
	memset(&client, e, sizeof(client));
    addrlen = sizeof(client);
	fds[i] = accept(sockfd, (struct sockaddr*)&client, &addrlen);
    //将监听的对应的文件描述符fd存入fds：[3,4,5,6,7]
    if(fds[i] > max)
		max = fds[i];
}
while(1) {
    FD_ZERO(&rset);//置为0
    for(i = 0; i < 5; i++) {
    	FD_SET(fds[i], &rset);//对应位置1 [0001 1111 00.....]
	}
	print("round again");
	select(max + 1, &rset, NULL, NULL, NULL);//监听
    
	for(i = 0; i <5; i++) {
        if(FD_ISSET(fds[i], &rset)) {//判断监听哪一个端口
            memset(buffer, 0, MAXBUF);
            read(fds[i], buffer, MAXBUF);//进入内核态读数据
            print(buffer);
        }
    }
}
```



参考视频：https://www.bilibili.com/video/BV19D4y1o797



****



##### 流程

select 调用流程图：

![](https://gitee.com/seazean/images/raw/master/Java/IO-select调用过程.png)

1. 使用 copy_from_user 从用户空间拷贝 fd_set 到内核空间，进程阻塞
2. 注册回调函数 _pollwait
3. 遍历所有 fd，调用其对应的 poll 方法（对于 socket，这个 poll 方法是 sock_poll，sock_poll 根据情况会调用到 tcp_poll、udp_poll 或者 datagram_poll），以 tcp_poll 为例，其核心实现就是 _pollwait
4. _pollwait 就是把 current（当前进程）挂到设备的等待队列，不同设备有不同的等待队列，对于 tcp_poll ，其等待队列是 sk → sk_sleep（把进程挂到等待队列中并不代表进程已经睡眠），在设备收到消息（网络设备）或填写完文件数据（磁盘设备）后，会唤醒设备等待队列上睡眠的进程，这时 current 便被唤醒
5. poll 方法返回时会返回一个描述读写操作是否就绪的 mask 掩码，根据这个 mask 掩码给 fd_set 赋值
6. 如果遍历完所有的 fd，还没有返回一个可读写的 mask 掩码，则会调用 schedule_timeout 让调用 select 的进程（就是current）进入睡眠。当设备驱动发生自身资源可读写后，会唤醒其等待队列上睡眠的进程，如果超过一定的超时时间（schedule_timeout指定），没有其他线程唤醒，则调用 select 的进程会重新被唤醒获得 CPU，进而重新遍历 fd，判断有没有就绪的 fd
7. 把 fd_set 从内核空间拷贝到用户空间，阻塞进程继续执行



参考文章：https://www.cnblogs.com/anker/p/3265058.html

其他流程图：https://www.processon.com/view/link/5f62b9a6e401fd2ad7e5d6d1



****



#### poll

poll 的功能与 select 类似，也是等待一组描述符中的一个成为就绪状态

```c
int poll(struct pollfd *fds, unsigned int nfds, int timeout);
```

poll 中的描述符是 pollfd 类型的数组，pollfd 的定义如下：

```c
struct pollfd {
    int   fd;         /* file descriptor */
    short events;     /* requested events */
    short revents;    /* returned events */
};
```

select 和 poll 对比：

- select 会修改描述符，而 poll 不会
- select 的描述符类型使用数组实现，有描述符的限制；而 poll 使用**链表**实现，没有描述符数量的限制
- poll 提供了更多的事件类型，并且对描述符的重复利用上比 select 高

* select 和 poll 速度都比较慢，每次调用都需要将全部描述符数组 fd 从应用进程缓冲区复制到内核缓冲区，同时每次都需要在内核遍历传递进来的所有 fd ，这个开销在 fd 很多时会很大
* 几乎所有的系统都支持 select，但是只有比较新的系统支持 poll
* select 和 poll 的时间复杂度 O(n)，对 socket 进行扫描时是线性扫描，即采用轮询的方法，效率较低，因为并不知道具体是哪个 socket 具有事件，所以随着 FD 的增加会造成遍历速度慢的**线性下降**性能问题
* poll 还有一个特点是水平触发，如果报告了 fd 后，没有被处理，那么下次 poll 时会再次报告该 fd
* 如果一个线程对某个描述符调用了 select 或者 poll，另一个线程关闭了该描述符，会导致调用结果不确定



参考文章：https://github.com/CyC2018/CS-Notes/blob/master/notes/Socket.md



****



#### epoll

##### 函数

epoll 使用事件的就绪通知方式，通过 epoll_ctl() 向内核注册新的描述符或者是改变某个文件描述符的状态。已注册的描述符在内核中会被维护在一棵**红黑树**上，一旦该 fd 就绪，内核通过 callback 回调函数将 I/O 准备好的描述符加入到一个**链表**中管理，进程调用 epoll_wait() 便可以得到事件完成的描述符

```c
int epoll_create(int size);
int epoll_ctl(int epfd, int op, int fd, struct epoll_event *event)；
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);
```

* epall_create：一个系统函数，函数将在内核空间内创建一个 epoll 数据结构，可以理解为 epoll 结构空间，返回值为 epoll 的文件描述符编号，后面当有client连接时，向该 epoll 区中添加监听，所以 epoll 使用一个文件描述符管理多个描述符

* epall_ctl：epoll 的事件注册函数，select 函数是调用时指定需要监听的描述符和事件，epoll 先将用户感兴趣的描述符事件注册到 epoll 空间。此函数是非阻塞函数，用来增删改 epoll 空间内的描述符，参数解释：

  * epfd：epoll 结构的进程 fd 编号，函数将依靠该编号找到对应的 epoll 结构

  * op：表示当前请求类型，有三个宏定义：

    * EPOLL_CTL_ADD：注册新的 fd 到 epfd 中
    * EPOLL_CTL_MOD：修改已经注册的 fd 的监听事件
    * EPOLL_CTI_DEL：从 epfd 中删除一个 fd

  * fd：需要监听的文件描述符，一般指 socket_fd

  * event：告诉内核对该 fd 资源感兴趣的事件，epoll_event 的结构：

    ```c
    struct epoll_event {
        _uint32_t events;	/*epoll events*/
        epoll_data_t data;	/*user data variable*/
    }
    ```

    events 可以是以下几个宏集合：EPOLLIN、EPOLOUT、EPOLLPRI、EPOLLERR、EPOLLHUP（挂断）、EPOLET（边缘触发）、EPOLLONESHOT（只监听一次，事件触发后自动清除该 fd，从 epoll 列表）

* epoll_wait：等待事件的产生，类似于 select() 调用，返回值为本次就绪的 fd 个数

  * epfd：指定感兴趣的 epoll 事件列表
  * events：指向一个 epoll_event 结构数组，当函数返回时，内核会把就绪状态的数据拷贝到该数组
  * maxevents：标明 epoll_event 数组最多能接收的数据量，即本次操作最多能获取多少就绪数据
  * timeout：单位为毫秒
    * 0：表示立即返回，非阻塞调用
    * -1：阻塞调用，直到有用户感兴趣的事件就绪为止
    * 大于0：阻塞调用，阻塞指定时间内如果有事件就绪则提前返回，否则等待指定时间后返回

epoll 的描述符事件有两种触发模式：LT（level trigger）和 ET（edge trigger）：

* LT 模式：当 epoll_wait() 检测到描述符事件到达时，将此事件通知进程，进程可以不立即处理该事件，下次调用 epoll_wait() 会再次通知进程。是默认的一种模式，并且同时支持 Blocking 和 No-Blocking
* ET 模式：通知之后进程必须立即处理事件，下次再调用 epoll_wait() 时不会再得到事件到达的通知。减少了 epoll 事件被重复触发的次数，因此效率要比 LT 模式高；只支持 No-Blocking，以避免由于一个文件的阻塞读/阻塞写操作把处理多个文件描述符的任务饥饿

```c
// 创建 epoll 描述符，每个应用程序只需要一个，用于监控所有套接字
int pollingfd = epoll_create(0xCAFE);
if ( pollingfd < 0 )// report error
// 初始化 epoll 结构
struct epoll_event ev = { 0 };

// 将连接类实例与事件相关联，可以关联任何想要的东西
ev.data.ptr = pConnection1;

// 监视输入，并且在事件发生后不自动重新准备描述符
ev.events = EPOLLIN | EPOLLONESHOT;
// 将描述符添加到监控列表中，即使另一个线程在epoll_wait中等待，描述符将被正确添加
if ( epoll_ctl( epollfd, EPOLL_CTL_ADD, pConnection1->getSocket(), &ev) != 0 )
    // report error

// 最多等待 20 个事件
struct epoll_event pevents[20];

// 等待10秒，检索20个并存入epoll_event数组
int ready = epoll_wait(pollingfd, pevents, 20, 10000);
// 检查epoll是否成功
if ( ret == -1)// report error and abort
else if ( ret == 0)// timeout; no event detected
else
{
    for (int i = 0; i < ready; i+ )
    {
        if ( pevents[i].events & EPOLLIN )
        {
            // 获取连接指针
            Connection * c = (Connection*) pevents[i].data.ptr;
            c->handleReadEvent();
         }
    }
}
```



流程图：https://gitee.com/seazean/images/blob/master/Java/IO-epoll%E5%8E%9F%E7%90%86%E5%9B%BE.jpg

图片来源：https://www.processon.com/view/link/5f62f98f5653bb28eb434add

参考视频：https://www.bilibili.com/video/BV19D4y1o797

参考文章：https://github.com/CyC2018/CS-Notes/blob/master/notes/Socket.md



***



##### 特点

epoll 的特点：

* epoll 仅适用于 Linux 系统
* epoll 使用一个文件描述符管理多个描述符，将用户关系的文件描述符的事件存放到内核的一个事件表中
* 没有最大描述符数量（并发连接）的限制，打开 fd 的上限远大于1024（1G 内存能监听约10万个端口）
* epoll 的时间复杂度 O(1)，epoll 理解为 event poll，不同于忙轮询和无差别轮询，调用 epoll_wait **只是轮询就绪链表**。当监听列表有设备就绪时调用回调函数，把就绪 fd 放入就绪链表中，并唤醒在 epoll_wait 中阻塞的进程，所以 epoll 实际上是**事件驱动**（每个事件关联上fd）的，降低了 system call 的时间复杂度
* epoll 内核中根据每个 fd 上的 callback 函数来实现，只有活跃的 socket 才会主动调用 callback，所以使用 epoll 没有前面两者的线性下降的性能问题，效率提高

* epoll 每次注册新的事件到 epoll 句柄中时，会把所有的 fd 拷贝进内核，但不是每次 epoll_wait 的时重复拷贝，对比前面两种，epoll 只需要将描述符从进程缓冲区向内核缓冲区拷贝一次。 epoll 也可以利用 mmap() 文件映射内存加速与内核空间的消息传递，减少复制开销
* 前面两者要把 current 往设备等待队列中挂一次，epoll 也只把 current 往等待队列上挂一次，但是这里的等待队列并不是设备等待队列，只是一个 epoll 内部定义的等待队列，这样节省不少的开销
* epoll 对多线程编程更有友好，一个线程调用了 epoll_wait() 另一个线程关闭了同一个描述符，也不会产生像 select 和 poll 的不确定情况



参考文章：https://www.jianshu.com/p/dfd940e7fca2

参考文章：https://www.cnblogs.com/anker/p/3265058.html



***



#### 应用

应用场景： 

* select 应用场景：
  * select 的 timeout 参数精度为微秒，而 poll 和 epoll 为毫秒，因此 select 更加适用于实时性要求比较高的场景，比如核反应堆的控制
  * select 可移植性更好，几乎被所有主流平台所支持

* poll 应用场景：poll 没有最大描述符数量的限制，适用于平台支持并且对实时性要求不高的情况

* epoll 应用场景：
  * 运行在 Linux 平台上，有大量的描述符需要同时轮询，并且这些连接最好是长连接
  * 需要同时监控小于 1000 个描述符，没必要使用 epoll，因为这个应用场景下并不能体现 epoll 的优势
  * 需要监控的描述符状态变化多，而且都是非常短暂的，也没有必要使用 epoll。因为 epoll 中的所有描述符都存储在内核中，造成每次需要对描述符的状态改变都需要通过 epoll_ctl() 进行系统调用，频繁系统调用降低效率，并且 epoll 的描述符存储在内核，不容易调试



参考文章：https://github.com/CyC2018/CS-Notes/blob/master/notes/Socket.md



****



### 系统调用

#### 内核态

用户空间：用户代码、用户堆栈

内核空间：内核代码、内核调度程序、进程描述符（内核堆栈、thread_info 进程描述符）

* 进程描述符和用户的进程是一一对应的
* SYS_API 系统调用：如 read、write，系统调用就是 0X80 中断
* 进程描述符 pd：进程从用户态切换到内核态时，需要保存用户态时的上下文信息，
* 线程上下文：用户程序基地址，程序计数器、cpu cache、寄存器等，方便程序切回用户态时恢复现场
* 内核堆栈：系统调用函数也是要创建变量的，这些变量在内核堆栈上分配

![](https://gitee.com/seazean/images/raw/master/Java/IO-用户态和内核态.png)



***



#### 80中断

在用户程序中调用操作系统提供的核心态级别的子功能，为了系统安全需要进行用户态和内核态转换，状态的转换需要进行 CPU 中断，中断分为硬中断和软中断：

* 硬中断：如网络传输中，数据到达网卡后，网卡经过一系列操作后发起硬件中断
* 软中断：如程序运行过程中本身产生的一些中断
  - 发起 `0X80` 中断
  - 程序执行碰到除 0 异常

系统调用 system_call 函数所对应的中断指令编号是 0X80（十进制是 8×16=128），而该指令编号对应的就是系统调用程序的入口，所以称系统调用为 80 中断

系统调用的流程：

* 在 CPU 寄存器里存一个系统调用号，表示哪个系统函数，比如 read
* 将 CPU 的临时数据都保存到 thread_info 中
* 执行 80 中断处理程序，找到刚刚存的系统调用号（read），先检查缓存中有没有对应的数据，没有就去磁盘中加载到内核缓冲区，然后从内核缓冲区拷贝到用户空间
* 最后恢复到用户态，通过 thread_info 恢复现场，用户态继续执行

![](https://gitee.com/seazean/images/raw/master/Java/IO-系统调用的过程.jpg)





****



### 零拷贝

#### DMA

DMA (Direct Memory Access) ：直接存储器访问，让外部设备不通过 CPU 直接与系统内存交换数据的接口技术

作用：可以解决批量数据的输入/输出问题，使数据的传送速度取决于存储器和外设的工作速度

把内存数据传输到网卡然后发送：

* 没有 DMA：CPU 读内存数据到 CPU 高速缓存，再写到网卡，这样就把 CPU 的速度拉低到和网卡一个速度
* 使用 DMA：把内存数据读到 socket 内核缓存区（CPU复制），CPU 分配给 DMA 开始**异步**操作，DMA 读取 socket 缓冲区到 DMA 缓冲区，然后写到网卡。DMA 执行完后中断 CPU，这时 socket 内核缓冲区为空，CPU 从用户态切换到内核态，执行中断处理程序，将需要使用 socket 缓冲区的阻塞进程移到运行队列

一个完整的 DMA 传输过程必须经历 DMA 请求、DMA 响应、DMA 传输、DMA 结束四个步骤：

<img src="https://gitee.com/seazean/images/raw/master/Java/IO-DMA.png" style="zoom: 50%;" />

DMA 方式是一种完全由硬件进行组信息传送的控制方式，通常系统总线由 CPU 管理，在 DMA 方式中，CPU 的主存控制信号被禁止使用，CPU 把总线（地址总线、数据总线、控制总线）让出来由 DMA 控制器接管，用来控制传送的字节数、判断 DMA 是否结束、以及发出 DMA 结束信号，所以 DMA 控制器必须有以下功能：

* 接受外设发出的 DMA 请求，并向 CPU 发出总线接管请求
* 当 CPU 发出允许接管信号后，进入 DMA 操作周期
* 确定传送数据的主存单元地址及长度，并自动修改主存地址计数和传送长度计数
* 规定数据在主存和外设间的传送方向，发出读写等控制信号，执行数据传送操作
* 判断 DMA 传送是否结束，发出 DMA 结束信号，使 CPU 恢复正常工作状态（中断）



***



#### BIO

传统的 I/O 操作进行了 4 次用户空间与内核空间的上下文切换，以及 4 次数据拷贝：

* JVM 发出 read() 系统调用，OS 上下文切换到内核模式（切换1）并将数据从网卡或硬盘等通过 DMA 读取到内核空间缓冲区（拷贝1）
* OS 内核将数据复制到用户空间缓冲区（拷贝2），然后 read 系统调用返回，又会导致一次内核空间到用户空间的上下文切换（切换2）
* JVM 处理代码逻辑并发送 write() 系统调用，OS 上下文切换到内核模式（切换3）并从用户空间缓冲区复制数据到内核空间缓冲区（拷贝3）
* write 系统调用返回，导致内核空间到用户空间的再次上下文切换（切换4），将内核空间缓冲区中的数据写到 hardware（拷贝4）

流程图中的箭头反过来也成立，可以从网卡获取数据

![](https://gitee.com/seazean/images/raw/master/Java/IO-BIO工作流程.png)

read 调用图示：read、write 都是系统调用指令

<img src="https://gitee.com/seazean/images/raw/master/Java/IO-缓冲区读写.png" style="zoom: 67%;" />



***



#### mmap

mmap（Memory Mapped Files）加 write 实现零拷贝，**零拷贝就是没有数据从内核空间复制到用户空间**

用户空间和内核空间都使用内存，所以可以共享同一块物理内存地址，省去用户态和内核态之间的拷贝。写网卡时，共享空间的内容拷贝到 socket 缓冲区，然后交给 DMA 发送到网卡，只需要 3 次复制

进行了 4 次用户空间与内核空间的上下文切换，以及 3 次数据拷贝（2 次 DMA，一次 CPU 复制）：

* 发出 mmap 系统调用，DMA 拷贝到内核缓冲区；mmap系统调用返回，无需拷贝
* 发出 write 系统调用，将数据从内核缓冲区拷贝到内核 socket 缓冲区；write系统调用返回，DMA 将内核空间 socket 缓冲区中的数据传递到协议引擎

![](https://gitee.com/seazean/images/raw/master/Java/IO-mmap工作流程.png)

原理：利用操作系统的 Page 来实现文件到物理内存的直接映射，完成映射后对物理内存的操作会**被同步**到硬盘上

缺点：不可靠，写到 mmap 中的数据并没有被真正的写到硬盘，操作系统会在程序主动调用 flush 的时候才把数据真正的写到硬盘

Java NIO 提供了 **MappedByteBuffer** 类可以用来实现 mmap 内存映射，MappedByteBuffer 类对象**只能**通过调用 `FileChannel.map()` 获取



****



#### sendfile

sendfile 实现零拷贝，打开文件的文件描述符 fd 和 socket 的 fd 传递给 sendfile，然后经过 3 次复制和 2 次用户态和内核态的切换

原理：数据根本不经过用户态，直接从内核缓冲区进入到 Socket Buffer，由于和用户态完全无关，就减少了两次上下文切换

![](https://gitee.com/seazean/images/raw/master/Java/IO-sendfile工作流程.png)

sendfile2.4 之后，sendfile 实现了更简单的方式，文件到达内核缓冲区后，不必再将数据全部复制到 socket buffer 缓冲区，而是只**将记录数据位置和长度相关等描述符信息**保存到 socket buffer，DMA 根据 socket 缓冲区中描述符提供的位置和偏移量信息直接将内核空间缓冲区中的数据拷贝到协议引擎上（2 次复制 2 次切换）

Java NIO 对 sendfile 的支持是 `FileChannel.transferTo()/transferFrom()`，把磁盘文件读取 OS 内核缓冲区后的 fileChannel，直接转给 socketChannel 发送，底层就是 sendfile



参考文章：https://blog.csdn.net/hancoder/article/details/112149121



***



## Inet

一个该 InetAddress 类的对象就代表一个IP地址对象

成员方法：
`static InetAddress getLocalHost()` : 获得本地主机IP地址对象
`static InetAddress getByName(String host)` : 根据IP地址字符串或主机名获得对应的IP地址对象
`String getHostName()` : 获取主机名
`String getHostAddress()` : 获得IP地址字符串

```java
public class InetAddressDemo {
    public static void main(String[] args) throws Exception {
        // 1.获取本机地址对象
        InetAddress ip = InetAddress.getLocalHost();
        System.out.println(ip.getHostName());//DESKTOP-NNMBHQR
        System.out.println(ip.getHostAddress());//192.168.11.1
        // 2.获取域名ip对象
        InetAddress ip2 = InetAddress.getByName("www.baidu.com");
        System.out.println(ip2.getHostName());//www.baidu.com
        System.out.println(ip2.getHostAddress());//14.215.177.38
        // 3.获取公网IP对象。
        InetAddress ip3 = InetAddress.getByName("182.61.200.6");
        System.out.println(ip3.getHostName());//182.61.200.6
        System.out.println(ip3.getHostAddress());//182.61.200.6
        
        // 4.判断是否能通： ping  5s之前测试是否可通
        System.out.println(ip2.isReachable(5000)); // ping百度
    }
}
```



***



## UDP

### 基本介绍

UDP（User Datagram Protocol）协议的特点：

* 面向无连接的协议
* 发送端只管发送，不确认对方是否能收到
* 基于数据包进行数据传输
* 发送数据的包的大小限制**64KB**以内
* 因为面向无连接，速度快，但是不可靠，会丢失数据

UDP协议的使用场景：在线视频、网络语音、电话



***



### 实现UDP

UDP协议相关的两个类

* DatagramPacket（数据包对象）：用来封装要发送或要接收的数据，比如：集装箱
* DatagramSocket（发送对象）：用来发送或接收数据包，比如：码头

**DatagramPacket**：

* DatagramPacket类

  `public new DatagramPacket(byte[] buf, int length, InetAddress address, int port)` : 创建发送端数据包对象，参数： 

  * buf：要发送的内容，字节数组
  * length：要发送内容的长度，单位是字节
  * address：接收端的IP地址对象
  * port：接收端的端口号

  `public new DatagramPacket(byte[] buf, int length)` : 创建接收端的数据包对象，参数：

  * buf：用来存储接收到内容		
  * length：能够接收内容的长度

* DatagramPacket 类常用方法
  `public int getLength()` : 获得实际接收到的字节个数
  `public byte[] getData()` : 返回数据缓冲区

**DatagramSocket**：

* DatagramSocket类构造方法
  `protected DatagramSocket()` : 创建发送端的Socket对象，系统会随机分配一个端口号
  `protected DatagramSocket(int port)` : 创建接收端的Socket对象并指定端口号
* DatagramSocket类成员方法
  `public void send(DatagramPacket dp)` : 发送数据包
  `public void receive(DatagramPacket p)` : 接收数据包
  `public void close()` : 关闭数据报套接字

```java
public class UDPClientDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("===启动客户端===");
        // 1.创建一个集装箱对象，用于封装需要发送的数据包!
        byte[] buffer = "我学Java".getBytes();
        DatagramPacket packet = new DatagramPacket(buffer,bubffer.length
											,InetAddress.getLoclHost,8000);
        // 2.创建一个码头对象
        DatagramSocket socket = new DatagramSocket();
        // 3.开始发送数据包对象
        socket.send(packet);
        socket.close();
    }
}
public class UDPServerDemo{
    public static void main(String[] args) throws Exception {
        System.out.println("==启动服务端程序==");
        // 1.创建一个接收客户都端的数据包对象（集装箱）
        byte[] buffer = new byte[1024*64];
        DatagramPacket packet = new DatagramPacket(buffer,bubffer.length);
        // 2.创建一个接收端的码头对象
        DatagramSocket socket = new DatagramSocket(8000);
        // 3.开始接收
        socket.receive(packet);
        // 4.从集装箱中获取本次读取的数据量
        int len = packet.getLength();
        // 5.输出数据
        //String rs = new String(socket.getData(), 0, len)
        String rs = new String(buffer , 0 , len);
        System.out.println(rs);
        // 6.服务端还可以获取发来信息的客户端的IP和端口。
        String ip = packet.getAddress().getHostAdress();
        int port = packet.getPort();
        socket.close();
    }
}
```



***



### 通讯方式

UDP通信方式：

+ 单播：用于两个主机之间的端对端通信

+ 组播：用于对一组特定的主机进行通信
  IP : 224.0.1.0
  Socket对象 : MulticastSocket

+ 广播：用于一个主机对整个局域网上所有主机上的数据通信
  IP : 255.255.255.255
  Socket对象 : DatagramSocket

  

***



## TCP

### 基本介绍

TCP/IP协议 ==> Transfer Control Protocol ==> 传输控制协议

TCP/IP协议的特点：

* 面向连接的协议
* 只能由客户端主动发送数据给服务器端，服务器端接收到数据之后，可以给客户端响应数据
* 通过**三次握手**建立连接，连接成功形成数据传输通道；通过**四次挥手**断开连接
* 基于IO流进行数据传输
* 传输数据大小没有限制
* 因为面向连接的协议，速度慢，但是是可靠的协议。

TCP协议的使用场景：文件上传和下载、邮件发送和接收、远程登录

注意：**TCP不会为没有数据的ACK超时重传**

<img src="https://gitee.com/seazean/images/raw/master/Java/三次握手.png" alt="三次握手" style="zoom: 50%;" />

<img src="https://gitee.com/seazean/images/raw/master/Java/四次挥手.png" alt="四次挥手" style="zoom: 67%;" />



***



### Socket

TCP通信也叫**Socket网络编程**，只要代码基于Socket开发，底层就是基于了可靠传输的TCP通信

TCP协议相关的类：

* Socket：一个该类的对象就代表一个客户端程序。
* ServerSocket：一个该类的对象就代表一个服务器端程序。

Socket类

* 构造方法：
  `Socket(InetAddress address,int port)` : 创建流套接字并将其连接到指定IP指定端口号
  `Socket(String host, int port)` : 根据ip地址字符串和端口号创建客户端Socket对象
  注意事项：执行该方法，就会立即连接指定的服务器，连接成功，则表示三次握手通过，反之抛出异常
* 常用API：
  `OutputStream getOutputStream()` : 获得字节输出流对象
  `InputStream getInputStream()` : 获得字节输入流对象
  `void shutdownInput()` : 停止接受
  `void shutdownOutput()` : 停止发送数据，终止通信
  `SocketAddress getRemoteSocketAddress() `: 返回套接字连接到的端点的地址，未连接返回null

ServerSocket类：

* 构造方法：`public ServerSocket(int port)`
* 常用API：`public Socket accept()`，**阻塞等待**接收一个客户端的Socket管道连接请求，连接成功返回一个Socket对象

相当于客户端和服务器建立一个数据管道，管道一般不用close



***



### 实现TCP

#### 开发流程

客户端的开发流程：

1. 客户端要请求于服务端的socket管道连接
2. 从socket通信管道中得到一个字节输出流
3. 通过字节输出流给服务端写出数据

服务端的开发流程：

1. 用ServerSocket注册端口
2. 接收客户端的Socket管道连接
3. 从socket通信管道中得到一个字节输入流
4. 从字节输入流中读取客户端发来的数据

![](https://gitee.com/seazean/images/raw/master/Java/BIO工作机制.png)

![](https://gitee.com/seazean/images/raw/master/Java/TCP-工作模型.png)

* 如果输出缓冲区空间不够存放主机发送的数据，则会被阻塞，输入缓冲区同理
* 缓冲区不属于应用程序，属于内核
* TCP从输出缓冲区读取数据会加锁阻塞线程



***



#### BIO通信

需求一：客户端发送一行数据，服务端接收一行数据

````java
public class ClientDemo {
    public static void main(String[] args) throws Exception {
        // 1.客户端要请求于服务端的socket管道连接。
        Socket socket = new Socket("127.0.0.1",8080);
        // 2.从socket通信管道中得到一个字节输出流
        OutputStream os = new socket.getOutputStream();
        // 3.把低级的字节输出流包装成高级的打印流。
        PrintStream ps = new PrintStream(os);
        // 4.开始发消息出去
        ps.println("我是客户端");
        ps.flush();//一般不关闭IO流
        System.out.println("客户端发送完毕~~~~");
    }
}
public class ServerDemo{
    public static void main(String[] args) throws Exception {
        System.out.println("----服务端启动----");
        // 1.注册端口: public ServerSocket(int port)
        ServerSocket serverSocket = new ServerSocket(8080);
        // 2.开始等待接收客户端的Socket管道连接。
        Socket socket = serverSocket.accept();
        // 3.从socket通信管道中得到一个字节输入流。
        InputStream is = socket.getInputStream();
        // 4.把字节输入流转换成字符输入流
        BufferedReader br = new BufferedReader(new InputStreamReader(is));
        // 6.按照行读取消息 。
        String line;
        if((line = br.readLine()) != null){
            System.out.println(line);
        }
    }
}
````



需求二：客户2端可以反复发送数据，服务端可以反复数据2

```java
public class ClientDemo {
    public static void main(String[] args) throws Exception {
        // 1.客户端要请求于服务端的socket管道连接。
        Socket socket = new Socket("127.0.0.1",8080);
        // 2.从socket通信管道中得到一个字节输出流
        OutputStream os = new socket.getOutputStream();
        // 3.把低级的字节输出流包装成高级的打印流。
        PrintStream ps = new PrintStream(os);
        // 4.开始发消息出去
         while(true){
            Scanner sc = new Scanner(System.in);
            System.out.print("请说：");
            ps.println(sc.nextLine());
            ps.flush();
        }
    }
}
public class ServerDemo{
    public static void main(String[] args) throws Exception {
        System.out.println("----服务端启动----");
        // 1.注册端口: public ServerSocket(int port)
        ServerSocket serverSocket = new ServerSocket(8080);
        // 2.开始等待接收客户端的Socket管道连接。
        Socket socket = serverSocket.accept();
        // 3.从socket通信管道中得到一个字节输入流。
        InputStream is = socket.getInputStream();
        // 4.把字节输入流转换成字符输入流
        BufferedReader br = new BufferedReader(new InputStreamReader(is));
        // 6.按照行读取消息 。
        String line;
        while((line = br.readLine()) != null){
            System.out.println(line);
        }
    }
}
```



需求三：实现一个服务端可以同时接收多个客户端的消息。

```java
public class ClientDemo {
    public static void main(String[] args) throws Exception {
        Socket socket = new Socket("127.0.0.1",8080);
        OutputStream os = new socket.getOutputStream();
        PrintStream ps = new PrintStream(os);
		while(true){
            Scanner sc = new Scanner(System.in);
            System.out.print("请说：");
            ps.println(sc.nextLine());
            ps.flush();
        }
    }
}
public class ServerDemo{
    public static void main(String[] args) throws Exception {
        System.out.println("----服务端启动----");
        ServerSocket serverSocket = new ServerSocket(8080);
        while(true){
            // 开始等待接收客户端的Socket管道连接。
             Socket socket = serverSocket.accept();
            // 每接收到一个客户端必须为这个客户端管道分配一个独立的线程来处理与之通信。
            new ServerReaderThread(socket).start();
        }
    }
}
class ServerReaderThread extends Thread{
    privat Socket socket;
    public ServerReaderThread(Socket socket){this.socket = socket;}
    @Override
    public void run() {
        try(InputStream is = socket.getInputStream();
           	BufferedReader br = new BufferedReader(new InputStreamReader(is))
           ){
            String line;
            while((line = br.readLine()) != null){
                sout(socket.getRemoteSocketAddress() + ":" + line);
            }
        }catch(Exception e){
            sout(socket.getRemoteSocketAddress() + "下线了~~~~~~");
        }
    }
}
```



***



#### 伪异步

一个客户端要一个线程，这种模型是不行的，并发越高系统瘫痪的越快，可以在服务端引入线程池，使用线程池来处理与客户端的消息通信

优势：不会引起系统的死机，可以控制并发线程的数量
劣势：同时可以并发的线程将受到限制

```java
public class BIOServer {
    public static void main(String[] args) throws Exception {
        //线程池机制
        //创建一个线程池，如果有客户端连接，就创建一个线程，与之通讯(单独写一个方法)
        ExecutorService newCachedThreadPool = Executors.newCachedThreadPool();
        //创建ServerSocket
        ServerSocket serverSocket = new ServerSocket(6666);
        System.out.println("服务器启动了");
        while (true) {
            System.out.println("线程名字 = " + Thread.currentThread().getName());
            //监听，等待客户端连接
            System.out.println("等待连接....");
            final Socket socket = serverSocket.accept();
            System.out.println("连接到一个客户端");
            //创建一个线程，与之通讯
            newCachedThreadPool.execute(new Runnable() {
                public void run() {
                    //可以和客户端通讯
                    handler(socket);
                }
            });
        }
    }

    //编写一个handler方法，和客户端通讯
    public static void handler(Socket socket) {
        try {
            System.out.println("线程名字 = " + Thread.currentThread().getName());
            byte[] bytes = new byte[1024];
            //通过socket获取输入流
            InputStream inputStream = socket.getInputStream();
            int len;
            //循环的读取客户端发送的数据
            while ((len = inputStream.read(bytes)) != -1) {
                System.out.println("线程名字 = " + Thread.currentThread().getName());
                //输出客户端发送的数据
                System.out.println(new String(bytes, 0, read));
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println("关闭和client的连接");
            try {
                socket.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```



****



### 文件传输

#### 字节流

客户端：本地图片:  ‪E:\seazean\图片资源\beautiful.jpg
服务端：服务器路径：E:\seazean\图片服务器

UUID. randomUUID() : 方法生成随机的文件名

**socket.shutdownOutput()**：这个必须执行，不然服务器会一直循环等待数据，最后文件损坏，程序报错

```java
//常量包
public class Constants {
    public static final String SRC_IMAGE = "D:\\seazean\\图片资源\\beautiful.jpg";
    public static final String SERVER_DIR = "D:\\seazean\\图片服务器\\";
    public static final String SERVER_IP = "127.0.0.1";
    public static final int SERVER_PORT = 8888;

}
public class ClientDemo {
    public static void main(String[] args) throws Exception {
        Socket socket = new Socket(Constants.ERVER_IP,Constants.SERVER_PORT);
        BufferedOutputStream bos=new BufferedOutputStream(socket.getOutputStream());
        //提取本机的图片上传给服务端。Constants.SRC_IMAGE
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream());
        byte[] buffer = new byte[1024];
        int len ;
        while((len = bis.read(buffer)) != -1) {
            bos.write(buffer, 0 ,len);
        }
        bos.flush();// 刷新图片数据到服务端！！
        socket.shutdownOutput();// 告诉服务端我的数据已经发送完毕，不要在等我了！
        bis.close();
        
        //等待着服务端的响应数据！！
        BufferedReader br = new BufferedReader(
           				 new InputStreamReader(socket.getInputStream()));
        System.out.println("收到服务端响应："+br.readLine());
    }
}
```

```java
public class ServerDemo {
    public static void main(String[] args) throws Exception {
        System.out.println("----服务端启动----");
        // 1.注册端口: 
        ServerSocket serverSocket = new ServerSocket(Constants.SERVER_PORT);
        // 2.定义一个循环不断的接收客户端的连接请求
        while(true){
            // 3.开始等待接收客户端的Socket管道连接。
            Socket socket = serverSocket.accept();
            // 4.每接收到一个客户端必须为这个客户端管道分配一个独立的线程来处理与之通信。
            new ServerReaderThread(socket).start();
        }
    }
}
class ServerReaderThread extends Thread{
    private Socket socket ;
    public ServerReaderThread(Socket socket){this.socket = socket;}
    @Override
    public void run() {
        try{
            InputStream is = socket.getInputStream();
           	BufferedInputStream bis = new BufferedInputStream(is);
            BufferedOutputStream bos = new BufferedOutputStream(
                new FileOutputStream
                (Constants.SERVER_DIR+UUID.randomUUID().toString()+".jpg"));
            byte[] buffer = new byte[1024];
            int len;
            while((len = bis.read(buffer)) != -1){
                bos.write(buffer,0,len);
            }
            bos.close();
            System.out.println("服务端接收完毕了！");
            
            // 4.响应数据给客户端
            PrintStream ps = new PrintStream(socket.getOutputStream());
            ps.println("您好，已成功接收您上传的图片！");
            ps.flush();
            Thread.sleep(10000);
        }catch (Exception e){
            sout(socket.getRemoteSocketAddress() + "下线了");
        }
    }
}
```



****



#### 数据流

构造方法：
`DataOutputStream(OutputStream out)` : 创建一个新的数据输出流，以将数据写入指定的底层输出流
`DataInputStream(InputStream in) ` : 创建使用指定的底层 InputStream 的 DataInputStream

常用API：
`final void writeUTF(String str)` : 使用机器无关的方式使用 UTF-8 编码将字符串写入底层输出流
`final String readUTF()` : 读取以modified UTF-8格式编码的 Unicode 字符串，返回 String  类型

```java
public class Client {
    public static void main(String[] args) {
		InputStream is = new FileInputStream("path");
            //  1、请求与服务端的Socket链接
            Socket socket = new Socket("127.0.0.1" , 8888);
            //  2、把字节输出流包装成一个数据输出流
            DataOutputStream dos = new DataOutputStream(socket.getOutputStream());
            //  3、先发送上传文件的后缀给服务端
            dos.writeUTF(".png");
            //  4、把文件数据发送给服务端进行接收
            byte[] buffer = new byte[1024];
            int len;
            while((len = is.read(buffer)) > 0 ){
                dos.write(buffer , 0 , len);
            }
            dos.flush();
            Thread.sleep(10000);
    }
}

public class Server {
    public static void main(String[] args) {
        ServerSocket ss = new ServerSocket(8888);
        Socket socket = ss.accept();
 		// 1、得到一个数据输入流读取客户端发送过来的数据
		DataInputStream dis = new DataInputStream(socket.getInputStream());
		// 2、读取客户端发送过来的文件类型
		String suffix = dis.readUTF();
		// 3、定义一个字节输出管道负责把客户端发来的文件数据写出去
		OutputStream os = new FileOutputStream("path"+
                    UUID.randomUUID().toString()+suffix);
		// 4、从数据输入流中读取文件数据，写出到字节输出流中去
		byte[] buffer = new byte[1024];
		int len;
		while((len = dis.read(buffer)) > 0){
 			os.write(buffer,0, len);
		}
		os.close();
		System.out.println("服务端接收文件保存成功！");
    }
}
```



***



## NIO

### 基本介绍

**NIO的介绍**：

Java NIO（New IO、Java non-blocking IO），从 Java 1.4 版本开始引入的一个新的 IO API，可以替代标准的  Java IO API，NIO 支持面**向缓冲区**的、基于**通道**的 IO 操作，以更加高效的方式进行文件的读写操作。

* NIO 有三大核心部分：**Channel( 通道) ，Buffer( 缓冲区)，Selector( 选择器)**
* NIO 是非阻塞IO，传统 IO 的 read 和 write 只能阻塞执行，线程在读写 IO 期间不能干其他事情，比如调用socket.read()，如果服务器没有数据传输过来，线程就一直阻塞，而 NIO 中可以配置 Socket 为非阻塞模式
* NIO 可以做到用一个线程来处理多个操作的。假设有 1000 个请求过来，根据实际情况可以分配20 或者 80个线程来处理，不像之前的阻塞 IO 那样分配 1000 个

NIO 和 BIO 的比较：

* BIO 以流的方式处理数据，而 NIO 以块的方式处理数据，块 I/O 的效率比流 I/O 高很多

* BIO 是阻塞的，NIO 则是非阻塞的

* BIO 基于字节流和字符流进行操作，而 NIO 基于 Channel 和 Buffer 进行操作，数据从通道读取到缓冲区中，或者从缓冲区写入到通道中。Selector 用于监听多个通道的事件（比如：连接请求，数据到达等），因此使用单个线程就可以监听多个客户端通道

  | NIO                       | BIO                 |
  | ------------------------- | ------------------- |
  | 面向缓冲区（Buffer）      | 面向流（Stream）    |
  | 非阻塞（Non Blocking IO） | 阻塞IO(Blocking IO) |
  | 选择器（Selectors）       |                     |



***



### 实现原理

NIO 三大核心部分：**Channel( 通道) ，Buffer( 缓冲区), Selector( 选择器)**

* Buffer 缓冲区

  缓冲区本质是一块可以写入数据、读取数据的内存，**底层是一个数组**，这块内存被包装成NIO Buffer对象，并且提供了方法用来操作这块内存，相比较直接对数组的操作，Buffer 的 API 更加容易操作和管理

* Channel 通道

  Java NIO 的通道类似流，不同的是既可以从通道中读取数据，又可以写数据到通道，流的读写通常是单向的，通道可以非阻塞读取和写入通道，支持读取或写入缓冲区，也支持异步地读写。

* Selector 选择器

  Selector 是一个 Java NIO 组件，能够检查一个或多个 NIO 通道，并确定哪些通道已经准备好进行读取或写入，这样一个单独的线程可以管理多个channel，从而管理多个网络连接，提高效率

NIO 的实现框架：

![](https://gitee.com/seazean/images/raw/master/Java/NIO框架.png)

* 每个 Channel 对应一个 Buffer
* 一个线程对应 Selector ， 一个 Selector 对应多个 Channel（连接）
* 程序切换到哪个Channel 是由事件决定的，Event 是一个重要的概念
* Selector 会根据不同的事件，在各个通道上切换
* Buffer 是一个内存块 ， 底层是一个数组
* 数据的读取写入是通过 Buffer 完成的 , BIO 中要么是输入流，或者是输出流，不能双向，NIO 的 Buffer 是可以读也可以写， flip() 切换 Buffer 的工作模式

Java NIO 系统的核心在于：通道和缓冲区，通道表示打开到 IO 设备（例如：文件、 套接字）的连接。若需要使用 NIO 系统，获取用于连接 IO 设备的通道以及用于容纳数据的缓冲区，然后操作缓冲区，对数据进行处理。简而言之，Channel 负责传输， Buffer 负责存取数据



***



### 缓冲区

#### 基本介绍

缓冲区（Buffer）：缓冲区本质上是一个**可以读写数据的内存块**，用于特定基本数据类型的容器，用于与 NIO 通道进行交互，数据是从通道读入缓冲区，从缓冲区写入通道中的

![](https://gitee.com/seazean/images/raw/master/Java/NIO-Buffer.png)

Buffer 底层是一个数组，可以保存多个相同类型的数据，根据数据类型不同 ，有以下 Buffer 常用子类：ByteBuffer、CharBuffer、ShortBuffer、IntBuffer、LongBuffer、FloatBuffer、DoubleBuffer 



***



#### 基本属性

* 容量 (capacity)：作为一个内存块，Buffer 具有固定大小，缓冲区容量不能为负，并且创建后不能更改

* 限制 (limit)：表示缓冲区中可以操作数据的大小（limit 后数据不能进行读写），缓冲区的限制不能为负，并且不能大于其容量。 **写入模式，限制等于 buffer 的容量；读取模式下，limit 等于写入的数据量**

* 位置 (position)：下一个要读取或写入的数据的索引，缓冲区的位置不能为负，并且不能大于其限制

* 标记 (mark)与重置 (reset)：标记是一个索引，通过Buffer中的 mark() 方法指定 Buffer 中一个特定的位置，可以通过调用 reset() 方法恢复到这个 position

* 位置、限制、容量遵守以下不变式： **0 <= position <= limit <= capacity**

  <img src="https://gitee.com/seazean/images/raw/master/Java/NIO-Buffer操作.png" style="zoom:67%;" />



***



#### 常用API

`static XxxBuffer allocate(int capacity)` : 创建一个容量为capacity 的 XxxBuffer 对象

Buffer 基本操作：

| 方法                                        | 说明                                                    |
| ------------------------------------------- | ------------------------------------------------------- |
| public Buffer clear()                       | 清空缓冲区，不清空内容，将位置设置为零，限制设置为容量  |
| public Buffer flip()                        | 翻转缓冲区，将缓冲区的界限设置为当前位置，position 置 0 |
| public int capacity()                       | 返回 Buffer的 capacity 大小                             |
| public final int limit()                    | 返回 Buffer 的界限 limit 的位置                         |
| public Buffer limit(int n)                  | 设置缓冲区界限为 n                                      |
| public Buffer mark()                        | 在此位置对缓冲区设置标记                                |
| public final int position()                 | 返回缓冲区的当前位置 position                           |
| public Buffer position(int n)               | 设置缓冲区的当前位置为n                                 |
| public Buffer reset()                       | 将位置 position 重置为先前 mark 标记的位置              |
| public Buffer rewind()                      | 将位置设为为0，取消设置的 mark                          |
| public final int remaining()                | 返回当前位置 position 和 limit 之间的元素个数           |
| public final boolean hasRemaining()         | 判断缓冲区中是否还有元素                                |
| public static ByteBuffer wrap(byte[] array) | 将一个字节数组包装到缓冲区中                            |
| abstract ByteBuffer asReadOnlyBuffer()      | 创建一个新的只读字节缓冲区                              |

Buffer 数据操作：

| 方法                                              | 说明                                            |
| ------------------------------------------------- | ----------------------------------------------- |
| public abstract byte get()                        | 读取该缓冲区当前位置的单个字节，然后增加位置    |
| public ByteBuffer get(byte[] dst)                 | 读取多个字节到字节数组dst中                     |
| public abstract byte get(int index)               | 读取指定索引位置的字节，不移动 position         |
| public abstract ByteBuffer put(byte b)            | 将给定单个字节写入缓冲区的当前位置，position+1  |
| public final ByteBuffer put(byte[] src)           | 将 src 字节数组写入缓冲区的当前位置             |
| public abstract ByteBuffer put(int index, byte b) | 将指定字节写入缓冲区的索引位置，不移动 position |

提示："\n"，占用两个字节



****



#### 读写数据

使用Buffer读写数据一般遵循以下四个步骤：

* 写入数据到 Buffer
* 调用 flip()方法，转换为读取模式
* 从 Buffer 中读取数据
* 调用 buffer.clear() 方法清除缓冲区

```java
public class TestBuffer {
	@Test
    public void test(){
		String str = "seazean";
		//1. 分配一个指定大小的缓冲区
		ByteBuffer buffer = ByteBuffer.allocate(1024);
		System.out.println("-----------------allocate()----------------");
		System.out.println(bufferf.position());//0
		System.out.println(buffer.limit());//1024
		System.out.println(buffer.capacity());//1024
        
        //2. 利用 put() 存入数据到缓冲区中
      	buffer.put(str.getBytes());
     	System.out.println("-----------------put()----------------");
		System.out.println(bufferf.position());//7
		System.out.println(buffer.limit());//1024
		System.out.println(buffer.capacity());//1024
        
        //3. 切换读取数据模式
        buffer.flip();
        System.out.println("-----------------flip()----------------");
        System.out.println(buffer.position());//0
        System.out.println(buffer.limit());//7
        System.out.println(buffer.capacity());//1024
        
        //4. 利用 get() 读取缓冲区中的数据
        byte[] dst = new byte[buffer.limit()];
        buffer.get(dst);
        System.out.println(dst.length);
        System.out.println(new String(dst, 0, dst.length));
        System.out.println(buffer.position());//7
        System.out.println(buffer.limit());//7
       
        //5. clear() : 清空缓冲区. 但是缓冲区中的数据依然存在，但是处于“被遗忘”状态
        System.out.println(buffer.hasRemaining());//true
        buffer.clear();
        System.out.println(buffer.hasRemaining());//true
      	System.out.println("-----------------clear()----------------");
      	System.out.println(buffer.position());//0
      	System.out.println(buffer.limit());//1024
      	System.out.println(buffer.capacity());//1024
    }
}
```



****



### 直接内存

#### 基本介绍

Byte Buffer 有两种类型，一种是基于直接内存（也就是非堆内存），另一种是非直接内存（也就是堆内存）

Direct Memory 优点：

* Java 的 NIO 库允许 Java 程序使用直接内存，用于数据缓冲区，使用 native 函数直接分配堆外内存
* 读写性能高，读写频繁的场合可能会考虑使用直接内存
* 大大提高 IO 性能，避免了在 Java 堆和 native 堆来回复制数据

直接内存缺点：

* 分配回收成本较高，不受 JVM 内存回收管理
* 可能导致 OutOfMemoryError 异常：OutOfMemoryError: Direct buffer memory
* 回收依赖 System.gc() 的调用，但这个调用 JVM 不保证执行、也不保证何时执行，行为是不可控的。程序一般需要自行管理，成对去调用 malloc、free

应用场景：

- 有很大的数据需要存储，数据的生命周期很长
- 适合频繁的 IO 操作，比如网络并发场景

数据流的角度：

* 非直接内存的作用链：本地IO → 内核缓冲区→ 用户缓冲区 →内核缓冲区 → 本地IO
* 直接内存是：本地IO → 直接内存 → 本地IO

JVM 直接内存图解：

<img src="https://gitee.com/seazean/images/raw/master/Java/JVM-直接内存直接缓冲区.png" style="zoom: 50%;" />

<img src="https://gitee.com/seazean/images/raw/master/Java/JVM-直接内存非直接缓冲区.png" style="zoom:50%;" />





***



#### 源码解析

直接内存创建 Buffer 对象：`static XxxBuffer allocateDirect(int capacity)`

堆外内存不受 JVM GC 控制，可以使用堆外内存进行**通信**，防止 GC 后缓冲区位置发生变化的情况，源码：

* SocketChannel#write(java.nio.ByteBuffer) → SocketChannelImpl#write(java.nio.ByteBuffer)

  ```java
  public int write(ByteBuffer var1) throws IOException {
       do {
           var3 = IOUtil.write(this.fd, var1, -1L, nd);
       } while(var3 == -3 && this.isOpen());
  }
  ```

* IOUtil#write(java.io.FileDescriptor, java.nio.ByteBuffer, long, sun.nio.ch.NativeDispatcher)

  ```java
  static int write(FileDescriptor var0, ByteBuffer var1, long var2, NativeDispatcher var4) {
      //判断是否是直接内存，是则直接写出，不是则封装到直接内存
      if (var1 instanceof DirectBuffer) {
          return writeFromNativeBuffer(var0, var1, var2, var4);
      } else {
          //....
          //从堆内buffer拷贝到堆外buffer
          ByteBuffer var8 = Util.getTemporaryDirectBuffer(var7);
          var8.put(var1);
          //...
          //从堆外写到内核缓冲区
  		int var9 = writeFromNativeBuffer(var0, var8, var2, var4);
  	}
  }
  ```



***



#### 分配回收

DirectByteBuffer 源码分析：

```java
DirectByteBuffer(int cap) { 
    //....
    long base = 0;
    try {
        base = unsafe.allocateMemory(size);
    }
    unsafe.setMemory(base, size, (byte) 0);
    if (pa && (base % ps != 0)) {
        address = base + ps - (base & (ps - 1));
    } else {
        address = base;
    }
    cleaner = Cleaner.create(this, new Deallocator(base, size, cap));
}
private static class Deallocator implements Runnable {
    public void run() {
        unsafe.freeMemory(address);
		//...
    }
}
```

分配和回收原理：

* 使用了 Unsafe 对象的 allocateMemory 方法完成直接内存的分配，setMemory 方法完成赋值
* ByteBuffer 的实现类内部，使用了 Cleaner （虚引用）来监测 ByteBuffer 对象，一旦 ByteBuffer 对象被垃圾回收，那么 ReferenceHandler 线程通过 Cleaner 的 clean 方法调用 Deallocator 的 run方法，最后通过 freeMemory 来释放直接内存

```java
/**
 * 直接内存分配的底层原理：Unsafe
 */
public class Demo1_27 {
    static int _1Gb = 1024 * 1024 * 1024;

    public static void main(String[] args) throws IOException {
        Unsafe unsafe = getUnsafe();
        // 分配内存
        long base = unsafe.allocateMemory(_1Gb);
        unsafe.setMemory(base, _1Gb, (byte) 0);
        System.in.read();
        // 释放内存
        unsafe.freeMemory(base);
        System.in.read();
    }

    public static Unsafe getUnsafe() {
        try {
            Field f = Unsafe.class.getDeclaredField("theUnsafe");
            f.setAccessible(true);
            Unsafe unsafe = (Unsafe) f.get(null);
            return unsafe;
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new RuntimeException(e);
        }
    }
}
```





****



#### 共享内存

FileChannel 提供 map 方法把文件映射到虚拟内存，通常情况可以映射整个文件，如果文件比较大，可以进行分段映射，完成映射后对物理内存的操作会被同步到硬盘上

FileChannel 中的成员属性：

* MapMode.mode：内存映像文件访问的方式，共三种：
  * `MapMode.READ_ONLY`：只读，试图修改得到的缓冲区将导致抛出异常。
  * `MapMode.READ_WRITE`：读/写，对得到的缓冲区的更改最终将写入文件；但该更改对映射到同一文件的其他程序不一定是可见的
  * `MapMode.PRIVATE`：私用，可读可写，但是修改的内容不会写入文件，只是 buffer 自身的改变，称之为 copy on write 写时复制

* `public final FileLock lock()`：获取此文件通道的排他锁

MappedByteBuffer，可以让文件直接在内存（堆外内存）中进行修改，这种方式叫做内存映射，可以直接调用系统底层的缓存，没有 JVM 和系统之间的复制操作，提高了传输效率，作用：

* 用在进程间的通信，能达到**共享内存页**的作用，但在高并发下要对文件内存进行加锁，防止出现读写内容混乱和不一致性，Java 提供了文件锁 FileLock，但在父/子进程中锁定后另一进程会一直等待，效率不高
* 读写那些太大而不能放进内存中的文件

MappedByteBuffer 较之 ByteBuffer新增的三个方法

- `final MappedByteBuffer force()`：缓冲区是 READ_WRITE 模式下，对缓冲区内容的修改强行写入文件
- `final MappedByteBuffer load()`：将缓冲区的内容载入物理内存，并返回该缓冲区的引用
- `final boolean isLoaded()`：如果缓冲区的内容在物理内存中，则返回真，否则返回假

```java
public class MappedByteBufferTest {
    public static void main(String[] args) throws Exception {
        RandomAccessFile ra = new RandomAccessFile("1.txt", "rw");
        //获取对应的通道
        FileChannel channel = ra.getChannel();

        /**
         * 参数1	FileChannel.MapMode.READ_WRITE 使用的读写模式
         * 参数2	0: 文件映射时的起始位置
         * 参数3	5: 是映射到内存的大小（不是索引位置），即将 1.txt 的多少个字节映射到内存
         * 可以直接修改的范围就是 0-5
         * 实际类型 DirectByteBuffer
         */
        MappedByteBuffer buffer = channel.map(FileChannel.MapMode.READ_WRITE, 0, 5);

        buffer.put(0, (byte) 'H');
        buffer.put(3, (byte) '9');
        buffer.put(5, (byte) 'Y');	//IndexOutOfBoundsException

        ra.close();
        System.out.println("修改成功~~");
    }
}
```

从硬盘上将文件读入内存，要经过文件系统进行数据拷贝，拷贝操作是由文件系统和硬件驱动实现。通过内存映射的方法访问硬盘上的文件，拷贝数据的效率要比 read 和 write 系统调用高：

- read() 是系统调用，首先将文件从硬盘拷贝到内核空间的一个缓冲区，再将这些数据拷贝到用户空间，实际上进行了两次数据拷贝
- mmap() 也是系统调用，但没有进行数据拷贝，当缺页中断发生时，直接将文件从硬盘拷贝到共享内存，只进行了一次数据拷贝

注意：mmap 的文件映射，在 Full GC 时才会进行释放，如果需要手动清除内存映射文件，可以反射调用sun.misc.Cleaner 方法



参考文章：https://www.jianshu.com/p/f90866dcbffc



***



### 通道

#### 基本介绍

通道（Channel）：表示 IO 源与目标打开的连接，Channel 类似于传统的流，只不过 Channel 本身不能直接访问数据，Channel 只能与 Buffer **进行交互**

1. NIO 的通道类似于流，但有些区别如下：
   * 通道可以同时进行读写，而流只能读或者只能写
   * 通道可以实现异步读写数据
   * 通道可以从缓冲读数据，也可以写数据到缓冲

2. BIO 中的 stream 是单向的，NIO中的 Channel 是双向的，可以读操作，也可以写操作

3. Channel 在 NIO 中是一个接口：`public interface Channel extends Closeable{}`



Channel 实现类：

* FileChannel：用于读取、写入、映射和操作文件的通道
* DatagramChannel：通过 UDP 读写网络中的数据通道
* SocketChannel：通过 TCP 读写网络中的数据
* ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个SocketChannel。
  提示：ServerSocketChanne 类似 ServerSocket , SocketChannel 类似 Socket



***



#### 常用API

获取 Channel 方式：

* 对支持通道的对象调用 `getChannel()` 方法
* 通过通道的静态方法 `open()` 打开并返回指定通道
* 使用 Files 类的静态方法 `newByteChannel()` 获取字节通道

Channel 基本操作：

| 方法                                       | 说明                                                     |
| ------------------------------------------ | -------------------------------------------------------- |
| public abstract int read(ByteBuffer dst)   | 从 Channel 中读取数据到 ByteBuffer，从 position 开始储存 |
| public final long read(ByteBuffer[] dsts)  | 将Channel中的数据“分散”到ByteBuffer[]                    |
| public abstract int write(ByteBuffer src)  | 将 ByteBuffer 中的数据写入 Channel，从 position 开始写出 |
| public final long write(ByteBuffer[] srcs) | 将ByteBuffer[]到中的数据“聚集”到Channel                  |
| public abstract long position()            | 返回此通道的文件位置                                     |
| FileChannel position(long newPosition)     | 设置此通道的文件位置                                     |
| public abstract long size()                | 返回此通道的文件的当前大小                               |

**读写都是相对于内存来看，也就是缓冲区**



****



#### 文件读写

```java
public class ChannelTest {
    @Test
	public void write() throws Exception{
 		// 1、字节输出流通向目标文件
        FileOutputStream fos = new FileOutputStream("data01.txt");
        // 2、得到字节输出流对应的通道Channel
        FileChannel channel = fos.getChannel();
        // 3、分配缓冲区
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        buffer.put("hello,黑马Java程序员！".getBytes());
        // 4、把缓冲区切换成写出模式
        buffer.flip();
        channel.write(buffer);
        channel.close();
        System.out.println("写数据到文件中！");
    }
    @Test
    public void read() throws Exception {
        // 1、定义一个文件字节输入流与源文件接通
        FileInputStream fis = new FileInputStream("data01.txt");
        // 2、需要得到文件字节输入流的文件通道
        FileChannel channel = fis.getChannel();
        // 3、定义一个缓冲区
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        // 4、读取数据到缓冲区
        channel.read(buffer);
        buffer.flip();
        // 5、读取出缓冲区中的数据并输出即可
        String rs = new String(buffer.array(),0,buffer.remaining());
        System.out.println(rs);
    }
}
```



***



#### 文件复制

Channel 的两个方法：

* `abstract long transferFrom(ReadableByteChannel src, long position, long count)`：从给定的可读字节通道将字节传输到该通道的文件中
  * src：源通道
  * position：文件中要进行传输的位置，必须是非负的 
  * count：要传输的最大字节数，必须是非负的 

* `abstract long transferTo(long position, long count, WritableByteChannel target)`：将该通道文件的字节传输到给定的可写字节通道。
  * position：传输开始的文件中的位置; 必须是非负的 
  * count：要传输的最大字节数; 必须是非负的 
  * target：目标通道 

文件复制的两种方式：

1. Buffer
2. 使用上述两种方法

![](https://gitee.com/seazean/images/raw/master/Java/NIO-复制文件.png)

```java
public class ChannelTest {
    @Test
    public void copy1() throws Exception {
        File srcFile = new File("C:\\壁纸.jpg");
        File destFile = new File("C:\\Users\\壁纸new.jpg");
        // 得到一个字节字节输入流
        FileInputStream fis = new FileInputStream(srcFile);
        // 得到一个字节输出流
        FileOutputStream fos = new FileOutputStream(destFile);
        // 得到的是文件通道
        FileChannel isChannel = fis.getChannel();
        FileChannel osChannel = fos.getChannel();
        // 分配缓冲区
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        while(true){
            // 必须先清空缓冲然后再写入数据到缓冲区
            buffer.clear();
            // 开始读取一次数据
            int flag = isChannel.read(buffer);
            if(flag == -1){
                break;
            }
            // 已经读取了数据 ，把缓冲区的模式切换成可读模式
            buffer.flip();
            // 把数据写出到
            osChannel.write(buffer);
        }
        isChannel.close();
        osChannel.close();
        System.out.println("复制完成！");
    }
    
	@Test
	public void copy02() throws Exception {
    	// 1、字节输入管道
   	 	FileInputStream fis = new FileInputStream("data01.txt");
   	 	FileChannel isChannel = fis.getChannel();
    	// 2、字节输出流管道
    	FileOutputStream fos = new FileOutputStream("data03.txt");
    	FileChannel osChannel = fos.getChannel();
    	// 3、复制
    	osChannel.transferFrom(isChannel,isChannel.position(),isChannel.size());
    	isChannel.close();
    	osChannel.close();
	}
    
	@Test
	public void copy03() throws Exception {
    	// 1、字节输入管道
    	FileInputStream fis = new FileInputStream("data01.txt");
    	FileChannel isChannel = fis.getChannel();
    	// 2、字节输出流管道
    	FileOutputStream fos = new FileOutputStream("data04.txt");
    	FileChannel osChannel = fos.getChannel();
    	// 3、复制
    	isChannel.transferTo(isChannel.position() , isChannel.size() , osChannel);
    	isChannel.close();
    	osChannel.close();
	}
}
```



***



#### 分散聚集

分散读取（Scatter ）：是指把Channel通道的数据读入到多个缓冲区中去

聚集写入（Gathering ）：是指将多个 Buffer 中的数据“聚集”到 Channel。

```java
public class ChannelTest {
    @Test
    public void test() throws IOException{
    	// 1、字节输入管道
        FileInputStream is = new FileInputStream("data01.txt");
        FileChannel isChannel = is.getChannel();
        // 2、字节输出流管道
        FileOutputStream fos = new FileOutputStream("data02.txt");
        FileChannel osChannel = fos.getChannel();
        // 3、定义多个缓冲区做数据分散
        ByteBuffer buffer1 = ByteBuffer.allocate(4);
        ByteBuffer buffer2 = ByteBuffer.allocate(1024);
        ByteBuffer[] buffers = {buffer1 , buffer2};
        // 4、从通道中读取数据分散到各个缓冲区
        isChannel.read(buffers);
        // 5、从每个缓冲区中查询是否有数据读取到了
        for(ByteBuffer buffer : buffers){
            buffer.flip();// 切换到读数据模式
            System.out.println(new String(buffer.array() , 0 , buffer.remaining()));
        }
        // 6、聚集写入到通道
        osChannel.write(buffers);
        isChannel.close();
        osChannel.close();
        System.out.println("文件复制~~");
    }
}
```



***



### 选择器

#### 基本介绍

选择器（Selector） 是 SelectableChannle 对象的**多路复用器**，Selector 可以同时监控多个通道的状况，利用 Selector 可使一个单独的线程管理多个 Channel。**Selector 是非阻塞 IO 的核心**。

![](https://gitee.com/seazean/images/raw/master/Java/NIO-Selector.png)

* Selector 能够检测多个注册的通道上是否有事件发生（多个 Channel 以事件的方式可以注册到同一个Selector)，如果有事件发生，便获取事件然后针对每个事件进行相应的处理，就可以只用一个单线程去管理多个通道，也就是管理多个连接和请求
* 只有在连接/通道真正有读写事件发生时，才会进行读写，就大大地减少了系统开销，并且不必为每个连接都创建一个线程，不用去维护多个线程
* 避免了多线程之间的上下文切换导致的开销



***



#### 常用API

创建 Selector：`Selector selector = Selector.open();`

向选择器注册通道：`SelectableChannel.register(Selector sel, int ops)`

选择器对通道的监听事件，需要通过第二个参数 ops 指定。监听的事件类型用四个常量表示：

* 读 : SelectionKey.OP_READ （1）
* 写 : SelectionKey.OP_WRITE （4）
* 连接 : SelectionKey.OP_CONNECT （8）
* 接收 : SelectionKey.OP_ACCEPT （16）
* 若注册时不止监听一个事件，则可以使用“位或”操作符连接：
  `int interestSet = SelectionKey.OP_READ | SelectionKey.OP_WRITE `



**Selector API**：

| 方法                                             | 说明                                  |
| ------------------------------------------------ | ------------------------------------- |
| public static Selector open()                    | 打开选择器                            |
| public abstract void close()                     | 关闭此选择器                          |
| public abstract int select()                     | 阻塞选择一组通道准备好进行I/O操作的键 |
| public abstract int select(long timeout)         | 阻塞等待 timeout 毫秒                 |
| public abstract int selectNow()                  | 获取一下，不阻塞，立刻返回            |
| public abstract Selector wakeup()                | 唤醒正在阻塞的 selector               |
| public abstract Set<SelectionKey> selectedKeys() | 返回此选择器的选择键集                |

SelectionKey API:

| 方法                                        | 说明                                               |
| ------------------------------------------- | -------------------------------------------------- |
| public abstract void cancel()               | 取消该键的通道与其选择器的注册                     |
| public abstract SelectableChannel channel() | 返回创建此键的通道，该方法在取消键之后仍将返回通道 |
| public final boolean isAcceptable()         | 检测此密钥的通道是否已准备好接受新的套接字连接     |
| public final boolean isConnectable()        | 检测此密钥的通道是否已完成或未完成其套接字连接操作 |
| public final boolean isReadable()           | 检测此密钥的频道是否可以阅读                       |
| public final boolean isWritable()           | 检测此密钥的通道是否准备好进行写入                 |

基本步骤：

```java
//1.获取通道
ServerSocketChannel ssChannel = ServerSocketChannel.open();
//2.切换非阻塞模式
ssChannel.configureBlocking(false);
//3.绑定连接
ssChannel.bin(new InetSocketAddress(9999));
//4.获取选择器
Selector selector = Selector.open();
//5.将通道注册到选择器上，并且指定“监听接收事件”
ssChannel.register(selector, SelectionKey.OP_ACCEPT);
```



***



### NIO实现

#### 常用API

* SelectableChannel_API

  | 方法                                                         | 说明                                         |
  | ------------------------------------------------------------ | -------------------------------------------- |
  | public final SelectableChannel configureBlocking(boolean block) | 设置此通道的阻塞模式                         |
  | public final SelectionKey register(Selector sel, int ops)    | 向给定的选择器注册此通道，并选择关注的的事件 |

* SocketChannel_API：

  | 方法                                                    | 说明                           |
  | :------------------------------------------------------ | ------------------------------ |
  | public static SocketChannel open()                      | 打开套接字通道                 |
  | public static SocketChannel open(SocketAddress remote)  | 打开套接字通道并连接到远程地址 |
  | public abstract boolean connect(SocketAddress remote)   | 连接此通道的到远程地址         |
  | public abstract SocketChannel bind(SocketAddress local) | 将通道的套接字绑定到本地地址   |
  | public abstract SocketAddress getLocalAddress()         | 返回套接字绑定的本地套接字地址 |
  | public abstract SocketAddress getRemoteAddress()        | 返回套接字连接的远程套接字地址 |

* ServerSocketChannel_API：

  | 方法                                                       | 说明                                                         |
  | ---------------------------------------------------------- | ------------------------------------------------------------ |
  | public static ServerSocketChannel open()                   | 打开服务器套接字通道                                         |
  | public final ServerSocketChannel bind(SocketAddress local) | 将通道的套接字绑定到本地地址，并配置套接字以监听连接         |
  | public abstract SocketChannel accept()                     | 接受与此通道套接字的连接，通过此方法返回的套接字通道将处于阻塞模式 |

  * 如果 ServerSocketChannel 处于非阻塞模式，如果没有挂起连接，则此方法将立即返回 null
  * 如果通道处于阻塞模式，如果没有挂起连接将无限期地阻塞，直到有新的连接或发生 I/O 错误



***



#### 代码实现

服务端 ：

1. 获取通道，当客户端连接服务端时，服务端会通过 `ServerSocketChannel.accept` 得到 SocketChannel 

2. 切换非阻塞模式

3. 绑定连接

4. 获取选择器

5. 将通道注册到选择器上, 并且指定“监听接收事件”

6. 轮询式的获取选择器上已经“准备就绪”的事件

客户端：

1. 获取通道：`SocketChannel sc = SocketChannel.open(new InetSocketAddress(HOST, PORT))`
2. 切换非阻塞模式
3. 分配指定大小的缓冲区：`ByteBuffer buffer = ByteBuffer.allocate(1024)`
4. 发送数据给服务端

37行代码，如果判断条件改为 !=-1，需要客户端 shutdown 一下

```java
public class Server {
    public static void main(String[] args){
        // 1、获取通道
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        // 2、切换为非阻塞模式
        serverSocketChannel.configureBlocking(false);
        // 3、绑定连接的端口
        serverSocketChannel.bind(new InetSocketAddress(9999));
        // 4、获取选择器Selector
        Selector selector = Selector.open();
        // 5、将通道都注册到选择器上去，并且开始指定监听接收事件
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
		// 6、使用Selector选择器轮询已经就绪好的事件
        while (selector.select() > 0) {
            System.out.println("----开始新一轮的时间处理----");
            // 7、获取选择器中的所有注册的通道中已经就绪好的事件
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            Iterator<SelectionKey> it = selectionKeys.iterator();
            // 8、开始遍历这些准备好的事件
            while (it.hasNext()) {
                SelectionKey key = it.next();// 提取当前这个事件
                // 9、判断这个事件具体是什么
                if (key.isAcceptable()) {
                    // 10、直接获取当前接入的客户端通道
                    SocketChannel socketChannel = serverSocketChannel.accept();
                    // 11 、切换成非阻塞模式
                    socketChannel.configureBlocking(false);
                    // 12、将本客户端通道注册到选择器
                    socketChannel.register(selector, SelectionKey.OP_READ);
                } else if (key.isReadable()) {
                    // 13、获取当前选择器上的读就绪事件
                    SelectableChannel channel = key.channel();
                    SocketChannel socketChannel = (SocketChannel) channel;
                    // 14、读取数据
                    ByteBuffer buffer = ByteBuffer.allocate(1024);
                    int len;
                    while ((len = socketChannel.read(buffer)) > 0) {
                        buffer.flip();
                        System.out.println(socketChannel.getRemoteAddress() + ":" + new String(buffer.array(), 0, len));
                        buffer.clear();// 清除之前的数据
                    }
                }
                //删除当前的 selectionKey，防止重复操作
                it.remove();
            }
        }
    }
}
```

```java
public class Client {
    public static void main(String[] args) throws Exception {
        // 1、获取通道
        SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 9999));
        // 2、切换成非阻塞模式
        socketChannel.configureBlocking(false);
        // 3、分配指定缓冲区大小
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        // 4、发送数据给服务端
        Scanner sc = new Scanner(System.in);
        while (true){
            System.out.print("请说：");
            String msg = sc.nextLine();
            buffer.put(("波妞：" + msg).getBytes());
            buffer.flip();
            socketChannel.write(buffer);
            buffer.clear();
        }
    }
}
```



***



## AIO

Java AIO(NIO.2) ： AsynchronousI/O，异步非阻塞，采用了 Proactor 模式。服务器实现模式为一个有效请求一个线程，客户端的 I/O 请求都是由 OS 先完成了再通知服务器应用去启动线程进行处理。

```java
AIO异步非阻塞，基于NIO的，可以称之为NIO2.0
  BIO                     NIO                                AIO        
Socket                SocketChannel                    AsynchronousSocketChannel
ServerSocket          ServerSocketChannel	       AsynchronousServerSocketChannel
```

当进行读写操作时，调用 API 的 read 或 write 方法，这两种方法均为异步的，完成后会主动调用回调函数：

* 对于读操作，当有流可读取时，操作系统会将可读的流传入 read 方法的缓冲区
* 对于写操作，当操作系统将 write 方法传递的流写入完毕时，操作系统主动通知应用程序

在JDK1.7中，这部分内容被称作NIO.2，主要在 Java.nio.channels 包下增加了下面四个异步通道：
AsynchronousSocketChannel、AsynchronousServerSocketChannel、AsynchronousFileChannel、AsynchronousDatagramChannel

