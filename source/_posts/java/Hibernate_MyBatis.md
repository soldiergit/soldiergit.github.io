---
title: Hibernate和MyBatis的区别
tags: [面试题, Java面试题, Hibernate, MyBatis， 框架, ORM框架]
index_img: /resource/img/java/Hibernate_MyBatis.png
date: 2020-11-27 20:29:03
---

## 简介
### Hibernate
**Hibernate** 是一个开源的对象关系映射框架，它对 JDBC 进行了非常轻量级的对象封装，它将 POJO 与数据库建立映射关系，是一个全自动的 ORM 框架。
 - Hibernate 可以自动生成简单的 SQL 语句，自动执行，对于复杂的 SQL 语句，Hibernate 不需要自己编写，因为它的性能不是很好；
 - Hibernate可以应用在任何使用JDBC的场合，既可以在Java的客户端程序使用，也可以在Servlet/JSP的Web应用中使用，最具革命意义的是，Hibernate可以在应用EJB的JaveEE架构中取代CMP，完成数据持久化的重任。

### MyBatis
**MyBatis** 本事 Apache 的一个开源项目 iBatis，是一款优秀的半自动 ORM 框架，避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。
 - 是一个支持普通的 SQL 查询、存储过程和高级映射的优秀持久层框架；
 - 可以自动生成简单的 SQL 语句，自动执行；对于复杂的 SQL，需要自己编写，因此具有更好的性能；
 - 不能自动创建表；
 - 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs（普通的 Java 对象）映射成数据库中的记录

`MyBatis 相比于 JDBC 的优点`：
把 SQL 语句从 Java 代码中抽取出来，方便维护。并且修改 SQL 时不需要修改 Java 代码，不用手动设置参数和对结果集进行处理。

## Hibernate 和 MyBatis 的区别
 - Hibernate 是一个全自动 ORM 框架，功能更加强大，操作更加简单，不需要自己写 SQL，所以不需要掌握数据库相关知识，但性能不好控制，适合中小型项目；

 - MyBatis 是一个半自动 ORM 框架，也封装了很多原生的 SQL，需要了解数据库相关知识，需要自己写 SQL ，性能可控。

### 开发速度对比
就开发速度而言，Hibernate 的真正掌握要比 MyBatis 要难。MyBatis 框架相对简单容易上手，但也相对简陋些。个人觉得要用好 MyBatis 还是要先理解好 Hibernate。

比起二者的开发速度，不仅仅要考虑到二者的特性及性能，还要根据项目需求去考虑究竟哪个更适合项目开发：一个项目中用到的复杂查询基本没有，就是简单的增删改查，这样选择 Hibernate 效率就很快了，因为基本的 SQL 语句都已经被封装好了；但是对于一个大型项目，复杂语句较多，选择 MyBatis 就会快很多，而且语句的管理也比较方便。

### 开发工作量的对比
Hibernate 和 MyBatis 都有相应的代码生成工具，可以生成简单基本的 DAO 层方法。针对高级查询，MyBatis 需要手动编写 SQL 语句，以及 ResultMap。而 Hibernate 有良好的映射机制，开发者无需关心 SQL 的生成以及结果映射，可以更专注于业务流程。

### sql 优化方面
Hibernate 的查询会将表中的所有字段查询出来，这一点会有性能消耗，当然也可以自己写 SQL 来指定，但这样就破坏了 Hibernate 开发的简洁性。而 MyBatis 的 SQL 是手动编写的，所以可以按需求指定查询的字段。

Hibernate HQL 语句的调优需要将 SQL 打印出来，而 Hibernate 的 SQL 被很多人嫌弃太丑；MyBatis 的 SQL 是自己手动写的所以调整方便。但 Hibernate 具有自己的日志统计；MyBatis 本身不带日志统计，使用 Log4j 进行日志记录。

### 对象管理的对比
Hibernate 是完整的对象/关系映射解决方案，它提供了对象状态管理(state management)的功能，使开发者不再需要理会底层数据库系统的细节。
 - 相对于常见的 JDBC/SQL 持久层方案中需要管理 SQL 语句，Hibernate 采用了更自然的面向对象的视角来持久化 Java 应用中的数据。
 - 使用 Hibernate 的开发者应该总是关注对象的状态state，不必考虑 SQL 语句的执行，因为这部分细节已经由 Hibernate 掌管妥当，只有开发者子啊进行系统性能调优的时候才需要进行了解。而MyBatis 在这一块没有文档说明，用户需要自己对对象进行详细的管理。

### 数据库扩展性计较
 - Hibernate 与数据库具体的关联在 XML 文件中，所以 HQL 对具体是用什么数据库并不是很关心；
 - MyBatis 由于所有 SQL 都是依赖数据库书写的，所以扩展性、迁移性比较差。

## 缓存机制对比
### Hibernate 缓存
 - 一级缓存是 Session 缓存，利用好一级缓存就需要严格管理好 Session 的生命周期。建议在一个Action 操作中使用一个 Session；
 - 二级缓存是 SessionFactory 级的缓存或进程级缓存，它可以被所有 Session 共享，它的生命周期伴随着 SessionFactory 的生命周期存亡，除了以内存作为存储介质外，还可以选用硬盘等外部存储设备。SessionFactory 的缓存分为内置缓存和外置缓存：
    - 内置缓存中存放的是 SessionFactory 对象的一些集合属性包含的数据（映射元素及预定 SQL 语句等），对于应用程序来说，它是只读的；
    - 外置缓存中存放的是数据库数据的副本，其作用和一级缓存类似。

### MyBatis 缓存
MyBatis 包含一个非常强大的查询缓存特性，可以方便地配置和定制。MyBatis 3中的缓存实现了很多改进，使得它更加强大且易于配置。

MyBatis 的一级缓存是在会话（SqlSession）层面进行缓存的。MyBatis 的一级缓存是默认开启的，不需要任何的配置。
要开启二级缓存，你需要在你的 SQL 映射文件中添加`<cache/>`标签，这个简单的语句效果如下：
1. 映射语句文件中所有的 select 语句将会被缓存；
2. 映射语句文件中所有的 insert、update 和 delete 语句会刷新缓存；
3. 缓存会使用 LRU算法（Last Recently Used，最近最少使用）来回收；
4. 根据时间表（比如：no Flush Interval，没有刷新间隔），缓存不会以任何时间顺序来刷新；
5. 缓存会存储列表集合或对象（无论查询方法返回什么）的1024个引用；
6. 缓存会被视为 read/write（可读/可写）的缓存，意味着对象检索不是共享的，而且可以安全地被调用者修改，而不干扰其它调用者或线程所做的潜在修改。

所有的这些属性都可以通过缓存元素的属性来修改：`<cache eviction=”FIFO” flushInterval=”60000″ size=”512″ readOnly=”true”/>`
这个更高级的配置创建了一个 FIFO 缓存，并每隔60秒刷新，存数列表集合或对象的512个引用，而且返回的对象被认为是只读的，因此在不同线程中的调用者之间修改它们会导致冲突。可用的回收策略有：
1. LRU（默认）-最近最少使用：移除最长时间不被使用的对象；
2. FIFO-先进先出：按对象进入缓存的顺序来移除它们；
3. SOFT-软引用：移除基于垃圾回收器状态和软引用规则的对象；
4. WEAK-弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。

*参数解析：*
 - **eviction**：指定缓存回收策略；
 - **flushInterval**：指定缓存刷新间隔，可以被设置为任意的正整数，而且它们代表一个合理的毫秒形式的时间段，默认情况是不设置的，也就是没有刷新间隔，缓存仅仅调用语句时才刷新；
 - **size**：指定缓存引用数目，可以被设置为任意的正整数，要记住你缓存的对象数目和你运行环境的可用内存资源数目，默认值是1024；
 - **readOnly**：只读，可以被设置为 true 或 false，只读的缓存会给所有调用者返回缓存对象的相同实例，因此这些对象不能被修改，这提供了很重要的性能优势（可读写的缓存会返回缓存对象的拷贝(通过序列化)。这会慢一些,但是安全,因此默认是 false）；

### 相同点
Hibernate 和 MyBatis 的二级缓存除了采用系统默认的缓存机制外，预留了集成第三方缓存的接口，都可以通过实现你自己的缓存或为其它第三方缓存方案，创建适配器来完全覆盖缓存行为。

### 不同点
 - Hibernate 的二级缓存配置在 SessionFactory 生成的配置文件中进行详细配置，然后再在具体的数据库表—Java对象映射中配置是那种缓存；
 - MyBatis 的二级缓存配置都是在每个具体的数据库表—Java对象映射中进行详细配置，这样针对不同的表可以使用不用的缓存机制，并且 MyBatis 可以在命名空间中共享相同的缓存配置和实例，通过 `Cache-ref` 来实现。

<font color=#FF000>*两者比较：*</font>
 - 因为 Hibernate 对查询对象有着良好的管理机制，用户无需关心 SQL。所以在使用二级缓存时如果出现脏数据，系统会报出错误并提示；
 - MyBatis在这方面使用二级缓存是需要特别小心，如果不能完全确定数据更新操作波及的范围，就要避免 Cache 的使用，否则脏数据的出现会给系统的正常运行带来很大的隐患。

## 总结
### 两个框架的相同点
Hibernate 和 MyBatis 都可以通过 SessionFactoryBuilder 由 XML 配置文件生成 SessionFactory，然后由 SessionFactory 生成 Session，最后由 Session 来开启执行事务和 SQL 语句。
 - 其中 SessionFactoryBuilder、SessionFactory、Session 的生命周期都差不多；
 - Hibernate 和 MyBatis 都支持 JDBC 和 JTA（Java Transaction API：Java 事务编程接口） 事务处理。

### MyBatis 优势
 - MyBatis 可以进行更为细致的 SQL 优化，可以减少查询字段；
 - MyBatis 容易掌握，而 Hibernate 门槛较高。

### Hibernate 优势
 - Hibernate 的 DAO 层开发比 MyBatis 简单，MyBatis 需要维护 SQL 和结果映射；
 - Hibernate 对对象的维护和缓存要比 MyBatis 好，对 CRUD 的对象的维护要方便；
 - Hibernate 数据库移植性很好，而 MyBatis 的数据库移植性不好，因为不同的数据库要写不同的 SQL；
 - Hibernate 有更好的二级缓存机制，可以使用第三方缓存，而 MyBatis 本身提供的缓存机制不佳；

## MyBatis中#{}和${}的区别
`#{}` 是预编译处理，`${}` 是字符串替换：
 - MyBatis 在处理 #{} 时，会把 SQL 中的 #{} 替换为 ?(SQL占位符)，调用 PreparedStatement 的 set 方法来赋值；
 - MyBatis 在处理 ${} 时，就是把 ${} 替换为变量的值；
 - <font color=#FF000>重点</font>：#{} 的存在与 JDBC 中出现 PreparedStatement 对象一样，都是为了防止 SQL 注入，提高系统安全性。

## MyBatis中实体类的属性名和数据库表的字段名不一样怎么办？
1. 通过在`mapper.xml`查询的 SQL 语句中使用 AS 关键字（也可以省略）定义字段的别名，让其与实体类的属性名一致：
```xml
<select id=”select” parametertype=”int” resultetype=”com.soldier.xxxEntity”>
   select order_id orderId, order_no orderNo ,order_price orderPrice form orders where order_id=#{id};
</select>
```
2. 通过在`mapper.xml`中使用 <resultMap/> 标签来映射数据库表与实体类的对应关系：
```xml
<resultMap type=”com.soldier.xxxEntity” id=”orderResultMap”>
    <!–用id属性来映射主键字段–>
    <id property=”orderId” column=”order_id”>
    <result property = “orderNo” column =”order_no”/>
    <result property=”orderPrice” column=”order_price”/>
</reslutMap>

<select id=”select” parametertype=”int” resulteMap=”orderResultMap”>
   select order_id, order_no, order_price form orders where order_id=#{id};
</select>
```

## 简述Mybatis的插件运行原理，以及如何编写一个插件
**答：** MyBatis 仅可以编写针对 ParameterHandler、ResultSetHandler、StatementHandler、Executor这四个接口的插件，MyBatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的 invoke() 方法，当然只会拦截那些你指定需要拦截的方法。

**编写插件：** 实现 MyBatis 的 Interceptor 接口并复写 intercept() 方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，还要在配置文件中配置编写好的插件。

## MyBatis是如何进行分页的？分页插件的原理是什么？
MyBatis 使用 RowBounds 对象进行分页，它是针对 ResultSet 结果集执行的内存分页，而非物理分页。可以在 SQL 内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用 MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 SQL，然后重写 SQL，根据 dialect 方言，添加对应的物理分页语句和物理分页参数。

## Hibernate 的工作原理
1. 读取并解析配置文件；
2. 读取并解析映射信息，创建 SessionFactory；
3. 打开 Session；
4. 创建事务 Transaction；
5. 持久化操作；
6. 提交事务；
7. 关闭 Session；
8. 关闭 SessionFactory。


