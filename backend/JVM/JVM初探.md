# JVM所处位置与体系结构

## JVM所处位置

JDK：Java开发工具包=JRE+编译器解释器文档生成器等

JRE：Java运行环境=JVM+Java核心类库等

JVM：Java虚拟机 -> Java程序一次编译、到处运行。本质上是通过javac命令编译生成class字节码文件后，不同平台的JVM将该份class文件翻译为对应平台的机器码。

<center class="half">
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181008585.png" width="400"/>
    <img src="https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181026013.png" width="440"/>
</center>





------



 ## JVM基本体系结构



![image-20221118105500547](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181055641.png) 



# 类加载器Class Loader

作用：加载和初始化Class文件，变成Class模板类



![image-20221118113734953](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181137269.png) 



## 类加载器的类型

Bootstrap class loader：启动类加载器，底层是用C++实现的，没有父加载器。/jre/lib/rt.jar

Extension class loader：扩展类加载器，负责加载JDK中一些特殊的模块。jdk1.9开始叫Platform class loader。/jre/lib/ext/*.jar

Application class loader：应用程序类加载器，也称System class loader，负责加载用户类路径上所指定的类库。

还有自定义的用户加载器

## 类加载器的关系

![image-20221118144630975](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181446106.png) 



```java
public class Car {
    public String name;

    public Car(String name) {
        this.name = name;
    }

    public static void main(String[] args) {
        Car car1 = new Car("奇瑞");
        Car car2 = new Car("宝马");
        Car car3 = new Car("大众");
        System.out.println(car1.hashCode());
        System.out.println(car2.hashCode());
        System.out.println(car3.hashCode());

        Class<? extends Car> aClass1 = car1.getClass();
        Class<? extends Car> aClass2 = car2.getClass();
        Class<? extends Car> aClass3 = car3.getClass();
        System.out.println(aClass1.hashCode());
        System.out.println(aClass2.hashCode());
        System.out.println(aClass3.hashCode());

        ClassLoader first = aClass1.getClassLoader();
        System.out.println(first);
        ClassLoader second = first.getParent();
        System.out.println(second);
        ClassLoader third = second.getParent();
        System.out.println(third);
    }
}

```

![image-20221118114850383](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181148470.png) 



# 双亲委派机制

- 在加载类时，各级加载器都会依次向上委派给父类加载器，APP->EXT->BOOT。

- 交给BOOT加载器后，如果扫描到该类，则进行加载，完毕；

- 如果没有该类，则抛出异常，向下交付，由下一级加载器扫描加载；

- 否则，以此类推，直到加载完毕。

```java
package java.lang;
//自己写一个java.lang包，新建String类
public class String {
    @Override
    public String toString() {
        return "hello";
    }
    public static void main(String[] args) {
        String s = new String();	
        //此处加载String类时，会依次向上委派父级加载器，APP->EXT->BOOT
        //最终由bootstrap加载器找到rt.jar包下的原生String类进行加载
        //而不是直接加载本次自己写的String类
        s.toString();
    }
}
```

![image-20221118145854668](https://richdogepic.oss-cn-hangzhou.aliyuncs.com/img/202211181458746.png) 





# 沙箱安全机制

沙箱安全机制是java安全模型的核心，它严格限制现在运行在jvm上面的代码对系统资源的访问。系统资源包括 cpu、内存、网络等。

现在的安全模型就是，JVM给不同的代码分配不同的域，该代码就拥有这个域所拥有的对于本地资源的全部权限。

沙箱安全机制的组成部分：

1. 字节码校验器：它保证java代码符合java语言规范，核心类由于已经校验过了封装好的，字节码不会校验核心类

2. 类加载器，类加载器是利用了双亲委派机制，它保证了好的代码不会被坏的代码污染 它定义了被信任类库的边界 为代码归入域（类似于分配角色，分配权限）



# Native（亮点）

native：本地方法关键字。如用Java驱动打印机，native Robot()

- 被native修饰的方法，说明Java作用域达不到，需要调用C语言的库
- 然后会进入本地方法栈，登记native方法，调用JNI本地方法接口执行
- 优点:可以扩展Java自身不便完成的功能，融合其它编程语言的功能

其它调用方式：Socket，http等



# PC寄存器

程序计数器，每一个线程都有一个程序计数器，是线程私有的。比如：每一次读下一条指令时就会加一，占用空间小。



# 方法区

方法区是所有线程共享的。当JVM加载某个类时，会读入该类对应的字节码.class文件。

并将类的静态结构-常量、方法、字段、接口描述等存入方法区中（永生代），1.8及以后常量加载到堆中。

类的实例信息存在堆内存中

类实例对象的引用是在栈内存中

即 实例化对象并执行对象方法 过程中，需要互相配合。





# 虚拟机栈

栈内存Stack，主管程序的运行，从main方法开始，生命周期与线程同步。线程结束时，栈内存弹出释放。因此对于栈而言没有GC。

栈内存中存放：8大基本类型，对象的引用，实例调用的的方法

栈内存问题：StackOverFlow栈内存溢出



# 堆

一个JVM只有一个堆，堆内存大小可调节。

堆内存中存放：实例对象，全局变量



