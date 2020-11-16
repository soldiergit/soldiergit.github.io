---
title: Java面试题——CAS你知道吗？
tags: [Java面试题, CAS, UnSafe]
index_img: /resource/img/CAS.png
date: 2020-11-10 10:49:02
---

## 1.CAS代码演示
CAS就是单词compareAndSet的首字母缩写，意思是： ***<font color=#FF000>比较并交换</font>***
CASDemo代码演示：
```java
public static void main(String[] args) {
    AtomicInteger atomicInteger = new AtomicInteger(5);

    // main thread do thing...

    // 两个参数：int expect, int update，如果atomicInteger的当前值expect == 5，那么就将atomicInteger的值改为update
    // 如果线程的期望值跟主物理内存的真实值一样，那么就修改为线程的更新值，本次修改成功并返回true
    System.out.println(atomicInteger.compareAndSet(5, 2019) + "\t current data:" + atomicInteger.get());

    // 本次修改失败并返回false
    System.out.println(atomicInteger.compareAndSet(5, 1024) + "\t current data:" + atomicInteger.get());
}
```
以上代码输出：
```text
true	 current data:2019
false	 current data:2019
```

## 2.CAS底层原理？如果知道，谈谈你对UnSafe的理解
直接回答：自旋锁、UnSafe类

### 2.1 atomicInteger.getAndIncrement();方法底层源码
在之前讲volatile时，我们使用 **atomicInteger.getAndIncrement();** 解决了 **number++;** 在多线程环境下的线程安全问题，它的底层源码实现为：
```java
/**
 * Atomically increments by one the current value.
 *
 * @return the previous value
 */
public final int getAndIncrement() {
    return unsafe.getAndAddInt(this, valueOffset, 1);
}
```
该方法中， **this** 指的是当前对象， **valueOffset** 指的是当前对象的内存偏移量（就是内存地址）

### 2.2 UnSafe
![](/resource/img/UnSafe.png)
1. ***<font color=#FF000>UnSafe</font>*** 是CAS的核心类，由于Java无法直接访问底层系统，需要通过本地（native）方法来访问，UnSafe相当于是一个后门，基于该类可以直接操作特定内存的数据。<font color=#FF000>UnSafe类存在于sun.misc包中</font>（存在于rt.jar，JDK从娘胎里就携带的最基础类），其内部方法操作可以想C的指针一样直接操作内存，因为Java中CAS操作执行依赖于UnSafe类的方法。
 ***<font color=#FF000><u>注意UnSafe类中所有方法都是native修饰的，也就是说UnSafe类中的方法都直接调用操作系统底层资源执行相应任务</u></font>***
2. 变量**valueOffset** ，表示该变量值子啊内存中的<font color=#FF000>偏移地址</font>，因为UnSafe就是根据内存偏移地址获取数据的。
![](/resource/img/valueOffset.png)
3. 变量value用volatile修饰，保证了多线程中间的内存可见性。

### 2.3 CAS是什么？
**CAS**的全称为**Compare-And-Swap**，<font color=#FF000>它是一条**CPU**并发原语</font>。
它的功能是判断内存某个位置的值是否为预期值，如果是则更改为新的值，这个过程是原子的。（一致性）

**CAS**并发原语体现在**Java**语言中就是**sun.misc.UnSafe**类中的各个方法。调用**UnSafe**类中的**CAS**方法，JVM会帮我们实现出<font color=#8A2BE2>**CAS汇编指令**</font>。这是6一种完全依赖于<font color=#FF000>硬件</font>的功能，通过它实现了原子操作。
再次强调，由于CAS是一种系统原语，原语属于操作系统用语范畴，是由若干条指令组成的，用于完成某个功能的一个过程，<font color=#FF000>并且原语的执行必须是连续的，在执行过程中不允许被中断，也就是说CAS是一条CPU的原子指令，不会造成所谓的数据不一致问题。</font>
![](/resource/img/CAS.png)
```text
根据当前对象和当前对象的内存偏移量获取var5
判断刚刚获取的var5是不是等于对象本身的值，相等的话，加上var4
不相等的话 do while 继续，直到相等为止
```

#### 2.3.1 unsafe.getAndAddInt();
var1 AtomicInteger对象本身。
var2 该对象值的引用地址。
var4 需要变动的数量。
var5 需要通过var1 var2 找出的主内存中真实存在的值，
用该对象当前的值与var5比较；
如果相同，更新var5+var4 并返回true，
如果不同，继续取值然后再比较，直到更新完成。
![](/resource/img/unsafe-getAndAddInt.png)
假设线程A和线程B两个线程同时执行getAndAddInt操作（分别跑在不同CPU上）：
1. AtomicInteger里面的value原始值是5，即主内存中AtomicInteger的value为5，根据JMM模型，线程A和线程B各自持有一份值为5的value的副本分别到各自的工作内存；
2. 线程A通过getIntVolatile(var1, var2)拿到value值为5，这是线程A被挂起；
3. 线程B也通过getIntVolatile(var1, var2)方法获取到value值为5，此时刚好线程B<font color=#FF000>没有被挂起</font>并执行compareAndSwapInt方法比较内存值也为5，成功修改主内存值为6，线程B打完收工，一切OK；
4. 这是线程A恢复，执行compareAndSwapInt方法比较，发现自己手里的值数字5和主内存的值数字6不一致，说明该值已经被其它线程抢先一步修改过了，那A线程本次修改失败，<font color=#FF000>只能重新读取重新来一遍了</font>；
5. 线程A重新获取value值，因为变量value被volatile修饰，所以其它线程对它的修改，线程A总是能够看到，线程A继续执行compareAndSwapInt方法进行比较替换，直到成功。

#### 2.3.2 底层汇编
UnSafe类中的compareAndSwapInt，是一个本地方法，该方法的实现位于unsafe.cpp中
```cpp
UNSAFE_ENTRY(jboolean, Unsafe_CompareAndSwapInt(JNIEnv *env, jobject unsafe, jobject obj, jlong offset, jint e, jint x))
  UnsafeWrapper("Unsafe_CompareAndSwapInt");
  oop p = JNIHandles::resolve(obj);
  jint* addr = (jint *) index_oop_from_field_offset_long(p, offset);
  return (jint)(Atomic::cmpxchg(x, addr, e)) == e;
UNSAFE_END
```
<font color=#FF000>(Atomic::cmpxchg(x, addr, e)) == e;</font>使得操作不用加synchronized也能保证原子性；
<font color=#0000F>
先想办法拿到变量value在内存中的地址；
通过Atomic::cmpxchg实现比较替换，其中参数x是即将更新的值，参数e是原内存的值。
</font>

## 3.CAS的缺点
**1、循环时间长开销很大**
我们可以看到getAndAddInt方法执行时，有个do while
```java
public final int getAndAddInt(Object var1, long var2, int var4) {
    int var5;
    do {
        var5 = this.getIntVolatile(var1, var2);
    } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

    return var5;
}
```
如果CAS失败，会一直进行尝试。如果CAS长时间一直不成功，可能会给CPU带来很大的开销。
**2、只能保证一个共享变量的原子操作**
**3、引出来ABA问题**

## 4.自旋锁
自旋 = UnSafe类 + CAS思想

