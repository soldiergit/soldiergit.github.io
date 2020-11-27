---
title: 面试题—请谈谈你对volatile的理解
tags: [面试题, Java面试题, JMM, volatile]
index_img: /resource/img/thread_visit_memory.png
date: 2020-11-03 10:44:24
---

## volatile是java虚拟机提供的轻量级的同步机制
1. 保证可见性
2. <font color=#FF000>不保证原子性</font>
3. 禁止命令重排
首先我们要懂得：JVM是java虚拟机、JMM是java内存模型。JMM（java内存模型Java Memory Model，简称JMM）本身是一个抽象的概念<font color=#FF000>并不真是存在</font>，它描述的是一组规则或规范，通过这组规范定义了程序中各个变量（包括实例字段，静态字段和构成数组对象的元素）的访问方式。

***JMM关于同步的规定：***
**1.** 线程解锁前，必须把共享变量的值刷新回主内存
**2.** 线程加锁前，必须读取主内存的最新值到自己的工作内存
**3.** 加锁解锁是同一把锁

由于JVM运行程序的实体是线程，而每个线程创建时JVM都会为其创建一个工作内存（有些地方称为栈空间），工作内存是每个线程的私有数据区域，而Java内存模型（JMM）中规定所有的变量都存储在<font color=#FF000>主内存</font>，主内存是共享内存区域，所有线程的可以访问，<font color=#FF000>但线程对变量的操作（读取赋值等）必须在工作内存中进行，首先要将变量从主内存拷贝到自己的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存</font>，不能直接操作主内存中的变量，各个线程中的工作内存中存储着主内存中的<font color=#0000F>变量副本拷贝</font>，因此不同的线程间无法访问对方的工作内存，线程间的通信（传值）必须通过主内存来完成，其简要访问过程如下图：
![](/resource/img/thread_visit_memory.png)

## JMM你谈谈
### JMM的可见性
 - 假设主内存中有一个Student对象，它的age = 25，现在有3个线程（t1、t2、t3）要操作age，3个线程首先将25拷贝回自己的工作内存（变量拷贝），线程t1在自己的工作内存中将age改为了37，之后t1将这个37写回主内存中，而此时此刻主内存的age是37 线程t2、t3的age是25
 - 我们必须要有一种机制，只要有一个线程修改完自己工作内存的值并写回主内存，要及时通知其它线程，而这个及时通知就是<font color=#FF000>JMM内存模型的第一种特性：可见性</font>，只要有变动，大家立马可见，知道最新消息。
 - 通过前面对JMM的介绍，我们知道：各个线程对主内存中共享变量的操作都是各个线程各自拷贝到自己的工作内存进行操作后再写回到主内存中的。这就可能存在一个线程A修改了共享变量X的值但还未写回主内存中时，另一个线程B又对主内存中的同一个共享变量X进行操作，但此时A线程工作内存中共享变量X对线程B来说是不可见的，这种工作内存与主内存同步延迟现象就造成了可见性问题。

### VolatileDemo代码演示可见性
```java
import java.util.concurrent.TimeUnit;

class MyData {
    volatile int number = 0;

    public void add() {
        this.number = 60;
    }
}

/**
 * 1、验证volatile的可见性
 *  1.1 假设 int number = 0;，number变量之前根本没有添加volatile关键字修饰
 */
public class VolatileDemo {

    // main方法本身算一个main线程
    public static void main(String[] args) {
        MyData myData = new MyData();

        // 创建一个新线程
        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t come in");
            // 暂停3秒钟后改变number的值
            try {
                TimeUnit.SECONDS.sleep(3);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            myData.add();
            System.out.println(Thread.currentThread().getName() + "\t update number value：" + myData.number);
        }, "AAA").start();


        // 第二个线程就是我们的main线程了
        while (myData.number == 0) {
            // main线程会一直在这里等待循环，直到number不为0
        }
        System.out.println(Thread.currentThread().getName() + "\t mission is over，main get number value：" + myData.number);
    }
}
```

### 原子性
<font color=#FF000>**number++在多线程下是非线程安全的**</font>：因为该操作不具备原子性。
**原子性**是指不可分割，完整性，即某个线程正在做某个业务时，中间不可以被加塞或者被分割。需要整体完整，要么同时成功，要么同时失败。

### VolatileDemo不保证原子性代码演示
```java
import java.util.concurrent.TimeUnit;

class MyData {
    volatile int number = 0;

    // 注意：此时number前面是加了volatile关键字修饰的，volatile不保证原子性
    public void addPlus() {
        number++;
    }
}

/**
 * 2、验证volatile不保证原子性
 *  1.1 原子性指的是什么？
 *      不可分割，完整性，即某个线程正在做某个业务时，中间不可以被加塞或者被分割。需要整体完整，要么同时成功，要么同时失败。
 */
public class VolatileDemo {

    // main方法本身算一个main线程
    public static void main(String[] args) {

        MyData myData = new MyData();

        // 20个线程，每个线程++1000次，理想值是20000
        for (int i = 1; i <= 20; i++) {
            new Thread(() -> {
                for (int j = 1; j <= 1000; j++) {
                    myData.addPlus();
                }
            }, String.valueOf(i)).start();
        }

        // 需要等待上面20个线程都全部计算完成后，再用main线程取得最终结果值
        while (Thread.activeCount() > 2) { // 返回活动线程的当前线程的线程组中的数量
            Thread.yield(); // 声明当前线程已经完成了生命周期中最重要的部分，可以切换给其它线程来执行。该方法只是对线程调度器的一个建议，而且也只是建议具有相同优先级的其它线程可以运行。
        }
        System.out.println(Thread.currentThread().getName() + "\t finally number value：" + myData.number);

    }
}
```
***<font color=#FF000>为什么volatile不保证原子性？</font>***
&nbsp;&nbsp;因为会出现丢失写值的情况，一个线程在写会主内存时，其它线程被挂起，而volatile保证可见性，主内存还没来得及通知其它线程，另一个线程就它自己工作内存的值写进来了。

### 有序性
&nbsp;&nbsp;计算机在执行程序时，为了提高性能，编译器和处理器常常会对<font color=#FF000>指令做重排</font>，一般分为一下3种：
![](/resource/img/JMM_rearrangement.png)
&nbsp;&nbsp;**1、** 单线程环境里面确保程序最终执行结果和代码顺序执行的结果一致。即<font color=#FF000>单线程环境下不用担心指令重排</font>。
&nbsp;&nbsp;**2、** 处理器在进行重排序时必须要考虑指令之间的<font color=#FF000>**数据依赖性**</font>
&nbsp;&nbsp;**3、** 多线程环境中线程交替执行，由于编译器优化重排的存在，两个线程中使用的变量能否保证一致性是无法确定的，结果无法预测
比如说：现有 int a,b = 0;
按顺序，线程t1执行 a = b，线程2执行 b = 2;
理论上的结果是：a=0 b=2
由于在多线程环境下，编译器会自己做优化，对你写的代码进行重排序，会导致 a=2 b=2

### 线程安全性保证
**1、** 工作内存与主内存同步延迟现象导致的可见性问题：
&nbsp;&nbsp;可以使用synchronized或volatile关键字解决，它们可以是一个线程<font color=#FF000>修改后的变量立即对其它线程可见</font>。
**2、** 对于指令重排导致的可见性问题和有序性问题：
&nbsp;&nbsp;可以利用volatile关键字解决，因为volatile的另一个作用就是禁止重排序优化。

## 你在哪些地方用过volatile
### 单例模式DCL代码
```java
public class SingletonDemo {

    // 单例模式
    public static volatile SingletonDemo instance = null;

    public SingletonDemo() {
        System.out.println(Thread.currentThread().getName() + "\t 我是构造函数SingletonDemo()");
    }

    // DCL（Double Check Lock）:双端检锁机制
    public static SingletonDemo getInstance() {
        if (instance == null) {
            // 没加volatile前 可能打印：2   我是构造函数SingletonDemo()
            // 加了volatile后 只会打印：1   我是构造函数SingletonDemo()
            synchronized (SingletonDemo.class) {
                if (instance == null) instance = new SingletonDemo();
            }
        }
        return instance;
    }

    public static void main(String[] args) {
        // 单线程（main线程的操作动作....） 构造函数只会执行一次
//        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
//        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());
//        System.out.println(SingletonDemo.getInstance() == SingletonDemo.getInstance());

        // 并发多线程后，情况发生了很大的变化
        for (int i = 1; i <= 10; i++) {
            new Thread(() -> {
                SingletonDemo.getInstance();
            }, String.valueOf(i)).start();
        }
    }
}
```

### 单例模式volatile分析
**<font color=#FF000>DCL（Double Check Lock双端检锁机制）</font>** 不一定线程安全，原因是<font color=#FF000>有指令重排序的存在</font>，加入volatile可以禁止指令重排。
原因在于某一个线程执行到第一次检测，读取到的instance不为null时，instance的引用对象可能还没完成初始化。
**instance = new SingletonDemo();可分为一下3步完成（伪代码）：**
```java
memory = allocate();//1.分配对象内存空间
instance(memory);  //2.初始化对象
instance = memory ;//3.设置instance指向刚刚分配的内存地址，此时instance!=null
```
步骤2和步骤3<font color=#FF000>不存在数据依赖关系</font>，而且无论重排前还是重排后程序的执行结果在单线程中并没有改变，因此这种重排优化是允许的。
```java
memory = allocate();//1.分配对象内存空间
instance = memory ;//3.设置instance指向刚刚分配的内存地址，此时instance!=null，但是对象还没有初始化完成！
instance(memory);  //2.初始化对象
```

但是指令重排只会保证串行语义的执行的一致性（单线程），但并不会关心多线程间的语义一致性。
<font color=#FF000>所以当一个线程访问instance不为null时，由于instance实例未必已初始化完成，也就造成了线程安全问题。</font>
