### Mybatis框架简介

- Mybatis框架是一个开源的数据持久层框架。
- 它内部封装了通过JDBC访问数据库的操作，支持普通的SQL查询、存储过程和高级映射，几乎消除了所有的JDBC代码和参数的手工设置以及结果集的检索。
- Mybatis作为持久层框架，其主要思想是将程序中的大量SQL语句剥离出来，配置在配置文件当中，实现SQL的灵活配置。
- 这样做的好处是将SQL与程序代码分离，可以在不修改代码的情况下，直接在配置文件当中修改SQL。

### 谈谈你对Mybatis的理解

1. Mybatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力去处理加载驱动、创建连接、创建Statement等繁杂的的过程。程序员直接编写原生态SQL，可以严格控制SQL执行性能，灵活度高。
2. Mybatis可以使用XML或注解来配置和映射原生信息，将POJO映射成数据库中的记录，避免了几乎所有的JDBC代码和手动设置参数以及获取结果集。
3. 通过XML文件或注解的方式将要执行的各种Statement配置起来，并通过Java对象和Statement中SQL的动态参数进行映射生成最终执行的SQL语句，最后由Mybatis框架执行SQL并将结果映射为Java对象并返回。

### Mybatis的优缺点有哪些？

**优点：**

1. 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除SQL与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用；
2. 与JDBC相比，减少了代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；
3. 很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）；
4. 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。

**缺点：**

1. SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求；
2. SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

### Mybatis与Hibernate有哪些不同？

1. MyBatis和Hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写SQL语句；Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用Hibernate开发可以节省很多代码，提高效率；
2. MyBatis直接编写原生态SQL，可以严格控制SQL执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是MyBatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套SQL映射文件，工作量大。

### Mybatis中#{}和${}的区别是什么？

#{}：是预编译处理；

${}：是字符串替换。

Mybatis在处理#{}时，会将SQL中的#{}替换为?，调用PrepareStatement的set方法来赋值；使用#{}可以有效防止SQL注入，提高系统安全性。在处理${}时，就是单纯的把${}替换成变量的值。

### Mybatis是如何进行分页？分页插件的原理是什么？

Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在SQL内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的SQL，然后重写SQL，根据dialect方言，添加对应的物理分页语句和物理分页参数。

### Mybatis有几种分页方式？

1. 数组分页：借助数组进行分页（逻辑分页）
2. SQL分页：借助SQL语句进行分页（物理分页）
3. 拦截器分页：通过拦截器给SQL语句末尾加上limit语句来查询，最优。
4. RowBounds分页：PageHelper是Mybatis的通用分页插件，通过Mybatis的拦截器实现分页功能，拦截SQL查询请求，添加分页语句，最终实现分页查询功能。

### Mybatis逻辑分页和物理分页的区别？

**物理分页：**

物理分页就是数据库本身提供了分页方式，如MySQL的Limit，Oracle的rownum，好处是效率高，不好得方式不同数据库有不同的写法。

**逻辑分页：**

逻辑分页利用游标分页，好处是所有数据库都统一，坏处是效率低。

逻辑分页是一次性查询很多数据，然后再在结果中检索分页的数据，这样做的弊端是需要消耗大量的内存，有内存溢出的风险，对数据库压力较大。物理分页是从数据库查询指定条数的数据，弥补了一次性全部查出所有的数据的种种缺点。

### Mybatis是否支持**延迟加载**？如果支持，它的实现原理是什么？⭐

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载。

lazyLoadingEnabled=true|false

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联a.getB().getName()方法的调用。这就是延迟加载的基本原理。

### 说一下Mybatis的一级缓存和二级缓存？

一级缓存：基于Perpetual的HashMap本地缓存，其存储作用域为Session，当Session flush或close之后，该Session中的所有Cache就将清空，默认打开一级缓存。

二级缓存：与一级缓存其机制相同，默认也是采用PerpetualCache，HashMap存储，不同在于其存储作用域为Mapper（NameSpace），并且可自定义存储源，如Ehache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口，可在它的配置文件中配置。
对于缓存数据更新机制，当某一个作用域（一级缓存Session/二级缓存Namespaces）进行了C/U/D操作后，默认该作用域下所有select中的缓存将被clear。

### Mybatis有哪些执行器（Executor）？

Mybatis有3中基本的执行器（Executor）：

1. SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。
2. ReuseExecutor：执行update或select，以SQL作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map内，供下一次使用。简言之，就是重复使用Statement对象。
3. BarchExecutor：执行update（没有select，JDBC批处理不支持select），将所有SQL都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理，与JDBC批处理相同。

### Mybatis动态SQL是做什么的？都有哪些动态SQL？能简述一下动态SQL的执行原理？

1. Mybatis动态SQL可以让我们在XML映射文件内，以标签的形式编写动态SQL，完成逻辑判断和动态拼接SQL的功能。
2. Mybatis提供了9种动态SQL标签：trim、where、set、foreach、if、when、otherwise、bind。
3. 执行原理：使用OGNL从SQL参数对象中计算表达式的值，根据表达式的值动态拼接SQL，以此来完成动态SQL的功能。



