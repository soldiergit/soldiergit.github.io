---
title: 聊聊Set集合？
tags: [面试题, Java面试题, Set, 集合类不安全问题]
index_img: /resource/img/Set.jpg
date: 2020-11-16 11:29:03
---

## 1.HashSet简介
HashSet与ArrayList类似，都是线程不安全的，但HashSet的底层是HashMap，调用add方法时只需要传一个参数作为key，value是公共的Object对象 PRESENT

## 2.HashSet线程不安全案例
```java
public class ContainerNotSafeDemo {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();
        for (int i = 1; i <= 3; i++) {
            new Thread(() -> {
                set.add(UUID.randomUUID().toString().substring(0 ,8));
                System.out.println(set);
            }, String.valueOf(i)).start();
        }
    }
}
```
当线程数量大一点，比如30时，会出现并发修改异常**java.util.ConcurrentModificationException**

## 3.线程安全解决方案
1. 使用JDK提供的Collections工具类
```java
Set<String> set = Collections.synchronizedSet(new HashSet<>());
```
2. JUC下的CopyOnWriteArraySet
```java
Set<String> set = new CopyOnWriteArraySet<>();
```
<font color=#FF000>**注意**</font>：CopyOnWriteArraySet的底层是通过CopyOnWriteArrayList实现的。

## 4.set遍历的几种方式？
1. for循环
```java
for (String str : set) {
      System.out.println(str);
}
```
2. iterator迭代器
```java
Iterator<String> it = set.iterator();
while (it.hasNext()) {
  String str = it.next();
  System.out.println(str);
}
```
3. set.forEach（java8）
```java
set.forEach(item -> {
    System.out.println(item);
});
```
4. set.stream().forEach（java8）
```java
set.stream().forEach(item -> {
    System.out.println(item);
});
```

**总结：**
 - iterator 迭代器方式比 foreach 的效率高；
 - forEach 最耗时；
 - stream forEach 比较耗时和 for 循环差不多。
