---
title: 面试题—ArrayList是线程不安全，请编码写一个不安全的案例并给出解决方案。
tags: [面试题, Java面试题, ArrayList, 集合类不安全问题]
index_img: /resource/img/ArrayList.jpeg
date: 2020-11-16 10:03:51
---

## 1.ArrayList简介
ArrayList 是基于数组实现的，所以支持快速随机访问。数组的默认大小为 10。每次扩容1.5 倍，扩容操作需要调用 Arrays.copyOf() 把原数组整个复制到新数组中，这个操作代价很高。
Vector出现在JDK1.0是线程安全的，ArrayList出现在JDK1.2是线程不安全的，因为Vector的操作使用了synchronized，保证了数据一致性但并发性下降

## 2.ArrayList线程不安全案例
```java
public class ContainerNotSafeDemo {
    public static void main(String[] args) {
        // 构造一个初始容量为10的空数组
//        List<String> list = Arrays.asList("a", "b", "c");
//        list.forEach(System.out :: println);

        List<String> list = new ArrayList<>();
        for (int i = 1; i <= 3; i++) {
            new Thread(() -> {
                list.add(UUID.randomUUID().toString().substring(0 ,8));
                System.out.println(list);
            }, String.valueOf(i)).start();
        }
    }
}
```
当线程数量大一点，比如30时，会出现并发修改异常**java.util.ConcurrentModificationException**

## 3.导致原因
***并发争抢***：
假设老师上课要考勤，List就是老师的签到表，加锁的时候有老师在维持秩序，同学们只能一个个轮流签到，这个同学签到好了才能到下一个；如果签到过程没有老师在 没人管理秩序，也就是没加锁，假设张三同学正在签到，才写了个“张”字，这时李四同学很粗暴的把签到表抢过去签，张三同学的笔就会在纸上画出一道长长的线，<font color=#FF000>这道长长的线就是程序出现的并发修改异常java.util.ConcurrentModificationException</font>

## 4.解决方案
1. 方法一：使用更古老更重的Vector
```java
List<String> list = new Vector<>();
```
2. 方法二：使用JDK提供的Collections工具类
```java
List<String> list = Collections.synchronizedList(new ArrayList<>());
```
区别于Collection接口，我们使用的容器类List、Set都是继承自Collection接口，Collections是一个class工具类，可以构造出线程安全的List、Set，包括Map

## 5.限制不可以使用Vector和Collections工具类的解决方案
***使用JUC下的CopyOnWriteArrayList***：
以add()方法为例，CopyOnWrite容器即即写时复制的容器。往一个容器添加元素的时候，不直接往当前容器Object[]添加，而是先将容器Object[]进行copy，复制出一个新的容器Object[] newElements，然后往新的容器Object[] newElements里添加元素，添加完之后再将原容器的引用指向新的容器setArray(newElements);。这样做的好处是可以对CopyOnWrite容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以CopyOnWrite容器也是一种读写分离的思想，读和写不同的容器。
CopyOnWriteArrayList使用volatile保证底层数组的可见性，当一个线程操作list时，给其上锁，操作完成后释放锁，保证只有一个线程进行写操作
```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

## 6.list遍历的几种方式？
1. 普通fori 方式
2. for 循环
```java
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}
```
3. iterator迭代器
```java
Iterator<String> it = list.iterator();
while (it.hasNext()) {
  String str = it.next();
  System.out.println(str);
}
```
4. list.forEach（java8）
```java
list.forEach(item -> {
    System.out.println(item);
});
```
5. list.stream().forEach（java8）
```java
list.stream().forEach(item -> {
    System.out.println(item);
});
```
6. parallelStream().forEach（java8）
```java
list.parallelStream().forEach(item -> {
    System.out.println(item);
});
```

**总结：**
 - 推荐使用 fori，或iterator迭代器；
 - forEach 最耗时；
 - stream forEach 比较耗时和 for 差不多。
