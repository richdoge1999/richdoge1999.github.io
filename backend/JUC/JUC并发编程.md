# 多线程

## 1、基本概念

- **进程**，是一个程序的运行，动态的。一个进程至少包括一个线程，通常包含多个线程。

  Java默认有2个线程：main线程和GC线程



- **线程**，可用Thread、Runnable、Callable开启线程。

  Java没有权限真正开启一个线程，底层是通过本地方法调用C去与操作系统交互的。

  ![image-20221118171917471](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181719584.png) 



- **并发**，多个线程在很短的时间里、快速交替使用同一处理机（一核CPU）执行动作，形成一种它们在同时执行的假象，叫做并发。



- **并行**，多个线程分别使用一个处理机（多核CPU）执行动作，是真正的同时执行，叫做并行。

  ```java
  //Java中获取本机的CPU核数
  Runtime.getRuntime().availableProcessors()
  ```

  

- **并发编程**的本质：充分利用CPU资源

  

- **线程的状态**：NEW-创建，RUNNABLE-运行，BLOCKED-阻塞，WAITING-死等，TIMED_WAITING超时等待，TERMINATED终止

  ```java
  public enum State {
          /**
           * Thread state for a thread which has not yet started.
           */
          NEW,
  
          /**
           * Thread state for a runnable thread.  A thread in the runnable
           * state is executing in the Java virtual machine but it may
           * be waiting for other resources from the operating system
           * such as processor.
           */
          RUNNABLE,
  
          /**
           * Thread state for a thread blocked waiting for a monitor lock.
           * A thread in the blocked state is waiting for a monitor lock
           * to enter a synchronized block/method or
           * reenter a synchronized block/method after calling
           * {@link Object#wait() Object.wait}.
           */
          BLOCKED,
  
          /**
           * Thread state for a waiting thread.
           * A thread is in the waiting state due to calling one of the
           * following methods:
           * <ul>
           *   <li>{@link Object#wait() Object.wait} with no timeout</li>
           *   <li>{@link #join() Thread.join} with no timeout</li>
           *   <li>{@link LockSupport#park() LockSupport.park}</li>
           * </ul>
           *
           * <p>A thread in the waiting state is waiting for another thread to
           * perform a particular action.
           *
           * For example, a thread that has called <tt>Object.wait()</tt>
           * on an object is waiting for another thread to call
           * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
           * that object. A thread that has called <tt>Thread.join()</tt>
           * is waiting for a specified thread to terminate.
           */
          WAITING,
  
          /**
           * Thread state for a waiting thread with a specified waiting time.
           * A thread is in the timed waiting state due to calling one of
           * the following methods with a specified positive waiting time:
           * <ul>
           *   <li>{@link #sleep Thread.sleep}</li>
           *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
           *   <li>{@link #join(long) Thread.join} with timeout</li>
           *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
           *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
           * </ul>
           */
          TIMED_WAITING,
  
          /**
           * Thread state for a terminated thread.
           * The thread has completed execution.
           */
          TERMINATED;
      }
  ```



- 基本认识

  Java默认有两个线程，main线程和GC线程，其中GC线程是守护线程

  进程是资源分配的基本单位，线程是cpu调度的基本单位

  线程的执行顺序是由处理机进行调度决定的，线程调度与操作系统密切相关

  对同一份资源，多个线程存在互相抢夺的情况，因此需要并发控制

  线程会带来额外开销，包括并发控制、cpu调度时间、上下文切换等

  每个线程在自己的工作内存交互，内存控制不当会导致多线程数据不一致



## 2、线程的创建

**有三种创建线程的方式**

### 1.继承Thread类

```java
// 1.定义线程类Thread1继承Thread
// 2.重写线程执行体run()方法
// 3.创建线程对象，调用start()方法开启线程
public class Thread1 extends Thread{

    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("我是新线程---");
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread1().start();
        for (int i = 0; i < 20; i++) {
            TimeUnit.NANOSECONDS.sleep(1);
            System.out.println("我是主线程");
        }
    }
}
```

![image-20221118175348518](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181753586.png) 



---



### 2.实现Runnable接口

```java
package cusx.tjy.demo2;
import java.util.concurrent.TimeUnit;
// 1.定义线程类Thread2实现Runnable接口
// 2.重写线程执行体run()方法
// 3.创建Thread对象，丢入Runnable接口实现类，调用start()方法
public class Thread2 implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println("我是Runnable实现类线程");
        }
    }
    public static void main(String[] args) throws InterruptedException {
        Thread2 runnableT = new Thread2();
        new Thread(runnableT).start();
        for (int i = 0; i < 20; i++) {
            TimeUnit.NANOSECONDS.sleep(1);
            System.out.println("我是主线程");
        }
    }
}
```

![image-20221118180437894](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181804957.png) 



> jdk1.8以后，可以通过lambda表达式简写成

```java
//        Thread2 runnableT = new Thread2();
//        new Thread(runnableT).start();

        new Thread(()->{
            for (int i = 0; i < 20; i++) {
                System.out.println("我是Lambda表达式实现类线程");
            }
        }).start();
```



----



### 3.实现Callable接口

```java
package cusx.tjy.demo2;

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

//1.定义线程类实现Callable接口，Callable的泛型是返回值的类型
//2.重写线程执行体call()方法，有返回值
//3.创建FutureTask对象，泛型是返回值类型，并丢入是Callable接口实现类
//4.创建线程类，丢入FutureTask对象，启动线程类
//5.通过FutureTask对象调用get()方法可获得返回值，需处理异常
public class Thread4 implements Callable<String> {

    public static void main(String[] args) {
        FutureTask<String> futureTask = new FutureTask<String>(new Thread4());
        new Thread(futureTask).start();
        try {
            String res = futureTask.get();
            System.out.println(res);	//Thread-0
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } catch (ExecutionException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public String call() throws Exception {
        return Thread.currentThread().getName();
    }
}

```





-----



## 3、线程状态

操作系统中，线程有5种状态

![image-20221118181826746](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181818845.png) 



上述状态对应Java中的常用线程操作：

`Thread thread = new Thread()`：一旦创建出一个线程对象，对应新生蓝色状态

`run()`：线程执行体开始执行时，对应绿色状态

`wait()、sleep()、同步锁定`：该线程代码不继续执行，阻塞在此处，对应红色状态

`notify()、睡醒、解锁`：解除阻塞状态之后，进入就绪等待cpu调度，对应黄色状态

`interrupt()、stop()、自定义中断标识`线程中断或结束之后，立即死亡，不可重新启动，对应灰色状态





----





## 4、线程方法

| 方法                             | 说明                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| `setPriority(int newPriority)`   | 更改线程的优先级，Thread.MIN_PRIORITY=1;Thread.MAX_PRIORITY=10;默认5 |
| `setDamen(boolean on)`           | 设置守护线程，默认false为用户线程，设置为true为守护线程。GC线程是守护线程 |
| `static void sleep(long millis)` | 让当前线程休眠指定毫秒数，存在中断InterruptedException异常，不会释放锁 |
| `void join()`                    | 插队，其它线程阻塞，必须等插队线程执行完                     |
| `static void yield()`            | 礼让，让出cpu执行权暂停执行，重新参与竞争。但下一次未必能让别人执行 |
| `void interrupt()`               | 中断线程，不推荐。建议线程正常停止，若必须强制停止，建议使用外部标识位 |
| `boolean isAlive()`              | 检查线程是否还是存活状态                                     |
| `getState()`                     | 获取线程状态，枚举类型，有6种                                |



```java
//测试观察线程状态
package cusx.tjy.demo2;

public class Thread3 {
    public static void main(String[] args) throws InterruptedException {
        Thread thread = new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try {
                    Thread.sleep(200);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
            System.out.println("======");   //5次循环结束后输出一次
        },"A");

        Thread.State state = thread.getState();
        System.out.println(state);  //NEW
        thread.start();
        state = thread.getState();
        System.out.println(state);  //RUNNABLE
        
        while (state != Thread.State.TERMINATED){
            Thread.sleep(100);  //主线程休眠，让循环的更新频率慢一点
            state = thread.getState();
            System.out.println(state);  //200ns*5期间：输出TIMED_WAITING；线程执行结束之后输出TERMINATED，退出循环。
        }


    }
}
```



-----





## 5、线程同步

并发场景中，多个线程争夺同一资源。因为线程会将资源读取到自己的内存空间执行动作，多个线程彼此之间如果没有同步机制，**会导致各自执行各自的逻辑后，最终资源数据不一致**。

联想到生活中，该问题最直观的解决方式是，排队。

因此在处理多线程问题时，多个线程需同时访问一个对象，就需要**进入该对象的等待池形成队列**，等待前面线程使用完，下一个线程再使用。



队列还需要保证安全，确保不会有访问冲突。此处采用**锁机制Synchronized**。当一个线程获得对象的排它锁，独占资源，其它线程必须等待，直到该线程释放锁。

因此存在的问题也很明显：一个线程持有锁，其它需要该锁的线程都要挂起等待。在多线程竞争下，加锁释放锁会导致较多的上下文切换和cpu调度，引起性能问题。若优先级高的线程需等待优先级低的线程释放锁，会导致优先级倒置，引发性能问题。



> **队列 + 锁 = 线程同步**：保证多线程并发场景的安全



-----





## 6、同步方法

用 `synchronized` 关键字修饰方法，该方法就成为了同步方法，锁的是调用该方法的对象this。

如果是修饰的静态方法，那么锁的是该类的Class类模板。

比如：售票功能是一个Runnable接口实现类，类中的同步方法——sellTicket()中，需要操作成员属性——票的张数，执行减一操作。

此时开启三个窗口线程来卖票，三个窗口都是使用的同一售票功能的sellTicket同步方法，此时锁的对象就是同一个售票功能对象。

![image-20221118201832750](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211182018932.png) 



----





## 7、同步代码块

`synchronized(Obj) { 代码 } `

`Obj`称之为同步监视器，可以是任何对象，但是推荐使用共享资源作为同步监视器。

同步监视器的执行：线程一访问，同步监视器锁定；线程二访问，发现同步监视器被锁，挂起，无法访问；线程一访问结束，同步监视器释放锁；线程二访问，发现同步监视器未被锁定，然后锁定并访问





----





## 8、线程安全集合

![image-20221119103018672](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191030768.png)

```java
package cusx.tjy.demo3;
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.concurrent.TimeUnit;

//线程安全集合测试
public class SynDemo1 {
    public static void main(String[] args) throws InterruptedException {
        CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>();
		//ArrayList<Integer> list = new ArrayList<>();    1998不安全
        for (int i = 0; i < 2000; i++) {
            final int j = i;    //静态变量
            new Thread(()->{
                list.add(j);
            }).start();
        }

        TimeUnit.SECONDS.sleep(2);
        System.out.println(list.size());	//2000
    }
}
```



> 补充1：Lambda表达式访问外部变量
>
> 1. Lambda表达式可以访问给它传递的变量、自己内部定义的变量，同时也能访问它外部的成员变量（实例变量和静态变量）。
>
> 2. 但在访问外部局部变量会出现编译错误：Variable used in lambda expression should be final or effectively final
> 3. 因为外部方法运行结束后，外部局部变量随着弹栈销毁，此时若Lambda表达式中方法未结束、但所访问的变量消失，出现错误
> 4. 所以要求，Lambda表达式访问外部变量时，只能访问成员变量（实例变量和静态变量，在堆中），且不能修改。



> 补充2：static和final的区别
>
> 1. final可以修饰类，方法和变量，static只能修饰方法和变量；
>
> 2. final可以修饰全局变量和局部变量，但是static只能修饰全局变量，不能修饰局部变量；
>
> 3. final不可以修饰代码块，但是static可以修饰代码块。
>
> 全局指的是，类中方法外，成员变量的位置；局部指的是方法中。事实上Java没有全局变量，与此概念对应的是成员变量。
>
> 上面线程安全集合测试代码中，循环中定义的final int j = i;	j是一个不可变的常量，值为i。每次进入循环都会创建新常量。





----





## 9、死锁

多个线程各自占有一些共享资源，并互相等待其它线程释放占有的资源才能执行，而导致大家都无限死等的现象叫死锁。

死锁的四个条件：

- 互斥条件：一个资源每次只能被一个进程使用，使用资源的时候互斥，无交集；
- 保持并请求条件：一个线程因请求资源而阻塞时，但对已获得的资源保持不放；
- 不剥夺条件：进程已获得的资源，在末使用完之前，不能强行剥夺；
- 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系；

根据条件破环死锁：

- 互斥条件：复制资源的副本？
- 请求和保持条件：一个线程一次处理，只能请求一个锁？
- 不剥夺条件：为了防止线程不主动释放资源，设置一个超时时间？
- 循环等待条件：一个线程，处理过程，不可以请求多个锁？

```java
package cusx.tjy.demo3;

class DeadLock {
    static Object lock1 = new Object();
    static Object lock2 = new Object();

    public static void main(String[] args) {

        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (lock1) {
                    System.out.println("thread1 get lock1");
                    try {
                        Thread.sleep(1000);
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                    synchronized (lock2) {
                        System.out.println("thread1 get lock2");
                    }
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (lock2) {
                    System.out.println("thread2 get lock2");
                    synchronized (lock1) {
                        System.out.println("thread2 get lock1");
                    }
                }
            }
        }).start();
    }
}
```



如何检测死锁：

java安装目录/lib/jconsole.exe图形界面可以查

在idea的Temninal中断输入

jps -l    此命令相当于linux下 ps -ef 查看进程

获得进程号后，使用

jstack 进程号

![image-20221119152210140](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191522234.png) 





----





## 10、Lock锁

JUC的locks包下的Lock接口提供比使用synchronized方法和语句可以获得的更广泛的锁定操作。需要手动显示地加锁或解锁。

其中ReentranLock，可重入锁，实现了Lock接口。是常用的控制线程安全的Lock接口实现类。

![image-20221118215124132](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211182151205.png) 



**使用`ReentrantLock`保证抢票问题的安全性**

```java
package cusx.tjy.demo3;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class LockDemo1 {
    public static void main(String[] args) {
        Tickets tickets = new Tickets();
        new Thread(() -> {
            while (tickets.flag) {
                try {
                    TimeUnit.NANOSECONDS.sleep(20);
                    tickets.sellTickets();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "小明").start();

        new Thread(() -> {
            while (tickets.flag) {
                try {
                    TimeUnit.NANOSECONDS.sleep(25);
                    tickets.sellTickets();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "小红").start();

        new Thread(() -> {
            while (tickets.flag) {
                try {
                    TimeUnit.NANOSECONDS.sleep(15);
                    tickets.sellTickets();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "黄牛").start();
    }

}

class Tickets {
    int num = 100;
    boolean flag = true;
    ReentrantLock lock = new ReentrantLock();

    public void sellTickets() {
        lock.lock();
        try {
            if (num <= 0) {
                flag = false;
                System.out.println("票卖完了");
            } else {
                num--;
                System.out.println(Thread.currentThread().getName() + "买到票，还剩" + num + "张票");
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }
}
```



----





**使用`synchronized`保证抢票问题安全性**

```java
package cusx.tjy.demo3;

public class SynDemo3 {
    public static void main(String[] args) {
        Tickets3 tickets = new Tickets3();
        new Thread(() -> {
            while (tickets.flag) {
                tickets.sellTickets();
            }
        }, "小明").start();

        new Thread(() -> {
            while (tickets.flag) {
                tickets.sellTickets();
            }
        }, "小红").start();

        new Thread(() -> {
            while (tickets.flag) {
                tickets.sellTickets();
            }
        }, "黄牛").start();
    }
}

class Tickets3 {
    int num = 100;
    boolean flag = true;
    Object lock = new Object();
    //此处如果用同步方法，锁的是this，即本对象。若小明进入同步方法操作num，锁完后小红和黄牛只能等待小明结束，不会干扰num
    //此处如果用同步代码块，synchronized(lock){代码块}，锁的是lock锁对象，同一个对象的锁对象是同一个
    public synchronized void sellTickets() {
        if (num <= 0) {
            //外部标识位控制线程启停
            flag = false;
            System.out.println("票卖完了");
        } else {
            num--;
            System.out.println(Thread.currentThread().getName() + "买到票，还剩" + num + "张票");
        }
    }
}

```



----



## 11、线程通信

### 生产者消费者问题

生产者往仓库放入产品，消费者从仓库拿出产品。

当仓库产品放满时，通知消费者来拿，生产者等待；

当仓库产品拿完为空时，通知生产者来放，消费者等待。

在本问题中，synchronized或者单纯的Lock已不能解决问题。因为前者只能阻止并发同时更细共享资源，实现资源状态在多个线程处是一致的。但此问题需要进行线程之间的通信，可以使用`wait()`、`notify()`、`notifyAll()`等方法，这三个方法来自`Object`类，它们必须在同步代码块中使用。

#### 管程法

建立一个消费者和生产者发生通信的缓冲区，根据判断缓冲区是空还是满，进而实现解决线程通信

```java
public class ProdAndCons {
    public static void main(String[] args) {
        Storehouse storehouse = new Storehouse(3);
        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                storehouse.produce();
            }
        }, "生产者").start();

        new Thread(() -> {
            for (int i = 0; i < 5; i++) {
                try {
                    TimeUnit.NANOSECONDS.sleep(200);
                    storehouse.consume();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "消费者").start();
    }
}
```

```java
class Storehouse {
    List<Product> productList;
    int capacity;
    public Storehouse(int capacity) {
        this.capacity = capacity;
        productList = new ArrayList<>();
    }
    
    //生产者的放入方法
    public synchronized void produce() {
        //此处使用while循环，是jdk文档推荐的做法。否则会出现虚假唤醒的问题
        while (productList.size() == capacity) {
            //仓库满了，阻塞在这里等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        productList.add(new Product(Thread.currentThread().getName()));
        System.out.println("生产者放入一个产品，现在仓库共有 " + productList.size() + " 个产品");
        //通知消费者
        this.notifyAll();
    }

    //消费者的拿走方法
    public synchronized void consume() {
        while (productList.size() == 0) {
            //仓库空了，阻塞在这里等待
            try {
                this.wait();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            } 
        }
        productList.remove(productList.size() - 1);
        System.out.println("消费者拿走了一个产品，现在仓库共有 " + productList.size() + " 个产品");
        //通知生产者
        this.notifyAll();
    }
}

class Product {
    String name;
    public Product(String name) {
        this.name = name;
    }
}
```

![image-20221119101257668](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191012904.png) 

结果正确，线程安全



> 关于wait()在if中出现**虚假唤醒**的问题
>
> 官方文档解释如下：



![image-20221119094316609](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211190943793.png) 



----



#### 信号灯法

适合生产1个后，立马取1个的模式，根据不断切换的标识位来确定是生产还是消费

```java
package cusx.tjy.demo4;

public class ProdAndCons2 {
    public static void main(String[] args) {
        SightLight sightLight = new SightLight();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                sightLight.put();
            }
        },"生产者").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                sightLight.take();
            }
        },"消费者").start();

    }
}
```

```java
class SightLight{
    //信号灯，true拿，false放
    boolean light = true;
    public synchronized void take(){
        //如果信号灯是false，需要生产者放，消费者等待
        while (!light){
            try {
                this.wait();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
        System.out.println(Thread.currentThread().getName() + "拿---");
        light = !light;     //切换信号灯
        this.notifyAll();

    }
    
    public synchronized void put(){
        //如果信号灯是true，需要消费者拿，生产者等待
        while (light){
            try {
                this.wait();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }
        System.out.println(Thread.currentThread().getName() + "放");
        light = !light;     //切换信号灯
        this.notifyAll();
    }
}
```

![image-20221119101109081](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191011160.png) 

结果正确，存取交替进行，线程安全



----







## 线程池

频繁创建和销毁线程的性能开销大，因此可以提前创建多个线程放在线程池中，用时拿走，不用时还，重复利用。可以提高响应速度，降低资源消耗，方便线程管理。



**ExecutorService**是JUC提供的一个异步执行的框架，通过使用ExecutorService可以方便的创建多线程执行环境。

![image-20221119103646451](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191036544.png)  



**Executors**是一个工厂工具类，可以创建ExecutorService对象

![image-20221119104058539](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191040645.png) 







线程池使用：

```java
//创建executorService执行类
//Executors中的静态方法newFixedThreadPool可以创建一个线程池，参数是线程池中线程的数量
ExecutorService executorService = Executors.newFixedThreadPool(5);

//执行类调用从父类Executor继承来的execute方法，启动线程。execute方法的参数是Runnable接口实现类
executorService.excute(()->{System.out.println(Thread.currentThread().getName());});

//关闭连接
executorService.shutdown();
```

![image-20221119104659573](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191046695.png) 





# JUC

## 1、什么是JUC

>  **JUC就是java.util.concurent包，它为Java提供了并发编程的支持**

![image-20221118163947321](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181639373.png) 



> **Callable接口和Lock接口就来自JUC**

![image-20221118164654383](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181646446.png) 





----





## 2、JUC.locks包下的Lock

![image-20221119111914048](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191119135.png) 



**ReentrantLock**

![image-20221119112348544](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191123640.png) 



> 公平锁：先来后到，不能插队
>
> 非公平锁：可以插队，默认是这样。允许cpu调度，这样可以防止性能倒置。



**使用lock时的固定格式**

```java
lock.lock();	//加锁

try{
    //业务代码
    
} catch(){
    
} finally{
    lock.unlock();	  //解锁
}

```



**synchronized和Lock的区别（重要）**

- `synchronized`是关键字；`Lock`是一个接口
- `synchronized`无法判断和获取锁的状态；`Lock`可以通过`isLocked()`判断是否上锁
- `synchronized`是隐式地上锁和解锁；`Lock`是显式地手动上锁和解锁，如果不解锁会死锁！
- `synchronized`上锁后，其它线程只会死等；`Lock`上锁之后，其它线程会使用`tryLock()`尝试获取锁
- `synchronized`可重入，非公平，不可中断；`Lock`可重入，可以设置公平非公平，可以判断锁的状态
- `synchronized`适合锁少量代码；`Lock`适合锁大量代码



<center class="half">
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191140278.png" width="435"/>
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191141121.png" width="505"/>
</center>



> synchronized关键字锁不了整个业务 demo

```java
package cusx.tjy.demo5;

import java.util.concurrent.TimeUnit;

public class SynchronizedDemo1 {
    public static void main(String[] args) {
        Account account = new Account(2000);
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    TimeUnit.NANOSECONDS.sleep(20);
                    account.add(200);   //存200
                    int remain = account.remain();   //查余额
                    System.out.println("小红存了 200 ，余额还有："+remain);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        },"小红").start();

        new Thread(()->{
            for (int i = 0; i < 8; i++) {
                try {
                    TimeUnit.NANOSECONDS.sleep(20);
                    account.take(500);
                    int remain = account.remain();
                    System.out.println("小明取了 500 ，余额还有："+remain);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }            }
        },"小明").start();

    }
}
```

```java
class Account {
    volatile int curMoney;
    public Account(int money) {
        this.curMoney = money;
    }
    public synchronized void add(int money) {
        this.curMoney += money;
    }
    public synchronized void take(int money) {
        if (this.curMoney < money) {
            throw new RuntimeException("余额不足");
        }
        this.curMoney -= money;
    }

    public synchronized int remain() {
        return this.curMoney;
    }

}
```

![image-20221119135725289](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191357397.png) 



> 解决本问题的方式其实很多，比如可以用线程通信`wait()`和`notify()`此类机制。
>
> 但此处使用Lock锁住整个账户的方式，让一个时刻只能有一个线程操作其中的业务。（此代码有BUG，只做思路参考）

```java
package cusx.tjy.demo5;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class SynchronizedDemo1 {
    public static void main(String[] args) {
        Account account = new Account(2000);
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                try {
                    account.lockAccount();
                    TimeUnit.NANOSECONDS.sleep(55);
                    account.add(200);   //存200
                    int remain = account.remain();   //查余额
                    System.out.println("小红存了 200 ，余额还有：" + remain);
                    account.unlockAccount();
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "小红").start();

        new Thread(() -> {
            for (int i = 0; i < 8; i++) {
                try {
                    account.lockAccount();//要执行时锁账户
                    TimeUnit.NANOSECONDS.sleep(20);
                    account.take(500);
                    int remain = account.remain();
                    System.out.println("小明取了 500 ，余额还有：" + remain);
                    account.unlockAccount();//执行结束解锁账户
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        }, "小明").start();

    }
}
```

```java
class Account {
    volatile int curMoney;
    ReentrantLock lock;

    public Account(int money) {
        this.curMoney = money;
        this.lock = new ReentrantLock();
    }

    public void lockAccount() {
        lock.lock();	//锁账户
    }

    public void unlockAccount() {
        lock.unlock();	  //解锁账户
    }

    public synchronized void add(int money) {
        this.curMoney += money;
    }

    public synchronized void take(int money) {
        if (this.curMoney < money) {
            unlockAccount();    //解锁瞬间就被别的线程抢走，抛异常的时间有延迟，有bug，还是不太安全
            throw new RuntimeException("余额不足");
        }
        this.curMoney -= money;
    }

    public synchronized int remain() {
        return this.curMoney;
    }
}
```





----







## 3、JUC.locks包下的Condition

`Condition`是JUC的locks包下的接口，

可以通过该包下的`Lock`接口的`newCondition()`方法获得`Condition`接口实现类，

再调用它专用的`await()`、`signalAll()`等方法进行线程通信。

除此之外！还可以精准唤醒某个线程。



![image-20221119144105082](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191441243.png) 



**JUC下的线程通信用的是Condition**

Condition.await()    ===     Object.wait() 

Condition.signal()   ===     Object.notify() 

Condition.signalAll()   ===     Object.notifyAll() 



**具体使用**

```java
package cusx.tjy.demo5;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class Demo1 {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data.add();
            }
        }, "A").start();
        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data.sub();
            }
        }, "B").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data.add();
            }
        }, "C").start();

        new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                data.sub();
            }
        }, "D").start();
    }

}
```

```java
class Data {
    private int number = 0;
    private ReentrantLock lock = new ReentrantLock();
    Condition condition = lock.newCondition();	//获得Condition实现类对象

    public void add() {
        lock.lock();
        try {
            while (number != 0) {
                condition.await();
            }
            number++;
            System.out.println(Thread.currentThread().getName()+ "=>" + number);
            condition.signalAll();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }

    public void sub() {
        lock.lock();
        try {
            while (number == 0){
                condition.await();//等价于this.wait()
            }
            number--;
            System.out.println(Thread.currentThread().getName()+ "=>" + number);
            condition.signalAll();//等价于this.notifyAll()
        }catch (InterruptedException e){
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }
}
```



**利用`Condition`精准唤醒**

```java
package cusx.tjy.demo5;

import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Demo2 {
    public static void main(String[] args) {
        MyData myData = new MyData();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                myData.printA();
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                myData.printB();
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                myData.printC();
            }
        },"C").start();

    }
}

class MyData {
    private int num = 1;
    private Lock lock = new ReentrantLock();
    //精准唤醒，每个Condition都对应一个线程
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();


    public void printA(){
        lock.lock();
        try {
            while (num != 1){
                condition1.await();
            }
            System.out.println("AAA");
            //精准唤醒打印B的线程
            num = 2;
            condition2.signal();
        } catch (Exception e){
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }

    public void printB(){
        lock.lock();
        try {
            while (num != 2){
                condition2.await();
            }
            System.out.println("BBB");
            //精准唤醒打印A的线程
            num = 3;
            condition3.signal();
        } catch (Exception e){
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }

    public void printC(){
        lock.lock();
        try {
            while (num != 3){
                condition3.await();
            }
            System.out.println("CCC");
            //精准唤醒打印B的线程
            num = 1;
            condition1.signal();
        } catch (Exception e){
            throw new RuntimeException(e);
        } finally {
            lock.unlock();
        }
    }
}
```





-----







## 4、JUC.locks包下的ReadWriteLock

只有一个实现类`ReentranReadWriteLock`

![image-20221119174057267](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191740392.png) 



```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class demo4 {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.put(temp+"",temp+"");
            },String.valueOf(i)).start();
        }

        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(()->{
                myCache.get(temp+"");
            },String.valueOf(i)).start();
        }
    }
}
```



```java
class MyCache{
    private volatile Map<String,Object> map = new HashMap<>();
    //读写锁，控制粒度更细
    private ReentrantReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    //如果使用synchronized或者普通的lock，锁的是都是同一个对象，就不能两条线程同时读和写

    //存-写：写入时，只希望同时只有一个线程-安全-独占锁
    public void put(String key,Object value){
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "写入" +key);
            map.put(key,value);
            System.out.println(Thread.currentThread().getName() + "写入OK");
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            readWriteLock.writeLock().unlock();
        }

    }

    //拿-读：读出时，可以有多个线程读-性能-共享锁
    public void get(String key){
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "读取" +key);
            Object o = map.get(key);
            System.out.println(Thread.currentThread().getName() + "读取OK");
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            readWriteLock.readLock().unlock();
        }
    }

}
```







## 5、JUC包下的集合类

**List不安全**

并发场景下，使用普通集合类，会报并发修改异常

```java
package cusx.tjy.demo6;

import java.util.ArrayList;
import java.util.UUID;

public class demo1 {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<>();
        for (int i = 0; i < 100; i++) {
            new Thread(() -> {
                //ConcurrentModificationException 并发修改异常
                list.add(UUID.randomUUID().toString().substring(0, 4));
                System.out.println(list);
            }).start();
        }
    }
}
```

![image-20221119155210506](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191552669.png) 





**解决方案：**

- `Vector`，默认线程安全。但是Vector很早就出现了，现在已经不常用了。

- 集合工具类`Collections.synchronizedList(list);`可以将普通集合变成线程安全的集合

- 使用JUC包下的线程安全集合类

  ![image-20221119155905735](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191559857.png) 



**`CopyOnWriteArrayList` 写时复制——COW——计算机程序设计的一种优化策略。**

多个线程在访问同一个`list`时，读没有冲突，但是写会覆盖原来的内容，触发数据安全问题。

所以COW就是在写的时候复制一份，在副本上写，最后合并回去，避免覆盖导致的数据问题。

![image-20221119160922951](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191609067.png) 





还有一种**读写分离**的思想，多用于数据库，与COW有类似之处。此处不多介绍。





**Set和Map不安全**

都可以通过使用JUC提供的并发安全类来解决。

Set   ===> CopyOnWriteArraySet

Map ===> ConcurrentHashMap<K,V>

与CopyOnWriteArrayList同理



----





## 6、JUC包下的Callable接口

![image-20221119161921327](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191619435.png) 

```java
import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

//1.定义线程类实现Callable接口，Callable的泛型是返回值的类型
//2.重写线程执行体call()方法，有返回值
//3.创建FutureTask对象，泛型是返回值类型，并丢入是Callable接口实现类
//4.创建线程类，丢入FutureTask对象，启动线程类
//5.通过FutureTask对象调用get()方法可获得返回值，需处理异常
public class Thread4 implements Callable<String> {

    public static void main(String[] args) {
        FutureTask<String> futureTask = new FutureTask<String>(new Thread4());
        new Thread(futureTask).start();
        try {
            String res = futureTask.get();
            System.out.println(res);	//Thread-0
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } catch (ExecutionException e) {
            throw new RuntimeException(e);
        }
    }

    @Override
    public String call() throws Exception {
        return Thread.currentThread().getName();
    }
}
```









## 7、JUC包下的三大辅助类

### CountDownLatch

理解为减法计数器

![image-20221119162934904](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191629049.png) 

```java
import java.util.concurrent.CountDownLatch;

public class demo1 {
    public static void main(String[] args) throws InterruptedException {
        //参数是总数
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + " GO OUT");
                countDownLatch.countDown();
            },String.valueOf(i)).start();
        }
        countDownLatch.await(); //在这里等待，直到计数器归零之后，才被唤醒，继续往下执行
        System.out.println("Close Door");

    }
}
```

![image-20221119163615797](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191636894.png) 





### CyclicBarrier

理解为加法计数器

![image-20221119163913235](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191639335.png) 

```java
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class demo2 {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
            System.out.println("召唤神龙成功");
        });

        for (int i = 1; i <= 7; i++) {
            final int temp = i;
            new Thread(() -> {
                System.out.println(Thread.currentThread().getName() + "集齐了 " + temp + " 颗龙珠");
                try {
                    cyclicBarrier.await();  //可以理解成加法计数器，上限是7。在此处等待，直到执行了7个线程时，被唤醒，开启它内部的那个线程执行
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                } catch (BrokenBarrierException e) {
                    throw new RuntimeException(e);
                }
            }).start();
        }
    }
}
```

![image-20221119164506544](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191645646.png) 





### Semaphore

`acquire()`获得，如果已满则等待，等到别人释放有空为止。

`release()`释放，会将当前信号量+1，表示多了一个空位，然后唤醒其它等待的线程。

作用：多个共享资源互斥的使用，并发限流，控制最大的线程数量。

![image-20221119165307003](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191653129.png) 

```java
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class demo3 {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                //acquire() 得到
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+" 抢到车位");
                    TimeUnit.NANOSECONDS.sleep(200);
                    System.out.println(Thread.currentThread().getName()+" 离开车位");
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                } finally {
                    semaphore.release();
                }
                //release() 释放
            },String.valueOf(i)).start();
        }
    }
}
```

![image-20221119165139772](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191651885.png) 



-----



## 8、JUC包下的阻塞队列

`BlockingQueue`一般在多线程、线程池里面使用

![image-20221119180353885](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191803015.png) 



`BlockingQueue`的在集合体系里的位置

<center class="half">
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191812220.png" width="570"/>
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191822945.png" width="380"/>
</center>



**阻塞队列的具体使用**：添加、移除

| 方式     | 抛出异常  | 不抛异常，有返回值 | 等待  | 超时等待                           |
| -------- | --------- | ------------------ | ----- | ---------------------------------- |
| 添加     | add()     | offer()            | put() | offer(long timeout, TimeUnit unit) |
| 移除     | remove()  | poll()             | get() | poll(long timeout, TimeUnit unit)  |
| 队首元素 | element() | peek()             |       |                                    |

```java
//抛出异常
private static void test1() {
    ArrayBlockingQueue<Object> queue = new ArrayBlockingQueue<>(3);
    System.out.println(queue.add("aaa"));
    System.out.println(queue.add("bbb"));
    System.out.println(queue.add("ccc"));
    //        Queue full
    //        System.out.println(queue.add("ddd"));

    System.out.println("----------");

    System.out.println(queue.remove());
    System.out.println(queue.remove());
    System.out.println(queue.remove());
    //        NoSuchElementException
    //        System.out.println(queue.remove());
    //        队首元素，没有元素会抛异常
    //        System.out.println(queue.element());
}
```

```java
//不抛异常，有返回值
private static void test2() {
    ArrayBlockingQueue<Object> queue = new ArrayBlockingQueue<>(3);
    System.out.println(queue.offer("aaa"));
    System.out.println(queue.offer("bbb"));
    System.out.println(queue.offer("ccc"));
    System.out.println(queue.offer("ddd"));   //false

    System.out.println("----------");
    System.out.println(queue.poll());
    System.out.println(queue.poll());
    System.out.println(queue.poll());
    System.out.println(queue.poll());   //null
    System.out.println(queue.peek());   //队首元素，不抛异常，null
}
```

```java
//死等
private static void test3() throws InterruptedException {
    ArrayBlockingQueue<Object> queue = new ArrayBlockingQueue<>(3);
    queue.put("aaa");
    queue.put("bbb");
    queue.put("ccc");
    //        queue.put("ddd");   //一直死等

    System.out.println("--------");
    System.out.println(queue.take());
    System.out.println(queue.take());
    System.out.println(queue.take());
    //        System.out.println(queue.take());   //一直死等
}
```

```java
//超时等待
private static void test4() throws InterruptedException {
    ArrayBlockingQueue<Object> queue = new ArrayBlockingQueue<>(3);
    queue.offer("aaa");
    queue.offer("bbb");
    queue.offer("ccc");
    //        queue.offer("ddd", 2,TimeUnit.SECONDS);   //超时2秒就不等了

    System.out.println("--------");
    System.out.println(queue.poll());
    System.out.println(queue.poll());
    System.out.println(queue.poll());
    System.out.println(queue.poll(2,TimeUnit.SECONDS));   //超时2秒就不等了
}
```



**`SynchronousQueue`同步队列是放一个拿一个**

```java
import java.util.concurrent.SynchronousQueue;
import java.util.concurrent.TimeUnit;

public class demo2 {
    public static void main(String[] args) {
        SynchronousQueue<String> queue = new SynchronousQueue<>();

        new Thread(()->{
            try {
                System.out.println(Thread.currentThread().getName() + " put 1");
                queue.put("1");
                System.out.println(Thread.currentThread().getName() + " put 2");
                queue.put("2");
                System.out.println(Thread.currentThread().getName() + " put 3");
                queue.put("3");
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        },"T1").start();

        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName() + " take " + queue.take());
                TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName() + " take " + queue.take());
                TimeUnit.SECONDS.sleep(1);
                System.out.println(Thread.currentThread().getName() + " take " + queue.take());
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        },"T2").start();
        
    }
}
```



## 9、线程池（重点）

**池化技术：**程序一旦运行，就会占用系统资源。因此我们需要优化资源的使用，提出了池化技术。线程池，JDBC连接池...

**池化技术关键指标：**

- 默认容量：
- 最大容量：



**线程池的好处：**

1. 降低资源的消耗，线程复用
2. 提高响应速度，频繁创建销毁线程性能开销大
3. 方便管理，可以控制最大并发数



**阿里巴巴开发手册里强制要求：**

线程池不允许使用`Executors`创建，而是要用`ThreadPoolExecutor`创建

`Executors`返回的线程池对象的弊端如下：

- `FixedThreadPool`和`SingleThreadPool`

  允许的请求队列长度为Integer.MAX_VALUE，21亿！！！可能会堆积大量的请求，从而导致OOM；

- `CachedThreadPool`和`ScheduledThreadPool`

  允许的创建线程数量为Integer.MAX_VALUE，21亿！！！可能会创建大量的线程，从而导致OOM；





### 三大方法

```java
//使用线程池创建并启动线程的代码
try {
    for (int i = 0; i < 10; i++) {
        threadPool.execute(()->{
            System.out.println(Thread.currentThread().getName() + " ok!");
        });
    }
} catch (Exception e) {
    throw new RuntimeException(e);
} finally {
    // 线程池用完，程序结束，关闭线程池
    threadPool.shutdown();
}
```



#### 1、`newSingleThreadExecutor()`

```java
//单个线程的线程池
ExecutorService threadPool = Executors.newSingleThreadExecutor();
```

![image-20221119193303571](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191933718.png) 



#### 2、`newFixedThreadPool(5)`

```java
//创建固定大小的线程池
ExecutorService threadPool = Executors.newFixedThreadPool(5);
```

![image-20221119193603200](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191936447.png) 





#### 3、`newCachedThreadPool()`

```java
//可伸缩的线程池
ExecutorService threadPool = Executors.newCachedThreadPool();
```

![image-20221119193756143](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211191937260.png) 



### 七大参数

```java
public ThreadPoolExecutor(int corePoolSize,		//核心线程池大小
                          int maximumPoolSize,		//最大容量
                          long keepAliveTime,		//空闲线程存活时间，超时没人调用的话，就会释放
                          TimeUnit unit,	//超时单位
                          BlockingQueue<Runnable> workQueue,	//阻塞队列，
                          ThreadFactory threadFactory,		//线程工厂：创建线程的，不用动它
                          RejectedExecutionHandler handler) 	//拒绝策略
{
    if (corePoolSize < 0 ||
        maximumPoolSize <= 0 ||
        maximumPoolSize < corePoolSize ||
        keepAliveTime < 0)
        throw new IllegalArgumentException();
    if (workQueue == null || threadFactory == null || handler == null)
        throw new NullPointerException();
    this.acc = System.getSecurityManager() == null ?
        null :
    AccessController.getContext();
    this.corePoolSize = corePoolSize;
    this.maximumPoolSize = maximumPoolSize;
    this.workQueue = workQueue;
    this.keepAliveTime = unit.toNanos(keepAliveTime);
    this.threadFactory = threadFactory;
    this.handler = handler;
}
```

![image-20221119201326665](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192013843.png) 





### 四种拒绝策略

- AbortPolicy：不处理，并抛出异常RejectedExecutionException
- CallerRunsPolicy；哪里来的去哪里，打发走了 
- DiscardPolicy：阻塞队列满了，会丢掉任务，但不会被抛出异常
- DiscardOldestPolicy：阻塞队列满了，尝试和阻塞队列的第一个人竞争，也不会抛出异常



### 手动创建线程池

使用`ThreadPoolExecutor`手动创建线程池

```java
package cusx.tjy.demo8;

import java.util.concurrent.*;

public class demo2 {
    public static void main(String[] args) {

        ExecutorService threadPool = new ThreadPoolExecutor(2,
                5,
                2,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.DiscardOldestPolicy());
        
        //四种拒绝策略
        //AbortPolicy：不处理，并抛出异常RejectedExecutionException
        //CallerRunsPolicy；哪里来的去哪里，打发走了，此处是main线程处理， main ok!
        //DiscardPolicy：阻塞队列满了，会丢掉任务，但不会被抛出异常
        //DiscardOldestPolicy：阻塞队列满了，尝试和阻塞队列的第一个人竞争，也不会抛出异常
        try {
            for (int i = 0; i < 9; i++) {
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName() + " ok!");
                });
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            // 线程池用完，程序结束，关闭线程池
            threadPool.shutdown();
        }
    }
}
```





### 最大线程到底该如何定义？

CPU密集型：设备是几核，就设置几个线程，可以保持CPU的效率最高

IO密集型：判断出耗IO的线程数是多少，设置比它高，一般设为它的2倍





## 10、四大函数式接口（必备）

新时代程序员需要掌握：Lambda表达式，Stream流式计算，函数式接口，链式编程

**`@FunctionalInterface`函数式接口**：只有一个方法的接口。典型例子Callable

```java
@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}
```



### `Function<T,R>`函数型接口

<center class="half">
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192026816.png" width="500"/>
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192031820.png" width="450"/>
</center>





### `Predicate<T>`断定型接口

<center class="half">
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192041881.png" width="470"/>
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192038172.png" width="475"/>
</center>



### `Consumer<T>`消费型接口

<center class="half">
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192040141.png" width="430"/>
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192043818.png" width="525"/>
</center>





### `Supplier<T>`供给型接口

<center class="half">
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192045728.png" width="430"/>
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211192046380.png" width="515"/>
</center>





## 11、Stream流式计算

什么是Stream流式计算？