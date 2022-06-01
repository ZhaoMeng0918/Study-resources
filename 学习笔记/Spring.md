### 使用Spring框架的好处是什么？

Spring是分层的Java SE/EE应用full-stack轻量级开源框架，以IOC和AOP为内核。提供了展现层SpringMVC和持久层Spring JDBCTemplate以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐称为使用最多的JavaEE企业级应用开源框架。

1. 方便解耦，简化开发：通过Spring提供的IOC容器，可以将对象间的依赖关系交由Spring进行控制，避免硬编码所造成的过度耦合。用户也不必再为单例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。
2. AOP编程的支持：通过SpringAOP功能，方便进行面向切面编程，许多不容易用传统OOP实现的功能可以通过AOP轻松实现。
3. 声明式事务管理：可以将我们从单调烦闷的事务管理代码中解脱出来，通过声明方式进行事务管理，提高开发效率和质量。
4. 方便程序的测试：可以用非容器依赖的编程方式进行几乎所有的测试工作，测试不再是昂贵的操作，而是随手可做的事情。
5. 方便集成各种优秀框架：Spring对各种优秀框架（Structs、Hibernate、Hessian、Quartz等）的支持。
6. 降低JavaEE API的使用难度：Spring对JavaEE API（如JDBC、JavaMail、远程调用等）进行了薄薄的封装，使这些 API 的使用难度大为降低。

### Spring由哪些模块组成？

Spring总共大约有20个模块，由1300多个不同的文件构成，而这些组件被分别整合在核心容器（Core Container）、AOP（Aspect Oriented Programming）和设备支持（Instrementation）、数据访问与集成（Data Access/Integeration）、Web、消息（Messaging）、Test等6个模块中。

- Spring Core：提供了框架的基本组成部分，包括控制反转（IOC）和依赖注入（DI）功能。
- Spring Beans：提供了BeanFactory，是工厂模式的一个经典实现，Spring将管理对象称为Bean。
- Spring Context：构建于Core封装包基础上的Context封装包，提供了一种框架式的对象访问方法。
- Spring JDBC：提供了一个JDBC的抽象层，消除了繁琐的JDBC编码和数据库厂商特有的错误代码解析，用于简化JDBC。
- Spring AOP：提供了面向切面的编程实现，让用户可以自定义拦截器、切点等。
- Spring Web：提供了针对Web开发的集成特性，例如文件上传，利用servlet listeners进行IOC容器初始化和针对Web的ApplicationContext/
- Spring Test：主要为测试提供支持的，支持使用JUnit或TestNG对Spring组件进行单元测试。

### Bean的基本配置

用于配置对象交由Spring来创建，默认情况下它调用的是类中的无参构造函数，如果没有无参构造函数则不能成功创建。

**Bean实例化三种方式**

- 构造方法实例化（默认是无参构造）
- 工厂静态方法实例化

```xml
<bean id="factory" class="" factory-method="" />
```

- 工厂实例方法实例化

```xml
<bean id="factory" class="" />
<bean id="userDao" factory-bean="factory" factory-method="" />
```

**Bean范围配置**

- singleton：默认值，单例。Bean的实例化个数只有1个，当Spring核心文件被加载时，就实例化配置的Bean。
  - 生命周期：
    - 对象创建：当应用加载，创建容器时，Bean就被创建了。
    - 对象运行：只要容器在，Bean就一直存活。
    - 对象销毁：当应用卸载，销毁容器时，对象就被销毁了。
- prototype：多例。
  - 生命周期：
    - 对象创建：当使用Bean时，创建新的对象实例。
    - 对象运行：只要Bean在使用中，就一直存活。
    - 对象销毁：当Bean长时间不使用，被垃圾回收器回收。
- request：web项目中，Spring创建一个Bean的对象，将对象存入到request域中。
- session：web项目中，Spring创建一个Bean的对象，将对象存入session域中。
- global session：web项目中，应用在Portlet环境中，如果没有Portlet环境，那么global session相当于session。

**Bean的依赖注入方式**

依赖注入（Dependency Injection）：它是Spring框架核心 IoC 的具体实现。

在编写程序时，通过控制反转，把对象的创建交给了Spring，但代码中不可能出现没有依赖的情况。IoC解耦只是降低他们的依赖关系，但不会消除。

那这种业务层和持久层的依赖关系，在使用Spring之后，就让Spring来维护了。简单的说，就是坐等框架把持久层对象传入业务层，而不用我们自己去获取。

- 构造方法（有参构造）

```xml
<bean id="userDao" class="xxx.xxx"/>
<bean id="userService" class="xxx.xxx">
    <constructor-arg name="userDao" ref="userDao" />
</bean>
```

- set方法

```xml
<bean id="userDao" class="xxx.xxx"/>
<bean id="userService" class="xxx.xxx">
    <!-- 要注入的是setUserDao() set后面的UserDao其中首字母小写 -->
    <property name="userDao" ref="userDao" />
</bean>
```

- set方法：P命名空间（简化了一点）

```xml
引入P命名空间
xmlns:p="http://www.springframework.org/schema/p"
修改注入方式
<bean id="userService" class="xxx.xxx" p:userDao-ref="userDao"></bean>
```

**Bean的依赖注入的数据类型**

- 普通数据类型

```xml
<bean id="userDao" class="xxx.xxx">
	<property name="" value="" />
</bean>
```

- 引用数据类型
- 集合数据类型

```xml
<bean id="userDao" class="xxx.xxx">
	<property name="list">
        <list>
        	<value></value><!-- 普通数据类型 -->
            <ref></ref><!-- 引用数据类型 -->
        </list>
    </property>
    <property name="map">
        <map>
        	<entry key="" value-ref=""></entry>
        </map>
    </property><property name="properties">
        <props>
        	<prop key=""></prop>
        </props>
    </property>
</bean>
```

### 引入其他配置文件（分模块开发）

实际开发中，Spring 的配置内容非常多，这就导致 Spring 配置很繁杂且体积庞大，所以，可以将部分配置拆解到其他配置文件中，而在 Spring 主配置文件通过 import 标签进行加载。

```xml
<import resourse="" />
```

### **小总结**

```xml
<bean>标签
    id属性：在容器中 Bean 实例的唯一标识，不允许重复
    class属性：要实例化的 Bean 的全限定名
    scope属性：Bean 的作用范围，常用是 singleton（默认）和prototype
    <property>标签：属性注入
        name属性：属性名称
        value属性：注入的普通属性值
        ref属性：注入的对象引用值
        <list>标签
        <map>标签
        <properties>标签
    <constructor-arg>标签
<import>标签：导入其他的 Spring 配置文件
```

### Spring相关API

**ApplicationContext的实现类**

applicationContext：接口类型，代表应用上下文，可以通过其实例获得Spring容器中的Bean对象。

- ClassPathApplicationContext：从类的根路径下加载配置文件，推荐使用
- FileSystemXmlApplicationContext：从磁盘地址加载配置文件
- AnnotationConfigApplicationContext：当使用注解配置容器对象时，需要使用此类来创建Spring容器，它用来读取注解

**getBean()方法使用**

- 可根据id获取
- 也可根据类路径获取（配置文件中仅允许存在一个，多个会报错）

### Spring注解开发

Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率，所以注解开发是一种趋势，注解代替xml配置文件可以简化配置，提高开发效率。

**配置扫描**

注意：使用注解进行开发时，需要在applicationContext.xml中配置组件扫描，作用是指定哪个包及其子包下的Bean需要进行扫描以便识别使用注解配置的类、字段和方法。

```xml
<!-- 注解的组件扫描 -->
<context:component-scan base-package="com.xxx" />
```

**Spring原始注解**

- @Component：使用在类上用于实例化Bean
- @Controller：使用在web层类上用于实例化Bean
- @Service：使用在service层类上用于实例化Bean
- @Repository：使用在dao层类上用于实例化Bean
- @Autowired：使用在字段上用于根据类型依赖注入（按照数据类型从Spring容器中进行匹配的），如果有多个就会出错。
- @Qualifier：结合@Autowired一起使用用于根据名称进行依赖注入（是按照id值从容器中进行匹配的，但是注意此处要结合@Autowired一起使用）
- @Resource：相当于@Autowired + @Qualifier，按照名称进行注入
- @Value：注入普通属性
- @Scope：标注Bean的作用范围
- @PostConstruct：使用在方法上标注该方法是Bean的初始化方法
- @PreDestory：使用在方法上标注该方法是Bean的销毁方法

**Spring新注解⭐⭐⭐**

PS：黑马课程P34可以多看两遍

使用上面的注解还不能全部替代xml配置文件，还需要使用注解替代的配置如下：

- 非自定义的Bean的配置：

```xml
<bean>
```

- 加载properties文件的配置：

```xml
<context:property-placeholder>
```

- 组件扫描的配置：

```xml
<context:component-scan>
```

- 引入其他文件：

```xml
<import>
```

可使用新注解，如下：

- @Configuration：用于指定当前类是一个Spring配置类，当创建容器时会从该类上加载注解
- @ComponentScan：用于指定Spring在初始化容器时要扫描的包，作用和在Spring的xml配置文件中的配置相同
- @Bean：使用在方法上，标注将该方法的返回值存储到Spring容器中
- @PropertySource：用于加载properties文件中的配置
- @Import：用于导入其他配置类

```java
@Configuration  // 标志该类是Spring的核心配置类
@ComponentScan("com.xxx")
@Import({DatasourceConfiguration.class})
public class SpringConfiguration {
    
}

@PropertySource("classpath:jdbc.properties")
public class DatasourceConfiguration {
    @Value("${jdbc.driver}")
    private String driver;
    
    @Value("${jdbc.url}")
    private String url;
    
    @Value("${jdbc.username}")
    private String username;
    
    @Value("${jdbc.password}")
    private String password;
    
    public DataSource getDataSource() {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass(driver);
        dataSource.setJdbcUrl(url);
        dataSource.setUser(username);
        dataSource.setPassword(password);
        return dataSource;    
    }
}

ApplicationContext app = new AnnotationConfigApplicationContext(SpringConfiguration.class);
```

### Spring配置数据源

**数据源（连接池）的作用**

- 数据源（连接池）是提高程序性能出现的
- 事先实例化数据源，初始化部分连接资源
- 使用连接资源时从数据源中获取
- 使用完毕后将连接资源归还给数据源

常见的数据源（连接池）：DBCP、C3P0、BoneCP、Druid等。

**数据源的开发步骤**

1. 导入数据源的坐标和数据库驱动坐标
2. 创建数据源对象
3. 设置数据源的基本连接数据
4. 使用数据源获取连接资源和归还连接资源

**Spring配置数据源**

可以将DataSource的创建权交由Spring容器去完成。

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPoolDataSource">
    <property name="driverClass" value="com.mysql.jdbc.Driver" />
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test" />
    <property name="user" value="root" />
    <property name="password" value="root" />
</bean>
```

**抽取jdbc配置文件**

applicationContext.xml加载jdbc.properties配置文件获得连接信息。

首先，需要引入context命名空间和约束路径。

```xml
命名空间：xmlns:context="http://www.springframework.org/schema/context"
约束路径：http://www.springframework.org/schema/context
                          http://www.springframework.org/schema/context/spring-context.xsd


<context:property-placeholder location="classpath:jdbc.properties" />
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPoolDataSource">
    <property name="driverClass" value="${jdbc.driver}" />
    <property name="jdbcUrl" value="${jdbc.url}" />
    <property name="user" value="${jdbc.username}" />
    <property name="password" value="${jdbc.password}" />
</bean>
```

### Spring集成Junit

在测试类中，每个测试方法都有以下两行代码：

```java
ApplicationContext app = new ClassPathXmlApplicationContext("bean.xml");
IAccountService as = app.getBean("accountService", IAccountService.class);
```

这两行代码的作用是获取容器，如果不写的话，直接会提示空指针异常，所以又不能轻易删掉。

上述问题解决思路：

- 让SpringJunit负责创建Spring容器，但是需要将配置文件的名称告诉它
- 将需要进行测试Bean直接在测试类中进行注入

**Spring集成Junit步骤：**

1. 导入Spring集成Junit的坐标
2. 使用@Runwith注解替换原来的运行期
3. 使用@ContextConfiguration指定配置文件或配置类
4. 使用@Autowired注入需要测试的对象
5. 创建测试方法进行测试

```java
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
</dependency>

@Runwith(SpringJunit4ClassRunner.class)
// @ContextConfiguration("class:applicationContext.xml") // 配置文件方式
@ContextConfiguration(calsses = {SpringConfiguration.class}) // 注解方式
public class SpringJunitTest {
    @Autowired
    private IUserService userService;
}
```

### Spring与Web环境集成

```xml
<denpendency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
</denpendency>
<denpendency>
    <groupId>javax.servlet.jsp</groupId>
    <artifactId>javax.servlet.jsp-api</artifactId>
</denpendency>
```

**ApplicationContext应用上下文获取方式**

应用上下文对象是通过new ClasspathXmlApplicationContext("配置文件")的方式获取的，但是每次从容器中获得Bean时都要编写new ClasspathXmlApplicationContext("配置文件")，这样的弊端是配置文件加载多次，应用上下文对象创建多次。

在Web项目中，可以使用ServletContextListener监听Web应用的启动，我们可以在Web应用启动时，就加载Spring的配置文件，创建应用上下文对象ApplicationContext，再将其存储到ServletContext域中，这样就可以在任意位置从域中获得应用上下文ApplicationContext对象了。

**Spring提供获取应用上下文的工具**

Spring提供了一个ContextLoaderListener就是对上述功能的封装，该监听器内部加载Spring配置文件，创建应用上下文对象，并存储到ServletContext域中，提供了一个客户端工具WebApplicationContextUtils供使用者获得应用上下文对象。

所以，只需要做两件事：

1. 在web.xml中配置ContextLoaderListener监听器（导入spring-web坐标）
2. 使用WebApplicationContextUtils获得应用上下文对象ApplicationContext

### SpringMVC简介

**开发步骤**

1. 导入SpringMVC相关坐标
2. 配置SpringMVC核心控制器DispatchServlet

```xml
<servlet>
    <servlet-name>DispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfiguration</param-name>
        <param-value>class:spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1<load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>DispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

4. 创建Controller类和视图页面
5. 使用注解配置Controller类中业务方法的映射地址（@Controller）
6. 配置SpringMVC核心配置文件spring-mvc.xml（配置组件扫描）

**SpringMVC执行流程⭐⭐⭐**

1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求，调用HandlerMapping处理器映射器
3. HandlerMapping找到具体的处理器（可以根据xml配置、注解进行查找），生成处理器对象及处理器拦截器（如果有则生成）一并返回给DispatcherServlet
4. DispatcherServlet调用HandlerAdapter处理器适配器
5. HandlerAdapter经过适配调用具体的处理器即Controller
6. Controller执行完成返回ModelAndView
7. HandlerAdapter将ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View
10. DispatcherServlet根据View进行渲染视图，响应用户

**SpringMVC注解解析**

- @RequestMapping
  - value：用于指定请求的URL，它和path属性的作用是一样的
  - method：用于指定请求的方式
  - params：用于指定限制请求参数的条件，它支持简单的表达式，要求请求参数的key和value必须和配置的一模一样

# Spring面经

## Bean的生命周期

1. Spring启动，查找并加载需要被Spring管理的Bean，进行Bean的实例化
2. Bean实例化后，对Bean的引用和值注入到Bean的属性中
3. 如果Bean实现了BeanNameAware接口的话，Spring将Bean的Id传递给setBeanName()方法
4. 如果Bean实现了BeanFactoryAware接口的话，Spring将调用setBeanFactory()方法，将BeanFactory容器实例注入
5. 如果Bean实现了ApplicationContextAware接口的话，Spring将调用Bean的setApplicationContext()方法，将Bean所在应用上下文引用传入进来
6. 如果Bean实现了BeanPostProcessor接口，Spring就调用它们的postProcessBeforeInitialization()方法
7. 如果Bean实现了InitializingBean接口，Spring将调用它们的afterPropertiesSet()方法。类似地，如果Bean使用init-method声明了初始化方法，该方法也会被调用
8. 如果Bean实现了BeanPostProcessor接口，Spring就将调用它们的postProcessAfterInitialization()方法
9. 此时，Bean已经准备就绪，可以被应用程序使用了。它们将一直驻留在应用上下文中，直到应用上下文被销毁
10. 如果Bean实现了DisposableBean接口，Spring将调用它的destory()接口方法，同样，如果Bean使用了destory-method声明销毁方法，该方法也会被调用。







