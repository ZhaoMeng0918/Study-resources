# Spring IOC试讲-20220601

## 一、课程大纲

1. 回顾：Spring是什么？Spring有哪些优势-IOC？Spring体系结构-IOC（了解）
2. Spring IOC是什么？为什么要使用IOC？（了解）
3. Spring IOC快速入门，看看IOC如何使用（掌握）
4. Bean的配置相关（掌握）
5. 基于注解的配置（掌握）
6. 动手实践

## 二、Spring是什么？Spring有哪些优势？Spring体系结构

### Spring 是什么？

Spring是分层的Java SE/EE应用full-stack轻量级开源框架，以IOC（Inverse of Control：控制反转）和AOP（Aspect Oriented Programming：面向切面编程）为内核。

提供了展现层SpringMVC和持久层Spring JDBCTemplate以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE企业应用开源框架。

### Spring有哪些优势？

1）**方便解耦，简化开发**

通过Spring提供的IoC容器，可以将对象间的依赖关系交由Spring进行控制，避免硬编码所造成的过度耦合。用户也不必再为单例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。

2）AOP编程的支持

通过Spring的AOP功能，方便进行面向切面编程，许多不容易用传统OOP实现的功能可以通过AOP轻松实现。

3）声明式事务的支持

可以将我们从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活的进行事务管理，提高开发效率和质量。

4）方便程序的测试

可以用非容器依赖的编程方式进行几乎所有的测试工作，测试不再是昂贵的操作，而是随手可做的事情。

5）方便集成各种优秀框架

Spring对各种优秀框架（Structs、Hibernate、Hessian、Quartz等）的支持。

6）降低 JavaEE API的使用难度

Spring对JavaEE API（如 JDBC、JavaMail、远程调用等）进行了薄薄的封装，使这些API的使用难度大为降低。

7）Java源码是经典学习范例

### Spring体系结构

![](E:\ProgramData\2_工作空间\2022.6~2022.7_东软代课\20220531_试讲\Spring体系结构图.png)

#### Core Container

Spring的核心容器是其他模块建立的基础，由Beans模块、Core核心模块、Context上下文模块和Expression Language表达式语言模块组成，具体介绍如下：

- **Beans模块**：提供了BeanFactory，是工厂模式的经典实现，Spring将管理对象称为Bean。
- **Core核心模块**：提供了Spring框架的基本组成部分，包括IOC和DI。
- **Context上下文模块**：建立在Core和Beans模块的基础之上，它是访问定义和配置任何对象的媒介。ApplicationContext接口是上下文模块的焦点。
- Expression Language模块：是运行时查询和操作对象图的强大的表达式语言。

#### 其他模块

Spring其他模块还有AOP、Aspects、Instrumentation以及Test模块，具体介绍如下：

- AOP模块：提供了面向切面编程实现，允许定义方法拦截器和切入点，将代码按照功能进行分离，以降低耦合性。
- Aspects模块：提供与AspectJ的集成，是一个功能强大且成熟的面向切面编程（AOP）模块。
- Instrumentation模块：提供了类工具的支持和类加载器的实现，可以在特定的应用服务器中使用。
- Test模块：支持Spring组件，使用JUnit或TestNG框架的测试。

#### DATA Access/Integration（数据访问 / 集成）

数据访问/集成层包括JDBC、ORM、OXM、JMS和Transactions模块，具体介绍如下。

- JDBC模块：提供了一个JDBC的抽象层，大幅度减少了在开发过程中对数据库操作的编码。
- ORM模块：对流行的对象关系映射API，包括JPA、JDO、Hibernate和iBatis提供了继承层。
- OXM模块：提供了一个支持对象/XML映射的抽象层实现，如JAXB、Castor、XMLBeans、JiBX和XStream。
- JMS模块：指Java消息服务，包含的功能为生产和消费的信息。
- Transcations事务模块：支持编程和声明式事务管理实现特殊接口类，并为所有的POJO。

#### Web模块

Spring的Web层包括Web、Servlet、Struts和Portlet组件，具体介绍如下。

- Web模块：提供了基本的Web开发集成特性，使用的Servlet监听器的IoC容器初始化以及Web应用上下文。
- Servlet模块：包括Spring模型—视图—控制器（MVC）实现Web应用程序。
- Struts模块：包含支持类内的Spring应用程序，集成了经典的Struts Web层。
- Portlet模块：提供了在Portlet环境中使用MVC实现，类似Web-Servlet模块的功能。

## 二、Spring IOC是什么？有哪些优点？

#### Java中对象的发展历程

最开始我们创建对象是使用Java内置的机制，也就是new一个对象，这个时候java会找到你需要创建的对象的那个类，然后调用里面的构造器构造一个对象供你使用。当你用完之后就完全不用你操心了，java内置的回收机制会自动的判断并回收摧毁无用的java对象。

这种方式有一个非常严重的弊端，对于大型的企业级应用，需要创建并管理相当多的对象的时候就会是非常痛苦的一件事情。

之后我们使用工厂模式，打个比方：

以前我们需要一个对象的时候，我们要自己找对象（new），非常耗费时间精力。

后来我们找媒婆介绍对象，这样我们就省略了找对象的过程，直接跟媒婆要对象就行了。需要的时候说一次，要一次，这就是工厂模式。这种模式也比较麻烦，因为每次需要对象的时候都要去跟工厂要，那么还有没有更加简便的方法呢？

幸运的是互联网相亲找对象兴起了。我们再也不用自己找对象，也不用去找媒婆了，直接求助于第三方机构。在相亲网站上注册一个账号，声明我是单身狗，要找对象。然后提交一个清单，我需要胖的，矮的，瘦的，高的等等各种对象。之后标注一下在哪里需要对象，比如上海1个，这样就万事大吉了，第三方机构会在规定的时间地点将你需要的对象送给你，你再也不需要操心找对象的事了，可以安安心心的敲代码了。

#### SpringIOC容器是什么？

Spring的IOC容器就是这么一个第三方的对象管理机构，也叫仓库。在Spring中，对象被称作Bean。

通过Spring的相关配置文件，比如xml文件或者配置类提交你的Bean配置信息。Spring收到你的配置文件之后就会存入相关Bean信息到IOC容器，之后在你的项目代码里标注你需要用到对象的地方，IOC容器发现需要Bean的地方就会自动从仓库里创建并发送Bean到需要的地方使用。

**依赖注入||依赖倒置（DI Dependency Inversion Principle）**

https://www.zhihu.com/question/23277575

https://www.iamshuaidi.com/1547.html

#### 为什么要使用IOC？

使用IOC当然是活少，钱多，离家近啦。再也不用在项目代码里写那么多new了，相当于有了智能机器人自动帮你处理创建与管理对象的那些烦心事了。即使是重量级的企业级应用，使用spring之后管理起来也会觉得轻便了许多。

## 三、Spring IOC如何使用

#### 搭建项目

编译器使用Idea：File -> New -> Project -> Maven

GroupId：com.dr

ArtifactId：SpringIOC

#### 导入坐标

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.5.RELEASE</version>
    </dependency>
</dependencies>
```

#### 创建Bean

```java
public class Student {
    private int id;
    private String name;
    private int age;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

#### 创建applicationContext.xml

resources -> New -> XML Configuration File -> Spring Config -> applicationContext.xml

#### 在配置文件中配置

```xml
<bean id="userDao" class="com.dr.SpringIOC.Student"></bean>
```

#### 获得Spring帮我们创建的Student类对象

```java
public class Main {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        Student student = (Student) app.getBean("student");
        student.setId(1);
        student.setName("张三");
        student.setAge(18);
        System.out.println(student);
    }
}
```

### 四、Bean的配置相关

bean标签作用：用于配置对象交由Spring来创建。默认情况下它调用的是类中的无参构造函数。

#### Bean基本配置

- id：Bean实例在Spring容器中的唯一标识，配置文件中不允许重复
- class：Bean的全限定名称

#### Bean范围配置

scope：指定对象的作用范围，取值如下：

- singleton：单例，默认值
- prototype：多例
- request：web项目中，Spring创建一个Bean的对象，将对象存入到request域中
- session：web项目中，Spring创建一个Bean的对象，将对象存入到session域中
- global session：Web项目中，应用在Portlet环境中，如果没有Portlet环境，那么global session相当于session

```xml
<bean id="student1" class="com.dr.SpringIOC.Student" scope="singleton"></bean>
<bean id="student2" class="com.dr.SpringIOC.Student" scope="prototype"></bean>
```

```java
public class DR_Bean范围配置演示 {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        Student stu1 = (Student) app.getBean("student");
        Student stu2 = (Student) app.getBean("student");
        System.out.println(stu1 == stu2);
    }
}
public class DR_Bean范围配置演示 {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        Student stu1 = (Student) app.getBean("student2");
        Student stu2 = (Student) app.getBean("student2");
        System.out.println(stu1 == stu2);
    }
}
// 备注：也可以使用构造函数执行次数来观察
```

总结：

**scope = "singleton"**时：

- Bean的实例化个数：1个

- Bean的实例化时机：当Spring核心文件被加载时，实例化配置的Bean实例

- Bean的生命周期：

- - 对象创建：当应用加载，创建容器时，对象就被创建了
  - 对象运行：只要容器在，对象就一直存活
  - 对象销毁：当应用卸载，销毁容器时，对象就被销毁了

**scope = "prototype"**时：

- Bean的实例化个数：多个

- Bean的实例化时机：当调用getBean() 方法时实例化Bean

- Bean的生命周期：

- - 对象创建：当使用对象时，创建新的对象实例
  - 对象运行：只要对象在使用中，就一直活着
  - 对象销毁：当对象长时间不用时，被垃圾回收器回收了

#### Bean生命周期配置

- init-method：指定类中的初始化方法名称
- destory-method：指定类中销毁方法名称

```xml
<bean id="student3" class="com.dr.SpringIOC.Student" init-method="initMethod" destroy-method="destoryMethod"></bean>
```

```java
public class DR_Bean生命周期配置演示 {
    public static void main(String[] args) {
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        ((ClassPathXmlApplicationContext) app).close();
    }
}
```

#### Bean实例化三种方式

- 无参构造方法实例化
- 工厂静态方法实例化

```xml
<bean id="student4" class="com.dr.SpringIOC.StaticFactory" factory-method="getStudent"></bean>
```

```java
public class StaticFactory {
    public static Student getStudent() {
        return new Student();
    }
}
```

- 工厂实例方法实例化

必须有工厂对象，再调用工厂对象的方法。

```xml
<bean id="factory" class="com.dr.SpringIOC.StaticFactory"></bean>
<bean id="student5" factory-bean="factory" factory-method="getStudent"></bean>
```

```java
public class StaticFactory {
    public Student getStudent() {
        return new Student();
    }
}
```

#### Bean的依赖注入分析

依赖注入（Dependency Injection）：它是Spring框架核心IoC的具体实现。

在编写程序时，通过控制反转，把对象的创建交给了Spring，但代码中不可能出现没有依赖的情况。

IoC解耦只是降低他们的依赖关系，但不会消除。

那这种业务层和持久层的依赖关系，在使用Spring之后，就让Spring来维护了。简单的说，就是坐等框架把持久层对象传入业务层，而不用我们自己去获取。

##### Bean 的依赖注入方式

- 构造方法

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

- set方法注入：P命名空间注入本质上也是set方法注入

```xml
引入P命名空间
xmlns:p="http://www.springframework.org/schema/p"
修改注入方式
<bean id="userService" class="xxx.xxx" p:userDao-ref="userDao"></bean>
```

#### Bean 的依赖注入的数据类型

上面的操作，都是注入的引用Bean，除了对象的引用可以注入，普通数据类型，集合等等都可以再容器中进行注入。

- 普通数据类型

```xml
<bean id="student5" class="com.dr.SpringIOC.Student">
	<property name="id" value="1"></property>
    <property name="name" value="zhangsan"></property>
    <property name="age" value="18"></property>
</bean>
```

- 引用数据类型

```xml
<bean id="student6" class="com.dr.SpringIOC.Student">
	<property name="grade" ref="grade"></property>
</bean>
```

- 集合数据类型

举例：List、Map、Properties、基本数据类型

```xml
<bean id="student1" class="com.dr.SpringIOC.Student">
	<property name="id" value="1"></property>
    <property name="name" value="zhangsan"></property>
    <property name="age" value="18"></property>
</bean>
<bean id="student7" class="com.dr.SpringIOC.Student">
    <property name="list">
        <list>
            <value>a</value>
            <value>b</value>
            <value>c</value>
            <!-- 如果是引用类型 -->
            <ref></ref>
        </list>
    </property>
    <property name="map">
        <map>
        	<entry key="1" value-ref="student1"></entry>
        </map>
    </property>
    <property name="properties">
        <props>
        	<prop key="username">zhangsan</entry>
        	<prop key="password">123456</entry>
        </props>
    </property>
</bean>
```

## 五、基于注解的配置

略

## 六、动手实践

1. 通过IOC容器创建对象，并为属性赋值

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="person01" class="per.wzy.spring.Person">
        <property name="age" value="18"></property>
        <property name="name" value="张三"></property>
        <property name="sex" value="男"></property>
    </bean>
</beans>
```

2. 通过Bean的类型从IOC中获取bean的实例

```java
//如果IOC容器中这个类型的bean有多个，则查找会报错
Person person = context.getBean(Person.class);
//存在多个同类型bean,根据id查找
Person person = context.getBean("person02",Person.class);
```

3. 使用构造器在IOC中创建bean

```xml
<bean id="person03" class="per.wzy.spring.Person">
    <constructor-arg name="name" value="大花"></constructor-arg>
    <constructor-arg name="age" value="19"></constructor-arg>
    <constructor-arg name="sex" value="女"></constructor-arg>
</bean>
```

4. 通过p名称空间为bean赋值

```xml
引入P命名空间
xmlns:p="http://www.springframework.org/schema/p"
修改注入方式
<bean id="userService" class="xxx.xxx" p:userDao-ref="userDao"></bean>
```

5. 通过IOC为对象注入基本数据类型、List、Map等数据集









