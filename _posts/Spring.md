@[TOC](目录)
# IoC
1. 控制反转（IOC，Inverse Of Control），即把创建对象的权利交给框架，也就是指将对象的创建、对象的存储、对象的管理交给了Spring容器。Spring容器是Spring中的一个核心模块，用于管理对象，底层可以理解为是一个Map集合。
2. Spring IoC最关键的作用在于解耦，它可以解除对象之间的耦合，让对象和对象之间完全没有联系，这样我们在完成或修改一个对象时不需要考虑其它对象；
3. 依赖注入：依赖注入（DI）与控制反转（IoC）含义相同，只不过是从两个角度描述同一个概念，Spring容器负责将被依赖对象赋值给调用者的成员变量，这相当于为调用者注入了它依赖的实例，即依赖注入；

# AOP
 - AOP（面向切面编程）：将那些与业务无关，却为业务模块所共同调用的逻辑或责任（日志记录、权限控制、性能统计等）分开封装起来，这样各司其职，不仅减少系统的重复代码，还降低了业务逻辑和通用功能的耦合度；
 - 保证开发者在不修改源代码的前提下，为系统中的业务组件添加某种通用功能。AOP 就是代理模式的典型应用。
 - **通知**：就是会在目标方法执行前后执行的方法；
 - **切入点**：应用通知进行增强的目标方法；
 - **连接点**：连接点就是可以应用通知进行增强的方法；
 - **切面**：切入点和通知的结合；
 - **织入**：就是通过动态代理对目标对象方法进行增强的过程；

**示例**

1 创建一个UserDao类：

```java
@Repository
// 以下3个方法都是连接点
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
     * log方法是后置通知，通过在方法上加上@After注解来表示
     * addUser()是切入点
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
<bean name="myAspect" class="cn.xh.dao.MyAspectLog"/>
```

4 当我们创建UserDao的对象userDao调用addUser方法的时候会打印“添加用户”，“记录日志”





# JSP——Servlet——Spring MVC
## MVC模式


## JSP+JavaBean
1. JSP+JavaBean 中 JSP 用于处理用户请求，JavaBean 用于封装和处理数据。该模式只有视图和模型，一般把控制器的功能交给视图来实现，适合业务流程比较简单的 Web 程序；

![在这里插入图片描述](https://img-blog.csdnimg.cn/ae73075fb5cd40b4b0ff55a0691a3234.png)

2. 通过上图可以发现 JSP 从 HTTP Request（请求）中获得所需的数据，并进行业务逻辑的处理，然后将结果通过 HTTP Response（响应）返回给浏览器。从中可见，JSP+JavaBean 模式在一定程度上实现了 MVC，即 JSP 将控制层和视图合二为一，JavaBean 为模型层；
3. JSP+JavaBean 模式中 JSP 身兼数职，既要负责视图层的数据显示，又要负责业务流程的控制，结构较为混乱，并且也不是我们所希望的松耦合架构模式，所以当业务流程复杂的时候并不推荐使用；

## Servlet+JSP+JavaBean
1. Servlet+JSP+JavaBean 中 Servlet 用于处理用户请求，JSP 用于数据显示，JavaBean 用于数据封装，适合复杂的 Web 程序；

![在这里插入图片描述](https://img-blog.csdnimg.cn/3802e41bc4c34b868d7177853ebde849.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAaGVsbG9zYzAx,size_18,color_FFFFFF,t_70,g_se,x_16)

2. 相比 JSP+JavaBean 模式来说，Servlet+JSP+JavaBean 模式将控制层单独划分出来负责业务流程的控制，接收请求，创建所需的 JavaBean 实例，并将处理后的数据返回视图层（JSP）进行界面数据展示。
3. Servlet+JSP+JavaBean 模式的结构清晰，是一个松耦合架构模式，一般情况下，建议使用该模式。

## Spring MVC
1. Spring MVC可以帮助我们进行更简洁的Web层的开发，它天生与 Spring 框架集成；
2. Spring MVC 下⼀般把后端项目分为 Controller层(控制层，返回数据给前台页面)、Service层（处理业务）、Dao层（数据库操作）、Entity层（实体类）；


**Spring MVC 工作流程**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210626144508359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

1. 客户端（浏览器）发送请求，请求到 DispatcherServlet（前端控制器） ；
2. DispatcherServlet 根据请求信息调用 HandlerMapping（处理器映射器），其作用是根据请求的 URL 路径，通过注解或者 XML 配置，寻找匹配的Handler（处理器）信息；
3. HandlerAdapter（处理器适配器）会根据处理器映射器找到的Handler信息按照特定规则执行相关的Handler；
4. Handler处理完业务后，会返回⼀个 ModelAndView 对象， Model 是数据对象， View是逻辑上的 View；
5. ViewResolver（视图解析器）会根据逻辑 View 查找实际的 View （如通过一个 JSP 路径返回一个真正的 JSP 页面）；
6. DispatcherServlet把返回的 Model 传给 View （视图渲染）；
7. 把 View 返回给请求者（浏览器）；

**Spring MVC 重要组件**

1 DispatcherServlet

 - **说明**：前端控制器，不需要工程师开发，由 SpringMVC 框架提供。
 - **作用**：Spring MVC 的入口。接收请求，响应结果，相当于转发器，中央处理器。DispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet 降低了组件之间的耦合度。

2 HandlerMapping

 - **说明**：处理器映射器，不需要工程师开发，由 SpringMVC 框架提供。
 - **作用**：根据请求的 url 查找 Handler。SpringMVC 提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

3 HandlerAdapter

 - **说明**：处理器适配器。
 - **作用**：按照特定规则（HandlerAdapter要求的规则）执行 Handler。通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

4 Handler

 - **说明**：处理器，需要工程师开发。
 - **注意**：编写 Handler 时按照 HandlerAdapter 的要求的规则去做，这样适配器才可以去正确执行 Handler, Handler 是后端控制器，在 DispatcherServlet 的控制下 Handler 对具体的用户请求进行处理。 由于 Handler 涉及到具体的用户业务请求，所以一般情况需要工程师根据业务需求开发 Handler。

5 ViewResolver

 - **说明**：视图解析器，不需要工程师开发，由 SpringMVC 框架提供。
 - **作用**：进行视图解析，根据逻辑视图名解析成真正的视图。ViewResolver 负责将处理结果生成 View 视图， ViewResolver 首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成 View 视图对象，最后对View 进行渲染将处理结果通过页面展示给用户。 SpringMVC 框架提供了很多的 View 视图类型，包括：jstlView、freemarkerView、pdfView等。 一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要工程师根据业务需求开发具体的页面。

6 View

 - **说明**：视图 View，需要工程师开发。
 - **作用**：View 是一个接口，实现类支持不同的 View类型（jsp、freemarker、pdf…）。


在 Spring MVC 框架中，Controller 替换 Servlet 来担负控制器的职责，用于接收请求，调用相应的 Model 进行处理，处理器完成业务处理后返回处理结果。Controller 调用相应的 View 并对处理结果进行视图渲染，最终客户端得到响应信息。

比于之前的servlet，它一定程度上简化了开发人员的工作，使用servlet的话需要每个请求都去在web.xml中配置一个servlet，而Spring 中的DispatcherServlet会拦截所有的请求，进一步去查找有没有合适的处理器，一个前端控制器就可以。

# Spring——Spring Boot
## Spring 
**1 方便解耦，简化开发**

通过Spring提供的IoC容器，我们可以将对象之间的依赖关系交由Spring进行控制，避免硬编码所造成的过度程序耦合。

**2 AOP编程的支持**

提供了面向切面编程实现，提供比如日志记录、权限控制、性能统计等通用功能和业务逻辑分离的技术，并且能动态的把这些功能添加到需要的代码中，这样各司其职，降低业务逻辑和通用功能的耦合。

保证开发者在不修改源代码的前提下，为系统中的业务组件添加某种通用功能。AOP 就是代理模式的典型应用。

**3 方便集成各种优秀框架**

Spring不排斥各种优秀的开源框架，其内部提供了对各种优秀框架（如MyBatis 等）的直接支持。

**4 方便程序的测试**

Spring 支持 JUnit4，可以通过注解方便地测试 Spring 程序。

**5 声明式事务的支持**

只需要通过配置就可以完成对事务的管理，而无须手动编程。

## Spring Boot
Spring Boot 具有 Spring 一切优秀特性，Spring 能做的事，Spring Boot 都可以做，而且使用更加简单，功能更加丰富，性能更加稳定而健壮。

**1  独立运行的 Spring 项目**

Spring Boot 可以以 jar 包的形式独立运行，Spring Boot 项目只需通过命令“ java–jar xx.jar” 即可运行。

**2 内嵌 Servlet 容器**

Spring Boot 使用嵌入式的 Servlet 容器（例如 Tomcat、Jetty 或者 Undertow 等），应用无需打成 WAR 包 。

**3 提供 starter 简化 Maven 配置**

Spring Boot 提供了一系列的“starter”项目对象模型（POMS）来简化 Maven 配置。

**4 提供了大量的自动配置**

Spring Boot 提供了大量的默认自动配置，来简化项目的开发，开发人员也通过配置文件修改默认配置。

**5 无代码生成和 xml 配置**

Spring Boot 不需要任何 xml 配置即可实现 Spring 的所有配置。
# 常用的SpringBoot注解，及其实现
1. @SpringBootApplication：标识了一个SpringBoot工程，实际上是另外3个注解的组合：
	1. @SpringBootConfiguration：这个注解实际上就是一个@Configuration，表示启动类也是一个配置类；
	2. @EnableAutoConfiguration：向Spring容器中导入了一个Selector，用来加载ClassPath下SpringFactories中定义的自动配置类，将这些自动加载为配置Bean；
	3. @ComponentScan：标识扫描路径，因为默认没有配置实际扫描路径，所以Spring Boot扫描的路径是启动类所在的当前目录；

2. @Bean注解：用来定义Bean，类似于XML中的`<bean>`标签，Spring在启动时，会对加了@Bean注解的方法进行解析，将方法的名字作为BeanName，并通过执行方法得到Bean对象；
# @RestController 和 @Controller
 - Controller 返回⼀个页面：单独使用 @Controller 不加 @ResponseBody 的话⼀般使用在要返回⼀个视图的情况，这种情况属于比较传统的Spring MVC 的应用，对应于前后端不分离的情况；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625115850517.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

 - @RestController 返回JSON 或 XML 形式数据：但 @RestController 只返回对象，对象数据直接以 JSON 或 XML 形式写⼊ HTTP 响应(Response)中，属于 RESTful Web服务，这也是⽬前⽇常开发所接触的最常⽤的情况（前后端分离）；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625130724690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NjMTc5,size_16,color_FFFFFF,t_70)

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




