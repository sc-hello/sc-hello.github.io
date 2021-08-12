@[TOC](目录)
# Maven下各种包的含义
1. Config：用于存放相关配置类，包括启动类；
2. Controller：所有请求的入口，前后端交互的入口；
3. Service：负责所有的业务逻辑；
4. Mapper：或称Dao（数据库访问对象），持久层，负责Java和数据库交互，包括interface和xml两类文件；（（View\Web）表示层调用控制层（Controller），控制层调用业务层（Service），业务层调用数据访问层（Dao）。）
5. Domain：或称Po(persistent object，持久对象)，用Java类来映射数据库表，类名就相当于表名，类的属性就相当于表的字段；
6. Dto(Data Transfer Object)：数据传输对象，用于前后端数据交互；
7. Domain类的属性完全和表的字段一致；Dto类的属性一般和表字段一致，但会根据不同的业务场景适当增加或减少属性；
8. Domain用于Java数据和数据库表记录的映射，用在Service和Mapper；Dto用于前后端数据传输，用在Service和Controller；Service介于Controller和Mapper之间，也是Domain和Dto的转换层；

# Idea快捷键

 - Ctrl + F：当前文件查找
 - Ctrl + R：当前文件替换
 - Ctrl + Shift + F：全局查找
 - Ctrl + Shift + R：全局替换
 - Ctrl + Shift + N：按文件名查找文件
 - Ctrl + Shift + A：查找所有的菜单或操作
 - 连按两次Shift：查找文件、菜单、操作等，但不能查找文件内容
 - 复制历史：Ctrl+Shift+V

# Spring框架介绍
1. 轻量级的J2EE框架，是一个控制反转（IoC）和面向切面（AOP）的容器框架，用来装JavaBean（Java对象）；
2. 是一个中间层框架（万能胶），可以起到连接作用，比如把Struts和Hibernate粘合在一起运用，使企业开发更快更简洁；
3. 从大小和开销两方面而言Spring都是轻量级的；
4. 通过控制反转(IoC)达到松耦合的目的；
5. 提供了面向切面编程的支持，允许通过分离应用的业务逻辑与系统级服务进行内聚性开发；
6. 包含并管理应用对象（Bean）的配置和生命周期，这个意义上是一个容器；
7. 将简单的组件配置组合称为复杂的应用，这个意义上是一个框架；


# 列举⼀些重要的Spring模块

 - Spring Core： 核心，Spring 其他所有的功能都需要依赖于该类库，主要提供 IoC 依赖注⼊功能；
 - Spring Aspects： 该模块为与AspectJ的集成提供⽀持；
 - Spring AOP ：提供了⾯向切⾯的编程实现；
 - Spring JDBC : Java数据库连接；
 - Spring JMS ：Java消息服务；
 - Spring ORM : ⽤于⽀持Hibernate等ORM⼯具；
 - Spring Web : 为创建Web应⽤程序提供⽀持；
 - Spring Test : 提供了对 JUnit 和 TestNG 测试的⽀持；

# @RestController 和 @Controller

 - Controller 返回⼀个⻚⾯：单独使⽤ @Controller 不加 @ResponseBody 的话⼀般使⽤在要返回⼀个视图的情况，这种情况属于比较传统的Spring MVC 的应⽤，对应于前后端不分离的情况；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625115850517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

 - @RestController 返回JSON 或 XML 形式数据：但 @RestController 只返回对象，对象数据直接以 JSON 或 XML 形式写⼊ HTTP 响应(Response)中，属于 RESTful Web服务，这也是⽬前⽇常开发所接触的最常⽤的情况（前后端分离）；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625130724690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

# 控制反转(IoC)和依赖注入(DI) 
1. 控制反转（IoC）与依赖注入（DI）含义相同，只不过是从两个角度描述同一个概念;
2. **控制反转**：1 当某个Java对象需要调用另一个Java对象时，在传统模式下，调用者通常采用“new”的代码方式来创建对象；2 而在Spring框架下，对象的实例不再由调用者来创建，而是由Spring容器来创建；3 这样，控制权由应用代码转移到了Spring容器，控制权发生了反转，这就是Spring的控制反转；
3. **依赖注入**：Spring容器负责将被依赖对象赋值给调用者的成员变量，这相当于为调用者注入了它依赖的实例，即依赖注入；

# IoC
1. IoC（控制反转）是⼀种设计思想，就是将原本在程序中⼿动创建对象的控制权，交由Spring框架来管理;
2. IoC 容器是 Spring⽤来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value），Map 中存放的是各种对象，将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注⼊，这样可以很⼤程度上简化应⽤的开发，把应⽤从复杂的依赖关系中解放出来；
3.  IoC 容器就像是⼀个⼯⼚，当我们需要创建⼀个对象的时候，只需要配置好配置⽂件/注解即可，完全不⽤考虑对象是如何被创建出来的；
4. Spring IoC最关键的作用在于解耦，它可以解除对象之间的耦合，让对象和对象之间完全没有联系，这样我们在完成或修改一个对象时不需要考虑其它对象；
5. Spring 时代⼀般通过 XML ⽂件来配置 Bean，后来开发⼈员觉得 XML ⽂件来配置不太好，于是Spring Boot 注解配置就慢慢开始流⾏起来；
6. Spring IoC的初始化过程：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625131504444.png)


# AOP

1. AOP(⾯向切⾯编程)能够将那些与业务⽆关，却为业务模块所共同调⽤的逻辑或责任（例如事务处理、⽇志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性；
2. Spring AOP是基于动态代理的，动态代理就是在运行时动态地生成目标对象的代理对象，在代理对象中对目标对象的方法进行增强；
3. AOP中几个重要概念：1 **通知**：就是会在目标方法执行前后执行的方法；2 **切入点**：应用通知进行增强的目标方法；3 **连接点**：可以应用通知进行增强的方法，一旦连接点被增强，它就成为了切入点；4 **切面**：是切入点和通知的结合；5 **织入**：就是通过动态代理对目标对象方法进行增强的过程；

示例：

1 创建一个UserDao类：

```java
@Repository
public class UserDao {
	 public void addUser(){
		 System.out.println("添加用户");
	 }
	 public void updateUser(){
		 System.out.println("修改用户");
	 }
	 public void deleteUser(){
		 System.out.println("删除用户");
	 }
}
```
2 创建一个切面类：

```java
@Aspect
public class MyAspectLog {
    /**
     * 方法执行完后执行的方法
     */
	@After(value="execution(* cn.xh.dao.UserDao.addUser(..))")
    public void log(){
        System.out.println("记录日志");
    }
}
```

3 在spring配置文件中加入：

```java
<!-- 启动@aspectj的自动代理支持-->
<aop:aspectj-autoproxy />

<!-- 定义aspect类 -->
<bean name="myAspect" class="cn.xh.dao.MyAspectLog "/>
```

4 当我们创建UserDao的对象userDao调用addUser方法的时候会打印“添加用户”，“记录日志”


# Spring MVC
1. MVC 是⼀种设计模式，Spring MVC 是⼀款很优秀的 MVC 框架，可以帮助我们进⾏更简洁的Web层的开发，并且它天⽣与 Spring 框架集成；
2. Spring MVC 下⼀般把后端项⽬分为 Controller层(控制层，返回数据给前台⻚⾯)、Service层（处理业务）、Dao层（数据库操作）、Entity层（实体类）；
3. 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626143943543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)


## Spring MVC 工作原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626144508359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

1. 客户端（浏览器）发送请求，直接请求到 DispatcherServlet ；
2. DispatcherServlet 根据请求信息调⽤ HandlerMapping ，解析请求对应的 Handler；
3. 解析到对应的Handler（即Controller 控制器）后，开始由HandlerAdapter 适配器处理；
4. HandlerAdapter 会根据 Handler 来调⽤真正的处理器并处理请求，并处理相应的业务逻辑；
5. 处理器处理完业务后，会返回⼀个 ModelAndView 对象， Model 是返回的数据对象， View是逻辑上的 View；
6. ViewResolver 会根据逻辑 View 查找实际的 View ；
7. DispaterServlet 把返回的 Model 传给 View （视图渲染）；
8. 把 View 返回给请求者（浏览器）；

## Spring MVC涉及到的组件
0. Spring MVC 涉及到的组件有 DispatcherServlet（前端控制器）、HandlerMapping（处理器映射器）、HandlerAdapter（处理器适配器）、Handler（处理器）、ViewResolver（视图解析器）和 View（视图）；
1. DispatcherServlet 是前端控制器，Spring MVC 的所有请求都要经过 DispatcherServlet 来统一分发。DispatcherServlet 相当于一个转发器或中央处理器，控制整个流程的执行，对各个组件进行统一调度，以降低组件之间的耦合性，有利于组件之间的拓展；
2. HandlerMapping 是处理器映射器，其作用是根据请求的 URL 路径，通过注解或者 XML 配置，寻找匹配处理器（Handler）信息；
3. HandlerAdapter 是处理器适配器，其作用是根据映射器找到的处理器（Handler）信息，按照特定规则执行相关的处理器（Handler）；
4. Handler 是处理器（即Controller），和 Java Servlet 扮演的角色一致。其作用是执行相关的请求处理逻辑，并返回相应的数据和视图信息，将其封装至 ModelAndView 对象中；
5. View Resolver 是视图解析器，其作用是进行解析操作，通过 ModelAndView 对象中的 View 信息将逻辑视图名解析成真正的视图 View（如通过一个 JSP 路径返回一个真正的 JSP 页面）；
6. View 是视图，其本身是一个接口，实现类支持不同的 View 类型（JSP、FreeMarker、Excel 等）。

以上组件中，需要开发人员进行开发的是处理器（Handler，常称Controller）和视图（View）。通俗的说，要开发处理该请求的具体代码逻辑，以及最终展示给用户的界面。

# 常用的SpringBoot注解，及其实现（未完，待续）
1. @SpringBootApplication：标识了一个SpringBoot工程，实际上是另外3个注解的组合：
	1. @SpringBootConfiguration：这个注解实际上就是一个@Configuration，表示启动类也是一个配置类；
	2. @EnableAutoConfiguration：向Spring容器中导入了一个Selector，用来加载ClassPath下SpringFactories中定义的自动配置类，将这些自动加载为配置Bean；
	3. @ComponentScan：标识扫描路径，因为默认没有配置实际扫描路径，所以Spring Boot扫描的路径是启动类所在的当前目录；

2. @Bean注解：用来定义Bean，类似于XML中的`<bean>`标签，Spring在启动时，会对加了@Bean注解的方法进行解析，将方法的名字作为beanName，并通过执行方法得到bean对象；
