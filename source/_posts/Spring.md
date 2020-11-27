---
title: 面试题—Spring框架面试总结
tags: [面试题, Java面试题, Spring， 框架]
index_img: /resource/img/Spring.png
date: 2020-11-18 15:49:57
---

## Spring是什么？
<font color=#FF000>Spring</font>是一个轻量级的IoC和AOP容器框架。是为Java应用程序提供基础性服务的一套框架，目的是用于简化企业应用程序的开发，它使得开发者只需要关心业务需求。常见的配置方式有三种：**基于XML的配置**、**基于注解的配置**、**基于Java的配置**。
主要由以下几个模块组成：
 - **Spring Core**：核心类库，提供IoC服务；
 - **Spring Context**：提供框架式的Bean访问方式，以及企业级功能（[JNDI](https://baike.baidu.com/item/JNDI)、定时任务等）；
 - **Spring AOP**：AOP服务；
 - **Spring DAO**：对JDBC的抽象，简化了数据访问异常的处理；
 - **Spring ORM**：对现有的ORM框架的支持；
 - **Spring Web**：提供了基本的面向Web的综合特性，例如多文件上传；
 - **Spring MVC**：提供面向Web应用的Model-View-Controller实现；

## Spring的优点是什么？
1. Spring属于低侵入式设计，代码的污染极低；
2. Spring的DI机制将对象之间的依赖关系交由框架处理，减低组件的耦合性；
3. Spring提供了AOP技术，支持将一些通用的任务，如：安全、事务、日志、权限等进行集中式管理，从而提供更好的复用；
4. Spring对主流的应用框架提供了集成支持。

## Spring的AOP理解
&nbsp;&nbsp;OOP面向对象。允许开发者定义纵向的关系，但并适用于定义横向的关系，导致了大量代码的重复，而不利于各个模块的 应用。
&nbsp;&nbsp;**AOP**，一般称为面向切面，作为面向对象的一种补充，<font color=#FF000>用于将那些与业务无关，但却对多个对象产生影响的公共行为和逻辑，抽取并封装为一个可重用的模块</font>，这个模块被命名为“切面”（Aspect），减少系统中的重复代码，降低了模块间的耦合度，同时提高了系统的可维护性。可用于权限认证、日志、事务处理。
&nbsp;&nbsp;**AOP实现的关键**在于代理模式，AOP代理主要分为**静态代理**和**动态代理** 。静态代理的代表为AspectJ；动态代理则以Spring AOP为代表。
(1) **AspectJ是静态代理的增强**，所谓静态代理，就是AOP框架会在编译阶段生成AOP代理类，因此也称为编译时增强，它会在编译阶段将AspectJ（切面）织入到Java字节码中，运行的时候就是增强之后的AOP对象。
(2) **Spring AOP使用的是动态代理**，所谓动态代理，就是说AOP框架不会去修改字节码，而是每次运行时在内存中临时为方法生成一个AOP对象，这个AOP对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。

**`Spring AOP中的动态代理主要有两种方式，JDK动态代理和CGLIB动态代理`** ：
1. JDK动态代理只提供接口的代理，不支持类的代理，核心是InvocationHandler接口和Proxy类，InvocationHandler通过invoke()方法反射来调用目标类中的代码，动态地将横切逻辑和业务编织在一起；接着Proxy类利用InvocationHandler接口动态创建一个符合某一接口的实例，生成目标类的代理对象。
> InvocationHandler的invoke(Object  proxy,Method  method,Object[] args)方法参数解析：
>> proxy：是最终生成的代理实例；
>> method：是被代理目标实例的某个具体方法；
>> args：是被代理目标实例某个方法的具体入参，在方法反射调用时使用。
2. 如果代理类没有实现InvocationHandler接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Core Generation Library）是一个代码生成的类库，可以在运行时动态的生成指定类的一个子类对象，并覆盖其中特定方法并添加增强代码，从而实现AOP。CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。
3. **静态代理与动态代理的区别**在于生成AOP代理对象的时机不同，相对来说AspectJ的静态代理方式具有更好的性能，但是AspectJ需要特定的编译器进行处理，而Spring AOP则无需特定的编译器处理。

## Spring的IoC理解
1. `IoC就是控制反转`，是指创建对象的控制权的转移，以前创建对象的主动权是时机是由自己把控的，而现在这种权力转移到Spring容器中，并由容器根据配置文件去创建实例和管理各个实例之间的依赖关系，对象与对象之间松散耦合，也利于功能的复用。`DI依赖注入`，和控制反转是同一个概念的不同角度的描述，即：应用程序在运行时依赖IoC容器来动态注入对象需要的外部资源。
2. 最直观的表达就是，IoC让对象的创建不用去 new 了，可以由Spring自动生产，使用Java的反射机制，根据配置文件在程序运行时动态的去创建对象以及管理对象，并调用对象的方法。
3. Spring的IoC有三种注入方式：构造器注入、setter方法注入、根据注解注入。

> IoC让相互协作的组件保持松散的耦合，而AOP编程允许你把遍布于应用各层的功能分离出来形成可重用的功能组件。

## BeanFactory和ApplicationContext有什么区别？
BeanFactory和ApplicationContext是Spring的两大核心接口，都可以当做Spring的容器。其中ApplicationContext是BeanFactory的子接口。
1. **BeanFactory**：是Spring里面最底层的接口，包含了各种Bean的定义，读取Bean配置文档，管理Bean的加载、实例化，控制Bean的生命周期，维护Bean之间的依赖关系。**ApplicationContext**作为BeanFactory的派生，除了提供BeanFactory所具有的功能外，还提供了更完整的架构功能：
&nbsp;&nbsp;&nbsp;&nbsp;① 继承MessageSource，因此支持国际化；
&nbsp;&nbsp;&nbsp;&nbsp;② 统一的资源文件访问方式；
&nbsp;&nbsp;&nbsp;&nbsp;③ 提供在监听器中注册Bean的事件；
&nbsp;&nbsp;&nbsp;&nbsp;④ 同时加载多个配置文件；
&nbsp;&nbsp;&nbsp;&nbsp;⑤ 载入多个（有继承关系）上下文，使用每一个上下文对象都专注于一个特定的层次，比如应用的web层。
2. <font color=#0000FF>BeanFactory采用的是延迟加载的形式来注入Bean的，即只有在使用到某个Bean时（调用getBean()），才对该Bean进行加载实例化。</font>弊端是：不能及时发现一些存在的Spring配置问题，如果Bean的某一个属性没有注入，BeanFactory加载后直到第一次调用getBean方法才会抛出异常。
<font color=#0000FF>ApplicationContext是在容器启动时，一次性创建了所有的Bean</font>，可以及时发现Spring配置中存在的错误，利于检查所依赖属性是否注入。ApplicationContext启动后预载入所有单例的Bean，通过预载入单例Bean确保需要时无需等待，因为它们已经创建好了。
相对于基本的BeanFactory，<font color=#0000FF>ApplicationContext唯一不足是占用内存空间</font>，当应用程序配置的Bean较多时程序启动较慢。
3. BeanFactory通常以编程的方式被创建，ApplicationContext还能以声明的方式创建，如：使用ContextLoader；
4. BeanFactory和ApplicationContext都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的**区别**是：BeanFactory需要手动注册，而ApplicationContext则是自动注册。

## Spring Bean的生命周期
首先说一下Servlet的生命周期：实例化 &rarr; 初始init &rarr; 接收请求service &rarr; 销毁destroy；
Spring上下文中的Bean生命周期也类似：
<font color=#0000FF>**（1）实例化Bean：**</font>
对于BeanFactory容器，当客户向容器请求一个尚未初始化的Bean时，或初始化Bean的时候需要注入另一个尚未初始化的依赖时，容器就会调用createBean进行实例化。对于ApplicationContext容器，当容器启动结束后，通过获取BeanDefinition对象中的信息，实例化所有的Bean。

<font color=#0000FF>**（2）设置对象属性（依赖注入）：**</font>
实例化后的对象被封装在BeanWrapper对象中，紧接着，Spring根据BeanDefinition中的信息以及通过BeanWrapper提供的设置属性的接口完成依赖注入。

<font color=#0000FF>**（3）处理Aware接口：**</font>
接着，Spring会检测该对象是否实现了xxxAware接口，并将相关的xxxAware实例注入给Bean：
① 如果这个Bean已经实现了BeanNameAware接口，会调用它实现的setBeanName(String beanId)方法，此处传递的就是Spring配置文件中Bean的id值；
② 如果这个Bean已经实现了BeanFactoryAware接口，会调用它实现的setBeanFactory()方法，传递的事Spring工厂本身； 
③ 如果这个Bean已经实现了ApplicationContextAware接口，会调用setApplicationContext(ApplicationContext context)方法，传入Spring上下文对象

<font color=#0000FF>**（4）BeanPostProcessor：**</font>
如果想对Bean进行一些自定义的处理，那么可以让Bean实现BeanPostProcessor接口，那将会调用postProcessBeforeInitialization(Object obj, String s)方法。

<font color=#0000FF>**（5）InitializingBean 与init-method：**</font>
如果Bean在Spring配置文件中配置了init-method属性，则会自动调用其配置的初始化方法。

<font color=#0000FF>**（6）如果这个Bean走了第四步：**</font>
如果这个Bean实现了BeanPostProcessor接口，将会调用postProcessBeforeInitialization(Object obj, String s)方法；由于这个方法是在Bean初始化结束之后调用的，所以可应用于内存或缓存技术；

`以上几个步骤完成后，Bean就已经被正确创建了，之后就可以使用这个Bean了。`&uarr;&uarr;&uarr;

<font color=#0000FF>**（7）DisposableBean(清理)：**</font>
当Bean不再需要时，会经过清理阶段，如果Bean实现了DisposableBean这个接口，会调用其实现的destroy()方法；

<font color=#0000FF>**（8）destroy-method：**</font>
最后，如果这个Bean的Spring配置中配置了destroy-method属性，就会自动调用其配置的销毁方法。

## 解释一下Spring支持的几种Bean的作用域
Spring容器中的Bean可以分为5个范围：
1. **singleton**：<font color=#FF000>默认</font>，每个容器中只有一个Bean的实例，单例模式由BeanFactory自身维护；
2. **prototype**：为每一个Bean请求提供一个实例；
3. **request**：为每一个网络请求创建一个实例，在请求完成后，Bean会失效并被垃圾回收器回收；
4. **session**：与request范围类似，确保每个session中有一个Bean实例，在session过期后，Bean会随之失效；
5. **global-session**：全局作用域，global-session和Portlet应用相关。当你的应用部署在Portlet容器中工作时，它包含很对portlet。如果你想要声明让所有的portlet共用全局的存储变量的话，那么这个全局变量需要存储在global-session中。全局作用域与Servlet的session作用域效果相同。

## Spring框架中的单例Beans是线程安全的吗？
Spring框架并没有对单例Bean进行任何多线程的封装处理。关于单例Bean的线程安全和并发问题需要开发者自行去搞定。关于单例Bean的线程安全和并发问题需要开发者自行去搞定。但实际上大部分的Spring Bean并没有可变的状态（比如Serview类和DAO类），所以在某种程度上说Spring的单例Bean是线程安全的。如果Bean有多种状态的话（比如View Model对象），就需要自行保证线程安全。最浅显的解决办法就是将多态Bean的作用域由“singleton”变更为“prototype”。

## Spring如何处理线程并发问题？
在一般情况下，只有无状态的Bean才可以在多线程环境下共享，在Spring中，绝大部分Bean都可以声明为singleton作用域，因为Spring对一些Bean中非线程安全状态采用<font color=#0000FF>ThreadLocal进行处理，解决线程安全问题</font>；
ThreadLocal和线程同步机制都是为了解决多线程中相同变量访问冲突问题。同步机制采用了“时间换空间”的方式，仅提供一份变量，不同线程在访问前需要获取锁，没获得锁的线程则需要排队。而ThreadLocal采用了“时间换空间”的方式。
ThreadLocal会为每一个线程提供一个独立的变量副本，从而隔离了多个线程对数据的访问冲突。因为每一个线程都拥有自己的变量副本（JMM内存模型），从而也就没有必要对该变量进行同步了。ThreadLocal提供了线程安全的共享对象，在编写多线程代码时，可以把不安全的变量封装进ThreadLocal。

## Spring基于xml注入Bean的几种方式
1. set方法注入；
2. 构造器注入：①通过index设置参数位置；②通过type设置参数类型；
3. 静态工厂注入；
4. 实例工厂；

## Spring的自动装配
在Spring中，对象无需自己查找或创建与其关联的其它对象，由容器负责把需要相互协作的对象引用赋予各个对象，使用Autowire来自动装载模式。
在Spring框架xml配置中共有5中自动装配：
1. **no**：默认的方式是不进行自动装配的，通过手工设置ref属性来进行装配Bean；
2. **byName**：通过Bean的名称进行自动装配，如果一个Bean的property与另一个Bean的name相同，就进行自动装配；
3. **byType**：通过参数的数据类型进行自动装配；
4. **constructor**：利用构造函数进行装配，并且构造函数的参数通过byType进行装配；
5. **autodetect**：自动探测，如果有构造方法，则通过constructor方式进行自动装配，否则使用byType方式；

基于注解的方式：
使用@Autowired注解来自动装配指定的bean。在使用@Autowired注解之前需要在Spring配置文件进行配置，<context:annotation-config />。在启动spring IoC时，容器自动装载了一个AutowiredAnnotationBeanPostProcessor后置处理器，当容器扫描到@Autowied、@Resource或@Inject时，就会在IoC容器自动查找需要的bean，并装配给该对象的属性。在使用@Autowired时，首先在容器中查询对应类型的bean：
 - 如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据；
 - 如果查询的结果不止一个，那么@Autowired会根据名称来查找；
 - 如果上述查找的结果为空，那么会抛出异常。解决方法时，使用required=false。

## Spring中用到了哪些设计模式？
1. 工厂模式：BeanFactory就是简单工厂模式的体现，用来创建对象的实例；
2. 单例模式：Bean默认为单例模式；
3. 代理模式：Spring的AOP功能用到了JDK的动态代理和CGLIB字节码生成技术；
4. 模板模式：用来解决代码重复的问题，比如：RestTemplate、JmsTemplate、JpaTemplate；
5. 观察者模式：定义对象见一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象就会得到通知被制动更新，如Spring中listener的实现——ApplicationListener。

## @Component和@Bean的区别是什么？
1. `作用对象不同`：@Component注解作用于类，而@Bean注解作用于方法；
2. `@Component通常是通过类路径扫描来自动侦测以及自动装配到Spring容器中(我们可以使用@ComponentScan注解定义要扫描的路径`。@Bean注解通常是我们在标有该注解的方法中定义产生这个Bean，@Bean告诉Spring这个是某个类的实例，当需要时还给我；
3. `@Bean注解比@Component注解的自定义性更强`，而且很多地方我们只能通过@Bean注解来注册Bean，比如当我们引用第三方库中的类需要装配到Spring容器时，只能通过@Bean来实现。

@Bean注解使用实例：
```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }
}
```
上面的代码相当于下面的xml配置：
```xml
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```
下面这个例子无法通过@Component实现：
```java
@Bean
public OneService getService(int status) {
    case (status)  {
        when 1:
                return new serviceImpl1();
        when 2:
                return new serviceImpl2();
    }
}
```

## 类声明为Spring的Bean的注解有哪些？
 - **@Component**：通用的注解，可标注任意类为 Spring 组件。
 - **@Repository**：对应DAO层，主要同于数据库相关操作；
 - **@Service**：对应服务层，主要涉及一些复杂的逻辑，需要用到DAO层；
 - **@Controller**：对应Spring MVC控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面

## Spring管理事务的方式有几种？
1. **编程式事务**，在代码中硬编程，`不推荐`；
2. **声明式事务**，在配置文件中配置，`推荐`，共分为两种：
    1. 基于XML的声明式事务；
    2. 基于注解的声明式事务。

## Spring的事务传播行为
1. **PROPAGATION_REQUIRED**：如果当前没有事务，就创建一个新事务，如果当前存在事务，就加入该事务，该设置是最常用的设置。
2. **PROPAGATION_SUPPORTS**：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就以非事务执行。‘
3. **PROPAGATION_MANDATORY**：支持当前事务，如果当前存在事务，就加入该事务，如果当前不存在事务，就抛出异常。
4. **PROPAGATION_REQUIRES_NEW**：创建新事务，无论当前存不存在事务，都创建新事务。
5. **PROPAGATION_NOT_SUPPORTED**：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
6. **PROPAGATION_NEVER**：以非事务方式执行，如果当前存在事务，则抛出异常。
7. **PROPAGATION_NESTED**：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则按REQUIRED属性执行。

## Spring事务中的隔离级别有哪几种?
1. **ISOLATION_DEFAULT**：这是个 PlatfromTransactionManager 默认的隔离级别，使用数据库默认的事务隔离级别。
2. **ISOLATION_READ_UNCOMMITTED**：读未提交，允许另外一个事务可以看到这个事务未提交的数据。
3. **ISOLATION_READ_COMMITTED**：读已提交，保证一个事务修改的数据提交后才能被另一事务读取，而且能看到该事务对已有记录的更新。
4. **ISOLATION_REPEATABLE_READ**：可重复读，保证一个事务修改的数据提交后才能被另一事务读取，但是不能看到该事务对已有记录的更新。
5. **ISOLATION_SERIALIZABLE**：一个事务在执行的过程中完全看不到其他事务对数据库所做的更新。

## SpringMVC的工作原理了解嘛？
![](/resource/img/SpringMVC.jpg)
<font color=#FF000>**流程说明**：</font>
1. 客户端发送请求，直接请求到 DispatcherServlet；
2. DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler；
3. 解析得到的这个Handler（也就是我们平时说的Controller）后，开始由 HandlerAdapter 适配器处理；
4. HandlerAdapter 会根据 Handler（Controller控制器） 来调用真正的处理器处理请求，并处理相应的业务逻辑。处理完成后会返回一个 ModelAndView 对象：Model是返回的数据对象、View是个逻辑上的View；
5. ViewResolver 会根据逻辑 View 查找实际的View；
6. DispatcherServlet 把返回的Model 传给 View 进行视图渲染；
7. 最后把View 返回给客户端。
