---
title: 排序算法学习笔记
tags: [基础知识, 算法, 排序]
index_img: /resource/img/java/Sort.png
date: 2020-11-24 11:20:00
---

## 约定
**提取每一个排序算法公共部分作为父类的属性**：
待排序的元素需要实现 Java 的 Comparable 接口，该接口有 compareTo() 方法，可以用它来判断两个元素的大小关系。
使用辅助函数 less() 和 swap() 来进行比较的交换的操作，使得代码的可读性和可移植性更好。

排序算法的成本模型是比较和交换的次数。
```java
public abstract class Sort<T extends Comparable<T>> {

    // 具体排序实现
    public abstract void sort(T[] nums);

    /**
     * 比较
     * 小于返回true；大于返回false
     */
    protected boolean less(T v, T w) {
        // compareTo-->小于返回-1；等于返回0；大于返回1
        return v.compareTo(w) < 0;
    }

    // 交换
    protected void swap(T[] a, int i, int j) {
        T t = a[i];
        a[i] = a[j];
        a[j] = t;
    }
}
```

## 选择排序
<font color=#FF000>**概念**</font>：从数组中选择最小元素，将它与数组的第一个元素交换位置。再从数组剩下的元素中选择出最小的元素，将它与数组的第二个元素交换位置。不断进行这样的操作，直到将整个数组排序。

选择排序需要 ~N<sup>2</sup>/2 次比较和 ~N 次交换，它的运行时间与输入无关，这个特点使得它对一个已经排序的数组也需要这么多的比较和交换操作。

**实现**：
```java
public class Selection<T extends Comparable<T>> extends Sort<T> {
    @Override
    public void sort(T[] nums) {
        // 数组长度
        int n = nums.length;
        for (int i = 0; i < n -1; i++) {
            // 取数组第1个到 n-1 个元素
            int min = i;
            // 与子数组进行比较
            for (int j = i + 1; j < n; j++) {
                // 获取最小元素下标
                if (less(nums[j], nums[min])) min = j;
            }
            // 把最小的元素放到前面
            swap(nums, i, min);
        }
    }
}
```

## 冒泡排序
<font color=#FF000>**概念**</font>：从左到右不断比较相邻逆序的元素，在一轮的循环之后，可以让未排序的最大元素上浮到右侧。在一轮循环中，如果没有发生变化，那么说明数组已经是有序的，此时可以直接退出。

**实现**：
```java
public class Bubble<T extends Comparable<T>> extends Sort<T> {
    @Override
    public void sort(T[] nums) {
        // 数组长度
        int n = nums.length;
        boolean isSorted = false;
        // 取游标i：从最后一个元素取到第二个元素，游标后的元素已经有序
        for (int i = n - 1; i > 0 && !isSorted; i--) {
            // 每取一次都默认当前数组已经有序
            isSorted = true;
            // 只比较游标前的元素
            for (int j = 0; j < i; j++) {
                // 某相邻的两个元素不是有序
                if (less(nums[j + 1], nums[j])) {
                    // 表明这个数组还不是有序的
                    isSorted = false;
                    // 交换相邻两个元素位置
                    swap(nums, j, j + 1);
                }
            }
        }
    }
}
```

## 插入排序
<font color=#FF000>**概念**</font>：每次都将当前元素插入到左侧以及排序的数组中，使得插入之后左侧数组依然有序。

对于数值{3, 5, 2, 4, 1}，它具有一下逆序：(3, 2), (3, 1), (5, 2), (5, 4), (5, 1), (2, 1), (4, 1)，插入排序每次只能交换相邻元素，令逆序数量减少1，因此插入排序需要交换的次数为逆序数量。

插入排序的时间复杂度取决于数组的初始顺序，如果数组已经部分有序了，那么逆序较少，需要的交换次数也就较少，时间复杂度较低。
 - 平均情况下插入排序需要 ~N2/4 比较以及 ~N2/4 次交换；
 - 最坏的情况下需要 ~N2/2 比较以及 ~N2/2 次交换，最坏的情况是数组是倒序的；
 - 最好的情况下需要 N-1 次比较和 0 次交换，最好的情况就是数组已经有序了。

**实现**：
```java
public class Insertion<T extends Comparable<T>> extends Sort<T> {
    @Override
    public void sort(T[] nums) {
        // 数组长度
        int n = nums.length;
        // 游标i：从第二个元素开始取
        for (int i = 1; i < n; i++) {
            // 从游标处倒着比较到第1个元素，比较并交换相邻两个元素；有序时直接跳过
            for (int j = i; j > 0 && less(nums[j], nums[j - 1]); j--) {
                swap(nums, j, j - 1);
            }
        }
    }
}
```

## 希尔排序
<font color=#FF000>**概念**</font>：对于大规模的数组，插入排序很慢，因为它只能交换相邻的元素，每次只能将逆序数量减少1。希尔排序的出现就是为了解决插入排序的这种局限性，它通过**交换不相邻的元素**，每次可以将逆序数量减少大于1.

希尔排序使用插入排序对间隔 h 的序列进行排序。通过不断减少 h，最后令 h=1，就可以使得整个数组是有序的。
![](/resource/img/java/Sort_shell.png)

**实现**：
```java
public class Shell<T extends Comparable<T>> extends Sort<T> {
    @Override
    public void sort(T[] nums) {
        // 数组长度
        int n = nums.length;

        // 计算递增序列h：1, 4, 13, 40, 121, 364, 1093...(间隔为1时是插入排序)
        int h = 1;
        while (h < n / 3) {
            h = 3 * h + 1;
        }

        // 通过while循环，每次减少3倍序列h的值
        while (h >= 1) {
            // 使用插入排序对间隔 h 的数组进行排序
            for (int i = h; i < n; i++) {
                for (int j = i; j >= h && less(nums[j], nums[j - h]) ; j -= h) {
                    swap(nums, j, j-h);
                }
            }
            h /= 3;
        }
    }
}
```
希尔排序的运行时间达不到平方级别，使用`递增序列 1, 4, 13, 40, ...` 的希尔排序所需要的比较次数不会超过 `n 的若干倍`乘于 `递增序列的长度`。后面介绍的高级排序算法只会比希尔排序快两倍左右。

## 归并排序
<font color=#FF000>**概念**</font>：是将数组分成两部分，分别进行排序，然后归并起来。
![](/resource/img/java/Sort_merge.png)

### 1.公共的归并方法
**归并方法**将数组中两个已经排序的部分归并成一个。它基础自约定Sort类，是两种类型的归并排序的父类，内含公共的合并方法。
```java
public abstract class MergeSort<T extends Comparable<T>> extends Sort<T> {

    // 辅助数组
    protected T[] aux;

    /**
     * 将数组中两个已经排序的部分归并成一个，l和h表明起始位置
     * @param nums  被操纵数组
     * @param l     数组下标开始位置
     * @param m     数组中间下标
     * @param h     数组下标结束位置
     */
    protected void merge(T[] nums, int l, int m, int h) {
        int i = 1, j = m+1;

        // 将数据复制到辅助数组
        for (int k = l; k <= h; k++) {
            aux[k] = nums[k];
        }

        for (int k = l; k <= h; k++) {
            if (i > m) {
                nums[k] = aux[j++];
            } else if (j > h) {
                nums[k] = aux[i++];
            } else if (aux[i].compareTo(aux[j]) <= 0) {
                // compareTo-->小于返回-1；等于返回0；大于返回1
                nums[k] = aux[i++]; // 先进行这一步，保证稳定性
            } else {
                nums[k] = aux[j++];
            }
        }
    }
}
```

### 2.自顶向下的归并排序
将一个大数组分成两个小数组去求解。

因为每次都将问题对半分成两个子问题，这种对半分的算法复杂度一般为：O(NlogN)。
```java
public class Up2DownMergeSort<T extends Comparable<T>> extends MergeSort<T> {
    @Override
    public void sort(T[] nums) {
        aux = (T[]) new Comparable[nums.length];
        sort(nums, 0, nums.length - 1);
    }

    /**
     * 排序
     * @param nums  被操纵数组
     * @param l     数组下标开始位置
     * @param h     数组下标结束位置
     */
    private void sort(T[] nums, int l, int h) {
        if (h <= 1) return;
        // 计算当前数组中间下标
        int mid = 1 + (h-1) / 2;
        // 递归：排序左子数组
        sort(nums, l, mid);
        // 递归：排序右子数组
        sort(nums, mid + 1, h);
        // 根据中间下标，合并左右子数组
        merge(nums, l ,mid, h);
    }
}
```

### 3.自底向上的归并排序
先归并那些微型数组，然后成对归并得到的微型数组。
```java
public class Down2UpMergeSort<T extends Comparable<T>> extends MergeSort<T> {
    @Override
    public void sort(T[] nums) {
        int n = nums.length;
        aux = (T[]) new Comparable[n];

        for (int sz = 1; sz < n; sz += sz) {
            for (int lo = 0; lo < n - sz; lo += sz + sz) {
                merge(nums, lo, lo + sz - 1, Math.min(lo + sz + sz - 1, n - 1));
            }
        }
    }
}
```

## 快速排序
### 1.基本算法
<font color=#FF000>**概念**</font>：
 - 归并排序将数组分为两个子数组分别排序，并将有序的子数组归并使得整个数组排序；
 - 快速排序通过一个切分元素将数组分为两个子数组，左子数组小于切分元素，右子数组大于等于切分元素，将这两个子数组排序也就将整个数组排序了。
 
![](/resource/img/java/Sort_quick.png)
```java
public class QuickSort<T extends Comparable<T>> extends Sort<T> {
    @Override
    public void sort(T[] nums) {
        shuffle(nums);
        sort(nums, 0, nums.length - 1);
    }

    protected void sort(T[] nums, int l, int h) {
        if (h <= 1) return;
        int j = partition(nums, l, h);
        sort(nums, l, j - 1);
        sort(nums, j + 1, h);
    }

    private void shuffle(T[] nums) {
        List<Comparable> list = Arrays.asList(nums);
        Collections.shuffle(list);
        list.toArray(nums);
    }
}
```
### 2.切分
将 a[l] 作为切分元素，然后从数组的左端向右扫描直到找到第一个大于等于它的元素，再从数组的右端向左扫描找到第一个小于它的元素，交换这两个元素。不断进行这个过程，就可以保证左指针 i 的左侧元素都不大于切分元素，右指针 j 的右侧元素都不小于切分元素。当两个指针相遇时，将切分元素 a[l] 和 a[j] 交换位置。
![](/resource/img/java/Sort_partition.gif)
```java
private int partition(T[] nums, int l, int h) {
    int i = l, j = h + 1;
    T v = nums[l];
    while (true) {
        while (less(nums[++i], v) && i != h) ;
        while (less(v, nums[--j]) && j != l) ;
        if (i >= j)
            break;
        swap(nums, i, j);
    }
    swap(nums, l, j);
    return j;
}
```
### 3.性能分析
快速排序是原地排序，不需要辅助数组，但是递归调用需要辅助栈。

快速排序最好的情况下是每次都正好将数组对半分，这样递归调用次数才是最少的。这种情况下比较次数为 C<sub>N</sub>=2C<sub>N/2</sub>+N，复杂度为 O(NlogN)。

最坏的情况下，第一次从最小的元素切分，第二次从第二小的元素切分，如此这般。因此最坏的情况下需要比较 N<sup>2</sup>/2。为了防止数组最开始就是有序的，在进行快速排序时需要随机打乱数组。
### 4.算法改进
#### 4.1 切换到插入排序
因为快速排序在小数组中也会递归调用自己，`对于小数组，插入排序比快速排序的性能更好`，因此在小数组中可以切换到插入排序。
#### 4.2 三数居中
最好的情况下是每次都能取数组的中位数作为切分元素，但是计算中位数的代价很高。一种折中方法是取3个元素，并将大小居中的元素作为切分元素。
#### 4.3 三向切分的快速排序
对于有大量重复元素的数组，可以将数组切分为三部分，分别对应小于、等于和大于切分元素。

三向切分快速排序对于有大量重复元素的随机数组可以在线性时间内完成排序。
```java
public class ThreeWayQuickSort<T extends Comparable<T>> extends QuickSort<T> {
    @Override
    protected void sort(T[] nums, int l, int h) {
        if (h <= l) {
            return;
        }
        int lt = l, i = l + 1, gt = h;
        T v = nums[l];
        while (i <= gt) {
            int cmp = nums[i].compareTo(v);
            if (cmp < 0) {
                swap(nums, lt++, i++);
            } else if (cmp > 0) {
                swap(nums, i, gt--);
            } else {
                i++;
            }
        }
        sort(nums, l, lt - 1);
        sort(nums, gt + 1, h);
    }
}
```
### 5.基于切分的快速选择算法
快速排序的 partition() 方法，会返回一个整数 j 使得 a[l..j-1] 小于等于 a[j]，且 a[j+1..h] 大于等于 a[j]，此时 a[j] 就是数组的第 j 大元素。

可以利用这个特性找出数组的第 k 个元素。

该算法是线性级别的，假设每次能将数组二分，那么比较的总次数为 (N+N/2+N/4+..)，直到找到第 k 个元素，这个和显然小于 2N。
```java
public T select(T[] nums, int k) {
    int l = 0, h = nums.length - 1;
    while (h > l) {
        int j = partition(nums, l, h);

        if (j == k) {
            return nums[k];

        } else if (j > k) {
            h = j - 1;

        } else {
            l = j + 1;
        }
    }
    return nums[k];
}
```

## 堆排序
### 1.堆
堆中某个节点的值总是大于等于或小于其子节点的值，并且堆是一颗完全二叉树。

堆可以用数组来表示，这是因为堆是完全二叉树，而完全二叉树很容易就存储在数组中。位置 k 的节点的父节点位置为 k/2，而它的两个子节点的位置分别为 2k 和 2k+1.这里不使用数组索引为0的位置，是为了更清晰地描述节点的位置关系。
![](/resource/img/java/Sort_heap.png)
```java
public class Heap<T extends Comparable<T>> {
    
    private T[] heap;
    private int n = 0;
    
    public Heap (int maxN) {
        this.heap = (T[]) new Comparable[maxN + 1];
    } 
    
    public boolean isEmpty() {
        return n == 0;
    }
    
    public int size() {
        return n;
    }
    
    // 比较：小于返回true；大于等于返回false
    private boolean less(int i, int j) {
        return heap[i].compareTo(heap[j]) < 0;
    }
    
    // 交换
    private void swap(int i, int j) {
        T t = heap[i];
        heap[i] = heap[j];
        heap[j] = t;
    }
}
```

### 2.上浮和下沉
在堆中，当一个节点比父节点大，那么需要交换这两个节点。交换后还可能比它新的父节点大，因此需要不断地进行比较和交换操作，把这种操作称为上浮。
![](/resource/img/java/Sort_heap_swim.gif)
```java
private void swim(int k) {
    while (k > 1 && less(k /2, k)) {
        swap(k /2, k);
        k = k /2;
    }
}
```
类似的，当一个节点比子节点小，也需要不断地向下进行比较和交换操作，把这种操作称为下沉。一个节点如果有两个子节点，应当与两个子节点中最大那个节点进行交换。
![](/resource/img/java/Sort_heap_sink.gif)
```java
private void sink(int k) {
    while (2 * k <= n) {
        int j = 2 *k;
        if (j < n && less(j, j+1))
            j++;
        if (!less(k, j)) 
            break;
        swap(k, j);
        k = j;
    }
}
```

### 3.插入元素
将新元素放到数组末尾，然后上浮到合适位置。
```java
public void insert(Comparable v) {
    heap[++n] = (T) v;
    swim(n);
}
```

### 4.删除最大元素
从数组顶端删除最大的元素，并将数组的最后一个元素放到顶端，并让这个元素下沉到合适位置。
```java
public T delMax() {
    T max = heap[1];
    swap(1, n--);
    heap[n + 1] = null;
    sink(1);
    return max;
}
```

### 5.堆排序
把最大元素和当前堆中数组的最后一个元素交换位置，并且不删除它，那么就可以得到一个从尾到头的递减序列，从正向来看就是一个递增序列，这就是堆排序。
#### 5.1构建堆
无序数组建立堆最直接的方法是从左到右遍历数组进行上浮操作。一个更高效的方法是从右至左进行下沉操作，如果一个节点的两个节点都已经是堆有序，那么进行下沉操作可以使得这个节点为根节点的堆有序。叶子节点不需要进行下沉操作，可以忽略叶子节点的元素，因此只需要遍历一半的元素即可。
#### 5.2交换堆顶元素与最后一个元素
交换之后需要进行下沉操作维持堆的有序状态。
```java
public class HeapSort<T extends Comparable<T>> extends Sort<T> {
    /**
     * 数组第 0 个位置不能有元素
     */
    @Override
    public void sort(T[] nums) {
        int n = nums.length - 1;
        for (int k = n / 2; k >= 1; k--)
            sink(nums, k, n);

        while (n > 1) {
            swap(nums, 1, n--);
            sink(nums, 1, n);
        }
    }

    // 下沉
    private void sink(T[] nums, int k, int n) {
        while (2 * k <= n) {
            int j = 2 * k;
            if (j < n && less(nums, j, j + 1))
                j++;
            if (!less(nums, k, j))
                break;
            swap(nums, k, j);
            k = j;
        }
    }

    // 比较：小于返回true；大于等于返回false
    private boolean less(T[] nums, int i, int j) {
        return nums[i].compareTo(nums[j]) < 0;
    }
}
```

### 6.分析
 - 一个堆的高度为 logN，因此在堆中插入元素和删除最大元素的复杂度都为 logN。
 - 对于堆排序，由于要对 N 个节点进行下沉操作，因此复杂度为 NlogN。
 - 堆排序是一种原地排序，没有利用额外的空间。
 - 现代操作系统很少使用堆排序，因为它无法利用局部性原理进行缓存，也就是数组元素很少和相邻的元素进行比较和交换。

## 小结
### 1.排序算法的比较
算法 | 稳定性 | 时间复杂度 | 空间复杂度 | 备注
:-: | :-: | :-: | :-: | :-:
选择排序 | x | N<sup>2</sup> | 1 | | 
冒泡排序 | &radic; | N<sup>2</sup> | 1 | |
插入排序 | &radic; | N ~ N<sup>2</sup> | 1 | 时间复杂度和初始顺序有关|
希尔排序 | x | N 的若干倍乘于递增序列的长度 | 1 | 改进版的插入排序|
快速排序 | x |  NlogN |  logN | |
三向切分的快速排序 | x |  N ~ NlogN | logN | 适用于大量重复元素的数组|
归并排序 | &radic; |  NlogN | N | |
堆排序 | x |  NlogN | 1 | 无法利用局部性原理|

### 2.Java 的排序算法实现
Java 主要排序方法为 `java.util.Arrays.sort()`，对原始数据类型使用<font color=#FF000>三向切分的快速排序</font>，对于引用类型使用<font color=#FF000>归并排序</font>。
