---
title: Java面试题——聊聊Map？
tags: [Java面试题, Map, 集合类不安全问题]
index_img: /resource/img/Map.png
date: 2020-11-16 11:59:33
---

## 1.HashMap简介
举一反三，HashMap与ArrayLsi、HashSet都是线程不安全的。JDK1.7用的是头插法，而JDK1.8及之后使用的都是尾插法。因为JDK1.7是用单链表进行的纵向延伸，当采用头插法就是能够提高插入的效率，但是也会容易出现逆序且环形链表死循环问题。但是在JDK1.8之后是因为加入了红黑树使用尾插法，能够避免出现逆序且链表死循环的问题。
***JDK1.8及之后***先插入，再判断是否有红黑树节点，再判断链表长度，当链表长度达到8是改为红黑树

## 2.HashMap线程不安全案例
```java
public class ContainerNotSafeDemo {
    public static void main(String[] args) {
        Map<String, String> map = new ConcurrentHashMap<>();
        for (int i = 1; i <= 3; i++) {
            new Thread(() -> {
                map.put(Thread.currentThread().getName(), UUID.randomUUID().toString().substring(0 ,8));
                System.out.println(map);
            }, String.valueOf(i)).start();
        }
    }
}
```
当线程数量大一点，比如30时，会出现并发修改异常**java.util.ConcurrentModificationException**

## 3.线程安全解决方案
1. Hashtable
```java
Map<String, String> map = new Hashtable<>()
```
Hashtable跟HashMap的关系与Vector跟ArrayList的关系一样，都是在所有操作方法中加入synchronized修饰，粒度大，不推荐使用！
2. 使用JDK提供的Collections工具类
```java
Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
```
3. JUC下的ConcurrentHashMap
```java
Map<String, String> map = new ConcurrentHashMap<>();
```
<font color=#FF000>**重点**</font>：ConcurrentHashMap使用分段锁Segment，将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。
在计算map的size是是遍历所有Segment的size进行累加，先连续两次尝试不加锁的size是否一致，超过3次会给每个Segment加锁。（Segment分段锁继承自ReentrantLock重入锁）
***JDK1.8抛弃Segment分段锁***，利用CAS+Synchronized，数据结构采用：数组+链表+红黑树

