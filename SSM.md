# MyBatis

## 基本介绍

ORM（Object Relational Mapping）： 对象关系映射，指的是持久化数据和实体对象的映射模式，解决面向对象与关系型数据库存在的互不匹配的现象

![](https://gitee.com/seazean/images/raw/master/Frame/ORM介绍.png)

**MyBatis**：

* MyBatis 是一个优秀的基于 java 的持久层框架，它内部封装了 JDBC，使开发者只需关注 SQL 语句本身，而不需要花费精力去处理加载驱动、创建连接、创建 Statement 等过程。

* MyBatis通过 xml 或注解的方式将要执行的各种 Statement 配置起来，并通过 Java 对象和 Statement 中 SQL 的动态参数进行映射生成最终执行的 sql 语句。

* MyBatis 框架执行 SQL 并将结果映射为 Java 对象并返回。采用 ORM 思想解决了实体和数据库映射的问题，对 JDBC 进行了封装，屏蔽了 JDBC 底层 API 的调用细节，使我们不用操作 JDBC API，就可以完成对数据库的持久化操作。

MyBatis官网地址：http://www.mybatis.org/mybatis-3/



***





## 基本操作

### 相关API

#### Resources

org.apache.ibatis.io.Resources：加载资源的工具类

`InputStream getResourceAsStream(String fileName)`：通过类加载器返回指定资源的字节流

* 参数 fileName 是放在 src 的核心配置文件名：MyBatisConfig.xml



#### SqlSessionFactoryBuilder

org.apache.ibatis.session.SqlSessionFactoryBuilder：构建器，用来获取 SqlSessionFactory 工厂对象

`SqlSessionFactory build(InputStream is)`：通过指定资源的字节输入流获取 SqlSession 工厂对象



#### SqlSessionFactory

org.apache.ibatis.session.SqlSessionFactory：获取 SqlSession 构建者对象的工厂接口

`SqlSession openSession()`：获取 SqlSession 构建者对象，并开启手动提交事务

`SqlSession openSession(boolean)`：获取 SqlSession 构建者对象，参数为 true 开启自动提交事务



#### SqlSession

org.apache.ibatis.session.SqlSession：构建者对象接口，用于执行 SQL、管理事务、接口代理

* SqlSession 代表和数据库的一次会话，用完必须关闭
* SqlSession 和 connection 一样都是非线程安全，每次使用都应该去获取新的对象

注：**update 数据需要提交事务，或开启默认提交**

| 方法                                                  | 说明                           |
| ----------------------------------------------------- | ------------------------------ |
| List<E> selectList(String statement,Object parameter) | 执行查询语句，返回List集合     |
| T selectOne(String statement,Object parameter)        | 执行查询语句，返回一个结果对象 |
| int insert(String statement,Object parameter)         | 执行新增语句，返回影响行数     |
| int update(String statement,Object parameter)         | 执行删除语句，返回影响行数     |
| int delete(String statement,Object parameter)         | 执行修改语句，返回影响行数     |
| void commit()                                         | 提交事务                       |
| void rollback()                                       | 回滚事务                       |
| T getMapper(Class<T> cls)                             | 获取指定接口的代理实现类对象   |
| void close()                                          | 释放资源                       |



***



### 映射配置

映射配置文件包含了数据和对象之间的映射关系以及要执行的 SQL 语句，放在src目录下，

命名：StudentMapper.xml

* 映射配置文件的文件头：

  ```xml-dtd
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  ```

* 根标签：
  * <mapper>：核心根标签
  * namespace：属性，名称空间

* 功能标签：

  * < select >：查询功能标签
  * <insert>：新增功能标签
  * <update>：修改功能标签
  * <delete>：删除功能标签
    * id：属性，唯一标识，配合名称空间使用
    * resultType：指定结果映射对象类型，和对应的方法的返回值类型(全限定名)保持一致，但是如果返回值是List则和其泛型保持一致
    * parameterType：指定参数映射对象类型，必须和对应的方法的参数类型(全限定名)保持一致
    * statementType：可选 STATEMENT，PREPARED 或 CALLABLE，默认值：PREPARED
      * STATEMENT：直接操作 sql，不进行预编译，获取数据：$ Statement
      * PREPARED：预处理参数，进行预编译，获取数据：# PreparedStatement
      * CALLABLE：执行存储过程，CallableStatement

* 参数获取方式：
  * SQL 获取参数：#{属性名}

    ```xml-dtd
    <mapper namespace="StudentMapper">
        <select id="selectById" resultType="student" parameterType="int">
    		SELECT * FROM student WHERE id = #{id}
        </select>
    <mapper/>
    ```



强烈推荐官方文档：https://mybatis.org/mybatis-3/zh/sqlmap-xml.html



***



### 核心配置

核心配置文件包含了 MyBatis 最核心的设置和属性信息，如数据库的连接、事务、连接池信息等。放在src目录下

命名：MyBatisConfig.xml

* 核心配置文件的文件头：

  ```xml-dtd
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
  ```

* 根标签：

  * <configuration>：核心根标签

* 引入连接配置文件：

  * <properties>： 引入数据库连接配置文件标签

    * resource：属性，指定配置文件名

    ```xml-dtd
    <properties resource="jdbc.properties"/>
    ```

* 调整设置

  * <settings>：可以改变 Mybatis 运行时行为

* 起别名：

  * <typeAliases>：为全类名起别名的父标签。

    * <typeAlias>：为全类名起别名的子标签
      * type：指定全类名      
      * alias：指定别名
      
    * <package>：为指定包下所有类起别名的子标签，别名就是类名，首字母小写

    ```xml-dtd
    <!--起别名-->
    <typeAliases>
    	<typeAlias type="bean.Student" alias="student"/>
    	<package name="com.seazean.bean"/>
    		<!--二选一-->
    </typeAliase>
    ```
  
  * 自带别名：
  
    | 别名    | 数据类型          |
    | ------- | ----------------- |
    | string  | java.lang.String  |
    | long    | java.lang.Lang    |
    | int     | java.lang.Integer |
    | double  | java.lang.Double  |
    | boolean | java.lang.Boolean |
    | ....    | ......            |



* 配置环境，可以配置多个标签
  * <environments>：配置数据库环境标签。default属性：指定哪个environment
  * <environment>：配置数据库环境子标签。id属性：唯一标识，与default对应
  * <transactionManager>：事务管理标签。type属性：默认JDBC事务
  * <dataSoure>：数据源标签。
    * type属性：POOLED使用连接池(mybatis内置); UNPOOLED不使用连接池
  * <property>：数据库连接信息标签。
    * name属性取值：driver，url，username，password
    * value属性取值：与name对应
* 引入映射配置文件

  * <mappers>：引入映射配置文件标签
  * <mapper>：引入映射配置文件子标签
    * resource：属性指定映射配置文件的名称
    * url：引用网路路径或者磁盘路径下的 sql 映射文件
  * <package>：批量注册



参考官方文档：https://mybatis.org/mybatis-3/zh/configuration.html



***



### #{}和${}

**#{}：**占位符，传入的内容会作为字符串，加上引号，以**预编译**的方式传入，将 sql 中的 #{} 替换为 ? 号，调用 PreparedStatement 的 set方法来赋值，有效的防止 SQL 注入，提高系统安全性

**${}：**拼接符，传入的内容会直接替换拼接，不会加上引号，可能存在 sql 注入的安全隐患

* 能用 #{} 的地方就用 #{}，不用或少用 ${}，
* 必须使用 ${} 的情况：
  * 表名作参数时，如：`SELECT * FROM ${tableName}`
  * order by 时，如：`SELECT * FROM t_user ORDER BY ${columnName}`

* sql 语句使用 #{}，properties 文件内容获取使用 ${} 



****



### 日志文件

在日常开发过程中，排查问题时需要输出 MyBatis 真正执行的 SQL 语句、参数、结果等信息，就可以借助 log4j 的功能来实现执行信息的输出。

* 在核心配置文件根标签内配置 log4j

  ```xml
  <!--配置LOG4J-->
  <settings>
  	<setting name="logImpl" value="log4j"/>
  </settings>
  ```

* 在 src 目录下创建 log4j.properties

  ```properties
  # Global logging configuration
  log4j.rootLogger=DEBUG, stdout
  # Console output...
  log4j.appender.stdout=org.apache.log4j.ConsoleAppender
  log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
  log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
  
  #输出到日志文件
    #log4j.appender.file=org.apache.log4j.FileAppender
    #log4j.appender.file.File=../logs/iask.log
    #log4j.appender.file.layout=org.apache.log4j.PatternLayout
    #log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss}  %l  %m%n
  ```

* pom.xml

  ```xml
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.21</version>
  </dependency>
  <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.21</version>
  </dependency>
  ```

  


***



### 代码实现

* 实体类

  ```java
  public class Student {
      private Integer id;
      private String name;
      private Integer age;
      .....
  }
  ```

* StudentMapper

  ```java
  public interface StudentMapper {
      //查询全部
      public abstract List<Student> selectAll();
  
      //根据id查询
      public abstract Student selectById(Integer id);
  
      //新增数据
      public abstract Integer insert(Student stu);
  
      //修改数据
      public abstract Integer update(Student stu);
  
      //删除数据
      public abstract Integer delete(Integer id);
  }
  ```

* config.properties

  ```properties
  driver=com.mysql.jdbc.Driver
  url=jdbc:mysql://192.168.2.184:3306/db11
  username=root
  password=123456
  ```

* MyBatisConfig.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
  
  <!--核心根标签-->
  <configuration>
      <!--引入数据库连接的配置文件-->
      <properties resource="jdbc.properties"/>
      
      <!--配置LOG4J-->
      <settings>
          <setting name="logImpl" value="log4j"/>
      </settings>
      
      <!--起别名-->
      <typeAliases>
          <typeAlias type="bean.Student" alias="student"/>
          <!--<package name="bean"/>-->
      </typeAliases>
  
      <!--配置数据库环境，可以多个环境，default指定哪个-->
      <environments default="mysql">
          <!--id属性唯一标识-->
          <environment id="mysql">
              <!--事务管理，type属性，默认JDBC事务-->
              <transactionManager type="JDBC"></transactionManager>
              <!--数据源信息   type属性连接池-->
              <dataSource type="POOLED">
                  <!--property获取数据库连接的配置信息-->
                  <property name="driver" value="${driver}"/>
                  <property name="url" value="${url}"/>
                  <property name="username" value="${username}"/>
                  <property name="password" value="${password}"/>
              </dataSource>
          </environment>
      </environments>
  
      <!--引入映射配置文件-->
      <mappers>
          <!--mapper引入指定的映射配置 resource属性执行的映射配置文件的名称-->
          <mapper resource="StudentMapper.xml"/>
      </mappers>
  </configuration>
  ```

* StudentMapper.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <mapper namespace="StudentMapper">
      <select id="selectAll" resultType="student">
          SELECT * FROM student
      </select>
  
      <select id="selectById" resultType="student" parameterType="int">
          SELECT * FROM student WHERE id = #{id}
      </select>
  
      <insert id="insert" parameterType="student">
          INSERT INTO student VALUES (#{id},#{name},#{age})
      </insert>
  
      <update id="update" parameterType="student">
          UPDATE student SET name = #{name}, age = #{age} WHERE id = #{id}
      </update>
  
      <delete id="delete" parameterType="student">
          DELETE FROM student WHERE id = #{id}
      </delete>
  
  </mapper>
  ```

* 控制层测试代码：根据id查询

  ```java
  @Test
  public void selectById() throws Exception{
      //1.加载核心配置文件
      InputStream is = Resources.getResourceAsStream("MyBatisConfig.xml");
  
      //2.获取SqlSession工厂对象
      SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(is);
  
      //3.通过工厂对象获取SqlSession对象
      SqlSession sqlSession = ssf.openSession();
  
      //4.执行映射配置文件中的sql语句，并接收结果
      Student stu = sqlSession.selectOne("StudentMapper.selectById", 3);
  
      //5.处理结果
      System.out.println(stu);
  
      //6.释放资源
      sqlSession.close();
      is.close();
  }
  ```

* 控制层测试代码：新增功能

  ```java
  @Test
  public void insert() throws Exception{
      //1.加载核心配置文件
      //2.获取SqlSession工厂对象
      //3.通过工厂对象获取SqlSession对象
      SqlSession sqlSession = sqlSessionFactory.openSession(true);
  
      //4.执行映射配置文件中的sql语句，并接收结果
      Student stu = new Student(5,"周七",27);
      int result = sqlSession.insert("StudentMapper.insert", stu);
  
      //5.提交事务
      //sqlSession.commit();
  
      //6.处理结果
      System.out.println(result);
  
      //7.释放资源
      sqlSession.close();
      is.close();
  }
  ```




****



### 批量操作

两种方式实现批量操作：

* <settings> 标签属性：这种方式属于全局批量

  ```xml
  <settings>
      <setting name="defaultExecutorType" value="BATCH"/>
  </settings>
  ```

  defaultExecutorType：配置默认的执行器

  * SIMPLE 就是普通的执行器
  * REUSE 执行器会重用预处理语句（PreparedStatement）
  * BATCH 执行器不仅重用语句还会执行批量更新

* SqlSession 会话内批量操作：

  ```java
  public void testBatch() throws IOException{
      SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
  
      //可以执行批量操作的sqlSession
      SqlSession openSession = sqlSessionFactory.openSession(ExecutorType.BATCH);
      long start = System.currentTimeMillis();
      try{
          EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
          for (int i = 0; i < 10000; i++) {
              mapper.addEmp(new Employee(UUID.randomUUID().toString().substring(0, 5), "b", "1"));
          }
          openSession.commit();
          long end = System.currentTimeMillis();
          //批量：（预编译sql一次==>设置参数===>10000次===>执行1次（类似管道））
          //非批量：（预编译sql=设置参数=执行）==》10000   耗时更多
          System.out.println("执行时长："+(end-start));
      }finally{
          openSession.close();
      }
  }
  ```

* Spring 配置文件方式（applicationContext.xml）：

  ```xml
  <!--配置一个可以进行批量执行的sqlSession  -->
  <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
      <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactoryBean"/>
      <constructor-arg name="executorType" value="BATCH"/>
  </bean>
  ```

  ```java
  @Autowired
  private SqlSession sqlSession;
  ```

  



***



## 代理开发

### 代理规则

分层思想：控制层（controller）、业务层（service）、持久层（dao）

调用流程：

![](https://gitee.com/seazean/images/raw/master/Frame/分层思想调用流程.png)

传统方式实现 DAO 层，需要写接口和实现类。采用 Mybatis 的代理开发方式实现 DAO 层的开发，只需要编写Mapper 接口（相当于 Dao  接口），由 Mybatis 框架根据接口定义创建接口的动态代理对象。

接口开发方式：

1. 定义接口
2. 操作数据库，mybatis框架根据接口，通过动态代理的方式生成代理对象，负责数据库的crud操作

Mapper 接口开发需要遵循以下规范：

* Mapper.xml 文件中的 namespace 与 DAO 层 mapper 接口的全类名相同

* Mapper.xml 文件中的增删改查标签的id属性和 DAO 层 Mapper 接口方法名相同

* Mapper.xml 文件中的增删改查标签的 parameterType 属性和 DAO 层 Mapper 接口方法的参数相同

* Mapper.xml 文件中的增删改查标签的 resultType 属性和 DAO 层 Mapper 接口方法的返回值相同

  ![](https://gitee.com/seazean/images/raw/master/Frame/接口代理方式实现DAO层.png)
  
  

***



### 实现原理

通过动态代理开发模式，我们只编写一个接口，不写实现类，通过 **getMapper()** 方法最终获取到 org.apache.ibatis.binding.MapperProxy 代理对象，而这个代理对象是 MyBatis 使用了 JDK 的动态代理技术

动态代理实现类对象在执行方法时最终调用了 **MapperMethod.execute()** 方法，这个方法中通过 switch case 语句根据操作类型来判断是新增、修改、删除、查询操作，最后一步回到了 MyBatis 最原生的 **SqlSession 方式来执行增删改查**。

* 代码实现：

  ```java
  public Student selectById(Integer id) {
      Student stu = null;
      SqlSession sqlSession = null;
      InputStream is = null;
      try{
          //1.加载核心配置文件
          is = Resources.getResourceAsStream("MyBatisConfig.xml");
  
          //2.获取SqlSession工厂对象
          SqlSessionFactory s = new SqlSessionFactoryBuilder().build(is);
  
          //3.通过工厂对象获取SqlSession对象
          sqlSession = s.openSession(true);
  
          //4.获取StudentMapper接口的实现类对象
          StudentMapper mapper = sqlSession.getMapper(StudentMapper.class); 
  
          //5.通过实现类对象调用方法，接收结果
          stu = mapper.selectById(id);
      } catch (Exception e) {
          e.getMessage();
      } finally {
          //6.释放资源
          if(sqlSession != null) {
              sqlSession.close();
          }
          if(is != null) {
              try {
                  is.close();
              } catch (IOException e) {
                  e.printStackTrace();
              }
          }
      }
      //7.返回结果
      return stu;
  }
  ```

  



***





## 结果映射

### 相关标签

<resultType>：返回的是一个集合，要写集合中元素的类型

<resultMap>：返回一条记录的Map，key 是列名，value 是对应的值，用来配置字段和对象属性的映射关系标签，结果映射（和 resultType 二选一）

* id 属性：唯一标识
* type 属性：实体对象类型

<resultMap>内的核心配置文件标签：

* <id>：配置主键映射关系标签。
* <result>：配置非主键映射关系标签
  * column 属性：表中字段名称
  * property 属性： 实体对象变量名称

* <association>：配置被包含对象的映射关系标签，嵌套封装结果集（多对一、一对一）
  *  property 属性：被包含对象的变量名，要进行映射的属性名（Java 中的 Bean 类）
  *  javaType 属性：被包含对象的数据类型，要进行映射的属性的类型

* <collection>：配置被包含集合对象的映射关系标签，嵌套封装结果集（一对多、多对多）
  * property 属性：被包含集合对象的变量名
  * ofType 属性：集合中保存的对象数据类型 

* <discriminator>：鉴别器，用来判断某列的值，根据得到某列的不同值做出不同自定义的封装行为

自定义封装规则可以将数据库中比较复杂的数据类型映射为 javaBean 中的属性



****



### 一对一

一对一实现：

* 数据准备

  ```mysql
  CREATE TABLE person(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	name VARCHAR(20),
  	age INT
  );
  INSERT INTO person VALUES (NULL,'张三',23),(NULL,'李四',24),(NULL,'王五',25);
  
  CREATE TABLE card(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	number VARCHAR(30),
  	pid INT,
  	CONSTRAINT cp_fk FOREIGN KEY (pid) REFERENCES person(id)
  );
  INSERT INTO card VALUES (NULL,'12345',1),(NULL,'23456',2),(NULL,'34567',3);
  ```

* bean 类

  ```java
  public class Card {
      private Integer id;     //主键id
      private String number;  //身份证号
      private Person p;       //所属人的对象
      ......
  }
  
  public class Person {
      private Integer id;     //主键id
      private String name;    //人的姓名
      private Integer age;    //人的年龄
  }
  ```

* 配置文件 OneToOneMapper.xml，MyBatisConfig.xml 需要引入（可以把 bean 包下起别名）

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <mapper namespace="OneToOneMapper">
  
      <!--配置字段和实体对象属性的映射关系-->
      <resultMap id="oneToOne" type="card">
         	<!--column 表中字段名称，property 实体对象变量名称-->
          <id column="cid" property="id" />
          <!--column 表中字段名称，property 实体对象变量名称-->
          <result column="number" property="number" />
          <!--
              association：配置被包含对象的映射关系
              property：被包含对象的变量名
              javaType：被包含对象的数据类型
          -->
          <association property="p" javaType="bean.Person">
              <id column="pid" property="id" />
              <result column="name" property="name" />
              <result column="age" property="age" />
          </association>
      </resultMap>
  
      <select id="selectAll" resultMap="oneToOne"> <!--SQL-->
          SELECT c.id cid,number,pid,NAME,age FROM card c,person p WHERE c.pid=p.id
      </select>
  </mapper>
  ```

* 核心配置文件 MyBatisConfig.xml

  ```xml
  <!-- mappers引入映射配置文件 -->
  <mappers>
      <mapper resource="one_to_one/OneToOneMapper.xml"/>
      <mapper resource="one_to_many/OneToManyMapper.xml"/>
      <mapper resource="many_to_many/ManyToManyMapper.xml"/>
  </mappers>
  ```

* 测试类

  ```java
  public class Test01 {
      @Test
      public void selectAll() throws Exception{
          //1.加载核心配置文件
          InputStream is = Resources.getResourceAsStream("MyBatisConfig.xml");
  
          //2.获取SqlSession工厂对象
          SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(is);
  
          //3.通过工厂对象获取SqlSession对象
          SqlSession sqlSession = ssf.openSession(true);
  
          //4.获取OneToOneMapper接口的实现类对象
          OneToOneMapper mapper = sqlSession.getMapper(OneToOneMapper.class);
  
          //5.调用实现类的方法，接收结果
          List<Card> list = mapper.selectAll();
  
          //6.处理结果
          for (Card c : list) {
              System.out.println(c);
          }
  
          //7.释放资源
          sqlSession.close();
          is.close();
      }
  }
  ```

  

  

***



### 一对多

一对多实现：

* 数据准备

  ```mysql
  CREATE TABLE classes(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	name VARCHAR(20)
  );
  INSERT INTO classes VALUES (NULL,'程序一班'),(NULL,'程序二班')
  
  CREATE TABLE student(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	name VARCHAR(30),
  	age INT,
  	cid INT,
  	CONSTRAINT cs_fk FOREIGN KEY (cid) REFERENCES classes(id)
  );
  INSERT INTO student VALUES (NULL,'张三',23,1),(NULL,'李四',24,1),(NULL,'王五',25,2);
  ```

* bean 类

  ```java
  public class Classes {
      private Integer id;     //主键id
      private String name;    //班级名称
      private List<Student> students; //班级中所有学生对象
      ........
  }
  public class Student {
      private Integer id;     //主键id
      private String name;    //学生姓名
      private Integer age;    //学生年龄
  }
  ```

* 映射配置文件

  ```xml
  <mapper namespace="OneToManyMapper">
      <resultMap id="oneToMany" type="bean.Classes">
          <id column="cid" property="id"/>
          <result column="cname" property="name"/>
  
          <!--collection：配置被包含的集合对象映射关系-->
          <collection property="students" ofType="bean.Student">
              <id column="sid" property="id"/>
              <result column="sname" property="name"/>
              <result column="sage" property="age"/>
          </collection>
      </resultMap>
      <select id="selectAll" resultMap="oneToMany"> <!--SQL-->
          SELECT c.id cid,c.name cname,s.id sid,s.name sname,s.age sage FROM classes c,student s WHERE c.id=s.cid
      </select>
  </mapper>
  ```

* 代码实现片段

  ```java
  //4.获取OneToManyMapper接口的实现类对象
  OneToManyMapper mapper = sqlSession.getMapper(OneToManyMapper.class);
  
  //5.调用实现类的方法，接收结果
  List<Classes> classes = mapper.selectAll();
  
  //6.处理结果
  for (Classes cls : classes) {
      System.out.println(cls.getId() + "," + cls.getName());
      List<Student> students = cls.getStudents();
      for (Student student : students) {
          System.out.println("\t" + student);
      }
  }
  ```





***



### 多对多

学生课程例子，中间表不需要 bean 实体类

* 数据准备

  ```mysql
  CREATE TABLE course(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	name VARCHAR(20)
  );
  INSERT INTO course VALUES (NULL,'语文'),(NULL,'数学');
  
  CREATE TABLE stu_cr(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	sid INT,
  	cid INT,
  	CONSTRAINT sc_fk1 FOREIGN KEY (sid) REFERENCES student(id),
  	CONSTRAINT sc_fk2 FOREIGN KEY (cid) REFERENCES course(id)
  );
  INSERT INTO stu_cr VALUES (NULL,1,1),(NULL,1,2),(NULL,2,1),(NULL,2,2);
  ```

* bean类

  ```java
  public class Student {
      private Integer id;     //主键id
      private String name;    //学生姓名
      private Integer age;    //学生年龄
      private List<Course> courses;   // 学生所选择的课程集合
  }
  public class Course {
      private Integer id;     //主键id
      private String name;    //课程名称
  }
  ```

* 配置文件

  ```xml
  <mapper namespace="ManyToManyMapper">
      <resultMap id="manyToMany" type="Bean.Student">
          <id column="sid" property="id"/>
          <result column="sname" property="name"/>
          <result column="sage" property="age"/>
  
          <collection property="courses" ofType="Bean.Course">
              <id column="cid" property="id"/>
              <result column="cname" property="name"/>
          </collection>
      </resultMap>
      <select id="selectAll" resultMap="manyToMany"> <!--SQL-->
          SELECT sc.sid,s.name sname,s.age sage,sc.cid,c.name cname FROM student s,course c,stu_cr sc WHERE sc.sid=s.id AND sc.cid=c.id
      </select>
  </mapper>
  ```




***



### 鉴别器

需求：如果查询结果是女性，则把部门信息查询出来，否则不查询 ；如果是男性，把 last_name 这一列的值赋值给emai

```xml
<!-- 
    column：指定要判断的列名 
    javaType：列值对应的java类型
   -->
<discriminator javaType="string" column="gender">
    <!-- 女生 -->
    <!-- resultType不可缺少，也可以使用resutlMap -->
    <case value="0" resultType="com.bean.Employee">
        <association property="dept"
                     select="com.dao.DepartmentMapper.getDeptById"
                     column="d_id">
        </association>
    </case>
    <!-- 男生 -->
    <case value="1" resultType="com.bean.Employee">
        <id column="id" property="id"/>
        <result column="last_name" property="lastName"/>
        <result column="gender" property="gender"/>
    </case>
</discriminator>
```





****



### 延迟加载

#### 两种加载

立即加载：只要调用方法，马上发起查询

延迟加载：在需要用到数据时才进行加载，不需要用到数据时就不加载数据，延迟加载也称懒加载。  

优点： 先从单表查询，需要时再从关联表去关联查询，提高数据库性能，因为查询单表要比关联查询多张表速度要快，节省资源

坏处：只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，查询工作也要消耗时间，所以可能造成用户等待时间变长，造成用户体验下降

核心配置文件

| 标签名                | 描述                                                         | 默认值 |
| --------------------- | ------------------------------------------------------------ | ------ |
| lazyLoadingEnabled    | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | false  |
| aggressiveLazyLoading | 开启时，任一方法的调用都会加载该对象的所有延迟加载属性。否则每个延迟加载属性会按需加载（参考 lazyLoadTriggerMethods） | false  |

```xml
<settings> 
	<setting name="lazyLoadingEnabled" value="true"/> 
    <setting name="aggressiveLazyLoading" value="false"/> 
</settings>
```



***



#### assocation

分布查询：先按照身份 id 查询所属人的 id、然后根据所属人的 id 去查询人的全部信息，这就是分步查询

* 映射配置文件 OneToOneMapper.xml 

  一对一映射：

  * column 属性表示要调用的其它的 select 标签中传入参数
  * select 属性表示调用其它的 select 标签
  * fetchType="lazy" 表示延迟加载（局部配置，只有配置了这个的地方才会延迟加载）

  ```xml
  <mapper namespace="OneToOneMapper">
      <!--配置字段和实体对象属性的映射关系-->
      <resultMap id="oneToOne" type="card">
          <id column="id" property="id" />
          <result column="number" property="number" />
          <association property="p" javaType="bean.Person"
                       column="pid" 
                       select="one_to_one.PersonMapper.findPersonByid"
                       fetchType="lazy">
              		<!--需要配置新的映射文件-->
          </association>
      </resultMap>
  
      <select id="selectAll" resultMap="oneToOne"> 
          SELECT * FROM card <!--查询全部，负责根据条件直接全部加载-->
      </select>
  </mapper>
  ```

* PersonMapper.xml

  ```xml
  <mapper namespace="one_to_one.PersonMapper">
      <select id="findPersonByid" parameterType="int" resultType="person">
          SELECT * FROM person WHERE id=#{pid}
      </select>
  </mapper>
  ```

* PersonMapper.java

  ```java
  public interface PersonMapper {
      /**
       * 为了演示分步查询的一对多另写的一个方法
       */
      User findPersonByid(int id);
  }
  ```

* 测试文件

  ```java
  public class Test01 {
      @Test
      public void selectAll() throws Exception{
          InputStream is = Resources.getResourceAsStream("MyBatisConfig.xml");
          SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(is);
          SqlSession sqlSession = ssf.openSession(true);
          OneToOneMapper mapper = sqlSession.getMapper(OneToOneMapper.class);
          //调用实现类的方法，接收结果
          List<Card> list = mapper.selectAll();
          
        	//不能遍历，遍历就是相当于使用了该数据，需要加载，不遍历就是没有使用。
          
          //释放资源
          sqlSession.close();
          is.close();
      }
  }
  ```
  
  



***



#### collection

同样在一对多关系配置的 <collection> 结点中配置延迟加载策略，<collection> 结点中也有 select 属性和 column 属性。

* 映射配置文件 OneToManyMapper.xml
  
  一对多映射：
  
  * column 是用于指定使用哪个字段的值作为条件查询
  * select 是用于指定查询账户的唯一标识（账户的dao全限定类名加上方法名称） 
  
  ```xml
  <mapper namespace="OneToManyMapper">
      <resultMap id="oneToMany" type="bean.Classes">
          <id column="id" property="id"/>
          <result column="name" property="name"/>
  
          <!--collection：配置被包含的集合对象映射关系-->
          <collection property="students" ofType="bean.Student"
                      column="id" 
                      select="one_to_one.StudentMapper.findStudentByCid">
          </collection>
      </resultMap>
      <select id="selectAll" resultMap="oneToMany">
        SELECT * FROM classes
      </select>
  </mapper>
  ```
  
* StudentMapper.xml

  ```xml
  <mapper namespace="one_to_one.StudentMapper">
      <select id="findPersonByCid" parameterType="int" resultType="student">
          SELECT * FROM person WHERE cid=#{id}
      </select>
  </mapper>
  ```





****





## 注解开发

### 单表操作

注解可以简化开发操作，省略映射配置文件的编写

常用注解：

* @Select(“查询的 SQL 语句”)：执行查询操作注解
* @Insert(“插入的 SQL 语句”)：执行新增操作注解
* @Update(“修改的 SQL 语句”)：执行修改操作注解
* @Delete(“删除的 SQL 语句”)：执行删除操作注解

参数注解：

* @Param：当 SQL 语句需要多个（大于1）参数时，用来指定参数的对应规则

核心配置文件配置映射关系：

```xml
<mappers>
	<package name="使用了注解的Mapper接口所在包"/>
</mappers>
<!--或者-->
<mappers>
 	<mapper class="包名.Mapper名"></mapper>
</mappers>
```

基本增删改查：

* 创建Mapper接口

  ```java
  package mapper;
  public interface StudentMapper {
      //查询全部
      @Select("SELECT * FROM student")
      public abstract List<Student> selectAll();
  
      //新增数据
      @Insert("INSERT INTO student VALUES (#{id},#{name},#{age})")
      public abstract Integer insert(Student student);
  
      //修改操作
      @Update("UPDATE student SET name=#{name},age=#{age} WHERE id=#{id}")
      public abstract Integer update(Student student);
  
      //删除操作
      @Delete("DELETE FROM student WHERE id=#{id}")
      public abstract Integer delete(Integer id);
  
  }
  ```

* 修改MyBatis的核心配置文件

  ```xml
  <mappers>
  	<package name="mapper"/>
  </mappers>
  ```

* bean类

  ```java
  public class Student {
      private Integer id;
      private String name;
      private Integer age;
  }
  ```

* 测试类

  ```java
  @Test
  public void selectAll() throws Exception{
      //1.加载核心配置文件
      InputStream is = Resources.getResourceAsStream("MyBatisConfig.xml");
  
      //2.获取SqlSession工厂对象
      SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(is);
  
      //3.通过工厂对象获取SqlSession对象
      SqlSession sqlSession = ssf.openSession(true);
  
      //4.获取StudentMapper接口的实现类对象
      StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
  
      //5.调用实现类对象中的方法，接收结果
      List<Student> list = mapper.selectAll();
  
      //6.处理结果
      for (Student student : list) {
          System.out.println(student);
      }
      
      //7.释放资源
      sqlSession.close();
      is.close();
  }
  ```



***



### 多表操作

#### 相关注解

实现复杂关系映射之前我们可以在映射文件中通过配置 <resultMap> 来实现，使用注解开发后，可以使用@Results 注解，@Result 注解，@One 注解，@Many 注解组合完成复杂关系的配置

| 注解          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| @Results      | 代替<resultMap>标签，注解中使用单个@Result注解或者@Result集合<br/>使用格式：@Results({ @Result(), @Result() })或@Results({ @Result() }) |
| @Result       | 代替<id>和<result>标签，@Result中属性介绍：<br />column：数据库的列名      property：封装类的变量名<br />one：需要使用@One注解（@Result(one = @One)）<br />Many：需要使用@Many注解（@Result(many= @Many)） |
| @One(一对一)  | 代替<association>标签，多表查询的关键，用来指定子查询返回单一对象<br/>select：指定调用Mapper接口中的某个方法<br />使用格式：@Result(column="", property="", one=@One(select="")) |
| @Many(多对一) | 代替<collection>标签，多表查询的关键，用来指定子查询返回对象集合<br />select：指定调用Mapper接口中的某个方法<br />使用格式：@Result(column="", property="", many=@Many(select="")) |



***



#### 一对一

身份证对人

* PersonMapper接口

  ```java
  public interface PersonMapper {
      //根据id查询
      @Select("SELECT * FROM person WHERE id=#{id}")
      public abstract Person selectById(Integer id);
  }
  ```

* CardMapper接口

  ```java
  public interface CardMapper {
      //查询全部
      @Select("SELECT * FROM card")
      @Results({
              @Result(column = "id",property = "id"),
              @Result(column = "number",property = "number"),
              @Result(
                      property = "p",             // 被包含对象的变量名
                      javaType = Person.class,    // 被包含对象的实际数据类型
                      column = "pid",  // 根据查询出的card表中的pid字段来查询person表
                       /* 
                       	one、@One 一对一固定写法
                          select属性：指定调用哪个接口中的哪个方法
                       */
                      one = @One(select = "one_to_one.PersonMapper.selectById")
              )
      })
      public abstract List<Card> selectAll();
  }
  ```

* 测试类（详细代码参考单表操作）

  ```java
  //1.加载核心配置文件
  //2.获取SqlSession工厂对象
  //3.通过工厂对象获取SqlSession对象
  
  //4.获取StudentMapper接口的实现类对象
  CardMapper mapper = sqlSession.getMapper(CardMapper.class);
  //5.调用实现类对象中的方法，接收结果
  List<Card> list = mapper.selectAll();
  ```

  

***



#### 一对多

班级和学生

* StudentMapper接口

  ```java
  public interface StudentMapper {
      //根据cid查询student表  cid是外键约束列
      @Select("SELECT * FROM student WHERE cid=#{cid}")
      public abstract List<Student> selectByCid(Integer cid);
  }
  ```

* ClassesMapper接口

  ```java
  public interface ClassesMapper {
      //查询全部
      @Select("SELECT * FROM classes")
      @Results({
              @Result(column = "id", property = "id"),
              @Result(column = "name", property = "name"),
              @Result(
                      property = "students",  //被包含对象的变量名
                      javaType = List.class,  //被包含对象的实际数据类型
                      column = "id",          //根据id字段查询student表
                      many = @Many(select = "one_to_many.StudentMapper.selectByCid")
              )
      })
      public abstract List<Classes> selectAll();
  }
  ```

* 测试类

  ```java 
  //4.获取StudentMapper接口的实现类对象
  ClassesMapper mapper = sqlSession.getMapper(ClassesMapper.class);
  //5.调用实现类对象中的方法，接收结果
  List<Classes> classes = mapper.selectAll();
  ```

  

***



#### 多对多

学生和课程

* SQL查询语句

  ```mysql
  SELECT DISTINCT s.id,s.name,s.age FROM student s,stu_cr sc WHERE sc.sid=s.id
  SELECT c.id,c.name FROM stu_cr sc,course c WHERE sc.cid=c.id AND sc.sid=#{id}
  ```
  
* CourseMapper接口

  ```java
  public interface CourseMapper {
      //根据学生id查询所选课程
      @Select("SELECT c.id,c.name FROM stu_cr sc,course c WHERE sc.cid=c.id AND sc.sid=#{id}")
      public abstract List<Course> selectBySid(Integer id);
  }
  ```

* StudentMapper接口

  ```java
  public interface StudentMapper {
      //查询全部
      @Select("SELECT DISTINCT s.id,s.name,s.age FROM student s,stu_cr sc WHERE sc.sid=s.id")
      @Results({
              @Result(column = "id",property = "id"),
              @Result(column = "name",property = "name"),
              @Result(column = "age",property = "age"),
              @Result(
                      property = "courses",    //被包含对象的变量名
                      javaType = List.class,  //被包含对象的实际数据类型
                      column = "id", //根据查询出的student表中的id字段查询中间表和课程表
                      many = @Many(select = "many_to_many.CourseMapper.selectBySid")
              )
      })
      public abstract List<Student> selectAll();
  }
  
  ```

* 测试类

  ```java
  //4.获取StudentMapper接口的实现类对象
  StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
  //5.调用实现类对象中的方法，接收结果
  List<Student> students = mapper.selectAll();
  ```





***





## 缓存机制

### 缓存概述

缓存：缓存就是一块内存空间，保存临时数据

作用：将数据源（数据库或者文件）中的数据读取出来存放到缓存中，再次获取时直接从缓存中获取，可以减少和数据库交互的次数，提升程序的性能

缓存适用：

+ 适用于缓存的：经常查询但不经常修改的，数据的正确与否对最终结果影响不大的
+ 不适用缓存的：经常改变的数据 , 敏感数据（例如：股市的牌价，银行的汇率，银行卡里面的钱）等等

缓存类别：

* 一级缓存：sqlSession 级别的缓存，又叫本地会话缓存，自带的（不需要配置），一级缓存的生命周期与 sqlSession 一致。在操作数据库时需要构造 sqlSession 对象，在对象中有一个数据结构（HashMap）用于存储缓存数据，不同的 sqlSession 之间的缓存数据区域是互相不影响的
* 二级缓存：mapper（namespace）级别的缓存，二级缓存的使用，需要手动开启（需要配置）。多个 SqlSession 去操作同一个 Mapper 的 sql，可以共用二级缓存，二级缓存是跨 SqlSession 的

开启缓存：配置核心配置文件中<settings>标签

* cacheEnabled：true 表示全局性地开启所有映射器配置文件中已配置的任何缓存

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-缓存的实现原理.png)



参考文章：https://www.cnblogs.com/ysocean/p/7342498.html



***



### 一级缓存

一级缓存是 SqlSession 级别的缓存

<img src="https://gitee.com/seazean/images/raw/master/Frame/MyBatis一级缓存.png" style="zoom: 67%;" />

工作流程：第一次发起查询用户 id 为 1 的用户信息，先去找缓存中是否有 id 为 1 的用户信息，如果没有，从数据库查询用户信息，得到用户信息，将用户信息存储到一级缓存中；第二次发起查询用户 id 为 1 的用户信息，先去找缓存中是否有 id 为 1 的用户信息，缓存中有，直接从缓存中获取用户信息。

一级缓存的失效：

* SqlSession 不同时
* SqlSession 相同，查询条件不同时（还未缓存该数据）
* SqlSession 相同，手动清除了一级缓存，调用`openSession.clearCache()`
* SqlSession 相同，执行 commit 操作（执行插入、更新、删除），清空 SqlSession 中的一级缓存，这样做的目的为了让缓存中存储的是最新的信息，避免脏读。

测试一级缓存存在

```java
public void testFirstLevelCache(){
    //1. 获取sqlSession对象
    SqlSession sqlSession = SqlSessionFactoryUtils.openSession();
    //2. 通过sqlSession对象获取UserDao接口的代理对象
    UserDao userDao1 = sqlSession.getMapper(UserDao.class);
    //3. 调用UserDao接口的代理对象的findById方法获取信息
	User user1 = userDao1.findById(1);
	System.out.println(user1);
    
    //sqlSession.clearCache() 清空缓存
    
   	UserDao userDao2 = sqlSession.getMapper(UserDao.class);
    User user = userDao.findById(1);
    System.out.println(user2);
    
    //4.测试两次结果是否一样
    System.out.println(user1 == user2);//true
    
    //5. 提交事务关闭资源
    SqlSessionFactoryUtils.commitAndClose(sqlSession);
}
```





****



### 二级缓存

二级缓存是 mapper 的缓存，只要是同一个 mapper 的 SqlSession 就共享二级缓存的内容，并且可以操作二级缓存

工作流程：一个会话查询一条数据，这个数据就会被存放在当前会话的一级缓存中，如果**会话关闭**，一级缓存中的数据会被保存到二级缓存

二级缓存的基本使用：

1. 在 MyBatisConfig.xml 文件开启二级缓存，**cacheEnabled 默认值为 true**，所以这一步可以省略不配置

   ```xml
   <!--配置开启二级缓存-->
   <settings>
       <setting name="cacheEnabled" value="true"/>
   </settings>
   ```

2. 配置 Mapper 映射文件

   `<cache>` 标签表示当前这个 mapper 映射将使用二级缓存，区分的标准就看 mapper 的 namespace 值

   ```xml
   <mapper namespace="dao.UserDao">
       <!--开启user支持二级缓存-->
      	<cache eviction="FIFO" flushInterval="6000" readOnly="" size="1024"/>
   	<cache></cache>则表示所有属性使用默认值
   </mapper>
   ```

   eviction（清除策略）：

   - `LRU` – 最近最少使用：移除最长时间不被使用的对象，默认
   - `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们
   - `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象
   - `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象

   flushInterval（刷新间隔）：可以设置为任意的正整数， 默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新

   size（引用数目）：缓存存放多少元素，默认值是 1024

   readOnly（只读）：可以被设置为 true 或 false

   * 只读的缓存会给所有调用者返回缓存对象的相同实例，因此这些对象不能被修改，促进了性能提升
   * 可读写的缓存会（通过序列化）返回缓存对象的拷贝， 速度上会慢一些，但是更安全，因此默认值是 false

   type：指定自定义缓存的全类名，实现 Cache 接口即可

3. 要进行二级缓存的类必须实现 java.io.Serializable 接口，可以使用序列化方式来保存对象。 

   ```java
   public class User implements Serializable{}
   ```

相关属性：

1. select 标签的 useCache 属性

   映射文件中的 `<select>` 标签中设置 `useCache="true"` 代表当前 statement 要使用二级缓存。
   注意：针对每次查询都需要最新的数据 sql，要设置成 useCache=false，禁用二级缓存

   ```xml
   <select id="findAll" resultType="user" useCache="true">
       select * from user
   </select>
   ```

2. 每个增删改标签都有 flushCache 属性，默认为 true，代表在执行增删改之后就会清除一、二级缓存，而查询标签默认值为 false，所以查询不会清空缓存

3. localCacheScope：本地缓存作用域，默认值为 SESSION，当前会话的所有数据保存在会话缓存中，设置为 STATEMENT 禁用一级缓存



***



### 自定义

自定义缓存

```xml
<cache type="com.domain.something.MyCustomCache"/>
```

type 属性指定的类必须实现 org.apache.ibatis.cache.Cache 接口，且提供一个接受 String 参数作为 id 的构造器

```java
public interface Cache {
  String getId();
  int getSize();
  void putObject(Object key, Object value);
  Object getObject(Object key);
  boolean hasKey(Object key);
  Object removeObject(Object key);
  void clear();
}
```

缓存的配置，只需要在缓存实现中添加公有的 JavaBean 属性，然后通过 cache 元素传递属性值，例如在缓存实现上调用一个名为 `setCacheFile(String file)` 的方法：

```xml
<cache type="com.domain.something.MyCustomCache">
  <property name="cacheFile" value="/tmp/my-custom-cache.tmp"/>
</cache>
```

* 可以使用所有简单类型作为 JavaBean 属性的类型，MyBatis 会进行转换。
* 可以使用占位符（如 `${cache.file}`），以便替换成在配置文件属性中定义的值

MyBatis 支持在所有属性设置完毕之后，调用一个初始化方法， 如果想要使用这个特性，可以在自定义缓存类里实现 `org.apache.ibatis.builder.InitializingObject` 接口

```java
public interface InitializingObject {
  void initialize() throws Exception;
}
```

注意：对缓存的配置（如清除策略、可读或可读写等），不能应用于自定义缓存

对某一命名空间的语句，只会使用该命名空间的缓存进行缓存或刷新，在多个命名空间中共享相同的缓存配置和实例，可以使用 cache-ref 元素来引用另一个缓存

```xml
<cache-ref namespace="com.someone.application.data.SomeMapper"/>
```





***





## 构造语句

### 动态SQL

#### 基本介绍

动态 SQL 是 MyBatis 强大特性之一，逻辑复杂时，MyBatis 映射配置文件中，SQL 是动态变化的，所以引入动态 SQL 简化拼装 SQL 的操作

DynamicSQL 包含的标签：

* if
* where
* set
* choose (when、otherwise)
* trim
* foreach

各个标签都可以进行灵活嵌套和组合

OGNL：Object Graphic Navigation Language（对象图导航语言），用于对数据进行访问



参考文章：https://www.cnblogs.com/ysocean/p/7289529.html



***



#### where

<where>：条件标签，有动态条件则使用该标签代替 WHERE 关键字，封装查询条件

作用：如果标签返回的内容是以 AND 或 OR 开头的，标签内会剔除掉

表结构：

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-动态sql用户表.png)



****



#### if

基本格式：

```xml
<if test=“条件判断”>
	查询条件拼接
</if>
```

我们根据实体类的不同取值，使用不同的 SQL 语句来进行查询。比如在 id 如果不为空时可以根据 id 查询，如果username 不同空时还要加入用户名作为条件，这种情况在我们的多条件组合查询中经常会碰到。

* UserMapper.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <mapper namespace="mapper.UserMapper">
      <select id="selectCondition" resultType="user" parameterType="user">
          SELECT * FROM user
          <where>
              <if test="id != null ">
                  id = #{id}
              </if>
              <if test="username != null ">
                  AND username = #{username}
              </if>
              <if test="sex != null ">
                  AND sex = #{sex}
              </if>
          </where>
      </select>	
  
  </mapper>
  ```

* MyBatisConfig.xml，引入映射配置文件

  ```xml
  <mappers>
      <!--mapper引入指定的映射配置 resource属性执行的映射配置文件的名称-->
      <mapper resource="UserMapper.xml"/>
  </mappers>
  ```

* DAO层Mapper接口

  ```java
  public interface UserMapper {
      //多条件查询
      public abstract List<User> selectCondition(Student stu);
  }
  ```

* 实现类

  ```java
  public class DynamicTest {
      @Test
      public void selectCondition() throws Exception{
          //1.加载核心配置文件
          InputStream is = Resources.getResourceAsStream("MyBatisConfig.xml");
  
          //2.获取SqlSession工厂对象
          SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(is);
  
          //3.通过工厂对象获取SqlSession对象
          SqlSession sqlSession = ssf.openSession(true);
  
          //4.获取StudentMapper接口的实现类对象
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
  
          User user = new User();
          user.setId(2);
          user.setUsername("李四");
          //user.setSex(男); AND 后会自动剔除
  
          //5.调用实现类的方法，接收结果
          List<Student> list = mapper.selectCondition(user);
  
          //6.处理结果
          for (User user : list) {
              System.out.println(user);
          }
          
          //7.释放资源
          sqlSession.close();
          is.close();
      }
  }
  ```

  

***



#### set

<set>：进行更新操作的时候，含有 set 关键词，使用该标签

```xml
<!-- 根据 id 更新 user 表的数据 -->
<update id="updateUserById" parameterType="com.ys.po.User">
    UPDATE user u
        <set>
            <if test="username != null and username != ''">
                u.username = #{username},
            </if>
            <if test="sex != null and sex != ''">
                u.sex = #{sex}
            </if>
        </set>
     WHERE id=#{id}
</update>
```

* 如果第一个条件 username 为空，那么 sql 语句为：update user u set u.sex=? where id=?
* 如果第一个条件不为空，那么 sql 语句为：update user u set u.username = ? ,u.sex = ? where id=?





****



#### choose

假如不想用到所有的查询条件，只要查询条件有一个满足即可，使用 choose 标签可以解决此类问题，类似于 Java 的 switch 语句

标签：<when>，<otherwise>

```xml
<select id="selectUserByChoose" resultType="user" parameterType="user">
    SELECT * FROM user
    <where>
        <choose>
            <when test="id !='' and id != null">
                id=#{id}
            </when>
            <when test="username !='' and username != null">
                AND username=#{username}
            </when>
            <otherwise>
                AND sex=#{sex}
            </otherwise>
        </choose>
    </where>
</select>
```

有三个条件，id、username、sex，只能选择一个作为查询条件

* 如果 id 不为空，那么查询语句为：select * from user where  id=?

* 如果 id 为空，那么看 username 是否为空
  * 如果不为空，那么语句为：select * from user where username=?
  * 如果 username 为空，那么查询语句为 select * from user where sex=?



***



#### trim

trim 标记是一个格式化的标记，可以完成 set 或者是 where 标记的功能，自定义字符串截取

* prefix：给拼串后的整个字符串加一个前缀，trim 标签体中是整个字符串拼串后的结果
* prefixOverrides：去掉整个字符串前面多余的字符
* suffix：给拼串后的整个字符串加一个后缀
* suffixOverrides：去掉整个字符串后面多余的字符

改写 if+where 语句：

```xml
<select id="selectUserByUsernameAndSex" resultType="user" parameterType="com.ys.po.User">
    SELECT * FROM user
    <trim prefix="where" prefixOverrides="and | or">
        <if test="username != null">
            AND username=#{username}
        </if>
        <if test="sex != null">
            AND sex=#{sex}
        </if>
    </trim>
</select>
```

改写 if+set 语句：

```xml
<!-- 根据 id 更新 user 表的数据 -->
<update id="updateUserById" parameterType="com.ys.po.User">
    UPDATE user u
    <trim prefix="set" suffixOverrides=",">
        <if test="username != null and username != ''">
            u.username = #{username},
        </if>
        <if test="sex != null and sex != ''">
            u.sex = #{sex},
        </if>
    </trim>
    WHERE id=#{id}
</update>
```



****



#### foreach

基本格式：

```xml
<foreach>：循环遍历标签。适用于多个参数或者的关系。
    <foreach collection=“”open=“”close=“”item=“”separator=“”>
		获取参数
</foreach>
```

属性：

* collection：参数容器类型， (list-集合， array-数组)
* open：开始的 SQL 语句
* close：结束的 SQL 语句
* item：参数变量名
* separator：分隔符

需求：循环执行 sql 的拼接操作，`SELECT * FROM user WHERE id IN (1,2,5)`

* UserMapper.xml片段

  ```xml
  <select id="selectByIds" resultType="user" parameterType="list">
      SELECT * FROM student
      <where>
          <foreach collection="list" open="id IN(" close=")" item="id" separator=",">
              #{id}
          </foreach>
      </where>
  </select>
  ```

* 测试代码片段

  ```java
  //4.获取StudentMapper接口的实现类对象
  UserMapper mapper = sqlSession.getMapper(UserMapper.class);
  
  List<Integer> ids = new ArrayList<>();
  Collections.addAll(list, 1, 2);
  //5.调用实现类的方法，接收结果
  List<User> list = mapper.selectByIds(ids);
  
  for (User user : list) {
      System.out.println(user);
  }
  ```

  

***



#### SQL片段

将一些重复性的 SQL 语句进行抽取，以达到复用的效果

格式：

```xml
<sql id=“片段唯一标识”>抽取的SQL语句</sql>		<!--抽取标签-->
<include refid=“片段唯一标识”/>				<!--引入标签-->
```

使用：

```xml
<sql id="select">SELECT * FROM user</sql>

<select id="selectByIds" resultType="user" parameterType="list">
    <include refid="select"/>
    <where>
        <foreach collection="list" open="id IN(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
 </select>
```





****



### 逆向工程

MyBatis 逆向工程，可以针对**单表**自动生成 mybatis 执行所需要的代码（mapper.java、mapper.xml、pojo…）

generatorConfig.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
 
<generatorConfiguration>
    <context id="testTables" targetRuntime="MyBatis3">
        <commentGenerator>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true" />
        </commentGenerator>
        <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
            connectionURL="jdbc:mysql://localhost:3306/mybatisrelation" userId="root"
            password="root">
        </jdbcConnection>
 
        <!-- 默认false，把JDBC DECIMAL 和 NUMERIC 类型解析为 Integer，为 true时把JDBC DECIMAL和NUMERIC类型解析为java.math.BigDecimal -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>
 
        <!-- targetProject:生成PO类的位置！！ -->
        <javaModelGenerator targetPackage="com.ys.po"
            targetProject=".\src">
            <!-- enableSubPackages:是否让schema作为包的后缀 -->
            <property name="enableSubPackages" value="false" />
            <!-- 从数据库返回的值被清理前后的空格 -->
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
        <!-- targetProject:mapper映射文件生成的位置！！ -->
        <sqlMapGenerator targetPackage="com.ys.mapper"
            targetProject=".\src">
            <property name="enableSubPackages" value="false" />
        </sqlMapGenerator>
        <!-- targetPackage：mapper接口生成的位置，重要！！ -->
        <javaClientGenerator type="XMLMAPPER"
            targetPackage="com.ys.mapper"
            targetProject=".\src">
            <property name="enableSubPackages" value="false" />
        </javaClientGenerator>
        <!-- 指定数据库表，要生成哪些表，就写哪些表，要和数据库中对应，不能写错！ -->
        <table tableName="items"></table>
        <table tableName="orders"></table>
        <table tableName="orderdetail"></table>
        <table tableName="user"></table>       
    </context>
</generatorConfiguration>
```

生成代码：

```java
public void testGenerator() throws Exception{
    List<String> warnings = new ArrayList<String>();
    boolean overwrite = true;
    //指向逆向工程配置文件
    File configFile = new File(GeneratorTest.class.
                               getResource("/generatorConfig.xml").getFile());
    ConfigurationParser cp = new ConfigurationParser(warnings);
    Configuration config = cp.parseConfiguration(configFile);
    DefaultShellCallback callback = new DefaultShellCallback(overwrite);
    MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
                                                             callback, warnings);
    myBatisGenerator.generate(null);

}
```



参考文章：https://www.cnblogs.com/ysocean/p/7360409.html



***





### 构建SQL

#### 基础语法

MyBatis 提供了 org.apache.ibatis.jdbc.SQL 功能类，专门用于构建 SQL 语句    

| 方法                          | 说明                 |
| ----------------------------- | -------------------- |
| SELECT(String... columns)     | 根据字段拼接查询语句 |
| FROM(String... tables)        | 根据表名拼接语句     |
| WHERE(String... conditions)   | 根据条件拼接语句     |
| INSERT_INTO(String tableName) | 根据表名拼接新增语句 |
| INTO_VALUES(String... values) | 根据值拼接新增语句   |
| UPDATE(String table)          | 根据表名拼接修改语句 |
| DELETE_FROM(String table)     | 根据表名拼接删除语句 |

增删改查注解：

* @SelectProvider：生成查询用的 SQL 语句
* @InsertProvider：生成新增用的 SQL 语句
* @UpdateProvider：生成修改用的 SQL 语句注解
* @DeleteProvider：生成删除用的 SQL 语句注解。
  * type 属性：生成 SQL 语句功能类对象 
  * method 属性：指定调用方法



***



#### 基本操作

* MyBatisConfig.xml配置

  ```xml
   <!-- mappers引入映射配置文件 -->
  <mappers>
      <package name="mapper"/>
  </mappers>
  ```

* Mapper类

  ```java
  public interface StudentMapper {
      //查询全部
      @SelectProvider(type = ReturnSql.class, method = "getSelectAll")
      public abstract List<Student> selectAll();
  
      //新增数据
      @InsertProvider(type = ReturnSql.class, method = "getInsert")
      public abstract Integer insert(Student student);
  
      //修改操作
      @UpdateProvider(type = ReturnSql.class, method = "getUpdate")
      public abstract Integer update(Student student);
  
      //删除操作
      @DeleteProvider(type = ReturnSql.class, method = "getDelete")
      public abstract Integer delete(Integer id);
  
  }
  ```

* ReturnSql类

  ```java
  public class ReturnSql {
      //定义方法，返回查询的sql语句
      public String getSelectAll() {
          return new SQL() {
              {
                  SELECT("*");
                  FROM("student");
              }
          }.toString();
      }
  
      //定义方法，返回新增的sql语句
      public String getInsert(Student stu) {
          return new SQL() {
              {
                  INSERT_INTO("student");
                  INTO_VALUES("#{id},#{name},#{age}");
              }
          }.toString();
      }
  
      //定义方法，返回修改的sql语句
      public String getUpdate(Student stu) {
          return new SQL() {
              {
                  UPDATE("student");
                  SET("name=#{name}","age=#{age}");
                  WHERE("id=#{id}");
              }
          }.toString();
      }
  
      //定义方法，返回删除的sql语句
      public String getDelete(Integer id) {
          return new SQL() {
              {
                  DELETE_FROM("student");
                  WHERE("id=#{id}");
              }
          }.toString();
      }
  }
  ```

* 功能实现类

  ```java
  public class SqlTest {	
  	@Test  //查询全部
      public void selectAll() throws Exception{
          //1.加载核心配置文件
          InputStream is = Resources.getResourceAsStream("MyBatisConfig.xml");
  
          //2.获取SqlSession工厂对象
          SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
  
          //3.通过工厂对象获取SqlSession对象
          SqlSession sqlSession = sqlSessionFactory.openSession(true);
  
          //4.获取StudentMapper接口的实现类对象
          StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
  
          //5.调用实现类对象中的方法，接收结果
          List<Student> list = mapper.selectAll();
  
          //6.处理结果
          for (Student student : list) {
              System.out.println(student);
          }
  
          //7.释放资源
          sqlSession.close();
          is.close();
      }
      
      @Test  //新增
      public void insert() throws Exception{
          //1 2 3 4获取StudentMapper接口的实现类对象
          StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
  
          //5.调用实现类对象中的方法，接收结果 ->6 7
          Student stu = new Student(4,"赵六",26);
          Integer result = mapper.insert(stu);
      }
      
      @Test //修改
      public void update() throws Exception{
          //1 2 3 4 5调用实现类对象中的方法，接收结果 ->6 7 
  		Student stu = new Student(4,"赵六wq",36);
          Integer result = mapper.update(stu);
      }
      @Test //删除
      public void delete() throws Exception{
          //1 2 3 4 5 6 7
          Integer result = mapper.delete(4);
      }
  }
  ```





***





## 运行原理

### 运行机制

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-执行流程.png)

MyBatis 运行原理：

1. 通过加载 mybatis 全局配置文件以及 mapper 映射文件初始化 configuration 对象和 Executor 对象（通过全局配置文件中的 defaultExecutorType 初始化）

2. 创建一个 defaultSqlSession 对象，将 configuration 和 Executor 对象注入到 defaulSqlSession 对象

3. defaulSqlSession 通过 getMapper() 获取 mapper 接口的代理对象 mapperProxy 

4. 执行增删改查：

   * 通过 defaulSqlSession 中的属性 Executor 创建 statementHandler 对象
   * 创建 statementHandler 对象的同时也创建 parameterHandler 和 resultSetHandler
   * 通过 parameterHandler 设置预编译参数及参数值

   * 调用 statementHandler 执行增删改查

   * 通过 resultsetHandler 封装查询结果

四大对象：

- StatementHandler：处理 sql 语句预编译，设置参数等相关工作
- ParameterHandler：设置预编译参数用的
- ResultHandler：处理结果集
- Executor：执行器，真正进行 java 与数据库交互的对象



参考视频：https://www.bilibili.com/video/BV1mW411M737?p=71



****



### 获取工厂

SqlSessionFactoryBuilder.build(InputStream, String,  Properties)：构建工厂

XMLConfigBuilder.parse()：解析核心配置文件每个标签的信息（**XPath**）

* `parseConfiguration(parser.evalNode("/configuration"))`：读取节点内数据，<configuration>是 MyBatis 配置文件中的顶层标签
* `mapperElement(root.evalNode("mappers"))`：解析 mappers 信息，分为 package 和 单个注册两种
* `Configuration.addMappers()`：将 mapper 接口添加到 mapperRegistry 中，用来**获取代理对象**

* `XMLMapperBuilder.parse()`：解析 mapper 的标签的信息
  * `configurationElement(parser.evalNode("/mapper"))`：解析 mapper 文件，顶层节点 <mapper>
  * `buildStatementFromContext(context.evalNodes("select..."))`：解析操作标签
  * `XMLStatementBuilder.parseStatementNode()`：解析操作标签的所有的属性
  * `builderAssistant.addMappedStatement(...)`：封装成 MappedStatement 对象

return new DefaultSqlSessionFactory(config)：返回工厂对象

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-获取工厂对象.png)

总结：解析 xml 是对 Configuration 中的属性进行填充，那么我们同样可以在一个类中创建 Configuration 对象，手动设置其中属性的值来达到配置的效果



***



### 获取会话

DefaultSqlSessionFactory.openSession()：获取 Session 对象，并且创建 Executor 对象

DefaultSqlSessionFactory.openSessionFromDataSource(...)：ExecutorType 为 Executor 的类型，TransactionIsolationLevel 为事务隔离级别，autoCommit 是否开启事务

* `transactionFactory.newTransaction(DataSource, IsolationLevel, boolean`：事务对象

* `configuration.newExecutor(tx, execType)`：根据参数创建指定类型的 Executor
  * 批量操作笔记的部分有讲解到 <setting> 的属性 defaultExecutorType，根据设置的创建对象
  * 二级缓存默认开启，会包装 Executor 对象 `BaseExecutor.setExecutorWrapper(executor)`

 return new DefaultSqlSession(configuration, executor, autoCommit)：返回 DefaultSqlSession 对象

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-获取会话对象.png)



****



### 获取代理

Configuration.getMapper(Class, SqlSession)：获取代理的 mapper 对象

MapperRegistry.getMapper(Class, SqlSession)：MapperRegistry 是 Configuration 属性，在获取工厂对象时初始化

* `(MapperProxyFactory<T>) knownMappers.get(type)`：获取接口信息封装为 MapperProxyFactory 对象
* `mapperProxyFactory.newInstance(sqlSession)`：创建代理对象
  * `new MapperProxy<>(sqlSession, mapperInterface, methodCache)`：包装对象
    * methodCache 是并发安全的 ConcurrentHashMap 集合，存放要执行的方法
    * `MapperProxy<T> implements InvocationHandler` 是一个 InvocationHandler 对象
  * `Proxy.newProxyInstance()`：**JDK 动态代理**创建 MapperProxy 对象

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-获取代理对象.png)



****



### 执行SQL

MapperProxy.invoke()：执行 SQL 语句，Object 类的方法直接执行

cachedMapperMethod(method)：包装成一个 MapperMethod 对象并初始化该对象

MapperMethod.execute()：根据 switch-case 判断使用的什么类型的 SQL 进行逻辑处理

sqlSession.selectOne(String, Object)：查询数据，底层调用 DefaultSqlSession.selectList(String, Object)

configuration.getMappedStatement(statement)：获取执行者对象

executor.query()：开始执行查询语句，参数通过 wrapCollection() 包装成集合类

* `CachingExecutor.query()`：先执行

  * `MappedStatement.getBoundSql(parameterObject)`：**把 parameterObject 封装成 BoundSql**
    构造函数中有：`this.parameterObject = parameterObject`

    ![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-boundSql对象.png)

  * `CachingExecutor.createCacheKey()`：创建缓存对象

  * `ms.getCache()`：获取二级缓存，`tcm.getObject(cache, key)`：尝试从**二级缓存**中获取数据

* `BaseExecutor.query()`：

  * `localCache.getObject(key) `：尝试从**本地缓存**（一级缓存）获取数据

* `BaseExecutor.queryFromDatabase()`：开始从数据库获取数据，并放入本地缓存

  * `SimpleExecutor.doQuery()`：执行 query
  * `configuration.newStatementHandler()`：创建 StatementHandler 对象
    * 根据 select 标签的 statementType 属性，根据属性选择创建哪种对象
    * 判断 BoundSql 是否被创建，没有创建会重新封装参数信息到 BoundSql
    * StatementHandler 的构造方法中，**创建了 ParameterHandler 和 ResultSetHandler 对象**
    * `interceptorChain.pluginAll(statementHandler)`：拦截器链
  * `prepareStatement()`：通过 StatementHandler **创建 JDBC 的 Statement 对象**
    * `getConnection()`：获取 JDBC 的 Connection 对象
    * `handler.prepare()`：初始化 Statement 对象
      * `instantiateStatement(Connection connection)`：**Connection**  中的方法实例化对象
        * 获取普通执行者对象：`Connection.createStatement()`
        * 获取预编译执行者对象：`Connection.prepareStatement()`
    * `handler.parameterize()`：进行参数的设置
      * `ParameterHandler.setParameters()`：**通过 ParameterHandler 设置参数**
      * `typeHandler.setParameter()`：**通过 TypeHandler 预编译 SQL**
  * `StatementHandler.query()`：**封装成 JDBC 的 PreparedStatement 执行 SQL**

  * `resultSetHandler.handleResultSets(ps)`：**通过 ResultSetHandler 对象封装结果集**

`return list.get(0)`：返回结果集的第一个数据

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis-执行SQL过程.png)







****





## 插件使用

### 插件原理

实现原理：插件是按照插件配置顺序创建层层包装对象，执行目标方法的之后，按照逆向顺序执行（栈）

<img src="https://gitee.com/seazean/images/raw/master/Frame/MyBatis-插件原理.png" style="zoom:50%;" />

在四大对象创建时：

 * 每个创建出来的对象不是直接返回的，而是 `interceptorChain.pluginAll(parameterHandler)`
 * 获取到所有 Interceptor（插件需要实现的接口），调用 `interceptor.plugin(target)`返回 target 包装后的对象
 * 插件机制可以使用插件为目标对象创建一个代理对象（AOP），代理对象可以拦截到四大对象的每一个执行

```java
@Intercepts(
		{
		@Signature(type=StatementHandler.class,method="parameterize",args=java.sql.Statement.class)
		})
public class MyFirstPlugin implements Interceptor{

	//intercept：拦截目标对象的目标方法的执行；	
	@Override
	public Object intercept(Invocation invocation) throws Throwable {
		System.out.println("MyFirstPlugin...intercept:"+invocation.getMethod());
		//动态的改变一下sql运行的参数：以前1号员工，实际从数据库查询11号员工
		Object target = invocation.getTarget();
		System.out.println("当前拦截到的对象："+target);
		//拿到：StatementHandler==>ParameterHandler===>parameterObject
		//拿到target的元数据
		MetaObject metaObject = SystemMetaObject.forObject(target);
		Object value = metaObject.getValue("parameterHandler.parameterObject");
		System.out.println("sql语句用的参数是："+value);
		//修改完sql语句要用的参数
		metaObject.setValue("parameterHandler.parameterObject", 11);
		//执行目标方法
		Object proceed = invocation.proceed();
		//返回执行后的返回值
		return proceed;
	}

	// plugin：包装目标对象的，为目标对象创建一个代理对象
	@Override
	public Object plugin(Object target) {
		//可以借助 Plugin 的 wrap 方法来使用当前 Interceptor 包装我们目标对象
		System.out.println("MyFirstPlugin...plugin:mybatis将要包装的对象" + target);
		Object wrap = Plugin.wrap(target, this);
		//返回为当前target创建的动态代理
		return wrap;
	}

	// setProperties：将插件注册时的property属性设置进来
	@Override
	public void setProperties(Properties properties) {
		System.out.println("插件配置的信息：" + properties);
	}
}
```

核心配置文件：

```xml
<!--plugins：注册插件  -->
<plugins>
    <plugin interceptor="mybatis.dao.MyFirstPlugin">
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </plugin>
</plugins>
```





****



### 分页插件

![](https://gitee.com/seazean/images/raw/master/Frame/分页介绍.png)

* 分页可以将很多条结果进行分页显示。如果当前在第一页，则没有上一页。如果当前在最后一页，则没有下一页，需要明确当前是第几页，这一页中显示多少条结果。  
* MyBatis 是不带分页功能的，如果想实现分页功能，需要手动编写 LIMIT 语句，不同的数据库实现分页的 SQL 语句也是不同，手写分页 成本较高。
* PageHelper：第三方分页助手，将复杂的分页操作进行封装，从而让分页功能变得非常简单



***



### 分页操作

开发步骤：

1. 导入 PageHelper 的 Maven 坐标

2. 在 mybatis 核心配置文件中配置 PageHelper 插件

   注意：分页助手的插件配置在通用 mapper 之前

   ```xml
   <plugins>
       <plugin interceptor="com.github.pagehelper.PageInterceptor">
           <!-- 指定方言 -->
       	<property name="dialect" value="mysql"/>
       </plugin> 
   </plugins>
   <mappers>.........</mappers>
   ```

3. 与 MySQL 分页查询页数计算公式不同
   static <E> Page<E> startPage(int pageNum, int pageSize) : pageNum第几页，pageSize页面大小

   ```java
   @Test
   public void selectAll() {
       //第一页：显示2条数据
       PageHelper.startPage(1,2);
       List<Student> students = sqlSession.selectList("StudentMapper.selectAll");
       for (Student student : students) {
           System.out.println(student);
       }
   }
   ```

   

****



### 参数获取

PageInfo构造方法：

* `PageInfo<Student> info = new PageInfo<>(list)` : list 是 SQL 执行返回的结果集合，参考上一节

PageInfo相关API：

1. startPage()：设置分页参数 
2. PageInfo：分页相关参数功能类。 
3. getTotal()：获取总条数 
4. getPages()：获取总页数
5. getPageNum()：获取当前页
6. getPageSize()：获取每页显示条数
7. getPrePage()：获取上一页 
8. getNextPage()：获取下一页 
9. isIsFirstPage()：获取是否是第一页 
10. isIsLastPage()：获取是否是最后一页 






*****





# Spring

## 概述

### 框架

框架源自于建筑学，隶属土木工程，后发展到软件工程领域

软件工程框架：经过验证的，具有一定功能的，半成品软件

- 经过验证

- 具有一定功能

- 半成品

框架作用：

* 提高开发效率
* 增强可重用性

* 提供编写规范
* 节约维护成本
* 解耦底层实现原理



笔记的总体架构基于黑马程序员的视频进行制作



****



### Spring

Spring 是分层的 JavaSE/EE 应用 full-stack 轻量级开源框架

![](https://gitee.com/seazean/images/raw/master/Frame/Spring框架介绍.png)

Spring 优点：

* 方便解耦，简化开发
* 方便集成各种框架
* 方便程序测试
* AOP 编程难过的支持
* 声明式事务的支持
* 降低 JavaEE API 的使用难度

体系结构：

![](https://gitee.com/seazean/images/raw/master/Frame/Spring体系结构.png)



***



## IoC

### 基本概述

- IoC（Inversion Of Control）控制反转，Spring反向控制应用程序所需要使用的外部资源
- **Spring 控制的资源全部放置在 Spring 容器中，该容器称为 IoC 容器**
- 官方网站：https://spring.io/ → Projects → spring-framework → LEARN → Reference Doc

![](https://gitee.com/seazean/images/raw/master/Frame/Spring_ioc介绍.png)



- 耦合（Coupling）：代码编写过程中所使用技术的结合紧密度，用于衡量软件中各个模块之间的互联程度
- 内聚（Cohesion）：代码编写过程中单个模块内部各组成部分间的联系，用于衡量软件中各个功能模块内部的功能联系
- 代码编写的目标：高内聚，低耦合。同一个模块内的各个元素之间要高度紧密，各个模块之间的相互依存度不紧密

![](https://gitee.com/seazean/images/raw/master/Frame/Spring发展历程.png)





***



### 入门项目

模拟三层架构中表现层调用业务层功能

- 表现层：UserApp 模拟 UserServlet（使用 main 方法模拟）

- 业务层：UserService

步骤：

1. 导入 spring 坐标（5.1.9.release）

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>5.1.9.RELEASE</version>
   </dependency>
   ```

2. 编写业务层与表现层（模拟）接口与实现类 service.UserService，service.impl.UserServiceImpl

   ```java
   public interface UserService {
   	//业务方法  
   	void save();
   }
   ```

   ```java
   public class UserServiceImpl implements UserService {
       public void save() {
           System.out.println("user service running...");
       }
   }
   ```

3. 建立spring配置文件：resources.**applicationContext**.xml (名字一般使用该格式)

4. 配置所需资源（Service）为 spring 控制的资源

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
       <!-- 1.创建spring控制的资源-->
       <bean id="userService" class="service.impl.UserServiceImpl"/>
   </beans>
   ```

5. 表现层（App）通过spring获取资源（Service实例）

   ```java
   public class UserApp {
       public static void main(String[] args) {
           //2.加载配置文件
           ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
           //3.获取资源
           UserService userService = (UserService) ctx.getBean("userService");
           userService.save();//user service running...
       }
   }
   ```

   ![](https://gitee.com/seazean/images/raw/master/Frame/Spring_ioc实现.png)



****



### XML开发

#### bean

##### 基本属性

标签：<bean> 标签，<beans> 的子标签

作用：定义 Spring 中的资源，受此标签定义的资源将受到 Spring 控制

格式：

```xml
<beans>
	<bean />
</beans>
```

基本属性

* id：bean 的名称，通过 id 值获取 bean (首字母小写)
* class：bean 的类型，使用完全限定类名
* name：bean 的名称，可以通过 name值获取bean，用于多人配合时给bean起别名

```xml
<bean id="beanId" name="beanName1,beanName2" class="ClassName"></bean>
```

```java
ctx.getBean("beanId") == ctx.getBean("beanName1") == ctx.getBean("beanName2")
```



***



##### 作用范围

作用：定义 bean 的作用范围

格式：

```xml
<bean scope="singleton"></bean>
```

取值：

- singleton：设定创建出的对象保存在 Spring 容器中，是一个单例的对象
- prototype：设定创建出的对象保存在 Spring 容器中，是一个非单例（原型）的对象
- request、session、application、 websocket ：设定创建出的对象放置在 web 容器对应的位置



***



##### 生命周期

作用：定义 bean 对象在初始化或销毁时完成的工作

格式：

```xml
<bean init-method="init" destroy-method="destroy></bean>
```

取值：bean 对应的类中对应的具体方法名

实现接口的方式实现初始化，无需配置文件配置 init-method：

* 实现 InitializingBean，定义初始化逻辑
* 实现 DisposableBean，定义销毁逻辑

注意事项：

- 当 scope=“singleton” 时，Spring 容器中有且仅有一个对象，init 方法在创建容器时仅执行一次
- 当 scope=“prototype” 时，Spring 容器要创建同一类型的多个对象，init 方法在每个对象创建时均执行一次
- 当 scope=“singleton” 时，关闭容器（.close()）会导致bean实例的销毁，调用 destroy 方法一次
- 当 scope=“prototype” 时，对象的销毁由垃圾回收机制 gc() 控制，destroy 方法将不会被执行

bean 配置：

```xml
<!--init-method和destroy-method用于控制bean的生命周期-->
<bean id="userService3" scope="prototype" init-method="init" destroy-method="destroy" class="service.impl.UserServiceImpl"/>
```

业务层实现类：

```java
public class UserServiceImpl implements UserService{
    public UserServiceImpl(){
        System.out.println(" constructor is running...");
    }

    public void init(){
        System.out.println("init....");
    }

    public void destroy(){
        System.out.println("destroy....");
    }

    public void save() {
        System.out.println("user service running...");
    }
}
```

测试类：

```java
UserService userService = (UserService)ctx.getBean("userService3");
```





***



##### 创建方式

* 静态工厂

  作用：定义 bean 对象创建方式，使用静态工厂的形式创建 bean，兼容早期遗留系统的升级工作

  格式：

  ```xml
  <bean class="FactoryClassName" factory-method="factoryMethodName"></bean>
  ```

  取值：工厂 bean 中用于获取对象的静态方法名

  注意事项：class 属性必须配置成静态工厂的类名

  bean配置：

  ```xml
  <!--静态工厂创建bean-->
  <bean id="userService4" class="service.UserServiceFactory" factory-method="getService"/>
  ```

  工厂类：

  ```java
  public class UserServiceFactory {
      public static UserService getService(){
          System.out.println("factory create object...");
          return new UserServiceImpl();
      }
  }
  ```

  测试类：

  ```java
  UserService userService = (UserService)ctx.getBean("userService4");
  ```



* 实例工厂

  作用：定义 bean 对象创建方式，使用实例工厂的形式创建 bean，兼容早期遗留系统的升级工作

  格式：

  ```xml
  <bean factory-bean="factoryBeanId" factory-method="factoryMethodName"></bean>
  ```

  注意事项：

  - 使用实例工厂创建 bean 首先需要将实例工厂配置 bean，交由 Spring 进行管理

  - factory-bean 是实例工厂的 Id

  bean 配置：

  ```xml
  <!--实例工厂创建bean，依赖工厂对象对应的bean-->
  <bean id="factoryBean" class="service.UserServiceFactory2"/>
  <bean id="userService5" factory-bean="factoryBean" factory-method="getService"/>
  ```
  
  工厂类：

  ```java
  public class UserServiceFactory2 {
      public UserService getService(){
          System.out.println(" instance factory create object...");
          return new UserServiceImpl();
      }
  }
  ```




***



##### 获取Bean

ApplicationContext子类相关API：

| 方法                                              | 说明                                         |
| ------------------------------------------------- | -------------------------------------------- |
| String[] getBeanDefinitionNames()                 | 获取 Spring容器中定义的所有 JavaBean 的名称  |
| BeanDefinition getBeanDefinition(String beanName) | 返回给定bean名称的BeanDefinition             |
| String[] getBeanNamesForType(Class<?> type)       | 获取Spring容器中指定类型的所有JavaBean的名称 |
| Environment getEnvironment()                      | 获取与此组件关联的环境                       |





***



#### DI

##### 依赖注入

- IoC（Inversion Of Control）控制翻转，Spring 反向控制应用程序所需要使用的外部资源

- DI（Dependency Injection）依赖注入，应用程序运行依赖的资源由 Spring 为其提供，资源进入应用程序的方式称为注入。简单说就是利用反射机制为类的属性赋值的操作

  ![](https://gitee.com/seazean/images/raw/master/Frame/DI介绍.png)

IoC 和 DI 的关系：IoC 与 DI 是同一件事站在不同角度看待问题



***



##### set注入

标签：<property> 标签，<bean> 的子标签

作用：使用 set 方法的形式为 bean 提供资源

格式：

```xml
<bean>
	<property />
    <property />
    .....
</bean>
```

基本属性：

* name：对应 bean 中的属性名，要注入的变量名，要求该属性必须提供可访问的 set 方法
  			 (严格规范此名称是 set 方法对应名称，首字母必须小写)
* value：设定非引用类型属性对应的值，不能与 ref 同时使用
* ref：设定引用类型属性对应 bean 的 id ，不能与 value 同时使用

```xml
<property name="propertyName" value="propertyValue" ref="beanId"/>
```

代码实现：

* DAO层：要注入的资源

  ```java
  public interface UserDao {
      public void save();
  }
  ```

  ```java
  public class UserDaoImpl implements UserDao{
      public void save(){
          System.out.println("user dao running...");
      }
  }
  ```

* Service业务层

  ```java
  public interface UserService {
      public void save();
  }
  ```

  ```java
  public class UserServiceImpl implements UserService {
  	private UserDao userDao;
      private int num;
      
      //1.对需要进行注入的变量添加set方法
      public void setUserDao(UserDao userDao) {
          this.userDao = userDao;
      }
      
  	public void setNum(int num) {
          this.num = num;
      }
      
      public void save() {
          System.out.println("user service running..." + num);
          userDao.save();
          bookDao.save();
      }
  }
  ```

* 配置applicationContext.xml

  ```xml
  <!--2.将要注入的资源声明为bean-->
  <bean id="userDao" class="dao.impl.UserDaoImpl"/>
  
  <bean id="userService" class="service.impl.UserServiceImpl">
  	<!--3.将要注入的引用类型的变量通过property属性进行注入，-->
      <property name="userDao" ref="userDao"/>
      <property name="num" value="666"/>
  </bean>
  ```

* 测试类

  ```java
  public class UserApp {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          UserService userService = (UserService) ctx.getBean("userService");
          userService.save();
      }
  }
  ```



***



##### 构造注入

标签：<constructor-arg>标签，<bean>的子标签

作用：使用构造方法的形式为bean提供资源，兼容早期遗留系统的升级工作

格式：

```xml
<bean>
	<constructor-arg />
    .....<!--一个bean可以有多个constructor-arg标签-->
</bean>
```

属性：

* name：对应bean中的构造方法所携带的参数名
* value：设定非引用类型构造方法参数对应的值，不能与ref同时使用
* ref：设定引用类型构造方法参数对应bean的id ，不能与value同时使用
* type ：设定构造方法参数的类型，用于按类型匹配参数或进行类型校验
* index ：设定构造方法参数的位置，用于按位置匹配参数，参数index值从0开始计数

```xml
<constructor-arg name="argsName" value="argsValue" />
<constructor-arg index="arg-index" type="arg-type" ref="beanId"/>
```

代码实现：

* DAO层：要注入的资源

  ```java
  public class UserDaoImpl implements UserDao{
      private String username;
      private String pwd;
      private String driver;
  
      public UserDaoImpl(String driver,String username, String pwd) {
          this.driver = driver;
          this.username = username;
          this.pwd = pwd;
      }
      public void save(){
          System.out.println("user dao running..."+username+" "+pwd+" "+driver);
      }
  }
  ```

* Service 业务层：参考 set 注入

* 配置 applicationContext.xml

  ```xml
  <bean id="userDao" class="dao.impl.UserDaoImpl">
      <!--使用构造方法进行注入，需要保障注入的属性与bean中定义的属性一致-->
  	<!--一致指顺序一致或类型一致或使用index解决该问题-->
      <constructor-arg index="2" value="123"/>
      <constructor-arg index="1" value="root"/>
      <constructor-arg index="0" value="com.mysql.jdbc.Driver"/>
  </bean>
  
  <bean id="userService" class="service.impl.UserServiceImpl">
      <property name="userDao" ref="userDao"/>
      <property name="num" value="666"/>
  </bean>
  ```

  方式二：使用 UserServiceImpl 的构造方法注入

  ```xml
  <bean id="userService" class="service.impl.UserServiceImpl">
  	<constructor-arg name="userDao" ref="userDao"/>
  	<constructor-arg name="num" value="666666"/>
  </bean>
  ```

* 测试类：参考 set 注入



***



##### 集合注入

标签：<array> <list> <set> <map> <props>，<property>或<constructor-arg>标签的子标签

作用：注入集合数据类型属性

格式：

```xml
<property>
	<list></list>
</property>
```

代码实现：

* DAO层：要注入的资源

  ```java
  public interface BookDao {
      public void save();
  }
  ```

  ```java
  public class BookDaoImpl implements BookDao {
      private ArrayList al;
      private Properties properties;
      private int[] arr;
      private HashSet hs;
      private HashMap hm ;
  
      public void setAl(ArrayList al) {
          this.al = al;
      }
  
      public void setProperties(Properties properties) {
          this.properties = properties;
      }
  
      public void setArr(int[] arr) {
          this.arr = arr;
      }
  
      public void setHs(HashSet hs) {
          this.hs = hs;
      }
  
      public void setHm(HashMap hm) {
          this.hm = hm;
      }
  
      public void save() {
          System.out.println("book dao running...");
          System.out.println("ArrayList:" + al);
          System.out.println("Properties:" + properties);
          for (int i = 0; i < arr.length; i++) {
              System.out.println(arr[i]);
          }
          System.out.println("HashSet:" + hs);
          System.out.println("HashMap:" + hm);
      }
  }
  ```

* Service业务层

  ```java
  public class UserServiceImpl implements UserService {
      private BookDao bookDao;
      
      public UserServiceImpl() {}
      
  	public void setBookDao(BookDao bookDao) {
          this.bookDao = bookDao;
      }
  
      public void save() {
          System.out.println("user service running..." + num);
          bookDao.save();
      }
  }
  ```

* 配置applicationContext.xml

  ```xml
  <bean id="userService" class="service.impl.UserServiceImpl">
      <property name="bookDao" ref="bookDao"/>
  </bean>
  
  <bean id="bookDao" class="dao.impl.BookDaoImpl">
      <property name="al">
          <list>
              <value>seazean</value>
              <value>66666</value>
          </list>
      </property>
      <property name="properties">
          <props>
              <prop key="name">seazean666</prop>
              <prop key="value">666666</prop>
          </props>
      </property>
      <property name="arr">
          <array>
              <value>123456</value>
              <value>66666</value>
          </array>
      </property>
      <property name="hs">
          <set>
              <value>seazean</value>
              <value>66666</value>
          </set>
      </property>
      <property name="hm">
          <map>
              <entry key="name" value="seazean66666"/>
              <entry key="value" value="6666666666"/>
          </map>
      </property>
  </bean>
  ```

  

****



#### P

标签：<p:propertyName>，<p:propertyName-ref>

作用：为 bean 注入属性值

格式：

```xml
<bean p:propertyName="propertyValue" p:propertyName-ref="beanId"/>
```

开启p命令空间：开启Spring对p命令空间的的支持，在beans标签中添加对应空间支持

```xml
<beans xmlns="http://www.springframework.org/schema/beans"   		
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
       xmlns:p="http://www.springframework.org/schema/p"       
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans     
		https://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

实例：

```xml
<bean 
      id="userService"
      class="service.impl.UserServiceImpl"
      p:userDao-ref="userDao"
      p:bookDao-ref="bookDao"
      p:num="10"
	/>
```



****



#### SpEL

Spring 提供了对 EL 表达式的支持，统一属性注入格式

作用：为 bean 注入属性值

格式：

```xml
<property value="EL">
```

注意：所有属性值不区分是否引用类型，统一使用value赋值

所有格式统一使用  value=“#{}”

- 常量  #{10}  #{3.14}  #{2e5}  #{‘it’}

- 引用bean  #{beanId}    

- 引用bean属性  #{beanId.propertyName}

- 引用bean方法  beanId.methodName().method2()

- 引用静态方法  T(java.lang.Math).PI

- 运算符支持  #{3 lt 4 == 4 ge 3}

- 正则表达式支持  #{user.name matches‘[a-z]{6,}’}

- 集合支持  #{likes[3]}

实例：

```xml
<bean id="userService" class="service.impl.UserServiceImpl">
        <property name="userDao" value="#{userDao}"/>
        <property name="bookDao" value="#{bookDao}"/>
        <property name="num" value="#{666666666}"/>
    </bean>
```



***



#### prop

Spring 提供了读取外部 properties 文件的机制，使用读取到的数据为 bean 的属性赋值

操作步骤：

1. 准备外部 properties 文件

2. 开启 context 命名空间支持

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd
           ">
   ```

3. 加载指定的 properties 文件

   ```xml
   <context:property-placeholder location="classpath:data.properties" />
   ```

4. 使用加载的数据

   ```xml
   <property name="propertyName" value="${propertiesName}"/>
   ```

- 注意：如果需要加载所有的 properties 文件，可以使用`*.properties`表示加载所有的 properties 文件

- 注意：读取数据使用**${propertiesName}**格式进行，其中 propertiesName 指 properties 文件中的属性名

代码实现：

* data.properties

  ```properties
  username=root
  pwd=123456
  ```

* DAO 层：注入的资源

  ```java
  public interface UserDao {
      public void save();
  }
  ```

  ```java
  public class UserDaoImpl implements UserDao{
      private String userName;
      private String password;
  
      public void setUserName(String userName) {
          this.userName = userName;
      }
      public void setPassword(String password) {
          this.password = password;
      }
  
      public void save(){
          System.out.println("user dao running..."+userName+" "+password);
      }
  }
  ```

* Service 业务层

  ```java
  public class UserServiceImpl implements UserService {
      private UserDao userDao;
      public void setUserDao(UserDao userDao) {
          this.userDao = userDao;
      }
      public void save() {
          System.out.println("user service running...");
          userDao.save();
      }
  }
  ```

* applicationContext.xml

  ```xml
  <context:property-placeholder location="classpath:*.properties"/>
  
  <bean id="userDao" class="dao.impl.UserDaoImpl">
      <property name="userName" value="${username}"/>
      <property name="password" value="${pwd}"/>
  </bean>
  
  <bean id="userService" class="service.impl.UserServiceImpl">
      <property name="userDao" ref="userDao"/>
  </bean>
  ```

* 测试类

  ```java
  public class UserApp {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          UserService userService = (UserService) ctx.getBean("userService");
          userService.save();
      }
  }
  ```

  

***



#### import

标签：<import>，<beans>标签的子标签

作用：在当前配置文件中导入其他配置文件中的项

格式：

```xml
<beans>
    <import />
</beans>
```

属性：

* resource：加载的配置文件名

```xml
<import resource=“config2.xml"/>
```

Spring容器加载多个配置文件：

* applicationContext-book.xml

  ```xml
  <bean id="bookDao" class="dao.impl.BookDaoImpl">
      <property name="num" value="1"/>
  </bean>
  ```

* applicationContext-user.xml

  ```xml
  <bean id="userDao" class="dao.impl.UserDaoImpl">
      <property name="userName" value="${username}"/>
      <property name="password" value="${pwd}"/>
  </bean>
  
  <bean id="userService" class="service.impl.UserServiceImpl">
      <property name="userDao" ref="userDao"/>
      <property name="bookDao" ref="bookDao"/>
  </bean>
  ```

* applicationContext.xml

  ```xml
  <import resource="applicationContext-user.xml"/>
  <import resource="applicationContext-book.xml"/>
  
  <bean id="bookDao" class="com.seazean.dao.impl.BookDaoImpl">
      <property name="num" value="2"/>
  </bean>
  ```

* 测试类

  ```java
  new ClassPathXmlApplicationContext("applicationContext-user.xml","applicationContext-book.xml");
  new ClassPathXmlApplicationContext("applicationContext.xml");
  ```

Spring 容器中的 bean 定义冲突问题

- 同 id 的 bean，后定义的覆盖先定义的

- 导入配置文件可以理解为将导入的配置文件复制粘贴到对应位置，程序执行选择最下面的配置使用

- 导入配置文件的顺序与位置不同可能会导致最终程序运行结果不同



***



#### 三方资源

##### Druid

第三方资源配置

* pom.xml

  ```xml
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
  </dependency>
  ```

* applicationContext.xml

  ```xml
  <!--加载druid资源-->
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
      <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
      <property name="url" value="jdbc:mysql://192.168.2.185:3306/spring_db"/>
      <property name="username" value="root"/>
      <property name="password" value="123456"/>
  </bean>
  ```
  
* App.java

  ```java
  ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
  DruidDataSource datasource = (DruidDataSource) ctx.getBean("datasource");
  System.out.println(datasource);
  ```



****



##### Mybatis

Mybatis 核心配置文件消失

- 环境 environment 转换成数据源对象

- 映射 Mapper 扫描工作交由 spring 处理

- 类型别名交由 spring 处理

业务发起使用spring上下文对象获取对应的bean

**原理**：DAO 接口不需要创建实现类，MyBatis-Spring 提供了一个动态代理的实现 **MapperFactoryBean**，这个类可以让直接注入数据映射器接口到 service 层 bean 中，底层将会动态代理创建类

* pom.xml，导入坐标

  ```xml
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.3</version>
  </dependency>
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
  </dependency>
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
  </dependency>
  
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
  </dependency>
  
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.1.9.RELEASE</version>
  </dependency>
  ```

* jdbc.properties 连接mysql

  ```properties
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://192.168.2.185:3306/spring_db
  jdbc.username=root
  jdbc.password=123456
  ```

* AccountDao.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="dao.AccountDao">
      <!--配置根据id查询-->
      <select id="findById" resultType="account" parameterType="int">
          SELECT * FROM account WHERE id = #{id}
      </select>
  </mapper>
  ```

* ApplicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          ">
      <!--加载properties配置文件-->
      <context:property-placeholder location="classpath:jdbc.properties"/>
  
      <!--加载druid资源-->
      <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
          <property name="driverClassName" value="${jdbc.driver}"/>
          <property name="url" value="${jdbc.url}"/>
          <property name="username" value="${jdbc.username}"/>
          <property name="password" value="${jdbc.password}"/>
      </bean>
  
      <!--配置service作为spring的bean,注入dao-->
      <bean id="accountService" class="service.impl.AccountServiceImpl">
          <property name="accountDao" ref="accountDao"/>
      </bean>
  
      <!--spring整合mybatis后控制的创建连接的对象,domain下包括所有引用类-->
      <bean class="org.mybatis.spring.SqlSessionFactoryBean">
          <property name="dataSource" ref="dataSource"/>
          <property name="typeAliasesPackage" value="domain"/>
      </bean>
  
      <!--加载mybatis映射配置的扫描，将其作为spring的bean进行管理-->
      <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
          <property name="basePackage" value="dao"/>
      </bean>
  </beans>
  ```

* DAO层

  ```java
  public interface AccountDao {
  	Account findById(Integer id);
  }
  ```

* Service层

  ```java
  public interface AccountService {
      Account findById(Integer id);
  }
  ```

  ```java
  public class AccountServiceImpl implements AccountService {
      private AccountDao accountDao;
  	//set注入
      public void setAccountDao(AccountDao accountDao) {
          this.accountDao = accountDao;
      }
  
      @Override
      public Account findById(Integer id) {
          return accountDao.findById(id);
      }
  }
  
  ```

* 测试类

  ```java
  public class App {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          AccountService accountService = (AccountService) ctx.getBean("accountService");
          Account ac = accountService.findById(1);
          System.out.println(ac);
  	}
  }
  ```

  

***



### 注解开发

#### 注解驱动

注解：启动时使用注解的形式替代 xml 配置，将 spring 配置文件从工程中消除，简化书写

缺点：为了达成注解驱动的目的，可能会将原先很简单的书写，变的更加复杂。XML中配置第三方开发的资源是很方便的，但使用注解驱动无法在第三方开发的资源中进行编辑，因此会增大开发工作量

![](https://gitee.com/seazean/images/raw/master/Frame/注解驱动示例.png)



****



#### 启动注解

##### XML

启动注解扫描，加载类中配置的注解项：

```xml
<context:component-scan base-package="packageName1,packageName2"/>
```

说明：

- 在进行包扫描时，会对配置的包及其子包中所有文件进行扫描，多个包采用`,`隔开

- 扫描过程是以文件夹递归迭代的形式进行的

- 扫描过程仅读取合法的 java 文件

- 扫描时仅读取 spring 可识别的注解

- 扫描结束后会将可识别的有效注解转化为 spring 对应的资源加入 IoC 容器

注意：

- 无论是注解格式还是 XML 配置格式，最终都是将资源加载到 IoC 容器中，差别仅仅是数据读取方式不同
- 从加载效率上来说注解优于 XML 配置文件



***



##### 纯注解

注解配置类

名称：@Configuration、@ComponentScan

类型：类注解

作用：**设置当前类为 Spring 核心配置加载类**

格式：

```java
@Configuration
@ComponentScan({"scanPackageName1","scanPackageName2"})
public class SpringConfigClassName{
}
```

说明：

- 核心配合类用于替换 Spring 核心配置文件，此类可以设置空的，不设置变量与属性
- bean 扫描工作使用注解 @ComponentScan 替代，多个包用`{}和,`隔开

加载纯注解格式上下文对象，需要使用**AnnotationConfigApplicationContext**

```java
@Configuration
public class SpringConfig {
    @Bean
    public Person person() {
        return new Person1("lisi", 20);
    }
}

public class MainTest {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new 
            		AnnotationConfigApplicationContext(SpringConfig.class);
        //方式一：名称对应类名
        Person bean = applicationContext.getBean(Person.class);
        System.out.println(bean);
		
        //方式二：名称对应方法名 
        Person bean1 = (Person) applicationContext.getBean("person1");	
        
        //方法三：指定名称@Bean("person2")
    }
}
```



***



##### 扫描器

组件扫描过滤器

开发过程中，需要根据需求加载必要的bean，排除指定bean

![](https://gitee.com/seazean/images/raw/master/Frame/组件扫描器.png)



名称：@ComponentScan

类型：**类注解**

作用：设置 Spring 配置加载类扫描规则

格式：

```java
@ComponentScan(
    value = {"dao","service"},			//设置基础扫描路径
    excludeFilters =					//设置过滤规则，当前为排除过滤
	@ComponentScan.Filter(				//设置过滤器
	    type= FilterType.ANNOTATION,  	//设置过滤方式为按照注解进行过滤
	    classes = Service.class)     	//设置具体的过滤项，过滤所有@Service修饰的bean
    )
)
```

属性：

* includeFilters：设置包含性过滤器 
* excludeFilters：设置排除性过滤器
* type：设置过滤器类型





***



#### 基本注解

##### 设置bean

名称：@Component、@Controller、@Service、@Repository

类型：类注解，写在类定义上方

作用：设置该类为Spring 管理的 bean

格式：

```java
@Component
public class ClassName{}
```

说明：@Controller、@Service 、@Repository 是 @Component 的衍生注解，功能同 @Component

属性：

- value（默认）：定义 bean 的访问 id



***



##### 作用范围

名称：@Scope

类型：类注解，写在类定义上方

作用：设置该类作为 bean 对应的 scope 属性

格式：

```java
@Scope
public class ClassName{}
```

相关属性

- value（默认）：定义 bean 的作用域，默认为 singleton，非单例取值 prototype



***



##### 生命周期

名称：@PostConstruct、@PreDestroy

类型：方法注解，写在方法定义上方

作用：设置该类作为 bean 对应的生命周期方法

示例：

```java
//定义bean，后面添加bean的id
@Component("userService")
//定义bean的作用域
@Scope("singleton")
public class UserServiceImpl implements UserService {
    //初始化
    @PostConstruct
    public void init(){
        System.out.println("user service init...");
    }
	//销毁
    @PreDestroy
    public void destroy(){
        System.out.println("user service destroy...");
    }
}
```



***



##### 加载资源

名称：@Bean

类型：方法注解

作用：设置该方法的返回值作为 Spring 管理的 bean

格式：

```java
@Bean("dataSource")
public DruidDataSource createDataSource() {    return ……;    }
```

说明：

- 因为第三方 bean 无法在其源码上进行修改，使用 @Bean 解决第三方 bean 的引入问题

- 该注解用于替代 XML 配置中的静态工厂与实例工厂创建 bean，不区分方法是否为静态或非静态

- @Bean 所在的类必须被 Spring 扫描加载，否则该注解无法生效

相关属性

- value（默认）：定义 bean 的访问 id
- initMethod：声明初始化方法
- destroyMethod：声明销毁方法



***



#### 属性注入

##### 基本类型

名称：@Value

类型：属性注解、方法注解

作用：设置对应属性的值或对方法进行传参

格式：

```java
//@Value("${jdbc.username}")
@Value("root")
private String username;
```

说明：

- value 值仅支持非引用类型数据，赋值时对方法的所有参数全部赋值

- value 值支持读取 properties 文件中的属性值，通过类属性将 properties 中数据传入类中

- value 值支持 SpEL

- @value 注解如果添加在属性上方，可以省略 set 方法（set 方法的目的是为属性赋值）

相关属性：

- value（默认）：定义对应的属性值或参数值



****



##### 属性填充

名称：@Autowired、@Qualifier

类型：属性注解、方法注解

作用：设置对应属性的对象、对方法进行引用类型传参

格式：

```java
@Autowired(required = false)
@Qualifier("userDao")
private UserDao userDao;
```

说明：

- @Autowired 默认按类型装配，指定 @Qualifier 后可以指定自动装配的 bean 的 id

相关属性：

- required：为 true （默认）表示注入 bean 时该 bean 必须存在，不然就会注入失败；为 false  表示注入时该 bean 存在就注入，不存在就忽略跳过

注意：在使用 @Autowired 时，首先在容器中查询对应类型的 bean，如果查询结果刚好为一个，就将该 bean 装配给 @Autowired 指定的数据，如果查询的结果不止一个，那么 @Autowired 会根据名称来查找。如果查询的结果为空，那么会抛出异常。解决方法：使用 required = false



***



##### 属性设置

名称：@Primary

类型：类注解

作用：设置类对应的 bean 按类型装配时优先装配

范例：

```java
@Primary
public class ClassName{}
```

说明：

- @Autowired 默认按类型装配，当出现相同类型的 bean，使用 @Primary 提高按类型自动装配的优先级，多个 @Primary 会导致优先级设置无效



***



##### 注解对比

名称：@Inject、@Named、@Resource

- @Inject 与 @Named 是 JSR330 规范中的注解，功能与 @Autowired 和 @Qualifier 完全相同，适用于不同架构场景
- @Resource 是 JSR250 规范中的注解，可以简化书写格式

@Resource相关属性

- name：设置注入的 bean 的 id

- type：设置注入的 bean 的类型，接收的参数为 Class 类型

**@Autowired和@Resource之间的区别**：

*  @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）

*  @Resource默认按照名称来装配注入，只有当找不到与名称匹配的bean才会按照类型来装配注入



***



#### 文件读取

名称：@PropertySource

类型：类注解

作用：加载properties文件中的属性值

格式：

```java
@PropertySource(value = "classpath:filename.properties")
public class ClassName {
    @Value("${propertiesAttributeName}")
    private String attributeName;
}
```

说明：

- 不支持 * 通配符，加载后，所有spring控制的bean中均可使用对应属性值，加载多个需要用`{}和,`隔开

相关属性

- value（默认）：设置加载的properties文件名

- ignoreResourceNotFound：如果资源未找到，是否忽略，默认为false





***



#### 加载控制

##### 依赖加载

@DependsOn

- 名称：@DependsOn

- 类型：类注解、方法注解

- 作用：控制 bean 的加载顺序，使其在指定 bean 加载完毕后再加载

- 格式：

  ```java
  @DependsOn("beanId")
  public class ClassName {
  }
  ```

- 说明：

  - 配置在方法上，使 @DependsOn 指定的 bean 优先于 @Bean 配置的 bean 进行加载

  - 配置在类上，使 @DependsOn 指定的 bean 优先于当前类中所有 @Bean 配置的 bean 进行加载

  - 配置在类上，使 @DependsOn 指定的 bean 优先于 @Component 等配置的bean进行加载

- 相关属性

  - value（默认）：设置当前 bean 所依赖的 bean 的 id

@Order

- 名称：@Order

- 类型：**配置类注解**

- 作用：控制配置类的加载顺序，值越小越先加载

- 格式：

  ```java
  @Order(1)
  public class SpringConfigClassName {
  }
  ```

@Lazy

- 名称：@Lazy

- 类型：类注解、方法注解

- 作用：控制bean的加载时机，使其延迟加载，获取的时候加载

- 格式：

  ```java
  @Lazy
  public class ClassName {
  }
  ```



****



##### 应用场景

@DependsOn

- 微信订阅号，发布消息和订阅消息的bean的加载顺序控制（先开订阅，再发布）

- 双11活动期间，零点前是结算策略A，零点后是结算策略B，策略B操作的数据为促销数据。策略B加载顺序与促销数据的加载顺序

@Lazy

- 程序灾难出现后对应的应急预案处理是启动容器时加载时机

@Order

- 多个种类的配置出现后，优先加载系统级的，然后加载业务级的，避免细粒度的加载控制





****



#### 整合资源

##### 导入资源

名称：@Import

类型：类注解

作用：导入第三方bean作为spring控制的资源，这些类都会被Spring创建并放入ioc容器

格式：

```java
@Configuration
@Import(OtherClassName.class)
public class ClassName {
}
```

说明：

- @Import注解在同一个类上，仅允许添加一次，如果需要导入多个，使用数组的形式进行设定
- 在被导入的类中可以继续使用@Import导入其他资源（了解）
- @Bean所在的类可以使用导入的形式进入spring容器，无需声明为bean



##### Druid

* 加载资源

  ```java
  @Component
  public class JDBCConfig {
      @Bean("dataSource")
      public static DruidDataSource getDataSource() {
          DruidDataSource ds = new DruidDataSource();
          ds.setDriverClassName("com.mysql.jdbc.Driver");
          ds.setUrl("jdbc:mysql://192.168.2.185:3306/spring_db");
          ds.setUsername("root");
          ds.setPassword("123456");
          return ds;
      }
  }
  ```

* 导入资源

  ```java
  @Configuration
  @ComponentScan(value = {"service","dao"})
  @Import(JDBCConfig.class)
  public class SpringConfig {
  }
  ```

* 测试

  ```java
  DruidDataSource dataSource = (DruidDataSource) ctx.getBean("dataSource");
  System.out.println(dataSource);
  ```

  

***



##### Junit

Spring 接管 Junit 的运行权，使用 Spring 专用的 Junit 类加载器，为 Junit 测试用例设定对应的 Spring 容器

注意：

- 从 Spring5.0 以后，要求 Junit 的版本必须是4.12及以上

- Junit 仅用于单元测试，不能将 Junit 的测试类配置成 Spring 的 bean，否则该配置将会被打包进入工程中 

test / java / service / UserServiceTest

```java
//设定spring专用的类加载器
@RunWith(SpringJUnit4ClassRunner.class)
//设定加载的spring上下文对应的配置
@ContextConfiguration(classes = SpringConfig.class)
public class UserServiceTest {
    @Autowired
    private AccountService accountService;
    @Test
    public void testFindById() {
        Account account = accountService.findById(1);
        Assert.assertEquals("Mike", account.getName());
    }
}
```

pom.xml

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.1.9.RELEASE</version>
</dependency>
```





****



### IoC原理

#### 核心类

##### BeanFactory

ApplicationContext：

1. ApplicationContext 是一个接口，提供了访问 Spring 容器的 API

2. ClassPathXmlApplicationContext 是一个类，实现了上述功能

3. ApplicationContext 的顶层接口是 BeanFactory

4. BeanFactory 定义了 bean 相关的最基本操作

5. ApplicationContext 在 BeanFactory 基础上追加了若干新功能

**ApplicationContext 和 BeanFactory对比：**

* BeanFactory 和 ApplicationContext 是 Spring 的两大核心接口，都可以当做 Spring 的容器
* BeanFactory 是 Spring 里面最底层的接口，是 IoC 的核心，定义了 IoC 的基本功能，包含了各种 Bean 的定义、加载、实例化，依赖注入和生命周期管理。ApplicationContext 接口作为 BeanFactory 的子类，除了提供 BeanFactory 所具有的功能外，还提供了更完整的框架功能：

  * 继承 MessageSource，因此支持国际化
  * 资源文件访问，如 URL 和文件（ResourceLoader）。
  * 载入多个（有继承关系）上下文（即加载多个配置文件） ，使得每一个上下文都专注于一个特定的层次，比如应用的 web 层
  * 提供在监听器中注册 bean 的事件
* BeanFactory 创建的 bean 采用延迟加载形式，只有在使用到某个 Bean 时（调用 getBean），才对该 Bean 进行加载实例化（Spring 早期使用该方法获取 bean），这样就不能提前发现一些存在的 Spring 的配置问题；ApplicationContext 是在容器启动时，一次性创建了所有的 Bean，容器启动时，就可以发现 Spring 中存在的配置错误，这样有利于检查所依赖属性是否注入
* ApplicationContext 启动后预载入所有的单实例 Bean，所以程序启动慢，运行时速度快
* 两者都支持 BeanPostProcessor、BeanFactoryPostProcessor 的使用，但两者之间的区别是：BeanFactory 需要手动注册，而 ApplicationContext 则是自动注册

FileSystemXmlApplicationContext：加载文件系统中任意位置的配置文件，而 ClassPathXmlAC 只能加载类路径下的配置文件

![](https://gitee.com/seazean/images/raw/master/Frame/ApplicationContext层级结构图.png)

BeanFactory 的成员属性：

```java
String FACTORY_BEAN_PREFIX = "&";
```

* 区分是 FactoryBean 还是创建的 Bean，加上 & 代表是工厂，getBean 将会返回工厂
* FactoryBean：如果某个 bean 的配置非常复杂，或者想要使用编码的形式去构建它，可以提供一个构建该 bean 实例的工厂，这个工厂就是 FactoryBean 接口实现类，FactoryBean 接口实现类也是需要 Spring 管理
  * 这里产生两种对象，一种是 FactoryBean 接口实现类（IOC 管理），另一种是 FactoryBean 接口内部管理的对象
  * 获取 FactoryBean 接口实现类，使用 getBean 时传的 beanName 需要带 & 开头
  * 获取 FactoryBean 内部管理的对象，不需要带 & 开头

BeanFactory 的基本使用：

```java
Resource res = new ClassPathResource("applicationContext.xml");
BeanFactory bf = new XmlBeanFactory(res);
UserService userService = (UserService)bf.getBean("userService");
```



****



##### FactoryBean

繁琐的 bean 初始化过程处理：

* FactoryBean：对单一的 bean 的初始化过程进行封装，达到简化配置的目的

FactoryBean与 BeanFactory 区别：

- FactoryBean：封装单个 bean 的创建过程，就是工厂的 Bean

- BeanFactory：Spring 容器顶层接口，定义了 bean 相关的获取操作

代码实现：

* FactoryBean，实现类一般是 MapperFactoryBean，创建 DAO 层接口的实现类

  ```java
  public class EquipmentDaoImplFactoryBean implements FactoryBean {
      @Override	//获取Bean
      public Object getObject() throws Exception {
          return new EquipmentDaoImpl();
      }
      
      @Override	//获取bean的类型
      public Class<?> getObjectType() {
          return null;
      }
      
      @Override	//是否单例
      public boolean isSingleton() {
          return false;
      }
  }
  ```






***



#### 过滤器

##### 数据准备

* DAO 层 UserDao、AccountDao、BookDao、EquipmentDao

  ```java
  public interface UserDao {
  	public void save();
  }
  ```

  ```java
  @Component("userDao")
  public class UserDaoImpl implements UserDao {
      public void save() {
          System.out.println("user dao running...");
      }
  
  }
  ```

* Service 业务层

  ```java
  public interface UserService {
      public void save();
  }
  ```

  ```java
  @Service("userService")
  public class UserServiceImpl implements UserService {
      @Autowired
      private UserDao userDao;//...........BookDao等
  
      public void save() {
          System.out.println("user service running...");
          userDao.save();
      }
  }
  ```
  



***



##### 过滤器

名称：TypeFilter

类型：**接口**

作用：自定义类型过滤器

示例：

* config / filter / MyTypeFilter

  ```java
  public class MyTypeFilter implements TypeFilter {
      @Override
      /**
      * metadataReader:读取到的当前正在扫描的类的信息
      * metadataReaderFactory:可以获取到任何其他类的信息
      */
      //加载的类满足要求，匹配成功
      public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
          //获取当前类注解的信息
  		AnnotationMetadata am = metadataReader.getAnnotationMetadata();
  		//获取当前正在扫描的类的类信息
  		ClassMetadata classMetadata = metadataReader.getClassMetadata();
  		//获取当前类资源（类的路径）
  		Resource resource = metadataReader.getResource();
          
          
          //通过类的元数据获取类的名称
          String className = classMetadata.getClassName();
          //如果加载的类名满足过滤器要求，返回匹配成功
          if(className.equals("service.impl.UserServiceImpl")){
         	//返回true表示匹配成功，返回false表示匹配失败。此处仅确认匹配结果，不会确认是排除还是加入，排除/加入由配置项决定，与此处无关
              return true;
          }
          return false;
      }
  }
  ```

* SpringConfig

  ```java
  @Configuration
  //设置排除bean，排除的规则是自定义规则（FilterType.CUSTOM），具体的规则定义为MyTypeFilter
  @ComponentScan(
          value = {"dao","service"},
          excludeFilters = @ComponentScan.Filter(
                  type= FilterType.CUSTOM,
                  classes = MyTypeFilter.class
          )
  )
  public class SpringConfig {
  }
  ```




***



#### 导入器

bean 只有通过配置才可以进入 Spring 容器，被 Spring 加载并控制

- 配置 bean 的方式如下：

  - XML 文件中使用 <bean/> 标签配置
  - 使用 @Component 及衍生注解配置

导入器可以快速高效导入大量 bean，替代 @Import({a.class,b.class})，无需在每个类上添加 @Bean

名称： ImportSelector

类型：**接口**

作用：自定义bean导入器

* selector / MyImportSelector

  ```java
  public class MyImportSelector implements ImportSelector{
      @Override
      public String[] selectImports(AnnotationMetadata importingClassMetadata) {
  //      1.编程形式加载一个类
  //      return new String[]{"dao.impl.BookDaoImpl"};
  
  //      2.加载import.properties文件中的单个类名
  //      ResourceBundle bundle = ResourceBundle.getBundle("import");
  //      String className = bundle.getString("className");
  
  //      3.加载import.properties文件中的多个类名
          ResourceBundle bundle = ResourceBundle.getBundle("import");
          String className = bundle.getString("className");
          return className.split(",");
      }
  }
  ```

* import.properties

  ```properties
  #2.加载import.properties文件中的单个类名
  #className=dao.impl.BookDaoImpl
  
  #3.加载import.properties文件中的多个类名
  #className=dao.impl.BookDaoImpl,dao.impl.AccountDaoImpl
  
  #4.导入包中的所有类
  path=dao.impl.*
  ```

* SpringConfig

  ```java
  @Configuration
  @ComponentScan({"dao","service"})
  @Import(MyImportSelector.class)
  public class SpringConfig {
  }
  ```



***



#### 注册器

可以取代 ComponentScan 扫描器

名称：ImportBeanDefinitionRegistrar

类型：**接口**

作用：自定义 bean 定义注册器

* registrar / MyImportBeanDefinitionRegistrar

  ```java
  public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
  /**
   * AnnotationMetadata:当前类的注解信息
   * BeanDefinitionRegistry:BeanDefinition注册类，把所有需要添加到容器中的bean调用registerBeanDefinition手工注册进来
   */
      @Override
      public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
          //自定义注册器
          //1.开启类路径bean定义扫描器，需要参数bean定义注册器BeanDefinitionRegistry，需要制定是否使用默认类型过滤器
          ClassPathBeanDefinitionScanner scanner = new ClassPathBeanDefinitionScanner(registry,false);
          //2.添加包含性加载类型过滤器（可选，也可以设置为排除性加载类型过滤器）
          scanner.addIncludeFilter(new TypeFilter() {
              @Override
              public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
                  //所有匹配全部成功，此处应该添加实际的业务判定条件
                  return true;
              }
          });
          //设置扫描路径
          scanner.addExcludeFilter(tf);//排除
          scanner.scan("dao","service");
      }
  }
  ```

* SpringConfig

  ```java
  @Configuration
  @Import(MyImportBeanDefinitionRegistrar.class)
  public class SpringConfig {
  }
  ```

  



***



#### 处理器

通过创建类**继承相应的处理器的接口**，重写后置处理的方法，来实现**拦截 Bean** 的生命周期来实现自己自定义的逻辑

BeanPostProcessor：bean 后置处理器，bean 创建对象初始化前后进行拦截工作的

BeanFactoryPostProcessor：beanFactory 的后置处理器

 * 		加载时机：在 BeanFactory 初始化之后调用，来定制和修改 BeanFactory 的内容；所有的 bean 定义已经保存加载到 beanFactory，但是 bean 的实例还未创建

* 		执行流程：
    * ioc 容器创建对象
    * invokeBeanFactoryPostProcessors(beanFactory)：执行 BeanFactoryPostProcessor
      * 在 BeanFactory 中找到所有类型是 BeanFactoryPostProcessor 的组件，并执行它们的方法
      * 在初始化创建其他组件前面执行

BeanDefinitionRegistryPostProcessor：

* 加载时机：在所有 bean 定义信息将要被加载，但是 bean 实例还未创建，优先于 BeanFactoryPostProcessor 执行；利用 BeanDefinitionRegistryPostProcessor 给容器中再额外添加一些组件

* 执行流程：
  * ioc 容器创建对象
  * refresh() → invokeBeanFactoryPostProcessors(beanFactory)
  * 从容器中获取到所有的 BeanDefinitionRegistryPostProcessor 组件
    * 依次触发所有的 postProcessBeanDefinitionRegistry() 方法
    * 再来触发 postProcessBeanFactory() 方法



*****



#### 监听器

##### 基本概述

ApplicationListener：监听容器中发布的事件，完成事件驱动模型开发

```java
public interface ApplicationListener<E extends ApplicationEvent>
```

所以监听 ApplicationEvent 及其下面的子事件

应用监听器步骤：

 * 	  写一个监听器（ApplicationListener实现类）来监听某个事件（ApplicationEvent及其子类）
 * 	  把监听器加入到容器@Component
 * 	  只要容器中有相关事件的发布，我们就能监听到这个事件；
       * 	  ContextRefreshedEvent：容器刷新完成（所有bean都完全创建）会发布这个事件
       * 	  ContextClosedEvent：关闭容器会发布这个事件
 * 	  发布一个事件：`applicationContext.publishEvent()`

```java
@Component
public class MyApplicationListener implements ApplicationListener<ApplicationEvent> {
	//当容器中发布此事件以后，方法触发
	@Override
	public void onApplicationEvent(ApplicationEvent event) {
		// TODO Auto-generated method stub
		System.out.println("收到事件："+event);
	}
}
```



****



##### 实现原理

1. **ContextRefreshedEvent事件**：

 * 容器创建对象：refresh()

 * 容器刷新完成：finishRefresh() --> publishEvent(new ContextRefreshedEvent(this))

   发布ContextRefreshedEvent事件：

   * 获取事件的多播器（派发器）：getApplicationEventMulticaster()

     容器初始化过程中执行`initApplicationEventMulticaster()`：初始化事件多播器

     * 先去容器中查询`id=applicationEventMulticaster`的组件，有直接返回
     * 如果没有就执行`this.applicationEventMulticaster = new SimpleApplicationEventMulticaster(beanFactory);`并且加入到容器中，
     * 以后在其他组件要派发事件，自动注入这个applicationEventMulticaster

   * multicastEvent派发事件

   * 获取到所有的ApplicationListener

     容器初始化过程执行**registerListeners()**注册监听器

     * 从容器中获取所有监听器：`getBeanNamesForType(ApplicationListener.class, true, false)`
     * 将listener注册到ApplicationEventMulticaster：`getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName)`

   * 遍历 ApplicationListener

     * 如果有 Executor，可以使用 Executor 异步派发；Executor executor = getTaskExecutor()
     * 反之，同步的方式直接执行listener方法；invokeListener(listener, event)，拿到listener回调onApplicationEvent方法

2. 自己发布事件

3. 容器关闭会发布 ContextClosedEvent



***



##### 注解实现

注解：@EventListener

基本使用：

```java
@Service
public class UserService{
    @EventListener(classes={ApplicationEvent.class})
	public void listen(ApplicationEvent event){
		System.out.println("UserService。。监听到的事件："+event);
	}
}
```

原理：使用 EventListenerMethodProcessor 处理器来解析方法上的 @EventListener，Spring 扫描使用注解的方法，并为之创建一个监听对象

SmartInitializingSingleton 原理：→ afterSingletonsInstantiated()

 *   		ioc 容器创建对象并 refresh()
 *   		finishBeanFactoryInitialization(beanFactory)：初始化剩下的单实例 bean
        *   		先创建所有的单实例 bean：getBean()
        *   		获取所有创建好的单实例 bean，判断是否是 SmartInitializingSingleton 类型的，如果是就调用 afterSingletonsInstantiated()





***



## AOP

### 基本概述

AOP（Aspect Oriented Programing）：面向切面编程，一种编程**范式**，指导开发者如何组织程序结构

AOP 弥补了 OOP 的不足，基于 OOP 基础之上进行横向开发：

- uOOP 规定程序开发以类为主体模型，一切围绕对象进行，完成某个任务先构建模型

- uAOP 程序开发主要关注基于 OOP 开发中的共性功能，一切围绕共性功能进行，完成某个任务先构建可能遇到的所有共性功能（当所有功能都开发出来也就没有共性与非共性之分），将软件开发由手工制作走向半自动化/全自动化阶段，实现“插拔式组件体系结构”搭建

AOP作用：

* 提高代码的可重用性
* 业务代码编码更简洁

* 业务代码维护更高效

* 业务功能扩展更便捷

AOP开发思想：

![](https://gitee.com/seazean/images/raw/master/Frame/AOP开发思想.png)



***



### 核心概念

#### 概念详解

- Joinpoint（连接点）：就是方法

- Pointcut（切入点）：就是挖掉共性功能的方法

- Advice（通知）：就是共性功能，最终以一个方法的形式呈现

- Aspect（切面）：就是共性功能与挖的位置的对应关系

- Target（目标对象）：就是挖掉功能的方法对应的类产生的对象，这种对象是无法直接完成最终工作的

- Weaving（织入）：就是将挖掉的功能回填的动态过程

- Proxy（代理）：目标对象无法直接完成工作，需要对其进行功能回填，通过创建原始对象的代理对象实现

- Introduction（引入/引介）：就是对原始对象无中生有的添加成员变量或成员方法

![](https://gitee.com/seazean/images/raw/master/Frame/AOP连接点.png)

![](https://gitee.com/seazean/images/raw/master/Frame/AOP切入点切面通知.png)

![](https://gitee.com/seazean/images/raw/master/Frame/AOP织入.png)



***



#### 入门项目

开发步骤：

- 开发阶段

  - 制作程序

  - 将非共性功能开发到对应的目标对象类中，并制作成切入点方法

  - 将共性功能独立开发出来，制作成通知

  - 在配置文件中，声明切入点

  - 在配置文件中，声明切入点与通知间的关系（含通知类型），即切面

- 运行阶段（AOP 完成）

  - Spring 容器加载配置文件，监控所有配置的**切入点**方法的执行

  - 当监控到切入点方法被运行，**使用代理机制，动态创建目标对象的代理对象，根据通知类别，在代理对象的对应位置将通知对应的功能织入**，完成完整的代码逻辑并运行

1. 导入坐标 pom.xml

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>5.1.9.RELEASE</version>
   </dependency>
   <dependency>
       <groupId>org.aspectj</groupId>
       <artifactId>aspectjweaver</artifactId>
       <version>1.9.4</version>
   </dependency>
   ```

2. 业务层抽取通用代码  service / UserServiceImpl

   ```java
   public interface UserService {
       public void save();
   }
   ```

   ```java
   public class UserServiceImpl implements UserService {
       @Override
       public void save() {
           //System.out.println("共性功能");
           System.out.println("user service running...");
       }
   }
   ```

   aop.AOPAdvice

   ```java
   //1.制作通知类，在类中定义一个方法用于完成共性功能
   public class AOPAdvice {
       //共性功能抽取后职称独立的方法
       public void function(){
           System.out.println("共性功能");
       }
   }
   ```

3. 把通知加入spring容器管理，配置aop  applicationContext.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/aop
           https://www.springframework.org/schema/aop/spring-aop.xsd
           ">
       <!--原始Spring控制资源-->
       <bean id="userService" class= "service.impl.UserServiceImpl"/>
       <!--2.配置共性功能成功spring控制的资源-->
       <bean id="myAdvice" class="aop.AOPAdvice"/>
       <!--3.开启AOP命名空间: beans标签内-->
       <!--4.配置AOP-->
       <aop:config>
           <!--5.配置切入点-->
           <aop:pointcut id="pt" expression="execution(* *..*(..))"/>
           <!--6.配置切面（切入点与通知的关系）-->
           <aop:aspect ref="myAdvice">
               <!--7.配置具体的切入点对应通知中那个操作方法-->
               <aop:before method="function" pointcut-ref="pt"/>
           </aop:aspect>
       </aop:config>
   </beans>
   ```

4. 测试类

   ```java
   public class App {
       public static void main(String[] args) {
           ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
           UserService userService = (UserService) ctx.getBean("userService");
           userService.save();//先输出共性功能，然后 user service running...
       }
   }
   ```



***



### XML开发

#### AspectJ

Aspect（切面）用于描述切入点与通知间的关系，是 AOP 编程中的一个概念

AspectJ 是基于 java 语言对 Aspect 的实现



***



#### AOP

##### config

标签：<aop:config>，<beans>的子标签

作用：设置 AOP

格式：

```xml
<beans>
    <aop:config>……</aop:config>
    <aop:config>……</aop:config>
    <!--一个beans标签中可以配置多个aop:config标签-->
</beans>
```



***



##### pointcut

标签：<aop:pointcut>，归属于 aop:config 标签和 aop:aspect 标签

作用：设置切入点

格式：

```xml
<aop:config>
    <aop:pointcut id="pointcutId" expression="……"/>
    <aop:aspect>
        <aop:pointcut id="pointcutId" expression="……"/>
    </aop:aspect>
</aop:config>
```

说明：

* 一个 aop:config 标签中可以配置多个 aop:pointcut 标签，且该标签可以配置在 aop:aspect 标签内

属性：

- id ：识别切入点的名称

- expression ：切入点表达式



***



##### aspect

标签：<aop:aspect>，aop:config 的子标签

作用：设置具体的 AOP 通知对应的切入点（切面）

格式：

```xml
<aop:config>
    <aop:aspect ref="beanId">……</aop:aspect>
    <aop:aspect ref="beanId">……</aop:aspect>
    <!--一个aop:config标签中可以配置多个aop:aspect标签-->
</aop:config>
```

属性：

- ref ：通知所在的 bean 的 id



***



#### Pointcut

##### 切入点

切入点描述的是某个方法

切入点表达式是一个快速匹配方法描述的通配格式，类似于正则表达式



***



##### 表达式

格式：

```xml
关键字(访问修饰符  返回值  包名.类名.方法名(参数)异常名)
```

示例：

```java
//匹配UserService中只含有一个参数的findById方法
execution(public User service.UserService.findById(int))
```

格式解析：

* 关键字：描述表达式的匹配模式（参看关键字列表）
* 访问修饰符：方法的访问控制权限修饰符
* 类名：方法所在的类（此处可以配置接口名称）
* 异常：方法定义中指定抛出的异常

关键字：

- execution ：匹配执行指定方法

- args ：匹配带有指定参数类型的方法

- within、this、target、@within、@target、@args、@annotation、bean、reference pointcut等


通配符：

* *：单个独立的任意符号，可以独立出现，也可以作为前缀或者后缀的匹配符出现

  ```java
  //匹配com.seazean包下的任意包中的UserService类或接口中所有find开头的带有一个任意参数的方法
  execution(public * com.seazean.*.UserService.find*(*)
  ```

* .. ：多个连续的任意符号，可以独立出现，常用于简化包名与参数

  ```java
  //匹配com包下的任意包中的UserService类或接口中所有名称为findById参数任意数量和类型的方法
  execution(public User com..UserService.findById(..))
  ```

* +：专用于匹配子类类型

  ```java
  //匹配任意包下的Service结尾的类或者接口的子类或者实现类
  execution(* *..*Service+.*(..))
  ```

逻辑运算符：

- &&：连接两个切入点表达式，表示两个切入点表达式同时成立的匹配
- ||：连接两个切入点表达式，表示两个切入点表达式成立任意一个的匹配
- ! ：连接单个切入点表达式，表示该切入点表达式不成立的匹配

示例：

```java
execution(* *(..))		//前三个都是匹配全部
execution(* *..*(..))
execution(* *..*.*(..))
execution(public * *..*.*(..))
execution(public int *..*.*(..))
execution(public void *..*.*(..))
execution(public void com..*.*(..)) 
execution(public void com..service.*.*(..))
execution(public void com.seazean.service.*.*(..))
execution(public void com.seazean.service.User*.*(..))
execution(public void com.seazean.service.*Service.*(..))
execution(public void com.seazean.service.UserService.*(..))
execution(public User com.seazean.service.UserService.find*(..))	//find开头
execution(public User com.seazean.service.UserService.*Id(..))		//I
execution(public User com.seazean.service.UserService.findById(..))
execution(public User com.seazean.service.UserService.findById(int))
execution(public User com.seazean.service.UserService.findById(int,int))
execution(public User com.seazean.service.UserService.findById(int,*))
execution(public User com.seazean.service.UserService.findById())
execution(List com.seazean.service.*Service+.findAll(..))
```



***



##### 配置方式

XML配置规则：

- 企业开发命名规范严格遵循规范文档进行

- 先为方法配置局部切入点，再抽取类中公共切入点，最后抽取全局切入点

- 代码走查过程中检测切入点是否存在越界性包含

- 代码走查过程中检测切入点是否存在非包含性进驻

- 设定 AOP 执行检测程序，在单元测试中监控通知被执行次数与预计次数是否匹配（不绝对正确：加进一个不该加的，删去一个不该删的相当于结果不变）

- 设定完毕的切入点如果发生调整务必进行回归测试

```xml
<aop:config>
    <!--1.配置公共切入点-->
    <aop:pointcut id="pt1" expression="execution(* *(..))"/>
    <aop:aspect ref="myAdvice">
        <!--2.配置局部切入点-->
        <aop:pointcut id="pt2" expression="execution(* *(..))"/>
        <!--引用公共切入点-->
        <aop:before method="logAdvice" pointcut-ref="pt1"/>
        <!--引用局部切入点-->
        <aop:before method="logAdvice" pointcut-ref="pt2"/>
        <!--3.直接配置切入点-->
        <aop:before method="logAdvice" pointcut="execution(* *(..))"/>
    </aop:aspect>
</aop:config>
```





***



#### Advice

##### 通知类型

AOP的通知类型共5种：前置通知，后置通知、返回后通知、抛出异常后通知、环绕通知

###### before

标签：<aop:before>，aop:aspect的子标签

作用：设置前置通知

* **前置通知**：原始方法执行前执行，如果通知中抛出异常，阻止原始方法运行
* 应用：数据校验

格式：

```xml
<aop:aspect ref="adviceId">
    <aop:before method="methodName" pointcut="execution(* *(..))"/>
    <!--一个aop:aspect标签中可以配置多个aop:before标签-->
</aop:aspect>
```

基本属性：

- method：在通知类中设置当前通知类别对应的方法

- pointcut：设置当前通知对应的切入点表达式，与pointcut-ref属性冲突

- pointcut-ref：设置当前通知对应的切入点id，与pointcut属性冲突



###### after

标签：<aop:after>，aop:aspect的子标签

作用：设置后置通知

* **后置通知**：原始方法执行后执行，无论原始方法中是否出现异常，都将执行通知

* 应用：现场清理

格式：

```xml
<aop:aspect ref="adviceId">
    <aop:after method="methodName" pointcut="execution(* *(..))"/>
    <!--一个aop:aspect标签中可以配置多个aop:after标签-->
</aop:aspect>
```

基本属性：

- method：在通知类中设置当前通知类别对应的方法

- pointcut：设置当前通知对应的切入点表达式，与pointcut-ref属性冲突

- pointcut-ref：设置当前通知对应的切入点id，与pointcut属性冲突



###### after-r

标签：<aop:after-returning>，aop:aspect的子标签

作用：设置返回后通知

* **返回后通知**：原始方法正常执行完毕并返回结果后执行，如果原始方法中抛出异常，无法执行

* 应用：返回值相关数据处理

格式：

```xml
<aop:aspect ref="adviceId">
    <aop:after-returning method="methodName" pointcut="execution(* *(..))"/>
    <!--一个aop:aspect标签中可以配置多个aop:after-returning标签-->
</aop:aspect>
```

基本属性：

- method：在通知类中设置当前通知类别对应的方法
- pointcut：设置当前通知对应的切入点表达式，与pointcut-ref属性冲突
- pointcut-ref：设置当前通知对应的切入点id，与pointcut属性冲突
- returning：设置接受返回值的参数，与通知类中对应方法的参数一致



###### after-t

标签：<aop:after-throwing>，aop:aspect的子标签

作用：设置抛出异常后通知

* **抛出异常后通知**：原始方法抛出异常后执行，如果原始方法没有抛出异常，无法执行
* 应用：对原始方法中出现的异常信息进行处理

格式：

```xml
<aop:aspect ref="adviceId">
    <aop:after-throwing method="methodName" pointcut="execution(* *(..))"/>
    <!--一个aop:aspect标签中可以配置多个aop:after-throwing标签-->
</aop:aspect>
```

基本属性：

- method：在通知类中设置当前通知类别对应的方法
- pointcut：设置当前通知对应的切入点表达式，与pointcut-ref属性冲突
- pointcut-ref：设置当前通知对应的切入点id，与pointcut属性冲突
- throwing：设置接受异常对象的参数，与通知类中对应方法的参数一致



###### around

标签：<aop:around>，aop:aspect的子标签

作用：设置环绕通知

* **环绕通知**：在原始方法执行前后均有对应执行执行，还可以阻止原始方法的执行

* 应用：功能强大，可以做任何事情

格式：

```xml
<aop:aspect ref="adviceId">
    <aop:around method="methodName" pointcut="execution(* *(..))"/>
    <!--一个aop:aspect标签中可以配置多个aop:around标签-->
</aop:aspect>
```

基本属性：

- method ：在通知类中设置当前通知类别对应的方法

- pointcut ：设置当前通知对应的切入点表达式，与pointcut-ref属性冲突

- pointcut-ref ：设置当前通知对应的切入点id，与pointcut属性冲突

环绕通知的开发方式（参考通知顺序章节）：

- 环绕通知是**在原始方法的前后添加功能**，在环绕通知中，存在对原始方法的显式调用

  ```java
  public Object around(ProceedingJoinPoint pjp) throws Throwable {
      Object ret = pjp.proceed();
      return ret;
  }
  ```

- 环绕通知方法相关说明：

  - 方法须设定Object类型的返回值，否则会**拦截**原始方法的返回。如果原始方法返回值类型为void，通知方法也可以设定返回值类型为void，最终返回null

  - 方法需在第一个参数位置设定ProceedingJoinPoint对象，通过该对象调用proceed()方法，实现**对原始方法的调用**。如省略该参数，原始方法将无法执行

  - 使用proceed()方法调用原始方法时，因无法预知原始方法运行过程中是否会出现异常，强制抛出Throwable对象，封装原始方法中可能出现的异常信息



****



##### 通知顺序

当同一个切入点配置了多个通知时，通知会存在运行的先后顺序，该顺序以通知配置的顺序为准。

* AOPAdvice

  ```java
  public class AOPAdvice {
      public void before(){
          System.out.println("before...);
      }
      public void after(){
          System.out.println("after...");
      }
      public void afterReturing(){
          System.out.println("afterReturing...");
      }
      public void afterThrowing(){
          System.out.println("afterThrowing...");
      }
      public Object around(ProceedingJoinPoint pjp) {
          System.out.println("around before...");
         	//对原始方法的调用
          Object ret = pjp.proceed();
          System.out.println("around after..."+ret);
     	    return ret;
      }
  }
  ```

* applicationContext.xml  **顺序执行**

  ```xml
  <aop:config>
      <aop:pointcut id="pt" expression="execution(* *..*(..))"/>
      <aop:aspect ref="myAdvice">
  		<aop:before method="before" pointcut-ref="pt"/>
          <aop:after method="after" pointcut-ref="pt"/>
          <aop:after-returning method="afterReturing" pointcut-ref="pt"/>
          <aop:after-throwing method="afterThrowing" pointcut-ref="pt"/>
          <aop:around method="around" pointcut-ref="pt"/>
      </aop:aspect>
  </aop:config>
  ```

  

***



##### 获取数据

###### 参数

第一种方式：

* 设定通知方法第一个参数为JoinPoint，通过该对象调用getArgs()方法，获取原始方法运行的参数数组

  ```java
  public void before(JoinPoint jp) throws Throwable {
      Object[] args = jp.getArgs();
  }
  ```

* 所有的通知均可以获取参数，环绕通知使用ProceedingJoinPoint.getArgs()方法

第二种方式：

* 设定切入点表达式为通知方法传递参数（锁定通知变量名）

* 流程图：![](https://gitee.com/seazean/images/raw/master/Frame/AOP通知获取参数方式二.png)

* 解释：
  * `&amp;`代表并且&
  * 输出结果：a = param1   b = param2

第三种方式：

* 设定切入点表达式为通知方法传递参数（改变通知变量名的定义顺序）
* 流程图：![](https://gitee.com/seazean/images/raw/master/Frame/AOP通知获取参数方式三.png)

* 解释：输出结果 a = param2   b = param1



****



###### 返回值

环绕通知和返回后通知可以获取返回值，后置通知不一定，其他类型获取不到

第一种方式：适用于返回后通知（after-returning）

- 设定返回值变量名

- 原始方法：

  ```java
  public class UserServiceImpl implements UserService {
      @Override
      public int save() {
          System.out.println("user service running...");
          return 100;
      }
  }
  ```

- AOP配置：

  ```xml
  <aop:aspect ref="myAdvice">
      <aop:pointcut id="pt" expression="execution(* *(..))"/>
      <aop:after-returning method="afterReturning" pointcut-ref="pt" returning="ret"/>
  </aop:aspect>
  ```

- 通知类：

  ```java
  public class AOPAdvice {
      public void afterReturning(Object ret) {
          System.out.println("return:" + ret);
      }
  }
  ```

  


第二种：适用于环绕通知（around）

- 在通知类的方法中调用原始方法获取返回值

- 原始方法：

  ```java
  public class UserServiceImpl implements UserService {
      @Override
      public int save() {
          System.out.println("user service running...");
          return 100;
      }
  }
  ```

- AOP配置：

  ```xml
  <aop:aspect ref="myAdvice">
      <aop:pointcut id="pt" expression="execution(* *(..))  "/>
      <aop:around method="around" pointcut-ref="pt" />
  </aop:aspect>
  ```

- 通知类：

  ```java
  public class AOPAdvice {    
  	public Object around(ProceedingJoinPoint pjp) throws Throwable {
          Object ret = pjp.proceed();
          return ret;
      }
  }
  ```

- 测试类：

  ```java
  public class App {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          UserService userService = (UserService) ctx.getBean("userService");
  		int ret = userService.save();
         	System.out.println("app....." + ret);
      }
  }
  ```

  

***



###### 异常

环绕通知和抛出异常后通知可以获取异常，后置通知不一定，其他类型获取不到

第一种：适用于返回后通知（after-throwing）

- 设定异常对象变量名

- 原始方法

  ```java
  public class UserServiceImpl implements UserService {
      @Override
  	public void save() {
          System.out.println("user service running...");
          int i = 1/0;
      }
  }
  ```

- AOP配置

  ```xml
  <aop:aspect ref="myAdvice">
  	<aop:pointcut id="pt" expression="execution(* *(..))  "/>
      <aop:after-throwing method="afterThrowing" pointcut-ref="pt" throwing="t"/>
  </aop:aspect>
  ```

- 通知类

  ```java
  public void afterThrowing(Throwable t){
      System.out.println(t.getMessage());
  }
  ```

  


第二种：适用于环绕通知（around）

* 在通知类的方法中调用原始方法捕获异常

- 原始方法：

  ```java
  public class UserServiceImpl implements UserService {
      @Override
  	public void save() {
          System.out.println("user service running...");
          int i = 1/0;
      }
  }
  ```

- AOP配置：

  ```xml
  <aop:aspect ref="myAdvice">
      <aop:pointcut id="pt" expression="execution(* *(..))  "/>
      <aop:around method="around" pointcut-ref="pt" />
  </aop:aspect>
  ```

- 通知类：try……catch……捕获异常后，ret为null

  ```java
  public Object around(ProceedingJoinPoint pjp) throws Throwable {
      Object ret = pjp.proceed();	//对此处调用进行try……catch……捕获异常，或抛出异常
      /* try {
              ret = pjp.proceed();
          } catch (Throwable throwable) {
              System.out.println("around exception..." + throwable.getMessage());
          }*/
      return ret;
  }
  ```

* 测试类

  ```java
  userService.delete();
  ```

  



***



###### 获取全部

* UserService

  ```java
  public interface UserService {
      public void save(int i, int m);
  
      public int update();
  
      public void delete();
  }
  ```

  ```java
  public class UserServiceImpl implements UserService {
      @Override
      public void save(int i, int m) {
          System.out.println("user service running..." + i + "," + m);
      }
  
      @Override
      public int update() {
          System.out.println("user service update running...");
          return 100;
      }
  
      @Override
      public void delete() {
          System.out.println("user service delete running...");
          int i = 1 / 0;
      }
  }
  ```

* AOPAdvice

  ```java
  public class AOPAdvice {
      public void before(JoinPoint jp){
          //通过JoinPoint参数获取调用原始方法所携带的参数
          Object[] args = jp.getArgs();
          System.out.println("before..."+args[0]);
      }
  
      public void after(JoinPoint jp){
          Object[] args = jp.getArgs();
          System.out.println("after..."+args[0]);
      }
  
      public void afterReturing(Object ret){
          System.out.println("afterReturing..."+ret);
      }
  
      public void afterThrowing(Throwable t){
          System.out.println("afterThrowing..."+t.getMessage());
      }
  
      public Object around(ProceedingJoinPoint pjp) {
          System.out.println("around before...");
          Object ret = null;
          try {
              //对原始方法的调用
              ret = pjp.proceed();
          } catch (Throwable throwable) {
              System.out.println("around...exception...."+throwable.getMessage());
          }
          System.out.println("around after..."+ret);
          return ret;
      }
  }
  ```

* applicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:aop="http://www.springframework.org/schema/aop"
         xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          https://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/aop
          https://www.springframework.org/schema/aop/spring-aop.xsd
          ">
      <bean id="userService" class="service.impl.UserServiceImpl"/>
      <bean id="myAdvice" class="aop.AOPAdvice"/>
  
      <aop:config>
          <aop:pointcut id="pt" expression="execution(* *..*(..))"/>
          <aop:aspect ref="myAdvice">
              <aop:before method="before" pointcut="pt"/>
              <aop:around method="around" pointcut-ref="pt"/>
              <aop:after method="after" pointcut="pt"/>
              <aop:after-returning method="afterReturning" pointcut-ref="pt" returning="ret"/>
              <aop:after-throwing method="afterThrowing" pointcut-ref="pt" throwing="t"/>
          </aop:aspect>
      </aop:config>
  </beans>
  ```

* 测试类

  ```java
  public class App {
      public static void main(String[] args) {
          ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
          UserService userService = (UserService) ctx.getBean("userService");
  //        userService.save(666, 888);
  //        int ret = userService.update();
  //        System.out.println("app....." + ret);
          userService.delete();
      }
  }
  ```

  



***



### 注解开发

#### AOP注解

AOP 注解简化 xml：

![](https://gitee.com/seazean/images/raw/master/Frame/AOP注解开发.png)

注意事项：

1. 切入点最终体现为一个方法，无参无返回值，无实际方法体内容，但不能是抽象方法

2. 引用切入点时必须使用方法调用名称，方法后面的（）不能省略

3. 切面类中定义的切入点只能在当前类中使用，如果想引用其他类中定义的切入点使用“类名.方法名()”引用

4. 可以在通知类型注解后添加参数，实现 XML 配置中的属性，例如 after-returning 后的 returning 性





****



#### 启动注解

##### XML

开启 AOP 注解支持：

```xml
<aop:aspectj-autoproxy/>
<context:component-scan base-package="aop,config,service"/><!--启动Spring扫描-->
```

开发步骤：

1. 导入坐标（伴随 spring-context 坐标导入已经依赖导入完成）
2. 开启 AOP 注解支持
3. 配置切面 @Aspect
4. 定义专用的切入点方法，并配置切入点 @Pointcut
5. 为通知方法配置通知类型及对应切入点 @Before



##### 纯注解

注解：@EnableAspectJAutoProxy

位置：Spring 注解配置类定义上方

作用：设置当前类开启 AOP 注解驱动的支持，加载 AOP 注解

格式：

```java
@Configuration
@ComponentScan("com.seazean")
@EnableAspectJAutoProxy
public class SpringConfig {
}
```



***



#### 基本注解

##### Aspect

注解：@Aspect

位置：类定义上方

作用：设置当前类为切面类

格式：

```java
@Aspect
public class AopAdvice {
}
```



##### Pointcut

注解：@Pointcut

位置：方法定义上方

作用：使用当前方法名作为切入点引用名称

格式：

```java
@Pointcut("execution(* *(..))")
public void pt() {
}
```

说明：被修饰的方法忽略其业务功能，格式设定为无参无返回值的方法，方法体内空实现（非抽象）





##### Before

注解：@Before

位置：方法定义上方

作用：标注当前方法作为前置通知

格式：

```java
@Before("pt()")
public void before(JoinPoint joinPoint){
    //joinPoint.getArgs();
}
```

注意：**多个参数时，JoinPoint参数一定要在第一位**



##### After

注解：@After

位置：方法定义上方

作用：标注当前方法作为后置通知

格式：

```java
@After("pt()")
public void after(){
}
```



##### AfterR

注解：@AfterReturning

位置：方法定义上方

作用：标注当前方法作为返回后通知

格式：

```java
@AfterReturning(value="pt()",returning = "result")
public void afterReturning(Object result) {
}
```

特殊参数：

- returning ：设定使用通知方法参数**接收**返回值的变量名



##### AfterT

注解：@AfterThrowing

位置：方法定义上方

作用：标注当前方法作为异常后通知

格式：

```java
@AfterThrowing(value="pt()",throwing = "t")
public void afterThrowing(Throwable t){
}
```

特殊参数：

- throwing ：设定使用通知方法参数接收原始方法中抛出的异常对象名



##### Around

注解：@Around

位置：方法定义上方

作用：标注当前方法作为环绕通知

格式：

```java
@Around("pt()")
public Object around(ProceedingJoinPoint pjp) throws Throwable {
    Object ret = pjp.proceed();
    return ret;
}
```



***



#### 执行顺序

AOP使用XML配置情况下，通知的执行顺序由配置顺序决定，在注解情况下由于不存在配置顺序的概念，参照通知所配置的**方法名字符串对应的编码值顺序**，可以简单理解为字母排序

- 同一个通知类中，相同通知类型以方法名排序为准

  ```java
  @Before("aop.AOPPointcut.pt()")
  public void aop001Log(){}
  
  @Before("aop.AOPPointcut.pt()")
  public void aop002Exception(){}
  ```

- 不同通知类中，以类名排序为准

- 使用@Order注解通过变更bean的加载顺序改变通知的加载顺序

  ```java
  @Component
  @Aspect
  @Order(1)  //先执行
  public class AOPAdvice2 {
  }
  ```

  ```java
  @Component
  @Aspect
  @Order(2) 
  public class AOPAdvice1 {//默认执行此通知
  }
  ```

  

企业开发经验：

- 通知方法名由3部分组成，分别是前缀、顺序编码、功能描述

- 前缀为固定字符串，例如baidu、seazean等，无实际意义

- 顺序编码为6位以内的整数，通常3位即可，不足位补0

- 功能描述为该方法对应的实际通知功能，例如exception、strLenCheck

  - 制通知执行顺序使用顺序编码控制，使用时做一定空间预留

  - 003使用，006使用，预留001、002、004、005、007、008

  - 使用时从中段开始使用，方便后期做前置追加或后置追加

  - 最终顺序以运行顺序为准，以测试结果为准，不以设定规则为准



***



### AOP 原理

#### 静态代理

装饰者模式（Decorator Pattern）：在不惊动原始设计的基础上，为其添加功能

```java
public class UserServiceDecorator implements UserService{
    private UserService userService;
    
    public UserServiceDecorator(UserService userService) {
        this.userService = userService;
    }
    
    public void save() {
        //原始调用
        userService.save();
        //增强功能（后置）
        System.out.println("后置增强功能");
    }
}
```



***



#### Proxy

JDKProxy 动态代理是针对对象做代理，要求原始对象具有接口实现，并对接口方法进行增强，因为**代理类继承Proxy**

静态代理和动态代理的区别：

* 静态代理是在编译时就已经将接口、代理类、被代理类的字节码文件确定下来
* 动态代理是程序在运行后通过反射创建字节码文件交由 JVM 加载

```java
public class UserServiceJDKProxy {
    public static UserService createUserServiceJDKProxy(UserService userService) {
        UserService service = (UserService) Proxy.newProxyInstance(
            userService.getClass().getClassLoader(),//获取被代理对象的类加载器
            userService.getClass().getInterfaces(),	//获取被代理对象实现的接口
            new InvocationHandler() {				//对原始方法执行进行拦截并增强
				@Override
				public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    if (method.getName().equals("save")) {
                        System.out.println("前置增强");
                        Object ret = method.invoke(userService, args);
                        System.out.println("后置增强");
                        return ret;
                    }
                    return null;
				}
 			});
        return service;
    }
}
```



***



#### CGLIB

CGLIB（Code Generation Library）：Code生成类库 

CGLIB 特点：

* CGLIB 动态代理**不限定**是否具有接口，可以对任意操作进行增强
* CGLIB 动态代理无需要原始被代理对象，动态创建出新的代理对象
* CGLIB **继承被代理类**，如果代理类是final则不能实现

![](https://gitee.com/seazean/images/raw/master/Frame/AOP底层原理-cglib.png)

* cglib类

  * JDKProxy仅对接口方法做增强，cglib对所有方法做增强，包括Object类中的方法 (toString、hashCode)
  * 返回值类型采用多态向下转型，所以需要设置父类类型

  需要对方法进行判断是否是save，来选择性增强

  ```java
  public class UserServiceImplCglibProxy {
      public static UserService createUserServiceCglibProxy(Class cls){
          //1.创建Enhancer对象（可以理解为内存中动态创建了一个类的字节码）
          Enhancer enhancer = new Enhancer();
          
          //2.设置Enhancer对象的父类是指定类型UserServerImpl
          enhancer.setSuperclass(cls);
          
          //3.设置回调方法
          enhancer.setCallback(new MethodInterceptor() {
              @Override
              public Object intercept(Object o, Method m, Object[] args, MethodProxy mp) throws Throwable {
                  //o是被代理出的类创建的对象，所以使用MethodProxy调用，并且是调用父类
                  //通过调用父类的方法实现对原始方法的调用
                  Object ret = methodProxy.invokeSuper(o, args);
                  //后置增强内容,需要判断是都是save方法
                  if (method.getName().equals("save")) {
                      System.out.println("I love Java");
                  }
                  return ret;
              }
          });
          //使用Enhancer对象创建对应的对象
          return (UserService)enhancer.create();
      }
  }
  ```

* Test类

  ```java
  public class App {
      public static void main(String[] args) {
          UserService userService = UserServiceCglibProxy.createUserServiceCglibProxy(UserServiceImpl.class);
          userService.save();
      }
  }
  ```

  



***



#### 代理选择

Spirng 可以通过配置的形式控制使用的代理形式，Spring 会先判断是否实现了接口，如果实现了接口就使用 JDK 动态代理，如果没有实现接口则使用 CGLIB 动态代理，通过配置可以修改为使用 CGLIB 

- XML 配置

  ```xml
  <!--XML配置AOP-->
  <aop:config proxy-target-class="false"></aop:config>
  ```

- XML 注解支持

  ```xml
  <!--注解配置AOP-->
  <aop:aspectj-autoproxy proxy-target-class="false"/>
  ```

- 注解驱动

  ```java
  //修改为使用 cglib 创建代理对象
  @EnableAspectJAutoProxy(proxyTargetClass = true)
  ```

* JDK 动态代理和 CGLIB 动态代理的区别：

  * JDK 动态代理只能对实现了接口的类生成代理，没有实现接口的类不能使用。
  * CGLIB 动态代理即使被代理的类没有实现接口也可以使用，因为 CGLIB 动态代理是使用继承被代理类的方式进行扩展
  * CGLIB 动态代理是通过继承的方式，覆盖被代理类的方法来进行代理，所以如果方法是被 final 修饰的话，就不能进行代理





***



#### 织入时机

![AOP织入时机](https://gitee.com/seazean/images/raw/master/Frame/AOP织入时机.png)





***





## 事务

### 事务机制

#### 事务介绍

事务：数据库中多个操作合并在一起形成的操作序列，事务特征（ACID）

作用：

* 当数据库操作序列中个别操作失败时，提供一种方式使数据库状态恢复到正常状态（**A**），保障数据库即使在异常状态下仍能保持数据一致性（**C**）（要么操作前状态，要么操作后状态）
* 当出现并发访问数据库时，在多个访问间进行相互隔离，防止并发访问操作结果互相干扰（**I**）

Spring 事务一般加到业务层，对应着业务的操作，Spring 事务的本质其实就是数据库对事务的支持，没有数据库的事务支持，Spring 是无法提供事务功能的，Spring 只提供统一事务管理接口

Spring 在事务开始时，根据当前环境中设置的隔离级别，调整数据库隔离级别，由此保持一致。程序是否支持事务首先取决于数据库 ，比如 MySQL ，如果是 **innodb 引擎**，是支持事务的；如果 MySQ L使用 myisam 引擎，那从根上就是不支持事务的

**保证原子性**：

* 要保证事务的原子性，就需要在异常发生时，对已经执行的操作进行**回滚**
* 在 MySQL 中，恢复机制是通过**回滚日志（undo log）** 实现，所有事务进行的修改都会先先记录到这个回滚日志中，然后再执行相关的操作。如果执行过程中遇到异常的话，直接利用回滚日志中的信息将数据回滚到修改之前的样子即可
* 回滚日志会先于数据持久化到磁盘上，这样保证了即使遇到数据库突然宕机等情况，当用户再次启动数据库的时候，数据库还能够通过查询回滚日志来回滚将之前未完成的事务



***



#### 隔离级别

TransactionDefinition 接口中定义了五个表示隔离级别的常量：

- **TransactionDefinition.ISOLATION_DEFAULT**：使用后端数据库默认的隔离级别，Mysql 默认采用的 REPEATABLE_READ隔离级别，Oracle 默认采用的 READ_COMMITTED隔离级别.
- **TransactionDefinition.ISOLATION_READ_UNCOMMITTED**：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读
- **TransactionDefinition.ISOLATION_READ_COMMITTED**：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生
- **TransactionDefinition.ISOLATION_REPEATABLE_READ**：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- **TransactionDefinition.ISOLATION_SERIALIZABLE**：最高的隔离级别，完全服从 ACID 的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别

MySQL InnoDB 存储引擎的默认支持的隔离级别是 **REPEATABLE-READ（可重读）**

**分布式事务**：允许多个独立的事务资源（transactional resources）参与到一个全局的事务中。事务资源通常是关系型数据库系统，但也可以是其他类型的资源，全局事务要求在其中的所有参与的事务要么都提交，要么都回滚，这对于事务原有的 ACID 要求又有了提高

在使用分布式事务时，InnoDB 存储引擎的事务隔离级别必须设置为 SERIALIZABLE



****



#### 传播行为

事务传播行为是为了解决业务层方法之间互相调用的事务问题，也就是方法嵌套：

* 当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。

* 例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行

  ```java
  //外层事务 Service A 的 aMethod 调用内层 Service B 的 bMethod
  class A {
      @Transactional(propagation=propagation.xxx)
      public void aMethod {
          B b = new B();
          b.bMethod();
      }
  }
  class B {
      @Transactional(propagation=propagation.xxx)
      public void bMethod {}
  }
  ```

**支持当前事务**的情况：

* TransactionDefinition.PROPAGATION_REQUIRED： 如果当前存在事务，则**加入该事务**；如果当前没有事务，则创建一个新的事务
  * 内外层是相同的事务
  * 在 aMethod 或者在 bMethod 内的任何地方出现异常，事务都会被回滚
* TransactionDefinition.PROPAGATION_SUPPORTS： 如果当前存在事务，则**加入该事务**；如果当前没有事务，则以非事务的方式继续运行
* TransactionDefinition.PROPAGATION_MANDATORY： 如果当前存在事务，则**加入该事务**；如果当前没有事务，则抛出异常

**不支持当前事务**的情况：

- TransactionDefinition.PROPAGATION_REQUIRES_NEW： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。
  - 内外层是不同的事务，如果 bMethod 已经提交，如果 aMethod 失败回滚 ，bMethod 不会回滚
  - 如果 bMethod 失败回滚，ServiceB 抛出的异常被 ServiceA 捕获，如果 B 抛出的异常是 A 会回滚的异常，aMethod 事务需要回滚，否则仍然可以提交
- TransactionDefinition.PROPAGATION_NOT_SUPPORTED： **以非事务方式运行**，如果当前存在事务，则把当前事务挂起
- TransactionDefinition.PROPAGATION_NEVER： **以非事务方式运行**，如果当前存在事务，则抛出异常

其他情况：

* TransactionDefinition.PROPAGATION_NESTED： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于 PROPAGATION_REQUIRED 
  * 如果 ServiceB 异常回滚，可以通过 try-catch 机制执行 ServiceC
  * 如果 ServiceB 提交， ServiceA 可以根据具体的配置决定是 commit 还是 rollback

requied：必须的、supports：支持的、mandatory：强制的、nested：嵌套的



****



#### 超时属性

事务超时，就是指一个事务所允许执行的最长时间，如果超过该时间限制但事务还没有完成，则自动回滚事务。在 `TransactionDefinition` 中以 int 的值来表示超时时间，其单位是秒，默认值为-1



***



#### 只读属性

对于只有读取数据查询的事务，可以指定事务类型为 readonly，即只读事务；只读事务不涉及数据的修改，数据库会提供一些优化手段，适合用在有多条数据库查询操作的方法中

读操作为什么需要启用事务支持：

* MySQL  默认对每一个新建立的连接都启用了 `autocommit` 模式，在该模式下，每一个发送到 MySQL 服务器的 `sql` 语句都会在一个**单独**的事务中进行处理，执行结束后会自动提交事务，并开启一个新的事务
* 执行多条查询语句，如果方法加上了 `Transactional` 注解，这个方法执行的所有 `sql` 会被放在一个事务中，如果声明了只读事务的话，数据库就会去优化它的执行，并不会带来其他的收益。如果不加 `Transactional`，每条 `sql` 会开启一个单独的事务，中间被其它事务修改了数据，比如在前条 SQL 查询之后，后条 SQL 查询之前，数据被其他用户改变，则这次整体的统计查询将会出**现读数据不一致的状态**



***



### 核心对象

#### 事务对象

J2EE 开发使用分层设计的思想进行，对于简单的业务层转调数据层的单一操作，事务开启在业务层或者数据层并无太大差别，当业务中包含多个数据层的调用时，需要在业务层开启事务，对数据层中多个操作进行组合并归属于同一个事务进行处理

Spring 为业务层提供了整套的事务解决方案：

- PlatformTransactionManager
- TransactionDefinition

- TransactionStatus



***



#### PTM

PlatformTransactionManager，平台事务管理器实现类：

- DataSourceTransactionManager  适用于 Spring JDBC 或 MyBatis

- HibernateTransactionManager  适用于 Hibernate3.0 及以上版本

- JpaTransactionManager  适用于 JPA

- JdoTransactionManager  适用于 JDO

- JtaTransactionManager  适用于 JTA

管理器：

- JPA（Java Persistence API）Java EE 标准之一，为 POJO 提供持久化标准规范，并规范了持久化开发的统一 API，符合 JPA 规范的开发可以在不同的 JPA 框架下运行

  **非持久化一个字段**：

  ```java
  static String transient1; // not persistent because of static
  final String transient2 = “Satish”; // not persistent because of final
  transient String transient3; // not persistent because of transient
  @Transient
  String transient4; // not persistent because of @Transient
  ```

- JDO（Java Data Object）是 Java 对象持久化规范，用于存取某种数据库中的对象，并提供标准化 API。JDBC 仅针对关系数据库进行操作，JDO 可以扩展到关系数据库、XML、对象数据库等，可移植性更强

- JTA（Java Transaction API）Java EE 标准之一，允许应用程序执行分布式事务处理。与 JDBC 相比，JDBC 事务则被限定在一个单一的数据库连接，而一个 JTA 事务可以有多个参与者，比如 JDBC 连接、JDO 都可以参与到一个 JTA 事务中

此接口定义了事务的基本操作：

| 方法                                                         | 说明     |
| ------------------------------------------------------------ | -------- |
| TransactionStatus getTransaction(TransactionDefinition definition) | 获取事务 |
| void commit(TransactionStatus status)                        | 提交事务 |
| void rollback(TransactionStatus status)                      | 回滚事务 |



****



#### Definition

TransactionDefinition 此接口定义了事务的基本信息：

| 方法                         | 说明                 |
| ---------------------------- | -------------------- |
| String getName()             | 获取事务定义名称     |
| boolean isReadOnly()         | 获取事务的读写属性   |
| int getIsolationLevel()      | 获取事务隔离级别     |
| int getTimeout()             | 获取事务超时时间     |
| int getPropagationBehavior() | 获取事务传播行为特征 |



***



#### Status

TransactionStatus 此接口定义了事务在执行过程中某个时间点上的状态信息及对应的状态操作：

| 方法                       | 说明                           |
| -------------------------- | ------------------------------ |
| boolean isNewTransaction() | 获取事务是否处于新开始事务状态 |
| voin flush()               | 刷新事务状态                   |
| boolean isCompleted()      | 获取事务是否处于已完成状态     |
| boolean hasSavepoint()     | 获取事务是否具有回滚储存点     |
| boolean isRollbackOnly()   | 获取事务是否处于回滚状态       |
| void setRollbackOnly()     | 设置事务处于回滚状态           |



****



### 编程式

#### 控制方式

编程式、声明式（XML）、声明式（注解）



#### 环境准备

银行转账业务

* 包装类

  ```java
  public class Account implements Serializable {
      private Integer id;
      private String name;
      private Double money;
      .....
  }
  ```

* DAO层接口：AccountDao

  ```java
  public interface AccountDao {
      //入账操作	name:入账用户名	money:入账金额
      void inMoney(@Param("name") String name, @Param("money") Double money);
  
      //出账操作	name:出账用户名	money:出账金额
      void outMoney(@Param("name") String name, @Param("money") Double money);
  }
  ```

* 业务层接口提供转账操作：AccountService

  ```java
  public interface AccountService {
  	//转账操作	outName:出账用户名	inName:入账用户名	money:转账金额
  	public void transfer(String outName,String inName,Double money);
  }
  ```

* 业务层实现提供转账操作：AccountServiceImpl

  ```java
  public class AccountServiceImpl implements AccountService {
      private AccountDao accountDao;
      public void setAccountDao(AccountDao accountDao) {
          this.accountDao = accountDao;
      }
      @Override
      public void transfer(String outName,String inName,Double money){
  		accountDao.inMoney(outName,money);
          accountDao.outMoney(inName,money);
  	}
  }
  ```

* 映射配置文件：dao / AccountDao.xml

  ```xml
  <mapper namespace="dao.AccountDao">
      <update id="inMoney">
          UPDATE account SET money = money + #{money} WHERE name = #{name}
      </update>
  
      <update id="outMoney">
          UPDATE account SET money = money - #{money} WHERE name = #{name}
      </update>
  </mapper>
  ```

* jdbc.properties

  ```properties
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://192.168.2.185:3306/spring_db
  jdbc.username=root
  jdbc.password=1234
  ```

* 核心配置文件：applicationContext.xml

  ```xml
  <context:property-placeholder location="classpath:*.properties"/>
  
  <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
      <property name="driverClassName" value="${jdbc.driver}"/>
      <property name="url" value="${jdbc.url}"/>
      <property name="username" value="${jdbc.username}"/>
      <property name="password" value="${jdbc.password}"/>
  </bean>
  
  <bean id="accountService" class="service.impl.AccountServiceImpl">
      <property name="accountDao" ref="accountDao"/>
  </bean>
  
  <bean class="org.mybatis.spring.SqlSessionFactoryBean">
      <property name="dataSource" ref="dataSource"/>
      <property name="typeAliasesPackage" value="domain"/>
  </bean>
  <!--扫描映射配置和Dao-->
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="basePackage" value="dao"/>
  </bean>
  ```

* 测试类

  ```java
  ApplicationContext ctx = new ClassPathXmlApplicationContext("ap...xml");
  AccountService accountService = (AccountService) ctx.getBean("accountService");
  accountService.transfer("Jock1", "Jock2", 100d);
  ```

  

***



#### 编程式

编程式事务就是代码显式的给出事务的开启和提交

* 修改业务层实现提供转账操作：AccountServiceImpl

  ```java
  public void transfer(String outName,String inName,Double money){
      //1.创建事务管理器，开启事务
      DataSourceTransactionManager dstm = new DataSourceTransactionManager();
      //2.为事务管理器设置与数据层相同的数据源
      dstm.setDataSource(dataSource);
      //3.创建事务定义对象
      TransactionDefinition td = new DefaultTransactionDefinition();
      //4.创建事务状态对象，用于控制事务执行
      TransactionStatus ts = dstm.getTransaction(td);
      accountDao.inMoney(inName,money);
      int i = 1/0;    //模拟业务层事务过程中出现错误
      accountDao.outMoney(outName,money);
      //5.提交事务
      dstm.commit(ts);
  }
  ```

* 配置applicationContext.xml

  ```xml
  <!--添加属性注入-->
  <bean id="accountService" class="service.impl.AccountServiceImpl">
      <property name="accountDao" ref="accountDao"/>
      <property name="dataSource" ref="dataSource"/>
  </bean>
  ```

  



#### AOP改造

* 将业务层的事务处理功能抽取出来制作成 AOP 通知，利用环绕通知运行期动态织入

  ```java
  public class TxAdvice {
      private DataSource dataSource;
      public void setDataSource(DataSource dataSource) {
          this.dataSource = dataSource;
      }
  
      public Object tx(ProceedingJoinPoint pjp) throws Throwable {
          //开启事务
          PlatformTransactionManager ptm = new DataSourceTransactionManager(dataSource);
          //事务定义
          TransactionDefinition td = new DefaultTransactionDefinition();
          //事务状态
          TransactionStatus ts =  ptm.getTransaction(td);
          //pjp.getArgs()标准写法，也可以不加，同样可以传递参数
          Object ret = pjp.proceed(pjp.getArgs());
          
          //提交事务
          ptm.commit(ts);
  
          return ret;
      }
  }
  ```

* 配置 applicationContext.xml，要开启 AOP 空间

  ```xml
  <!--修改bean的属性注入-->
  <bean id="accountService" class="service.impl.AccountServiceImpl">
      <property name="accountDao" ref="accountDao"/>
  </bean>
  
  <!--配置AOP通知类，并注入dataSource-->
  <bean id="txAdvice" class="aop.TxAdvice">
      <property name="dataSource" ref="dataSource"/>
  </bean>
  
  <!--使用环绕通知将通知类织入到原始业务对象执行过程中-->
  <aop:config>
      <aop:pointcut id="pt" expression="execution(* *..transfer(..))"/>
      <aop:aspect ref="txAdvice">
          <aop:around method="tx" pointcut-ref="pt"/>
      </aop:aspect>
  </aop:config>
  ```

* 修改业务层实现提供转账操作：AccountServiceImpl

  ```java
  public class AccountServiceImpl implements AccountService {
      private AccountDao accountDao;
      public void setAccountDao(AccountDao accountDao) {
          this.accountDao = accountDao;
      }
      @Override
      public void transfer(String outName,String inName,Double money){
  		accountDao.inMoney(outName,money);
          //int i = 1 / 0;
          accountDao.outMoney(inName,money);
  	}
  }
  ```

  

  

***



### 声明式

#### XML

##### tx使用

* 删除 TxAdvice 通知类，开启 tx 命名空间，配置 applicationContext.xml

  ```xml
  <!--配置平台事务管理器-->
  <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      <property name="dataSource" ref="dataSource"/>
  </bean>
  
  <!--定义事务管理的通知类-->
  <tx:advice id="txAdvice" transaction-manager="txManager">
      <!--定义控制的事务-->
      <tx:attributes>
          <tx:method name="transfer" read-only="false"/>
      </tx:attributes>
  </tx:advice>
  
  <!--使用aop:advisor在AOP配置中引用事务专属通知类，底层invoke调用-->
  <aop:config>
      <aop:pointcut id="pt" expression="execution(* service.*Service.*(..))"/>
      <aop:advisor advice-ref="txAdvice" pointcut-ref="pt"/>
  </aop:config>
  ```

* aop:advice 与 aop:advisor 区别
  * aop:advice 配置的通知类可以是普通 java 对象，不实现接口，也不使用继承关系
  * aop:advisor 配置的通知类必须实现通知接口，底层 invoke 调用

    - MethodBeforeAdvice

    - AfterReturningAdvice

    - ThrowsAdvice


方法调用：`AbstractAspectJAdvice#invokeAdviceMethod(org.aspectj.weaver.tools.JoinPointMatch, java.lang.Object, java.lang.Throwable)`



***



##### tx配置

###### advice

标签：tx:advice，beans 的子标签

作用：专用于声明事务通知

格式：

```xml
<beans>
    <tx:advice id="txAdvice" transaction-manager="txManager">
    </tx:advice>
</beans>
```

基本属性：

- id：用于配置 aop 时指定通知器的 id
- transaction-manager：指定事务管理器 bean



###### attributes

类型：tx:attributes，tx:advice 的子标签

作用：定义通知属性

格式：

```xml
<tx:advice id="txAdvice" transaction-manager="txManager">
    <tx:attributes>
    </tx:attributes>
</tx:advice>
```



###### method

标签：tx:method，tx:attribute 的子标签

作用：设置具体的事务属性

格式：

```xml
<tx:attributes>
    <!--标准格式-->
    <tx:method name="*" read-only="false"/>
    <tx:method name="get*" read-only="true"/>
    <tx:method name="find*" read-only="true"/>
</tx:attributes>
<aop:pointcut id="pt" expression="execution(* service.*Service.*(..))"/><!--标准-->
```

说明：通常事务属性会配置多个，包含 1 个读写的全事务属性，1 个只读的查询类事务属性

属性：

* name：待添加事务的方法名表达式（支持 * 通配符）
* read-only：设置事务的读写属性，true 为只读，false 为读写
* timeout：设置事务的超时时长，单位秒，-1 为无限长
* isolation：设置事务的隔离界别，该隔离级设定是基于 Spring 的设定，非数据库端
* no-rollback-for：设置事务中不回滚的异常，多个异常使用 `,` 分隔
* rollback-for：设置事务中必回滚的异常，多个异常使用 `,` 分隔
* propagation：设置事务的传播行为



***



#### 注解

##### 开启注解

###### XML

标签：tx:annotation-driven

归属：beans 标签

作用：开启事务注解驱动，并指定对应的事务管理器

范例：

```xml
<tx:annotation-driven transaction-manager="txManager"/>
```



###### 纯注解

名称：@EnableTransactionManagement

类型：类注解，Spring 注解配置类上方

作用：开启注解驱动，等同 XML 格式中的注解驱动

范例：

```java
@Configuration
@ComponentScan("com.seazean")
@PropertySource("classpath:jdbc.properties")
@Import({JDBCConfig.class,MyBatisConfig.class,TransactionManagerConfig.class})
@EnableTransactionManagement
public class SpringConfig {
}
```

```java
public class TransactionManagerConfig {
    @Bean												//自动装配
    public PlatformTransactionManager getTransactionManager(@Autowired DataSource dataSource){
        return new DataSourceTransactionManager(dataSource);
    }
}
```





***



##### 配置注解

名称：@Transactional

类型：方法注解，类注解，接口注解

作用：设置当前类/接口中所有方法或具体方法开启事务，并指定相关事务属性

范例：

```java
@Transactional(
    readOnly = false,
    timeout = -1,
    isolation = Isolation.DEFAULT,
    rollbackFor = {ArithmeticException.class, IOException.class},
    noRollbackFor = {},
    propagation = Propagation.REQUIRES_NEW
)
public void addAccount{} 
```

说明：

* `@Transactional` 注解只有作用到 public 方法上事务才生效

* 不推荐在接口上使用 `@Transactional` 注解

  原因：在接口上使用注解，只有**在使用基于接口的代理时才会生效**，因为**注解是不能继承的**，这就意味着如果正在使用基于类的代理时，那么事务的设置将不能被基于类的代理所识别

* 正确的设置 `@Transactional` 的 rollbackFor 和 propagation 属性，否则事务可能会回滚失败

* 默认情况下，事务只有遇到运行期异常 和 Error 会导致事务回滚，但是在遇到检查型（Checked）异常时不会回滚

  * 继承自 RuntimeException 或 error 的是非检查型异常，比如空指针和索引越界，而继承自 Exception 的则是检查型异常，比如 IOException、ClassNotFoundException，RuntimeException 本身继承 Exception
  * 非检查型类异常可以不用捕获，而检查型异常则必须用 try 语句块把异常交给上级方法，这样事务才能有效

**事务不生效的问题**

* 情况 1：确认创建的 mysql 数据库表引擎是 InnoDB，MyISAM 不支持事务

* 情况 2：注解到 protected，private 方法上事务不生效，但不会报错
  原因：理论上而言，不用 public 修饰，也可以用 aop 实现事务的功能，但是方法私有化让其他业务无法调用

  AopUtils.canApply：`methodMatcher.matches(method, targetClass) --true--> return true`
  `TransactionAttributeSourcePointcut.matches()` ，AbstractFallbackTransactionAttributeSource 中 getTransactionAttribute 方法调用了其本身的 computeTransactionAttribute 方法，当加了事务注解的方法不是 public 时，该方法直接返回 null，所以造成增强不匹配

  ```java
  private TransactionAttribute computeTransactionAttribute(Method method, Class<?> targetClass) {
      // Don't allow no-public methods as required.
      if (allowPublicMethodsOnly() && !Modifier.isPublic(method.getModifiers())) {
          return null;
      }
  }
  ```

* 情况 3：注解所在的类没有被加载成 Bean

* 情况 4：在业务层捕捉异常后未向上抛出，事务不生效

  原因：在业务层捕捉并处理了异常（try..catch）等于把异常处理掉了，Spring 就不知道这里有错，也不会主动去回滚数据，推荐做法是在业务层统一抛出异常，然后在控制层统一处理

* 情况 5：遇到检测异常时，事务不开启，也无法回滚

  原因：Spring 的默认的事务规则是遇到运行异常（RuntimeException）和程序错误（Error）才会回滚。想针对检测异常进行事务回滚，可以在 @Transactional 注解里使用 rollbackFor 属性明确指定异常

* 情况 6：Spring 的事务传播策略在**内部方法**调用时将不起作用，在一个 Service 内部，事务方法之间的嵌套调用，普通方法和事务方法之间的嵌套调用，都不会开启新的事务，事务注解要加到调用方法上才生效

  原因：Spring 的事务都是使用 AOP 代理的模式，动态代理最终是要调用原始对象，而原始对象在去调用方法时是不会再触发代理，就是一个方法调用**本对象**的另一个方法，没有通过代理类直接调用，所以事务也就无法生效

  ```java
  @Transactional
  public int add(){
      update();
  }
  //注解添加在update方法上无效，需要添加到add()方法上
  public int update(){}
  ```

  



***



##### 使用注解

* Dao 层

  ```java
  public interface AccountDao {
      @Update("update account set money = money + #{money} where name = #{name}")
      void inMoney(@Param("name") String name, @Param("money") Double money);
  
      @Update("update account set money = money - #{money} where name = #{name}")
      void outMoney(@Param("name") String name, @Param("money") Double money);
  }
  ```

* 业务层

  ```java
  public interface AccountService {
      //对当前方法添加事务，该配置将替换接口的配置
      @Transactional(
          readOnly = false,
          timeout = -1,
          isolation = Isolation.DEFAULT,
          rollbackFor = {},//java.lang.ArithmeticException.class, IOException.class
          noRollbackFor = {},
          propagation = Propagation.REQUIRED
          )
      public void transfer(String outName, String inName, Double money);
  }
  ```
  
  ```java
  public class AccountServiceImpl implements AccountService {
      @Autowired
      private AccountDao accountDao;
      public void transfer(String outName, String inName, Double money) {
          accountDao.inMoney(outName,money);
          //int i = 1/0;
          accountDao.outMoney(inName,money);
      }
  }
  ```
  
* 添加文件 Spring.config、Mybatis.config、JDBCConfig (参考ioc_Mybatis)、TransactionManagerConfig

  ```java
  @Configuration
  @ComponentScan({"","",""})
  @PropertySource("classpath:jdbc.properties")
  @Import({JDBCConfig.class,MyBatisConfig.class})
  @EnableTransactionManagement
  public class SpringConfig {
  }
  ```



****



### 模板对象

Spring 模板对象：TransactionTemplate、JdbcTemplate、RedisTemplate、RabbitTemplate、JmsTemplate、HibernateTemplate、RestTemplate

* JdbcTemplate：提供标准的 sql 语句操作API
* NamedParameterJdbcTemplate：提供标准的具名 sql 语句操作API

* RedisTemplate：

  ```java
  public void changeMoney(Integer id, Double money) {
      redisTemplate.opsForValue().set("account:id:"+id,money);
  }
  public Double findMondyById(Integer id) {
      Object money = redisTemplate.opsForValue().get("account:id:" + id);
      return new Double(money.toString());
  }
  ```

![](https://gitee.com/seazean/images/raw/master/Frame/Spring-RedisTemplate.png)





***





## 原理

### XML

三大对象：

* **BeanDefinition**：是 Spring 中极其重要的一个概念，它存储了 bean 对象的所有特征信息，如是否单例、是否懒加载、factoryBeanName 等，和 bean 的关系就是类与对象的关系，一个不同的 bean 对应一个 BeanDefinition

* **BeanDefinationRegistry**：存放 BeanDefination 的容器，是一种键值对的形式，通过特定的 Bean 定义的 id，映射到相应的 BeanDefination，**BeanFactory 的实现类同样继承 BeanDefinationRegistry 接口**，拥有保存 BD 的能力

* **BeanDefinitionReader**：读取配置文件，比如 xml 用 dom4j 解析，配置文件用 io 流

程序：

```java
BeanFactory bf = new XmlBeanFactory(new ClassPathResource("applicationContext.xml"));
UserService userService1 = (UserService)bf.getBean("userService");
```

源码解析：

```java
public XmlBeanFactory(Resource resource, BeanFactory parentBeanFactory) {
    super(parentBeanFactory);
    this.reader.loadBeanDefinitions(resource);
}
public int loadBeanDefinitions(Resource resource) {
    //将 resource 包装成带编码格式的 EncodedResource
    //EncodedResource 中 getReader()方法，调用java.io包下的 转换流 创建指定编码的输入流对象
    return loadBeanDefinitions(new EncodedResource(resource));
}
```

* `XmlBeanDefinitionReader.loadBeanDefinitions()`：**把 Resource 解析成 BeanDefinition 对象**

  * `currentResources = this.resourcesCurrentlyBeingLoaded.get()`：拿到当前线程已经加载过的所有 EncodedResoure 资源，用 ThreadLocal 保证线程安全
  * `if (currentResources == null)`：判断 currentResources 是否为空，为空则进行初始化
  * `if (!currentResources.add(encodedResource))`：如果已经加载过该资源会报错，防止重复加载
  * `inputSource = new InputSource(inputStream)`：资源对象包装成 InputSource，InputSource 是 **SAX** 中的资源对象，用来进行 XML 文件的解析
  * `return doLoadBeanDefinitions()`：**加载返回**
  * `currentResources.remove(encodedResource)`：加载完成移除当前 encodedResource
  * `resourcesCurrentlyBeingLoaded.remove()`：ThreadLocal 为空时移除元素，防止内存泄露

* `XmlBeanDefinitionReader.doLoadBeanDefinitions(inputSource, resource)`：真正的加载函数

  `Document doc = doLoadDocument(inputSource, resource)`：转换成有**层次结构**的 Document 对象

  * `getEntityResolver()`**：获取用来解析 DTD、XSD 约束的解析器**

  * `getValidationModeForResource(resource)`：获取验证模式

  `int count = registerBeanDefinitions(doc, resource)`：**将 Document 解析成 BD 对象，注册（添加）到  BeanDefinationRegistry 中**，返回新注册的数量

  * `createBeanDefinitionDocumentReader()`：创建 DefaultBeanDefinitionDocumentReader 对象
  * `getRegistry().getBeanDefinitionCount()`：获取解析前 BeanDefinationRegistry 中的 bd 数量
  * `registerBeanDefinitions(doc, readerContext)`：注册 BD
    * `this.readerContext = readerContext`：保存上下文对象
    * `doRegisterBeanDefinitions(doc.getDocumentElement())`：真正的注册 BD 函数
      * `doc.getDocumentElement()`：拿出顶层标签 <beans></beans>
  * `return getRegistry().getBeanDefinitionCount() - countBefore`：返回新加入的数量

* `DefaultBeanDefinitionDocumentReader.doRegisterBeanDefinitions()`：注册 BD 到 BR

  * `createDelegate(getReaderContext(), root, parent)`：beans 是标签的解析器对象
  * `delegate.isDefaultNamespace(root)`：判断 beans 标签是否是默认的属性
  * `root.getAttribute(PROFILE_ATTRIBUTE)`：解析 profile 属性
  * `preProcessXml(root)`：解析前置处理，自定义实现
  * `parseBeanDefinitions(root, this.delegate)`：**解析 beans 标签中的子标签**
    * `parseDefaultElement(ele, delegate)`：如果是默认的标签，用该方法解析子标签
      * 判断标签名称，进行相应的解析
      * `processBeanDefinition(ele, delegate)`：
    * `delegate.parseCustomElement(ele)`：解析自定义的标签
  * `postProcessXml(root)`：解析后置处理

* `DefaultBeanDefinitionDocumentReader.processBeanDefinition()`：**解析 bean 标签并注册到注册中心**

  * `delegate.parseBeanDefinitionElement(ele)`：解析 bean 标签封装为 BeanDefinitionHolder

    * `if (!StringUtils.hasText(beanName) && !aliases.isEmpty())`：条件一成立说明 name 没有值，条件二成立说明别名有值

      `beanName = aliases.remove(0)`：拿别名列表的第一个元素当作 beanName

    * `parseBeanDefinitionElement(ele, beanName, containingBean)`：**解析 bean 标签**

      * `parseState.push(new BeanEntry(beanName))`：当前解析器的状态设置为 BeanEntry
      * class 和 parent 属性存在一个，parent 是作为父标签为了被继承
      * `createBeanDefinition(className, parent)`：设置了class 的 GenericBeanDefinition对象
      * `parseBeanDefinitionAttributes()`：解析 bean 标签的属性
      * 接下来解析子标签

    * `beanName = this.readerContext.generateBeanName(beanDefinition)`：生成 className + # + 序号的名称赋值给 beanName 

    * `return new BeanDefinitionHolder(beanDefinition, beanName, aliasesArray)`：包装成 BeanDefinitionHolder

  * `registerBeanDefinition(bdHolder, getReaderContext().getRegistry())`：**注册到容器**

    * `beanName = definitionHolder.getBeanName()`：获取beanName
    * `this.beanDefinitionMap.put(beanName, beanDefinition)`：添加到注册中心

  * `getReaderContext().fireComponentRegistered`：发送注册完成事件

  

**说明：源码部分的笔记做的不一定适合所有人观看，作者采用流水线式的解析重要的代码，解析的结构类似于树状，如果视觉疲劳可以去网上参考一些博客和流程图学习源码。**



****



### IOC

#### 容器启动

Spring IOC 容器是 ApplicationContext 或者 BeanFactory，使用多个 Map 集合保存单实例 Bean，环境信息等资源，不同层级有不同的容器，比如整合 SpringMVC 的父子容器（先看 Bean 部分的源码解析再回看容器）

ClassPathXmlApplicationContext 与 AnnotationConfigApplicationContext 差不多：

```java
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
    this();
    register(annotatedClasses);// 解析配置类，封装成一个 BeanDefinitionHolder，并注册到容器
    refresh();// 加载刷新容器中的 Bean
}
```

```java
public AnnotationConfigApplicationContext() {
    // 注册 Spring 的注解解析器到容器
    this.reader = new AnnotatedBeanDefinitionReader(this);
    // 实例化路径扫描器，用于对指定的包目录进行扫描查找 bean 对象
    this.scanner = new ClassPathBeanDefinitionScanner(this);
}
```

AbstractApplicationContext.refresh()：

* prepareRefresh()：刷新前的**预处理**

  * `this.startupDate = System.currentTimeMillis()`：设置容器的启动时间
  * `initPropertySources()`：初始化一些属性设置，可以自定义个性化的属性设置方法
  * `getEnvironment().validateRequiredProperties()`：检查环境变量
  * `earlyApplicationEvents= new LinkedHashSet<ApplicationEvent>()`：保存容器中早期的事件

* obtainFreshBeanFactory()：获取一个**全新的 BeanFactory 接口实例**
  
  `refreshBeanFactory()`：创建 BeanFactory，设置序列化 ID、读取 BeanDefinition 并加载到工厂
  
  * `if (hasBeanFactory())`：applicationContext 内部拥有一个 beanFactory 实例，需要将该实例完全释放销毁
  * `destroyBeans()`：销毁原 beanFactory 实例，将 beanFactory 内部维护的单实例 bean 全部清掉，如果哪个 bean 实现了 Disposablejie接口，还会进行 bean distroy 方法的调用处理
    * `this.singletonsCurrentlyInDestruction = true`：设置当前 beanFactory 状态为销毁状态
    * `String[] disposableBeanNames`：获取销毁集合中的 bean，如果当前 bean 有**析构函数**就会在销毁集合
    * `destroySingleton(disposableBeanNames[i])`：遍历所有的 disposableBeans，执行销毁方法
      * `removeSingleton(beanName)`：清除三级缓存和 registeredSingletons 中的当前 beanName 的数据
      * `this.disposableBeans.remove(beanName)`：从销毁集合中清除，每个 bean 只能 destroy 一次
      * `destroyBean(beanName, disposableBean)`：销毁 bean
        * dependentBeanMap 记录了依赖当前 bean 的其他 bean 信息，因为依赖的对象要被回收了，所以依赖当前 bean 的其他对象都要执行 destroySingleton，遍历 dependentBeanMap 执行销毁
        * `bean.destroy()`：解决完成依赖后，执行 DisposableBean 的 destroy 方法
        * `this.containedBeanMap.remove(beanName)`：处理映射
        * ` this.dependenciesForBeanMap.remove(beanName)`：保存当前 bean 依赖了谁，直接清除
    * 进行一些集合和缓存的清理工作
  * `closeBeanFactory()`：将容器内部的 beanFactory 设置为空，重新创建
  * `beanFactory = createBeanFactory()`：创建新的 DefaultListableBeanFactory 对象
  * `beanFactory.setSerializationId(getId())`：进行 ID 的设置，可以根据 ID 获取 BeanFactory 对象
  * `customizeBeanFactory(beanFactory)`：设置是否允许覆盖和循环引用
  * `loadBeanDefinitions(beanFactory)`：**加载 BeanDefinition 信息，注册到 BeanFactory 中**
  * `this.beanFactory = beanFactory`：把 beanFactory 填充至容器中
  
  `getBeanFactory()`：返回创建的 DefaultListableBeanFactory 对象，该对象继承 BeanDefinitionRegistry
  
* prepareBeanFactory(beanFactory)：**BeanFactory 的预准备**工作，向容器中添加一些组件

  * `setBeanClassLoader(getClassLoader())`：给当前 bf 设置一个类加载器，加载 bd 的 class 信息
  * `setBeanExpressionResolver()`：设置 EL 表达式解析器
  * `addPropertyEditorRegistrar`：添加一个属性编辑器，解决属性注入时的格式转换
  * `addBeanPostProcessor()`：添加后处理器，主要用于向 bean 内部注入一些框架级别的实例
  * `ignoreDependencyInterface()`：设置忽略自动装配的接口，bean 内部的这些类型的字段   不参与依赖注入
  * `registerResolvableDependency()`：注册一些类型依赖关系
  * `addBeanPostProcessor()`：将配置的监听者注册到容器中，当前 bean 实现 ApplicationListener 接口就是**监听器事件**

* postProcessBeanFactory(beanFactory)：BeanFactory 准备工作完成后进行的后置处理工作，通过重写这个方法来在 BeanFactory 创建并预准备完成以后做进一步的设置

* invokeBeanFactoryPostProcessors(beanFactory)：**执行 BeanFactoryPostProcessor 的方法**

  * `processedBeans = new HashSet<>()`：存储已经执行过的 BeanFactoryPostProcessor 的 beanName

  * `if (beanFactory instanceof BeanDefinitionRegistry)`：**当前 BeanFactory 是 bd 的注册中心，bd 全部注册到 bf**

  * `for (BeanFactoryPostProcessor postProcessor : beanFactoryPostProcessors)`：遍历所有的 bf 后置处理器

  * `if (postProcessor instanceof BeanDefinitionRegistryPostProcessor)`：是 Registry 类的后置处理器

    `registryProcessor.postProcessBeanDefinitionRegistry(registry)`：向 bf 中注册一些 bd

    `registryProcessors.add(registryProcessor)`：添加到 registryProcessors 集合

  * `regularPostProcessors.add(postProcessor)`：添加到普通集合

  * 获取到所有 BeanDefinitionRegistryPostProcessor 和 BeanFactoryPostProcessor  接口类型了，首先回调 bdrpp 类

    * **执行实现了 PriorityOrdered（主排序接口）接口的 bdrpp，再执行实现了 Ordered（次排序接口）接口的 bdrpp**

    * **最后执行没有实现任何优先级或者是顺序接口 bdrpp**

      `boolean reiterate = true`：控制 while 是否需要再次循环，循环内是查找并执行 bdrpp 后处理器的 registry 相关的接口方法，接口方法执行以后会向 bf 内注册 bd，注册的 bd 也有可能是 bdrpp 类型，所以需要该变量控制循环

    * ` invokeBeanFactoryPostProcessors()`：BeanDefinitionRegistryPostProcessor  也继承了 BeanFactoryPostProcessor，也有 postProcessBeanFactory 方法，所以需要调用

  * 执行普通 BeanFactoryPostProcessor 的相关 postProcessBeanFactory 方法，同 bdrpp，按照主次无次序执行

  * `beanFactory.clearMetadataCache()`：清除缓存中合并的 bean 定义，因为后置处理器可能更改了元数据


**以上是 BeanFactory 的创建及预准备工作，接下来进入 Bean 的流程**

* registerBeanPostProcessors(beanFactory)：**注册 Bean 的后置处理器**，为了干预 Spring 初始化 bean 的流程，这里仅仅是向容器中**注入而非使用**

  * `beanFactory.getBeanNamesForType(BeanPostProcessor.class)`：**获取配置中实现了 BeanPostProcessor 接口类型**

  * `int beanProcessorTargetCount`：后置处理器的数量，已经注册的 + 未注册的 + 即将要添加的一个

  * `beanFactory.addBeanPostProcessor(new BeanPostProcessorChecker())`：添加一个检查器

    `BeanPostProcessorChecker.postProcessAfterInitialization()`：初始化后的后处理器方法

    * `!(bean instanceof BeanPostProcessor) `：当前 bean 类型是普通 bean，不是后置处理器
    * `!isInfrastructureBean(beanName)`：成立说明当前 beanName 是用户级别的 bean  不是 Spring 框架的
    * `this.beanFactory.getBeanPostProcessorCount() < this.beanPostProcessorTargetCount`：BeanFactory 上面注册后处理器数量 < 后处理器数量，说明后处理框架尚未初始化完成

  * `for (String ppName : postProcessorNames)`：遍历 PostProcessor 集合，**根据实现不同的顺序接口添加到不同集合**

  * `sortPostProcessors(priorityOrderedPostProcessors, beanFactory)`：实现 PriorityOrdered 接口的后处理器排序

    `registerBeanPostProcessors(beanFactory, priorityOrderedPostProcessors)`：**注册到 beanFactory 中**

  * 接着排序注册实现 Ordered 接口的后置处理器，然后注册普通的（ 没有实现任何优先级接口）后置处理器

  * 最后排序 MergedBeanDefinitionPostProcessor 类型的处理器，根据实现的排序接口，排序完注册到 beanFactory 中

  * `beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(applicationContext))`：重新注册 ApplicationListenerDetector 后处理器，用于在 Bean 创建完成后检查是否属于 ApplicationListener 类型，如果是就把 Bean 放到**监听器容器**中保存起来

* initMessageSource()：初始化 MessageSource 组件，主要用于做国际化功能，消息绑定与消息解析

  * `if (beanFactory.containsLocalBean(MESSAGE_SOURCE_BEAN_NAME))`：容器是否含有名称为 messageSource 的 bean
  * `beanFactory.getBean(MESSAGE_SOURCE_BEAN_NAME, MessageSource.class)`：如果有证明用户自定义了该类型的 bean，获取后直接赋值给 this.messageSource
  * `dms = new DelegatingMessageSource()`：容器中没有就新建一个赋值

* initApplicationEventMulticaster()：**初始化事件传播器**，在注册监听器时会用到

  * `if (beanFactory.containsLocalBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME))`：**条件成立说明用户自定义了事件传播器**，可以实现 ApplicationEventMulticaster 接口编写自己的事件传播器，通过 bean 的方式提供给 Spring
  * 如果有就直接从容器中获取；如果没有则创建一个 SimpleApplicationEventMulticaster 注册

* onRefresh()：留给用户去实现，可以硬编码提供一些组件，比如提供一些监听器

* registerListeners()：注册通过配置提供的 Listener，这些监听器最终注册到 ApplicationEventMulticaster 内

  * `for (ApplicationListener<?> listener : getApplicationListeners()) `：注册硬编码实现的监听器

  * `getBeanNamesForType(ApplicationListener.class, true, false)`：注册通过配置提供的 Listener

  * `multicastEvent(earlyEvent)`：**发布前面步骤产生的事件 applicationEvents**

    `Executor executor = getTaskExecutor()`：获取线程池，有线程池就异步执行，没有就同步执行

* finishBeanFactoryInitialization()：**实例化非懒加载状态的单实例**

  * `beanFactory.freezeConfiguration()`：**冻结配置信息**，就是冻结 BD 信息，冻结后无法再向 bf 内注册 bd

  * `beanFactory.preInstantiateSingletons()`：实例化 non-lazy-init singletons

    * `for (String beanName : beanNames)`：遍历容器内所有的 beanDefinitionNames

    * `getMergedLocalBeanDefinition(beanName)`：获取与父类合并后的对象（Bean → 获取流程部分详解此函数）

    * `if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit())`：BD 对应的 Class 满足非抽象、单实例，非懒加载，需要预先实例化

      `if (isFactoryBean(beanName))`：BD 对应的 Class 是 factoryBean 对象

      * `getBean(FACTORY_BEAN_PREFIX + beanName)`：获取工厂 FactoryBean 实例本身
      * `isEagerInit`：控制 FactoryBean 内部管理的 Bean 是否也初始化
      * `getBean(beanName)`：**初始化 Bean，获取 Bean 详解此函数**

      `getBean(beanName)`：不是工厂 bean 直接获取

    * `for (String beanName : beanNames)`：检查所有的 Bean 是否实现 SmartInitializingSingleton 接口，实现了就执行 afterSingletonsInstantiated()，进行一些创建后的操作

* `finishRefresh()`：完成刷新后做的一些事情，主要是启动生命周期

  * `clearResourceCaches()`：清空上下文缓存
  * `initLifecycleProcessor()`：**初始化和生命周期有关的后置处理器**
    * `if (beanFactory.containsLocalBean(LIFECYCLE_PROCESSOR_BEAN_NAME))`：成立说明自定义了生命周期处理器
    * `defaultProcessor = new DefaultLifecycleProcessor()`：Spring 默认提供的生命周期处理器
    * ` beanFactory.registerSingleton()`：将生命周期处理器注册到 bf 的一级缓存和注册单例集合中
  * `getLifecycleProcessor().onRefresh()`：获取该**生命周期后置处理器回调 onRefresh()**，调用 `startBeans(true)`
    * `lifecycleBeans = getLifecycleBeans()`：获取到所有实现了 Lifecycle 接口的对象包装到 Map 内，key 是beanName， value 是 Lifecycle 对象
    * `int phase = getPhase(bean)`：获取当前 Lifecycle 的 phase 值，当前生命周期对象可能依赖其他生命周期对象的执行结果，所以需要 **phase 决定执行顺序，数值越低的优先执行**
    * `LifecycleGroup group = phases.get(phase)`：把 phsae 相同的 Lifecycle 存入 LifecycleGroup
    * `if (group == null)`：group 为空则创建，初始情况下是空的
    * `group.add(beanName, bean)`：将当前 Lifecycle 添加到当前 phase 值一样的 group 内
    * `Collections.sort(keys)`：**从小到大排序，按优先级启动**
    * `phases.get(key).start()`：遍历所有的 Lifecycle 对象开始启动
    * `doStart(this.lifecycleBeans, member.name, this.autoStartupOnly)`：底层调用该方法启动
      * `bean = lifecycleBeans.remove(beanName)`： 确保 Lifecycle 只被启动一次，在一个分组内被启动了在其他分组内就看不到 Lifecycle 了
      * `dependenciesForBean = getBeanFactory().getDependenciesForBean(beanName)`：**获取当前即将被启动的 Lifecycle 所依赖的其他 beanName，需要先启动所依赖的 bean，才能启动自身**
      * `if ()`：传入的参数 autoStartupOnly 为 true 表示启动 isAutoStartUp 为 true 的 SmartLifecycle 对象，不会启动普通的生命周期的对象；false 代表全部启动
      * bean.start()：**调用启动方法**
  * `publishEvent(new ContextRefreshedEvent(this))`：**发布容器刷新完成事件**
  * `liveBeansView.registerApplicationContext(this)`：暴露 Mbean

补充生命周期 stop() 方法的调用

* DefaultLifecycleProcessor.stop()：调用 DefaultLifecycleProcessor.stopBeans()

  * 获取到所有实现了 Lifecycle 接口的对象并按 phase 数值分组的

  * `keys.sort(Collections.reverseOrder())`：按 phase 降序排序 Lifecycle 接口，最先启动的最晚关闭（责任链？）

  * `phases.get(key).stop()`：遍历所有的 Lifecycle 对象开始停止

    * `latch = new CountDownLatch(this.smartMemberCount)`：创建 CountDownLatch，设置 latch 内部的值为当前分组内的  smartMemberCount 的数量

    * `countDownBeanNames = Collections.synchronizedSet(new LinkedHashSet<>())`：保存当前正在处理关闭的smartLifecycle 的 BeanName

    * `for (LifecycleGroupMember member : this.members)`：处理本分组内需要关闭的 Lifecycle

      `doStop(this.lifecycleBeans, member.name, latch, countDownBeanNames)`：真正的停止方法

      * `getBeanFactory().getDependentBeans(beanName)`：**获取依赖当前 Lifecycle 的其他对象的 beanName**，因为当前的 Lifecycle 即将要关闭了，所有的依赖了当前 Lifecycle 的 bean 也要关闭
      * `countDownBeanNames.add(beanName)`：将当前 SmartLifecycle beanName 添加到 countDownBeanNames 集合内，该集合表示正在关闭的 SmartLifecycle

      * `bean.stop()`：调用停止的方法



***



#### 获取Bean

单实例：在容器启动时创建对象

多实例：在每次获取的时候创建对象

获取流程：**获取 Bean 时先从单例池获取，如果没有则进行第二次获取，并带上工厂类去创建并添加至单例池**

Java 启动 Spring 代码：

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
UserService userService = (UserService) context.getBean("userService");
```

AbstractBeanFactory.doGetBean()：获取 Bean，context.getBean() 追踪到此

* `beanName = transformedBeanName(name)`：name 可能是一个别名，重定向出来真实 beanName；也可能是一个 & 开头的 name，说明要获取的 bean 实例对象，是一个 FactoryBean 对象（IOC 原理 → 核心类）

  * `BeanFactoryUtils.transformedBeanName(name)`：判断是哪种 name，返回截取 & 以后的 name 并放入缓存
    * `transformedBeanNameCache.computeIfAbsent`：缓存是并发安全集合，key == null || value == null 时 put 成功 
    * do while 循环一直去除 & 直到不再含有 &
  * `canonicalName(name)`：aliasMap 保存别名信息，其中的 do while 逻辑是迭代查找，比如 A 别名叫做 B，但是 B 又有别名叫 C， aliasMap 为 {"C":"B", "B":"A"}，get(C) 最后返回的是  A

* `DefaultSingletonBeanRegistry.getSingleton()`：**第一次获取从缓存池获取**（循环依赖详解此代码）

  * 缓存中有数据进行 getObjectForBeanInstance() 获取可使用的 Bean（本节结束部分详解此函数）
  * 缓存中没有数据进行下面的逻辑进行创建

* `if(isPrototypeCurrentlyInCreation(beanName))`：检查 bean 是否在原型（Prototype）正在被创建的集合中，如果是就报错，说明产生了循环依赖，**原型模式解决不了循环依赖**

  原因：先加载 A，把 A 加入集合，A 依赖 B 去加载 B，B 又依赖 A，发现 A 在正在创建集合中，产生循环依赖

* `markBeanAsCreated(beanName)`：把 bean 标记为已经创建

* `mbd = getMergedLocalBeanDefinition(beanName)`：**获取合并父 BD 后的 BD 对象**，BD 是直接继承的，合并后的 BD 信息是包含父类的 BD 信息

  * `this.mergedBeanDefinitions.get(beanName)`：从缓存中获取

  * `if(bd.getParentName()==null)`：beanName 对应 BD 没有父 BD 就不用处理继承，封装为 RootBeanDefinition 返回

  * `parentBeanName = transformedBeanName(bd.getParentName())`：处理父 BD 的 name 信息

  * `if(!beanName.equals(parentBeanName))`：一般情况父子 BD 的名称不同

    `pbd = getMergedBeanDefinition(parentBeanName)`：递归调用，最终返回父 BD 的父 BD 信息

  * `mbd = new RootBeanDefinition(pbd)`：按照父 BD 信息创建 RootBeanDefinition 对象

  * `mbd.overrideFrom(bd)`：**子 BD 信息覆盖 mbd**，因为是要以子 BD 为基准，不存在的才去父 BD 寻找（**类似 Java 继承**）

  * `this.mergedBeanDefinitions.put(beanName, mbd)`：放入缓存

* `checkMergedBeanDefinition()`：判断当前 BD 是否为**抽象 BD**，抽象 BD 不能创建实例，只能作为父 BD 被继承

* `mbd.getDependsOn()`：获取 bean 标签 depends-on

* `if(dependsOn != null)`：**遍历所有的依赖加载**

  `isDependent(beanName, dep)`：判断循环依赖，出现循环依赖问题报错

  * 两个 Map：`<bean name="A" depends-on="B" ...>`

    * dependentBeanMap：记录依赖了当前 beanName 的其他 beanName（谁依赖我，我记录谁）
    * dependenciesForBeanMap：记录当前 beanName 依赖的其它 beanName 
    * 以 B 为视角 dependentBeanMap {"B"：{"A"}}，以 A 为视角 dependenciesForBeanMap {"A" :{"B"}}

  * `canonicalName(beanName)`：处理 bean 的 name

  * `dependentBeans = this.dependentBeanMap.get(canonicalName)`：获取依赖了当前 bean 的 name

  * `if (dependentBeans.contains(dependentBeanName))`：依赖了当前 bean 的集合中是否有该 name，有就产生循环依赖

  * 进行递归处理所有的引用：假如 `<bean name="A" dp="B"> <bean name="B" dp="C"> <bean name="C" dp="A">`

    ```java
    dependentBeanMap={A:{C}, B:{A}, C:{B}} 
    // C 依赖 A     		判断谁依赖了C				递归判断				谁依赖了B
    isDependent(C, A)  → C#dependentBeans={B} → isDependent(B, A); → B#dependentBeans={A} //返回true
    ```

  `registerDependentBean(dep, beanName)`：把 bean 和依赖注册到两个 Map 中，注意参数的位置，被依赖的在前

  `getBean(dep)`：**先加载依赖的 Bean**，又进入 doGetBean() 的逻辑

* `if (mbd.isSingleton())`：**判断 bean 是否是单例的 bean**

  `getSingleton(String, ObjectFactory<?>)`：**第二次获取，传入一个工厂对象**，这个方法更倾向于创建实例并返回

  ```java
  sharedInstance = getSingleton(beanName, () -> {
      return createBean(beanName, mbd, args);//创建，跳转生命周期
      //lambda表达式，调用了ObjectFactory的getObject()方法，实际回调接口实现的是 createBean()方法进行创建对象
  });
  ```

  * `singletonObjects.get(beanName)`：从一级缓存检查是否已经被加载，单例模式复用已经创建的bean

  * `this.singletonsCurrentlyInDestruction`：容器销毁时会设置这个属性为 true，这时就不能再创建 bean 实例了

  * `beforeSingletonCreation(beanName)`：检查构造参数的依赖，**构造参数产生的循环依赖无法解决**

    `!this.singletonsCurrentlyInCreation.add(beanName)`：将当前 beanName 放入到正在创建中单实例集合，放入成功说明没有产生循环依赖，失败则产生循环依赖，进入判断条件内的逻辑抛出异常

    原因：加载 A，向正在创建集合中添加了 {A}，根据 A 的构造方法实例化 A 对象，发现 A 的构造方法依赖 B，然后加载 B，B 构造方法的参数依赖于 A，又去加载 A 时来到当前方法，因为创建中集合已经存在 A，所以添加失败

  * `singletonObject = singletonFactory.getObject()`：**实例化 bean**（生命周期部分详解）

  * **创建完成以后，Bean 已经初始化好，是一个完整的可使用的 Bean**

  * `afterSingletonCreation(beanName)`：从正在创建中的集合中移出

  * `addSingleton(beanName, singletonObject)`：**添加一级缓存单例池中，从二级三级缓存移除**

  `bean = getObjectForBeanInstance`：**单实例可能是普通单实例或者 FactoryBean**，如果是 FactoryBean 实例，需要判断 name 是带 & 还是不带 &，带 & 说明 getBean 获取 FactoryBean 对象，否则是获取 FactoryBean 内部管理的实例

  * 参数 bean 是未处理 & 的 name，beanName 是处理过 & 和别名后的 name

  * `if(BeanFactoryUtils.isFactoryDereference(name))`：判断 name 前是否带 &

  * `if(!(beanInstance instanceof FactoryBean) || BeanFactoryUtils.isFactoryDereference(name))`：Bean 是普通单实例或者是 FactoryBean 就可以直接返回，否则进入下面的获取一个 **FactoryBean 内部管理的实例**的逻辑

  * `getCachedObjectForFactoryBean(beanName)`：尝试到缓存获取，获取到直接返回，获取不到进行下面逻辑

  * `if (mbd == null && containsBeanDefinition(beanName))`：Spring 中有当前 beanName 的 BeanDefinition 信息

    `mbd = getMergedLocalBeanDefinition(beanName)`：获取合并后的 BeanDefinition

  * `mbd.isSynthetic()`：默认值是 false 表示这是一个用户对象，如果是 true 表示是系统对象

  * `object = getObjectFromFactoryBean(factory, beanName, !synthetic)`：从工厂内获取实例

    * `factory.isSingleton() && containsSingleton(beanName)`：工厂内部维护的对象是单实例并且一级缓存存在该 bean
    * 首先去缓存中获取，获取不到就使用工厂获取然后放入缓存，进行循环依赖判断

* `else if (mbd.isPrototype())`：**bean 是原型的 bean**

  `beforePrototypeCreation(beanName)`：当前线程正在创建的原型对象 beanName 存入 prototypesCurrentlyInCreation

  * `curVal = this.prototypesCurrentlyInCreation.get()`：获取当前线程的正在创建的原型类集合
  * `this.prototypesCurrentlyInCreation.set(beanName)`：集合为空就把当前 beanName 加入
  * `if (curVal instanceof String)`：已经有线程相关原型类创建了，把当前的创建的加进去

  `createBean(beanName, mbd, args)`：创建原型类对象

  `afterPrototypeCreation(beanName)`：从正在创建中的集合中移除该 beanName， **与 beforePrototypeCreation逻辑相反**

* `convertIfNecessary()`：**依赖检查**，检查所需的类型是否与实际 bean 实例的类型匹配

* `return (T) bean`：返回创建完成的 bean




***



#### 生命周期

##### 四个阶段

Bean 的生命周期：实例化 instantiation，填充属性 populate，初始化 initialization，销毁 destruction

AbstractAutowireCapableBeanFactory.createBean()：进入 Bean 生命周期的流程

* `resolvedClass = resolveBeanClass(mbd, beanName)`：判断 mdb 中的 class 是否已经**加载到 JVM**，如果未加载则使用类加载器将 beanName 加载到 JVM中并返回 class 对象
* `if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null)`：条件成立封装 mbd 并把 resolveBeanClass 设置到 bd 中
  * 条件二：mbd 在 resolveBeanClass 之前是否有 class
  * 条件三：mbd 有 className
* `bean = resolveBeforeInstantiation(beanName, mbdToUse)`：实例化前的后置处理器返回一个代理实例对象（不是 AOP）
  * 自定义类继承 InstantiationAwareBeanPostProcessor，重写 postProcessBeforeInstantiation 方法，**方法逻辑为创建对象** 
  * 并配置文件 `<bean class="intefacePackage.MyInstantiationAwareBeanPostProcessor">` 导入为 bean
  * 条件成立，**短路操作**，直接 return bean

* `Object beanInstance = doCreateBean(beanName, mbdToUse, args)`：Do it

AbstractAutowireCapableBeanFactory.**doCreateBean**(beanName, RootBeanDefinition, Object[] args)：创建 Bean

* `BeanWrapper instanceWrapper = null`：**Spring 给所有创建的 Bean 实例包装成 BeanWrapper**，内部最核心的方法是获取实例，提供了一些额外的接口方法，比如属性访问器

* `instanceWrapper = this.factoryBeanInstanceCache.remove(beanName)`：单例对象尝试从缓存中获取，会移除缓存

* `createBeanInstance()`：**缓存中没有实例就进行创建实例**（逻辑复杂，下一小节详解）

* `if (!mbd.postProcessed)`：每个 bean 只进行一次该逻辑

  `applyMergedBeanDefinitionPostProcessors()`：后置处理器，合并 bd 信息，接下来要属性填充了

  `AutowiredAnnotationBeanPostProcessor.postProcessMergedBeanDefinition()`：后置处理逻辑**（@Autowired）**

  * `metadata = findAutowiringMetadata(beanName, beanType, null)`：提取出当前 beanType 类型整个继承体系内的 **@Autowired、@Value、@Inject** 信息，存入一个 InjectionMetadata 对象的 injectedElements 中并放入缓存

    * `metadata = buildAutowiringMetadata(clazz)`：查询当前 clazz 感兴趣的注解信息

      * `ReflectionUtils.doWithLocalFields()`：提取**字段**的注解的属性信息

        `findAutowiredAnnotation(field)`：代表感兴趣的注解就是那三种注解

      * `ReflectionUtils.doWithLocalMethods()`：提取**方法**的注解的属性信息

      * `do{} while (targetClass != null && targetClass != Object.class)`：循环从父类中解析，直到 Object 类

    * `this.injectionMetadataCache.put(cacheKey, metadata)`：存入缓存

  `mbd.postProcessed = true`：设置为 true，下次访问该逻辑不会再进入

* `earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences && isSingletonCurrentlyInCreation(beanName)`：单例、解决循环引用、是否在单例正在创建集合中

  ```java
  if (earlySingletonExposure) {
      //放入三级缓存
      addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
      //lamda 表达式，用来获取提前引用，循环依赖部分详解该逻辑
  }
  ```

* ` populateBean(beanName, mbd, instanceWrapper)`：**属性填充，依赖注入，整体逻辑是先处理标签再处理注解，填充至 pvs 中，最后通过 apply 方法最后完成属性依赖注入到 BeanWrapper **

  * `if (!ibp.postProcessAfterInstantiation(bw.getWrappedInstance(), beanName))`：实例化后的后置处理器，默认返回 true，自定义继承 InstantiationAwareBeanPostProcessor 修改返回值为 false，使 continueWithPropertyPopulation 为 false

  * `if (!continueWithPropertyPopulation)`：自定义方法返回值会造成该条件成立，逻辑为直接返回，不能进行依赖注入

  * `PropertyValues pvs = (mbd.hasPropertyValues() ? mbd.getPropertyValues() : null)`：处理依赖注入逻辑开始

  * `mbd.getResolvedAutowireMode() == ?`：**根据 bean 标签配置**的 autowire 判断是 **BY_NAME 或者 BY_TYPE**

    `autowireByName(beanName, mbd, bw, newPvs)`：根据字段名称去查找依赖的 bean

    * `propertyNames = unsatisfiedNonSimpleProperties(mbd, bw)`：bean 实例中有该字段和该字段的 setter 方法，但是在 bd 中没有 property 属性

      * 拿到配置的 property 信息和 bean 的所有字段信息

      * `pd.getWriteMethod() != null`：**当前字段是否有 setter 方法，配置类注入的方式需要 set 方法**

        `!isExcludedFromDependencyCheck(pd)`：当前字段类型是否在忽略自动注入的列表中

        `!pvs.contains(pd.getName()`：当前字段不在 xml 或者其他方式的配置中，也就是 bd 中不存在对应的 property

        `!BeanUtils.isSimpleProperty(pd.getPropertyType()`：是否是基本数据类型和内置的几种数据类型，基本数据类型不允许自动注入

    * `if (containsBean(propertyName))`：BeanFactory 中存在当前 property 的 bean 实例，说明找到对应的依赖数据

    * `getBean(propertyName)`：**拿到 propertyName 对应的 bean 实例**

    * `pvs.add(propertyName, bean)`：填充到 pvs 中

    * `registerDependentBean(propertyName, beanName))`：添加到两个依赖 Map（dependsOn）中

    `autowireByType(beanName, mbd, bw, newPvs)`：根据字段类型去查找依赖的 bean

    * `desc = new AutowireByTypeDependencyDescriptor(methodParam, eager)`：依赖描述信息
    * `resolveDependency(desc, beanName, autowiredBeanNames, converter)`：根据描述信息，查找依赖对象，容器中没有对应的实例但是有对应的 BD，会调用 getBean(Type) 获取对象

    `pvs = newPvs`：newPvs 是处理了依赖数据后的 pvs，所以赋值给 pvs

  * `hasInstAwareBpps`：表示当前是否有 InstantiationAwareBeanPostProcessors 的后置处理器

  * `pvsToUse = ibp.postProcessProperties(pvs, bw.getWrappedInstance(), beanName)`：**@Autowired 注解的注入**

    * `findAutowiringMetadata()`：包装着当前 bd 需要注入的注解信息集合，**三种注解的元数据**，直接缓存获取
    * `InjectionMetadata.InjectedElement.inject()`：将注解信息解析后注入到 pvs，方法和字段的注入的实现不同
      * `ReflectionUtils.makeAccessible()`：修改访问权限，true 代表暴力破解
      * `method.invoke()`：利用反射为此对象赋值

  * `applyPropertyValues()`：**将所有解析的 PropertyValues 的注入至 BeanWrapper 实例中**（深拷贝）

* `initializeBean(String,Object,RootBeanDefinition)`：**初始化，分为配置文件和实现接口两种方式**

  * `invokeAwareMethods(beanName, bean)`：根据 bean 是否实现 Aware 接口执行初始化的方法

  * `wrappedBean = applyBeanPostProcessorsBeforeInitialization`：初始化前的后置处理器，可以继承接口重写方法

    * `processor.postProcessBeforeInitialization()`：执行后置处理的方法，默认返回 bean 本身
    * `if (current == null) return result`：重写方法返回 null，会造成后置处理的短路，直接返回

  * `invokeInitMethods(beanName, wrappedBean, mbd)`：**反射执行初始化方法**

    * `isInitializingBean = (bean instanceof InitializingBean)`：初始化方法的定义有两种方式，一种是自定义类实现 InitializingBean 接口，另一种是配置文件配置 <bean id="..." class="..." init-method="init"/ >

    * `isInitializingBean && (mbd == null || !mbd.isExternallyManagedInitMethod("afterPropertiesSet"))`：

      * 条件一：当前 bean 是不是实现了 InitializingBean 

      * 条件二：InitializingBean 接口中的方法 afterPropertiesSet，判断该方法是否是容器外管理的方法

    * `if (mbd != null && bean.getClass() != NullBean.class)`：成立说明是配置文件的方式

      `if(!(接口条件))`表示**如果通过接口实现了初始化方法的话，就不会在调用配置类中 init-method 定义的方法**

      `((InitializingBean) bean).afterPropertiesSet()`：调用方法

      `invokeCustomInitMethod`：执行自定义的方法
      
      * `initMethodName = mbd.getInitMethodName()`：获取方法名
      * `Method initMethod = ()`：根据方法名获取到 init-method 方法
      * ` methodToInvoke = ClassUtils.getInterfaceMethodIfPossible(initMethod)`：将方法转成从接口层面获取
      * `ReflectionUtils.makeAccessible(methodToInvoke)`：访问权限设置成可访问
      * ` methodToInvoke.invoke(bean)`：**反射调用初始化方法**，以当前 bean 为角度去调用

  * `wrappedBean = applyBeanPostProcessorsAfterInitialization`：初始化后的后置处理器

    * `AbstractAutoProxyCreator.postProcessAfterInitialization()`：如果 Bean 被子类标识为要代理的 bean，则使用配置的拦截器**创建代理对象**，AOP 部分详解

    * 如果不存在循环依赖，创建动态代理 bean 在此处完成；否则真正的创建阶段是在属性填充时获取提前引用的阶段，**循环依赖**详解，源码分析：

      ```java
      // 该集合用来避免重复将某个 bean 生成代理对象，
      private final Map<Object, Object> earlyProxyReferences = new ConcurrentHashMap<>(16);
      
      public Object postProcessAfterInitialization(@Nullable Object bean,String bN){
          if (bean != null) {
              // cacheKey 是 beanName 或者加上 &
              Object cacheKey = getCacheKey(bean.getClass(), beanName);y
                  if (this.earlyProxyReferences.remove(cacheKey) != bean) {
                      //去提前代理引用池中寻找该key，不存在则创建代理
                      //如果存在则证明被代理过，则判断是否是当前的 bean，不是则创建代理
                      return wrapIfNecessary(bean, bN, cacheKey);
                  }
          }
          return bean;
      }
      ```

* `if (earlySingletonExposure)`：是否循序提前引用

  `earlySingletonReference = getSingleton(beanName, false)`：**从二级缓存获取实例**，放入一级缓存是在 doGetBean 中的sharedInstance = getSingleton() 方法中，此时在 createBean 的逻辑还没有返回。

  `if (earlySingletonReference != null)`：当前 bean 实例从二级缓存中获取到了，说明产生了循环依赖，在属性填充阶段会提前调用三级缓存中的工厂生成 Bean 对象的动态代理，放入二级缓存中，然后使用原始 bean 继续执行初始化

  * ` if (exposedObject == bean)`：初始化后的 bean == 创建的原始实例，条件成立的两种情况：当前的真实实例不需要被代理；当前实例已经被代理过了，初始化时的后置处理器直接返回 bean 原实例

    `exposedObject = earlySingletonReference`：**把代理后的 Bean 传给 exposedObject 用来返回，因为只有代理对象才封装了增强的拦截器链，main 方法中用代理对象调用方法时，会进行增强，代理是对原始对象的包装，所以这里返回的代理对象中含有完整的原实例（属性填充和初始化后的），是一个完整的代理对象**

  * `else if (!this.allowRawInjectionDespiteWrapping && hasDependentBean(beanName))`：是否有其他 bean 依赖当前 bean，执行到这里说明是不存在循环依赖、存在增强代理的逻辑，也就是正常的逻辑

    * `dependentBeans = getDependentBeans(beanName)`：取到依赖当前 bean 的其他 beanName

    * `if (!removeSingletonIfCreatedForTypeCheckOnly(dependentBean))`：判断 dependentBean 是否创建完成

      * `if (!this.alreadyCreated.contains(beanName))`：成立当前 bean 尚未创建完成，当前 bean 是依赖exposedObject 的 bean，返回 true
    * `return false`：创建完成返回 false
  
      `actualDependentBeans.add(dependentBean)`：创建完成的 dependentBean 加入该集合

    * `if (!actualDependentBeans.isEmpty())`：条件成立说明有依赖于当前 bean 的 bean 实例创建完成，但是当前的 bean 还没创建完成返回，依赖当前 bean 的外部 bean 持有的是不完整的 bean，所以需要报错

* `registerDisposableBeanIfNecessary`：判断当前 bean 是否需要注册析构回调，当容器销毁时进行回调

  * `if (!mbd.isPrototype() && requiresDestruction(bean, mbd))`

    * 如果是原型 prototype 不会注册析构回调，不会回调该函数，对象的回收由 JVM 的 GC 机制完成

    * requiresDestruction：

      `DisposableBeanAdapter.hasDestroyMethod(bean, mbd)`：bd 中定义了 DestroyMethod 返回 true

      `hasDestructionAwareBeanPostProcessors()`：后处理器框架决定是否进行析构回调

  * `registerDisposableBean()`：条件成立进入该方法，给当前单实例注册回调适配器，适配器内根据当前 bean 实例是继承接口（DisposableBean）还是自定义标签来判定具体调用哪个方法实现

    * `this.disposableBeans.put(beanName, bean)`：向销毁集合添加实例



****



##### 创建实例

AbstractAutowireCapableBeanFactory.createBeanInstance(beanName, RootBeanDefinition, Object[] args)

* `resolveBeanClass(mbd, beanName)`：确保 Bean 的 Class 真正的被加载

* 判断类的访问权限是不是 public，不是进入下一个判断，是否允许访问类的 non-public 的构造方法，不允许则报错

* `Supplier<?> instanceSupplier = mbd.getInstanceSupplier()`：获取创建实例的函数，可以自定义，没有进入下面的逻辑

* `if (mbd.getFactoryMethodName() != null)`：**判断 bean 是否设置了 factory-method 属性**

  <bean class="" factory-method="">，设置了该属性进入 factory-method 方法创建实例

* `resolved = false`：代表 bd 对应的构造信息是否已经解析成可以反射调用的构造方法

* `autowireNecessary = false`：是否自动匹配构造方法

* `if(mbd.resolvedConstructorOrFactoryMethod != null)`：获取 bd 的构造信息转化成反射调用的 method 信息

  * method 为 null 则 resolved 和 autowireNecessary 都为默认值 false
  * `autowireNecessary = mbd.constructorArgumentsResolved`：构造方法有参数，设置为 true

* **bd 对应的构造信息解析完成，可以直接反射调用构造方法了**：

  * `return autowireConstructor(beanName, mbd, null, null)`：**有参构造**，根据参数匹配最优的构造器创建实例

  * `return instantiateBean(beanName, mbd)`：**无参构造方法通过反射创建实例**

* `ctors = determineConstructorsFromBeanPostProcessors(beanClass, beanName)`：@Autowired 注解对应的后置处理器**AutowiredAnnotationBeanPostProcessor 逻辑**

  * 配置了 lookup 的相关逻辑

  * `this.candidateConstructorsCache.get(beanClass)`：从缓存中获取构造方法，第一次获取为 null，进入下面逻辑

  * `rawCandidates = beanClass.getDeclaredConstructors()`：获取所有的构造器

  * `Constructor<?> requiredConstructor = null`：唯一的选项构造器，**@Autowired(required = "true")** 时有值

  * `for (Constructor<?> candidate : rawCandidates)`：遍历所有的构造器：

    `ann = findAutowiredAnnotation(candidate)`：有三种注解中的一个会返回注解的属性

    * 遍历 this.autowiredAnnotationTypes 中的三种注解：

      ```java
      this.autowiredAnnotationTypes.add(Autowired.class);//！！！！！！！！！！！！！！
      this.autowiredAnnotationTypes.add(Value.class);
      this.autowiredAnnotationTypes.add(...ClassUtils.forName("javax.inject.Inject"));
      ```

    * ` AnnotatedElementUtils.getMergedAnnotationAttributes(ao, type)`：获取注解的属性

    * `if (attributes != null) return attributes`：任意一个注解属性不为空就注解返回

    `if (ann == null)`：注解属性为空

    * `userClass = ClassUtils.getUserClass(beanClass)`：如果当前 beanClass 是代理对象，方法上就已经没有注解了，所以**获取原始的用户类型重新获取该构造器上的注解属性**（**事务注解失效**也是这个原理）

    `if (ann != null)`：注解属性不为空了

    * `required = determineRequiredStatus(ann)`：获取 required 属性的值

      * `!ann.containsKey(this.requiredParameterName) || `：判断属性是否包含 required，不包含进入后面逻辑
      * `this.requiredParameterValue == ann.getBoolean(this.requiredParameterName)`：获取属性值返回

    * `if (required)`：代表注解 @Autowired(required = true)

      `if (!candidates.isEmpty())`：true 代表只能有一个构造方法，构造集合不是空代表可选的构造器不唯一，报错

      `requiredConstructor = candidate`：把构造器赋值给 requiredConstructor

    * `candidates.add(candidate)`：**把当前构造方法添加至 candidates 集合**

    ` if(candidate.getParameterCount() == 0)`：当前遍历的构造器的参数为 0 代表没有参数，是**默认构造器**，赋值给 defaultConstructor 

  * `candidateConstructors = candidates.toArray(new Constructor<?>[0])`：**将构造器转成数组返回**

* `if(ctors != null)`：条件成立代表指定了**构造方法数组**

  `mbd.getResolvedAutowireMode() == AUTOWIRE_CONSTRUCTOR`：<bean autowire=""> 标签内 autowiremode 的属性值，默认是 no，AUTOWIRE_CONSTRUCTOR 代表选择最优的构造方法

  `mbd.hasConstructorArgumentValues()`：bean 信息中是否配置了构造参数的值

  `!ObjectUtils.isEmpty(args)`：getBean 时，指定了参数 arg

* `return autowireConstructor(beanName, mbd, ctors, args)`：**选择最优的构造器进行创建实例**（复杂，不建议研究）

  * `beanFactory.initBeanWrapper(bw)`：向 BeanWrapper 中注册转换器，向工厂中注册属性编辑器

  * `Constructor<?> constructorToUse = null`：实例化反射构造器

    `ArgumentsHolder argsHolderToUse`：实例化时真正去用的参数，并持有对象

    * rawArguments 是转换前的参数，arguments 是类型转换完成的参数

    `Object[] argsToUse`：参数实例化时使用的参数

  * `Object[] argsToResolve`：表示构造器参数做转换后的参数引用

  * `if (constructorToUse != null && mbd.constructorArgumentsResolved)`：

    * 条件一成立说明当前 bd 生成的实例不是第一次，缓存中有解析好的构造器方法可以直接拿来反射调用
    * 条件二成立说明构造器参数已经解析过了

  * `argsToUse = resolvePreparedArguments()`：argsToResolve 不是完全解析好的，还需要继续解析

  * `if (constructorToUse == null || argsToUse == null)`：条件成立说明缓存机制失败，进入构造器匹配逻辑

  * `Constructor<?>[] candidates = chosenCtors`：chosenCtors  只有在构造方法上有 autowaire 三种注解时才有数据

  * `if (candidates == null)`：candidates 为空就根据 beanClass 是否允许访问非公开的方法来获取构造方法

  * `if (candidates.length == 1 && explicitArgs == null && !mbd.hasConstructorArgumentValues())`：默认无参

    `bw.setBeanInstance(instantiate())`：**使用无参构造器反射调用，创建出实例对象，设置到 BeanWrapper 中去**

  * `boolean autowiring`：**需要选择最优的构造器**

  * `cargs = mbd.getConstructorArgumentValues()`：获取参数值

    `resolvedValues = new ConstructorArgumentValues()`：获取已经解析后的构造器参数值

    * `final Map<Integer, ValueHolder> indexedArgumentValues`：key 是 index， value 是值
    * `final List<ValueHolder> genericArgumentValues`：没有 index 的值

    `minNrOfArgs = resolveConstructorArguments(..,resolvedValues)`：从 bd 中解析并获取构造器参数的个数

    * `valueResolver.resolveValueIfNecessary()`：将引用转换成真实的对象
    * `resolvedValueHolder.setSource(valueHolder)`：将对象填充至 ValueHolder 中
    * ` resolvedValues.addIndexedArgumentValue()`：将参数值封装至 resolvedValues 中

  * `AutowireUtils.sortConstructors(candidates)`：排序规则 public > 非公开的 > 参数多的 > 参数少的

  * ` int minTypeDiffWeight = Integer.MAX_VALUE`：值越低说明构造器**参数列表类型**和构造参数的匹配度越高

  * `Set<Constructor<?>> ambiguousConstructors`：模棱两可的构造器，两个构造器匹配度相等时放入

  * `for (Constructor<?> candidate : candidates)`：遍历筛选出 minTypeDiffWeight 最低的构造器

  * `Class<?>[] paramTypes = candidate.getParameterTypes()`：获取当前处理的构造器的参数类型

  * `if()`：candidates 是排过序的，当前筛选出来的构造器的优先级一定是优先于后面的 constructor

  * `if (paramTypes.length < minNrOfArgs)`：需求的小于给的，不匹配

  * `int typeDiffWeight`：获取匹配度

    * `mbd.isLenientConstructorResolution()`：true 表示 ambiguousConstructors 允许有数据，false 代表不允许有数据，有数据就报错（LenientConstructorResolution：宽松的构造函数解析）
    * `argsHolder.getTypeDifferenceWeight(paramTypes)`：选择参数转换前和转换后匹配度最低的，循环向父类中寻找该方法，直到寻找到 Obejct 类

  * ` if (typeDiffWeight < minTypeDiffWeight)`：条件成立说明当前循环处理的构造器更优

  * `else if (constructorToUse != null && typeDiffWeight == minTypeDiffWeight)`：当前处理的构造器的计算出来的 DiffWeight 与上一次筛选出来的最优构造器的值一致，说明有模棱两可的情况

  * `if (constructorToUse == null)`：未找到可以使用的构造器，报错

  * ` else if (ambiguousConstructors != null && !mbd.isLenientConstructorResolution())`：模棱两可有数据，LenientConstructorResolution == false，所以报错

  * `argsHolderToUse.storeCache(mbd, constructorToUse)`：匹配成功，进行缓存，方便后来者使用该 bd 实例化

  * ` bw.setBeanInstance(instantiate(beanName, mbd, constructorToUse, argsToUse))`：匹配成功调用 instantiate 创建出实例对象，设置到 BeanWrapper 中去

* `return instantiateBean(beanName, mbd)`：默认走到这里

  * `SimpleInstantiationStrategy.instantiate()`：**真正用来实例化的函数**（无论如何都会走到这一步）

    * `if (!bd.hasMethodOverrides())`：没有方法重写覆盖

      `BeanUtils.instantiateClass(constructorToUse)`：调用 `java.lang.reflect.Constructor.newInstance()` 实例化
  
    * `instantiateWithMethodInjection(bd, beanName, owner)`：有方法重写采用 CGLIB  实例化
    
  * `BeanWrapper bw = new BeanWrapperImpl(beanInstance)`：包装成 BeanWrapper 类型的对象
  
  * `return bw`：返回实例



****



#### 循环依赖

##### 循环引用

循环依赖：是一个或多个对象实例之间存在直接或间接的依赖关系，这种依赖关系构成一个环形调用

Spring 循环依赖有三种：

* 原型模式循环依赖【无法解决】
* 单例 Bean 循环依赖：构造参数产生依赖【无法解决】
* 单例 Bean 循环依赖：setter产生依赖【可以解决】

解决循环依赖：提前引用，提前暴露创建中的 Bean

* Spring 先实例化 A，拿到 A 的构造方法反射创建出来 A 的早期实例对象，这个对象被包装成 ObjectFactory 对象，放入三级缓存
* 处理 A 的依赖数据，检查发现 A 依赖 B 对象，所以 Spring 就会去根据 B 类型到容器中去 getBean(B.class)，这里产生递归
* 拿到 B 的构造方法，进行反射创建出来 B 的早期实例对象，也会把 B 包装成 ObjectFactory 对象，放到三级缓存，处理 B 的依赖数据，检查发现 B 依赖了 A 对象，然后 Spring 就会去根据A类型到容器中去 getBean(A.class)
* 这时获取到 A 的早期对象进入属性填充

循环依赖的三级缓存：

```java
//一级缓存：存放所有初始化完成单实例bean，单例池，key是beanName，value是对应的单实例对象引用
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);

//二级缓存：存放实例化未进行初始化的Bean，提前引用池
private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);

/** Cache of singleton factories: bean name to ObjectFactory. 3*/
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
```

* 为什么需要三级缓存？

  * 循环依赖解决需要提前引用动态代理对象，AOP 动态代理是在 Bean 初始化后的后置处理中进行，这时的 bean 已经是成品对象，需要提前进行动态代理，三级缓存的 ObjectFactory 提前产生需要代理的对象
  * 若存在循环依赖，**后置处理不创建代理对象，真正创建代理对象的过程是在 getBean(B) 的阶段中**

* 三级缓存一定会创建提前引用吗？

  * 出现循环依赖就会去三级缓存获取提前引用，不出现就不会
  * 如果当前有增强方法，就创建代理对象放入二级缓存，如果没有代理对象就返回 createBeanInstance 创建的实例

* wrapIfNecessary 一定创建代理对象吗？（AOP 动态代理部分有源码解析）

  * 存在增强器会创建动态代理，不需要增强就不需要创建动态代理对象
  * 不创建就会把最原始的实例化的Bean放到二级缓存，因为 addSingletonFactory 参数中传入了实例化的Bean，在singletonFactory.getObject() 中返回给 singletonObject，放入二级缓存

* 什么时候将 Bean 的引用提前暴露给第三级缓存的 ObjectFactory 持有？

  * 实例化之后，依赖注入之前

    ```java
    createBeanInstance --> addSingletonFactory --> populateBean
    ```

    



***



##### 源码解析

假如 A 依赖 B，B 依赖 A

* 当 A 创建实例后填充属性前，执行：

  ````java
  addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean))
  ````

  ```java
  // 添加给定的单例工厂以构建指定的单例
  protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
      Assert.notNull(singletonFactory, "Singleton factory must not be null");
      synchronized (this.singletonObjects) {
          //单例池包含该Bean说明已经创建完成，不需要循环依赖
          if (!this.singletonObjects.containsKey(beanName)) {
              this.singletonFactories.put(beanName,singletonFactory);//加入三级缓存
              this.earlySingletonObjects.remove(beanName);
              //从二级缓存移除，因为三个Map中都是一个对象，不能同时存在！
              this.registeredSingletons.add(beanName);
          }
      }
  }
  ```

* 填充属性时 A 依赖 B，这时需要 getBean(B)，也会把 B 的工厂放入三级缓存，接着 B 填充属性时发现依赖 A，去进行**第一次 ** getSingleton(A)

  ```java
  public Object getSingleton(String beanName) {
      return getSingleton(beanName, true);//为true代表允许拿到早期引用。
  }
  protected Object getSingleton(String beanName, boolean allowEarlyReference) {
      // 在一级缓存中获取 beanName 对应的单实例对象。
      Object singletonObject = this.singletonObjects.get(beanName);
      //条件一成立：单实例确实尚未创建；单实例正在创建，发生了循环依赖
      //条件二成立：代表单实例正在创建
      if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
          synchronized (this.singletonObjects) {
              //从二级缓存获取
              singletonObject = this.earlySingletonObjects.get(beanName);
              //二级缓存不存在，并且允许获取早期实例对象，去三级缓存查看
              if (singletonObject == null && allowEarlyReference) {
                  ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                  if (singletonFactory != null) {
                      //从三级缓存获取工厂对象，并得到bean的提前引用
                      singletonObject = singletonFactory.getObject();
                      //缓存升级，放入二级缓存，提前引用池
                      this.earlySingletonObjects.put(beanName, singletonObject);
                      //从三级缓存移除该对象
                      this.singletonFactories.remove(beanName);
                  }
              }
          }
      }
      return singletonObject;
  }
  ```

* 从三级缓存获取 A 的 Bean：`singletonFactory.getObject()`，调用了 Lambda 表达式的 getEarlyBeanReference 方法：

  ```java
  public Object getEarlyBeanReference(Object bean, String beanName) {
      Object cacheKey = getCacheKey(bean.getClass(), beanName);
      this.earlyProxyReferences.put(cacheKey, bean);
      //向提前引用代理池 earlyProxyReferences 中添加该Bean，防止对象被重新代理
      return wrapIfNecessary(bean, beanName, cacheKey);
  	//创建代理对象，createProxy
  }
  ```

* B 填充了 A 的提前引用后会继续初始化直到完成，**返回原始 A 的逻辑继续执行，这时的 A 还不是代理后增强的 A**



***



### AOP

#### 注解原理

@EnableAspectJAutoProxy：AOP 注解驱动，给容器中导入 AspectJAutoProxyRegistrar

```java
@Import(AspectJAutoProxyRegistrar.class)
public @interface EnableAspectJAutoProxy {
    // 是否强制使用 CGLIB 创建代理对象 
    // 配置文件方式：<aop:aspectj-autoproxy proxy-target-class="true"/>
	boolean proxyTargetClass() default false;
	
    // 将当前代理对象暴露到上下文内，方便代理对象内部的真实对象拿到代理对象
    // 配置文件方式：<aop:aspectj-autoproxy expose-proxy="true"/>
	boolean exposeProxy() default false;
}
```

AspectJAutoProxyRegistrar 在用来向容器中注册 **AnnotationAwareAspectJAutoProxyCreator**，以 BeanDefiantion 形式存在，在容器初始化时加载。AnnotationAwareAspectJAutoProxyCreator 间接实现了 InstantiationAwareBeanPostProcessor，Order 接口，该类会在 Bean 的实例化和初始化的前后起作用

工作流程：创建 IOC 容器，调用 refresh() 刷新容器，`registerBeanPostProcessors(beanFactory)` 阶段，通过 getBean() 创建 AnnotationAwareAspectJAutoProxyCreator 对象，在生命周期的初始化方法中执行回调 initBeanFactory() 方法初始化注册三个工具类：BeanFactoryAdvisorRetrievalHelperAdapter、ReflectiveAspectJAdvisorFactory、BeanFactoryAspectJAdvisorsBuilderAdapter



***



#### 动态代理

##### 后置处理

Bean 初始化完成的执行后置处理器的方法：

```java
public Object postProcessAfterInitialization(@Nullable Object bean,String bN){
    if (bean != null) {
        // cacheKey 是 beanName 或者加上 &
        Object cacheKey = getCacheKey(bean.getClass(), beanName);
            if (this.earlyProxyReferences.remove(cacheKey) != bean) {
                //去提前代理引用池中寻找该key，不存在则创建代理
                //如果存在则证明被代理过，则判断是否是当前的 bean，不是则创建代理
                return wrapIfNecessary(bean, bN, cacheKey);
            }
    }
    return bean;
}
```

AbstractAutoProxyCreator.wrapIfNecessary()：根据通知创建动态代理，没有通知直接返回原实例

```java
protected Object wrapIfNecessary(Object bean, String beanName, Object cacheKey) {
    //条件一般不成立，很少使用 TargetSourceCreator 去创建对象 BeforeInstantiation 阶段，doCreateBean 之前的阶段
    if (StringUtils.hasLength(beanName) && this.targetSourcedBeans.contains(beanName)) {
        return bean;
    }
    // advisedBeans 集合保存的是 bean 是否被增强过了
    // 条件成立说明当前 beanName 对应的实例不需要被增强处理，判断是在 BeforeInstantiation 阶段做的
    if (Boolean.FALSE.equals(this.advisedBeans.get(cacheKey))) {
        return bean;
    }
    //条件一：判断当前 bean 类型是否是基础框架类型，这个类的实例不能被增强
    //条件二：shouldSkip 判断当前 beanName 是否是 .ORIGINAL 结尾，如果是就跳过增强逻辑，直接返回
    if (isInfrastructureClass(bean.getClass()) || shouldSkip(bean.getClass(), beanName)) {
        this.advisedBeans.put(cacheKey, Boolean.FALSE);
        return bean;
    }

    // 查找适合当前 bean 实例 Class 的通知（下一节详解）
    Object[] specificInterceptors = getAdvicesAndAdvisorsForBean(bean.getClass(), beanName, null);
    //条件成立说明上面方法查询到适合当前class的通知
    if (specificInterceptors != DO_NOT_PROXY) {
        this.advisedBeans.put(cacheKey, Boolean.TRUE);
        //根据查询到的增强创建代理对象（下一节详解）
        //参数一：目标对象
        //参数二：beanName
        //参数三：匹配当前目标对象 clazz 的 Advisor 数据
        Object proxy = createProxy(
            bean.getClass(), beanName, specificInterceptors, new SingletonTargetSource(bean));
        //保存代理对象类型
        this.proxyTypes.put(cacheKey, proxy.getClass());
        //返回代理对象
        return proxy;
    }
	// 执行到这里说明没有查到通知，当前 bean 不需要增强
    this.advisedBeans.put(cacheKey, Boolean.FALSE);
    //返回原始的 bean 实例
    return bean;
}
```



***



##### 获取通知

AbstractAdvisorAutoProxyCreator.getAdvicesAndAdvisorsForBean()：查找适合当前实例的增强

```java
protected Object[] getAdvicesAndAdvisorsForBean(Class<?> beanClass, String beanName, @Nullable TargetSource targetSource) {
	// 查询适合当前类型的增强通知
    List<Advisor> advisors = findEligibleAdvisors(beanClass, beanName);
    if (advisors.isEmpty()) {
        //增强为空直接返回 null，不需要创建代理
        return DO_NOT_PROXY;
    }
    // 不是空，转成数组返回
    return advisors.toArray();
}
```

AbstractAdvisorAutoProxyCreator.findEligibleAdvisors()：

* `candidateAdvisors = findCandidateAdvisors()`：**获取当前容器内可以使用（所有）的 advisor**，调用的是 AnnotationAwareAspectJAutoProxyCreator 类的方法

  * `advisors = super.findCandidateAdvisors()`：查询出所有 Advisor 类型

    * `advisorNames = BeanFactoryUtils.beanNamesForTypeIncludingAncestors()`：通过 BF 查询出来 BD 配置的 class 中 是 **Advisor 子类的 BeanName**
    * `advisors.add()`：使用 Spring 容器获取当前这个 Advisor 类型的实例

  * `advisors.addAll(this.aspectJAdvisorsBuilder.buildAspectJAdvisors())`：获取添加 @Aspect 注解类中的 Advisor

    `buildAspectJAdvisors()`：构建的方法，**把 Advice 封装成 Advisor**（逻辑很绕，不建议深究）

    * ` beanNames = BeanFactoryUtils.beanNamesForTypeIncludingAncestors(this.beanFactory, Object.class, true, false)`：获取出容器内 Object 所有的 beanName，就是全部的

    * ` for (String beanName : beanNames)`：遍历所有的 beanName，判断每个 beanName 对应的 class 是否是 Aspect 类型，就是**加了 @Aspect 注解的类**

      * `factory = new BeanFactoryAspectInstanceFactory(this.beanFactory, beanName)`：使用工厂模式管理 Aspect 的元数据，关联的真实 @Aspect 注解的实例对象

      * `classAdvisors = this.advisorFactory.getAdvisors(factory)`：获取当前添加了 @Aspect 注解的 class 的 Advisor 相关信息

        * aspectClass：@Aspect 标签的类的 class

        * `for (Method method : getAdvisorMethods(aspectClass))`：遍历不包括 @Pointcut 注解的方法

        * `Advisor advisor = getAdvisor(method, lazySingletonAspectInstanceFactory, advisors.size(), aspectName)`：将当前 method 包装成 Advisor 数据

          * `AspectJExpressionPointcut expressionPointcut = getPointcut()`：获取切点表达式

          * `return new InstantiationModelAwarePointcutAdvisorImpl()`：把 method 中 Advice 包装成 Advisor，Spring 中每个 Advisor 内部一定是持有一个 Advice 的，Advice 内部最重要的数据是当前 method 和aspectInstanceFactory，工厂用来获取实例

            `this.instantiatedAdvice = instantiateAdvice(this.declaredPointcut)`：实例化 Advice 对象

            * `this.aspectJAdvisorFactory.getAdvice()`：获取 Advice，逻辑是获取注解信息，根据注解的不同生成对应的 Advice 对象

      * `advisors.addAll(classAdvisors)`：保存通过 @Aspect 注解定义的 Advisor 数据

    * `this.aspectBeanNames = aspectNames`：将所有 @Aspect 注解 beanName 缓存起来，表示提取 Advisor 工作完成

    * `return advisors`：返回 Advisor 列表

* `eligibleAdvisors = findAdvisorsThatCanApply(candidateAdvisors, beanClass, beanName)`：**选出适合当前类型的增强**

  * `if (candidateAdvisors.isEmpty())`：条件成立说明当前 Spring 没有可以操作的 Advisor

  * `List<Advisor> eligibleAdvisors = new ArrayList<>()`：匹配当前 clazz 的 Advisors 信息

  * `for (Advisor candidate : candidateAdvisors)`：遍历所有的 AdvisorIntroduction

    ` if (canApply(candidate, clazz, hasIntroductions))`：判断遍历的 advisor 是否匹配当前的 class，匹配就加入集合

    * `if (advisor instanceof PointcutAdvisor)`：创建的 advisor 是 InstantiationModelAwarePointcutAdvisorImpl 类型

      `PointcutAdvisor pca = (PointcutAdvisor) advisor`：封装当前 Advisor

      `return canApply(pca.getPointcut(), targetClass, hasIntroductions)`：重载该方法

      * `if (!pc.getClassFilter().matches(targetClass))`：条件成立说明不满足切点定义，直接返回 false
      * `methodMatcher = pc.getMethodMatcher()`：**获取方法匹配器**
      * `Set<Class<?>> classes`：保存目标对象 class 和目标对象父类超类的接口和自身实现的接口
      * `if (!Proxy.isProxyClass(targetClass))`：判断当前实例是不是代理类，确保 class 内存储的数据包括目标对象的class  而不是代理类的 class
      * `for (Class<?> clazz : classes)`：检查目标 class 和上级接口的所有方法，查看是否会被方法匹配器匹配，如果有一个方法匹配成功，就说明目标对象 AOP 代理需要增强
        * `specificMethod = AopUtils.getMostSpecificMethod(method, targetClass)`：方法可能是接口的，判断当前类有没有该方法
        * `return (specificMethod != method && matchesMethod(specificMethod))`：类和方法的匹配，不包括参数（静态匹配）

* `extendAdvisors(eligibleAdvisors)`：在 eligibleAdvisors 列表的索引 0 的位置添加 DefaultPointcutAdvisor，**封装了 ExposeInvocationInterceptor 拦截器**

* ` eligibleAdvisors = sortAdvisors(eligibleAdvisors)`：**对拦截器链进行排序**，数值越小优先级越高，高的排在前面
  
  * 实现 Ordered 或 PriorityOrdered 接口，PriorityOrdered 的级别要优先于 Ordered，使用 OrderComparator 比较器
  * 使用 @Order（Spring 规范）或 @Priority（JDK 规范）注解，使用 AnnotationAwareOrderComparator 比较器
  * ExposeInvocationInterceptor 实现了 PriorityOrdered ，所以总是排在第一位，MethodBeforeAdviceInterceptor 没实现任何接口，所以优先级最低，排在最后
* `return eligibleAdvisors`：返回拦截器链



****



##### 创建代理

AbstractAutoProxyCreator.createProxy()：根据增强方法创建代理对象

* `ProxyFactory proxyFactory = new ProxyFactory()`：**无参构造 ProxyFactory**，讲解一下两种有参构造方法：

  * public ProxyFactory(Object target)：

    ```java
    public ProxyFactory(Object target) {
    	// 将目标对象封装成 SingletonTargetSource 保存到父类的字段中
       	setTarget(target);
        // 获取目标对象 class 所有接口保存到 AdvisedSupport 中的 interfaces 集合中
       	setInterfaces(ClassUtils.getAllInterfaces(target));
    }
    ```

    ClassUtils.getAllInterfaces(target) 底层调用 getAllInterfacesForClassAsSet(java.lang.Class<?>, java.lang.ClassLoader)：

    *  `if (clazz.isInterface() && isVisible(clazz, classLoader))`：
       * 条件一：判断当前目标对象是接口
       * 条件二：检查给定的类在给定的 ClassLoader 中是否可见
    *  `Class<?>[] ifcs = current.getInterfaces()`：拿到自己实现的接口，拿不到接口实现的接口
    *  `current = current.getSuperclass()`：递归寻找父类的接口，去获取父类实现的接口

  * public ProxyFactory(Class<?> proxyInterface, Interceptor interceptor)：

    ```java
    public ProxyFactory(Class<?> proxyInterface, Interceptor interceptor) {
        // 添加一个代理的接口
        addInterface(proxyInterface);
        // 添加通知，底层调用 addAdvisor
        addAdvice(interceptor);
    }
    ```
    
    * `addAdvisor(pos, new DefaultPointcutAdvisor(advice))`：Spring 中 Advice 对应的接口就是 Advisor，Spring 使用 Advisor 包装 Advice 实例

* `proxyFactory.copyFrom(this)`：填充一些信息到 proxyFactory

* `if (!proxyFactory.isProxyTargetClass())`：条件成立说明没有配置修改过 proxyTargetClass 为 true

  `if (shouldProxyTargetClass(beanClass, beanName))`：如果 **bd 内有 preserveTargetClass = true** ，那么这个 bd 对应的 class 创建代理时必须使用 CGLIB，条件成立设置 proxyTargetClass 为 true

  `evaluateProxyInterfaces(beanClass, proxyFactory)`：**根据目标类判定是否可以使用 JDK 动态代理**

  * `targetInterfaces = ClassUtils.getAllInterfacesForClass()`：获取当前目标对象 class 和父类的全部实现接口
  * `boolean hasReasonableProxyInterface = false`：实现的接口中是否有一个合理的接口
  * `if (!isConfigurationCallbackInterface(ifc) && !isInternalLanguageInterface(ifc) && ifc.getMethods().length > 0)`：遍历所有的接口，如果有任意一个接口满足条件，设置 hRPI 变量为 true
    * 条件一：判断当前接口是否是 Spring 生命周期内会回调的接口
    * 条件二：接口不能是 GroovyObject、Factory、MockAccess 类型的
    * 条件三：找到一个可以使用的被代理的接口
  * `if (hasReasonableProxyInterface)`：**有合理的接口，将这些接口设置到 proxyFactory 内**
  * `proxyFactory.setProxyTargetClass(true)`：**没有合理的代理接口，强制使用 CGLIB 创建对象**

* `advisors = buildAdvisors(beanName, specificInterceptors)`：匹配目标对象 clazz 的 Advisors，填充至 ProxyFactory

* `proxyFactory.setPreFiltered(true)`：设置为 true 表示传递给 proxyFactory 的 Advisors 信息做过基础 class 匹配

* `return proxyFactory.getProxy(getProxyClassLoader())`：创建代理对象

  ```java
  public Object getProxy() {
      return createAopProxy().getProxy();
  }
  ```

  DefaultAopProxyFactory.createAopProxy(AdvisedSupport config)：参数是一个配置对象，保存着创建代理需要的生产资料

  ```java
  public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
      //条件一：积极的优化     
      //条件二：为 true 代表强制使用 CGLIB 动态代理，
      //两种配置方法：
      // <aop:aspectj-autoproxy proxy-target-class="false"/> 
      // @EnableAspectJAutoProxy(proxyTargetClass = true)
      if (config.isOptimize() || config.isProxyTargetClass() || 
          //条件三：被代理对象没有实现任何接口或者只实现了 SpringProxy 接口，只能使用 CGLIB 动态代理
          hasNoUserSuppliedProxyInterfaces(config)) {
          Class<?> targetClass = config.getTargetClass();
          if (targetClass == null) {
              throw new AopConfigException("");
          }
          // 条件成立说明 target 是接口或者是已经被代理过的类型，只能使用 JDK 动态代理
          if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
              return new JdkDynamicAopProxy(config);	// 使用 JDK 动态代理
          }
          return new ObjenesisCglibAopProxy(config);	// 使用 CGLIB 动态代理
      }
      else {
          return new JdkDynamicAopProxy(config);		// 有接口的情况下只能使用 JDK 动态代理
      }
  }
  ```

  JdkDynamicAopProxy.getProxy(java.lang.ClassLoader)：获取 JDK 的代理对象

  ```java
  public JdkDynamicAopProxy(AdvisedSupport config) throws AopConfigException {
      // 配置类封装到 JdkDynamicAopProxy 属性中
      this.advised = config;
  }
  public Object getProxy(@Nullable ClassLoader classLoader) {
      // 获取需要代理的接口数组
      Class<?>[] proxiedInterfaces = AopProxyUtils.completeProxiedInterfaces(this.advised, true);
      // 查找当前所有的需要代理的接口，看是否有 equals 方法和 hashcode 方法，如果有就做一个标记
      findDefinedEqualsAndHashCodeMethods(proxiedInterfaces);
      // classLoader：类加载器  proxiedInterfaces：生成的代理类，需要实现的接口集合
      // this JdkDynamicAopProxy 实现了 InvocationHandler
      // 该方法最终返回一个代理类对象
      return Proxy.newProxyInstance(classLoader, proxiedInterfaces, this);
  }
  ```

  AopProxyUtils.completeProxiedInterfaces(this.advised, true)：获取代理的接口数组

  * `specifiedInterfaces = advised.getProxiedInterfaces()`：从 ProxyFactory 中拿到所有的 target 提取出来的接口
  * `if (specifiedInterfaces.length == 0)`：如果没有实现接口，检查当前 target 是不是接口或者已经是代理类，封装到 ProxyFactory 的 interfaces 集合中

  * ` addSpringProxy = !advised.isInterfaceProxied(SpringProxy.class)`：判断目标对象所有接口中是否有 **SpringProxy** 接口，没有的话需要添加，这个接口**标识这个代理类型是 Spring 管理的**
  * `addAdvised = !advised.isOpaque() && !advised.isInterfaceProxied(Advised.class)`：判断目标对象的所有接口，是否已经有 Advised 接口
  * ` addDecoratingProxy = (decoratingProxy && !advised.isInterfaceProxied(DecoratingProxy.class))`：判断目标对象的所有接口，是否已经有 DecoratingProxy 接口
  * `int nonUserIfcCount = 0`：非用户自己定义的接口数量，接下来要添加上面的三个接口了
  * `proxiedInterfaces = new Class<?>[specifiedInterfaces.length + nonUserIfcCount]`：创建一个新的 class 数组，长度是原目标对象提取出来的接口数量和 Spring 追加的数量，然后进行 **System.arraycopy 拷贝到新数组中**
  * `int index = specifiedInterfaces.length`：获取原目标对象提取出来的接口数量，当作 index
  * `if(addSpringProxy)`：根据上面三个布尔值把接口添加到新数组中
  * `return proxiedInterfaces`：返回追加后的接口集合

  JdkDynamicAopProxy.findDefinedEqualsAndHashCodeMethods()：查找在任何定义在接口中的 equals 和 hashCode 方法

  * `for (Class<?> proxiedInterface : proxiedInterfaces)`：遍历所有的接口
  * ` Method[] methods = proxiedInterface.getDeclaredMethods()`：获取接口中的所有方法
  * `for (Method method : methods)`：遍历所有的方法
    * `if (AopUtils.isEqualsMethod(method))`：当前方法是 equals 方法，把 equalsDefined 置为 true
    * `if (AopUtils.isHashCodeMethod(method))`：当前方法是 hashCode 方法，把 hashCodeDefined 置为 true

    * `if (this.equalsDefined && this.hashCodeDefined)`：如果有一个接口中有这两种方法，直接返回



***



#### 方法增强

main() 函数中调用用户方法，会进入该逻辑

JdkDynamicAopProxy 类中的 invoke 方法是真正执行代理方法

```java
public Object invoke(Object proxy, Method method, Object[] args)
//proxy：代理对象
//method：目标对象的方法
//args：目标对象方法对应的参数
```

* `targetSource = this.advised.targetSource`：advised 就是初始化 JdkDynamicAopProxy 对象时传入的变量

* `if (!this.equalsDefined && AopUtils.isEqualsMethod(method))`：条件成立说明代理类实现的接口没有定义 equals 方法，并且当前 method 调用 equals 方法，就调用 JdkDynamicAopProxy 提供的 equals 方法

* `if (this.advised.exposeProxy)`：需不需要暴露当前代理对象到 AOP 上下文内，true 暴露

  `oldProxy = AopContext.setCurrentProxy(proxy)`：把代理对象设置到上下文环境

  `setProxyContext = true`：允许提前引用

* `target = targetSource.getTarget()`：根据 targetSource  获取真正的代理对象

* `chain = this.advised.getInterceptorsAndDynamicInterceptionAdvice(method, targetClass)`：**查找适合该方法的增强**，首先从缓存中查找，查找不到进入主方法

  * `AdvisorAdapterRegistry registry = GlobalAdvisorAdapterRegistry.getInstance()`：向容器注册适配器，**可以将非 Advisor 类型的增强，包装成为 Advisor，将 Advisor 类型的增强提取出来对应的 MethodInterceptor**

    * `instance = new DefaultAdvisorAdapterRegistry()`：**该对象向容器中注册了** MethodBeforeAdviceAdapter、AfterReturningAdviceAdapter、ThrowsAdviceAdapter **三个适配器**

  * `advisors = config.getAdvisors()`：获取 ProxyFactory 内部持有的增强信息

  * `interceptorList = new ArrayList<>(advisors.length)`：拦截器列表有 5 个，一个 ExposeInvocationInterceptor 和 4 个增强器

  * `actualClass = (targetClass != null ? targetClass : method.getDeclaringClass())`：真实的目标对象类型

  * `Boolean hasIntroductions = null`：引介增强，不关心

  * `for (Advisor advisor : advisors)`：**遍历所有的增强**

  * `if (advisor instanceof PointcutAdvisor)`：条件成立说明当前 Advisor 是包含切点信息的，进入匹配逻辑

    `pointcutAdvisor = (PointcutAdvisor) advisor`：转成可以获取到切点信息的接口

    `if(config.isPreFiltered() || pointcutAdvisor.getPointcut().getClassFilter().matches(actualClass))`：当前代理被预处理，或者当前被代理的 class 对象匹配当前 Advisor 成功，只是 **class 匹配成功**

    * `mm = pointcutAdvisor.getPointcut().getMethodMatcher()`：获取切点的方法匹配器，不考虑引介增强

    * `match = mm.matches(method, actualClass)`：**静态匹配成功返回 true，只关注于处理类及其方法，不考虑参数**

    * `if (match)`：如果静态切点检查是匹配的，在运行的时候才进行**动态切点检查，会考虑参数匹配**（代表传入了参数）。如果静态匹配失败，直接不需要进行参数匹配，提高了工作效率

      `interceptors = registry.getInterceptors(advisor)`：提取出 advisor 内持有的拦截器信息 

      * `Advice advice = advisor.getAdvice()`：获取增强方法

      * `if (advice instanceof MethodInterceptor)`：当前 advice 是 MethodInterceptor 直接加入集合

      * `for (AdvisorAdapter adapter : this.adapters)`：**遍历三个适配器进行匹配**（初始化时创建的），以 MethodBeforeAdviceAdapter 为例

        `if (adapter.supportsAdvice(advice))`：判断当前 advice 是否是对应的 MethodBeforeAdvice

        `interceptors.add(adapter.getInterceptor(advisor))`：是就往拦截器链中添加 advisor
      
        * `advice = (MethodBeforeAdvice) advisor.getAdvice()`：**获取增强方法**
        * `return new MethodBeforeAdviceInterceptor(advice)`：**封装成 MethodInterceptor 方法拦截器返回**

      `interceptorList.add(new InterceptorAndDynamicMethodMatcher(interceptor, mm))`：向拦截器链添加动态匹配器
      
      `interceptorList.addAll(Arrays.asList(interceptors))`：将当前 advisor 内部的方法拦截器追加到 interceptorList

  * `interceptors = registry.getInterceptors(advisor)`：进入 else 的逻辑，说明当前 Advisor 匹配全部 class 的全部 method，全部加入到 interceptorList

  * `return interceptorList`：返回 method 方法的拦截器链

* `if (chain.isEmpty())`：查询出来匹配当前方法的拦截器，**数量是 0 说明当前 method 不需要被增强**，直接调用目标方法

  `retVal = AopUtils.invokeJoinpointUsingReflection(target, method, argsToUse)`：调用目标对象的目标方法

* `invocation = new ReflectiveMethodInvocation(proxy, target, method, args, targetClass, chain)`：**有匹配当前 method 的方法拦截器，要做增强处理**，把方法信息封装到方法调用器里

  `retVal = invocation.proceed()`：**核心拦截器链驱动方法**

  * `if (this.currentInterceptorIndex == this.interceptorsAndDynamicMethodMatchers.size() - 1)`：条件成立说明方法拦截器全部都已经调用过了（0 - 1 = -1），接下来需要执行目标对象的目标方法

    `return invokeJoinpoint()`：调用连接点

  * `this.interceptorsAndDynamicMethodMatchers.get(++this.currentInterceptorIndex)`：**获取下一个方法拦截器**

  * `if (interceptorOrInterceptionAdvice instanceof InterceptorAndDynamicMethodMatcher)`：**需要运行时匹配**

    `if (dm.methodMatcher.matches(this.method, targetClass, this.arguments))`：判断是否匹配成功

    * `return dm.interceptor.invoke(this)`：匹配成功，执行方法
    * `return proceed()`：匹配失败跳过当前拦截器

  * `return ((MethodInterceptor) interceptorOrInterceptionAdvice).invoke(this)`：**所有的方法拦截器都会执行到该方法，此方法内继续执行 proceed() 完成责任链的驱动，直到最后一个  MethodBeforeAdviceInterceptor 调用前置通知，然后调用 mi.proceed()，发现是最后一个拦截器就直接执行目标方法，return 到上一个拦截器的 mi.proceed() 处，依次返回到责任链的上一个拦截器执行通知方法**

* `retVal = proxy`：如果目标方法返回目标对象，这里做个普通替换返回代理对象

* `if (setProxyContext)`：如果允许了提前暴露，这里需要设置为初始状态

  `AopContext.setCurrentProxy(oldProxy)`：当前代理对象已经完成工作，把原始对象设置回上下文
  
* `return retVal`：返回执行的结果

图示先从上往下建立链，然后从下往上依次执行，责任链模式

* 正常执行：（环绕通知）→ 前置通知 → 目标方法 → 后置通知 → 返回通知

 * 出现异常：（环绕通知）→ 前置通知 → 目标方法 → 后置通知 → 异常通知

 * AfterReturningAdviceInterceptor 源码：没有任何异常处理机制，直接抛给上层

   ```java
   public Object invoke(MethodInvocation mi) throws Throwable {
       Object retVal = mi.proceed();
       this.advice.afterReturning(retVal, mi.getMethod(), mi.getArguments(), mi.getThis());
       return retVal;
   }
   ```

   AspectJAfterThrowingAdvice 执行异常处理：

   ```java
   public Object invoke(MethodInvocation mi) throws Throwable {
       try {
           return mi.proceed();
       }
       catch (Throwable ex) {
           if (shouldInvokeOnThrowing(ex)) {
               invokeAdviceMethod(getJoinPointMatch(), null, ex);
           }
           throw ex;
       }
   }
   ```

![](https://gitee.com/seazean/images/raw/master/Frame/Spring-AOP动态代理执行方法.png)






***



### 注解

#### Component

解析 @Component 和 @Service 都是常用的注解

**@Component 解析流程：**

* 注解类启动容器的时，注册 ClassPathBeanDefinitionScanner 到容器，用来扫描 Bean 的相关信息

  ```java
  protected Set<BeanDefinitionHolder> doScan(String... basePackages) {
      Set<BeanDefinitionHolder> beanDefinitions = new LinkedHashSet<>();
      // 遍历指定的所有的包
      for (String basePackage : basePackages) {
          // 读取当前包下的资源装换为 BeanDefinition，字节流的方式
          Set<BeanDefinition> candidates = findCandidateComponents(basePackage);
          for (BeanDefinition candidate : candidates) {
              //遍历，封装，类似于 XML 的解析方式
              // 注册到容器中
              registerBeanDefinition(definitionHolder, this.registry)
          }
      return beanDefinitions;
  }
  ```

* ClassPathScanningCandidateComponentProvider.findCandidateComponents()

  ```java
  public Set<BeanDefinition> findCandidateComponents(String basePackage) {
      if (this.componentsIndex != null && indexSupportsIncludeFilters()) {
          return addCandidateComponentsFromIndex(this.componentsIndex, basePackage);
      }
      else {
          return scanCandidateComponents(basePackage);
      }
  }
  ```

  ```java
  private Set<BeanDefinition> scanCandidateComponents(String basePackage) {}
  ```

    * `String packageSearchPath = ResourcePatternResolver.CLASSPATH_ALL_URL_PREFIX resolveBasePackage(basePackage) + '/' + this.resourcePattern` ：将 package 转化为 ClassLoader 类资源搜索路径 packageSearchPath，例如：`com.wl.spring.boot` 转化为 `classpath*:com/wl/spring/boot/**/*.class`

    * `resources = getResourcePatternResolver().getResources(packageSearchPath)`：加载搜素路径下的资源

    * `for (Resource resource : resources) `：遍历所有的资源

      `metadataReader = getMetadataReaderFactory().getMetadataReader(resource)`：获取元数据阅读器

      `if (isCandidateComponent(metadataReader))`：**当前类不匹配任何排除过滤器，并且匹配一个包含过滤器**，返回 true

        * includeFilters 由 `registerDefaultFilters()` 设置初始值，方法有 @Component，没有 @Service，因为 @Component 是 @Service 的元注解，Spring 在读取 @Service 时也读取了元注解，并将 @Service 作为 @Component 处理

          ```java
          this.includeFilters.add(new AnnotationTypeFilter(Component.class))
          ```

          ```java
          @Target({ElementType.TYPE})
          @Retention(RetentionPolicy.RUNTIME)
          @Documented
          @Component
          public @interface Service {}
          ```

      `candidates.add(sbd)`：添加到返回结果的 list



***



#### Autowired

打开 @Autowired 源码，注释上写 Please consult the javadoc for the AutowiredAnnotationBeanPostProcessor

AutowiredAnnotationBeanPostProcessor 间接实现 InstantiationAwareBeanPostProcessor，就具备了实例化前后（而不是初始化前后）管理对象的能力，实现了 BeanPostProcessor，具有初始化前后管理对象的能力，实现 BeanFactoryAware，具备随时拿到 BeanFactory 的能力，所以这个类**具备一切后置处理器的能力**

**在容器启动，为对象赋值的时候，遇到 @Autowired 注解，会用后置处理器机制，来创建属性的实例，然后再利用反射机制，将实例化好的属性，赋值给对象上，这就是 Autowired 的原理**

作用时机：

* Spring 在每个 Bean 实例化之后，调用 AutowiredAnnotationBeanPostProcessor 的 `postProcessMergedBeanDefinition()` 方法，查找该 Bean 是否有 @Autowired 注解，进行相关数据的获取
* Spring 在每个 Bean 调用 `populateBean()` 进行属性注入的时候，即调用 `postProcessProperties()` 方法，查找该 Bean 属性是否有 @Autowired 注解，进行相关数据的填充



***



#### Transaction

@EnableTransactionManagement 导入 TransactionManagementConfigurationSelector，该类给 Spring 容器中两个组件：

* AdviceMode 为 PROXY：导入 AutoProxyRegistrar 和 ProxyTransactionManagementConfiguration（默认）
* AdviceMode  为 ASPECTJ：导入 AspectJTransactionManagementConfiguration（与声明式事务无关）

AutoProxyRegistrar：给容器中注册 InfrastructureAdvisorAutoProxyCreator，该类实现了 InstantiationAwareBeanPostProcessor 接口，可以拦截 Spring 的 bean 初始化和实例化前后。利用后置处理器机制拦截 bean 以后包装该 bean 并返回一个代理对象，代理对象中保存所有的拦截器，代理对象执行目标方法，利用拦截器的链式机制依次进入每一个拦截器中进行执行（AOP 原理）

ProxyTransactionManagementConfiguration：是一个 Spring 的配置类，注册 BeanFactoryTransactionAttributeSourceAdvisor 事务增强器，利用注解 @Bean 把该类注入到容器中，该增强器有两个字段：

* TransactionAttributeSource：用于解析事务注解的相关信息，比如 @Transactional 注解，该类的真实类型是 AnnotationTransactionAttributeSource，初始化方法中注册了三个**注解解析器**，解析三种类型的事务注解 Spring、JTA、Ejb3

* TransactionInterceptor：**事务拦截器**，代理对象执行拦截器方法时，会调用 TransactionInterceptor 的 invoke 方法，底层调用TransactionAspectSupport.invokeWithinTransaction()，通过 PlatformTransactionManager **控制着事务的提交和回滚**，所以事务的底层原理就是通过 AOP 动态织入，进行事务开启和提交

  ```java
  // 创建平台事务管理器对象
  final PlatformTransactionManager tm = determineTransactionManager(txAttr);
  // 开启事务
  TransactionInfo txInfo = createTransactionIfNecessary(tm, txAttr, joinpointIdentification);
  // 执行目标方法
  retVal = invocation.proceedWithInvocation();
  // 调用 java.sql.Connection 提交或者回滚事务
  commitTransactionAfterReturning(txInfo);
  ```

  `createTransactionIfNecessary(tm, txAttr, joinpointIdentification)`：

  * `status = tm.getTransaction(txAttr)`：获取事务状态，方法内通过 doBegin **调用 Connection 的 setAutoCommit 开启事务**，就是 JDBC 原生的方式

  * `prepareTransactionInfo(tm, txAttr, joinpointIdentification, status)`：方法内调用 bindToThread() 方法，利用 ThreadLocal 把当前事务绑定到当前线程

    补充策略模式（Strategy Pattern）：**使用不同策略的对象实现不同的行为方式**，策略对象的变化导致行为的变化，事务也是这种模式，每个事务对应一个新的 connection 对象

![](https://gitee.com/seazean/images/raw/master/Frame/Spring-图解事务执行流程.jpg)



图片来源：https://blog.csdn.net/weixin_45596022/article/details/113749478





****





# MVC

## 基本介绍

SpringMVC：是一种基于 Java 实现 MVC 模型的轻量级 Web 框架

SpringMVC 优点：

* 使用简单
* 性能突出（对比现有的框架技术）
* 灵活性强

软件开发三层架构：

- 表现层：负责数据展示

- 业务层：负责业务处理

- 数据层：负责数据操作

  ![](https://gitee.com/seazean/images/raw/master/Frame/MVC三层架构.png)

MVC（Model View Controller），一种用于设计创建Web应用程序表现层的模式

- Model（模型）：数据模型，用于封装数据

- View（视图）：页面视图，用于展示数据

  - jsp  
  - html

* Controller（控制器）：处理用户交互的调度器，用于根据用户需求处理程序逻辑
  * Servlet
  * SpringMVC

  ![](https://gitee.com/seazean/images/raw/master/Frame/MVC功能图示.png)





***





## 基本配置

### 入门项目

流程分析：

* 服务器启动
  1. 加载 web.xml 中 DispatcherServlet
  2. 读取 spring-mvc.xml 中的配置，加载所有 controller 包中所有标记为 bean 的类
  3. 读取 bean 中方法上方标注 @RequestMapping 的内容
* 处理请求
  1. DispatcherServlet 配置拦截所有请求 /
  2. 使用请求路径与所有加载的 @RequestMapping 的内容进行比对
  3. 执行对应的方法
  4. 根据方法的返回值在 webapp 目录中查找对应的页面并展示  

代码实现：

* pom.xml 导入坐标

  ```xml
  <modelVersion>4.0.0</modelVersion>
  
  <groupId>demo</groupId>
  <artifactId>spring_base_config</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
  
  <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <maven.compiler.source>1.8</maven.compiler.source>
      <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  
  <dependencies>
      <!-- servlet3.0规范的坐标 -->
      <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>3.1.0</version>
          <scope>provided</scope>
      </dependency>
      <!--jsp坐标-->
      <dependency>
          <groupId>javax.servlet.jsp</groupId>
          <artifactId>jsp-api</artifactId>
          <version>2.1</version>
          <scope>provided</scope>
      </dependency>
      <!--spring的坐标-->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-context</artifactId>
          <version>5.1.9.RELEASE</version>
      </dependency>
      <!--springmvc的坐标-->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-webmvc</artifactId>
          <version>5.1.9.RELEASE</version>
      </dependency>
  </dependencies>
  
  <!--构建-->
  <build>
      <!--设置插件-->
      <plugins>
          <!--具体的插件配置-->
          <plugin>
              <groupId>org.apache.tomcat.maven</groupId>
              <artifactId>tomcat7-maven-plugin</artifactId>
              <version>2.1</version>
              <configuration>
                  <port>80</port>
                  <path>/</path>
              </configuration>
          </plugin>
      </plugins>
  </build>
  ```
  
* 设定具体 Controller，控制层 java / controller / UserController

  ```java
  @Controller  //@Component衍生注解
  public class UserController {
      //设定当前方法的访问映射地址，等同于Servlet在web.xml中的配置
      @RequestMapping("/save")
      //设置当前方法返回值类型为String，用于指定请求完成后跳转的页面
      public String save(){
          System.out.println("user mvc controller is running ...");
          //设定具体跳转的页面
      	return "success.jsp";
      }
  }
  ```

* webapp / WEB-INF / web.xml，配置SpringMVC核心控制器，请求转发到对应的具体业务处理器Controller中（等同于Servlet配置）

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
           version="3.1">
      <!--配置Servlet-->
      <servlet>
          <servlet-name>DispatcherServlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <!--加载Spring控制文件-->
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath*:spring-mvc.xml</param-value>
          </init-param>
      </servlet>
      <servlet-mapping>
          <servlet-name>DispatcherServlet</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  </web-app>
  ```

* resouces / spring-mvc.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans 
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/mvc 
          http://www.springframework.org/schema/mvc/spring-mvc.xsd">
      <!--扫描加载所有的控制类-->
      <context:component-scan base-package="controller"/>
  </beans>
  ```




***



### 加载控制

Controller 加载控制：SpringMVC 的处理器对应的 bean 必须按照规范格式开发，未避免加入无效的 bean 可通过 bean 加载过滤器进行包含设定或排除设定，表现层 bean 标注通常设定为 @Controller  

* resources / spring-mvc.xml 配置

  ```xml
  <context:component-scan base-package="com.seazean">
      <context:include-filter 
  						type="annotation" 
  						expression="org.springframework.stereotype.Controller"/>
  </context:component-scan>
  ```

* 静态资源加载（webapp 目录下的相关资源），spring-mvc.xml 配置，开启 mvc 命名空间

  ```xml
  <!--放行指定类型静态资源配置方式-->
  <mvc:resources mapping="/img/**" location="/img/"/> <!--webapp/img/资源-->
  <mvc:resources mapping="/js/**" location="/js/"/>
  <mvc:resources mapping="/css/**" location="/css/"/>
  
  <!--SpringMVC 提供的通用资源放行方式，建议选择-->
  <mvc:default-servlet-handler/>
  ```

* 中文乱码处理 SpringMVC 提供专用的中文字符过滤器，用于处理乱码问题。配置在 web.xml 里面

  ```xml
  <!--乱码处理过滤器，与Servlet中使用的完全相同，差异之处在于处理器的类由Spring提供-->
  <filter>
      <filter-name>CharacterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
          <param-name>encoding</param-name>
          <param-value>UTF-8</param-value>
      </init-param>
  </filter>
  <filter-mapping>
      <filter-name>CharacterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```



***



### 注解驱动

纯注解开发：

* 使用注解形式转化 SpringMVC 核心配置文件为配置类 java / config /  SpringMVCConfiguration.java

  ```java
  @Configuration
  @ComponentScan(value = "com.seazean", includeFilters = @ComponentScan.Filter(
      								type=FilterType.ANNOTATION,
      								classes = {Controller.class} )
      )
  public class SpringMVCConfiguration implements WebMvcConfigurer{
      //注解配置放行指定资源格式
  //    @Override
  //    public void addResourceHandlers(ResourceHandlerRegistry registry) {
  //        registry.addResourceHandler("/img/**").addResourceLocations("/img/");
  //    }
      //注解配置通用放行资源的格式 建议使用
      @Override
      public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
          configurer.enable();
      }
  }
  ```

* 基于 servlet3.0 规范，自定义 Servlet 容器初始化配置类，加载 SpringMVC 核心配置类  

  ```java
  //创建Servlet容器时，使用注解方式加载SPRINGMVC配置类中的信息，
  //并加载成WEB专用的ApplicationContext对象该对象放入了ServletContext范围，
  //在整个WEB容器中可以随时获取调用
  public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
      @Override
      protected WebApplicationContext createServletApplicationContext() {
          A.C.W.A ctx = new AnnotationConfigWebApplicationContext();
          ctx.register(SpringMVCConfiguration.class);
          return ctx;
      }
  
      //注解配置映射地址方式，服务于SpringMVC的核心控制器DispatcherServlet
      @Override
      protected String[] getServletMappings() {
          return new String[]{"/"};
      }
  
      @Override
      protected WebApplicationContext createRootApplicationContext() {
          return null;
      }
  
      //乱码处理作为过滤器，在servlet容器启动时进行配置
      @Override
      public void onStartup(ServletContext servletContext) throws ServletException {
          super.onStartup(servletContext);
          CharacterEncodingFilter cef = new CharacterEncodingFilter();
          cef.setEncoding("UTF-8");
          FilterRegistration.Dynamic registration = servletContext.addFilter("characterEncodingFilter", cef);
          registration.addMappingForUrlPatterns(EnumSet.of(
             			DispatcherType.REQUEST,
              		DispatcherType.FORWARD,
              		DispatcherType.INCLUDE), false,"/*");
      }
  }
  ```



***



### 请求映射

名称：@RequestMapping

类型：方法注解、类注解

位置：处理器类中的方法定义上方、处理器类定义上方

* 方法注解

  作用：绑定请求地址与对应处理方法间的关系

  无类映射地址访问格式： http://localhost/requestURL2

  ```java
  @Controller
  public class UserController {
      @RequestMapping("/requestURL2")
      public String requestURL2() {
          return "page.jsp";
      }
  }
  ```

* 类注解
  
  作用：为当前处理器中所有方法设定公共的访问路径前缀
  
  带有类映射地址访问格式，将类映射地址作为前缀添加在实际映射地址前面：**/user/requestURL1**
  
  最终返回的页面如果未设定绝对访问路径，将从类映射地址所在目录中查找 **webapp/user/page.jsp**
  
  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
      @RequestMapping("/requestURL2")
      public String requestURL2() {
          return "page.jsp";
      }
  } 
  ```
  
* 常用属性

  ```java
  @RequestMapping(
      value="/requestURL3", //设定请求路径，与path属性、 value属性相同
      method = RequestMethod.GET, //设定请求方式
      params = "name", //设定请求参数条件
      headers = "content-type=text/*", //设定请求消息头条件
      consumes = "text/*", //用于指定可以接收的请求正文类型（MIME类型）
      produces = "text/*" //用于指定可以生成的响应正文类型（MIME类型）
  )
  public String requestURL3() {
      return "/page.jsp";
  }
  ```



***



## 基本操作

### 请求处理

#### 普通类型

SpringMVC 将传递的参数封装到处理器方法的形参中，达到快速访问参数的目的

* 访问 URL：http://localhost/requestParam1?name=seazean&age=14  

  ```java
  @Controller
  public class UserController {
      @RequestMapping("/requestParam1")
      public String requestParam1(String name ,int age){
          System.out.println("name=" + name + ",age=" + age);
          return "page.jsp";
      }
  }
  ```

  ```jsp
  <%@page pageEncoding="UTF-8" language="java" contentType="text/html;UTF-8" %>
  <html>
  <body>
  	<h1>请求参数测试页面</h1>
  </body>
  </html>
  ```

@RequestParam 的使用：

* 类型：形参注解

* 位置：处理器类中的方法形参前方

* 作用：绑定请求参数与对应处理方法形参间的关系 

* 访问 URL：http://localhost/requestParam2?userName=Jock

  ```java
  @RequestMapping("/requestParam2")
  public String requestParam2(@RequestParam(
                              name = "userName",
                              required = true,	//为true代表必须有参数
                              defaultValue = "s") String name){
      System.out.println("name=" + name);
      return "page.jsp";
  }
  ```



***



#### POJO类型

##### 简单类型

当 POJO 中使用简单类型属性时， 参数名称与 POJO 类属性名保持一致  

* 访问 URL： http://localhost/requestParam3?name=seazean&age=14  

  ```java
  @RequestMapping("/requestParam3")
  public String requestParam3(User user){
      System.out.println("name=" + user.getName());
      return "page.jsp";
  }
  ```

  ```java
  public class User {
      private String name;
      private Integer age;
      //......
  }
  ```



****



##### 参数冲突

当 POJO 类型属性与其他形参出现同名问题时，将被**同时赋值**，建议使用 @RequestParam 注解进行区分

* 访问 URL： http://localhost/requestParam4?name=seazean&age=14  

  ```java
  @RequestMapping("/requestParam4")
  public String requestParam4(User user, String age){
      System.out.println("user.age=" + user.getAge() + ",age=" + age);//14 14 
      return "page.jsp";
  }
  ```



***



##### 复杂类型

当 POJO 中出现对象属性时，参数名称与对象层次结构名称保持一致  

* 访问 URL： http://localhost/requestParam5?address.province=beijing  

  ```java
  @RequestMapping("/requestParam5")
  public String requestParam5(User user){
      System.out.println("user.address=" + user.getAddress().getProvince());
      return "page.jsp";
  }
  ```

  ```java
  public class User {
      private String name;
      private Integer age;
      private Address address; //....
  }
  ```

  ```java
  public class Address {
      private String province;
      private String city;
      private String address;
  }
  ```




***



##### 容器类型

POJO 中出现集合类型的处理方式

* 通过 URL 地址中同名参数，可以为 POJO 中的集合属性进行赋值，集合属性要求保存简单数据

  访问 URL：http://localhost/requestParam6?nick=Jock1&nick=Jockme&nick=zahc

  ```java
  @RequestMapping("/requestParam6")
  public String requestParam6(User user){
      System.out.println("user=" + user);
      //user = User{name='null',age=null,nick={Jock1,Jockme,zahc}}
      return "page.jsp";
  }
  ```

  ```java
  public class User {
      private String name;
      private Integer age;
      private List<String> nick;
  }
  ```

* POJO 中出现 List 保存对象数据，参数名称与对象层次结构名称保持一致，使用数组格式描述集合中对象的位置访问 URL：http://localhost/requestParam7?addresses[0].province=bj&addresses[1].province=tj  

  ```java
  @RequestMapping("/requestParam7")
  public String requestParam7(User user){
      System.out.println("user.addresses=" + user.getAddress());
      //{Address{provice=bj,city='null',address='null'}},{Address{....}}
      return "page.jsp";
  }
  ```

  ```java
  public class User {
      private String name;
      private Integer age;
      private List<Address> addresses;
  }
  ```

* POJO 中出现 Map 保存对象数据，参数名称与对象层次结构名称保持一致，使用映射格式描述集合中对象位置

  URL: http://localhost/requestParam8?addressMap[’home’].province=bj&addressMap[’job’].province=tj  

  ```java
  @RequestMapping("/requestParam8")
  public String requestParam8(User user){
      System.out.println("user.addressMap=" + user.getAddressMap());
      //user.addressMap={home=Address{p=,c=,a=},job=Address{....}}
      return "page.jsp";
  }
  ```

  ```java
  public class User {
      private Map<String,Address> addressMap;
      //....
  }
  ```




***



#### 数组集合

##### 数组类型

请求参数名与处理器方法形参名保持一致，且请求参数数量＞ 1个  

* 访问 URL： http://localhost/requestParam9?nick=Jockme&nick=zahc  

  ```java
  @RequestMapping("/requestParam9")
  public String requestParam9(String[] nick){
      System.out.println(nick[0] + "," + nick[1]);
      return "page.jsp";
  }
  ```



***



##### 集合类型

保存简单类型数据，请求参数名与处理器方法形参名保持一致，且请求参数数量＞ 1个

* 访问 URL： http://localhost/requestParam10?nick=Jockme&nick=zahc

  ```java
  @RequestMapping("/requestParam10")
  public String requestParam10(@RequestParam("nick") List<String> nick){
      System.out.println(nick);
      return "page.jsp";
  }
  ```

* 注意： SpringMVC 默认将 List 作为对象处理，赋值前先创建对象，然后将 nick **作为对象的属性**进行处理。List 是接口无法创建对象，报无法找到构造方法异常；修复类型为可创建对象的 ArrayList 类型后，对象可以创建但没有 nick 属性，因此数据为空
  解决方法：需要告知 SpringMVC 的处理器 nick 是一组数据，而不是一个单一属性。通过 @RequestParam 注解，将数量大于 1 个 names 参数打包成参数数组后， SpringMVC 才能识别该数据格式，并判定形参类型是否为数组或集合，并按数组或集合对象的形式操作数据



***



#### 转换器

##### 类型

开启转换配置：`<mvc:annotation-driven />  `
作用：提供 Controller 请求转发，Json 自动转换等功能

如果访问 URL：http://localhost/requestParam1?name=seazean&age=seazean，会出现报错，类型转化异常

```java
@RequestMapping("/requestParam1")
public String requestParam1(String name ,int age){
    System.out.println("name=" + name + ",age=" + age);
    return "page.jsp";
}
```

SpringMVC 对接收的数据进行自动类型转换，该工作通过 Converter 接口实现：

* **标量转换器**
  StringToBooleanConverter String → Boolean
  ObjectToStringConverter Object → String
  StringToNumberConverterFactory String → Number（ Integer、 Long 等）
  NumberToNumberConverterFactory Number子类型之间（Integer、 Long、 Double 等）
  StringToCharacterConverter String → java.lang.Character
  NumberToCharacterConverter Number子类型（Integer、 Long、 Double 等）→ java.lang.Character
  CharacterToNumberFactory java.lang.Character → Number子类型（Integer、Long、Double 等）
  StringToEnumConverterFactory String → enum类型
  EnumToStringConverter enum类型 → String
  StringToLocaleConverter String → java.util.Local
  PropertiesToStringConverter java.util.Properties → String
  StringToPropertiesConverter String → java.util.Properties  

* **集合、数组相关转换器**
  ArrayToCollectionConverter 数组 → 集合（ List、 Set）
  CollectionToArrayConverter 集合（ List、 Set） →数组
  ArrayToArrayConverter 数组间
  CollectionToCollectionConverter 集合间（ List、 Set）
  MapToMapConverter Map间
  ArrayToStringConverter 数组→String类型
  StringToArrayConverter String →数组， trim后使用“,”split
  ArrayToObjectConverter 数组 → Object
  ObjectToArrayConverter Object → 单元素数组
  CollectionToStringConverter 集合（ List、 Set） →String
  StringToCollectionConverter String → 集合（ List、 Set）， trim后使用“,”split
  CollectionToObjectConverter 集合 → Object
  ObjectToCollectionConverter Object → 单元素集合  
* **默认转换器**
  ObjectToObjectConverter Object间
  IdToEntityConverter Id → Entity
  FallbackObjectToStringConverter Object → String  



***



##### 日期

![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-date数据类型转换.png)

如果访问 URL：http://localhost/requestParam11?date=1999-09-09 会报错，所以需要日期类型转换

* 声明自定义的转换格式并覆盖系统转换格式，配置 resources / spring-mvc.xml

  ```xml
  <!--5.启用自定义Converter-->
  <mvc:annotation-driven conversion-service="conversionService"/>
  <!--1.设定格式类型Converter，注册为Bean，受SpringMVC管理-->
  <bean id="conversionService" class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
      <!--2.自定义Converter格式类型设定，该设定使用的是同类型覆盖的思想-->
      <property name="formatters">
          <!--3.使用set保障相同类型的转换器仅保留一个，避免冲突-->
          <set>
              <!--4.设置具体的格式类型-->
              <bean class="org.springframework.format.datetime.DateFormatter">
                  <!--5.类型规则-->
                  <property name="pattern" value="yyyy-MM-dd"/>
              </bean>
          </set>
      </property>
  </bean>
  ```

* @DateTimeFormat
  类型：形参注解、成员变量注解
  位置：形参前面 或 成员变量上方
  作用：为当前参数或变量指定类型转换规则

  ```java
  public String requestParam12(@DateTimeFormat(pattern = "yyyy-MM-dd") Date date){
      System.out.println("date=" + date);
      return "page.jsp";
  }
  ```

  ```java
  @DateTimeFormat(pattern = "yyyy-MM-dd")
  private Date date;
  ```

  依赖注解驱动支持，xml 开启配置：

  ```xml
  <mvc:annotation-driven />  
  ```



***



##### 自定义

自定义类型转换器，实现 Converter 接口或者直接容器中注入：

* 方式一：

  ```java
  public class WebConfig implements WebMvcConfigurer {
      @Bean
      public WebMvcConfigurer webMvcConfigurer() {
          return new WebMvcConfigurer() {
              @Override
              public void addFormatters(FormatterRegistry registry) {
                  registry.addConverter(new Converter<String, Date>() {
                      @Override
                      public Pet convert(String source) {
                      	DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
                          Date date = null;
                          //类型转换器无法预计使用过程中出现的异常，因此必须在类型转换器内部捕获，
                          //不允许抛出，框架无法预计此类异常如何处理
                          try {
                              date = df.parse(source);
                          } catch (ParseException e) {
                              e.printStackTrace();
                          }
                          return date;
                      }
                  });
          }
      }
  }

* 方式二：

  ```java
  //本例中的泛型填写的是String，Date，最终出现字符串转日期时，该类型转换器生效
  public class MyDateConverter implements Converter<String, Date> {
      //重写接口的抽象方法，参数由泛型决定
      public Date convert(String source) {
          DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
          Date date = null;
          //类型转换器无法预计使用过程中出现的异常，因此必须在类型转换器内部捕获，
          //不允许抛出，框架无法预计此类异常如何处理
          try {
              date = df.parse(source);
          } catch (ParseException e) {
              e.printStackTrace();
          }
          return date;
      }
  }
  ```

  配置 resources / spring-mvc.xml，注册自定义转换器，将功能加入到 SpringMVC 转换服务 ConverterService 中

  ```xml
  <!--1.将自定义Converter注册为Bean，受SpringMVC管理-->
  <bean id="myDateConverter" class="converter.MyDateConverter"/>
  <!--2.设定自定义Converter服务bean-->
  <bean id="conversionService"
        class="org.springframework.context.support.ConversionServiceFactoryBean">
      <!--3.注入所有的自定义Converter，该设定使用的是同类型覆盖的思想-->
      <property name="converters">
          <!--4.set保障同类型转换器仅保留一个，去重规则以Converter<S,T>的泛型为准-->
          <set>
              <!--5.具体的类型转换器-->
              <ref bean="myDateConverter"/>
          </set>
      </property>
  </bean>
  
  <!--开启注解驱动，加载自定义格式化转换器对应的类型转换服务-->
  <mvc:annotation-driven conversion-service="conversionService"/>
  ```

* 使用转换器

  ```java
  @RequestMapping("/requestParam12")
  public String requestParam12(Date date){
      System.out.println(date);
      return "page.jsp";
  }
  ```

  



***



### 响应处理

#### 页面跳转

请求转发和重定向：

* 请求转发：

  ```java
  @Controller
  public class UserController {
      @RequestMapping("/showPage1")
  	public String showPage1() {
     	 	System.out.println("user mvc controller is running ...");
      	return "forward:/WEB-INF/page/page.jsp;
  	}
  }
  ```

* 请求重定向：

  ```java
  @RequestMapping("/showPage2")
  public String showPage2() {
      System.out.println("user mvc controller is running ...");
      return "redirect:/WEB-INF/page/page.jsp";//不能访问WEB-INF下的资源
  }
  ```


页面访问快捷设定（InternalResourceViewResolver）：

* 展示页面的保存位置通常固定且结构相似，可以设定通用的访问路径简化页面配置，配置 spring-mvc.xml：

  ```xml
  <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
      <property name="prefix" value="/WEB-INF/pages/"/>
      <property name="suffix" value=".jsp"/>
  </bean>
  ```

* 简化

  ```java
  @RequestMapping("/showPage3")
  public String showPage3() {
      System.out.println("user mvc controller is running...");
      return "page";
  }
  @RequestMapping("/showPage4")
  public String showPage4() {
      System.out.println("user mvc controller is running...");
      return "forward:page";
  }
  
  @RequestMapping("/showPage5")
  public String showPage5() {
      System.out.println("user mvc controller is running...");
      return "redirect:page";
  }
  ```

* 如果未设定了返回值，使用 void 类型，则默认使用访问路径作页面地址的前缀后缀

  ```java
  //最简页面配置方式，使用访问路径作为页面名称，省略返回值
  @RequestMapping("/showPage6")
  public void showPage6() {
      System.out.println("user mvc controller is running ...");
  }
  ```



***



#### 数据跳转

ModelAndView 是 SpringMVC 提供的一个对象，该对象可以用作控制器方法的返回值（Model 同），实现携带数据跳转

作用：

+ 设置数据，向请求域对象中存储数据
+ 设置视图，逻辑视图

代码实现：

* 使用 HttpServletRequest 类型形参进行数据传递

  ```java
  @Controller
  public class BookController {
      @RequestMapping("/showPageAndData1")
      public String showPageAndData1(HttpServletRequest request) {
          request.setAttribute("name","seazean");
          return "page";
      }
  }
  ```

* 使用 Model 类型形参进行数据传递

  ```java
  @RequestMapping("/showPageAndData2")
  public String showPageAndData2(Model model) {
      model.addAttribute("name","seazean");
      Book book = new Book();
      book.setName("SpringMVC入门实战");
      book.setPrice(66.6d);
      //添加数据的方式，key对value
      model.addAttribute("book",book);
      return "page";
  }
  ```

  ```java
  public class Book {
      private String name;
      private Double price;
  }
  ```

* 使用 ModelAndView 类型形参进行数据传递，将该对象作为返回值传递给调用者  

  ```java
  @RequestMapping("/showPageAndData3")
  public ModelAndView showPageAndData3(ModelAndView modelAndView) {
      //ModelAndView mav = new ModelAndView(); 替换形参中的参数
      Book book  = new Book();
      book.setName("SpringMVC入门案例");
      book.setPrice(66.66d);
  
      //添加数据的方式，key对value
      modelAndView.addObject("book",book);
      modelAndView.addObject("name","Jockme");
      //设置页面的方式，该方法最后一次执行的结果生效
      modelAndView.setViewName("page");
      //返回值设定成ModelAndView对象
      return modelAndView;
  }
  ```

* ModelAndView 扩展

  ```java
  //ModelAndView对象支持转发的手工设定，该设定不会启用前缀后缀的页面拼接格式
  @RequestMapping("/showPageAndData4")
  public ModelAndView showPageAndData4(ModelAndView modelAndView) {
      modelAndView.setViewName("forward:/WEB-INF/page/page.jsp");
      return modelAndView;
  }
  
  //ModelAndView对象支持重定向的手工设定，该设定不会启用前缀后缀的页面拼接格式
  @RequestMapping("/showPageAndData5")
  public ModelAndView showPageAndData6(ModelAndView modelAndView) {
      modelAndView.setViewName("redirect:page.jsp");
      return modelAndView;
  }
  ```



***



#### JSON

注解：@ResponseBody

作用：将 Controller 的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到 Response 的 body 区。如果返回值是字符串，那么直接将字符串返回客户端；如果是一个对象，会**将对象转化为 Json**，返回客户端

注意：当方法上面没有写 ResponseBody，底层会将方法的返回值封装为 ModelAndView 对象

* 使用 HttpServletResponse 对象响应数据

  ```java
  @Controller
  public class AccountController {
      @RequestMapping("/showData1")
      public void showData1(HttpServletResponse response) throws IOException {
          response.getWriter().write("message");
      }
  }
  ```

* 使用 **@ResponseBody 将返回的结果作为响应内容**（页面显示），而非响应的页面名称

  ```java
  @RequestMapping("/showData2")
  @ResponseBody
  public String showData2(){
      return "{'name':'Jock'}";
  }
  ```

* 使用 jackson 进行 json 数据格式转化

  导入坐标：

  ```xml
  <!--json相关坐标3个-->
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.0</version>
  </dependency>
  
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
  </dependency>
  
  <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.0</version>
  </dependency>
  ```

  ```java
  @RequestMapping("/showData3")
  @ResponseBody
  public String showData3() throws JsonProcessingException {
      Book book  = new Book();
      book.setName("SpringMVC入门案例");
      book.setPrice(66.66d);
  
      ObjectMapper om = new ObjectMapper();
      return om.writeValueAsString(book);
  }
  ```

* 使用 SpringMVC 提供的消息类型转换器将对象与集合数据自动转换为 JSON 数据

  ```java
  //使用SpringMVC注解驱动，对标注@ResponseBody注解的控制器方法进行结果转换，由于返回值为引用类型，自动调用jackson提供的类型转换器进行格式转换
  @RequestMapping("/showData4")
  @ResponseBody
  public Book showData4() {
      Book book  = new Book();
      book.setName("SpringMVC入门案例");
      book.setPrice(66.66d);
      return book;
  }
  ```

  * 手工添加信息类型转换器  

    ```xml
    <bean class="org.springframework.web.servlet.mvc.method.
                 annotation.RequestMappingHandlerAdapter">
        <property name="messageConverters">
            <list>
                <bean class="org.springframework.http.converter.
                          json.MappingJackson2HttpMessageConverter"/>
            </list>
        </property>
    </bean
    ```

  * 使用 SpringMVC 注解驱动：

    ```xml
    <!--开启springmvc注解驱动，对@ResponseBody的注解进行格式增强，追加其类型转换的功能，具体实现由MappingJackson2HttpMessageConverter进行-->
    <mvc:annotation-driven/>
    ```

* 转换集合类型数据

  ```java
  @RequestMapping("/showData5")
  @ResponseBody
  public List showData5() {
      Book book1  = new Book();
      book1.setName("SpringMVC入门案例");
      book1.setPrice(66.66d);
  
      Book book2  = new Book();
      book2.setName("SpringMVC入门案例");
      book2.setPrice(66.66d);
  
      ArrayList al = new ArrayList();
      al.add(book1);
      al.add(book2);
      return al;
  }
  ```



****



### Restful

#### 基本介绍

Rest（REpresentational State Transfer）：表现层状态转化，定义了**资源”在网络传输中以某种“表现形式”进行“状态转移**，即网络资源的访问方式

* 资源：把真实的对象数据称为资源，一个资源既可以是一个集合，也可以是单个个体；每一种资源都有特定的 URI（统一资源标识符）与之对应，如果获取这个资源，访问这个 URI 就可以，比如获取特定的班级`/class/12`；资源也可以包含子资源，比如 `/classes/classId/teachers`某个指定班级的所有老师
* 表现形式："资源"是一种信息实体，它可以有多种外在表现形式，把"资源"具体呈现出来的形式比如 json、xml、image、txt 等等叫做它的"表现层/表现形式"
* 状态转移：描述的服务器端资源的状态，比如增删改查（通过 HTTP 动词实现）引起资源状态的改变，互联网通信协议 HTTP 协议，是一个**无状态协议**，所有的资源状态都保存在服务器端



***



#### 访问方式

Restful 是按照 Rest 风格访问网络资源

* 传统风格访问路径：http://localhost/user/get?id=1
* Rest 风格访问路径：http://localhost/user/1

优点：隐藏资源的访问行为，通过地址无法得知做的是何种操作，书写简化

Restful 请求路径简化配置方式：`@RestController = @Controller + @ResponseBody`

相关注解：

* `@GetMapping("/poll")` = `@RequestMapping(value = "/poll",method = RequestMethod.GET)`

  ```java
  @RequestMapping(method = RequestMethod.GET)
  public @interface GetMapping {
      @AliasFor(annotation = RequestMapping.class)	//与 RequestMapping 相通
  	String name() default "";
  }
  ```

* `@PostMapping("/push")` = `@RequestMapping(value = "/push",method = RequestMethod.POST)`


过滤器：HiddenHttpMethodFilter 是 SpringMVC 对 Restful 风格的访问支持的过滤器

代码实现：

* restful.jsp：

  * 页面表单**使用隐藏域提交请求类型**，参数名称固定为 _method，必须配合提交类型 method=post 使用

  * GET 请求通过地址栏可以发送，也可以通过设置 form 的请求方式提交
  * POST 请求必须通过 form 的请求方式提交

  ```html
  <h1>restful风格请求表单</h1>
  <!--切换请求路径为restful风格-->
  <form action="/user" method="post">
  	<!--一隐藏域，切换为PUT请求或DELETE请求，但是form表单的提交方式method属性必须填写post-->
  	<input name="_method" type="hidden" value="PUT"/>
  	<input value="REST-PUT 提交" type="submit"/>
  </form>
  ```

* java / controller / UserController

  ```java
  @RestController				//设置rest风格的控制器
  @RequestMapping("/user/")	//设置公共访问路径，配合下方访问路径使用
  public class UserController {
      @GetMapping("/user")
      //@RequestMapping(value = "/user",method = RequestMethod.GET)
      public String getUser(){
          return "GET-张三";
      }
  
      @PostMapping("/user")
      //@RequestMapping(value = "/user",method = RequestMethod.POST)
      public String saveUser(){
          return "POST-张三";
      }
  
      @PutMapping("/user")
      //@RequestMapping(value = "/user",method = RequestMethod.PUT)
      public String putUser(){
          return "PUT-张三";
      }
  
      @DeleteMapping("/user")
      //@RequestMapping(value = "/user",method = RequestMethod.DELETE)
      public String deleteUser(){
          return "DELETE-张三";
      }
  }
  ```

* 配置拦截器 web.xml

  ```xml
  <!--配置拦截器，解析请求中的参数_method，否则无法发起PUT请求与DELETE请求，配合页面表单使用-->
  <filter>
      <filter-name>HiddenHttpMethodFilter</filter-name>
      <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
  </filter>
  <filter-mapping>
      <filter-name>HiddenHttpMethodFilter</filter-name>
      <servlet-name>DispatcherServlet</servlet-name>
  </filter-mapping>
  ```



***



#### 参数注解

Restful 开发中的参数注解

```java
@GetMapping("{id}")
public String getMessage(@PathVariable("id") Integer id){
}
```

使用 @PathVariable 注解获取路径上配置的具名变量，一般在有多个参数的时候添加

其他注解：

* @RequestHeader：获取请求头
* @RequestParam：获取请求参数（指问号后的参数，url?a=1&b=2）
* @CookieValue：获取 Cookie 值
* @RequestAttribute：获取 request 域属性
* @RequestBody：获取请求体 [POST]
* @MatrixVariable：矩阵变量
* @ModelAttribute：自定义类型变量

```java
@RestController	
@RequestMapping("/user/")
public class UserController {
    //rest风格访问路径简化书写方式，配合类注解@RequestMapping使用
    @RequestMapping("{id}")
    public String restLocation2(@PathVariable Integer id){
        System.out.println("restful is running ....get:" + id);
        return "success.jsp";
    }

    //@RequestMapping(value = "{id}",method = RequestMethod.GET)
    @GetMapping("{id}")
    public String get(@PathVariable Integer id){
        System.out.println("restful is running ....get:" + id);
        return "success.jsp";
    }

    @PostMapping("{id}")
    public String post(@PathVariable Integer id){
        System.out.println("restful is running ....post:" + id);
        return "success.jsp";
    }

    @PutMapping("{id}")
    public String put(@PathVariable Integer id){
        System.out.println("restful is running ....put:" + id);
        return "success.jsp";
    }

    @DeleteMapping("{id}")
    public String delete(@PathVariable Integer id){
        System.out.println("restful is running ....delete:" + id);
        return "success.jsp";
    }
}
```





****



#### 识别原理

表单提交要使用 REST 时，会带上 `_method=PUT`，请求过来被 `HiddenHttpMethodFilter` 拦截，进行过滤操作

org.springframework.web.filter.HiddenHttpMethodFilter.doFilterInternal()：

```java
public class HiddenHttpMethodFilter extends OncePerRequestFilter {
    //兼容的请求 PUT、DELETE、PATCH
    private static final List<String> ALLOWED_METHODS =
			Collections.unmodifiableList(Arrays.asList(HttpMethod.PUT.name(),
					HttpMethod.DELETE.name(), HttpMethod.PATCH.name()));
    //隐藏域的名字
	public static final String DEFAULT_METHOD_PARAM = "_method";

	private String methodParam = DEFAULT_METHOD_PARAM;
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {

        HttpServletRequest requestToUse = request;
        //请求必须是 POST，
        if ("POST".equals(request.getMethod()) && request.getAttribute(WebUtils.ERROR_EXCEPTION_ATTRIBUTE) == null) {
            //获取标签中 name="_method" 的 value 值
            String paramValue = request.getParameter(this.methodParam);
            if (StringUtils.hasLength(paramValue)) {
                //转成大写
                String method = paramValue.toUpperCase(Locale.ENGLISH);
                //兼容的请求方式
                if (ALLOWED_METHODS.contains(method)) {
                    //包装请求
                    requestToUse = new HttpMethodRequestWrapper(request, method);
                }
            }
        }
        //过滤器链放行的时候用wrapper。以后的方法调用getMethod是调用requesWrapper的
        filterChain.doFilter(requestToUse, response);
    }
}
```

Rest 使用客户端工具，如 PostMan 可直接发送 put、delete 等方式请求不被过滤

改变默认的 `_method` 的方式：

```java
@Configuration(proxyBeanMethods = false)
public class WebConfig{
    //自定义filter
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter(){
        HiddenHttpMethodFilter methodFilter = new HiddenHttpMethodFilter();
        //通过set 方法自定义
        methodFilter.setMethodParam("_m");
        return methodFilter;
    }    
}
```



***



### Servlet

SpringMVC 提供访问原始 Servlet 接口的功能

* SpringMVC 提供访问原始 Servlet 接口 API 的功能，通过形参声明即可 

  ```java
  @RequestMapping("/servletApi")
  public String servletApi(HttpServletRequest request,
                           HttpServletResponse response, HttpSession session){
      System.out.println(request);
      System.out.println(response);
      System.out.println(session);
      request.setAttribute("name","seazean");
      System.out.println(request.getAttribute("name"));
      return "page.jsp";
  }
  ```

* Head 数据获取快捷操作方式
  名称：@RequestHeader
  类型：形参注解
  位置：处理器类中的方法形参前方
  作用：绑定请求头数据与对应处理方法形参间的关系
  范例：

  ```java
  快捷操作方式@RequestMapping("/headApi")
  public String headApi(@RequestHeader("Accept-Language") String headMsg){
      System.out.println(headMsg);
      return "page";
  }  
  ```

* Cookie 数据获取快捷操作方式
  名称：@CookieValue
  类型：形参注解
  位置：处理器类中的方法形参前方
  作用：绑定请求 Cookie 数据与对应处理方法形参间的关系
  范例：

  ```java
  @RequestMapping("/cookieApi")
  public String cookieApi(@CookieValue("JSESSIONID") String jsessionid){
      System.out.println(jsessionid);
      return "page";
  }  
  ```

* Session 数据获取
  名称：@SessionAttribute
  类型：形参注解
  位置：处理器类中的方法形参前方
  作用：绑定请求Session数据与对应处理方法形参间的关系
  范例：

  ```java
  @RequestMapping("/sessionApi")
  public String sessionApi(@SessionAttribute("name") String name){
      System.out.println(name);
      return "page.jsp";
  }
  //用于在session中放入数据
  @RequestMapping("/setSessionData")
  public String setSessionData(HttpSession session){
      session.setAttribute("name","seazean");
      return "page";
  }
  ```

* Session 数据设置
  名称：@SessionAttributes
  类型：类注解
  位置：处理器类上方
  作用：声明放入session范围的变量名称，适用于Model类型数据传参
  范例：

  ```java
  @Controller
  //设定当前类中名称为age和gender的变量放入session范围，不常用
  @SessionAttributes(names = {"age","gender"})
  public class ServletController {
  	//将数据放入session存储范围，Model对象实现数据set，@SessionAttributes注解实现范围设定
      @RequestMapping("/setSessionData2")
      public String setSessionDate2(Model model) {
          model.addAttribute("age",39);
          model.addAttribute("gender","男");
          return "page";
      }
      
      @RequestMapping("/sessionApi")
      public String sessionApi(@SessionAttribute("age") int age,
                               @SessionAttribute("gender") String gender){
          System.out.println(name);
          System.out.println(age);
          return "page";
      }
  }  
  ```

* spring-mvc.xml 配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
  
      <context:component-scan base-package="com.seazean"/>
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/page/"/>
          <property name="suffix" value=".jsp"/>
      </bean>
      <mvc:annotation-driven/>
  </beans>
  ```

  




***



## 运行原理

### 技术架构

#### 组件介绍

* DispatcherServlet：核心控制器， 是 SpringMVC 的核心，整体流程控制的中心，所有的请求第一步都先到达这里，由其调用其它组件处理用户的请求，它就是在 web.xml 配置的核心 Servlet，有效的降低了组件间的耦合性

* HandlerMapping：处理器映射器， 负责根据用户请求找到对应具体的 Handler 处理器，SpringMVC 中针对配置文件方式、注解方式等提供了不同的映射器来处理

* Handler：处理器，其实就是 Controller，业务处理的核心类，通常由开发者编写，并且必须遵守 Controller 开发的规则，这样适配器才能正确的执行。例如实现 Controller 接口，将 Controller 注册到 IOC 容器中等

* HandlAdapter：处理器适配器，根据映射器中找到的 Handler，通过 HandlerAdapter 去执行 Handler，这是适配器模式的应用

* View Resolver：视图解析器， 将 Handler 中返回的逻辑视图（ModelAndView）解析为一个具体的视图（View）对象

* View：视图， View 最后对页面进行渲染将结果返回给用户。Springmvc 框架提供了很多的 View 视图类型，包括：jstlView、freemarkerView、pdfView 等

  ![](https://gitee.com/seazean/images/raw/master/Frame/SpingMVC技术架构.png)



****



#### 工作原理

在 Spring 容器初始化时会建立所有的 URL 和 Controller 的对应关系，保存到 Map<URL,Controller> 中，这样 request 就能快速根据 URL 定位到 Controller：

* 在 Spring IOC 容器初始化完所有单例 bean 后
* SpringMVC 会遍历所有的 bean，获取 controller 中对应的 URL（这里获取 URL 的实现类有多个，用于处理不同形式配置的 Controller）
* 将每一个 URL 对应一个 controller 存入 Map<URL,Controller> 中

注意：将 Controller 类的注解换成 @Component，启动时不会报错，但是在浏览器中输入路径时会出现 404，说明 Spring 没有对所有的 bean 进行 URL 映射

**一个 Request 来了：**

* 监听端口，获得请求：Tomcat 监听 8080 端口的请求，进行接收、解析、封装，根据路径调用了 web.xml 中配置的核心控制器 DispatcherServlet
* 获取 Handler：进入 DispatcherServlet，核心控制器调用 HandlerMapping 去根据请求的 URL 获取对应的 Handler，如果获取的 Handler 为 null 则返回 404
* 调用适配器执行 Handler：
  * 适配器根据 request 的 URL 去 Handler 中寻找对应的处理方法（Controller 的 URL 与方法的 URL 拼接后对比）
  * 获取到对应方法后，需要将 request 中的参数与方法参数上的数据进行绑定，根据反射获取方法的参数名和注解，再根据注解或者根据参数名对照进行绑定（找到对应的参数，然后在反射调用方法时传入）
  * 绑定完参数后，反射调用方法获取 ModelAndView（如果 Handler 中返回的是 String、View 等对象，SpringMVC 也会将它们重新封装成一个 ModelAndView）
* 调用视图解析器解析：将 ModelAndView 解析成 View 对象
* 渲染视图：将 View 对象中的返回地址、参数信息等放入 RequestDispatcher，最后进行转发



****



### 调度函数

请求进入原生的 HttpServlet 的 doGet() 方法处理，调用子类 FrameworkServlet 的 doGet() 方法，最终调用 DispatcherServlet 的 doService() 方法，为请求设置相关属性后调用 doDispatch()，请求和响应的以参数的形式传入

![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-请求相应的原理.png)

```java
//request 和 response 为 Java 原生的类
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;			//文件上传请求
    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);// 异步管理器

    try {
        ModelAndView mv = null;
        Exception dispatchException = null;

        try {
            processedRequest = checkMultipart(request);	//文件上传请求
            multipartRequestParsed = (processedRequest != request);

            // 找到当前请求使用哪个 HandlerMapping（Controller的方法）处理，返回执行链
            mappedHandler = getHandler(processedRequest);
            // 没有合适的处理请求的方式 HandlerMapping 直接返回
            if (mappedHandler == null) {
                noHandlerFound(processedRequest, response);
                return;
            }

            //根据映射器获取当前 handler 处理器适配器，用来处理当前的请求
            HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
            // 获取发出此次请求的方法
            String method = request.getMethod();
            // 判断请求是不是 GET 方法
            boolean isGet = HttpMethod.GET.matches(method);
            if (isGet || HttpMethod.HEAD.matches(method)) {
                long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
                    return;
                }
            }

            if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                return;
            }
            // 执行处理方法，返回的是 ModelAndView 对象，封装了所有的返回值数据
            mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

            if (asyncManager.isConcurrentHandlingStarted()) {
                return;
            }
			// 设置视图名字
            applyDefaultViewName(processedRequest, mv);
            // 执行拦截器链中的方法
            mappedHandler.applyPostHandle(processedRequest, response, mv);
        } catch (Exception ex) {
            dispatchException = ex;
        }
        
        // 处理 程序调用的结果，进行结果派发
        processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
    }
    //....
}
```



笔记参考视频：https://www.bilibili.com/video/BV19K4y1L7MT



***



### 请求映射

#### 映射器

doDispatch() 中调用 getHandler 方法获取所有的映射器

总体流程：

* 所有的请求映射都在 HandlerMapping 中，**RequestMappingHandlerMapping 处理 @RequestMapping 注解的映射规则**

* 遍历所有的 HandlerMapping 看是否可以匹配当前请求，匹配成功后返回，匹配失败设置 HTTP 404 响应码
* 用户可以自定义的映射处理，也可以给容器中放入自定义 HandlerMapping

访问 URL：http://localhost:8080/user

```java
@GetMapping("/user")
public String getUser(){
    return "GET";
}
@PostMapping("/user")
public String postUser(){
    return "POST";
}
//。。。。。
```

HandlerMapping 处理器映射器，保存了所有 `@RequestMapping`  和 `handler` 的映射规则

```java
protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
    if (this.handlerMappings != null) {
        //遍历所有的 HandlerMapping
        for (HandlerMapping mapping : this.handlerMappings) {
            //尝试去每个 HandlerMapping 中匹配当前请求的处理
            HandlerExecutionChain handler = mapping.getHandler(request);
            if (handler != null) {
                return handler;
            }
        }
    }
    return null;
}
```

![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-获取Controller处理器.png)

* `mapping.getHandler(request)`：调用 AbstractHandlerMapping#getHandler

  * `Object handler = getHandlerInternal(request)`：获取映射器，底层调用 RequestMappingInfoHandlerMapping 类的方法，又调用 AbstractHandlerMethodMapping#getHandlerInternal

    * `String lookupPath = initLookupPath(request)`：地址栏的 uri，这里的 lookupPath 为 /user

    * `this.mappingRegistry.acquireReadLock()`：防止并发

    * `handlerMethod = lookupHandlerMethod(lookupPath, request)`：获取当前 HandlerMapping 中的映射规则

      AbstractHandlerMethodMapping.lookupHandlerMethod()：

      * `directPathMatches = this.mappingRegistry.getMappingsByDirectPath(lookupPath)`：获取当前的映射器与当前**请求的 URI 有关的所有映射规则**

        ![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-HandlerMapping的映射规则.png)

      * `addMatchingMappings(directPathMatches, matches, request)`：**匹配某个映射规则**

        * `for (T mapping : mappings)`：遍历所有的映射规则
        * `match = getMatchingMapping(mapping, request)`：去匹配每一个映射规则，匹配失败返回 null
        * `matches.add(new Match())`：匹配成功后封装成匹配器添加到匹配集合中

      * `Match bestMatch = matches.get(0)`：匹配完成只剩一个，直接获取返回对应的处理方法

      * `if (matches.size() > 1)`：当有多个映射规则符合请求时，报错

      * `return bestMatch.getHandlerMethod()`：返回匹配器中的处理方法

  * `executionChain = getHandlerExecutionChain(handler, request)`：**为当前请求和映射器的构建一个拦截器链**

    * `for (HandlerInterceptor interceptor : this.adaptedInterceptors)`：遍历所有的拦截器
    * `chain.addInterceptor(interceptor)`：把所有的拦截器添加到 HandlerExecutionChain 中，形成拦截器链
  
  * `return executionChain`：**返回拦截器链，包含 HandlerMapping 和拦截方法**



****



#### 适配器

doDispatch() 中 调用 `HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler())`

```java
protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
    if (this.handlerAdapters != null) {
        //遍历所有的 HandlerAdapter
        for (HandlerAdapter adapter : this.handlerAdapters) {
            //判断当前适配器是否支持当前 handle
            //return (handler instanceof HandlerMethod && supportsInternal((HandlerMethod) handler))
            //这里返回的是True，
            if (adapter.supports(handler)) {
                //返回的是 RequestMappingHandlerAdapter
                return adapter;
            }
        }
    }
    throw new ServletException();
}
```



***



#### 方法执行

##### 执行流程

实例代码：

```java
@GetMapping("/params")
public String param(Map<String, Object> map, Model model, HttpServletRequest request) {
    map.put("k1", "v1");			//都可以向请求域中添加数据
    model.addAttribute("k2", "v2");	//它们两个都在数据封装在 BindingAwareModelMap
    request.setAttribute("m", "HelloWorld");
    return "forward:/success";
}
```

![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-Model和Map的数据解析.png)

doDispatch() 中调用 `mv = ha.handle(processedRequest, response, mappedHandler.getHandler())` 执行目标方法

`AbstractHandlerMethodAdapter#handle` → `RequestMappingHandlerAdapter#handleInternal` → `invokeHandlerMethod`：

```java
//使用适配器执行方法
protected ModelAndView invokeHandlerMethod(HttpServletRequest request,
                                           HttpServletResponse response, 
                                           HandlerMethod handlerMethod) throws Exception {
	//封装成 SpringMVC 的接口，用于通用 Web 请求拦截器，使能够访问通用请求元数据，而不是用于实际处理请求
    ServletWebRequest webRequest = new ServletWebRequest(request, response);
    try {
        //WebDataBinder 用于从 Web 请求参数到 JavaBean 对象的数据绑定，获取创建该实例的工厂
        WebDataBinderFactory binderFactory = getDataBinderFactory(handlerMethod);
        //创建 Model 实例，用于向模型添加属性
        ModelFactory modelFactory = getModelFactory(handlerMethod, binderFactory);
		//方法执行器
        ServletInvocableHandlerMethod invocableMethod = createInvocableHandlerMethod(handlerMethod);
        
        //参数解析器，有很多
        if (this.argumentResolvers != null) {
            invocableMethod.setHandlerMethodArgumentResolvers(this.argumentResolvers);
        }
        //返回值处理器，也有很多
        if (this.returnValueHandlers != null) {
            invocableMethod.setHandlerMethodReturnValueHandlers(this.returnValueHandlers);
        }
        //设置数据绑定器
        invocableMethod.setDataBinderFactory(binderFactory);
        //设置参数检查器
		invocableMethod.setParameterNameDiscoverer(this.parameterNameDiscoverer);
   
        //新建一个 ModelAndViewContainer 并进行初始化和一些属性的填充
        ModelAndViewContainer mavContainer = new ModelAndViewContainer();
            
        //设置一些属性
        
        //执行目标方法
        invocableMethod.invokeAndHandle(webRequest, mavContainer);
        //异步请求
        if (asyncManager.isConcurrentHandlingStarted()) {
            return null;
        }
		// 获取 ModelAndView 对象，封装了 ModelAndViewContainer
        return getModelAndView(mavContainer, modelFactory, webRequest);
    }
    finally {
        webRequest.requestCompleted();
    }
}
```

**ServletInvocableHandlerMethod#invokeAndHandle**：执行目标方法

* `returnValue = invokeForRequest(webRequest, mavContainer, providedArgs)`：**执行自己写的 controller 方法，返回的就是自定义方法中 return 的值**

  `Object[] args = getMethodArgumentValues(request, mavContainer, providedArgs)`：**参数处理的逻辑**，遍历所有的参数解析器解析参数或者将 URI 中的参数进行绑定，绑定完成后开始执行目标方法

  * `parameters = getMethodParameters()`：获取此处理程序方法的方法参数的详细信息

  * `Object[] args = new Object[parameters.length]`：存放所有的参数

  * `for (int i = 0; i < parameters.length; i++)`：遍历所有的参数

  * `args[i] = findProvidedArgument(parameter, providedArgs)`：获取调用方法时提供的参数，一般是空

  * `if (!this.resolvers.supportsParameter(parameter))`：**获取可以解析当前参数的参数解析器**

    * `return getArgumentResolver(parameter) != null`：获取参数的解析是否为空

      * `for (HandlerMethodArgumentResolver resolver : this.argumentResolvers)`：遍历容器内所有的解析器

        `if (resolver.supportsParameter(parameter))`：是否支持当前参数

        * `PathVariableMethodArgumentResolver#supportsParameter`：**解析标注 @PathVariable 注解的参数**
    * `ModelMethodProcessor#supportsParameter`：解析 Map 类型的参数
        * `ModelMethodProcessor#supportsParameter`：解析 Model 类型的参数，Model 和 Map 的作用一样
    * `ExpressionValueMethodArgumentResolver#supportsParameter`：解析标注 @Value 注解的参数
        * `RequestParamMapMethodArgumentResolver#supportsParameter`：**解析标注 @RequestParam 注解**
    * `RequestPartMethodArgumentResolver#supportsParameter`：解析文件上传的信息
        * `ModelAttributeMethodProcessor#supportsParameter`：解析标注 @ModelAttribute 注解或者不是简单类型
      * 子类 ServletModelAttributeMethodProcessor 是**解析自定义类型 JavaBean 的解析器**
          * 简单类型有 Void、Enum、Number、CharSequence、Date、URI、URL、Locale、Class

  * `args[i] = this.resolvers.resolveArgument()`：**开始解析参数，每个参数使用的解析器不同**
  
    `resolver = getArgumentResolver(parameter)`：获取参数解析器

    `return resolver.resolveArgument()`：开始解析

    * `PathVariableMapMethodArgumentResolver#resolveArgument`：@PathVariable，包装 URI 中的参数为 Map
  * `MapMethodProcessor#resolveArgument`：调用 `mavContainer.getModel()` 返回默认的 BindingAwareModelMap 对象
    * `ModelAttributeMethodProcessor#resolveArgument`：**自定义的 JavaBean 的绑定封装**，下一小节详解

  `return doInvoke(args)`：真正的执行方法
  
  * `Method method = getBridgedMethod()`：从 HandlerMethod 获取要反射执行的方法
* `ReflectionUtils.makeAccessible(method)`：破解权限
  * `method.invoke(getBean(), args)`：**执行方法**，getBean 获取的是标记 @Controller 的 Bean 类，其中包含执行方法

* **进行返回值的处理，响应部分详解**，处理完成进入下面的逻辑

**RequestMappingHandlerAdapter#getModelAndView**：获取 ModelAndView 对象

* `modelFactory.updateModel(webRequest, mavContainer)`：Model 数据升级到会话域（**请求域中的数据在重定向时丢失**）

  `updateBindingResult(request, defaultModel)`：把绑定的数据添加到 Model 中

* `if (mavContainer.isRequestHandled())`：判断请求是否已经处理完成了

* `ModelMap model = mavContainer.getModel()`：获取**包含 Controller 方法参数**的 BindingAwareModelMap 对象（本节开头）

* `mav = new ModelAndView()`：**把 ModelAndViewContainer 和 ModelMap 中的数据封装到 ModelAndView** 

* `if (!mavContainer.isViewReference())`：视图是否是通过名称指定视图引用

* `if (model instanceof RedirectAttributes)`：判断 model 是否是重定向数据，如果是进行重定向逻辑

* `return mav`：**任何方法执行都会返回 ModelAndView 对象**



***



##### 参数解析

解析自定义的 JavaBean 为例

* Person.java：

  ```java
  @Data
  @Component	//加入到容器中
  public class Person {
      private String userName;
      private Integer age;
      private Date birth;
  }
  ```

* Controller：

  ```java
  @RestController	//返回的数据不是页面
  public class ParameterController {
      // 数据绑定：页面提交的请求数据（GET、POST）都可以和对象属性进行绑定
      @GetMapping("/saveuser")
      public Person saveuser(Person person){
          return person;
      }
  }
  ```

* 访问 URL：http://localhost:8080/saveuser?userName=zhangsan&age=20

进入源码：ModelAttributeMethodProcessor#resolveArgument

* `name = ModelFactory.getNameForParameter(parameter)`：获取名字，此例就是 person

* `ann = parameter.getParameterAnnotation(ModelAttribute.class)`：是否有 ModelAttribute 注解

* `if (mavContainer.containsAttribute(name))`：ModelAndViewContainer 中是否包含 person 对象

* `attribute = createAttribute()`：**创建一个实例，空的 Person 对象**

* `binder = binderFactory.createBinder(webRequest, attribute, name)`：Web 数据绑定器，可以利用 Converters 将请求数据转成指定的数据类型，绑定到 JavaBean 中

* `bindRequestParameters(binder, webRequest)`：利用反射向目标对象填充数据

  `servletBinder = (ServletRequestDataBinder) binder`：类型强转

  `servletBinder.bind(servletRequest)`：绑定数据

  * `mpvs = new MutablePropertyValues(request.getParameterMap())`：获取请求 URI 参数中的 KV 键值对

  * `addBindValues(mpvs, request)`：子类可以用来为请求添加额外绑定值

  * `doBind(mpvs)`：真正的绑定的方法，调用 `applyPropertyValues` 应用参数值，然后调用 `setPropertyValues` 方法

    `AbstractPropertyAccessor#setPropertyValues()`：

    * `List<PropertyValue> propertyValues`：获取到所有的参数的值，就是 URI 上的所有的参数值

    * `for (PropertyValue pv : propertyValues)`：遍历所有的参数值

    * `setPropertyValue(pv)`：**填充到空的 Person 实例中**

      * `nestedPa = getPropertyAccessorForPropertyPath(propertyName)`：获取属性访问器

      * `tokens = getPropertyNameTokens()`：获取元数据的信息

      * `nestedPa.setPropertyValue(tokens, pv)`：填充数据

      * `processLocalProperty(tokens, pv)`：处理属性

        * `if (!Boolean.FALSE.equals(pv.conversionNecessary))`：数据是否需要转换了

        * `if (pv.isConverted())`：数据已经转换过了，转换了直接赋值，没转换进行转换

        * `oldValue = ph.getValue()`：获取未转换的数据

        * `valueToApply = convertForProperty()`：进行数据转换

          `TypeConverterDelegate#convertIfNecessary`：进入该方法的逻辑

          * `if (conversionService.canConvert(sourceTypeDesc, typeDescriptor))`：判断能不能转换

            `GenericConverter converter = getConverter(sourceType, targetType)`：获取**类型转换器**

            * `converter = this.converters.find(sourceType, targetType)`：寻找合适的转换器

              * `sourceCandidates = getClassHierarchy(sourceType.getType())`：原数据类型

              * `targetCandidates = getClassHierarchy(targetType.getType())`：目标数据类型

                ```java
                for (Class<?> sourceCandidate : sourceCandidates) {
                    //双重循环遍历，寻找合适的转换器
                 	for (Class<?> targetCandidate : targetCandidates) {
                ```

              * `GenericConverter converter = getRegisteredConverter(..)`：匹配类型转换器

              * `return converter`：返回转换器

          * `conversionService.convert(newValue, sourceTypeDesc, typeDescriptor)`：开始转换

            * `converter = getConverter(sourceType, targetType)`：**获取可用的转换器**
            * `result = ConversionUtils.invokeConverter()`：执行转换方法
              * `converter.convert()`：**调用转换器的转换方法**（GenericConverter#convert）
            * `return handleResult(sourceType, targetType, result)`：返回结果

        * `ph.setValue(valueToApply)`：设置 JavaBean 属性（BeanWrapperImpl.BeanPropertyHandler）

          * `Method writeMethod`：获取 set 方法
            * `Class<?> cls = getClass0()`：获取 Class 对象
            * `writeMethodName = Introspector.SET_PREFIX + getBaseName()`：set 前缀 + 属性名
            * `writeMethod = Introspector.findMethod(cls, writeMethodName, 1, args)`：获取只包含一个参数的 set 方法
            * `setWriteMethod(writeMethod)`：加入缓存
          * `ReflectionUtils.makeAccessible(writeMethod)`：设置访问权限
          * `writeMethod.invoke(getWrappedInstance(), value)`：执行方法

* `bindingResult = binder.getBindingResult()`：获取绑定的结果

* `mavContainer.addAllAttributes(bindingResultModel)`：**把所有填充的参数放入 ModelAndViewContainer**

* `return attribute`：返回填充后的 Person 对象





****



### 响应处理

#### 响应数据

以 Person 为例：

```java
@ResponseBody  		//利用返回值处理器里面的消息转换器进行处理
@GetMapping(value = "/person")
public Person getPerson(){
    Person person = new Person();
    person.setAge(28);
    person.setBirth(new Date());
    person.setUserName("zhangsan");
    return person;
}
```

直接进入方法执行完后的逻辑 ServletInvocableHandlerMethod#invokeAndHandle：

```java
public void invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContainer mavContainer,
                            Object... providedArgs) throws Exception {
	// 执行目标方法，return person 对象
    Object returnValue = invokeForRequest(webRequest, mavContainer, providedArgs);
    // 设置状态码
    setResponseStatus(webRequest);

    // 判断方法是否有返回值
    if (returnValue == null) {
        if (isRequestNotModified(webRequest) || getResponseStatus() != null || mavContainer.isRequestHandled()) {
            disableContentCachingIfNecessary(webRequest);
            mavContainer.setRequestHandled(true);
            return;
        }
    }	//返回值是字符串
    else if (StringUtils.hasText(getResponseStatusReason())) {
        //设置请求处理完成
        mavContainer.setRequestHandled(true);
        return;
	// 设置请求没有处理完成，还需要进行返回值的逻辑
    mavContainer.setRequestHandled(false);
    Assert.state(this.returnValueHandlers != null, "No return value handlers");
    try {
        // 返回值的处理
        this.returnValueHandlers.handleReturnValue(
            returnValue, getReturnValueType(returnValue), mavContainer, webRequest);
    }
    catch (Exception ex) {}
}
```

* 没有加 @ResponseBody 注解的返回数据按照视图（页面）处理的逻辑，ViewNameMethodReturnValueHandler（视图详解）

* 此例是加了注解的，返回的数据不是视图，HandlerMethodReturnValueHandlerComposite#handleReturnValue：

  ```java
  public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType,
                                ModelAndViewContainer mavContainer, NativeWebRequest webRequest)  {
  	//获取合适的返回值处理器
      HandlerMethodReturnValueHandler handler = selectHandler(returnValue, returnType);
      if (handler == null) {
          throw new IllegalArgumentException();
      }
      //使用处理器处理返回值（详解源码中的这两个函数）
      handler.handleReturnValue(returnValue, returnType, mavContainer, webRequest);
  }
  ```

**HandlerMethodReturnValueHandlerComposite#selectHandler**：

* `boolean isAsyncValue = isAsyncReturnValue(value, returnType)`：是否是异步请求

* `for (HandlerMethodReturnValueHandler handler : this.returnValueHandlers)`：遍历所有的返回值处理器
  * `RequestResponseBodyMethodProcessor#supportsReturnType`：**处理标注 @ResponseBody 注解的返回值**
  * `ModelAndViewMethodReturnValueHandler#supportsReturnType`：处理**返回值类型**是 ModelAndView 的处理器
  * `ModelAndViewResolverMethodReturnValueHandler#supportsReturnType`：直接返回 true，处理所有数据

**RequestResponseBodyMethodProcessor#handleReturnValue**：处理返回值

* `mavContainer.setRequestHandled(true)`：设置请求处理完成

* `inputMessage = createInputMessage(webRequest)`：获取输入的数据

* `outputMessage = createOutputMessage(webRequest)`：获取输出的数据

* `writeWithMessageConverters(returnValue, returnType, inputMessage, outputMessage)`：使用消息转换器进行写出

  * `if (value instanceof CharSequence)`：判断返回的数据是不是字符类型

  * `body = value`：把 value 赋值给 body，此时 body 中就是填充后的 Person 对象

  * `if (isResourceType(value, returnType))`：当前数据是不是流数据

  * `MediaType selectedMediaType`：**内容协商后选择使用的类型，浏览器和服务器都支持的媒体（数据）类型**

    `MediaType contentType = outputMessage.getHeaders().getContentType()`：获取响应头的数据

  * `if (contentType != null && contentType.isConcrete())`：判断当前响应头中是否已经有确定的媒体类型

    `selectedMediaType = contentType`：说明前置处理已经使用了媒体类型，直接继续使用该类型

  * `acceptableTypes = getAcceptableMediaTypes(request)`：**获取浏览器支持的媒体类型，请求头字段**

    `this.contentNegotiationManager.resolveMediaTypes(new ServletWebRequest(request))`：调用该方法

    * `for(ContentNegotiationStrategy strategy:this.strategies)`：默认策略是提取请求头的字段的内容，策略类为**HeaderContentNegotiationStrategy**，可以配置添加其他类型的策略
    * `List<MediaType> mediaTypes = strategy.resolveMediaTypes(request)`：解析 Accept 字段存储为 List
      * `headerValueArray = request.getHeaderValues(HttpHeaders.ACCEPT)`：获取请求头中 Accept 字段
      * `List<MediaType> mediaTypes = MediaType.parseMediaTypes(headerValues)`：解析成 List 集合
      * `MediaType.sortBySpecificityAndQuality(mediaTypes)`：按照相对品质因数 q 降序排序

    ![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-浏览器支持接收的数据类型.png)

  * `producibleTypes = getProducibleMediaTypes(request, valueType, targetType)`：服务器能生成的媒体类型

    * `request.getAttribute(HandlerMapping.PRODUCIBLE_MEDIA_TYPES_ATTRIBUTE)`：从请求域获取默认的媒体类型
    * ` for (HttpMessageConverter<?> converter : this.messageConverters)`：遍历所有的消息转换器
    * `converter.canWrite(valueClass, null)`：是否支持当前的类型
    * ` result.addAll(converter.getSupportedMediaTypes())`：把当前 MessageConverter 支持的所有类型放入 result

  * `List<MediaType> mediaTypesToUse = new ArrayList<>()`：存储最佳匹配

  * **内容协商：**

    ```java
    for (MediaType requestedType : acceptableTypes) {			//遍历所有的浏览器能接受的媒体类型
        for (MediaType producibleType : producibleTypes) {		//遍历所有服务器能产出的
            if (requestedType.isCompatibleWith(producibleType)) {	//判断类型是否匹配，最佳匹配
                //数据协商匹配成功
                mediaTypesToUse.add(getMostSpecificMediaType(requestedType, producibleType));
            }
        }
    }
    ```

  * `MediaType.sortBySpecificityAndQuality(mediaTypesToUse)`：按照相对品质因数 q 排序，降序排序，越大的越好

  * `for (MediaType mediaType : mediaTypesToUse)`：**遍历所有的最佳匹配**

    `selectedMediaType = mediaType`：赋值给选择的类型

  * `selectedMediaType = selectedMediaType.removeQualityValue()`：媒体类型去除相对品质因数

  * `for (HttpMessageConverter<?> converter : this.messageConverters)`：遍历所有的 HTTP 数据转换器

  * `GenericHttpMessageConverter genericConverter`：**MappingJackson2HttpMessageConverter 可以将对象写为 JSON**

  * `((GenericHttpMessageConverter) converter).canWrite()`：转换器是否可以写出给定的类型

    `AbstractJackson2HttpMessageConverter#canWrit`

    * `if (!canWrite(mediaType))`：是否可以写出指定类型
      * `MediaType.ALL.equalsTypeAndSubtype(mediaType)`：是不是 `*/*` 类型
      * `getSupportedMediaTypes()`：支持 `application/json` 和 `application/*+json` 两种类型
      * `return true`：返回 true
    * `objectMapper = selectObjectMapper(clazz, mediaType)`：选择可以使用的 objectMapper 
    * `causeRef = new AtomicReference<>()`：获取并发安全的引用
    * `if (objectMapper.canSerialize(clazz, causeRef))`：objectMapper 可以序列化当前类
    * `return true`：返回 true

  * ` body = getAdvice().beforeBodyWrite()`：**要响应的所有数据，Person 对象**

  * `addContentDispositionHeader(inputMessage, outputMessage)`：检查路径

  * `genericConverter.write(body, targetType, selectedMediaType, outputMessage)`：调用消息转换器的 write 方法

    `AbstractGenericHttpMessageConverter#write`：该类的方法

    * `addDefaultHeaders(headers, t, contentType)`：设置响应头中的数据类型

      ![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-服务器设置数据类型.png)

    * `writeInternal(t, type, outputMessage)`：**真正的写出数据的函数**

      * `Object value = object`：value 引用 Person 对象
      * `ObjectWriter objectWriter = objectMapper.writer()`：获取用来输出 JSON 对象的 ObjectWriter
      * `objectWriter.writeValue(generator, value)`：写出数据为 JSON





***



#### 协商策略

开启基于请求参数的内容协商模式：（SpringBoot 方式）

```yaml
spring.mvc.contentnegotiation:favor-parameter: true  #开启请求参数内容协商模式
```

发请求： http://localhost:8080/person?format=json，解析 format

策略类为 ParameterContentNegotiationStrategy，运行流程如下：

* `acceptableTypes = getAcceptableMediaTypes(request)`：获取浏览器支持的媒体类型

  `mediaTypes = strategy.resolveMediaTypes(request)`：解析请求 URL 参数中的数据

  * `return resolveMediaTypeKey(webRequest, getMediaTypeKey(webRequest))`：

    `getMediaTypeKey(webRequest)`：

    * `request.getParameter(getParameterName())`：获取 URL 中指定的需求的数据类型
      * `getParameterName()`：获取参数的属性名 format
      * `getParameter()`：获取 URL 中 format 对应的数据

    `resolveMediaTypeKey()`：解析媒体类型，封装成集合

自定义内容协商策略：

```java
public class WebConfig implements WebMvcConfigurer {
    @Bean
    public WebMvcConfigurer webMvcConfigurer() {
        return new WebMvcConfigurer() {
            @Override	//自定义内容协商策略
            public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
                Map<String, MediaType> mediaTypes = new HashMap<>();
                mediaTypes.put("json", MediaType.APPLICATION_JSON);
                mediaTypes.put("xml",MediaType.APPLICATION_XML);
                mediaTypes.put("person",MediaType.parseMediaType("application/x-person"));
                //指定支持解析哪些参数对应的哪些媒体类型
                ParameterContentNegotiationStrategy parameterStrategy = new ParameterContentNegotiationStrategy(mediaTypes);

                //请求头解析
                HeaderContentNegotiationStrategy headStrategy = new HeaderContentNegotiationStrategy();

                //添加到容器中，即可以解析请求头 又可以解析请求参数
                configurer.strategies(Arrays.asList(parameterStrategy,headStrategy));
            }
            
            @Override 	//自定义消息转换器
            public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
                converters.add(new GuiguMessageConverter());
            }
        }
    }
}
```

也可以自定义 HttpMessageConverter，实现 HttpMessageConverter<T> 接口重写方法即可



***



### 视图解析

#### 返回解析

请求处理：

```java
@GetMapping("/params")
public String param(){
	return "forward:/success";
    //return "redirect:/success";
}
```

进入执行方法逻辑 ServletInvocableHandlerMethod#invokeAndHandle，进入 `this.returnValueHandlers.handleReturnValue`：

```java
public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType,
                              ModelAndViewContainer mavContainer, NativeWebRequest webRequest)  {
	//获取合适的返回值处理器：调用 if (handler.supportsReturnType(returnType))判断是否支持
    HandlerMethodReturnValueHandler handler = selectHandler(returnValue, returnType);
    if (handler == null) {
        throw new IllegalArgumentException();
    }
    //使用处理器处理返回值
    handler.handleReturnValue(returnValue, returnType, mavContainer, webRequest);
}
```

* ViewNameMethodReturnValueHandler#supportsReturnType

  ```java
  public boolean supportsReturnType(MethodParameter returnType) {
      Class<?> paramType = returnType.getParameterType();
      //返回值是否是void 或者 是 CharSequence 字符序列
      return (void.class == paramType || CharSequence.class.isAssignableFrom(paramType));
  }
  ```

* ViewNameMethodReturnValueHandler#handleReturnValue

  ```java
  public void handleReturnValue(@Nullable Object returnValue, MethodParameter returnType,
                                ModelAndViewContainer mavContainer, 
                                NativeWebRequest webRequest) throws Exception {
  	// 返回值是字符串，是 return "forward:/success"
      if (returnValue instanceof CharSequence) {
          String viewName = returnValue.toString();
          //把视图名称设置进入 ModelAndViewContainer 中
          mavContainer.setViewName(viewName);
          //判断是否是重定向数据 `viewName.startsWith("redirect:")`
          if (isRedirectViewName(viewName)) {
              //如果是重定向，设置是重定向指令
              mavContainer.setRedirectModelScenario(true);
          }
      }
      else if (returnValue != null) {
          // should not happen
          throw new UnsupportedOperationException();
      }
  }
  ```

  



***



#### 结果派发

doDispatch()中的 processDispatchResult：处理派发结果

```java
private void processDispatchResult(HttpServletRequest request, HttpServletResponse response,
                                   @Nullable HandlerExecutionChain mappedHandler, 
                                   @Nullable ModelAndView mv,
                                   @Nullable Exception exception) throws Exception {
    boolean errorView = false;
    if (exception != null) {
    }
    // mv 是 ModelAndValue
    if (mv != null && !mv.wasCleared()) {
        // 渲染视图
        render(mv, request, response);
        if (errorView) {
            WebUtils.clearErrorRequestAttributes(request);
        }
    }
    else {}  
}
```

DispatcherServlet#render：

* `Locale locale = this.localeResolver.resolveLocale(request)`：国际化相关

* `String viewName = mv.getViewName()`：视图名字，是请求转发 forward:/success（响应数据部分解析了该名字存入 ModelAndView 是**通过 ViewNameMethodReturnValueHandler**）

* `view = resolveViewName(viewName, mv.getModelInternal(), locale, request)`：解析视图

  * `for (ViewResolver viewResolver : this.viewResolvers)`：遍历所有的视图解析器

    `view = viewResolver.resolveViewName(viewName, locale)`：根据视图名字解析视图，调用内容协商视图处理器 ContentNegotiatingViewResolver 的方法

    * `attrs = RequestContextHolder.getRequestAttributes()`：获取请求的相关属性信息

    * `requestedMediaTypes = getMediaTypes(((ServletRequestAttributes) attrs).getRequest())`：获取最佳匹配的媒体类型，函数内进行了匹配的逻辑

    * `candidateViews = getCandidateViews(viewName, locale, requestedMediaTypes)`：获取候选的视图对象

      * `for (ViewResolver viewResolver : this.viewResolvers)`：遍历所有的视图解析器

      * `View view = viewResolver.resolveViewName(viewName, locale)`：解析视图

        `AbstractCachingViewResolver#resolveViewName`：调用此方法

        **请求转发**：实例为 InternalResourceView 

        * `returnview = createView(viewName, locale)`：UrlBasedViewResolver#createView

          * `if (viewName.startsWith(FORWARD_URL_PREFIX))`：视图名字是否是 **`forward:`** 的前缀
          * `forwardUrl = viewName.substring(FORWARD_URL_PREFIX.length())`：**名字截取前缀**
          * `view = new InternalResourceView(forwardUrl)`：新建 InternalResourceView  对象并返回

          * `return applyLifecycleMethods(FORWARD_URL_PREFIX, view)`：Spring 中的初始化操作

        **重定向**：实例为 RedirectView 

        * `returnview = createView(viewName, locale)`：UrlBasedViewResolver#createView
          * `if (viewName.startsWith(REDIRECT_URL_PREFIX))`：视图名字是否是 **`redirect:`** 的前缀
          * `redirectUrl = viewName.substring(REDIRECT_URL_PREFIX.length())`：名字截取前缀
          * `RedirectView view = new RedirectView()`：新建 RedirectView 对象并返回

    * `bestView = getBestView(candidateViews, requestedMediaTypes, attrs)`：选出最佳匹配的视图对象

* `view.render(mv.getModelInternal(), request, response)`：**页面渲染**

  * `mergedModel = createMergedOutputModel(model, request, response)`：把请求域中的数据封装到 Map

  * `prepareResponse(request, response)`：响应前的准备工作，设置一些响应头

  * `renderMergedOutputModel(mergedModel, getRequestToExpose(request), response)`：渲染输出的数据

    **请求转发** InternalResourceView 的逻辑：

    * `getRequestToExpose(request)`：获取 Servlet 原生的方式
    * `exposeModelAsRequestAttributes(model, request)`：暴露 model 作为请求域的属性
      * `model.forEach()`：遍历 Model 中的数据
      * `request.setAttribute(name, value)`：设置到请求域中
    * `exposeHelpers(request)`：自定义接口
    * `dispatcherPath = prepareForRendering(request, response)`：确定调度分派的路径，此例是 /success
    * `rd = getRequestDispatcher(request, dispatcherPath)`：**获取 Servlet 原生的 RequestDispatcher 实现转发**
    * `rd.forward(request, response)`：实现请求转发

    **重定向** RedirectView 的逻辑：

    * `targetUrl = createTargetUrl(model, request)`：获取目标 URL
      * `enc = request.getCharacterEncoding()`：设置编码 UTF-8
      * `appendQueryProperties(targetUrl, model, enc)`：添加一些属性，比如 `url + ?name=123&&age=324`
      * `sendRedirect(request, response, targetUrl, this.http10Compatible)`：重定向

        * `response.sendRedirect(encodedURL)`：**使用 Servlet 原生方法实现重定向**







****





## 异步调用

### 请求参数

名称：@RequestBody
类型：形参注解
位置：处理器类中的方法形参前方
作用：将异步提交数据**转换**成标准请求参数格式，并赋值给形参
范例：

```java
@Controller //控制层
public class AjaxController {
    @RequestMapping("/ajaxController")
    public String ajaxController(@RequestBody String message){
        System.out.println(message);
        return "page.jsp";
    }  
}
```

* 注解添加到 Pojo 参数前方时，封装的异步提交数据按照 Pojo 的属性格式进行关系映射
  * POJO 中的属性如果请求数据中没有，属性值为 null
  * POJO 中没有的属性如果请求数据中有，不进行映射
* 注解添加到集合参数前方时，封装的异步提交数据按照集合的存储结构进行关系映射 

```java
@RequestMapping("/ajaxPojoToController")
//如果处理参数是POJO，且页面发送的请求数据格式与POJO中的属性对应，@RequestBody注解可以自动映射对应请求数据到POJO中
public String  ajaxPojoToController(@RequestBody User user){
    System.out.println("controller pojo :"+user);
    return "page.jsp";
}

@RequestMapping("/ajaxListToController")
//如果处理参数是List集合且封装了POJO，且页面发送的数据是JSON格式，数据将自动映射到集合参数
public String  ajaxListToController(@RequestBody List<User> userList){
    System.out.println("controller list :"+userList);
    return "page.jsp";
}
```

ajax.jsp

```html
<%@page pageEncoding="UTF-8" language="java" contentType="text/html;UTF-8" %>

<a href="javascript:void(0);" id="testAjax">访问springmvc后台controller</a><br/>
<a href="javascript:void(0);" id="testAjaxPojo">传递Json格式POJO</a><br/>
<a href="javascript:void(0);" id="testAjaxList">传递Json格式List</a><br/>
    
<script type="text/javascript" src="${pageContext.request.contextPath}/js/jquery-3.3.1.min.js"></script>
<script type="text/javascript">
    $(function () {
        //为id="testAjax"的组件绑定点击事件
        $("#testAjax").click(function(){
            //发送异步调用
            $.ajax({
               //请求方式：POST请求
               type:"POST",
               //请求的地址
               url:"ajaxController",
               //请求参数（也就是请求内容）
               data:'ajax message',
               //响应正文类型
               dataType:"text",
               //请求正文的MIME类型
               contentType:"application/text",
            });
        });
        
         //为id="testAjaxPojo"的组件绑定点击事件
        $("#testAjaxPojo").click(function(){
            $.ajax({
               type:"POST",
               url:"ajaxPojoToController",
               data:'{"name":"Jock","age":39}',
               dataType:"text",
               contentType:"application/json",
            });
        });
        
        //为id="testAjaxList"的组件绑定点击事件
        $("#testAjaxList").click(function(){
            $.ajax({//.....
               data:'[{"name":"Jock","age":39},{"name":"Jockme","age":40}]'})}
    }
</script>
```

web.xml配置：请求响应章节请求中的web.xml配置

```xml
CharacterEncodingFilter + DispatcherServlet
```

spring-mvc.xml：

```xml
<context:component-scan base-package="controller,domain"/>
<mvc:resources mapping="/js/**" location="/js/"/>
<mvc:annotation-driven/>
```



****



### 响应数据

注解：@ResponseBody
作用：将 java 对象转为 json 格式的数据

方法返回值为 POJO 时，自动封装数据成 Json 对象数据：

```java
@RequestMapping("/ajaxReturnJson")
@ResponseBody
public User ajaxReturnJson(){
    System.out.println("controller return json pojo...");
    User user = new User("Jockme",40);
    return user;
}  
```

方法返回值为 List 时，自动封装数据成 json 对象数组数据：

```java
@RequestMapping("/ajaxReturnJsonList")
@ResponseBody
//基于jackon技术，使用@ResponseBody注解可以将返回的保存POJO对象的集合转成json数组格式数据
public List ajaxReturnJsonList(){
    System.out.println("controller return json list...");
    User user1 = new User("Tom",3);
    User user2 = new User("Jerry",5);

    ArrayList al = new ArrayList();
    al.add(user1);
    al.add(user2);
    return al;
}
```

AJAX 文件：

```js
//为id="testAjaxReturnString"的组件绑定点击事件
$("#testAjaxReturnString").click(function(){
    //发送异步调用
    $.ajax({
        type:"POST",
        url:"ajaxReturnString",
        //回调函数
        success:function(data){
            //打印返回结果
            alert(data);
        }
    });
});

//为id="testAjaxReturnJson"的组件绑定点击事件
$("#testAjaxReturnJson").click(function(){
    $.ajax({
        type:"POST",
        url:"ajaxReturnJson",
        success:function(data){
            alert(data['name']+" ,  "+data['age']);
        }
    });
});

//为id="testAjaxReturnJsonList"的组件绑定点击事件
$("#testAjaxReturnJsonList").click(function(){
    $.ajax({
        type:"POST",
        url:"ajaxReturnJsonList",
        success:function(data){
            alert(data);
            alert(data[0]["name"]);
            alert(data[1]["age"]);
        }
    });
});
```



****



### 跨域访问

跨域访问：当通过域名 A 下的操作访问域名 B 下的资源时，称为跨域访问。跨域访问时，会出现无法访问的现象。

环境搭建：

* 为当前主机添加备用域名
  * 修改 windows 安装目录中的 host 文件
  * 格式： ip 域名
* 动态刷新 DNS
  *  命令： ipconfig /displaydns
  *  命令： ipconfig /flushdns   

跨域访问支持：

* 名称：@CrossOrigin
* 类型：方法注解 、 类注解
* 位置：处理器类中的方法上方 或 类上方
* 作用：设置当前处理器方法 / 处理器类中所有方法支持跨域访问
* 范例：  

```java
@RequestMapping("/cross")
@ResponseBody
//使用@CrossOrigin开启跨域访问
//标注在处理器方法上方表示该方法支持跨域访问
//标注在处理器类上方表示该处理器类中的所有处理器方法均支持跨域访问
@CrossOrigin
public User cross(HttpServletRequest request){
    System.out.println("controller cross..." + request.getRequestURL());
    User user = new User("Jockme",36);
    return user;
}
```

* jsp 文件

```html
<a href="javascript:void(0);" id="testCross">跨域访问</a><br/>
<script type="text/javascript" src="${pageContext.request.contextPath}/js/jquery-3.3.1.min.js"></script>
<script type="text/javascript">
    $(function () {
        //为id="testCross"的组件绑定点击事件
        $("#testCross").click(function(){
            //发送异步调用
            $.ajax({
               type:"POST",
               url:"http://127.0.0.1/cross",
               //回调函数
               success:function(data){
                   alert("跨域调用信息反馈:" + data['name'] + "," + data['age']);
               }
            });
        });
    });
</script>
```





***





## 拦截器

### 基本介绍

拦截器（Interceptor）是一种动态拦截方法调用的机制

作用：

1. 在指定的方法调用前后执行预先设定后的的代码
2. 阻止原始方法的执行

核心原理：AOP 思想

拦截器链：多个拦截器按照一定的顺序，对原始被调用功能进行增强  

拦截器和过滤器对比：

1. 归属不同： Filter 属于 Servlet 技术， Interceptor 属于 SpringMVC 技术

2. 拦截内容不同： Filter 对所有访问进行增强， Interceptor 仅针对 SpringMVC 的访问进行增强  

   <img src="https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-过滤器和拦截器的运行机制.png" style="zoom:67%;" />



***



### 处理方法

#### 前置处理

原始方法之前运行：

```java
public boolean preHandle(HttpServletRequest request,
                         HttpServletResponse response,
                         Object handler) throws Exception {
    System.out.println("preHandle");
    return true;
}
```

* 参数：
  * request：请求对象
  * response：响应对象
  * handler：被调用的处理器对象，本质上是一个方法对象，对反射中的Method对象进行了再包装
    * handler：public String controller.InterceptorController.handleRun
    * handler.getClass()：org.springframework.web.method.HandlerMethod
* 返回值：
  * 返回值为 false，被拦截的处理器将不执行  



***



#### 后置处理

原始方法运行后运行，如果原始方法被拦截，则不执行：

```java
public void postHandle(HttpServletRequest request,
                       HttpServletResponse response,
                       Object handler,
                       ModelAndView modelAndView) throws Exception {
    System.out.println("postHandle");
}
```

参数：

* modelAndView：如果处理器执行完成具有返回结果，可以读取到对应数据与页面信息，并进行调整  



***



#### 异常处理

拦截器最后执行的方法，无论原始方法是否执行：

```java
public void afterCompletion(HttpServletRequest request,
                            HttpServletResponse response,
                            Object handler,
                            Exception ex) throws Exception {
    System.out.println("afterCompletion");
}
```

参数：

* ex：如果处理器执行过程中出现异常对象，可以针对异常情况进行单独处理  



***



### 拦截配置

拦截路径：

* `/**`：表示拦截所有映射
* `/* `：表示拦截所有/开头的映射
* `/user/*`：表示拦截所有 /user/ 开头的映射
* `/user/add*`：表示拦截所有 /user/ 开头，且具体映射名称以 add 开头的映射
* `/user/*All`：表示拦截所有 /user/ 开头，且具体映射名称以 All 结尾的映射

```xml
<mvc:interceptors>
    <!--开启具体的拦截器的使用，可以配置多个-->
    <mvc:interceptor>
        <!--设置拦截器的拦截路径，支持*通配-->       
        <mvc:mapping path="/handleRun*"/>
        <!--设置拦截排除的路径，配置/**或/*，达到快速配置的目的-->
        <mvc:exclude-mapping path="/b*"/>
        <!--指定具体的拦截器类-->
        <bean class="MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```



***



### 拦截器链

**责任链模式**：责任链模式是一种行为模式

特点：沿着一条预先设定的任务链顺序执行，每个节点具有独立的工作任务
优势：

* 独立性：只关注当前节点的任务，对其他任务直接放行到下一节点
* 隔离性：具备链式传递特征，无需知晓整体链路结构，只需等待请求到达后进行处理即可
* 灵活性：可以任意修改链路结构动态新增或删减整体链路责任
* 解耦：将动态任务与原始任务解耦

缺点：

* 链路过长时，处理效率低下
* 可能存在节点上的循环引用现象，造成死循环，导致系统崩溃  

<img src="https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-多拦截器配置.png" style="zoom:67%;" />



***



### 源码解析

DispatcherServlet#doDispatch 方法中：

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
	try {
        // 获取映射器以及映射器的所有拦截器（运行原理部分详解了源码）
        mappedHandler = getHandler(processedRequest);
        // 前置处理，返回 false 代表条件成立
        if (!mappedHandler.applyPreHandle(processedRequest, response)) {
            //请求从这里直接结束
            return;
        }
        //所有拦截器都返回 true，执行目标方法
        mv = ha.handle(processedRequest, response, mappedHandler.getHandler())
        // 倒序执行所有拦截器的后置处理方法
        mappedHandler.applyPostHandle(processedRequest, response, mv);
    } catch (Exception ex) {
        //异常处理机制
        triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
    }
}
```

HandlerExecutionChain#applyPreHandle：前置处理

```java
boolean applyPreHandle(HttpServletRequest request, HttpServletResponse response) throws Exception {
    //遍历所有的拦截器
    for (int i = 0; i < this.interceptorList.size(); i++) {
        HandlerInterceptor interceptor = this.interceptorList.get(i);
        //执行前置处理，如果拦截器返回 false，则条件成立，不在执行其他的拦截器，直接返回 false，请求直接结束
        if (!interceptor.preHandle(request, response, this.handler)) {
            triggerAfterCompletion(request, response, null);
            return false;
        }
        this.interceptorIndex = i;
    }
    return true;
}
```

HandlerExecutionChain#applyPostHandle：后置处理

```java
void applyPostHandle(HttpServletRequest request, HttpServletResponse response, @Nullable ModelAndView mv)
    throws Exception {
	//倒序遍历
    for (int i = this.interceptorList.size() - 1; i >= 0; i--) {
        HandlerInterceptor interceptor = this.interceptorList.get(i);
        interceptor.postHandle(request, response, this.handler, mv);
    }
}
```

DispatcherServlet#triggerAfterCompletion 底层调用 HandlerExecutionChain#triggerAfterCompletion：

* 前面的步骤有任何异常都会直接倒序触发 afterCompletion

* 页面成功渲染有异常，也会倒序触发 afterCompletion

```java
void triggerAfterCompletion(HttpServletRequest request, HttpServletResponse response, @Nullable Exception ex) {
    //倒序遍历
    for (int i = this.interceptorIndex; i >= 0; i--) {
        HandlerInterceptor interceptor = this.interceptorList.get(i);
        try {
            //执行异常处理的方法
            interceptor.afterCompletion(request, response, this.handler, ex);
        }
        catch (Throwable ex2) {
            logger.error("HandlerInterceptor.afterCompletion threw exception", ex2);
        }
    }
}
```



拦截器的执行流程：

<img src="https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-拦截器工作流程.png" style="zoom: 50%;" />



参考文章：https://www.yuque.com/atguigu/springboot/vgzmgh#wtPLU



***



### 自定义

* Contoller层

  ```java
  @Controller
  public class InterceptorController {
      @RequestMapping("/handleRun")
      public String handleRun() {
          System.out.println("业务处理器运行------------main");
          return "page.jsp";
      }
  }
  ```

* 自定义拦截器需要实现 HandleInterceptor 接口

  ```java
  //自定义拦截器需要实现HandleInterceptor接口
  public class MyInterceptor implements HandlerInterceptor {
      //处理器运行之前执行
      @Override
      public boolean preHandle(HttpServletRequest request,
                               HttpServletResponse response,
                               Object handler) throws Exception {
          System.out.println("前置运行----a1");
          //返回值为false将拦截原始处理器的运行
          //如果配置多拦截器，返回值为false将终止当前拦截器后面配置的拦截器的运行
          return true;
      }
  
      //处理器运行之后执行
      @Override
      public void postHandle(HttpServletRequest request,
                             HttpServletResponse response,
                             Object handler,
                             ModelAndView modelAndView) throws Exception {
          System.out.println("后置运行----b1");
      }
  
      //所有拦截器的后置执行全部结束后，执行该操作
      @Override
      public void afterCompletion(HttpServletRequest request,
                                  HttpServletResponse response,
                                  Object handler,
                                  Exception ex) throws Exception {
          System.out.println("完成运行----c1");
      }
  }
  ```

  说明：三个方法的运行顺序为    preHandle → postHandle → afterCompletion，如果 preHandle 返回值为 false，三个方法仅运行preHandle

* web.xml：

  ```xml
  CharacterEncodingFilter + DispatcherServlet
  ```

* 配置拦截器：spring-mvc.xml

  ```xml
  <mvc:annotation-driven/>
  <context:component-scan base-package="interceptor,controller"/>
  <mvc:interceptors>
      <mvc:interceptor>
          <mvc:mapping path="/handleRun"/>
          <bean class="interceptor.MyInterceptor"/>
      </mvc:interceptor>
  </mvc:interceptors>
  ```

  注意：配置顺序为**先配置执行位置，后配置执行类**





***





## 异常处理

### 处理器

异常处理器： **HandlerExceptionResolver** 接口

类继承该接口的以后，当开发出现异常后会执行指定的功能

```java
@Component
public class ExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request,
                                         HttpServletResponse response,
                                         Object handler,
                                         Exception ex) {
        System.out.println("异常处理器正在执行中");
        ModelAndView modelAndView = new ModelAndView();
        //定义异常现象出现后，反馈给用户查看的信息
        modelAndView.addObject("msg","出错啦！ ");
        //定义异常现象出现后，反馈给用户查看的页面
        modelAndView.setViewName("error.jsp");
        return modelAndView;
    }
}
```

根据异常的种类不同，进行分门别类的管理，返回不同的信息：

```java
public class ExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request,
                                         HttpServletResponse response,
                                         Object handler,
                                         Exception ex) {
        System.out.println("my exception is running ...." + ex);
        ModelAndView modelAndView = new ModelAndView();
        if( ex instanceof NullPointerException){
            modelAndView.addObject("msg","空指针异常");
        }else if ( ex instanceof  ArithmeticException){
            modelAndView.addObject("msg","算数运算异常");
        }else{
            modelAndView.addObject("msg","未知的异常");
        }
        modelAndView.setViewName("error.jsp");
        return modelAndView;
    }
}
```

模拟错误：

```java
@Controller
public class UserController {
    @RequestMapping("/save")
    @ResponseBody
    public String save(@RequestBody String name) {
        //模拟业务层发起调用产生了异常
//        int i = 1/0;
//        String str = null;
//        str.length();

        return "error.jsp";
    }
```



***



### 注解开发

使用注解实现异常分类管理，开发异常处理器

ControllerAdvice 注解：

* 类型：类注解

* 位置：异常处理器类上方

* 作用：设置当前类为异常处理器类

* 格式：

  ```java
  @Component
  //声明该类是一个Controller的通知类，声明后该类就会被加载成异常处理器
  @ControllerAdvice
  public class ExceptionAdvice {
  }  
  ```

ExceptionHandler 注解：

* 类型：方法注解

* 位置：异常处理器类中针对指定异常进行处理的方法上方

* 作用：设置指定异常的处理方式

* 说明：处理器方法可以设定多个

* 格式：

  ```java
  @Component
  @ControllerAdvice
  public class ExceptionAdvice {
      //类中定义的方法携带@ExceptionHandler注解的会被作为异常处理器，后面添加实际处理的异常类型
      @ExceptionHandler(NullPointerException.class)
      @ResponseBody
      public String doNullException(Exception ex){
          return "空指针异常";
      }
  
      @ExceptionHandler(Exception.class)
      @ResponseBody
      public String doException(Exception ex){
          return "all Exception";
      }
  }
  ```


@ResponseStatus 注解：

* 类型：类注解、方法注解

* 位置：异常处理器类、方法上方

* 参数：

  value：出现错误指定返回状态码

  reason：出现错误返回的错误信息





***



### 解决方案

* web.xml

  ```java
  DispatcherServlet + CharacterEncodingFilter
  ```

* ajax.jsp

  ```jsp
  <%@page pageEncoding="UTF-8" language="java" contentType="text/html;UTF-8" %>
  
  <a href="javascript:void(0);" id="testException">点击</a><br/>
  
  <script type="text/javascript" src="${pageContext.request.contextPath}/js/jquery-3.3.1.min.js"></script>
  <script type="text/javascript">
      $(function () {
          $("#testException").click(function(){
              $.ajax({
                  contentType:"application/json",
                  type:"POST",
                  url:"save",
                  /*通过修改参数，激活自定义异常的出现*/
                  // name长度低于8位出现业务异常
                  // age小于0出现业务异常
                  // age大于100出现系统异常
                  // age类型如果无法匹配将转入其他类别异常
                  data:'{"name":"JockSuperMan","age":"-1"}',
                  dataType:"text",
                  //回调函数
                  success:function(data){
                      alert(data);
                  }
              });
          });
      });
  </script>
  ```

* spring-mvc.xml

  ```xml
  <mvc:annotation-driven/>
  <context:component-scan base-package="com.seazean"/>
  <mvc:resources mapping="/js/**" location="/js/"/>
  ```

* java / controller / UserController

  ```java
  @Controller
  public class UserController {
      @RequestMapping("/save")
      @ResponseBody
      public List<User> save(@RequestBody User user) {
          System.out.println("user controller save is running ...");
          //对用户的非法操作进行判定，并包装成异常对象进行处理，便于统一管理
          if(user.getName().trim().length() < 8){
              throw new BusinessException("对不起，用户名长度不满足要求，请重新输入！");
          }
          if(user.getAge() < 0){
              throw new BusinessException("对不起，年龄必须是0到100之间的数字！");
          }
          if(user.getAge() > 100){
              throw new SystemException("服务器连接失败，请尽快检查处理！");
          }
  
          User u1 = new User("Tom",3);
          User u2 = new User("Jerry",5);
          ArrayList<User> al = new ArrayList<User>();
          al.add(u1);al.add(u2);
          return al;
      }
  }
  ```

* 自定义异常

  ```java
  //自定义异常继承RuntimeException，覆盖父类所有的构造方法
  public class BusinessException extends RuntimeException {覆盖父类所有的构造方法}
  ```

  ```java
  public class SystemException extends RuntimeException {}
  ```

* 通过自定义异常将所有的异常现象进行分类管理，以统一的格式对外呈现异常消息

  ```java
  @Component
  @ControllerAdvice
  public class ProjectExceptionAdvice {
      @ExceptionHandler(BusinessException.class)
      public String doBusinessException(Exception ex, Model m){
          //使用参数Model将要保存的数据传递到页面上，功能等同于ModelAndView
          //业务异常出现的消息要发送给用户查看
          m.addAttribute("msg",ex.getMessage());
          return "error.jsp";
      }
  
      @ExceptionHandler(SystemException.class)
      public String doSystemException(Exception ex, Model m){
          //系统异常出现的消息不要发送给用户查看，发送统一的信息给用户看
          m.addAttribute("msg","服务器出现问题，请联系管理员！");
          return "error.jsp";
      }
  
      @ExceptionHandler(Exception.class)
      public String doException(Exception ex, Model m){
          m.addAttribute("msg",ex.getMessage());
          //将ex对象保存起来
          return "error.jsp";
      }
  
  }
  ```

  



***





## 实用技术

### 文件传输

#### 上传下载

上传文件过程：

![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-上传文件过程分析.png)



MultipartResolver接口：

*  MultipartResolver 接口定义了文件上传过程中的相关操作，并对通用性操作进行了封装
*  MultipartResolver 接口底层实现类 CommonsMultipartResovler
*  CommonsMultipartResovler 并未自主实现文件上传下载对应的功能，而是调用了 apache 文件上传下载组件  

文件上传下载实现：

* 导入坐标

  ```xml
  <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
  </dependency>
  ```

* 页面表单 fileupload.jsp

  ```html
  <form method="post" action="/upload" enctype="multipart/form-data">
      <input type="file" name="file"><br>
      <input type="submit" value="提交">
  </form>
  ```
  
* web.xml

  ```xml
  DispatcherServlet + CharacterEncodingFilter
  ```

* 控制器

  ```java
  @PostMapping("/upload")
  public String upload(@RequestParam("email") String email,
                       @RequestParam("username") String username,
                       @RequestPart("headerImg") MultipartFile headerImg) throws IOException {
  
      if(!headerImg.isEmpty()){
          //保存到文件服务器，OSS服务器
          String originalFilename = headerImg.getOriginalFilename();
          headerImg.transferTo(new File("H:\\cache\\" + originalFilename));
      }
      return "main";
  }
  ```





***



#### 名称问题

MultipartFile 参数中封装了上传的文件的相关信息。

1. 文件命名问题， 获取上传文件名，并解析文件名与扩展名

   ```java
   file.getOriginalFilename();
   ```

2. 文件名过长问题

3. 文件保存路径

   ```java
   ServletContext context = request.getServletContext();
   String realPath = context.getRealPath("/uploads");
   File file = new File(realPath + "/");
   if(!file.exists()) file.mkdirs();
   ```

4. 重名问题

   ```java
   String uuid = UUID.randomUUID.toString().replace("-", "").toUpperCase();
   ```

```java
@Controller
public class FileUploadController {
    @RequestMapping(value = "/fileupload")
	//参数中定义MultipartFile参数，用于接收页面提交的type=file类型的表单，表单名称与参数名相同
    public String fileupload(MultipartFile file,MultipartFile file1,MultipartFile file2, HttpServletRequest request) throws IOException {
        System.out.println("file upload is running ..."+file);
//        MultipartFile参数中封装了上传的文件的相关信息
//        System.out.println(file.getSize());
//        System.out.println(file.getBytes().length);
//        System.out.println(file.getContentType());
//        System.out.println(file.getName());
//        System.out.println(file.getOriginalFilename());
//        System.out.println(file.isEmpty());
        //首先判断是否是空文件，也就是存储空间占用为0的文件
        if(!file.isEmpty()){
            //如果大小在范围要求内正常处理，否则抛出自定义异常告知用户（未实现）
            //获取原始上传的文件名，可以作为当前文件的真实名称保存到数据库中备用
            String fileName = file.getOriginalFilename();
            //设置保存的路径
            String realPath = request.getServletContext().getRealPath("/images");
            //保存文件的方法，通常文件名使用随机生成策略产生，避免文件名冲突问题
            file.transferTo(new File(realPath,file.getOriginalFilename()));
        }
        //测试一次性上传多个文件
        if(!file1.isEmpty()){
            String fileName = file1.getOriginalFilename();
            //可以根据需要，对不同种类的文件做不同的存储路径的区分，修改对应的保存位置即可
            String realPath = request.getServletContext().getRealPath("/images");
            file1.transferTo(new File(realPath,file1.getOriginalFilename()));
        }
        if(!file2.isEmpty()){
            String fileName = file2.getOriginalFilename();
            String realPath = request.getServletContext().getRealPath("/images");
            file2.transferTo(new File(realPath,file2.getOriginalFilename()));
        }
        return "page.jsp";
    }
}
```



****



#### 源码解析

**StandardServletMultipartResolver** 文件上传解析器

DispatcherServlet#doDispatch：

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    // 判断当前请求是不是文件上传请求
    processedRequest = checkMultipart(request);
    // 文件上传请求会对 request 进行包装，导致两者不相等，此处赋值为 true，代表已经被解析
    multipartRequestParsed = (processedRequest != request);
}
```

DispatcherServlet#checkMultipart：

* `if (this.multipartResolver != null && this.multipartResolver.isMultipart(request))`：判断是否是文件请求
  * `StandardServletMultipartResolver#isMultipart`：根据开头是否符合 multipart/form-data 或者 multipart/
* `return this.multipartResolver.resolveMultipart(request)`：把请求封装成 StandardMultipartHttpServletRequest 对象

开始执行 ha.handle() 目标方法进行数据的解析

* RequestPartMethodArgumentResolver#supportsParameter：支持解析文件上传数据

  ```java
  public boolean supportsParameter(MethodParameter parameter) {
      // 参数上有 @RequestPart 注解
      if (parameter.hasParameterAnnotation(RequestPart.class)) {
          return true;
      }
  }
  ```

* RequestPartMethodArgumentResolver#resolveArgument：解析参数数据，封装成 MultipartFile 对象

  * `RequestPart requestPart = parameter.getParameterAnnotation(RequestPart.class)`：获取注解的相关信息
  * `String name = getPartName(parameter, requestPart)`：获取上传文件的名字
  * `Object mpArg = MultipartResolutionDelegate.resolveMultipartArgument()`：解析参数
    * `List<MultipartFile> files = multipartRequest.getFiles(name)`：获取文件的所有数据

* `return doInvoke(args)`：解析完成执行自定义的方法，完成上传功能





***



### 校验框架

#### 校验概述

表单校验保障了数据有效性、安全性  

校验分类：客户端校验和服务端校验

* 格式校验
  * 客户端：使用Js技术，利用正则表达式校验
  * 服务端：使用校验框架 
* 逻辑校验
  * 客户端：使用ajax发送要校验的数据，在服务端完成逻辑校验，返回校验结果
  * 服务端：接收到完整的请求后，在执行业务操作前，完成逻辑校验

表单校验框架：

* JSR（Java Specification Requests）：Java 规范提案 

* 303：提供bean属性相关校验规则

* JCP（Java Community Process）：Java社区

* Hibernate框架中包含一套独立的校验框架hibernate-validator 

* 导入坐标：

  ```xml
  <!--导入校验的jsr303规范-->
  <dependency>
      <groupId>javax.validation</groupId>
      <artifactId>validation-api</artifactId>
      <version>2.0.1.Final</version>
  </dependency>
  <!--导入校验框架实现技术-->
  <dependency>
      <groupId>org.hibernate</groupId>
      <artifactId>hibernate-validator</artifactId>
      <version>6.1.0.Final</version>
  </dependency>
  ```

**注意：**
tomcat7：搭配hibernate-validator版本5.*.*.Final
tomcat8.5↑：搭配hibernate-validator版本6.*.*.Final  

 

***



#### 基本使用

##### 开启校验

名称：@Valid、@Validated
类型：形参注解
位置：处理器类中的实体类类型的方法形参前方
作用：设定对当前实体类类型参数进行校验
范例：  

```java
@RequestMapping(value = "/addemployee")
public String addEmployee(@Valid Employee employee) {
    System.out.println(employee);
}
```



##### 设置校验规则

名称：@NotNull
类型：属性注解等
位置：实体类属性上方
作用：设定当前属性校验规则
范例：每个校验规则所携带的参数不同，根据校验规则进行相应的调整，具体的校验规则查看对应的校验框架进行获取

```java
public class Employee{
    @NotNull(message = "姓名不能为空")
    private String name;//员工姓名
}  
```



##### 获取错误信息

```java
@RequestMapping(value = "/addemployee")
//Errors对象用于封装校验结果，如果不满足校验规则，对应的校验结果封装到该对象中，包含校验的属性名和校验不通过返回的消息
public String addEmployee(@Valid Employee employee, Errors errors, Model model){
    System.out.println(employee);
    //判定Errors对象中是否存在未通过校验的字段
    if(errors.hasErrors()){
        for(FieldError error : errors.getFieldErrors()){
        	//将校验结果添加到Model对象中，用于页面显示，返回json数据即可
            model.addAttribute(error.getField(),error.getDefaultMessage());
        }
        //当出现未通过校验的字段时，跳转页面到原始页面，进行数据回显
        return "addemployee.jsp";
    }
    return "success.jsp";
}  
```

通过形参Errors获取校验结果数据，通过Model接口将数据封装后传递到页面显示，页面获取后台封装的校验结果信息  

```html
<form action="/addemployee" method="post">
    员工姓名：<input type="text" name="name"><span style="color:red">${name}</span><br/>
    员工年龄：<input type="text" name="age"><span style="color:red">${age}</span><br/>
    <input type="submit" value="提交">
</form>
```



****



#### 多规则校验

* 同一个属性可以添加多个校验器  

  ```java
  public class Employee{
      @NotBlank(message = "姓名不能为空")
      private String name;//员工姓名
  
      @NotNull(message = "请输入年龄")
      @Max(value = 60,message = "年龄最大值60")
      @Min(value = 18,message = "年龄最小值18")
      private Integer age;//员工年龄
  }
  ```

* 三种判定空校验器的区别 
  ![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-三种判定空检验器的区别.png)



***



#### 嵌套校验

名称：@Valid
类型：属性注解
位置：实体类中的引用类型属性上方
作用：设定当前应用类型属性中的属性开启校验
范例：

```java
public class Employee {
    //实体类中的引用类型通过标注@Valid注解，设定开启当前引用类型字段中的属性参与校验
    @Valid
    private Address address;
}
```

注意：开启嵌套校验后，被校验对象内部需要添加对应的校验规则  

```java
//嵌套校验的实体中，对每个属性正常添加校验规则即可
public class Address implements Serializable {
    @NotBlank(message = "请输入省份名称")
    private String provinceName;//省份名称

    @NotBlank(message = "请输入邮政编码")
    @Size(max = 6,min = 6,message = "邮政编码由6位组成")
    private String zipCode;//邮政编码
}
```



***



#### 分组校验

分组校验的介绍

* 同一个模块，根据执行的业务不同，需要校验的属性会有不同
  * 新增用户
  * 修改用户
* 对不同种类的属性进行分组，在校验时可以指定参与校验的字段所属的组类别
  * 定义组（通用）
  * 为属性设置所属组，可以设置多个
  * 开启组校验

domain：

```java
//用于设定分组校验中的组名，当前接口仅提供字节码，用于识别
public interface GroupOne {
}
```

```java
public class Employee{
    @NotBlank(message = "姓名不能为空",groups = {GroupA.class})
    private String name;//员工姓名

    @NotNull(message = "请输入年龄",groups = {GroupA.class})
    @Max(value = 60,message = "年龄最大值60")//不加Group的校验不生效
    @Min(value = 18,message = "年龄最小值18")
    private Integer age;//员工年龄

    @Valid
    private Address address;
    //......
}
```

controller：

```java
@Controller
public class EmployeeController {
    @RequestMapping(value = "/addemployee")
    public String addEmployee(@Validated({GroupA.class}) Employee employee, Errors errors, Model m){
        if(errors.hasErrors()){
            List<FieldError> fieldErrors = errors.getFieldErrors();
            System.out.println(fieldErrors.size());
            for(FieldError error : fieldErrors){
                m.addAttribute(error.getField(),error.getDefaultMessage());
            }
            return "addemployee.jsp";
        }
        return "success.jsp";
    }
}
```

jsp：

```html
<form action="/addemployee" method="post"><%--页面使用${}获取后台传递的校验信息--%>
    员工姓名：<input type="text" name="name"><span style="color:red">${name}</span><br/>
    员工年龄：<input type="text" name="age"><span style="color:red">${age}</span><br/>
    <%--注意，引用类型的校验未通过信息不是通过对象进行封装的，直接使用对象名.属性名的格式作为整体属性字符串进行保存的，和使用者的属性传递方式有关，不具有通用性，仅适用于本案例--%>
    省：<input type="text" name="address.provinceName"><span style="color:red">${requestScope['address.provinceName']}</span><br/>
        <input type="submit" value="提交">
/form>
```







***





# SSM

## XML

### 结构搭建

* 创建项目，组织项目结构，创建包

* 创建表与实体类

* 创建三层架构对应的模块、接口与实体类，建立关联关系

* 数据层接口（代理自动创建实现类）

  * 业务层接口 + 业务层实现类
  * 表现层类

  ![](https://gitee.com/seazean/images/raw/master/Frame/SSM目录结构.png)





***



### 数据准备

* 导入坐标 pom.xml

  ```xml
  <dependencies>
      <!--spring环境-->
      <<dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-context</artifactId>
          <version>5.1.9.RELEASE</version>
      </dependency>
  
      <!--mybatis环境-->
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis</artifactId>
          <version>3.5.3</version>
      </dependency>
  
      <!--mysql环境-->
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>5.1.47</version>
      </dependency>
  
      <!--spring整合jdbc-->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-jdbc</artifactId>
          <version>5.1.9.RELEASE</version>
      </dependency>
  
      <!--spring整合mybatis-->
      <dependency>
          <groupId>org.mybatis</groupId>
          <artifactId>mybatis-spring</artifactId>
          <version>2.0.3</version>
      </dependency>
  
      <!--druid连接池-->
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid</artifactId>
          <version>1.1.16</version>
      </dependency>
  
      <!--分页插件坐标-->
      <dependency>
          <groupId>com.github.pagehelper</groupId>
          <artifactId>pagehelper</artifactId>
          <version>5.1.2</version>
      </dependency>
  
  
      <!--springmvc环境-->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-webmvc</artifactId>
          <version>5.1.9.RELEASE</version>
      </dependency>
  
      <!--jackson相关坐标3个-->
      <dependency>
          <groupId>com.fasterxml.jackson.core</groupId>
          <artifactId>jackson-databind</artifactId>
          <version>2.9.0</version>
      </dependency>
      <dependency>
          <groupId>com.fasterxml.jackson.core</groupId>
          <artifactId>jackson-core</artifactId>
          <version>2.9.0</version>
      </dependency>
      <dependency>
          <groupId>com.fasterxml.jackson.core</groupId>
          <artifactId>jackson-annotations</artifactId>
          <version>2.9.0</version>
      </dependency>
  
      <!--servlet环境-->
      <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>3.1.0</version>
          <scope>provided</scope>
      </dependency>
  
  
      <!--其他组件-->
      <!--junit单元测试-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.12</version>
      </dependency>
      <!--spring整合junit-->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-test</artifactId>
          <version>5.1.9.RELEASE</version>
      </dependency>
  </dependencies>
  
  <build>
      <!--设置插件-->
      <plugins>
          <!--具体的插件配置-->
          <plugin>
              <groupId>org.apache.tomcat.maven</groupId>
              <artifactId>tomcat7-maven-plugin</artifactId>
              <version>2.1</version>
              <configuration>
                  <port>80</port>
                  <path>/</path>
              </configuration>
          </plugin>
      </plugins>
  </build>
  ```

* resources.jdbc.properties

  ```properties
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://192.168.0.137:3306/ssm_db?useSSL=false
  jdbc.username=root
  jdbc.password=123456
  ```

* domain

  ```java
  public class User implements Serializable {
      private Integer uuid;
      private String userName;
      private String password;
      private String realName;
      private Integer gender;
      private Date birthday;
  }
  ```

* Dao层

  ```java
  public interface UserDao {
      //添加用户
      public boolean save(User user);
  
      //修改用户
      public boolean update(User user);
  
      //删除用户
      public boolean delete(Integer uuid);
  
      //查询单个用户信息
      public User get(Integer uuid);
  
      //查询全部用户信息
      public List<User> getAll();
  
      /**
       * 根据用户名密码查询个人信息
       * @param userName 用户名
       * @param password 密码信息
       * @return
       */
  	//数据层操作不要和业务层操作的名称混淆，通常数据层仅反映与数据库间的信息交换，不体现业务逻辑
      public User getByUserNameAndPassword(@Param("userName") String userName,
                                           @Param("password") String password);
  }
  ```

* service.UserService

  ```java
  public interface UserService {
      //添加用户
      public boolean save(User user);
  
      //修改用户
      public boolean update(User user);
  
      //删除用户
      public boolean delete(Integer uuid);
  
      //查询单个用户信息
      public User get(Integer uuid);
  
      //查询全部用户信息
      public List<User> getAll();
  
      //根据用户名密码进行登录
      public User login(String userName,String password);
  }
  ```

  service.impl.UserServiceImpl

  ```java
  @Service  //设置为bean
  public class UserServiceImpl implements UserService {
      @Autowired
      private UserDao userDao;
  
      @Override
      public boolean save(User user) {
          return userDao.save(user);
      }
  
      @Override
      public boolean update(User user) {
          return userDao.update(user);
      }
  
      @Override
      public boolean delete(Integer uuid) {
          return userDao.delete(uuid);
      }
  
      @Override
      public User get(Integer uuid) {
          return userDao.get(uuid);
      }
  
      @Override
      public PageInfo<User> getAll(int page, int size) {//用分页插件
          PageHelper.startPage(page, size);
          List<User> all = userDao.getAll();
          return new PageInfo<User>(all);
      }
  
      @Override
      public User login(String userName, String password) {
          return userDao.getByUserNameAndPassword(userName, password);
      }
  }
  ```

* controller

  ```java
  public class UserController {
      
  }
  ```
  
  

***



### Mybatis

* Spring环境配置：spring-mvc.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="
          http://www.springframework.org/schema/context 
          http://www.springframework.org/schema/context/spring-context.xsd">
  	<!--开启注解驱动，提供Controller请求转发，json自动转换等功能-->
      <mvc:annotation-driven/>
      <!--开启bean注解扫描-->
      <context:component-scan base-package="controller"/>
  </beans>
  ```
  
* MyBatis映射：resources.dao.UserDao.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <mapper namespace="dao.UserDao">
      <!--添加-->
      <insert id="save" parameterType="user">
          INSERT INTO user (userName,password,realName,gender,birthday) VALUES (#{userName},#{password},#{realName},#{gender},#{birthday})
      </insert>
  
      <!--删除-->
      <delete id="delete" parameterType="int">
          DELETE FROM user WHERE uuid = #{uuid}
      </delete>
  
      <!--修改-->
      <update id="update" parameterType="user">
          UPDATE user SET userName=#{userName},password=#{password},realName=#{realName},gender=#{gender},birthday=#{birthday} WHERE uuid=#{uuid}
      </update>
  
      <!--查询单个-->
      <select id="get" resultType="user" parameterType="int">
          select * from user where uuid= #{uuid}
      </select>
  
      <!--分页查询-->
      <select id="getAll" resultType="user">
          select * from user
      </select>
  
      <!--登录-->
      <select id="getByUserNameAndPassword" resultType="user" >
          SELECT * FROM user WHERE userName=#{userName} AND password=#{password}
      </select>
  </mapper>
  ```

* Mybatis 核心配置：resouces.applicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="
          http://www.springframework.org/schema/context 	
          http://www.springframework.org/schema/context/spring-context.xsdd">
  	<!--开启bean-->
      <context:component-scan base-package="dao,domain,service">
          <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
      </context:component-scan>
  
      <!--事务管理器,开启注解式事务-->
      <tx:annotation-driven transaction-manager="txManager"/>
  
      <!--加载properties文件-->
      <context:property-placeholder location="classpath*:jdbc.properties"/>
  
      <!--加载数据源-->
      <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
          <property name="driverClassName" value="${jdbc.driver}"/>
          <property name="url" value="${jdbc.url}"/>
          <property name="username" value="${jdbc.username}"/>
          <property name="password" value="${jdbc.password}"/>
      </bean>
  
      <!--整合mybatis到Spring-->
      <bean class="org.mybatis.spring.SqlSessionFactoryBean">
          <property name="dataSource" ref="dataSource"/>
          <property name="typeAliasesPackage" value="domain"/>
          <!--分页插件-->
          <property name="plugins">
              <array>
                  <bean class="com.github.pagehelper.PageInterceptor">
                      <property name="properties">
                          <props>
                              <prop key="helperDialect">mysql</prop>
                              <prop key="reasonable">true</prop>
                          </props>
                      </property>
                  </bean>
              </array>
          </property>
      </bean>
  
      <!--映射扫描-->
      <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
          <property name="basePackage" value="dao"/>
      </bean>
  
  	<!--事务管理器-->
      <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource"/>
      </bean>
  </beans>
  ```
  
* 业务层接口开启事务

  ```java
  @Transactional(readOnly = true)
  public interface UserService {
      //添加用户
      @Transactional(readOnly = false)
      public boolean save(User user);
      @Transactional(readOnly = false)
      public boolean update(User user);
      @Transactional(readOnly = false)
      public boolean delete(Integer uuid);
  
      //查询单个用户
      public User get(Integer uuid);
  
      //查询全部用户
      public PageInfo<User> getAll(int page, int size);
  
      //根据用户名密码进行登录
      public User login(String userName, String password);
  }
  ```

  

***



### Junit

* 单元测试整合 junit

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)  
  @ContextConfiguration(locations = "classpath:applicationContext.xml") 
  public class UserServiceTest {
      @Autowired
      private UserService userService;
  
      @Test
      public void testDelete(){  
          User user = new User();  userService.delete(3);
      }
  }
  ```

* test.resouces

  ```java
  applicationContext.xml + jdbc.properties
  ```



***



### MVC

* web.xml配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
           version="3.1">
  
    <!--加载配置文件-->
    <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath*:applicationContext.xml</param-value>
    </context-param>
  
    <!--启动服务器时,通过监听器加载spring运行环境-->
    <listener>
      <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
  
    <filter>
      <filter-name>CharacterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
      </init-param>
    </filter>
    <filter-mapping>
      <filter-name>CharacterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
    </filter-mapping>
  
    <servlet>
      <servlet-name>DispatcherServlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <!--加载Spring控制文件-->
      <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:spring-mvc.xml</param-value>
      </init-param>
    </servlet>
    <servlet-mapping>
      <servlet-name>DispatcherServlet</servlet-name>
      <url-pattern>/</url-pattern>
    </servlet-mapping>
  </web-app>
  ```

* spring-mvc.xml

  ```xml
  <mvc:annotation-driven/>
  <context:component-scan base-package="controller"/>
  ```

* Controller层

  ```java
  @RestController   //@RestController = @Controller + @ResponseBody
  @RequestMapping("/user")
  public class UserController {
  
      @PostMapping
      public boolean save(User user){
          System.out.println("save ..." + user);
          return true;
      }
  
      @PutMapping
      public boolean update(User user){
          System.out.println("update ..." + user);
          return true;
      }
  
      @DeleteMapping("/{uuid}")
      public boolean delete(@PathVariable Integer uuid){
          System.out.println("delete ..." + uuid);
          return true;
      }
  
      @GetMapping("/{uuid}")
      public User get(@PathVariable Integer uuid){
          System.out.println("get ..." + uuid);
          return null;
      }
  
      @GetMapping("/{page}/{size}")
      public List getAll(@PathVariable Integer page,@PathVariable Integer size){
          System.out.println("getAll ..." + page+","+size);
          return null;
      }
  
      @PostMapping("/login")
      public User login(String userName,String password){
          System.out.println("login ..." + userName + " ," +password);
          return null;
      }
  }
  ```

  



****



### 表现层

#### 数据封装

* 前端接收表现层返回的数据种类
  * 返回数据格式设计：状态、数据、消息
  * 返回数据状态设计，根据业务不同设计：404、500、200

* 数据格式代码：

  ```java
  public class Result {
      // 操作结果编码
      private Integer code;
      // 操作数据结果
      private Object data;
      // 消息
      private String message;
      public Result(Integer code) {
          this.code = code;
      }
      public Result(Integer code, Object data) {
          this.code = code;
          this.data = data;
      }
  }
  ```

* 状态代码格式：状态码常量可以根据自己的业务需求设定

  ```java
  public class Code {
      //操作结果编码
      public static final Integer SAVE_OK = 20011;
      public static final Integer UPDATE_OK = 20021;
      public static final Integer SAVE_ERR = 20010;
      public static final Integer UPDATE_ERR = 20020;
  
      //系统错误编码
      //操作权限编码
      //校验结果编码
  }
  
  ```

* Controller调用

  ```java
  @RestController
  public class UserController {
      @Autowired
      private UserService userService;
      @PostMapping
      public Result save(User user){
          boolean flag = userService.save(user);
          return new Result(flag ? Code.SAVE_OK:Code.SAVE_ERROR);
      }
      @GetMapping("/{uuid}")
      public Result get(@PathVariable Integer uuid){
          User user = userService.get(uuid);
          //三目运算符
          return new Result(null != user ?Code.GET_OK: Code.GET_ERROR,user);
      }
  }
  ```



***



#### 自定义异常

设定自定义异常，封装程序执行过程中出现的问题，便于表现层进行统一的异常拦截并进行处理

* BusinessException
* SystemException

自定义异常消息返回时需要与业务正常执行的消息按照统一的格式进行处理

* 定义 BusinessException

  ```java
  public class BusinessException extends RuntimeException {
      //自定义异常中封装对应的错误编码，用于异常处理时获取对应的操作编码
      private Integer code;
  
      public Integer getCode() {
          return code;
      }
  
      public void setCode(Integer code) {
          this.code = code;
      }
  
      public BusinessException(Integer code) {
          this.code = code;
      }
  
      public BusinessException(String message, Integer code) {
          super(message);
          this.code = code;
      }
  
      public BusinessException(String message, Throwable cause,Integer code) {
          super(message, cause);
          this.code = code;
      }
  
      public BusinessException(Throwable cause,Integer code) {
          super(cause);
          this.code = code;
      }
  
      public BusinessException(String message, Throwable cause, boolean enableSuppression, boolean writableStackTrace,Integer code) {
          super(message, cause, enableSuppression, writableStackTrace);
          this.code = code;
      }
  }
  ```

* Controller调用

  ```java
  @RestController
  public class UserController {
      @Autowired
      private UserService userService;
      @GetMapping("/{uuid}")
      public Result get(@PathVariable Integer uuid){
          User user = userService.get(uuid);
          //模拟出现异常，使用条件控制，便于测试结果
          if (uuid == 10 ) throw new BusinessException("查询出错啦，请重试！",Code.GET_ERROR);
          return new Result(null != user ?Code.GET_OK: Code.GET_ERROR,user);
      }
  }
  ```



***



#### 兼容异常

java.controller.interceptor

```java
@Component
@ControllerAdvice
public class ProjectExceptionAdivce {
    @ExceptionHandler(BusinessException.class)
    @ResponseBody
    //对出现异常的情况进行拦截，并将其处理成统一的页面数据结果格式
    public Result doBusinessException(BusinessException e){
        return new Result(e.getCode(),e.getMessage());
    }
}
```



****



## 注解

### 结构搭建

项目整体目录结构

![](https://gitee.com/seazean/images/raw/master/Frame/SSM-annotation.png)





***



### UserDao.xml

注解：@Param

作用：当 SQL 语句需要多个（大于1）参数时，用来指定参数的对应规则

* 注解替代 UserDao 映射配置文件：dao.UserDao

  ```java
  public interface UserDao {
      //添加用户
      @Insert("insert into user(userName,password,realName,gender,birthday)values(#{userName},#{password},#{realName},#{gender},#{birthday})")
      public boolean save(User user);
  
      //修改用户
      @Update("update user set userName=#{userName},password=#{password},realName=#{realName},gender=#{gender},birthday=#{birthday} where uuid=#{uuid}")
      public boolean update(User user);
  
      // 删除用户
      @Delete("delete from user where uuid = #{uuid}")
      public boolean delete(Integer uuid);
  
      //查询单个用户信息
      @Select("select * from user where uuid = #{uuid}")
      public User get(Integer uuid);
  
      //查询全部用户信息
      @Select("select * from user")
      public List<User> getAll();
  
  
      //根据用户名密码查询个人信息
      @Select("select * from user where userName=#{userName} and password=#{password}")
      //注意：数据层操作不要和业务层操作的名称混淆，通常数据层仅反映与数据库间的信息交换，不体现业务逻辑
      public User getByUserNameAndPassword(@Param("userName") String userName, @Param("password") String password);
  }
  ```

  



***



### applicationContext.xml

![](https://gitee.com/seazean/images/raw/master/Frame/IoC注解整合MyBatis图解.png)

* JdbcConfig

  ```java
  public class JdbcConfig {
      //使用注入的形式，读取properties文件中的属性值，等同于<property name="*******" value="${jdbc.driver}"/>
      @Value("${jdbc.driver}")
      private String driver;
      @Value("${jdbc.url}")
      private String url;
      @Value("${jdbc.username}")
      private String userName;
      @Value("${jdbc.password}")
      private String password;
  
      //定义dataSource的bean，等同于<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
      @Bean("dataSource")
      public DataSource getDataSource(){
          //创建对象
          DruidDataSource ds = new DruidDataSource();
          //，等同于set属性注入<property name="driverClassName" value="***"/>
          ds.setDriverClassName(driver);
          ds.setUrl(url);
          ds.setUsername(userName);
          ds.setPassword(password);
          return ds;
      }
  }
  ```

* MybatisConfig

  ```java
  public class MyBatisConfig {
  //定义MyBatis的核心连接工厂bean，等同于<bean class="org.mybatis.spring.SqlSessionFactoryBean">
      @Bean
      //自动装配的形式加载dataSource，为set注入提供数据，dataSource来源于JdbcConfig中
      public SqlSessionFactoryBean getSqlSessionFactoryBean(@Autowired DataSource dataSource,@Autowired Interceptor interceptor){
          SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
          //等同于<property name="typeAliasesPackage" value="domain"/>
          ssfb.setTypeAliasesPackage("domain");
          //等同于<property name="dataSource" ref="dataSource"/>
          ssfb.setDataSource(dataSource);
  		//可以把Interceptor写在这里
          ssfb.setPlugins(interceptor);
          return ssfb;
      }
  
      //定义MyBatis的映射扫描，等同于<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      @Bean
      public MapperScannerConfigurer getMapperScannerConfigurer(){
          MapperScannerConfigurer msc = new MapperScannerConfigurer();
          //等同于<property name="basePackage" value="dao"/>
          msc.setBasePackage("dao");
          return msc;
      }
  
      @Bean("pageInterceptor")
      public Interceptor getPageInterceptor(){
          Interceptor interceptor = new PageInterceptor();
          Properties properties = new Properties();
          properties.setProperty("helperDialect","mysql");
          properties.setProperty("reasonable","true");
          //等同于<property name="properties">
          interceptor.setProperties(properties);
          return interceptor;
      }
  }
  ```

* SpringConfig.xml

  ```java
  @Configuration
  @ComponentScan(value = {"config","dao","service","system"},excludeFilters =
      @ComponentScan.Filter(type= FilterType.ANNOTATION,classes = {Controller.class}))
  //等同于<context:property-placeholder location="classpath*:jdbc.properties"/>
  @PropertySource("classpath:jdbc.properties")
  //等同于<tx:annotation-driven />，bean的名称默认取transactionManager
  @EnableTransactionManagement
  @Import({MyBatisConfig.class,JdbcConfig.class})
  public class SpringConfig {
      //等同于<bean id="txManager"/>
      @Bean("transactionManager")
      //等同于<bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
      public DataSourceTransactionManager getTxManager(@Autowired DataSource dataSource){
          DataSourceTransactionManager tm = new DataSourceTransactionManager();
          //等同于<property name="dataSource" ref="dataSource"/>
          tm.setDataSource(dataSource);
          return tm;
      }
  }
  ```
  
  


***



### spring-mvc.xml 

* 注解替代 spring-mvc.xml：SpringMvcConfig

  ```java
  @Configuration
  //等同于<context:component-scan base-package="com.seazean.controller"/>
  @ComponentScan("controller")
  //等同于<mvc:annotation-driven/>，还不完全相同
  @EnableWebMvc
  public class SpringMvcConfig {
      //注解配置通用放行静态资源的格式
      @Override
      public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
          configurer.enable();
      }
  }
  ```

* EnableWebMvc  

   1. 支持 ConversionService 的配置，可以方便配置自定义类型转换器
   2. 支持 @NumberFormat 注解格式化数字类型
   3. 支持 @DateTimeFormat 注解格式化日期数据，日期包括 Date、Calendar
   4. 支持 @Valid 的参数校验（需要导入 JSR-303 规范）
   5. 配合第三方 jar 包和 SpringMVC 提供的注解读写 XML 和 JSON 格式数据



***



### web.xml

* 注解替代 web.xml：ServletContainersInitConfig

  ```java
  public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
  
      //创建Servlet容器，加载SpringMVC配置类中的信息，并加载成WEB专用的ApplicationContext对象，该对象放入了ServletContext范围，后期在整个WEB容器中可以随时获取调用
      @Override
      protected WebApplicationContext createServletApplicationContext() {
          AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
          ctx.register(SpringMvcConfig.class);
          return ctx;
      }
  
      //注解配置映射地址方式，服务于SpringMVC的核心控制器DispatcherServlet
      @Override
      protected String[] getServletMappings() {
          return new String[]{"/"};
      }
  	//启动服务器时,通过监听器加载spring运行环境
      @Override
      protected WebApplicationContext createRootApplicationContext() {
          AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
          ctx.register(SpringConfig.class);
          return ctx;
      }
  
      //乱码处理作为过滤器，在servlet容器启动时进行配置，相关内容参看Servlet零配置相关课程
      @Override
      public void onStartup(ServletContext servletContext) throws ServletException {
          //触发父类的onStartup
          super.onStartup(servletContext);
          //1.创建字符集过滤器对象
          CharacterEncodingFilter cef = new CharacterEncodingFilter();
          //2.设置使用的字符集
          cef.setEncoding("UTF-8");
          //3.添加到容器（它不是ioc容器，而是ServletContainer）
          FilterRegistration.Dynamic registration = servletContext.addFilter(
              							"characterEncodingFilter", cef);
          //4.添加映射
          registration.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST, DispatcherType.FORWARD, DispatcherType.INCLUDE), false, "/*");
      }
  }
  ```

* WebApplicationContext，生成 Spring 核心容器（主容器/父容器/根容器）

  * 父容器：Spring 环境加载后形成的容器，包含 Spring 环境下的所有的 bean
  * 子容器：当前 mvc 环境加载后形成的容器，不包含 Spring 环境下的 bean
  * 子容器可以访问父容器中的资源，父容器不可以访问子容器的资源







****





# Boot

（这部分笔记做的非常一般，一周内完善）

