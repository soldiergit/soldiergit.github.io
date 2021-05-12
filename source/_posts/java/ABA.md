---
title: 原子类AtomicInteger的ABA问题谈谈？原子更新引用知道吗？
tags: [面试题, Java面试题, ABA, 乐观锁]
index_img: /resource/img/java/ABA.jpeg
date: 2020-11-15 11:09:54
---

CAS ---> UnSafe ---> CAS底层思想 ---> ABA ---> 原子引用更新 ---> 如何规避ABA问题
## 1.ABA问题是怎么产生的
CAS会导致“ABA”。**狸猫换太子**
![](/resource/img/java/ABA.jpeg)
CAS算法实现一个重要前提需要提取出内存中某时刻的数据并在当下时刻比较并替换，那么在这个时间差内会导致数据的变化。
比如说一个线程t1从内存地址V中取出A，这时候另一个线程t2也从内存中取出A，并且线程t2进行了一些操作将值变成了B，然后线程t2又将V位置的数据变成A，这时候线程t1进行CAS操作发现内存中仍然是A，然后线程t1操作成功。
<font color=#FF000>尽管线程t1的CAS操作成功，但是并不代表这个过程就是没有问题的。</font>

## 2.原子引用
解决ABA问题的方法：**理解原子引用** + 新增一种机制，那就是修改版本号（类似于时间戳）
原子引用：**AtomicReference**类
```java
class User {
    String userName;
    int age;

    public User(String userName, int age) {
        this.userName = userName;
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "userName='" + userName + '\'' +
                ", age=" + age +
                '}';
    }
}

public class AtomicReferenceDemo {
    public static void main(String[] args) {

        User z3 = new User("zhangsan", 20);
        User l4 = new User("lisi", 22);

        AtomicReference<User> atomicReference = new AtomicReference<>();
        atomicReference.set(z3);

        // 如果当前值等于z3，那么替换为l4
        System.out.println(atomicReference.compareAndSet(z3, l4) + "\t" + atomicReference.get().toString());
        System.out.println(atomicReference.compareAndSet(z3, l4) + "\t" + atomicReference.get().toString());
    }
}
```

## 3.时间戳的原子引用
ABA问题的解决办法：**AtomicStampedReference类**
```java
public class ABADemo {

    static AtomicReference<Integer> atomicReference = new AtomicReference<>(100);
    static AtomicStampedReference<Integer> atomicStampedReference = new AtomicStampedReference<>(100, 1); // 初始值和初始版本号

    public static void main(String[] args) {
        System.out.println("============================以下是ABA问题的产生=============================");
        new Thread(() -> {
            // 如果值等于expect，那么修改为update
            atomicReference.compareAndSet(100, 101);
            atomicReference.compareAndSet(101, 100);
        }, "t1").start();

        new Thread(() -> {
            // 暂停t2线程1秒钟，保证上面的t1线程完成一次ABA操作
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            System.out.println(atomicReference.compareAndSet(100, 2019) + "\t" + atomicReference.get());
        }, "t2").start();

        System.out.println("============================以下是ABA问题的解决=============================");

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t第1次版本号：" + atomicStampedReference.getStamp());
            // 暂停1秒t3线程
            try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
            atomicStampedReference.compareAndSet(100, 101, atomicStampedReference.getStamp(), atomicStampedReference.getStamp()+1);
            System.out.println(Thread.currentThread().getName() + "\t第2次版本号：" + atomicStampedReference.getStamp());
            atomicStampedReference.compareAndSet(101, 100, atomicStampedReference.getStamp(), atomicStampedReference.getStamp()+1);
            System.out.println(Thread.currentThread().getName() + "\t第3次版本号：" + atomicStampedReference.getStamp());
        }, "t3").start();


        new Thread(() -> {
            int stamp = atomicStampedReference.getStamp();
            System.out.println(Thread.currentThread().getName() + "\t第1次版本号：" + stamp);
            // 暂停3秒t4线程，保证上面的t3线程完成一次ABA操作
            try { TimeUnit.SECONDS.sleep(3); } catch (InterruptedException e) { e.printStackTrace(); }
            boolean result = atomicStampedReference.compareAndSet(100, 2019, stamp, stamp+1);
            System.out.println(Thread.currentThread().getName() + "\t是否修改成功：" + result + "\t当前最新实际版本号：" + atomicStampedReference.getStamp());
            System.out.println(Thread.currentThread().getName() + "\t当前最新值：" + atomicStampedReference.getReference());
        }, "t4").start();
    }
}
```
线程t1、t2对AtomicReference类的操作，最终compareAndSet的结果为true
线程t3、t4对AtomicStampedReference类的操作，最终compareAndSet的结果为false
