---
title: 聊聊Map集合？
tags: [面试题, Java面试题, Map, 集合类不安全问题]
index_img: /resource/img/java/Map.png
date: 2020-11-16 11:59:33
---

## 1.HashMap简介
举一反三，HashMap与ArrayList、HashSet都是线程不安全的。<font color=#FF000>JDK1.7用的是头插法（先扩容再插入）</font>，而<font color=#FF000>JDK1.8及之后使用的都是尾插法（先插入再扩容）</font>。因为JDK1.7是用单链表进行的纵向延伸，当采用头插法就是能够提高插入的效率，但是也会容易出现<font color=#FF000>**逆序且环形链表死循环问题**</font>。但是在JDK1.8之后是因为加入了红黑树使用尾插法，能够避免出现逆序且链表死循环的问题。
**JDK1.8及之后**先插入，再判断是否有红黑树节点，再判断链表长度，当链表长度达到8是改为红黑树

## 2.为什么HashMap要转红黑树？
HashMap会触发链表转红黑树的两个条件是：
1. 链表的长度达到8个
2. 数组的长度达到64个

出现这种数据结构的转换，实际上就是：<font color=#FF000>空间和时间的权衡</font>。
（1）为了实现快速查找，HashMap 选择了数组而不是链表。以利用数组的索引实现 O(1) 复杂度的查找效率。
（2）为了利用索引查找，HashMap 引入 Hash 算法, 将 key 映射成数组下标: key -> Index。
（3）引入 Hash 算法又导致了 Hash 冲突。为了解决 Hash 冲突，HashMap 采用链地址法，在冲突位置转为使用链表存储。
（4）链表存储过多的节点又导致了在链表上节点的查找性能的恶化。为了优化查找性能，HashMap 在链表长度超过 8 之后转而将链表转变成红黑树，以将 O(n) 复杂度的查找效率提升至 O(log n)。

## 3.HashMap线程不安全案例
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

## 4.线程安全解决方案
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

## 5.map遍历的几种方式？
1. keySet的for循环
```java
for(String key : map.keySet()){  
    System.out.println(key + "--" + map.get(key));  
}
```
2. keySet的iterator迭代器
```java
Iterator it=map.keySet().iterator();  
while(it.hasNext()){  
    String key = it.next().toString();   
    String value = map.get(key);  
    System.out.println(key + "--" + value);  
}
```
3. entrySet的for循环
```java
for(Map.Entry<String, String> entry : map.entrySet()){  
    System.out.println(entry.getKey() + "--" + entry.getValue());  
}
```
4. entrySet的iterator迭代器
```java
Set set = map.entrySet();       
Iterator i = set.iterator();       
while(i.hasNext()){    
    Map.Entry<String, String> entry = (Map.Entry<String, String>) i.next();  
    System.out.println(entry.getKey() + "==" + entry.getValue());  
}
```
5. java8还新增了forEach方式以及.stream().forEach

**总结：**
 - entrySet的方式整体都是比keySet方式要高一些；
 - 单纯的获取key来说，两者的差别并不大，但是如果要获取value，还是entrySet的效率会更好，因为keySet需要从map中再次根据key获取value，而entrySet一次都全部获取出来；
 - iterator 迭代器方式比 foreach 的效率高。
