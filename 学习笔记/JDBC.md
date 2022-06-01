# JDBC

## JDBC简介

JDBC（Java DataBase Connectivity Java数据库连接）就是使用Java语言操作关系型数据库的一套API。

JDBC本质：

- 官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口
- 各个数据库厂商去实现这套接口，提供数据库驱动的jar包
- 我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中实现类

JDBC提供了一套标准接口（仅仅），使得同一套Java代码，操作不同的关系型数据库。MySQL、Oracle、DB2

JDBC好处：

- 各数据库厂商使用相同的接口，Java代码不需要针对不同数据库分别开发
- 可随时替换底层数据库，访问数据库的Java代码基本不变

## JDBC快速入门

首先导入驱动jar包。

```java
// 1. 注册驱动
// 当使用一个类的时候（比如new一个类的实例），JVM会检查此类是否被加载到内存。
// 如果没有，则会执行加载操作，加载操作的内容是，读取类对应的class文件数据，解析此数据，
// 构造一个此类对应的Class类的实例，此Class类的实例描述了类的结构，并且提供了调用此类成员的接口。
// 此时JVM就可以使用该类了，比如实例化此类，或者调用此类的静态方法。
// Java也提供了手动加载类的接口，Class.forName()方法就是其中之一。
Class.forName("com.mysql.jdbc.Driver");
// 2. 获取连接对象
String url = "jdbc:mysql://127.0.0.1:3306/db?useSSL-false";
Connection conn = DriverManager.getConnection(url, username, password);
// 3. 定义SQL
String sql = "";
// 4. 获取执行SQL的对象
Statement stmt = conn.createStatement();
// 5. 执行SQL
stmt.executeUpdate(sql); // 增删改查
// 6. 处理结果
// 7. 释放资源
stmt.clase();
conn.close();
```

## JDBC API详解

### DriverManager（驱动管理类）

作用：

1. 注册驱动
2. 获取数据库连接

**注册驱动详解：**

```java
public class Driver extends NonRegisteringDriver implements java.sql.Driver {
    public Driver() throws SQLException {
    }
	
    // 静态代码块
    static {
        try {
            // 调用DDriverManager.registerDriver将驱动注册到JDBC中
            DriverManager.registerDriver(new Driver());
        } catch (SQLException var1) {
            throw new RuntimeException("Can't register driver!");
        }
    }
}
```

```java
// 在MySQL5以后这行代码可以省略
// JDBC会自动加载jar包中META-INF/services/java.sql.Driver文件中的驱动类
Class.forName("com.mysql.jdbc.Driver");
```

**获取数据库连接：**

细节：

- 如果连接的是本机MySQL服务器，并且MySQL服务器默认端口是3306，则URL可以简写为：jdbc:mysql///dbname?k1=v1&k2=v2
- 配置useSSL=false，禁用安全连接方式，解决警告信息

### Connection（数据库连接对象）

作用：

1. 获取执行SQL的对象
2. 管理事务

**获取执行SQL对象：**

- 普通执行SQL对象

```java
Statement createStatement();
```

- 预编译SQL的执行SQL对象：防止SQL注入

```java
PreparedStatement prepareStatement(sql);
```

- 执行存储过程的对象

```java
CallableStatement prepareCall(sql);
```

**事务管理：**

- MySQL事务管理

```tex
开启事务：BEGIN; / START TRANSACTION;
提交事务：COMMIT
回滚事务：ROLLBACK

MySQL默认自动提交事务
```

- JDBC事务管理：Connection接口中定义了3个对应的方法

```tex
开启事务：setAutoCommit(boolean autoCommit); true为自动提交事务；false为手动提交事务；
提交事务：commit()
回滚事务：rollback()
```

```java
try {
    // 开启事务
	// 提交事务
} catch() {
    // 回滚事务
} finally {
    // 关闭资源
}

```

### Statement

作用：

1. 执行SQL语句

**执行SQL语句：**

```java
int executeUpdate(sql)：执行DML、DDL语句
// 返回值：(1)DML语句影响的行数 (2)DDL语句执行后，执行成功也可能返回0
ResultSet executeQuery(sql)：执行DQL语句
// 返回值：Result结果集对象
```

### ResultSet（结果集对象）

作用：

1. 封装了DQL查询语句的结果

**获取查询结果：**

```java
boolean next();
// (1) 将光标从当前位置向前移动一行
// (2) 判断当前行是否为有效行
// 返回值：true：有效行，当前行有数据；false：无效行，当前行没有数据；
xxx getXxx(参数); // 获取数据
// xxx：数据类型；如int getInt(参数); String getString(参数);
// 参数：int：列的编号，从1开始；String：列的名称；
```

**使用步骤：**

1. 游标向下移动一行，并判断该行是否有数据：next()
2. 获取数据：getXxx(参数)

```java
// 循环判断游标是否是最后一行末尾
while(rs.next()) {
    // 获取数据
    rs.getXxx(参数);
}
```

### PreparedStatement

作用：

1. 预编译SQL语句并执行：预防SQL注入问题

**SQL注入：**

SQL注入式通过操作输入来修改事先定义好的SQL语句，用以达到执行代码对服务器进行攻击的方法。

**具体过程：**

1. 获取PreparedStatement对象

```java
sql = "select * from table where id = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
```

2. 设置参数值

```java
PreparedStatement对象：setXxx(参数1， 参数2); // 给?赋值
Xxx：数据类型
参数：参数1：?的位置编号，从1开始；参数2：?的值
```

3. 执行SQL

```java
pstmt.executeUpdate(); 或者 pstmt.executeQuery(); // 不需要再传递sql
```

**PreparedStatement原理：**

PreparedStatement好处：

1. 预编译SQL：性能更高
2. 防止SQL注入：将敏感字符进行转义

PreparedStatement预编译功能开启：useServerPrepStmts=true

配置MySQL执行日志（重启MySQL服务生效）

PreparedStatement原理：

1. 在获取PreparedStatement对象时，将Sql语句发送给MySQL服务器进行检查，编译
2. 执行时就不用在进行这些步骤了，速度更快
3. 如果sql模板一样，则只需要进行一次检查、编译

## 数据库连接池

**数据库连接池简介：**

- 数据库连接池是个容器，负责分配、管理数据库连接（Connection）
- 它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个；
- 释放空闲时间超过最大空闲时间的数据库连接来避免因为没有释放数据库连接而引起的数据库连接遗漏

- 好处：
  - 资源重用
  - 提升系统响应速度
  - 避免数据库连接遗漏

**数据库连接池实现：**

- 标准接口：DataSource

  - 官方（SUN）提供的数据库连接池标准接口，由第三方组织实现此接口
  - 功能：获取连接

  ```java
  Connection getConnection()
  ```

- 常见的数据库连接池：
  - DBCP
  - C3P0
  - Druid
- Druid（德鲁伊）
  - Druid连接池是阿里巴巴开源的数据库连接池项目
  - 功能强大，性能优秀，是Java语言最好的数据库连接池之一

**Druid数据库连接池：**

使用步骤：

1. 导入jar包 druid-1.1.12.jar
2. 定义配置文件
3. 加载配置文件
4. 获取数据库连接池对象
5. 获取连接

```java
Properties prop = new Properties("");
DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);
Connection connection = dataSource.getConnection();
```





















