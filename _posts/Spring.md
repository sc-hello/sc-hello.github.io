@[TOC](目录)
# 1 Profile+1
1. profile是用来完成不同环境下，配置动态切换功能的；
2. profile配置方式：
	2.1 多profile文件方式：提供多个配置文件，每个代表一种环境：
		- application-dev.properties/yml开发环境
		- application-test.properties/yml测试环境
		- application-pro.properties/yml生产环境
	2.2 yml多文档方式：
		- 在yml中使用 --- 分隔不同配置
3. profile激活方式：
	3.1 配置文件：在配置文件中配置：spring.profiles.active=dev
	3.2 虚拟机参数：在VM aptions指定：-Dspring.profiles.active=dev
	3.3 命令行参数：java-jar xxx.jar --spring.profiles.active=dev

# 2 Spring Boot内部配置文件加载顺序+1
Spring Boot程序启动时，会从以下位置加载配置文件：

 1. file:./config/：当前目录下的/config目录
 2. file:./：当前项目的根目录
 3. classpath:/config/：classpath的/config目录
 4. classpath:/：classpath的根目录

加载顺序为以上的排列顺序，高优先级配置的属性会生效。

# 3 Condition+1
1. 自定义条件：
	- 自定义条件类：自定义条件类实现Condition接口，重写matches方法，在matches方法中进行逻辑判断，返回boolean值。matches方法有两个参数：
	
		- context：上下文对象，可以获取属性值，获取类加载器，获取BeanFactory等；
		- metadata：元数据对象，用于获取注解属性；
		
	- 判断条件：初始化Bean时，使用@Conditional(条件类.class)注解；

2. Spring Boot提供的常用条件注解：

	- ConditionalOnProperty：判断配置文件中是否有对应属性和值才初始化Bean；
	- ConditonalOnClass：判断环境中是否有对应字节码文件才初始化Bean；
	- ConditionalOnMissingBean：判断环境中没有对应Bean才初始化Bean；


# 5 Maven下各种包的含义+1
1. Config：用于存放相关配置类，包括启动类；
2. Controller：所有请求的入口，前后端交互的入口；
3. Service：负责所有的业务逻辑；
4. Mapper：或称Dao（数据库访问对象），持久层，负责Java和数据库交互，包括interface和xml两类文件；（（View\Web）表示层调用控制层（Controller），控制层调用业务层（Service），业务层调用数据访问层（Dao）。）
5. Domain：或称Po(persistent object，持久对象)，用Java类来映射数据库表，类名就相当于表名，类的属性就相当于表的字段；
6. Dto(Data Transfer Object)：数据传输对象，用于前后端数据交互；
7. Domain类的属性完全和表的字段一致；Dto类的属性一般和表字段一致，但会根据不同的业务场景适当增加或减少属性；
8. Domain用于Java数据和数据库表记录的映射，用在Service和Mapper；Dto用于前后端数据传输，用在Service和Controller；Service介于Controller和Mapper之间，也是Domain和Dto的转换层；

# 6 Idea快捷键+1

 - Ctrl + F：当前文件查找
 - Ctrl + R：当前文件替换
 - Ctrl + Shift + F：全局查找
 - Ctrl + Shift + R：全局替换
 - Ctrl + Shift + N：按文件名查找文件
 - Ctrl + Shift + A：查找所有的菜单或操作
 - 连按两次Shift：查找文件、菜单、操作等，但不能查找文件内容
 - 复制历史：Ctrl+Shift+V

# 什么是Spring框架+1
1. 是一种轻量级开发框架，旨在提高开发人员的开发效率和系统的可维护性；
2. 是很多模块的集合，使用这些模块可以很方便地协助我们进行开发，这些模块包括：核心容器、数据访问/集成、Web、AOP（面向切面编程）、工具、消息和测试模块；
3. 核心技术：依赖注入（DI）、AOP、事件（events）、资源、i18n、验证、数据绑定、类型转换、SpEL;
4. 测试 ：模拟对象，TestContext框架，Spring MVC 测试，WebTestClient;
5. 数据访问 ：事务，DAO⽀持，JDBC，ORM，编组XML;
6. Web⽀持 : Spring MVC和Spring WebFlux Web框架;
7. 集成 ：远程处理，JMS，JCA，JMX，电⼦邮件，任务，调度，缓存;


# 列举⼀些重要的Spring模块+1

 - Spring Core： 核心，Spring 其他所有的功能都需要依赖于该类库，主要提供 IoC 依赖注⼊功能；
 - Spring Aspects： 该模块为与AspectJ的集成提供⽀持；
 - Spring AOP ：提供了⾯向切⾯的编程实现；
 - Spring JDBC : Java数据库连接；
 - Spring JMS ：Java消息服务；
 - Spring ORM : ⽤于⽀持Hibernate等ORM⼯具；
 - Spring Web : 为创建Web应⽤程序提供⽀持；
 - Spring Test : 提供了对 JUnit 和 TestNG 测试的⽀持；

# @RestController 和 @Controller+1

 - Controller 返回⼀个⻚⾯：单独使⽤ @Controller 不加 @ResponseBody 的话⼀般使⽤在要返回⼀个视图的情况，这种情况属于比较传统的Spring MVC 的应⽤，对应于前后端不分离的情况；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625115850517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

 - @RestController 返回JSON 或 XML 形式数据：但 @RestController 只返回对象，对象数据直接以 JSON 或 XML 形式写⼊ HTTP 响应(Response)中，属于 RESTful Web服务，这也是⽬前⽇常开发所接触的最常⽤的情况（前后端分离）；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625130724690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

# 控制反转(IoC)和依赖注入(DI) +1
1. 控制反转（IoC）与依赖注入（DI）含义相同，只不过是从两个角度描述同一个概念;
2. **控制反转**：1 当某个Java对象需要调用另一个Java对象时，在传统模式下，调用者通常采用“new”的代码方式来创建对象；2 而在Spring框架下，对象的实例不再由调用者来创建，而是由Spring容器来创建；3 这样，控制权由应用代码转移到了Spring容器，控制权发生了反转，这就是Spring的控制反转；
3. **依赖注入**：Spring容器负责将被依赖对象赋值给调用者的成员变量，这相当于为调用者注入了它依赖的实例，即依赖注入；

# IoC+1
1. IoC（控制反转）是⼀种设计思想，就是将原本在程序中⼿动创建对象的控制权，交由Spring框架来管理;
2. IoC 容器是 Spring⽤来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value），Map 中存放的是各种对象，将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注⼊，这样可以很⼤程度上简化应⽤的开发，把应⽤从复杂的依赖关系中解放出来；
3.  IoC 容器就像是⼀个⼯⼚，当我们需要创建⼀个对象的时候，只需要配置好配置⽂件/注解即可，完全不⽤考虑对象是如何被创建出来的；
4. Spring IoC最关键的作用在于解耦，它可以解除对象之间的耦合，让对象和对象之间完全没有联系，这样我们在完成或修改一个对象时不需要考虑其它对象；
5. Spring 时代⼀般通过 XML ⽂件来配置 Bean，后来开发⼈员觉得 XML ⽂件来配置不太好，于是Spring Boot 注解配置就慢慢开始流⾏起来；
6. Spring IoC的初始化过程：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625131504444.png)


# AOP+1

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


# Spring MVC+1
1. MVC 是⼀种设计模式，Spring MVC 是⼀款很优秀的 MVC 框架，可以帮助我们进⾏更简洁的Web层的开发，并且它天⽣与 Spring 框架集成；
2. Spring MVC 下⼀般把后端项⽬分为 Controller层(控制层，返回数据给前台⻚⾯)、Service层（处理业务）、Dao层（数据库操作）、Entity层（实体类）；
3. 

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626143943543.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)


# Spring MVC 工作原理+1

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626144508359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

1. 客户端（浏览器）发送请求，直接请求到 DispatcherServlet ；
2. DispatcherServlet 根据请求信息调⽤ HandlerMapping ，解析请求对应的 Handler；
3. 解析到对应的Handler（即Controller 控制器）后，开始由HandlerAdapter 适配器处理；
4. HandlerAdapter 会根据 Handler 来调⽤真正的处理器并处理请求，并处理相应的业务逻辑；
5. 处理器处理完业务后，会返回⼀个 ModelAndView 对象， Model 是返回的数据对象， View是逻辑上的 View；
6. ViewResolver 会根据逻辑 View 查找实际的 View ；
7. DispaterServlet 把返回的 Model 传给 View （视图渲染）；
8. 把 View 返回给请求者（浏览器）；

# Spring 框架中⽤到了哪些设计模式？
