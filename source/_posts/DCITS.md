---
title: 于2020年12月14日到西安神州数码融信软件有限公司培训一个月
tags: [神州信息金融SBU岗前培训]
index_img: /resource/img/DCITS.jpeg
date: 2020-12-15 08:53:02
---

## 公司介绍
张微微 05年 项目经理
神州数码控股有限公司
母公司：神州数码信息服务股份有限公司 简称：神州信息 关键字：使命、定位、战略 股票代码：000555.SZ
神州数码融信软件有限公司 金融SBU部门，百分之九十做的银行
在金融、政府、电信

分布式与微服务、前端服务、渠道营销、数据服务、业务处理、融合场景、运维管理、测试服务质量管控、人工智能、大数据、云管理、区块链、物联网、5G
拥有3个研发中心、5个交付中心、1个创新中心 5个研发基地：北京、上海、广州、深圳、西安

## 第一章、Java基础知识
### 1.类和对象
#### 1)类的定义和声明
 - **类的定义：** 基于面向对象思想，现实生活中具有相同特性的对象的抽象就称为类。类由类声明和类体构成，类体又由变量和方法构成。
 - **类的声明：** 访问说明符 class 类名 extends 超类名 implements 接口名
 - **类体：**
    - 成员变量：指类的一些属性。格式：访问说明符号 数据类型 变量名
    - 成员方法：指类创建的对象能做什么
    - 格式：访问说明符 数据类型 方法名（数据类型1 变量名1， 数据类型2 变量2）
        - 八大基本数据类型：int short float double long boolean chart byte 

#### 2)类的实例化
 - **概念：** 通过一个类创建一个对象，这个过程叫做实例化
 - **举例：** Hero a = new Hero();

#### 3)方法的重载
 - **概念：** 存在与同一个类中，指一个方法与已经存在的方法名称相同，但是参数类型、个数、顺序至少有一个不同；但是返回值不同，其它都相同不是重载
 - **作用：** 使用同一个方法名，根据参数的不同，调用不同的方法，实现相似的操作

#### 4)类的构造方法
 - **概念：** 方法名和类名一样，包括大小写，没有返回类型的方法
 - **作用：** 实现类的实例化，实例化一个对象的时候，必须调用构造方法

#### 5)类的继承
 - **概念：** 继承就是子类继承父类的特征和行为，使得子类对象具有父类的属性和方法
 - **作用：** 子类可以继承父类的全部功能，可以增加新属性、新功能

#### 6)方法的重写
 - **概念：** 子类可以继承父类的对象方法，在继承后，重复提供该方法，就叫做方法的重写，又叫覆盖override
 - **作用：** 当父类的方法无法满足要求，需要子类具有特有的功能，就需要方法重写

#### 7)方法的隐藏
 - **概念：** 方法的隐藏与重写类似，重写是子类覆盖父类的对象方法，隐藏就是子类覆盖父类的类方法
 - **作用：** 当方法属于类是，子类通过方法的隐藏调用到子类的类方法

### 2.接口
#### 1)接口的定义和声明
 - **定义：** 接口帮助类扩展方法，接口与类不同在于；
  - 没有变量的声明，但可以定义变量；
  - 只有方法的声明，没有方法的实现
 - **声明：** public interface 接口名 extends 接口列表

### 3.常用类和数据结构
#### 1)数组
 - **概念：** 数组是一个固定长度的，包含了相同类型数据的容器
 - **初始化：** `int[] arr = new int[5];`

#### 2)Object类
 - **Object类概念：** Object类是所有类的父类，声明一个类的时候，默认是继承了Object类。如`public class Hero extends Object {...}`
 - **toString方法：** 返回当前对象的字符串表达，默认返回对象的类名+内存地址值
 - **equals方法：** 用于判断两个对象的内容是否相同，默认比较的是地址，在实际使用中需要比较两个对象的内容是否一致，所以经常要重写equals方法

#### 3)String类
 - length()方法：返回字符串长度；
 - trim()方法：返回字符串并删除任何前导和尾随控股；
 - String构造方法
  - String(char[] value)：使用字符数组构造一个字符串；
  - String(String original):使用原字符串original的拷贝以构造一个新字符串；
 - indexOf(String str)：返回str第一次出现的位置；
 - substring(int begin, int end)：返回下标begin到end-1的字符串；
 - equals(Object obj)：比较字符串的值是否与obj的字符串的相同，应使用常量或确定有值的对象来调用equals方法。

#### 4)String、StringBuffer和StringBuilder使用比较
名称 | 特点 | 使用场景
:-: | :-: | :-:
String | ①不可变字符序列，String一经初始化后内容不会改变了；<br/>②对String的操作实际上对其副本（原始拷贝）的操作，原本字符串一点都没有改变；<br/>③如果多次执行改变串内容操作，会导致大量副本字符串对象留在内存中，降低效率。 | 固定的、不经常变化的字符串数据| 
StringBuffer | ①可变字符序列，线程安全，但是效率低；<br/>②其是对原子符串本身操作，可以对字符串进行修改而不产生副本拷贝；<br/>③append方法用于追加字符串，类似于String的“+”。 | 多线程环境下，需要经常变化的字符串|
StringBuilder | ①可变字符串序列，线程不安全，但是效率高；<br/>②使用方法与StringBuffer类型。 | 单线程环境下，需要经常变化的字符串|

#### 5)包装类
 - 概念：在面向对象的操作中经常需要将基本数据转为对象，这个基本和基本数据类型对应的类统称为包装类（wrapper class）；
 - 用途：
  - 作为和基本数据类型对应的类型存在，方便对象的操作和数组转换；
  - 当业务方法的参数是Object时，不能传入基本数据类型，需要使用包装类，包装类型的初始值为null；

基本数据类型 | 包装类
:-: | :-:
byte | Byte| 
boolean | Boolean|
char | Character|
short | Short|
int | Integer|
long | Long|
float | Float|
double | Double|

#### 6)包装类的数值转换
 - 基本数据类型-包装类：
  - 基本数据类型-》包装类：使用包装类的valueOf()方法，参数为对应的基本数据类型的值，返回一个包装类的对象；
  - 包装类-》基本数据类型：使用包装类XXX的XXXValue()，如`Integer i1=new Integer(10); int i = i1.intValue();`

 - 基本数据类型/包装类-字符串：
  - 基本数据类型-》字符串：可以使用基本数据类型+""，还可以使用String.valueOf(基本数据类型)方法；
  - 包装类-》字符串：使用包装类的toString()方法；
  - 字符串-》基本数据类型/包装类：调用包装类的parseXXX(String str)方法，如`int i = Integer.parseInt("10");`

#### 7)容器类
名称 | 特点
:-: | :-:
Collection和Map | ①Collection是一个独立元素的序列，这些元素都服从一条或多条规则；<br/>②Map是一组成对的“键值对”对象，允许使用键来查找值。| 
HashSet | ①集合元素不重复；<br/>②不保证集合中原始的顺序；<br/>③只能允许有一个null元素|
Vector | ①基于数组的数据结构，同步的，线程安全；<br/>②执行效率不如ArrayList|
ArrayList | ①基于数组的数据结构，不同步，线程不安全；<br/>②查找、修改元素效率高|
LinkedList | ①基于链表的数据结构，允许null元素；<br/>②增加、删除元素效率高|
HashMap | ①key值不能重复；<br/>②线程不安全。效率高；<br/>③null值可以作为key，且只能有一个|
Hashtable | ①key值不能重复；<br/>②线程安全，效率不如HashMap；<br/>③key和value都不能出现null|

### 4.IO流
 - 概念：IO是实现输入和输出的基础，在Java中通过流的形式允许Java程序使用相同的方式来访问不同的输入和输出源；
 - 体系：
  - 磁盘操作：File
  - 字节操作：InputStream和OutputStream
  - 字符操作：Reader和Writer
  - 对象操作：Serializable
  - 网络操作：Socket
  - 新的输入/输出：NIO
 - File对象：负责程序与文件之间的数据传输，在Java中文件被抽象为File类，可以表示文件和文件夹；
 - 使用字节流读写文件：
  - 读：
```java
public void bitR() throws Exception{
    File file = new File("/home/soldier/Downloads/test.txt");
    //从文件中读取内容
    FileInputStream fileInputStream = new FileInputStream(file);
    byte b[] = new byte[(int) file.length()];
    int len = fileInputStream.read(b);//读取后返回长度
    fileInputStream.close();
    System.err.println(new String(b));
}
```
  - 写：
```java
public void bitW() throws Exception{
    File file = new File("/home/soldier/Downloads/test.txt");
    FileOutputStream fileOutputStream = new FileOutputStream(file);
    fileOutputStream.write("soldier is good！\n".getBytes());//可以指定编码
    fileOutputStream.close();
}
```
 - 使用字符流读写文件：
  - 读：
```java
public void charR() throws Exception{
    FileReader fileReader = new FileReader("/home/soldier/Downloads/test.txt");
    BufferedReader bufferedReader = new BufferedReader(fileReader);
    String bString;
    while ((bString = bufferedReader.readLine())!=null) {//逐行读取
        System.out.println(bString);
    }
    bufferedReader.close();
}
```
  - 写：
```java
public void charW() throws Exception{
    FileWriter fileWriter = new FileWriter("/home/soldier/Downloads/test.txt");
    PrintWriter printWriter = new PrintWriter(fileWriter);
    printWriter.write("soldier is good！");
    printWriter.close();
}
```

### 5.Java的异常
 - **概念：** 指当程序中某些地方出错时创建的一种特殊的运行时错误对象，程序捕捉到这个异常后，可以编写相应的异常处理代码进行处理；
 - **分类：** Throwable 可以用来表示任何可以作为异常抛出的类，分为两种：Error和Exception。其中Error用来表示JVM无法处理的错误，Exception分为两种：
  - 受检异常：需要用try...catch...语句捕获并进行处理，并且可以从异常中恢复；
  - 非受检异常：是程序运行时错误，例如除0会引发 Arichmetic Exception，此时程序奔溃并无法恢复。

## 第二章、SQL基础知识
### 1.数据对象
1. 数据库表：由行和列组成；
2. 用户：所谓用户就是有权限访问数据库的人，同时需要自己登陆账号和密码；
3. 索引：是指根据指定的数据库表列建立起来的顺序，达到快速访问数据的目的；
4. 触发器：是一个用户定义SQL事务命令的集合，当对一个表进行插入、修改、删除时，这组命令就会自动执行；
5. 视图：视图看上去跟表类似，但其实是一个虚拟的表，并不在数据库中真实存在。

### 2.数据库用户及权限
#### 1)用户
1. 创建用户：create user username@'host' identified by 'password';
2. 用户授权：grant 权限 on database.tables to username@'host';
3. 修改密码：alter user username@'host' identified by 'newPassword';
4. 删除用户：drop user username@'host';

#### 2)数据库权限
1. 三种系统权限：DBA、RESOURCE、CONNECT;
2. 六种数据对象权限：ALL、DELETE、INSERT、ALTER、UPDATE、SELECT ON;
3. GRANT赋予权限：grant 权限1,权限2,... on username1,username2,...;
4. REMOVE收回权限：remove 权限1,权限2,... on username1,username2,...;

### 3.数据查询语句
1. **多表查询：** 允许你在查询的from条件后面跟多个表，然后把表之间的关系用where进行连接；
```sql
select a.stuname from student a, course b where a.stuid = b.stuid;
```
2. 外连接：多表查询的外连接技术，可以列出多表查询中其中一个表的全部记录；
```sql
# 显示左表全部记录，右表匹配不上时用null
select * from student t1 left join course t2 on a.stuid = b.stuid;
# 显示左表全部记录，右表匹配不上时用null
select * from student t1 right join course t2 on a.stuid = b.stuid;
# Oracle数据库支持full join，mysql不支持，可以使用左外连接+ union +右外连接实现
select * from student t1 left join course t2 on a.stuid = b.stuid
    union
select * from student t1 right join course t2 on a.stuid = b.stuid;
```
3. 嵌套查询：select查询语句里可以嵌入select查询语句；
```sql
# 查询学生有几门课
select name, (select count(*) from course where course.stuid=student.stuid) as courses from student;
```
4. 子查询：将子查询或in或exists当成where条件的一部分；
```sql
# 查询学生为soldier的学生
select * from student where stuid in (select stuid from course where name='soldier');
select * from student where EXISTS(select name from student where name='soldier');
```
5. 查询排序：order by语句根据指定的列对结果集进行排序；
```sql
# asc是升序、desc是降序，默认asc
select * from student order by student.name asc;
```
6. 查询分组：SQL无法把正常的列和汇总函数结合在一起，这时需要group by子句；
```sql
# 统计学生的课程数
select stuid, count(courseid) from course group by stuid;
```
7. 优化查询语句的两种方法
    7.1 表的查询顺序：①from子句中的第一个表最先处理叫基础表，多表查询时应选择记录最少的表做基础表；②3个表连接查询时，应选择交叉表作为基础表；
    7.2 用exists替代in：因为exists是判断真假，二in是与之一一比较。

### 4.数据操作
1. 插入
```sql
insert into table(colum1, colum2, ...) values (value1, value2, ...);
```
2. 修改
```sql
update table set colum1=value1, colum2=value2, ... where 条件;
```
3. 删除
```sql
delete from table where 条件;
```

**注意：** 以上语句操作完成后，必须加上事务处理结束的命令commit才能生效，如果想撤回可以使用rollback命令复原。

### 5.数据表定义
1. 创建表
```sql
create table tablename (
    id int,
    fullname varcher(20)
);
```
2. 修改表
```sql
alter table tablename add sex int(11);
```
3. 删除表
```sql
drop table tablename;
```

## 第三章、Linux常用命令
### 1.ls 显示文件名
**命令格式：** `ls [option] file`
option：
 - -l 显示详细列表
  - 结果域1：文件类型和文件权限
    - 第一个字符表示文件类型(-表示普通文件、d表示目录...)；
    - 后面9个字符以每三个划分，分别表示文件拥有者、文件所属组、其他用户的权限
  - 域2：文件连接数
  - 域3：文件拥有者名字
  - 域4：文件所属组名字
  - 域5：文件长度
  - 域6-8：最近修改时间
  - 域9：文件名
 - -a 显示所有文件，包括隐藏文件
 - -R 显示文件及所有子目录
 - -F 显示文件(文件名后跟*)和目录(后跟/)
 - -d 与l选项合用，显示目录名而非其内容

### 2.cd 目录转换
注意目录分隔符为‘/’，与dos想反
**命令格式：** `cd dirname`

### 3.pwd 显示当前路径
**命令格式：** `pwd`

### 4.cat 显示文件内容
**命令格式：** `cat filename`

### 5.more 以分页方式查看文件内容
**命令格式：** `more filename`

快捷键 | 含义
:-: | :-:
Enter | 向下n行，需要定义，默认1行| 
Ctrl+F或空格键 | 向下滚动1屏|
Ctrl+B | 向上滚动1屏|
= | 输出当前行的符号|
q | 退出more|

### 6.vi 创建/编辑文件
**命令格式：** `vi filename`

快捷键 | 含义 | 快捷键 | 含义
:-: | :-: | :-: | :-:
Ctrl+F | 向文件尾翻1屏 | Ctrl+B | 向文件首翻1屏|
i | 在光标前插入文本 | I | 在当前行首插入文本|
a | 在光标后插入文本 | A | 在当前行尾插入文本|
:w | 保存 | :x或:wq | 保存并退出文件|
:q | 退出vi | :q! | 不保存并退出文件|
x | 非编辑状态下删除一个字符 | dd | 非编辑状态下删除整行|
Esc | 退出编辑状态 | :set nu | 显示文件行号|
/ | 搜索 | n | 搜索状态下查看下一个结果，N则是上一个|

### 7.df 查看问价系统磁盘使用情况
**命令格式：** `df [option]`
option：
 - -k：按照千字节查看；
 - -m：按照兆字节查看；

### 8.chmod 改变文件权限
**命令格式：** `chmod [options] mode file`
只能文件拥有者或特权用户才能使用该功能来改变文件权限。mode可以是数字形式或以who opcode permission形式表示；who是可选的，默认是a(所有用户)；只能选择一个opcode(操作码)；可指定多个mode，以逗号隔开。
options：
 - -c：只输出被改变文件信息；
 - -R：可递归遍历子目录，把修改应用到目录下所有文件和目录；
 - who：u用户、g组、o其它、a所有用户(默认)；
 - opcode：+增加权限，-删除权限；
 - permission：r读、w写、x执行

我们多用三位八进制数字的形式来表示权限，第一位指定文件拥有者的权限，第一位指定文件所属组的权限，第一位指定其他用户的权限，每位通过4(读)、2(写)、1(执行)三种数值的和来确定权限，如：
 - 3(2+1)：表示有写和执行权限
 - 5(4+1)：表示读和执行权限
 - 6(4+2)：表示有读写权限
 - 7(4+2+1)：表示有全部权限

例子：
 - `chmod u+x file`：给file的拥有者执行权限；
 - `chmod 751 file`：给file的拥有者全部权限，给file所属组读和执行权限，给其它用户执行权限；

### ps 显示当前进程状态
**命令格式：** `ps [option]`
例子：
 - `ps -u root`：显示root用户进程信息；
 - `ps -ef`：显示全部进程，连带命令行；

### mkdir 创建目录
**命令格式：** `mkdir [-p] dirname`
例子：
 - `mkdir dir1`：建立一个新目录dir1；
 - `mkdir -p dir/subdir`：建立多级目录；

### cp 文件复制
**命令格式：** `cp [-r] source destination`
例子：
 - `cp file1 file2`：复制file1为file2，不覆盖；
 - `cp file1 dir/`：将file1复制到dir目录下；
 - `cp /etc/profile .`：将etc下的profile复制到当前目录下；
 - `cp -r /etc dir2`：复制整个目录，若dir2存在，则将etc目录复制到dir2目录下；若不存在，则新增dir2目录并将etc下的所有文件和目录复制到dir2目录下；

### mv 文件移动
**命令格式：** `mv source' destination`
例子：
 - `mv file1 file2`：将file1重命名为file2；
 - `mv file1 dir`：将file1移动到dir目录下下；
 - `mv dir1 dir2`：移动整个目录，若dir2存在，则将dir1目录移动到dir2目录下；若不存在，则新增dir2目录并将dir1下的所有文件和目录移动到dir2目录下，然后dir1消失，相当于重命名；

### rm 删除文件
**命令格式：** `rm [-r] file/dir` -f表示不用确认
例子：
 - `rm file1`：删除file1文件或file1空目录；
 - `rm -r dir1`：删除dir1目录及其所有子目录和文件；
 - `rm file?`：删除以file开头的、文件名个数为5的文件或空目录；
 - `rm f*`：删除以f开头的文件或空目录；

### find 文件查找
**命令格式：** `find dir -name filename`
例子：
 - `find . -name hello`：寻找当前目录及子目录下叫hello的文档；
 - `find . -name hello*`：寻找当前目录及子目录下以hello开头的文档；
 - `find . -ctime +7`：寻找7天前创建的文档，-mtime表示修改，-7表示7天内，7表示等于7天；
 - `find . -size +2000c`：与上个命令类似，找出当前目录下大小超过2000 bytes的文档；
 - `find /tmp -user soldier`：查找在tmp目录下归属用户soldier的文档；

### tar 归档命令
**命令格式：** `tar [主选项+辅选项] 文件或目录`
主选项：
 - -c：新建一个压缩包；
 - -x：解压文件；
 - -t：查看压缩文件的内容；
 - -r：向压缩文件追加文件；
 - -u：更新原压缩包的文件；

辅助选项：
 - -z：是否同时具有gzip的属性？即是否需要用gzip压缩或解压？一般格式为xxx.tar.gz或xxx.tgz；
 - -v：显示操作工程；
 - -f：使用文档名，注意要在f之后立即接文档名，不能再加其它参数；

例子：
 - `tar -cvf 123.tar 111 222`：将111和222压缩成123.tar；
 - `tar -zcvf 123.tar.gz 111 222`：使用gzip将111和222压缩成123.tar.gz；
 - `tar -tvf 123.tar`：查看123.tar中的文件；
 - `tar -xvf 123.tar`：解压123.tar；
 - `tar -zxvf 123.tar.gz`：使用gzip解压123.tar.gz；
 - `tar -rvf 123.tar 333`：将333追加到123.tar中；
