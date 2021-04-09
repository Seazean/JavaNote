# Maven

## 基本介绍

### Mvn概述

Maven：本质是一个项目管理工具，将项目开发和管理过程抽象成一个项目对象模型（POM）

POM：Project Object Model，项目对象模型。Maven是用Java语言编写的，它管理的东西以面向对象的形式进行设计，最终把一个项目看成一个对象，而这个对象叫做POM

pom.xml：Maven需要一个pom.xml文件，Maven通过加载这个配置文件可以知道项目的相关信息，这个文件代表就一个项目。如果我们做8个项目，对应的是8个pom.xml文件

依赖管理：Maven对项目所有依赖资源的一种管理，它和项目之间是一种双向关系，即做项目时可以管理所需要的其他资源，当其他项目需要依赖我们项目时，Maven也会把我们的项目当作一种资源去进行管理。

管理资源的存储位置：本地仓库，私服，中央仓库

![](https://gitee.com/seazean/images/raw/master/Frame/Maven介绍.png)





### Mvn作用

* 项目构建：提供标准的，跨平台的自动化构建项目的方式

* 依赖管理：方便快捷的管理项目依赖的资源（jar包），避免资源间的版本冲突等问题

* 统一开发结构：提供标准的，统一的项目开发结构

  ![](https://gitee.com/seazean/images/raw/master/Frame/Maven标准结构.png)

各目录存放资源类型说明：

* src/main/java：项目java源码

* src/main/resources：项目的相关配置文件（比如mybatis配置，xml映射配置，自定义配置文件等）

* src/main/webapp：web资源（比如html，css，js等）

* src/test/java：测试代码

* src/test/resources：测试相关配置文件

* src/pom.xml：项目pom文件





### 基础概念

* **仓库**：用于存储资源，主要是各种jar包。有本地仓库，私服，中央仓库，私服和中央仓库都是远程仓库

  * 中央仓库：maven团队自身维护的仓库，属于开源的

  * 私服：各公司/部门等小范围内存储资源的仓库，私服也可以从中央仓库获取资源，作用：
    * 保存具有版权的资源，包含购买或自主研发的jar
    * 一定范围内共享资源，能做到仅对内不对外开放

  * 本地仓库：开发者自己电脑上存储资源的仓库，也可从远程仓库获取资源



* **坐标**：Maven中的坐标用于描述仓库中资源的位置
  
  * 作用：使用唯一标识，唯一性定义资源位置，通过该标识可以将资源的识别与下载工作交由机器完成
    * https://mvnrepository.com：查询maven某一个资源的坐标，输入资源名称进行检索，
  
   * 依赖设置：
     * groupId：定义当前资源隶属组织名称（通常是域名反写，如：org.mybatis；com.itheima）
   * artifactId：定义当前资源的名称（通常是项目或模块名称，如：crm，sms）
     * version：定义当前资源的版本号
  
* packaging：定义资源的打包方式，取值一般有如下三种

  * jar：该资源打成jar包，默认是jar

  * war：该资源打成war包

  * pom：该资源是一个父资源（表明使用maven分模块管理），打包时只生成一个pom.xml不生成jar或其他包结构





***



## 环境搭建

### 环境配置

Maven的官网：http://maven.apache.org/

下载安装：Maven是一个绿色软件，解压即安装

目录结构：
	bin：可执行程序目录
	boot：maven自身的启动加载器
	conf：maven配置文件的存放目录
	lib：maven运行所需库的存放目录

配置MAVEN_HOME：

![](https://gitee.com/seazean/images/raw/master/Frame/Maven配置环境变量.png)



环境变量配置好之后需要测试环境配置结果，在DOS命令窗口下输入以下命令查看输出：`mvn -v`



***



### 仓库配置

默认情况下maven本地仓库在系统盘当前用户目录下的`.m2/repository`，修改Maven的配置文件`conf/settings.xml`来修改仓库位置

* 修改本地仓库位置：找到<localRepository>标签，修改默认值

  ```xml
  <!-- localRepository
  | The path to the local repository maven will use to store artifacts.
  | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <localRepository>E:\Workspace\Java\Project\.m2\repository</localRepository>
  ```

  注意：在仓库的同级目录即`.m2`也应该包含一个`settings.xml`配置文件，局部用户配置优先与全局配置
  * 全局setting定义了Maven的公共配置
  * 用户setting定义了当前用户的配置

* 修改远程仓库：在配置文件中找到`<mirrors>`标签，在这组标签下添加国内镜像

  ```xml
  <mirror>
      <id>nexus-aliyun</id>
      <mirrorOf>central</mirrorOf>  <!--必须是central-->
      <name>Nexus aliyun</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
  </mirror>
  ```

* 修改默认JDK：在配置文件中找到`<profiles>`标签，添加配置：

  ```xml
  <profile> 
      <id>jdk-10</id> 
      <activation> 
          <activeByDefault>true</activeByDefault> 
          <jdk>10</jdk> 
      </activation>
      <properties>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <maven.compiler.source>10</maven.compiler.source> 
          <maven.compiler.target>10</maven.compiler.target>  
      </properties>  
  </profile>
  ```





***





## 项目搭建

### 手动搭建

1. 在E盘下创建目录`mvnproject`并进入该目录，作为我们的操作目录

2. 创建我们的maven项目，创建一个目录`project-java`作为我们的项目文件夹，并进入到该目录

3. 创建java代码（源代码）所在目录，即创建`src/main/java`

4. 创建配置文件所在目录，即创建`src/main/resources`

5. 创建测试源代码所在目录，即创建`src/test/java`

6. 创建测试存放配置文件存放目录，即`src/test/resources`

7. 在`src/main/java`中创建一个包（注意在windos文件夹下就是创建目录）`demo`，在该目录下创建`Demo.java`文件，作为演示所需java程序，内容如下

   ```java
   package demo;
   public class Demo{
   	public String say(String name){
   		System.out.println("hello "+name);
   		return "hello "+name;
   	}
   }
   ```

8. 在`src/test/java`中创建一个测试包（目录）`demo`，在该包下创建测试程序`DemoTest.java`

   ```java
   package demo;
   import org.junit.*;
   public class DemoTest{
   	@Test
   	public void testSay(){
   		Demo d = new Demo();
   		String ret = d.say("maven");
   		Assert.assertEquals("hello maven",ret);
   	}
   }
   ```

9. **在`project-java/src`下创建`pom.xml`文件，格式如下：**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project
       xmlns="http://maven.apache.org/POM/4.0.0"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 	
                           http://maven.apache.org/maven-v4_0_0.xsd">
       
       <!--指定pom的模型版本-->
       <modelVersion>4.0.0</modelVersion>
       <!--打包方式，web工程打包为war，java工程打包为jar -->
       <packaging>jar</packaging>
       
       <!--组织id-->
       <groupId>demo</groupId>
   	<!--项目id-->
       <artifactId>project-java</artifactId>
       <!--版本号:release,snapshot-->
       <version>1.0</version>
       
       <!--设置当前工程的所有依赖-->
       <dependencies>
           <!--具体的依赖-->
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>4.12</version>
           </dependency>
       </dependencies>
   </project>
   ```

10. 搭建好了maven的项目结构，通过maven来构建项目
    maven的构建命令以`mvn`开头，后面添加功能参数，可以一次性执行多个命令，用空格分离
    `mvn compile`：编译
    `mvn clean`：清理
    `mvn test`：测试
    `mvn package`：打包
    `mvn install`：安装到本地仓库

    注意：执行某一条命令，则会把前面所有的都执行一遍



***



### 插件构建

![](https://gitee.com/seazean/images/raw/master/Frame/Maven-插件构建.png)



***



### IDEA搭建

#### 不用原型

1. 在IDEA中配置Maven，选择maven3.6.1防止依赖问题
   <img src="https://gitee.com/seazean/images/raw/master/Frame/IDEA配置Maven.png" alt="IDEA配置Maven" style="zoom:67%;" />

2. 创建Maven，New Module --> Maven --> 不选中Create from archetype

3. 填写项目的坐标
   GroupId：demo
   ArtifactId：project-java

4. 查看各目录颜色标记是否正确

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA创建Maven目录结构.png)

5. IDEA右侧侧栏有Maven Project，打开后有Lifecycle生命周期

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA-Maven生命周期.png)

6. 自定义Maven命令：Run --> Edit Configurations --> 左上角 +  --> Maven

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA配置Maven命令.png)





#### 使用原型

普通工程：

1. 创建maven项目的时候选择使用原型骨架

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA创建Maven-quickstart.png)

2. 创建完成后发现通过这种方式缺少一些目录，需要手动去补全目录，并且要对补全的目录进行标记



web工程：

1. 选择web对应的原型骨架（选择maven开头的是简化的）

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA创建Maven-webapp.png)

2. 通过原型创建web项目得到的目录结构是不全的，因此需要我们自行补全，同时要标记正确

3. web工程创建之后需要启动运行，使用tomcat插件来运行项目，在`pom.xml`中添加插件的坐标：

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" 		
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                                http://maven.apache.org/maven-v4_0_0.xsd">
   
     <modelVersion>4.0.0</modelVersion>
     <packaging>war</packaging>
   
     <name>web01</name>
     <groupId>demo</groupId>
     <artifactId>web01</artifactId>
     <version>1.0-SNAPSHOT</version>
   
     <dependencies>
     </dependencies>
   
     <!--构建-->
     <build>
       <!--设置插件-->
       <plugins>
         <!--具体的插件配置-->
         <plugin>
           <!--https://mvnrepository.com/  搜索-->
           <groupId>org.apache.tomcat.maven</groupId>
           <artifactId>tomcat7-maven-plugin</artifactId>
           <version>2.1</version>
           <configuration>
               <port>80</port> <!--80端口默认不显示-->
               <path>/</path>
           </configuration>
         </plugin>
       </plugins>
     </build>
   </project>
   ```

4. 插件配置以后，在IDEA右侧`maven-project`操作面板看到该插件，并且可以利用该插件启动项目
   web01-->Plugins-->tomcat7-->tomcat7:run

   

***



## 依赖管理

### 依赖配置

依赖是指在当前项目中运行所需的jar，依赖配置的格式如下：

```xml
<!--设置当前项目所依赖的所有jar-->
<dependencies>
    <!--设置具体的依赖-->
    <dependency>
        <!--依赖所属群组id-->
        <groupId>junit</groupId>
        <!--依赖所属项目id-->
        <artifactId>junit</artifactId>
        <!--依赖版本号-->
        <version>4.12</version>
    </dependency>
</dependencies>
```



***



### 依赖传递

依赖具有传递性，分两种：

* 直接依赖：在当前项目中通过依赖配置建立的依赖关系

* 间接依赖：被依赖的资源如果依赖其他资源，则表明当前项目间接依赖其他资源

  注意：直接依赖和间接依赖其实也是一个相对关系

  

依赖传递的冲突问题：在依赖传递过程中产生了冲突，有三种优先法则

* 路径优先：当依赖中出现相同资源时，层级越深，优先级越低，反之则越高

* 声明优先：当资源在相同层级被依赖时，配置顺序靠前的覆盖靠后的

* 特殊优先：当同级配置了相同资源的不同版本时，后配置的覆盖先配置的



**可选依赖：**对外隐藏当前所依赖的资源，不透明

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.11</version>
    <optional>true</optional> <!--默认是false，true以后就变得不透明-->
</dependency>
```

**排除依赖：主动**断开依赖的资源，被排除的资源无需指定版本

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
    <exclusions>
        <exclusion>
            <groupId>org.hamcrest</groupId>  <!--排除这个资源-->
            <artifactId>hamcrest-core</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```



***



### 依赖范围

依赖的jar默认情况可以在任何地方可用，可以通过`scope`标签设定其作用范围，有三种：

* 主程序范围有效（src/main目录范围内）

* 测试程序范围内有效（src/test目录范围内）

* 是否参与打包（package指令范围内）

`scope`标签的取值有四种：`compile,test,provided,runtime`

![](https://gitee.com/seazean/images/raw/master/Frame/Maven依赖范围.png)



**依赖范围的传递性：**

![](https://gitee.com/seazean/images/raw/master/Frame/Maven依赖范围的传递性.png)





***



## 生命周期

### 相关事件

Maven的构建生命周期描述的是一次构建过程经历了多少个事件

最常用的一套流程：compile --> test-compile --> test --> package --> install

* clean：清理工作
  * pre-clean：执行一些在clean之前的工作
  * clean：移除上一次构建产生的所有文件
  * post-clean：执行一些在clean之后立刻完成的工作

* default：核心工作，例如编译，测试，打包，部署等

  对于default生命周期，每个事件在执行之前都会**将之前的所有事件依次执行一遍**

  ![](https://gitee.com/seazean/images/raw/master/Frame/Maven-default生命周期.png)

* site：产生报告，发布站点等
  * pre-site：执行一些在生成站点文档之前的工作
  * site：生成项目的站点文档
  * post-site：执行一些在生成站点文档之后完成的工作，并为部署做准备
  * site-deploy：将生成的站点文档部署到特定的服务器上



***



### 执行事件

Maven的插件用来执行生命周期中的相关事件

- 插件与生命周期内的阶段绑定，在执行到对应生命周期时执行对应的插件

- maven默认在各个生命周期上都绑定了预先设定的插件来完成相应功能

- 插件还可以完成一些自定义功能

  ```xml
  <build>
      <plugins>
          <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-source-plugin</artifactId>
              <version>2.2.1</version>
              <!--执行-->
              <excutions>
                  <!--具体执行位置-->
                  <excution>
                      <goals>
                          <!--对源码进行打包，打包放在target目录-->
                      	<goal>jar</goal>
                          <!--对测试代码进行打包-->
                          <goal>test-jar</goal>
                      </goals>
                      <!--执行的生命周期-->
                      <phase>generate-test-resources</phase> 
                  </excution>
              </excutions>
          </plugin>
      </plugins>
  </build>
  ```



***



## 模块开发

### 拆分

工程模块与模块划分：

![](https://gitee.com/seazean/images/raw/master/JavaWeb/Maven模块划分.png)

* ssm_pojo拆分

  * 新建模块，拷贝原始项目中对应的相关内容到ssm_pojo模块中
  * 实体类（User）
  * 配置文件（无） 

* ssm_dao拆分

  * 新建模块

  * 拷贝原始项目中对应的相关内容到ssm_dao模块中

    - 数据层接口（UserDao）

    - 配置文件：保留与数据层相关配置文件(3个）

    - 注意：分页插件在配置中与SqlSessionFactoryBean绑定，需要保留

    - pom.xml：引入数据层相关坐标即可，删除springmvc相关坐标

      - spring
      - mybatis
      - spring 整合mybatis
      - mysql
      - druid
      - pagehelper
      - 直接依赖ssm_pojo（对ssm_pojo模块执行install指令，将其安装到本地仓库）

      ```xml
      <dependencies>
          <!--导入资源文件pojo-->
          <dependency>
              <groupId>demo</groupId>
              <artifactId>ssm_pojo</artifactId>
              <version>1.0-SNAPSHOT</version>
          </dependency>
          <!--spring环境-->
          <!--mybatis环境-->
          <!--mysql环境-->
          <!--spring整合jdbc-->
          <!--spring整合mybatis-->
          <!--druid连接池-->
          <!--分页插件坐标-->
          
      </dependencies>
      ```

* ssm_service拆分

  * 新建模块
  * 拷贝原始项目中对应的相关内容到ssm_service模块中

    - 业务层接口与实现类（UserService、UserServiceImpl）
    - 配置文件：保留与数据层相关配置文件(1个）
    - pom.xml：引入数据层相关坐标即可，删除springmvc相关坐标

      - spring

      - junit

      - spring 整合junit

      - 直接依赖ssm_dao（对ssm_dao模块执行install指令，将其安装到本地仓库）

      - 间接依赖ssm_pojo（由ssm_dao模块负责依赖关系的建立）
    - 修改service模块spring核心配置文件名，添加模块名称，格式：applicationContext-service.xml
    - 修改dao模块spring核心配置文件名，添加模块名称，格式：applicationContext-dao.xml
    - 修改单元测试引入的配置文件名称，由单个文件修改为多个文件

* ssm_control拆分
  * 新建模块（使用webapp模板）

  * 拷贝原始项目中对应的相关内容到ssm_controller模块中

    - 现层控制器类与相关设置类（UserController、异常相关……）

    - 配置文件：保留与表现层相关配置文件(1个）、服务器相关配置文件（1个）

    - pom.xml：引入数据层相关坐标即可，删除springmvc相关坐标

      - spring

      - springmvc

      - jackson

      - servlet

      - tomcat服务器插件

      - 直接依赖ssm_service（对ssm_service模块执行install指令，将其安装到本地仓库）

      - 间接依赖ssm_dao、ssm_pojo

        ```xml
        <dependencies>
              <!--导入资源文件service-->
              <dependency>
                  <groupId>demo</groupId>
                  <artifactId>ssm_service</artifactId>
                  <version>1.0-SNAPSHOT</version>
              </dependency>
        
            <!--springmvc环境-->
            <!--jackson相关坐标3个-->
            <!--servlet环境-->
          </dependencies>
        
          <build>
            <!--设置插件-->
            <plugins>
              <!--具体的插件配置-->
              <plugin>
              </plugin>
            </plugins>
          </build>
        ```

    - 修改web.xml配置文件中加载spring环境的配置文件名称，使用*通配，加载所有applicationContext-开始的配置文件：

      ```xml
      <!--加载配置文件-->
      <context-param>
          <param-name>contextConfigLocation</param-name>
          <param-value>classpath*:applicationContext-*.xml</param-value>
      </context-param>
      ```

    - spring-mvc

      ```xml
      <mvc:annotation-driven/>
      <context:component-scan base-package="controller"/>
      ```

      

***



### 聚合

作用：聚合用于快速构建maven工程，一次性构建多个项目/模块。

制作方式：

- 创建一个空模块，打包类型定义为pom

  ```xml
  <packaging>pom</packaging>
  ```

- 定义当前模块进行构建操作时关联的其他模块名称

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="............">
      <modelVersion>4.0.0</modelVersion>
  
      <groupId>demo</groupId>
      <artifactId>ssm</artifactId>
      <version>1.0-SNAPSHOT</version>
  
      <!--定义该工程用于构建管理-->
      <packaging>pom</packaging>
  
      <!--管理的工程列表-->
      <modules>
          <!--具体的工程名称-->
          <module>../ssm_pojo</module>
              <module>../ssm_dao</module>
              <module>../ssm_service</module>
              <module>../ssm_controller</module>
          </modules>
  </project>
  ```

注意事项：参与聚合操作的模块最终执行顺序与模块间的依赖关系有关，与配置顺序无关



***



### 继承

作用：通过继承可以实现在子工程中沿用父工程中的配置

- maven中的继承与java中的继承相似，在子工程中配置继承关系

制作方式：

- 在子工程中声明其父工程坐标与对应的位置

  ```xml
  <!--定义该工程的父工程-->
  <parent>
      <groupId>com.itheima</groupId>
      <artifactId>ssm</artifactId>
      <version>1.0-SNAPSHOT</version>
      <!--填写父工程的pom文件-->
      <relativePath>../ssm/pom.xml</relativePath>
  </parent>
  ```

- 继承依赖的定义：在父工程中定义依赖管理

  ```xml
  <!--声明此处进行依赖管理，版本锁定-->
  <dependencyManagement>
      <!--具体的依赖-->
      <dependencies>
          <!--spring环境-->
          <dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-context</artifactId>
              <version>5.1.9.RELEASE</version>
          </dependency>
          <!--等等所有-->
      <dependencies>
  <dependencyManagement>
  ```

- 继承依赖的使用：在子工程中定义依赖关系，**无需声明依赖版本**，版本参照父工程中依赖的版本

  ```xml
  <dependencies>
      <!--spring环境-->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-context</artifactId>
      </dependency>
  </dependencies>
  ```

- 继承的资源：

  ```xml
  groupId：项目组ID，项目坐标的核心元素
  version：项目版本，项目坐标的核心因素
  description：项目的描述信息
  organization：项目的组织信息
  inceptionYear：项目的创始年份
  url：项目的URL地址
  developers：项目的开发者信息
  contributors：项目的贡献者信息
  distributionManagement：项目的部署配置
  issueManagement：项目的缺陷跟踪系统信息
  ciManagement：项目的持续集成系统信息
  scm：项目的版本控制系统西溪
  malilingLists：项目的邮件列表信息
  properties：自定义的Maven属性
  dependencies：项目的依赖配置
  dependencyManagement：项目的依赖管理配置
  repositories：项目的仓库配置
  build：包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等
  reporting：包括项目的报告输出目录配置、报告插件配置等
  ```

- 继承与聚合：

  作用：

  - 聚合用于快速构建项目

  - 继承用于快速配置

  相同点：

  - 聚合与继承的pom.xml文件打包方式均为pom，可以将两种关系制作到同一个pom文件中

  - 聚合与继承均属于设计型模块，并无实际的模块内容

  不同点：

  - 聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些

  - 继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己



***



### 属性

* 版本统一的重要性： 

  ![](https://gitee.com/seazean/images/raw/master/Frame/Maven版本统一的重要性.png)

* 属性类别：

  1.自定义属性   2.内置属性    3.Setting属性   4.Java系统属性   5.环境变量属性

* 自定义属性：

  作用：等同于定义变量，方便统一维护

  定义格式：

  ```xml
  <!--定义自定义属性，放在dependencyManagement上方-->
  <properties>
      <spring.version>5.1.9.RELEASE</spring.version>
      <junit.version>4.12</junit.version>
  </properties>
  ```

  - 聚合与继承的pom.xml文件打包方式均为pom，可以将两种关系制作到同一个pom文件中

  - 聚合与继承均属于设计型模块，并无实际的模块内容

  调用格式：

  ```xml
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
  </dependency>
  ```

* 内置属性：

  作用：使用maven内置属性，快速配置

  调用格式：

  ```xml
  ${project.basedir} or ${project.basedir}  <!--../ssm根目录-->
  ${version} or ${project.version}
  ```

  * vresion是1.0-SNAPSHOT

    ```xml
    <groupId>demo</groupId>
    <artifactId>ssm</artifactId>
    <version>1.0-SNAPSHOT</version>
    ```

* Setting属性

  - 使用Maven配置文件setting.xml中的标签属性，用于动态配置

  调用格式：

  ```xml
  ${settings.localRepository} 
  ```

* Java系统属性：

  作用：读取Java系统属性

  调用格式：

  ```
  ${user.home}
  ```

  系统属性查询方式  cmd命令：

  ```sh
  mvn help:system 
  ```

* 环境变量属性

  作用：使用Maven配置文件setting.xml中的标签属性，用于动态配置

  调用格式：

  ```
  ${env.JAVA_HOME} 
  ```

  环境变量属性查询方式：

  ```
  mvn help:system 
  ```




***



### 工程版本

SNAPSHOT（快照版本）

- 项目开发过程中，为方便团队成员合作，解决模块间相互依赖和时时更新的问题，开发者对每个模块进行构建的时候，输出的临时性版本叫快照版本（测试阶段版本）

- 快照版本会随着开发的进展不断更新

RELEASE（发布版本）

- 项目开发到进入阶段里程碑后，向团队外部发布较为稳定的版本，这种版本所对应的构件文件是稳定的，即便进行功能的后续开发，也不会改变当前发布版本内容，这种版本称为发布版本

约定规范：

- <主版本>.<次版本>.<增量版本>.<里程碑版本>

- 主版本：表示项目重大架构的变更，如：spring5相较于spring4的迭代

- 次版本：表示有较大的功能增加和变化，或者全面系统地修复漏洞

- 增量版本：表示有重大漏洞的修复

- 里程碑版本：表明一个版本的里程碑（版本内部）。这样的版本同下一个正式版本相比，相对来说不是很稳定，有待更多的测试

范例：

- 5.1.9.RELEASE



***





### 资源配置

作用：在任意配置文件中加载pom文件中定义的属性

* 父文件pom.xml

  ```xml
  <properties>
      <jdbc.url>jdbc:mysql://192.168.0.137:3306/ssm_db?useSSL=false</jdbc.url>
  </properties>
  ```

- 开启配置文件加载pom属性：

  ```xml
  <!--配置资源文件对应的信息-->
  <resources>
      <resource>
          <!--设定配置文件对应的位置目录，支持使用属性动态设定路径-->
          <directory>${project.basedir}/src/main/resources</directory>
          <!--开启对配置文件的资源加载过滤-->
          <filtering>true</filtering>
      </resource>
  </resources>
  ```

* properties文件中调用格式：

  ```xml-dtd
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=${jdbc.url}
  jdbc.username=root
  jdbc.password=123456
  ```



***



### 多环境配置

* 环境配置

  ```xml
  <!--创建多环境-->
  <profiles>
      <!--定义具体的环境：生产环境-->
      <profile>
          <!--定义环境对应的唯一名称-->
          <id>pro_env</id>
          <!--定义环境中专用的属性值-->
          <properties>
              <jdbc.url>jdbc:mysql://127.1.1.1:3306/ssm_db</jdbc.url>
          </properties>
          <!--设置默认启动-->
          <activation>
              <activeByDefault>true</activeByDefault>
          </activation>
      </profile>
      <!--定义具体的环境：开发环境-->
      <profile>
          <id>dev_env</id>
          ……
      </profile>
  </profiles>
  ```

* 加载指定环境

  作用：加载指定环境配置

  调用格式：

  ```
  mvn 指令 –P 环境定义id
  ```

  范例：

  ```
  mvn install –P pro_env
  ```




***



## 跳过测试

### 命令跳过

命令：

```
mvn 指令 –D skipTests
```

注意事项：执行的指令生命周期必须包含测试环节



### IEDA界面

![](https://gitee.com/seazean/images/raw/master/Frame/IDEA使用界面操作跳过测试.png)



### 配置跳过

```xml
<plugin>
    <!--<groupId>org.apache.maven</groupId>-->
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.22.1</version>
    <configuration>
        <skipTests>true</skipTests><!--设置跳过测试-->
        <includes> <!--包含指定的测试用例-->
            <include>**/User*Test.java</include>
        </includes>
        <excludes><!--排除指定的测试用例-->
            <exclude>**/User*TestCase.java</exclude>
        </excludes>
    </configuration>
</plugin>
```



***



## 私服

### Nexus

Nexus是Sonatype公司的一款maven私服产品

下载地址：https://help.sonatype.com/repomanager3/download 

启动服务器（命令行启动）：

```
nexus.exe /run nexus
```

访问服务器（默认端口：8081）：

```
http://localhost:8081
```

修改基础配置信息

- 安装路径下etc目录中nexus-default.properties文件保存有nexus基础配置信息，例如默认访问端口

修改服务器运行配置信息

- 安装路径下bin目录中nexus.vmoptions文件保存有nexus服务器启动对应的配置信息，例如默认占用内存空间



***



### 资源操作

![](https://gitee.com/seazean/images/raw/master/Frame/Maven私服资源获取.png)



仓库分类：

* 宿主仓库hosted 
  * 保存无法从中央仓库获取的资源
    * 自主研发
    * 第三方非开源项目

* 代理仓库proxy 
  * 代理远程仓库，通过nexus访问其他公共仓库，例如中央仓库

* 仓库组group 
  * 将若干个仓库组成一个群组，简化配置
  * 仓库组不能保存资源，属于设计型仓库



资源上传，上传资源时提供对应的信息

- 保存的位置（宿主仓库）

- 资源文件

- 对应坐标



***



### IDEA操作

#### 上传下载

![](https://gitee.com/seazean/images/raw/master/Frame/IDEA环境中资源上传与下载.png)



***



#### 访问私服配置

##### 本地仓库访问私服

配置本地仓库访问私服的权限（setting.xml）

```xml
<servers>
    <server>
        <id>heima-release</id>
        <username>admin</username>
        <password>admin</password>
    </server>
    <server>
        <id>heima-snapshots</id>
        <username>admin</username>
        <password>admin</password>
    </server>
</servers>
```

配置本地仓库资源来源（setting.xml）

```xml
<mirrors>
    <mirror>
        <id>nexus-heima</id>
        <mirrorOf>*</mirrorOf>
        <url>http://localhost:8081/repository/maven-public/</url>
    </mirror>
</mirrors>
```



##### 项目工程访问私服

配置当前项目访问私服上传资源的保存位置（pom.xml）

```xml
<distributionManagement>
    <repository>
        <id>heima-release</id>
        <url>http://localhost:8081/repository/heima-release/</url>
    </repository>
    <snapshotRepository>
        <id>heima-snapshots</id>
        <url>http://localhost:8081/repository/heima-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

发布资源到私服命令

```
mvn deploy
```





***



## 日志

### Log4j

程序中的日志可以用来记录程序在运行时候的详情，并可以进行永久存储。

|          | 输出语句                   | 日志技术                                 |
| -------- | -------------------------- | ---------------------------------------- |
| 取消日志 | 需要修改代码，灵活性比较差 | 不需要修改代码，灵活性比较好             |
| 输出位置 | 只能是控制台               | 可以将日志信息写入到文件或者数据库中     |
| 多线程   | 和业务代码处于一个线程中   | 多线程方式记录日志，不影响业务代码的性能 |

Log4j是Apache的一个开源项目。
使用Log4j，通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。我们可以控制日志信息输送的目的地是控制台、文件等位置，也可以控制每一条日志的输出格式。

<img src="https://gitee.com/seazean/images/raw/master/Frame/日志体系结构.png" style="zoom:50%;" />



***



### 配置文件

配置文件的三个核心：

+ 配置根Logger

  + 格式：log4j.rootLogger=日志级别，appenderName1，appenderName2，…

  + 日志级别：常见的五个级别：**DEBUG < INFO < WARN < ERROR < FATAL**（可以自定义）
    Log4j规则：只输出级别不低于设定级别的日志信息

  + appenderName1：指定日志信息要输出地址。可以同时指定多个输出目的地，用逗号隔开：

    例如：log4j.rootLogger＝INFO，ca，fa

+ Appenders(输出源)：日志要输出的地方，如控制台（Console）、文件（Files）等

  + Appenders取值：
    + org.apache.log4j.ConsoleAppender（控制台）
    + org.apache.log4j.FileAppender（文件）

  + ConsoleAppender常用参数
    + `ImmediateFlush=true`：表示所有消息都会被立即输出，设为false则不输出，默认值是true。
    + `Target=System.err`：默认值是System.out
  + FileAppender常用的选项
    + `ImmediateFlush=true`：表示所有消息都会被立即输出。设为false则不输出，默认值是true

    + `Append=false`：true表示将消息添加到指定文件中，原来的消息不覆盖。默认值是true

    + `File=E:/logs/logging.log4j`：指定消息输出到logging.log4j文件中

+ Layouts(布局)：日志输出的格式，常用的布局管理器：

  + org.apache.log4j.PatternLayout（可以灵活地指定布局模式）

+ org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）

+ org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等信息）

+ PatternLayout常用的选项
  <img src="https://gitee.com/seazean/images/raw/master/Frame/日志-PatternLayout常用的选项.png" style="zoom:80%;" />



***



### 日志应用

* log4j的配置文件,名字为log4j.properties, 放在src根目录下

  ```properties
  log4j.rootLogger=debug,my,fileAppender
  
  ### direct log messages to my ###
  log4j.appender.my=org.apache.log4j.ConsoleAppender
  log4j.appender.my.ImmediateFlush = true
  log4j.appender.my.Target=System.out
  log4j.appender.my.layout=org.apache.log4j.PatternLayout
  log4j.appender.my.layout.ConversionPattern=%d %t %5p %c{1}:%L - %m%n
  
  # fileAppender演示
  log4j.appender.fileAppender=org.apache.log4j.FileAppender
  log4j.appender.fileAppender.ImmediateFlush = true
  log4j.appender.fileAppender.Append=true
  log4j.appender.fileAppender.File=E:/log4j-log.log
  log4j.appender.fileAppender.layout=org.apache.log4j.PatternLayout
  log4j.appender.fileAppender.layout.ConversionPattern=%d %5p %c{1}:%L - %m%n
  ```

* 测试类

  ```java
  // 测试类
  public class Log4JTest01 {
  
      //使用log4j的api来获取日志的对象
      //弊端：如果以后我们更换日志的实现类，那么下面的代码就需要跟着改
      //不推荐使用
      //private static final Logger LOGGER = Logger.getLogger(Log4JTest01.class);
  
      //使用slf4j里面的api来获取日志的对象
      //好处：如果以后我们更换日志的实现类，那么下面的代码不需要跟着修改
      //推荐使用
      private static  final Logger LOGGER = LoggerFactory.getLogger(Log4JTest01.class);
  
      public static void main(String[] args) {
          //1.导入jar包
          //2.编写配置文件
          //3.在代码中获取日志的对象
          //4.按照日志级别设置日志信息
          LOGGER.debug("debug级别的日志");
          LOGGER.info("info级别的日志");
          LOGGER.warn("warn级别的日志");
          LOGGER.error("error级别的日志");
      }
  }
  ```








***





# MyBatis

## 基本介绍

框架是一款半成品软件，我们可以基于这个半成品软件继续开发，来完成我们个性化的需求！

ORM(Object Relational Mapping)： 对象关系映射
指的是持久化数据和实体对象的映射模式，解决面向对象与关系型数据库存在的互不匹配的现象

![](https://gitee.com/seazean/images/raw/master/Frame/ORM介绍.png)

**MyBatis**：

* MyBatis是一个优秀的基于java的持久层框架，它内部封装了JDBC，使开发者只需关注SQL语句本身，而不需要花费精力去处理加载驱动、创建连接、创建Statement等过程。

* MyBatis通过xml或注解的方式将要执行的各种 Statement配置起来，并通过Java对象和Statement中SQL的动态参数进行映射生成最终执行的sql语句。

* MyBatis框架执行SQL并将结果映射为Java对象并返回。采用ORM思想解决了实体和数据库映射的问题，对JDBC进行了封装，屏蔽了JDBC底层API的调用细节，使我们不用操作JDBC API，就可以完成对数据库的持久化操作。

MyBatis官网地址：http://www.mybatis.org/mybatis-3/



***



## 基本操作

### 相关API

#### Resources

org.apache.ibatis.io.Resources : 加载资源的工具类

`InputStream getResourceAsStream(String fileName)` : 通过类加载器返回指定资源的字节流

* 参数fileName是放在src的核心配置文件名：MyBatisConfig.xml



#### SqlSessionFactoryBuilder

org.apache.ibatis.session.SqlSessionFactoryBuilder : 构建器，用来获取 SqlSessionFactory 工厂对象

`SqlSessionFactory build(InputStream is)` : 通过指定资源的字节输入流获取SqlSession工厂对象



#### SqlSessionFactory

org.apache.ibatis.session.SqlSessionFactory : 获取 SqlSession 构建者对象的工厂接口

`SqlSession openSession()` : 获取SqlSession构建者对象，并开启手动提交事务

`SqlSession openSession(boolean)` : 获取SqlSession构建者对象，参数为true开启自动提交事务



#### SqlSession

org.apache.ibatis.session.SqlSession : 构建者对象接口。用于执行 SQL、管理事务、接口代理

注：**设计update数据需要提交事务，或开启默认提交**

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



### #{}和${}

**#{}：**占位符，传入的内容会作为字符串，加上引号，以预编译的方式传入，将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值，有效的防止SQL注入，提高系统安全性

**${}：**拼接符，传入的内容会直接替换拼接，不会加上引号，可能存在sql注入的安全隐患

* 能用 #{} 的地方就用 #{}，不用或少用 ${}，
* 必须使用 ${} 的情况：
  * 表名作参数时，如：`SELECT * FROM ${tableName}`
  * order by 时，如：`SELECT * FROM t_user ORDER BY ${columnName}`

* Sql语句使用#{}，properties文件内容获取使用${} 





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

* 参数获取方式：
  * SQL 获取参数：#{属性名}

    ```xml-dtd
    <mapper namespace="StudentMapper">
        <select id="selectById" resultType="student" parameterType="int">
    		SELECT * FROM student WHERE id = #{id}
        </select>
    <mapper/>
    ```



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

    

* 起别名：

  * <typeAliases>：为全类名起别名的父标签。

    * <typeAlias>：为全类名起别名的子标签
      * type：指定全类名      
      * alias：指定别名

  * <package>：为指定包下所有类起别名的子标签(别名就是类名，首字母小写)

    ```xml-dtd
    <!--起别名-->
    <typeAliases>
    <typeAlias type="bean.Student" alias="student"/>
    <!--<package name="com.itheima.bean"/>-->
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



* 配置环境
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
  * <mapper>：引入映射配置文件子标签。resource属性指定映射配置文件的名称



***



### 日志文件

在日常开发过程中，排查问题时难免需要输出 MyBatis 真正执行的 SQL 语句、参数、结果等信息，我们就可以借助 LOG4J 的功能来实现执行信息的输出。

* 在核心配置文件根标签内配置log4j

  ```xml
  <!--配置LOG4J-->
  <settings>
  	<setting name="logImpl" value="log4j"/>
  </settings>
  ```

* 在src目录下创建log4j.properties

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



### 配置实现

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
      //SqlSession sqlSession = sqlSessionFactory.openSession();
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

  



***



## 代理开发

### 分层思想

分层思想：控制层(controller)、业务层(service)、持久层(dao)

传统实现方式：参考JDBCTemplate

调用流程：

![](https://gitee.com/seazean/images/raw/master/Frame/分层思想调用流程.png)



### 代理规则

传统方式实现DAO层，需要写接口和实现类。采用 Mybatis 的代理开发方式实现 DAO 层的开发，只需要编写Mapper 接口（相当于Dao 接口），由Mybatis 框架根据接口定义创建接口的动态代理对象。

接口开发方式：

1. 定义接口
2. 操作数据库，mybatis框架根据接口，通过动态代理的方式生成代理对象，负责数据库的crud操作

Mapper 接口开发需要遵循以下规范：

* Mapper.xml文件中的namespace与DAO层mapper接口的全类名相同

* Mapper.xml文件中的增删改查标签的id属性和DAO层Mapper接口方法名相同

* Mapper.xml文件中的增删改查标签的parameterType属性和DAO层Mapper接口方法的参数相同

* Mapper.xml文件中的增删改查标签的resultType属性和DAO层Mapper接口方法的返回值相同

  ![](https://gitee.com/seazean/images/raw/master/Frame/接口代理方式实现DAO层.png)
  
  

***



### 源码分析

* 动态代理对象如何生成的？ 

  通过动态代理开发模式，我们只编写一个接口，不写实现类，我们通过 **getMapper()** 方法最终获取到 org.apache.ibatis.binding.MapperProxy 代理对象，然后执行功能，而这个代理对象正是 MyBatis 使用了 JDK 的动态代理技术，帮助我们生成了代理实现类对象。从而可以进行相关持久化操作

* 方法是如何执行的？

  动态代理实现类对象在执行方法的时候最终调用了 **MapperMethod.execute()** 方法，这个方法中通过 switch case语句根据操作类型来判断是新增、修改、删除、查询操作，最后一步回到了 MyBatis 最原生的**SqlSession方式来执行增删改查**。 

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



## 动态SQL

逻辑复杂时，MyBatis映射配置文件中，SQL是动态变化的

### where标签

```xml-dtd
<where>：条件标签。有动态条件，则使用该标签代替 WHERE关键字
```



### if标签

基本格式：

```xml
<if test=“条件判断”>
	查询条件拼接
</if>
```

我们根据实体类的不同取值，使用不同的 SQL语句来进行查询。比如在 id如果不为空时可以根据id查询，如果username 不同空时还要加入用户名作为条件。这种情况在我们的多条件组合查询中经常会碰到。

* StudentMapper.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <mapper namespace="mapper.StudentMapper">
      <select id="selectCondition" resultType="student" parameterType="student">
          SELECT * FROM student
          <where>
              <if test="id != null ">
                  id = #{id}
              </if>
              <if test="name != null ">
                  AND name = #{name}
              </if>
              <if test="age != null ">
                  AND age = #{age}
              </if>
          </where>
      </select>	
  
  </mapper>
  ```

* MyBatisConfig.xml，引入映射配置文件

  ```xml
  <mappers>
      <!--mapper引入指定的映射配置 resource属性执行的映射配置文件的名称-->
      <mapper resource="StudentMapper.xml"/>
  </mappers>
  ```

* DAO层Mapper接口

  ```java
  public interface StudentMapper {
      //多条件查询
      public abstract List<Student> selectCondition(Student stu);
  }
  ```

* 实现类

  ```java
  public class DynamicTest01 {
      @Test
      public void selectCondition() throws Exception{
          //1.加载核心配置文件
          InputStream is = Resources.getResourceAsStream("MyBatisConfig.xml");
  
          //2.获取SqlSession工厂对象
          SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(is);
  
          //3.通过工厂对象获取SqlSession对象
          SqlSession sqlSession = ssf.openSession(true);
  
          //4.获取StudentMapper接口的实现类对象
          StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
  
          Student stu = new Student();
          stu.setId(2);
          stu.setName("李四");
          //stu.setAge(24);
  
          //5.调用实现类的方法，接收结果
          List<Student> list = mapper.selectCondition(stu);
  
          //6.处理结果
          for (Student student : list) {
              System.out.println(student);
          }
          
          //7.释放资源
          sqlSession.close();
          is.close();
      }
  }
  ```

  

***



### foreach标签

基本格式：

```xml
<foreach>：循环遍历标签。适用于多个参数或者的关系。
    <foreach collection=“”open=“”close=“”item=“”separator=“”>
		获取参数
</foreach>
```

属性：

* collection：参数容器类型， (list-集合， array-数组)。
* open：开始的 SQL 语句。
* close：结束的 SQL 语句。
* item：参数变量名。
* separator：分隔符。

需求：循环执行sql的拼接操作，SELECT * FROM student  WHERE id IN (1,2,5)

* StudentMapper.xml片段

  ```xml
  <select id="selectByIds" resultType="student" parameterType="list">
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
  StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
  
  List<Integer> ids = new ArrayList<>();
  Collections.addAll(list, 1, 2);
  //5.调用实现类的方法，接收结果
  List<Student> list = mapper.selectByIds(ids);
  
  for (Student student : list) {
      System.out.println(student);
  }
  ```

  

***



### SQL片段抽取

将一些重复性的 SQL 语句进行抽取，以达到复用的效果

格式：

```xml
<sql id=“片段唯一标识”>抽取的SQL语句</sql>		<!--抽取标签-->
<include refid=“片段唯一标识”/>				<!--引入标签-->
```

使用：

```xml
<sql id="select">SELECT * FROM student</sql>

<select id="selectByIds" resultType="student" parameterType="list">
    <include refid="select"/>
    <where>
        <foreach collection="list" open="id IN(" close=")" item="id" separator=",">
            #{id}
        </foreach>
    </where>
 </select>
```



***



## 分页插件

### 分页介绍

![](https://gitee.com/seazean/images/raw/master/Frame/分页介绍.png)

* 分页可以将很多条结果进行分页显示。如果当前在第一页，则没有上一页。如果当前在最后一页，则没有下一页，需要明确当前是第几页，这一页中显示多少条结果。  
* MyBatis 是不带分页功能的，如果想实现分页功能，需要手动编写 LIMIT 语句，不同的数据库实现分页的 SQL 语句也是不同，手写分页 成本较高。
* PageHelper：第三方分页助手，将复杂的分页操作进行封装，从而让分页功能变得非常简单



### 插件使用

开发步骤：

1. 导入与PageHelper的jar包

2. 在mybatis核心配置文件中配置PageHelper插件
   注意：分页助手的插件配置在通用mapper之前

   ```xml
   <plugins>
       <plugin interceptor="com.github.pagehelper.PageInterceptor">
           <!-- 指定方言 -->
       	<property name="dialect" value="mysql"/>
       </plugin> 
   </plugins>
   <mappers>.........</mappers>
   ```

3. 与MySQL分页查询页数计算公式不同
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

   



### 参数获取

PageInfo构造方法：
	`PageInfo<Student> info = new PageInfo<>(list)` : list是SQL执行返回的结果集合，参考上一节

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



***



## 多表操作

### 标签配置

核心配置文件标签：

* <resultMap>：配置字段和对象属性的映射关系标签。
  * id 属性：唯一标识
  * type 属性：实体对象类型

* <id>：配置主键映射关系标签。 
* <result>：配置非主键映射关系标签。
  * column 属性：表中字段名称
  * property 属性： 实体对象变量名称

* <association>：配置被包含对象的映射关系标签。（多对一、一对一）
  *  property 属性：被包含对象的变量名，要进行映射的属性名
  *  javaType 属性：被包含对象的数据类型，要进行映射的属性的类型

* <collection>：配置被包含集合对象的映射关系标签。（一对多、多对多）
  * property 属性：被包含集合对象的变量名
  * ofType 属性：集合中保存的对象数据类型 



***



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

* bean类

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
  
* 配置文件OneToOneMapper.xml，MyBatisConfig.xml需要引入(可以把bean包下起别名)

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <mapper namespace="OneToOneMapper">
      
      <!--配置字段和实体对象属性的映射关系-->
      <resultMap id="oneToOne" type="card">
          <id column="cid" property="id" />
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

* 核心配置文件MyBatisConfig.xml

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
  INSERT INTO classes VALUES (NULL,'黑马一班'),(NULL,'黑马二班')
  
  CREATE TABLE student(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	name VARCHAR(30),
  	age INT,
  	cid INT,
  	CONSTRAINT cs_fk FOREIGN KEY (cid) REFERENCES classes(id)
  );
  INSERT INTO student VALUES (NULL,'张三',23,1),(NULL,'李四',24,1),(NULL,'王五',25,2);
  ```

* bean类

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
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
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

标签属性同上

学生课程例子，中间表不需要bean实体类

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

  



****



## 缓存机制

### 缓存概述

缓存：缓存就是一块内存空间，保存临时数据

作用：将数据源（数据库或者文件）中的数据读取出来存放到缓存中，再次获取时直接从缓存中获取，可以减少和数据库交互的次数，提升程序的性能

缓存适用：

+ 适用于缓存的：经常查询但不经常修改的(eg: 省市,类别数据)，数据的正确与否对最终结果影响不大的
+ 不适用缓存的：经常改变的数据 , 敏感数据（例如：股市的牌价，银行的汇率，银行卡里面的钱)等等

缓存类别：

* 一级缓存：sqlSession级别的缓存，自带的(不需要配置)不可卸载的(必须使用)，一级缓存的生命周期与sqlSession一致
* 二级缓存：SqlSessionFactory的缓存，只要是同一个SqlSessionFactory创建的SqlSession就共享二级缓存的内容，并且可以操作二级缓存。二级缓存的使用，需要手动开启(需要配置的)



开启缓存：配置核心配置文件中<settings>标签

* cacheEnabled：全局性地开启或关闭所有映射器配置文件中已配置的任何缓存，默认true





***



### 一级缓存

一级缓存是SqlSession级别的缓存，调用SqlSession的修改添加删除、commit()、close()等方法时会清空一级缓存

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis一级缓存.png)

第一次发起查询用户id为1的用户信息，先去找缓存中是否有id为1的用户信息，如果没有，从数据库查询用户信息，得到用户信息，将用户信息存储到一级缓存中；第二次发起查询用户id为1的用户信息，先去找缓存中是否有id为1的用户信息，缓存中有，直接从缓存中获取用户信息。 

如果 sqlSession 去执行 commit操作（执行插入、更新、删除），清空 SqlSession 中的一级缓存，这样做的目的为了让缓存中存储的是最新的信息，避免脏读。

* 测试一级缓存存在

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

* 调用sqlSession的commit()或者clearCache()或者close()都能清除一级缓存

    1. sqlSession.close()  
    2. sqlSession.commit()  
    3. **sqlSession.clearCache()**  
    4.  数据发生增删改
    





****



### 二级缓存

二级缓存是SqlSessionFactory的缓存。只要是同一个SqlSessionFactory创建的SqlSession就共享二级缓存的内容，并且可以操作二级缓存

![](https://gitee.com/seazean/images/raw/master/Frame/MyBatis二级缓存.png)



二级缓存的开启与关闭：

1. 在MyBatisConfig.xml文件开启二级缓存。**cacheEnabled默认值为true**，所以这一步可以省略不配置

   ```xml
   <!--配置开启二级缓存-->
   <settings>
       <setting name="cacheEnabled" value="true"/>
   </settings>
   ```

2. 配置Mapper映射文件

   `<cache>` 标签表示当前这个 mapper 映射将使用二级缓存，区分的标准就看 mapper 的 namespace 值

   ```xml
   <mapper namespace="dao.UserDao">
       <!--开启user支持二级缓存-->
       <cache/>
       <select id="findAll" resultType="user">
           select * from user
       </select>
   
       <delete id="deleteById" parameterType="int">
           delete from user where id=#{id}
       </delete>
   </mapper>
   ```

3. 配置statement上面的useCache属性

   映射文件中的<select>标签中设置`useCache=”true”`代表当前statement要使用二级缓存。 
   注意：针对每次查询都需要最新的数据sql，要设置成useCache=false，禁用二级缓存。

   ```xml
   	<select id="findAll" resultType="user" useCache="true">
           select * from user
       </select>
   ```

4. 要进行二级缓存的类必须实现java.io.Serializable 接口，可以使用序列化方式来保存对象。 

   ```java
   public class User implements Serializable{}
   ```





****



## 延迟加载

### 两种加载

立即加载：只要调用方法，马上发起查询

延迟加载：在需要用到数据时才进行加载，不需要用到数据时就不加载数据，延迟加载也称懒加载。  

优点： 先从单表查询，需要时再从关联表去关联查询，提高数据库性能，因为查询单表要比关联查询多张表速度要快

坏处：只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，查询工作也要消耗时间，所以可能造成用户等待时间变长，造成用户体验下降



***



### Assocation实现延迟加载

一对多,多对多

* 核心配置文件

  | 标签名                | 描述                                                         | 默认值               |
  | --------------------- | ------------------------------------------------------------ | -------------------- |
  | lazyLoadingEnabled    | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | false                |
  | aggressiveLazyLoading | 开启时，任一方法的调用都会加载该对象的所有延迟加载属性。否则，每个延迟加载属性会按需加载（参考 `lazyLoadTriggerMethods`)。 | false(3.4.1版本以后) |

  ```xml
  <settings> 
  	<setting name="lazyLoadingEnabled" value="true"/> 
      <setting name="aggressiveLazyLoading" value="false"/> 
  </settings>
  ```

* 映射配置文件OneToOneMapper.xml 
一对一映射：
  
  * column属性表示往要调用的其它的select标签中传入参数
  * select属性表示调用其它的select标签
  * fetchType="lazy"表示延迟加载(局部配置，只有配置了这个的地方才会延迟加载)
  
  ```xml
  <mapper namespace="OneToOneMapper">
      <!--配置字段和实体对象属性的映射关系-->
      <resultMap id="oneToOne" type="card">
          <id column="id" property="id" />
          <result column="number" property="number" />
          <association property="p" javaType="bean.Person"
                       column="pid" 
                       select="one_to_one.PersonMapper.findPersonByid">
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
        //6.不能遍历，遍历就是相当于使用了该数据，需要加载，不遍历就是没有使用。
          
          //释放资源
          sqlSession.close();
          is.close();
      }
  }
  ```

  



***



### Collection实现延迟加载

同样在一对多关系配置的<collection>结点中配置延迟加载策略。 <collection>结点中也有select属性，column属性。

* 映射配置文件OneToManyMapper.xml
一对多映射：
  
  * column是用于指定使用哪个字段的值作为条件查询
  * select是用于指定查询账户的唯一标识（账户的dao全限定类名加上方法名称） 
  
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





***



## 注解开发

### 单表操作

注解可以简化开发操作，省略映射配置文件的编写。

 常用注解：

* @Select(“查询的SQL 语句”)：执行查询操作注解
* @Insert(“插入的SQL 语句”)：执行新增操作注解
* @Update(“修改的SQL 语句”)：执行修改操作注解
* @Delete(“删除的SQL 语句”)：执行删除操作注解

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

实现复杂关系映射之前我们可以在映射文件中通过配置<resultMap>来实现，使用注解开发后，我们可以使用@Results注解，@Result注解，@One注解，@Many注解组合完成复杂关系的配置

| 注解          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| @Results      | 代替<resultMap>标签，注解中使用单个@Result注解或者@Result集合<br/>使用格式：@Results({ @Result(), @Result() })或@Results({ @Result() }) |
| @Result       | 代替<id>和<result>标签，@Result中属性介绍：<br />column：数据库的列名      property：封装类的变量名<br />one：需要使用@One注解（@Result(one = @One)）<br />Many：需要使用@Many注解（@Result(many= @Many)） |
| @One(一对一)  | 代替<association>标签，多表查询的关键，用来指定子查询返回单一对象<br/>select：指定调用Mapper接口中的某个方法<br />使用格式：@Result(column="", property="", one=@One(select="")) |
| @Many(多对一) | 代替<collection>标签，多表查询的关键，用来指定子查询返回对象集合<br />select：指定调用Mapper接口中的某个方法<br />使用格式：@Result(column="", property="", many=@Many(select="")) |



***



#### 一对一

身份证对人

* 数据准备

  ```sql
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

* bean类

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

* 数据准备

  ```mysql
  CREATE TABLE classes(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	name VARCHAR(20)
  );
  CREATE TABLE student(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	name VARCHAR(30),
  	age INT,
  	cid INT,
  	CONSTRAINT cs_fk FOREIGN KEY (cid) REFERENCES classes(id)
  );
  ```

* bean类

  ```java
  public class Classes {
      private Integer id;     //主键id
      private String name;    //班级名称
      private List<Student> students; //班级中所有学生对象：id、姓名、学生年龄
      ........
  }
  ```

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

* 中间表

  ```mysql
  CREATE TABLE stu_cr(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	sid INT,
  	cid INT,
  	CONSTRAINT sc_fk1 FOREIGN KEY (sid) REFERENCES student(id),
  	CONSTRAINT sc_fk2 FOREIGN KEY (cid) REFERENCES course(id)
  );
  ```

* SQL查询语句

  ```mysql
  SELECT DISTINCT s.id,s.name,s.age FROM student s,stu_cr sc WHERE sc.sid=s.id
  SELECT c.id,c.name FROM stu_cr sc,course c WHERE sc.cid=c.id AND sc.sid=#{id}
  ```

* bean类

  ```java
  public class Student {
      private Integer id;     //主键id
      private String name;    //学生姓名
      private Integer age;    //学生年龄
      private List<Course> courses; //学生选择的课程  id、课程名
  }
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





## 构建SQL

### 基础语法

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



### 实现CRUD

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



### Spring

Spring是分层的 JavaSE/EE 应用 full-stack 轻量级开源框架

![](https://gitee.com/seazean/images/raw/master/Frame/Spring框架介绍.png)

Spring优点：

* 方便解耦，简化开发
* 方便集成各种框架
* 方便程序测试
* AOP编程难过的支持
* 声明式事务的支持
* 降低JavaEE API的使用难度

体系结构：

![](https://gitee.com/seazean/images/raw/master/Frame/Spring体系结构.png)



***



## IoC

### 基本概述

- IoC（Inversion Of Control）控制反转，Spring反向控制应用程序所需要使用的外部资源
- **Spring控制的资源全部放置在Spring容器中，该容器称为IoC容器**
- 官方网站：https://spring.io/ -->Projects --> spring-framework --> LEARN --> Reference Doc

![](https://gitee.com/seazean/images/raw/master/Frame/Spring_ioc介绍.png)



- 耦合（Coupling）：代码编写过程中所使用技术的结合紧密度，用于衡量软件中各个模块之间的互联程度
- 内聚（Cohesion）：代码编写过程中单个模块内部各组成部分间的联系，用于衡量软件中各个功能模块内部的功能联系
- 代码编写的目标：高内聚，低耦合。同一个模块内的各个元素之间要高度紧密，各个模块之间的相互依存度不紧密

![](https://gitee.com/seazean/images/raw/master/Frame/Spring发展历程.png)





***



### 入门项目

模拟三层架构中表现层调用业务层功能

- 表现层：UserApp模拟UserServlet（使用main方法模拟）

- 业务层：UserService

步骤：

1. 导入spring坐标（5.1.9.release）—pom.xml文件

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>5.1.9.RELEASE</version>
   </dependency>
   ```

2. 编写业务层与表现层（模拟）接口与实现类—service.UserService，service.impl.UserServiceImpl

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

4. 配置所需资源（Service）为spring控制的资源

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

标签：<bean>标签，<beans>的子标签

作用：定义spring中的资源，受此标签定义的资源将受到spring控制

格式：

```xml
<beans>
	<bean />
</beans>
```

基本属性

* id：bean的名称，通过id值获取bean (首字母小写)
* class：bean的类型，使用完全限定类名
* name：bean的名称，可以通过name值获取bean，用于多人配合时给bean起别名

```xml
<bean id="beanId" name="beanName1,beanName2" class="ClassName"></bean>
```

```java
ctx.getBean("beanId") == ctx.getBean("beanName1") == ctx.getBean("beanName2")
```



##### 作用范围

作用：定义bean的作用范围

格式：

```xml
<bean scope="singleton"></bean>
```

取值：

- singleton：设定创建出的对象保存在spring容器中，是一个单例的对象
- prototype：设定创建出的对象保存在spring容器中，是一个非单例的对象
- request、session、application、 websocket ：设定创建出的对象放置在web容器对应的位置



***



##### 生命周期

作用：定义bean对象在初始化或销毁时完成的工作

格式：

```xml
<bean init-method="init" destroy-method="destroy></bean>
```

取值：bean对应的类中对应的具体方法名

注意事项：

- 当scope=“singleton”时，spring容器中有且仅有一个对象，init方法在创建容器时仅执行一次
- 当scope=“prototype”时，spring容器要创建同一类型的多个对象，init方法在每个对象创建时均执行一次
- 当scope=“singleton”时，关闭容器 (.close()) 会导致bean实例的销毁，调用destroy方法一次
- 当scope=“prototype”时，对象的销毁由垃圾回收机制gc()控制，destroy方法将不会被执行

bean配置：

```xml
<!--init-method和destroy-method用于控制bean的生命周期-->
<bean id="userService3" scope="prototype" init-method="init" destroy-method="destroy" class="service.impl.UserServiceImpl"/>
```

业务层实现类：

```java
public class UserServiceImpl implements UserService {
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

* factory-bean

  作用：定义bean对象创建方式，使用静态工厂的形式创建bean，兼容早期遗留系统的升级工作

  格式：

  ```xml
  <bean class="FactoryClassName" factory-method="factoryMethodName"></bean>
  ```

  取值：工厂bean中用于获取对象的静态方法名

  注意事项：class属性必须配置成静态工厂的类名

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



* factory-bean，factory-method

  作用：定义bean对象创建方式，使用实例工厂的形式创建bean，兼容早期遗留系统的升级工作

  格式：

  ```xml
  <bean factory-bean="factoryBeanId" factory-method="factoryMethodName"></bean>
  ```

  取值：工厂bean中用于获取对象的实例方法名

  注意事项：

  - 使用实例工厂创建bean首先需要将实例工厂配置bean，交由spring进行管理

  - factory-bean是实例工厂的beanId

  bean配置：

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

- IoC（Inversion Of Control）控制翻转，Spring反向控制应用程序所需要使用的外部资源

- DI（Dependency Injection）依赖注入，应用程序运行依赖的资源由Spring为其提供，资源进入应用程序的方式称为注入。简单说就是利用反射机制为类的属性赋值的操作

  ![](https://gitee.com/seazean/images/raw/master/Frame/DI介绍.png)

IoC和DI的关系：IoC与DI是同一件事站在不同角度看待问题



***



##### set注入

标签：<property>标签，<bean>的子标签

作用：使用set方法的形式为bean提供资源

格式：

```xml
<bean>
	<property />
    <property />
    .....
</bean>
```

基本属性：

* name：对应bean中的属性名，要注入的变量名，要求该属性必须提供可访问的set方法
  			 (严格规范此名称是set方法对应名称，首字母必须小写)
* value：设定非引用类型属性对应的值，不能与ref同时使用
* ref：设定引用类型属性对应bean的id ，不能与value同时使用

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



##### 构造器注入

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

* Service业务层：参考set注入

* 配置applicationContext.xml

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

  方式二：使用UserServiceImpl的构造方法注入

  ```xml
  <bean id="userService" class="service.impl.UserServiceImpl">
  	<constructor-arg name="userDao" ref="userDao"/>
  	<constructor-arg name="num" value="666666"/>
  </bean>
  ```

* 测试类：参考set注入



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
              <prop key="name">itheima666</prop>
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
              <value>itheima</value>
              <value>66666</value>
          </set>
      </property>
      <property name="hm">
          <map>
              <entry key="name" value="itheima66666"/>
              <entry key="value" value="6666666666"/>
          </map>
      </property>
  </bean>
  ```

  

****



#### P

标签：<p:propertyName>，<p:propertyName-ref>

作用：为bean注入属性值

格式：

```xml
<bean p:propertyName="propertyValue" p:propertyName-ref="beanId"/>
```

开启p命令空间：开启spring对p命令空间的的支持，在beans标签中添加对应空间支持

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

Spring提供了对EL表达式的支持，统一属性注入格式

作用：为bean注入属性值

格式：

```xml
<property value="EL">
```

注意：所有属性值不区分是否引用类型，统一使用value赋值

所有格式统一使用  value=“#{}”

- 常量  #{10}  #{3.14}  #{2e5}  #{‘itcast’}

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

Spring提供了读取外部properties文件的机制，使用读取到的数据为bean的属性赋值

操作步骤：

1. 准备外部properties文件

2. 开启context命名空间支持

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

3. 加载指定的properties文件

   ```xml
   <context:property-placeholder location="classpath:data.properties" />
   ```

4. 使用加载的数据

   ```xml
   <property name="propertyName" value="${propertiesName}"/>
   ```

- 注意：如果需要加载所有的properties文件，可以使用`*.properties`表示加载所有的properties文件

- 注意：读取数据使用**${propertiesName}**格式进行，其中 propertiesName 指properties文件中的属性名

代码实现：

* data.properties

  ```properties
  username=root
  pwd=123456
  ```

* DAO层：注入的资源

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

* Service业务层

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
  
  <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
      <property name="num" value="2"/>
  </bean>
  ```

* 测试类

  ```java
  new ClassPathXmlApplicationContext("applicationContext-user.xml","applicationContext-book.xml");
  new ClassPathXmlApplicationContext("applicationContext.xml");
  ```

Spring容器中的bean定义冲突问题

- 同id的bean，后定义的覆盖先定义的

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





##### Mybatis

Mybatis核心配置文件消失

- 环境environment转换成数据源对象

- 映射Mapper扫描工作交由spring处理

- 类型别名交由spring处理

业务发起使用spring上下文对象获取对应的bean

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

注解：启动时使用注解的形式替代xml配置，将spring配置文件从工程中消除，简化书写

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

- 扫描过程仅读取合法的java文件

- 扫描时仅读取spring可识别的注解

- 扫描结束后会将可识别的有效注解转化为spring对应的资源加入IoC容器

注意：

- 无论是注解格式还是XML配置格式，最终都是将资源加载到IoC容器中，差别仅仅是数据读取方式不同
- 从加载效率上来说注解优于XML配置文件



##### 纯注解

注解配置类

名称：@Configuration、@ComponentScan

类型：类注解

作用：**设置当前类为spring核心配置加载类**

格式：

```java
@Configuration
@ComponentScan({"scanPackageName1","scanPackageName2"})
public class SpringConfigClassName{
}
```

说明：

- 核心配合类用于替换spring核心配置文件，此类可以设置空的，不设置变量与属性
- bean扫描工作使用注解@ComponentScan替代，多个包用`{}和,`隔开

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



##### 扫描器

组件扫描过滤器

开发过程中，需要根据需求加载必要的bean，排除指定bean

![](https://gitee.com/seazean/images/raw/master/Frame/组件扫描器.png)



名称：@ComponentScan

类型：**类注解**

作用：设置spring配置加载类扫描规则

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

名称：@Component    @Controller    @Service    @Repository

类型：类注解，写在类定义上方

作用：设置该类为spring管理的bean

格式：

```java
@Component
public class ClassName{}
```

说明：@Controller、@Service 、@Repository是@Component的衍生注解，功能同@Component

属性：

- value（默认）：定义bean的访问id



##### 作用范围

名称：@Scope

类型：类注解，写在类定义上方

作用：设置该类作为bean对应的scope属性

格式：

```java
@Scope
public class ClassName{}
```

相关属性

- value（默认）：定义bean的作用域，默认为singleton，非单例取值prototype



##### 生命周期

名称：@PostConstruct、@PreDestroy

类型：方法注解，写在方法定义上方

作用：设置该类作为bean对应的生命周期方法

示例：

```java
//定义bean，后面添加bean的id
@Component("userService")
//定义bean的作用域
@Scope("singleton")
public class UserServiceImpl implements UserService {
    //设定bean的生命周期
    @PostConstruct
    public void init(){
        System.out.println("user service init...");
    }

    @PreDestroy
    public void destroy(){
        System.out.println("user service destroy...");
    }
}
```

拓展方式：

* 实现InitializingBean，定义初始化逻辑
* 实现DisposableBean，定义销毁逻辑



##### 加载资源

名称：@Bean

类型：方法注解

作用：设置该方法的返回值作为spring管理的bean

格式：

```java
@Bean("dataSource")
public DruidDataSource createDataSource() {    return ……;    }
```

说明：

- 因为第三方bean无法在其源码上进行修改，使用@Bean解决第三方bean的引入问题

- 该注解用于替代XML配置中的静态工厂与实例工厂创建bean，不区分方法是否为静态或非静态

- @Bean所在的类必须被spring扫描加载，否则该注解无法生效

相关属性

- value（默认）：定义bean的访问id
- initMethod：声明初始化方法
- destroyMethod：声明销毁方法



***



#### 属性注入

##### 非引用类型

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

- value值仅支持非引用类型数据，赋值时对方法的所有参数全部赋值

- value值支持读取properties文件中的属性值，通过类属性将properties中数据传入类中

- value值支持SpEL

- @value注解如果添加在属性上方，可以省略set方法（set方法的目的是为属性赋值）

相关属性：

- value（默认）：定义对应的属性值或参数值



****



##### 引用类型

###### 属性引入

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

- @Autowired默认按类型装配，指定@Qualifier后可以指定自动装配的bean的id

相关属性：

- required：定义该属性是否允许为null

注意：
在使用@Autowired时，首先在容器中查询对应类型的bean，如果查询结果刚好为一个，就将该bean装配给@Autowired指定的数据，如果查询的结果不止一个，那么@Autowired会根据名称来查找。如果查询的结果为空，那么会抛出异常。解决方法：使用required=false



###### 属性设置

名称：@Primary

类型：类注解

作用：设置类对应的bean按类型装配时优先装配

范例：

```java
@Primary
public class ClassName{}
```

说明：

- @Autowired默认按类型装配，当出现相同类型的bean，使用@Primary提高按类型自动装配的优先级，多个@Primary会导致优先级设置无效





###### 拓展

名称：@Inject、@Named、@Resource

说明：

- @Inject与@Named是JSR330规范中的注解，功能与@Autowired和@Qualifier完全相同，适用于不同架构场景
- @Resource是JSR250规范中的注解，可以简化书写格式

@Resource相关属性

- name：设置注入的bean的id

- type：设置注入的bean的类型，接收的参数为Class类型



注：**@Autowired和@Resource之间的区别**：

*  @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）

* @Resource默认按照名称来装配注入，只有当找不到与名称匹配的bean才会按照类型来装配注入
  



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

- 作用：控制bean的加载顺序，使其在指定bean加载完毕后再加载

- 格式：

  ```java
  @DependsOn("beanId")
  public class ClassName {
  }
  ```

- 说明：

  - 配置在方法上，使@DependsOn指定的bean优先于@Bean配置的bean进行加载

  - 配置在类上，使@DependsOn指定的bean优先于当前类中所有@Bean配置的bean进行加载

  - 配置在类上，使@DependsOn指定的bean优先于@Component等配置的bean进行加载

- 相关属性

  - value（默认）：设置当前bean所依赖的bean的id

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



##### 导入Druid

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



### 整合框架

#### Mybatis

**原理**：DAO接口不需要去创建实现类，因为MyBatis-Spring提供了一个动态代理的实现**MapperFactoryBean**，这个类可以让你直接注入数据映射器接口到service层 bean 中，底层将会为你创建JDK代理

![](https://gitee.com/seazean/images/raw/master/Frame/IoC注解整合MyBatis图解.png)

* pom.xml

  ```xml
  <dependencies>
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
          <groupId>org.springframework</groupId>
          <artifactId>spring-context</artifactId>
          <version>5.1.9.RELEASE</version>
      </dependency>
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-jdbc</artifactId>
          <version>5.1.9.RELEASE</version>
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
  </dependencies>
  ```

* java / domain

  ```java
  public class Account implements Serializable {
      private Integer id;
      private String name;
      private Double money;
      .....
  }
  ```

* java / dao / AccountDao

  ```java
  public interface AccountDao {
      @Insert("insert into account(name,money) values(#{name},#{money})")
      void save(Account account);
  
      @Delete("delete from account where id = #{id} ")
      void delete(Integer id);
  
      @Update("update account set name = #{name},money = #{money} where id=#{id}")
      void update(Account account);
  
      @Select("select * from account")
      List<Account> findAll();
  
      @Select("select * from account where id = #{id} ")
      Account findById(Integer id);
  }
  ```

* java / service 

  ```java
  public interface AccountService {
      void save(Account account);
      void delete(Integer id);
      void update(Account account);
      List<Account> findAll();
      Account findById(Integer id);
  }
  ```

  ```java
  @Service("accountService")  
  //@Component("accountService")
  public class AccountServiceImpl implements AccountService {
      @Autowired //自动装配
      private AccountDao accountDao;
  
      public void save(Account account) {
          accountDao.save(account);
      }
  
      public void update(Account account){
          accountDao.update(account);
      }
  
      public void delete(Integer id) {
          accountDao.delete(id);
      }
  
      public Account findById(Integer id) {
          return accountDao.findById(id);
      }
  
      public List<Account> findAll() {
          return accountDao.findAll();
      }
  }
  ```

* resources / jdbc.properties

  ```properties
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://192.168.2.185:3306/spring_db?useSSL=false
  jdbc.username=root
  jdbc.password=123456
  ```

* java / config / SpringConfig   JDBCConfig  MyBatisConfig  

  ```java
  @Configuration
  @ComponentScan({"config","dao","domain","service"})
  @PropertySource("classpath:jdbc.properties")
  @Import({JDBCConfig.class, MyBatisConfig.class})
  public class SpringConfig {
  }
  ```

  ```java
  public class JDBCConfig {
      @Value("${jdbc.driver}")
      private String driver;
      @Value("${jdbc.url}")
      private String url;
      @Value("${jdbc.username}")
      private String userName;
      @Value("${jdbc.password}")
      private String password;
  
      @Bean("dataSource")
      public DataSource getDataSource(){
          DruidDataSource ds = new DruidDataSource();
          ds.setDriverClassName(driver);
          ds.setUrl(url);
          ds.setUsername(userName);
          ds.setPassword(password);
          return ds;
      }
  }
  ```

  ```java
  public class MyBatisConfig {
      @Bean 							    //DataSource使用自动装配
      public SqlSessionFactoryBean getSSFB(@Autowired DataSource dataSource){
          SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
          ssfb.setTypeAliasesPackage("domain");
          ssfb.setDataSource(dataSource);
          return ssfb;
      }
      @Bean
      public MapperScannerConfigurer getMapperScannerConfigurer(){
          MapperScannerConfigurer msc = new MapperScannerConfigurer();
          msc.setBasePackage("dao");
          return msc;
      }
  
  }
  ```

* 测试类

  ```java
  public class App {
      public static void main(String[] args) {
          ApplicationContext ctx = new AnnotationConfigApplicationContext(
              										SpringConfig.class);
          AccountService as = (AccountService) ctx.getBean("accountService");
          Account account = as.findById(3);
          System.out.println(account);
      }
  }
  ```

  



***



#### Junit

Spring接管Junit的运行权，使用Spring专用的Junit类加载器，为Junit测试用例设定对应的spring容器

注意：

- 从Spring5.0以后，要求Junit的版本必须是4.12及以上

- Junit仅用于单元测试，不能将Junit的测试类配置成spring的bean，否则该配置将会被打包进入工程中 

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

ApplicationContext：

1. ApplicationContext是一个接口，提供了访问spring容器的API

2. ClassPathXmlApplicationContext是一个类，实现了上述功能

3. ApplicationContext的顶层接口是BeanFactory

4. BeanFactory定义了bean相关的最基本操作

5. ApplicationContext在BeanFactory基础上追加了若干新功能

**ApplicationContext和BeanFactory对比：**

* BeanFactory和ApplicationContext是Spring的两大核心接口，都可以当做Spring的容器
* BeanFactory是Spring里面最底层的接口，是IoC的核心，定义了IoC的基本功能，包含了各种Bean的定义、加载、实例化，依赖注入和生命周期管理。ApplicationContext接口作为BeanFactory的子类，除了提供BeanFactory所具有的功能外，还提供了更完整的框架功能：

  * 继承MessageSource，因此支持国际化
  * 资源文件访问，如URL和文件（ResourceLoader）。
  * 载入多个（有继承关系）上下文（即同时加载多个配置文件） ，使得每一个上下文都专注于一个特定的层次，比如应用的web层
  * 提供在监听器中注册bean的事件
* BeanFactory创建的bean采用延迟加载形式，只有在使用到某个Bean时(调用getBean())，才对该Bean进行加载实例化（Spring早期使用该方法获取bean），这样就不能提前发现一些存在的Spring的配置问题；ApplicationContext是在容器启动时，一次性创建了所有的Bean，容器启动时，就可以发现Spring中存在的配置错误，这样有利于检查所依赖属性是否注入
* ApplicationContext启动后预载入所有的单实例Bean，所以程序启动慢，运行时速度快
* 两者都支持BeanPostProcessor、BeanFactoryPostProcessor的使用，但两者之间的区别是：BeanFactory需要手动注册，而ApplicationContext则是自动注册



FileSystemXmlApplicationContext：

* 加载文件系统中任意位置的配置文件，而ClassPathXmlApplicationContext只能加载类路径下的配置文件

![](https://gitee.com/seazean/images/raw/master/Frame/ApplicationContext层级结构图.png)

**BeanFactory的基本使用**：

```java
String FACTORY_BEAN_PREFIX = "&";//获取工厂Bean本身，在Bean的id前加此符号
```

```java
Resource res = new ClassPathResource("applicationContext.xml");
BeanFactory bf = new XmlBeanFactory(res);
UserService userService = (UserService)bf.getBean("userService");
```



***



#### 过滤器

##### 数据准备

* DAO层 UserDao、AccountDao、BookDao、EquipmentDao

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

* Service业务层

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

- bean只有通过配置才可以进入spring容器，被spring加载并控制

- 配置bean的方式如下：

  - XML文件中使用<bean/>标签配置

  - 使用@Component及衍生注解配置

- **快速高效导入大量bean的方式，替代@Import({a.class,b.class})，无需在每个类上添加@Bean**

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

可以取代ComponentScan扫描器

名称：ImportBeanDefinitionRegistrar

类型：**接口**

作用：自定义bean定义注册器

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

BeanPostProcessor：bean后置处理器，bean创建对象初始化前后进行拦截工作的

BeanFactoryPostProcessor：beanFactory的后置处理器

 * 		加载时机：在BeanFactory初始化之后调用，来定制和修改BeanFactory的内容；所有的bean定义已经保存加载到beanFactory，但是bean的实例还未创建

* 		执行流程：
    * ioc容器创建对象
    * invokeBeanFactoryPostProcessors(beanFactory)：执行BeanFactoryPostProcessor
      * 在BeanFactory中找到所有类型是BeanFactoryPostProcessor的组件，并执行它们的方法
      * 在初始化创建其他组件前面执行

BeanDefinitionRegistryPostProcessor：

* 加载时机：在所有bean定义信息将要被加载，但是bean实例还未创建，优先于BeanFactoryPostProcessor执行；利用BeanDefinitionRegistryPostProcessor给容器中再额外添加一些组件

* 执行流程：
  * ioc容器创建对象
  * refresh() --> invokeBeanFactoryPostProcessors(beanFactory)
  * 从容器中获取到所有的BeanDefinitionRegistryPostProcessor组件
    * 依次触发所有的postProcessBeanDefinitionRegistry()方法
    * 再来触发postProcessBeanFactory()方法
  * 从容器中找到BeanFactoryPostProcessor组件；然后依次触发postProcessBeanFactory()方法





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





##### 监听器原理

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

   * 遍历ApplicationListener

     * 如果有Executor，可以使用Executor异步派发；Executor executor = getTaskExecutor()
     * 反之，同步的方式直接执行listener方法；invokeListener(listener, event)，拿到listener回调onApplicationEvent方法

2. 自己发布事件

3. 容器关闭会发布ContextClosedEvent



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

原理：使用EventListenerMethodProcessor处理器来解析方法上的@EventListener，Spring扫描使用注解的方法，并为之创建一个监听对象

SmartInitializingSingleton 原理：-> afterSingletonsInstantiated()

 *   		ioc容器创建对象并refresh()
 *   		finishBeanFactoryInitialization(beanFactory：初始化剩下的单实例bean
        *   		先创建所有的单实例bean：getBean()
        *   		获取所有创建好的单实例bean，判断是否是SmartInitializingSingleton类型的，如果是就调用afterSingletonsInstantiated()



***



#### FactoryBean

繁琐的bean初始化过程处理：

* FactoryBean：对单一的bean的初始化过程进行封装，达到简化配置的目的

FactoryBean与BeanFactory区别：

- FactoryBean：封装单个bean的创建过程

- BeanFactory：Spring容器顶层接口，定义了bean相关的获取操作

代码实现：

* FactoryBean，实现类一般是SqlSessionFactoryBean

  ```java
  public class EquipmentDaoImplFactoryBean implements FactoryBean {
      @Override
      public Object getObject() th  rows Exception {
          return new EquipmentDaoImpl();
      }
      @Override
      public Class<?> getObjectType() {
          return null;
      }
      @Override
      public boolean isSingleton() {
          return false;
      }
  }
  ```

  

  

***



## AOP

### 基本概述

AOP(Aspect Oriented Programing)：面向切面编程，一种编程**范式**，指导开发者如何组织程序结构

AOP弥补了OOP的不足，基于OOP基础之上进行横向开发：

- uOOP规定程序开发以类为主体模型，一切围绕对象进行，完成某个任务先构建模型

- uAOP程序开发主要关注基于OOP开发中的共性功能，一切围绕共性功能进行，完成某个任务先构建可能遇到的所有共性功能（当所有功能都开发出来也就没有共性与非共性之分），将软件开发由手工制作走向半自动化/全自动化阶段，实现“插拔式组件体系结构”搭建

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

- Joinpoint(连接点)：就是方法

- Pointcut(切入点)：就是挖掉共性功能的方法

- Advice(通知)：就是共性功能，最终以一个方法的形式呈现

- Aspect(切面)：就是共性功能与挖的位置的对应关系

- Target(目标对象)：就是挖掉功能的方法对应的类产生的对象，这种对象是无法直接完成最终工作的

- Weaving(织入)：就是将挖掉的功能回填的动态过程

- Proxy(代理)：目标对象无法直接完成工作，需要对其进行功能回填，通过创建原始对象的代理对象实现

- Introduction(引入/引介) ：就是对原始对象无中生有的添加成员变量或成员方法

![](https://gitee.com/seazean/images/raw/master/Frame/AOP连接点.png)

![](https://gitee.com/seazean/images/raw/master/Frame/AOP切入点切面通知.png)

![](https://gitee.com/seazean/images/raw/master/Frame/AOP织入.png)



***



#### 入门项目

开发步骤：

- 开发阶段

  - 制作程序

  - 将非共性功能开发到对应的目标对象类中，并制作成切入点方法

  - 将共性功能独立开发出来，制作成**通知**

  - 在配置文件中，声明**切入点**

  - 在配置文件中，声明**切入点**与**通知**间的关系（含**通知类型**），即**切面**

- 运行阶段(AOP完成)

  - Spring容器加载配置文件，监控所有配置的**切入点**方法的执行

  - 当监控到切入点方法被运行，使用**代理**机制，动态创建**目标对象**的**代理对象**，根据**通知类别**，在**代理对象**的对应位置将通知对应的功能**织入**，完成完整的代码逻辑并运行

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
   public class AOPAdvice{
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
           userService.save();//先输出共性功能，然后user service running...
       }
   }
   ```



***



### XML开发

#### AspectJ

Aspect（切面）用于描述切入点与通知间的关系，是AOP编程中的一个概念

AspectJ是基于java语言对Aspect的实现



#### AOP

##### config

标签：<aop:config>，<beans>的子标签

作用：设置AOP

格式：

```xml
<beans>
    <aop:config>……</aop:config>
    <aop:config>……</aop:config>
    <!--一个beans标签中可以配置多个aop:config标签-->
</beans>
```



##### pointcut

标签：<aop:pointcut>，归属于aop:config标签和aop:aspect标签

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

* 一个aop:config标签中可以配置多个aop:pointcut标签，且该标签可以配置在aop:aspect标签内

属性：

- id ：识别切入点的名称

- expression ：切入点表达式



##### aspect

标签：<aop:aspect>，aop:config的子标签

作用：设置具体的AOP通知对应的切入点（切面）

格式：

```xml
<aop:config>
    <aop:aspect ref="beanId">……</aop:aspect>
    <aop:aspect ref="beanId">……</aop:aspect>
    <!--一个aop:config标签中可以配置多个aop:aspect标签-->
</aop:config>
```

属性：

- ref ：通知所在的bean的id



***



#### Pointcut

##### 切入点

切入点描述的是某个方法

切入点表达式是一个快速匹配方法描述的通配格式，类似于正则表达式



##### 表达式

格式：

```xml
关键字(访问修饰符  返回值  包名.类名.方法名(参数)异常名)
```

示例：

```java
execution(public User service.UserService.findById(int))
//匹配UserService中只含有一个参数的findById方法
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
  execution(public * com.itheima.*.UserService.find*(*)
  //匹配com.itheima包下的任意包中的UserService类或接口中所有find开头的带有一个任意参数的方法
  ```

* .. ：多个连续的任意符号，可以独立出现，常用于简化包名与参数

  ```java
  execution(public User com..UserService.findById(..))
  //匹配com包下的任意包中的UserService类或接口中所有名称为findById参数任意数量和类型的方法
  ```

* +：专用于匹配子类类型

  ```java
  execution(* *..*Service+.*(..))
  //匹配任意包下的Service结尾的类或者接口的子类或者实现类
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
execution(public void com.itheima.service.*.*(..))
execution(public void com.itheima.service.User*.*(..))
execution(public void com.itheima.service.*Service.*(..))
execution(public void com.itheima.service.UserService.*(..))
execution(public User com.itheima.service.UserService.find*(..))
execution(public User com.itheima.service.UserService.*Id(..))
execution(public User com.itheima.service.UserService.findById(..))
execution(public User com.itheima.service.UserService.findById(int))
execution(public User com.itheima.service.UserService.findById(int,int))
execution(public User com.itheima.service.UserService.findById(int,*))
execution(public User com.itheima.service.UserService.findById())
execution(List com.itheima.service.*Service+.findAll(..))
```



##### 配置方式

XML配置规则：

- 企业开发命名规范严格遵循规范文档进行

- 先为方法配置局部切入点，再抽取类中公共切入点，最后抽取全局切入点

- 代码走查过程中检测切入点是否存在越界性包含

- 代码走查过程中检测切入点是否存在非包含性进驻

- 设定AOP执行检测程序，在单元测试中监控通知被执行次数与预计次数是否匹配（不绝对正确：加进一个不该加的，删去一个不该删的相当于结果不变）

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

AOP注解简化xml：

![](https://gitee.com/seazean/images/raw/master/Frame/AOP注解开发.png)

注意事项：

1. 切入点最终体现为一个方法，无参无返回值，无实际方法体内容，但不能是抽象方法

2. 引用切入点时必须使用方法调用名称，方法后面的（）不能省略

3. 切面类中定义的切入点只能在当前类中使用，如果想引用其他类中定义的切入点使用“类名.方法名()”引用

4. 可以在通知类型注解后添加参数，实现XML配置中的属性，例如after-returning后的returning属性





****



#### 启动注解

##### XML

开启AOP注解支持：

```xml
<aop:aspectj-autoproxy/>
<context:component-scan base-package="aop,config,service"/><!--启动Spring扫描-->
```

开发步骤：

1. 导入坐标（伴随spring-context坐标导入已经依赖导入完成）
2. 开启AOP注解支持
3. 配置切面@Aspect
4. 定义专用的切入点方法，并配置切入点@Pointcut
5. 为通知方法配置通知类型及对应切入点@Before



##### 纯注解

注解：@EnableAspectJAutoProxy

位置：Spring注解配置类定义上方

作用：设置当前类开启AOP注解驱动的支持，加载AOP注解

格式：

```java
@Configuration
@ComponentScan("com.itheima")
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

- 前缀为固定字符串，例如baidu、itheima等，无实际意义

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

JDKProxy动态代理是针对对象做代理，要求原始对象具有接口实现，并对接口方法进行增强，因为代理类继承Proxy

静态代理和动态代理的区别：

* 静态代理是在编译时就已经将接口、代理类、被代理类的字节码文件确定下来
* 动态代理是程序在运行后通过反射创建字节码文件交由JVM加载

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

CGLIB(Code Generation Library)：Code生成类库 

CGLIB特点：

* CGLIB动态代理**不限定**是否具有接口，可以对任意操作进行增强
* CGLIB动态代理无需要原始被代理对象，动态创建出新的代理对象
* CGLIB**继承被代理类**，如果代理类是final则不能实现

![](https://gitee.com/seazean/images/raw/master/Frame/AOP底层原理-cglib.png)

* cglib类

  * JDKProxy仅对接口方法做增强，cglib对所有方法做增强，包括Object类中的方法 (toString hashCode)，需要对方法进行判断是否是save，来选择性增强
  * 返回值类型采用多态向下转型，所以需要设置父类类型

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

Spirng可以通过配置的形式控制使用的代理形式，Spring会先判断是否实现了接口，如果实现了接口就使用JDK动态代理，如果没有实现接口则使用Cglib动态代理，通过配置可以修改为使用cglib

- XML配置

  ```xml
  <!--XML配置AOP-->
  <aop:config proxy-target-class="false"></aop:config>
  ```

- XML注解支持

  ```xml
  <!--注解配置AOP-->
  <aop:aspectj-autoproxy proxy-target-class="false"/>
  ```

- 注解驱动

  ```java
  //修改为使用cglib创建代理对象
  @EnableAspectJAutoProxy(proxyTargetClass = true)
  ```

* JDK动态代理和Cglib动态代理的区别：

  * JDK动态代理只能对实现了接口的类生成代理，没有实现接口的类不能使用。
  * Cglib动态代理即使被代理的类没有实现接口也可以使用，因为Cglib动态代理是使用继承被代理类的方式进行扩展
  * Cglib动态代理是通过继承的方式，覆盖被代理类的方法来进行代理，所以如果方法是被final修饰的话，就不能进行代理。
    





***



#### 织入时机

![AOP织入时机](https://gitee.com/seazean/images/raw/master/Frame/AOP织入时机.png)





***





## 事务

### 基本概念

#### 事务介绍

事务：数据库中多个操作合并在一起形成的操作序列

Spring事务一般加到业务层，对应着业务的操作，数据层有自己默认的隔离界别

作用：

* 当数据库操作序列中个别操作失败时，提供一种方式使数据库状态恢复到正常状态（**A**），保障数据库即使在异常状态下仍能保持数据一致性（**C**）（要么操作前状态，要么操作后状态）
* 当出现并发访问数据库时，在多个访问间进行相互隔离，防止并发访问操作结果互相干扰（**I**）

事务特征（ACID）：

**程序是否支持事务首先取决于数据库 ，比如使用 MySQL ，如果选择的是 innodb 引擎，那么是可以支持事务的。但是，如果MySQL使用的是 myisam 引擎的话，那从根上就是不支持事务的**

**保证原子性**：

* 要保证事务的原子性，就需要在异常发生时，对已经执行的操作进行**回滚**
* 在 MySQL 中，恢复机制是通过**回滚日志（undo log）** 实现，所有事务进行的修改都会先先记录到这个回滚日志中，然后再执行相关的操作。如果执行过程中遇到异常的话，直接利用**回滚日志**中的信息将数据回滚到修改之前的样子即可
* 回滚日志会先于数据持久化到磁盘上，这样保证了即使遇到数据库突然宕机等情况，当用户再次启动数据库的时候，数据库还能够通过查询回滚日志来回滚将之前未完成的事务



***



#### 隔离级别

TransactionDefinition 接口中定义了五个表示隔离级别的常量：

- **TransactionDefinition.ISOLATION_DEFAULT**：使用后端数据库默认的隔离级别，Mysql 默认采用的 REPEATABLE_READ隔离级别 Oracle 默认采用的 READ_COMMITTED隔离级别.
- **TransactionDefinition.ISOLATION_READ_UNCOMMITTED**：最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读
- **TransactionDefinition.ISOLATION_READ_COMMITTED**：允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生
- **TransactionDefinition.ISOLATION_REPEATABLE_READ**：对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
- **TransactionDefinition.ISOLATION_SERIALIZABLE**：最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。但是这将严重影响程序的性能。通常情况下也不会用到该级别

MySQL InnoDB 存储引擎的默认支持的隔离级别是 **REPEATABLE-READ（可重读）**

**分布式事务**：允许多个独立的事务资源（transactional resources）参与到一个全局的事务中
事务资源通常是关系型数据库系统，但也可以是其他类型的资源。全局事务要求在其中的所有参与的事务要么都提交，要么都回滚，这对于事务原有的ACID要求又有了提高

在使用分布式事务时，InnoDB存储引擎的事务隔离级别必须设置为SERIALIZABLE



****



#### 传播行为

事务传播行为描述的是事务协调员对事务管理员所携带事务的处理态度

![](https://gitee.com/seazean/images/raw/master/Frame/事务传播行为.png)

**事务传播行为是为了解决业务层方法之间互相调用的事务问题**：

* 当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。

* 例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行

  ```java
  //A 类的aMethod（）方法中调用了 B 类的 bMethod() 方法
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

**支持当前事务的情况：**

* TransactionDefinition.PROPAGATION_REQUIRED： 如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务
* TransactionDefinition.PROPAGATION_SUPPORTS： 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行
* TransactionDefinition.PROPAGATION_MANDATORY： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常（mandatory：强制性）

**不支持当前事务的情况：**

- TransactionDefinition.PROPAGATION_REQUIRES_NEW： 创建一个新的事务，如果当前存在事务，则把当前事务挂起
- TransactionDefinition.PROPAGATION_NOT_SUPPORTED： 以非事务方式运行，如果当前存在事务，则把当前事务挂起
- TransactionDefinition.PROPAGATION_NEVER： 以非事务方式运行，如果当前存在事务，则抛出异常

**其他情况：**

* **TransactionDefinition.PROPAGATION_NESTED：** 如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED 



****



#### 超时属性

事务超时，就是指一个事务所允许执行的最长时间，如果超过该时间限制但事务还没有完成，则自动回滚事务。在 `TransactionDefinition` 中以 int 的值来表示超时时间，其单位是秒，默认值为-1



***



#### 只读属性

对于只有读取数据查询的事务，可以指定事务类型为 readonly，即只读事务；只读事务不涉及数据的修改，数据库会提供一些优化手段，适合用在有多条数据库查询操作的方法中

读操作为什么需要启用事务支持：

* MySQL 默认对每一个新建立的连接都启用了`autocommit`模式，在该模式下，每一个发送到 MySQL 服务器的`sql`语句都会在一个**单独**的事务中进行处理，执行结束后会自动提交事务，并开启一个新的事务
* 执行多条查询语句，如果方法加上了`Transactional`注解，这个方法执行的所有`sql`会被放在一个事务中，如果声明了只读事务的话，数据库就会去优化它的执行，并不会带来其他的收益。如果不加`Transactional`，每条`sql`会开启一个单独的事务，中间被其它事务修改了数据，比如在前条 SQL 查询之后，后条 SQL 查询之前，数据被其他用户改变，则这次整体的统计查询将会出**现读数据不一致的状态**



***



#### 回滚规则

默认情况下，事务只有遇到运行期异常（RuntimeException 的子类）时才会回滚，Error 也会导致事务回滚，但是，在遇到检查型（Checked）异常时不会回滚

可以自定义定哪些异常会导致事务回滚而哪些不会：

```java
@Transactional(rollbackFor= MyException.class)
//回滚定义的特定的异常类型的
//noRollbackFor设置遇到哪些错误不需要回滚
```

声明式事务部分详解注解



***



### 核心对象

#### 事务对象

J2EE开发使用分层设计的思想进行，对于简单的业务层转调数据层的单一操作，事务开启在业务层或者数据层并无太大差别，当业务中包含多个数据层的调用时，需要在业务层开启事务，对数据层中多个操作进行组合并归属于同一个事务进行处理

Spring为业务层提供了整套的事务解决方案：

- PlatformTransactionManager
- TransactionDefinition

- TransactionStatus



***



#### PTM

PlatformTransactionManager，平台事务管理器实现类：

- DataSourceTransactionManager  适用于Spring JDBC或MyBatis

- HibernateTransactionManager  适用于Hibernate3.0及以上版本

- JpaTransactionManager  适用于JPA

- JdoTransactionManager  适用于JDO

- JtaTransactionManager  适用于JTA

管理器：

- JPA（Java Persistence API）Java EE 标准之一，为POJO提供持久化标准规范，并规范了持久化开发的统一API，符合JPA规范的开发可以在不同的JPA框架下运行

  **非持久化一个字段**：

  ```java
  static String transient1; // not persistent because of static
  final String transient2 = “Satish”; // not persistent because of final
  transient String transient3; // not persistent because of transient
  @Transient
  String transient4; // not persistent because of @Transient
  ```

- JDO(Java Data Object )是Java对象持久化规范，用于存取某种数据库中的对象，并提供标准化API。与JDBC相比，JDBC仅针对关系数据库进行操作，JDO可以扩展到关系数据库、文件、XML、对象数据库（ODBMS）等，可移植性更强

- JTA（Java Transaction API）Java EE 标准之一，允许应用程序执行分布式事务处理。与JDBC相比，JDBC事务则被限定在一个单一的数据库连接，而一个JTA事务可以有多个参与者，比如JDBC连接、JDO 都可以参与到一个JTA事务中

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



### 编程式事务

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

* 修改业务层实现提供转账操作：AccountServiceImpl

  ```java
  public void transfer(String outName,String inName,Double money){
      //1.创建事务管理器
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

* 将业务层的事务处理功能抽取出来制作成AOP通知，利用环绕通知运行期动态织入

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

* 配置applicationContext.xml，要开启AOP空间

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



### 声明式事务

#### XML

##### tx使用

* 删除TxAdvice通知类，开启tx命名空间，配置applicationContext.xml

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

* aop:advice与aop:advisor区别
  * aop:advice配置的通知类可以是普通java对象，不实现接口，也不使用继承关系
  * aop:advisor配置的通知类必须实现通知接口，底层invoke调用

    - MethodBeforeAdvice

    - AfterReturningAdvice

    - ThrowsAdvice

    - ……

方法调用：`AbstractAspectJAdvice#invokeAdviceMethod(org.aspectj.weaver.tools.JoinPointMatch, java.lang.Object, java.lang.Throwable)`



***



##### tx配置

###### advice

标签：tx:advice，beans的子标签

作用：专用于声明事务通知

格式：

```xml
<beans>
    <tx:advice id="txAdvice" transaction-manager="txManager">
    </tx:advice>
</beans>
```

基本属性：

- id：用于配置aop时指定通知器的id
- transaction-manager：指定事务管理器bean



###### attributes

类型：tx:attributes，tx:advice的子标签

作用：定义通知属性

格式：

```xml
<tx:advice id="txAdvice" transaction-manager="txManager">
    <tx:attributes>
    </tx:attributes>
</tx:advice>
```



###### method

标签：tx:method，tx:attribute的子标签

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

说明：通常事务属性会配置多个，包含1个读写的全事务属性，1个只读的查询类事务属性

属性：

* name：待添加事务的方法名表达式（支持*通配符）
* read-only：设置事务的读写属性，true为只读，false为读写
* timeout：设置事务的超时时长，单位秒，-1为无限长
* isolation：设置事务的隔离界别，该隔离级设定是基于Spring的设定，非数据库端
* no-rollback-for：设置事务中不回滚的异常，多个异常使用`,`分隔
* rollback-for：设置事务中必回滚的异常，多个异常使用`,`分隔
* propagation：设置事务的传播行为



***



#### 注解

##### 开启注解

###### XML

标签：tx:annotation-driven

归属：beans标签

作用：开启事务注解驱动，并指定对应的事务管理器

范例：

```xml
<tx:annotation-driven transaction-manager="txManager"/>
```



###### 纯注解

名称：@EnableTransactionManagement

类型：类注解，Spring注解配置类上方

作用：开启注解驱动，等同XML格式中的注解驱动

范例：

```java
@Configuration
@ComponentScan("com.itheima")
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
public interface AccountService{} 
```

说明：

* 接口上配置总体的，方法中配置具体的
* `@Transactional` 注解只有作用到 public 方法上事务才生效，不推荐在接口上使用；
* 避免同一个类中调用 `@Transactional` 注解的方法，这样会导致事务失效；
* 正确的设置 `@Transactional` 的 rollbackFor 和 propagation 属性，否则事务可能会回滚失败



***



##### 使用注解

* Dao层

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
  //对当前接口的所有方法添加事务
  @Transactional(isolation = Isolation.DEFAULT)
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

* 添加文件Spring.config、Mybatis.config、JDBCConfig (参考ioc_Mybatis)、TransactionManagerConfig

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

* Spring模板对象：
  * TransactionTemplate
  * JdbcTemplate

  * RedisTemplate

  * RabbitTemplate

  * JmsTemplate

  * HibernateTemplate

  * RestTemplate

* JdbcTemplate：提供标准的sql语句操作API
* NamedParameterJdbcTemplate：提供标准的具名sql语句操作API

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



### 底层原理

TransactionManagementConfigurationSelector类：

* 导入AutoProxyRegistrar组件和ProxyTransactionManagementConfiguration组件

* AutoProxyRegistrar：利用后置处理器机制在对象创建以后包装对象，返回一个代理对象（增强器），代理对象执行方法利用拦截器链进行调用，通过@Transactional作为方法拦截的标记，把有事务管理的类作为目标类，生成代理对象，然后增强@Transactional标记的方法，在使用目标方法的时候，从IOC容器中获取的其实是被增强的代理类，且事务方法会被代理，跟AOP原理一样

* ProxyTransactionManagementConfiguration：向IOC容器中导入事务增强器(BeanFactoryTransactionAttributeSourceAdvisor)，事务注解@Transactional的解析器(AnnotationTransactionAttributeSource)和事务方法拦截器(TransactionInterceptor)
  

通过AOP动态织入，进行事务开启和提交

事务底层原理解析：策略模式

策略模式（Strategy Pattern）**使用不同策略的对象实现不同的行为方式**，策略对象的变化导致行为的变化，每个事务对应一个新的connection对象

![](https://gitee.com/seazean/images/raw/master/Frame/Spring-事务底层原理策略模式.png)





***



## 原理

### 容器

Spring ioc容器就是很多Map集合，保存了单实例Bean，环境信息等资源

* **BeanDefinition**：是Spring中极其重要的一个概念，它存储了 bean 对象的所有特征信息，如是否单例、是否懒加载、factoryBeanName等
* **BeanDefinationRegistry**：存放BeanDefination的容器，是一种键值对的形式，通过特定的Bean定义的id，映射到相应的BeanDefination

* **BeanDefinitionReader**：读取配置文件，比如xml用dom4j解析，配置文件用io流

Spring容器的启动流程：三个步骤

ClassPathXmlApplicationContext同AnnotationConfigApplicationContext

```java
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
    this();// 注册 Spring 内置后置处理器的 BeanDefinition 到容器
    register(annotatedClasses);// 注册配置类 BeanDefinition 到容器
    refresh();// 加载或者刷新容器中的Bean
}
```

1. Spring容器的初始化时，通过this()调用了无参构造函数

   `this.reader = new AnnotatedBeanDefinitionReader(this)`：实例化BeanDefinitionReader注解配置读取器，用于对特定注解（如@Service、@Repository）的类进行读取转化成BeanDefinition对象

   * `AnnotationConfigUtils.registerAnnotationConfigProcessors`：向容器添加内置组件
   * ConfigurationClassPostProcessor：beanFactory 后置处理器，用来完成 bean 的扫描与注入工作AutowiredAnnotationBeanPostProcessor：bean 后置处理器，用来完成@AutoWired自动注入
     BeanPostProcessor很多相关的后置处理器

   `this.scanner = new ClassPathBeanDefinitionScanner(this)`：实例化路径扫描器，用于对指定的包目录进行扫描查找bean对象

2. 解析传入的 Spring 配置类，构造成一个 BeanDefinition 并注册到容器

   * 解析传入的配置类，也可以解析bean对象
   * 根据类上有没有 @Conditional 注解判断是否需要跳过
   * 处理类上的通用注解：Lazy、Primary、DependsOn等
   * 封装成一个 BeanDefinitionHolder，并注册到容器

3. refresh()容器刷新流程：`AbstractApplicationContext.refresh()`

   1. **prepareRefresh()**：刷新前的预处理
      * initPropertySources()：初始化一些属性设置，子类自定义个性化的属性设置方法
      * getEnvironment().validateRequiredProperties()：检验属性的合法等
      * earlyApplicationEvents= new LinkedHashSet<ApplicationEvent>()：保存容器中早期的事件

   2. **obtainFreshBeanFactory()**：获取在容器初始化时创建的BeanFactory

      * refreshBeanFactory()：创建BeanFactory，设置序列化ID、读取BeanDefinition并加载到工厂
      * getBeanFactory()：返回创建的DefaultListableBeanFactory对象    
   3. **prepareBeanFactory(beanFactory)**：BeanFactory的预准备工作，向容器中添加一些组件
      * 设置BeanFactory的类加载器、设置表达式解析器、属性编辑器
      * 添加BeanPostProcessor：ApplicationContextAwareProcessor
      * 设置忽略自动装配的接口，注册可以解析的自动装配类，即是否在任意组件中通过注解自动注入
      * 添加BeanPostProcessor：ApplicationListenerDetector监听器添加编译时的AspectJ，给BeanFactory中注册的3个组件

   4. **postProcessBeanFactory(beanFactory)**：BeanFactory准备工作完成后进行的后置处理工作，
      子类通过重写这个方法来在BeanFactory创建并预准备完成以后做进一步的设置

     * 以上是BeanFactory的创建及预准备工作

   5. **invokeBeanFactoryPostProcessors(beanFactory)**：执行BeanFactoryPostProcessor的方法

      首先回调 postProcessBeanDefinitionRegistry() 方法，然后再回调 postProcessBeanFactory() 方法

      * 先获取实现BeanDefinitionRegistryPostProcessor或BeanFactoryPostProcessor接口类型
      * 先执行实现了PriorityOrdered优先级接口的PostProcessor
      * 再执行实现了Ordered顺序接口的PostProcessor
      * 最后执行没有实现任何优先级或者是顺序接口PostProcessor

   6. **registerBeanPostProcessors(beanFactory)**：注册Bean的后置处理器，为了干预Spring初始化bean的流程，从而完成代理、自动注入、循环依赖等功能（intercept bean creation）
      注意：这里仅仅是向容器中注入而非使用

      * 获取所有实现了BeanPostProcessor接口类型的集合
      * 注册顺序：注册实现PriorityOrdered优先级接口 -->Ordered优先级接口 --> 没有实现任何优先级接口 --> MergedBeanDefinitionPostProcessor类型的BeanPostProcessor
      * 给容器注册ApplicationListenerDetector：用于在Bean创建完成后检查是否ApplicationListener，如果是，就把Bean放到监听器容器中保存起来

   7. **initMessageSource()**：初始化MessageSource组件，主要用于做国际化功能，消息绑定与消息解析

      * 判断BeanFactory容器中是否有id为messageSource 并且类型是MessageSource的组件：
      * 如果有，直接赋值给messageSource；如果没有，则创建一个DelegatingMessageSource注入

   8. **initApplicationEventMulticaster()**：初始化事件派发器，在注册监听器时会用到

      * 判断BeanFactory容器中是否存在自定义的ApplicationEventMulticaster
      * 如果有，直接从容器中获取；如果没有，则创建一个SimpleApplicationEventMulticaster注入

   9. **onRefresh()**：留给子容器、子类重写这个方法，在容器刷新的时候可以自定义逻辑

   10. **registerListeners()**：注册监听器

       * 将容器中所有的ApplicationListener注册到事件派发器：addApplicationListenerBean(listener)
       * 派发之前步骤产生的事件applicationEvents：multicastEvent(earlyEvent)

   11. **finishBeanFactoryInitialization()**：初始化所有剩下的单实例bean

       * `().preInstantiateSingletons()`：Instantiate all remaining (non-lazy-init) singletons
       * 获取Bean的定义信息RootBeanDefinition，它表示自己的BeanDefinition和可能存在父类的BeanDefinition合并后的对象
       * Bean满足非抽象的，单实例，非懒加载，则执行单例Bean创建流程：**getBean()**，详细步骤在Bean
       * 检查所有的Bean是否实现SmartInitializingSingleton接口，是则执行afterSingletonsInstantiated()，进行一些创建后的操作

   12. **finishRefresh()**：发布BeanFactory容器刷新完成事件

       * initLifecycleProcessor()：初始化和生命周期有关的后置处理器，容器中有就返回，没有就new
       * getLifecycleProcessor().onRefresh()：获取该生命周期后置处理器 (BeanFactory) 回调onRefresh()
       * publishEvent(new ContextRefreshedEvent(this))：发布容器刷新完成事件
       * liveBeansView.registerApplicationContext(this)：暴露Mbean

**总结：**

* Spring容器在启动的时候，先会保存所有注册进来的Bean的定义信息

  * xml注册bean；<bean>
  * 注解注册Bean；@Service、@Component、@Bean、xxx

* Spring容器会寻找合适的时机创建这些Bean

  * 用到bean的时候，利用getBean创建bean，创建好以后保存在容器中
  * 统一创建剩下所有的bean的时候；finishBeanFactoryInitialization()

* 后置处理器：BeanPostProcessor

  每一个bean创建完成，都会使用各种后置处理器进行处理、增强bean的功能：

  * AutowiredAnnotationBeanPostProcessor：处理自动注入
  * AnnotationAwareAspectJAutoProxyCreator：来做AOP功能

* 事件驱动模型

  * ApplicationListener：事件监听
  * ApplicationEventMulticaster：事件派发



***



### Bean

单实例：在容器启动时创建对象

多实例：在每次获取的时候创建对象

Bean的获取：**获取Bean时先从单例池获取，如果没有则创建并添加至单例池**

Bean的生命周期：实例化instantiation，填充属性populate，初始化initialization，销毁destruction

![](https://gitee.com/seazean/images/raw/master/Frame/Sprin-getBean.png)

![](https://gitee.com/seazean/images/raw/master/Frame/Sprin-AOP+循环依赖.png)

Java启动Spring代码：

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
```

* **获取**`AbstractBeanFactory.doGetBean`

  **第一次查询**：`DefaultSingletonBeanRegistry.getSingleton()`：从缓存池获取，获取不到继续进行

  * 获取到以后进行实例化，然后直接return 循环依赖阶段有代码详解

  查询成功：`getObjectForBeanInstance`从缓存中获取的是原始状态的bean，需要对bean进行实例化

  * 验证 bean 类型：判断是否是工厂bean，对非 FactoryBean 不做处理
  * 处理 FactoryBean：先对bean进行转换，再委托给getObjectFromFactoryBean()方法进行处理

  依次进行：循环依赖检查、父工厂检查、标记Bean已经创建、读取XML配置文件初始化bean的属性

  **第二次查询**：`DefaultSingletonBeanRegistry.getSingleton(String, ObjectFactory<?>)`

  * `singletonObjects.get(beanName)`：检查是否已经被加载，单例模式复用已经创建的bean
  * `beforeSingletonCreation(beanName)`：初始化前操作，校验是否 beanName 是否有别的线程在初始化，并记录beanName的初始化状态
  * `singletonObject = singletonFactory.getObject()`：**实例化 bean**，第二阶段详解
    * `singletonObject = singletonFactory.getObject()`：创建bean
    * 创建完成以后，Bean已经填充好，是一个完整的可使用的Bean
    * `afterSingletonCreation(beanName)`：初始化后的操作，移除初始化状态
    * `addSingleton(beanName, singletonObject)`：添加单例池，从二级三级缓存移除
  * `getTypeConverter().convertIfNecessary()`：检查所需的类型是否与实际bean实例的类型匹配

  ```java
  sharedInstance = getSingleton(beanName, () -> {
      return createBean(beanName, mbd, args);//创建，跳转第二阶段
      //lambda表达式，调用了ObjectFactory的getObject()方法，实际回调接口实现的是 createBean()方法进行创建对象
  });
  ```

  

* **实例化（创建）**AbstractAutowireCapableBeanFactory类

  **前置处理**：`createBean().resolveBeforeInstantiation()`BeanPostProcessor拦截进行前置处理

  * 前置处理：`postProcessBeforeInstantiation()`，**注解AOP部分详解，用来执行处理器**

  * 有返回值触发：`postProcessAfterInitialization()`返回代理对象
  * 如果这个bean没有特定的前置处理，那说明这个bean是一个普通的bean，执行doGetBean()

  **开始创建**：`doCreateBean(beanName, RootBeanDefinition, Object[] args)`

  * **创建实例**：`createBeanInstance(beanName, RootBeanDefinition, Object[] args)`
    * 优先级从高到低：工厂方法、有参构造函数、默认构造函数 
    * 将 BeanDifinition 转化成 BeanWrapper，Spring给所有创建的Bean实例包装成BeanWrapper，BeanWrapper是对反射相关API的简单封装，使得上层使用反射完成相关的业务逻辑大大简化
    
  * 后置处理：`applyMergedBeanDefinitionPostProcessors()`
    * 将所有的后置处理器拿出来，并且把名字叫beanName的类中的变量都封装到InjectionMetadata的injectedElements集合里面，目的是以后从中获取，创建实例，通过反射注入到相应类
    * `AutowiredAnnotationBeanPostProcessor.postProcessMergedBeanDefinition`
    
  * 添加工厂：`DefaultSingletonBeanRegistry.addSingletonFactory()`
    
    * 允许提前引用才执行，用来解决**循环依赖**
    
  * **填充属性 (依赖注入)**：`populateBean(beanName, RootBeanDefinition, BeanWrapper)`
    
    * 填充准备：通过awareBeanPostProcessor拦截，判断控制程序是否继续进行属性填充
    * 获取依赖：根据autowire类型 (Type/Name)提取依赖，存入PropertyValues并给bean注册依赖
    * 后置处理：判断是否需要进行 BeanPostProcessor 和 依赖检查
      
      * `postProcessProperties`：转入**AutowiredAnnotationBeanPostProcessor（注解**）
      
      * `findAutowiringMetadata()`：找到需要注入的元数据
      * `InjectionMetadata.InjectedElement.inject()`：注入数据（重写实现，注入变量或方法）
        * `DefaultListableBeanFactory.resolveDependency()`：解决依赖
        * `doResolveDependency().resolveCandidate()`：通过工厂获取Bean对象
        * `registerDependentBeans()`：将Bean注册为Autowired自动装配的Bean
        * `ReflectionUtils.makeAccessible()`：修改访问权限，true代表暴力破解
        * `method.invoke`：利用反射为此对象赋值
    * 填充属性：`applyPropertyValues()`，将所有解析的PropertyValues的属性填充至BeanWrapper 
    
  * **初始化**：`initializeBean(String, Object, RootBeanDefinition)`
    
    * 填充Aware接口属性：`invokeAwareMethods(beanName,bean)`
      * BeanName、ClassLoader对象实例、Spring工厂、Spring上下文ApplicationContext
    * 前置处理：`applyBeanPostProcessorsBeforeInitialization()`
      * 需要在Bean初始化前进行一些自定义的前置处理，让Bean实现BeanPostProcessor接口
    * 自定义init方法调用：`AbstractAutowireCapableBeanFactory.invokeInitMethods()`
      * 如果Bean实现了InitializingBean接口，执行afeterPropertiesSet()方法
      * 如果Bean在Spring配置文件中配置了 init-method属性，则会自动调用其配置的初始化方法
    * 后置处理：`applyBeanPostProcessorsAfterInitialization()`
      * **AOP，跳转注解**，`AbstractAutoProxyCreatorwrapIfNecessary -> creatProxy`
      * 如果不存在循环依赖，动态代理在此处完成，否则会提前创建
    
  * 循环依赖检查：如果存在循环依赖，在属性填充阶段会生成Bean对象的动态代理，则缓存中放置了提前生成的代理对象，然后使用原始bean继续执行初始化，所以返回最终bean前，把原始bean置换为代理对象返回；
  
    存在循环依赖，在初始化的后置处理中不会重新创建代理对象，真正创建动态代理Bean的阶段是在获取提前引用阶段，**循环依赖**详解，看后置处理源码：  
  
    ```java
    public Object postProcessAfterInitialization() {。。。。。
        //去提前代理引用池中寻找该key，如果存在就不会创建动态代理
        Object cacheKey = getCacheKey(bean.getClass(), beanName);
        if (this.earlyProxyReferences.remove(cacheKey) != bean) {//不成立
            return wrapIfNecessary(bean, beanName, cacheKey);
    //。。。。。。
    ```
  
  * **注册销毁**：`AbstractBeanFactory.registerDisposableBeanIfNecessary`，
    
    * 根据不同的scope进行disposableBean的注册，在销毁对象时调用destory()



****



### 循环依赖

* 解决循环依赖：提前引用，提前暴露创建中的Bean

  循环依赖的三级缓存：

  ```java
  //一级缓存：存放所有初始化完成单实例bean，单例池
  private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
  
  //二级缓存：存放实例化未进行初始化的Bean，提前引用池
  private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
  
  /** Cache of singleton factories: bean name to ObjectFactory. 3*/
  private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
  ```

  为什么需要三级缓存？

  * 循环依赖解决需要提前引用动态代理对象，AOP动态代理是在Bean初始化后的后置处理中进行，这时的bean已经是成品对象，需要提前进行动态代理，三级缓存的ObjectFactory可以提前产生需要代理的对象
  * 若存在循环依赖，**后置处理不创建代理对象，真正创建代理对象的过程是在getBean(B)的阶段中**

  一定会提前引用吗？

  * 出现循环依赖才去使用，不出现就不使用

  wrapIfNecessary一定创建代理对象吗？

  * 存在增强器会创建动态代理，不需要增强就不需要创建动态代理对象
  * 不创建就会把最原始的实例化的Bean放到二级缓存，因为addSingletonFactory参数中传入了实例化的Bean，在singletonFactory.getObject()中返回给singletonObject，放入二级缓存

  什么时候将Bean的引用提前暴露给第三级缓存的ObjectFactory持有？

  * 实例化之后，依赖注入之前

    ```java
    createBeanInstance --> addSingletonFactory --> populateBean
    ```

* 解决循环依赖，源码解析（A依赖B，B依赖A）

  第二阶段当A创建实例后填充属性前，执行`addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));`方法，注意lambda表达式，getObject()时调用

  ````java
  //添加给定的单例工厂以构建指定的单例
  protected void addSingletonFactory(String beanName, ObjectFactory<?> singletonFactory) {
      Assert.notNull(singletonFactory, "Singleton factory must not be null");
      synchronized (this.singletonObjects) {
          //单例池包含该Bean说明已经创建完成，不需要循环依赖
          if (!this.singletonObjects.containsKey(beanName)) {
              this.singletonFactories.put(beanName,singletonFactory);//加入三级缓存
              this.earlySingletonObjects.remove(beanName);
              //从二级缓存移除，因为三个Map中都是一个对象，不能同时存在
              this.registeredSingletons.add(beanName);
          }
      }
  }
  ````

  填充属性时A依赖B，这时需要getBean(B)，接着B填充属性时发现依赖A，去进行**第一次**getSingleton(A)

  <img src="https://gitee.com/seazean/images/raw/master/Frame/Spring-三级缓存getSingleton()方法.png" style="zoom:67%;" />

  从三级缓存获取A的Bean：`singletonFactory.getObject();`，相当于调用了Lambda表达式的方法：

  ```java
  protected Object getEarlyBeanReference(String beanName, RootBeanDefinition mbd, Object bean) {
      //....省略
      exposedObject = ibp.getEarlyBeanReference(exposedObject, beanName);
      return exposedObject;
  }
  ```

  追踪getEarlyBeanReference(exposedObject, beanName)，

  ```java
  public Object getEarlyBeanReference(Object bean, String beanName) {
      Object cacheKey = getCacheKey(bean.getClass(), beanName);
      this.earlyProxyReferences.put(cacheKey, bean);
      //提前引用代理池earlyProxyReferences中添加该Bean
      return wrapIfNecessary(bean, beanName, cacheKey);
  	//创建代理对象，createProxy
  }
  ```
  
  wrapIfNecessary
  
  ```java
  // Create proxy if we have advice.获取增强方法
  Object[] specificInterceptors = getAdvicesAndAdvisorsForBean(bean.getClass(), beanName, null);///
  if (specificInterceptors != DO_NOT_PROXY) {
      Object proxy = createProxy(。。。。);。。。
      return proxy;
  }
  this.advisedBeans.put(cacheKey, Boolean.FALSE);
  return bean;
  ```




***



### 注解

#### Component

@Component和@Service都是常用的注解，Spring如何解析？

* **@Component解析流程：**

  打开源码注释：@see org.....ClassPathBeanDefinitionScanner.doScan()

  findCandidateComponents()：从classPath扫描组件，并转换为备选BeanDefinition

  ```java
  protected Set<BeanDefinitionHolder> doScan(String... basePackages) {
      Set<BeanDefinitionHolder> beanDefinitions = new LinkedHashSet<>();
      for (String basePackage : basePackages) {
          //findCandidateComponents 读资源装换为BeanDefinition
          Set<BeanDefinition> candidates = findCandidateComponents(basePackage);
          for (BeanDefinition candidate : candidates) {//....}
              //.......
      return beanDefinitions;
  }
  ```
  
  ClassPathScanningCandidateComponentProvider.findCandidateComponents()
  
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
    * `String packageSearchPath = ResourcePatternResolver.CLASSPATH_ALL_URL_PREFIX resolveBasePackage(basePackage) + '/' + this.resourcePattern` ：将package转化为ClassLoader类资源搜索路径packageSearchPath，例如：`com.wl.spring.boot`转化为`classpath*:com/wl/spring/boot/**/*.class`
    * `Resource[] resources = getResourcePatternResolver().getResources(packageSearchPath)`：加载搜素路径下的资源
    * `MetadataReader metadataReader = getMetadataReaderFactory().getMetadataReader(resource)`：获取元数据阅读器
    * isCandidateComponent：判断是否是备选组件
    * candidates.add(sbd)：添加到返回结果的list
  
    isCandidateComponent源码：
  
  ```java
  protected boolean isCandidateComponent(MetadataReader m) throws IOException {
      //....
      for (TypeFilter tf : this.includeFilters) {
          if (tf.match(m, getMetadataReaderFactory())) {
              return isConditionMatch(metadataReader);
              //....
          }
  ```
  
  ```java
  protected void registerDefaultFilters() {
      this.includeFilters.add(new AnnotationTypeFilter(Component.class));//...
  }
  ```
  
   includeFilters由`registerDefaultFilters()`设置初始值，有@Component，没有@Service
  
    因为@Component是@Service的元注解，Spring在读取@Service，也读取了它的元注解，并将@Service作为@Component处理
  
  ```java
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Component
  public @interface Service {}
  ```
  
* **@Component派生性流程：**

  metadataReader本质上：`MetadataReader metadataReader =new SimpleMetadataReader(...);`

  `isCandidateComponent.match()`方法：`TypeFilter.match` -->`AnnotationTypeFilter.matchSelf()`

  ```java
  @Override
  protected boolean matchSelf(MetadataReader metadataReader) {
      AnnotationMetadata metadata = metadataReader.getAnnotationMetadata();
      return metadata.hasAnnotation(this.annotationType.getName()) ||
          (this.considerMetaAnnotations && metadata.hasMetaAnnotation(this.annotationType.getName()));
  }
  ```

  * `metadata = new SimpleMetadataReader(...).getAnnotationMetadata()`

    ```java
    @Override
    public AnnotationMetadata getAnnotationMetadata() {
        return this.annotationMetadata;
    }
    ```

    观察源码：`annotationMetadata = new AnnotationMetadataReadingVisitor(classLoader);`

  * `metadata.hasMetaAnnotation=AnnotationMetadataReadingVisitor.hasMetaAnnotation`

    判断该注解的元注解在不在metaAnnotationMap中，如果在就返回true

    ```java
    @Override
    public boolean hasMetaAnnotation(String metaAnnotationType) {
        Collection<Set<String>> allMetaTypes = this.metaAnnotationMap.values();
        for (Set<String> metaTypes : allMetaTypes) {
            if (metaTypes.contains(metaAnnotationType)) {
                return true;
            }
        }
        return false;
    }
    ```

    metaAnnotationMap怎么赋值的？

    metaAnnotationMap赋值方法在`SimpleMetadataReader.SimpleMetadataReader`中：

    ```java
    classReader.accept(visitor, ClassReader.SKIP_DEBUG);
    ```

    然后通过readElementValues方法中：

    ```java
    annotationVisitor.visitEnd();
    ```

    追踪方法：`AnnotationAttributesReadingVisitor.visitEnd()`

    递归读取：内部方法`recursivelyCollectMetaAnnotations()`递归的读取注解，与注解的元注解（读@Service，再读元注解@Component），

    添加数据：`this.metaAnnotationMap.put(annotationClass.getName(), metaAnnotationTypeNames);`



***



#### Autowired

打开@Autowired，注释上写Please consult the javadoc for the AutowiredAnnotationBeanPostProcessor

AutowiredAnnotationBeanPostProcessor间接实现InstantiationAwareBeanPostProcessor，就具备了实例化前后 (而不是初始化前后) 管理对象的能力，实现了BeanPostProcessor，具有初始化前后管理对象的能力，实现BeanFactoryAware，具备随时拿到BeanFactory的能力，所以这个类**具备一切后置处理器的能力**

**在容器启动，为对象赋值的时候，遇到@Autowired注解，会用后置处理器机制，来创建属性的实例，然后再利用反射机制，将实例化好的属性，赋值给对象上，这就是Autowired的原理**

作用时机：

* Spring容器在每个Bean实例化之后，调用AutowiredAnnotationBeanPostProcessor的`postProcessMergedBeanDefinition`方法，查找该Bean是否有@Autowired注解

* Spring在每个Bean实例化时，调用`populateBean`进行属性注入的时候，即调用`postProcessPropertyValues`方法，查找该Bean是否有@Autowired注解



***



#### AOP

##### 注解

@EnableAspectJAutoProxy

给容器中导入AspectJAutoProxyRegistrar：`@Import(AspectJAutoProxyRegistrar.class)`

利用AspectJAutoProxyRegistrar给容器中注册**AnnotationAwareAspectJAutoProxyCreator**，以BeanDefiantion形式存在，在容器初始化时加载。

AnnotationAwareAspectJAutoProxyCreator是这种类型的后置处理器：InstantiationAwareBeanPostProcessor

* InstantiationAwareBeanPostProcessor是在创建Bean实例之前进行拦截，会调用后置处理器来返回对象`postProcessBeforeInstantiation()`
* BeanPostProcessor是在Bean对象创建完成初始化前后调用
* **该类会在bean的实例化和初始化的前后起作用**



##### 实现流程

1. 创建IOC容器，调用refresh()刷新容器

2. registerBeanPostProcessors(beanFactory)：注册bean的后置处理器来方便拦截bean的创建

   **注册BeanPostProcessor，实际上就是创建BeanPostProcessor对象，保存在容器中**

   向IOC容器中注入 BeanPostProcessor 后置处理器，AAAPC间接实现了Order接口：

   ```java
   for (String ppName : orderedPostProcessorNames) {
       BeanPostProcessor pp = beanFactory.getBean(ppName, BeanPostProcessor.class);
       orderedPostProcessors.add(pp);//...
   }//....
   registerBeanPostProcessors(beanFactory, orderedPostProcessors);//注册
   ```

   通过`getBean()`创建AnnotationAwareAspectJAutoProxyCreator对象，调用doGetBean()

   在initializeBean()方法中执行invokeAwareMethods()方法`if (bean instanceof BeanFactoryAware)`条件判别成功，转入`AbstractAdvisorAutoProxyCreator.setBeanFactory`，代码跟踪转入`AnnotationAwareAspectJAutoProxyCreator.initBeanFactory()`进行工厂的初始化，至此创建成功

3. finishBeanFactoryInitialization(beanFactory)：创建剩下的单实例bean

   `AbstractAutowireCapableBeanFactory.createBean().resolveBeforeInstantiation()`：

   `if(是否实现接口)`为真进入：`applyBeanPostProcessorsBeforeInstantiation`方法

   * `this.advisedBeans.containsKey(cacheKey)`：判断当前bean是否在advisedBeans中（保存了所有需要增强bean）

   * `isInfrastructureClass`：判断当前bean是否是基础类型的Advice、Pointcut、Advisor、AopInfrastructureBean，或者（子类中）是否是切面Aspect

   * 是否需要跳过：子类`AspectJAwareAdvisorAutoProxyCreator.shouldSkip()`

     * `findCandidateAdvisors()`：获取候选的增强器（切面里面的通知方法）每一个封装的通知方法的增强器是 InstantiationModelAwarePointcutAdvisor**（AAAPC）**

     * `if()`：判断每一个增强器是否是 AspectJPointcutAdvisor 类型的，返回true，否则继续执行
     * `return super.shouldSkip(beanClass, beanName)`：永远返回false  

   * `getCustomTargetSource(beanClass, beanName)`：返回为空，doCreateBean()

   **进入applyBeanPostProcessorsAfterInitialization：后置处理器创建AOP**

   ```java
   //如果Bean被子类标识为要代理的bean，则使用配置的拦截器创建代理
   public Object postProcessAfterInitialization(@Nullable Object bean,String bN){
       if (bean != null) {
           Object cacheKey = getCacheKey(bean.getClass(), beanName);//获取缓存key
           if (this.earlyProxyReferences.remove(cacheKey) != bean) {
               //去提前代理引用池中寻找该key，不存在则创建代理
               //如果存在则证明被代理过，则判断是否是当前的bean，不是则创建代理
               return wrapIfNecessary(bean, bN, cacheKey);
           }
       }
       return bean;
   }
   ```

   创建动态代理：`wrapIfNecessary()`调用`createProxy()`（wrap包装）

   注释：Create proxy if we have advice

   * `getAdvicesAndAdvisorsForBean()`：获取当前bean的所有增强器 (通知方法)，**为空就直接返回**

     * findEligibleAdvisors()：找到哪些通知方法是需要切入当前bean方法的
     * AopUtils.findAdvisorsThatCanApply()：获取到能在bean使用的增强器
     * sortAdvisors(eligibleAdvisors)：给增强器排序

   * `this.advisedBeans.put(cacheKey, Boolean.TRUE)`：保存当前bean在advisedBeans中

   * `Object proxy = createProxy(...)`：如果增强器不为空就创建代理，创建当前bean的代理对象

     * buildAdvisors(beanName, specificInterceptors)：获取所有增强器（通知方法）

     * 保存到proxyFactory

     * `return proxyFactory.getProxy(getProxyClassLoader())`：返回代理对象

     * ProxyFactory类：`return createAopProxy().getProxy(classLoader)`

     DefaultAopProxyFactory类：给容器中返回当前组件使用增强了的代理对象

     ```java
     @Override
     public AopProxy createAopProxy(AdvisedSupport config) throws AopConfigException {
         if (config.isOptimize() || config.isProxyTargetClass() || hasNoUserSuppliedProxyInterfaces(config)) {
             Class<?> targetClass = config.getTargetClass();
             //根据是否实现接口选择哪种动态代理
             if (targetClass.isInterface() || Proxy.isProxyClass(targetClass)) {
                 return new JdkDynamicAopProxy(config);
             }
             return new ObjenesisCglibAopProxy(config);
         }
         else {
             return new JdkDynamicAopProxy(config);
         }
     }
     ```

4. 给容器中返回使用cglib增强了的代理对象，**初始化完成，加入容器**

5. 以后容器中获取到的就是这个组件的代理对象，执行目标方法的时候，代理对象就会执行通知方法的流程



##### 链式调用

容器中保存了组件的代理对象（cglib增强后的对象），这个对象里面保存了详细信息（增强器，目标对象等）

1. `CglibAopProxy.intercept()`：拦截目标方法的执行（静态内部类）

2. `List<Object> chain = this.advised.getInterceptorsAndDynamicInterceptionAdvice()`：
   根据ProxyFactory对象获取将要执行的目标方法拦截器链

   * `config.getAdvisors()`：获取所有拦截器，一个默认ExposeInvocationInterceptor和4个增强器

   * `List<Object> interceptorList`：保存所有的拦截器

   * `registry.getInterceptors(advisor)`：遍历所有的增强器，将其转为Interceptor

     将增强器转为List<MethodInterceptor>：

     * 如果是MethodInterceptor，直接加入到集合中
     * 如果不是，使用AdvisorAdapter将增强器转为MethodInterceptor（适配器）

   **拦截器链**：每一个通知方法又被包装为方法拦截器，利用MethodInterceptor机制

3. 如果没有拦截器链，直接执行目标方法

4. 如果有拦截器链，把需要执行的目标对象、目标方法、拦截器链等信息传入CglibMethodInvocation 对象

5.  `Object retVal =  ReflectiveMethodInvocation.proceed`：拦截器链的触发过程

   * 如果没有拦截器执行执行目标方法，或者拦截器的索引和拦截器数组-1大小一样（指定到了最后一个拦截器）执行目标方法
   * 链式获取每一个拦截器，拦截器执行invoke方法，每一个拦截器等待下一个拦截器执行完成返回以后再来执行；拦截器链的机制，保证通知方法与目标方法的执行顺序
   * 效果：图示先从上往下建立链，然后从下往上依次执行，责任链模式
     * 正常执行：（环绕通知）-> 前置通知 -> 目标方法 -> 后置通知 -> 返回通知
      * 出现异常：（环绕通知）-> 前置通知 -> 目标方法 -> 后置通知 -> 异常通知

   ![](https://gitee.com/seazean/images/raw/master/Frame/Spring-AOP动态代理执行方法.png)



***



#### Transactional

如果一个类或者一个类中的 public 方法上被标注@Transactional 注解的话，Spring 容器就会在启动的时候为其创建一个代理类，在调用被@Transactional注解的 public 方法的时候，实际调用的是TransactionInterceptor类中的 invoke()方法。这个方法的作用就是在目标方法之前开启事务，方法执行过程中如果遇到异常的时候回滚事务，方法调用完成之后提交事务

`TransactionInterceptor` 类中的 `invoke()`方法内部实际调用的是 `TransactionAspectSupport` 类的 `invokeWithinTransaction()`方法







****





# MVC

## 概述

SpringMVC：是一种基于Java实现MVC模型的轻量级Web框架

SpringMVC优点：

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



## 技术架构

### 组件介绍

* DispatcherServlet：核心控制器， 是SpringMVC的核心，整体流程控制的中心，所有的请求第一步都先到达这里，由其调用其它组件处理用户的请求，它就是在web.xml配置的核心Servlet，有效的降低了组件间的耦合性
  
* HandlerMapping：处理器映射器， 负责根据用户请求找到对应具体的Handler处理器，SpringMVC中针对配置文件方式、注解方式等提供了不同的映射器来处理

* Handler：处理器，其实就是Controller，业务处理的核心类，通常由开发者编写，并且必须遵守Controller开发的规则，这样适配器才能正确的执行。例如实现Controller接口，将Controller注册到IOC容器中等

* HandlAdapter：处理器适配器，根据映射器中找到的Handler，通过HandlerAdapter去执行Handler，这是适配器模式的应用

* View Resolver：视图解析器， 将Handler中返回的逻辑视图（ModelAndView）解析为一个具体的视图（View）对象

* View：视图， View最后对页面进行渲染将结果返回给用户。springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等

  ![](https://gitee.com/seazean/images/raw/master/Frame/SpingMVC技术架构.png)



### 工作原理

在Spring容器初始化时会建立所有的URL和Controller的对应关系，保存到Map<URL,Controller>中，这样request就能快速根据URL定位到Controller。实现：

1. 在SpringIOC容器初始化完所有单例bean后
2. SpringMVC会遍历所有的bean，获取controller中对应的URL（这里获取URL的实现类有多个，用于处理不同形式配置的Controller）
3. 将每一个URL对应一个controller存入Map<URL,Controller>中

注意：将Controller类的注解换成@Component，启动时不会报错，但是在浏览器中输入路径时会出现404，说明Spring没有对所有的bean进行URL映射

**一个Request来了：**

1. 监听端口，获得请求：Tomcat监听8080端口的请求，进行接收、解析、封装，根据路径调用了web.xml中配置的核心控制器DispatcherServlet
2. 获取Handler：进入DispatcherServlet，核心控制器调用HandlerMapping去根据请求的URL获取对应的Handler。这里有个问题，如果获取的Handler为null则返回404
3. 调用适配器执行Handler：
   * 适配器根据request的URL去Handler中寻找对应的处理方法 (Controller的URL与方法的URL拼接后对比)
   * 获取到对应方法后，需要将request中的参数与方法参数上的数据进行绑定，根据反射获取方法的参数名和注解，再根据注解或者根据参数名对照进行绑定(找到对应的参数，然后在反射调用方法时传入)
   * 绑定完参数后，反射调用方法获取ModelAndView（如果Handler中返回的是String、View等对象，SpringMVC也会将它们重新封装成一个ModelAndView）
4. 调用视图解析器解析：将ModelAndView解析成View对象
5. 渲染视图：将View对象中的返回地址、参数信息等放入RequestDispatcher，最后进行转发





***



## 基本配置

### 入门项目

流程分析：

* 服务器启动
  1. 加载web.xml中DispatcherServlet
  2. 读取spring-mvc.xml中的配置，加载所有controller包中所有标记为bean的类
  3. 读取bean中方法上方标注@RequestMapping的内容
* 处理请求
  1. DispatcherServlet配置拦截所有请求 /
  2. 使用请求路径与所有加载的@RequestMapping的内容进行比对
  3. 执行对应的方法
  4. 根据方法的返回值在webapp目录中查找对应的页面并展示  

代码实现：

* pom.xml导入坐标

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
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
  </project>
  ```

* 设定具体Controller，控制层 java / controller / UserController

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
              <param-value>classpath*:spring-mvc.xml</para m-value>
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

Controller加载控制：SpringMVC的处理器对应的bean必须按照规范格式开发，未避免加入无效的bean可通过bean加载过滤器进行包含设定或排除设定，表现层bean标注通常设定为@Controller  

* resources / spring-mvc.xml配置

  ```xml
  <context:component-scan base-package="com.itheima">
      <context:include-filter 
  						type="annotation" 
  						expression="org.springframework.stereotype.Controller"/>
  </context:component-scan>
  ```

* 静态资源加载（webapp目录下的相关资源），spring-mvc.xml配置，开启mvc命名空间

  ```xml
  <!--放行指定类型静态资源配置方式-->
  <mvc:resources mapping="/img/**" location="/img/"/> <!--webapp/img/资源-->
  <mvc:resources mapping="/js/**" location="/js/"/>
  <mvc:resources mapping="/css/**" location="/css/"/>
  
  <!--SpringMVC提供的通用资源放行方式，建议选择-->
  <mvc:default-servlet-handler/>
  ```

* 中文乱码处理 SpringMVC提供专用的中文字符过滤器，用于处理乱码问题。配置在 web.xml 里面

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

* 使用注解形式转化SpringMVC核心配置文件为配置类 java / config /  SpringMVCConfiguration.java

  ```java
  @Configuration
  @ComponentScan(value = "com.itheima", includeFilters = @ComponentScan.Filter(
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

* 基于servlet3.0规范，自定义Servlet容器初始化配置类，加载SpringMVC核心配置类  

  ```java
  public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
      //创建Servlet容器时，使用注解方式加载SPRINGMVC配置类中的信息，
      //并加载成WEB专用的ApplicationContext对象该对象放入了ServletContext范围，
      //在整个WEB容器中可以随时获取调用
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



## 请求响应

### 请求

#### 数据准备

* web.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
           version="3.1">
  
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
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans 
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/mvc 
          http://www.springframework.org/schema/mvc/spring-mvc.xsd
          http://www.springframework.org/schema/context 
          http://www.springframework.org/schema/context/spring-context.xsd">
      <context:component-scan base-package="controller,converter,domain"/>
  </beans>
  ```



#### 普通类型

SpringMVC将传递的参数封装到处理器方法的形参中，达到快速访问参数的目的

* 访问URL：http://localhost/requestParam1?name=itheima&age=14  

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

@RequestParam的使用：

*  类型： 形参注解

* 位置：处理器类中的方法形参前方

* 作用：绑定请求参数与对应处理方法形参间的关系 

* 访问URL：http://localhost/requestParam2?userName=Jock

  ```java
  @RequestMapping("/requestParam2")
  public String requestParam2(@RequestParam(
                              name = "userName",
                              required = true,	//为true代表必须有参数
                              defaultValue = "itheima") String name){
      System.out.println("name=" + name);
      return "page.jsp";
  }
  ```



***



#### POJO类型

##### 简单类型

当POJO中使用简单类型属性时， 参数名称与POJO类属性名保持一致  

* 访问URL： http://localhost/requestParam3?name=itheima&age=14  

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



##### 参数冲突

当POJO类型属性与其他形参出现同名问题时，将被**同时赋值**，建议使用@RequestParam注解进行区分

* 访问URL： http://localhost/requestParam4?name=itheima&age=14  

  ```java
  @RequestMapping("/requestParam4")
  public String requestParam4(User user,String age){
      System.out.println("user.age=" + user.getAge() + ",age=" + age);//14 14 
      return "page.jsp";
  }
  ```



##### 复杂类型

当POJO中出现对象属性时，参数名称与对象层次结构名称保持一致  

* 访问URL： http://localhost/requestParam5?address.province=beijing  

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

  

##### 容器类型

* 通过URL地址中同名参数，可以为POJO中的集合属性进行赋值，集合属性要求保存简单数据

  访问URL：http://localhost/requestParam6?nick=Jock1&nick=Jockme&nick=zahc

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

* POJO中出现List保存对象数据，参数名称与对象层次结构名称保持一致，使用数组格式描述集合中对象的位置访问URL： http://localhost/requestParam7?addresses[0].province=bj&addresses[1].province=tj  

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

  

* POJO中出现Map保存对象数据，参数名称与对象层次结构名称保持一致，使用映射格式描述集合中对象位置

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

* 访问URL： http://localhost/requestParam9?nick=Jockme&nick=zahc  

  ```java
  @RequestMapping("/requestParam9")
  public String requestParam9(String[] nick){
      System.out.println(nick[0] + "," + nick[1]);
      return "page.jsp";
  }
  ```



##### 集合类型

保存简单类型数据，请求参数名与处理器方法形参名保持一致，且请求参数数量＞ 1个

* 访问URL： http://localhost/requestParam10?nick=Jockme&nick=zahc

  ```java
  @RequestMapping("/requestParam10")
  public String requestParam10(@RequestParam("nick") List<String> nick){
      System.out.println(nick);
      return "page.jsp";
  }
  ```

* 注意： SpringMVC默认将List作为对象处理，赋值前先创建对象，然后将nick**作为对象的属性**进行处理。由于List是接口，无法创建对象，报无法找到构造方法异常；修复类型为可创建对象的ArrayList类型后，对象可以创建，但没有nick属性，因此数据为空。
  解决方法：需要告知SpringMVC的处理器nick是一组数据，而不是一个单一属性。通过@RequestParam注解，将数量大于1个names参数打包成参数数组后， SpringMVC才能识别该数据格式，并判定形参类型是否为数组或集合，并按数组或集合对象的形式操作数据



***



#### 转换器

##### 类型转换器

开启转换配置：`<mvc:annotation-driven />  `
作用：提供Controller请求转发，Json自动转换等功能

如果访问URL：http://localhost/requestParam1?name=itheima&age=seazean，会出现报错，类型转化异常

```java
@RequestMapping("/requestParam1")
public String requestParam1(String name ,int age){
    System.out.println("name=" + name + ",age=" + age);
    return "page.jsp";
}
```

SpringMVC对接收的数据进行自动类型转换，该工作通过Converter接口实现：

* **标量转换器**
  StringToBooleanConverter String→Boolean
  ObjectToStringConverter Object→String
  StringToNumberConverterFactory String→Number（ Integer、 Long等）
  NumberToNumberConverterFactory Number子类型之间(Integer、 Long、 Double等)
  StringToCharacterConverter String→java.lang.Character
  NumberToCharacterConverter Number子类型(Integer、 Long、 Double等)→java.lang.Character
  CharacterToNumberFactory java.lang.Character→Number子类型(Integer、 Long、 Double等)
  StringToEnumConverterFactory String→enum类型
  EnumToStringConverter enum类型→String
  StringToLocaleConverter String→java.util.Local
  PropertiesToStringConverter java.util.Properties→String
  StringToPropertiesConverter String→java.util.Properties  

* **集合、数组相关转换器**
  ArrayToCollectionConverter 数组→集合（ List、 Set）
  CollectionToArrayConverter 集合（ List、 Set） →数组
  ArrayToArrayConverter 数组间
  CollectionToCollectionConverter 集合间（ List、 Set）
  MapToMapConverter Map间
  ArrayToStringConverter 数组→String类型
  StringToArrayConverter String→数组， trim后使用“,”split
  ArrayToObjectConverter 数组→Object
  ObjectToArrayConverter Object→单元素数组
  CollectionToStringConverter 集合（ List、 Set） →String
  StringToCollectionConverter String→集合（ List、 Set）， trim后使用“,”split
  CollectionToObjectConverter 集合→Object
  ObjectToCollectionConverter Object→单元素集合  
* **默认转换器**
  ObjectToObjectConverter Object间
  IdToEntityConverter Id→Entity
  FallbackObjectToStringConverter Object→String  



##### 日期类型转换

![](https://gitee.com/seazean/images/raw/master/Frame/SpringMVC-date数据类型转换.png)

如果访问URLhttp://localhost/requestParam11?date=1999-09-09会报错，所以需要日期类型转换

* 声明自定义的转换格式并覆盖系统转换格式，配置resources / spring-mvc.xml

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

  依赖注解驱动支持，xml开启配置：

  ```xml
  <mvc:annotation-driven />  
  ```



***



##### 自定义类型转换器

* 自定义类型转换器，实现Converter接口，并制定转换前与转换后的类型

  ```java
  //自定义类型转换器，实现Converter接口，接口中指定的泛型即为最终作用的条件
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

* 配置resources / spring-mvc.xml，注册自定义转换器，将功能加入到SpringMVC转换服务ConverterService中

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



### 响应

#### 页面跳转

请求转发和重定向：

* 转发

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

* 重定向

  ```java
  @RequestMapping("/showPage2")
  public String showPage2() {
      System.out.println("user mvc controller is running ...");
      return "redirect:/WEB-INF/page/page.jsp";//不能访问WEB-INF下的资源
  }
  ```

  

页面访问快捷设定 (InternalResourceViewResolver)：

* 展示页面的保存位置通常固定且结构相似，可以设定通用的访问路径简化页面配置，配置spring-mvc.xml：

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

* 如果未设定了返回值，使用void类型，则默认使用访问路径作页面地址的前缀后缀

  ```java
  //最简页面配置方式，使用访问路径作为页面名称，省略返回值
  @RequestMapping("/showPage6")
  public void showPage6() {
      System.out.println("user mvc controller is running ...");
  }
  ```



***



#### 带数据跳转

ModelAndView 是SpringMVC提供的一个对象，该对象可以用作控制器方法的返回值（Model同）
作用：

+ 设置数据，向请求域对象中存储数据
+ 设置视图，逻辑视图

代码实现：

* 使用HttpServletRequest类型形参进行数据传递

  ```java
  @Controller
  public class BookController {
      @RequestMapping("/showPageAndData1")
      public String showPageAndData1(HttpServletRequest request) {
          request.setAttribute("name","itheima");
          return "page";
      }
  }
  ```

* 使用Model类型形参进行数据传递

  ```java
  @RequestMapping("/showPageAndData2")
  public String showPageAndData2(Model model) {
      model.addAttribute("name","itheima");
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

* 使用ModelAndView类型形参进行数据传递，将该对象作为返回值传递给调用者  

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

* ModelAndView扩展

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



#### JSON数据

注解：@ResponseBody

作用：将Controller的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到Response的body区。如果返回值是字符串，那么直接将字符串返回客户端；如果是一个对象，会将对象转化为Json，然后返回客户端。

注意：当方法上面没有写ResponseBody，底层会将方法的返回值封装为ModelAndView对象。

* 使用HttpServletResponse对象响应数据

  ```java
  @Controller
  public class AccountController {
      @RequestMapping("/showData1")
      public void showData1(HttpServletResponse response) throws IOException {
          response.getWriter().write("message");
      }
  }
  ```

* 使用**@ResponseBody将返回的结果作为响应内容**（页面显示），而非响应的页面名称

  ```java
  @RequestMapping("/showData2")
  @ResponseBody
  public String showData2(){
      return "{'name':'Jock'}";
  }
  ```

* 使用jackson进行json数据格式转化

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

* 使用SpringMVC提供的消息类型转换器将对象与集合数据自动转换为JSON数据

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

  * 使用SpringMVC注解驱动：

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



## Servlet

* web.xml配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
           version="3.1">
  
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

* spring-mvc.xml配置

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
          http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
  
      <context:component-scan base-package="com.itheima"/>
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/page/"/>
          <property name="suffix" value=".jsp"/>
      </bean>
      <mvc:annotation-driven/>
  </beans>
  ```

* SpringMVC提供访问原始Servlet接口API的功能，通过形参声明即可 

  ```java
  @RequestMapping("/servletApi")
  public String servletApi(HttpServletRequest request,
                           HttpServletResponse response, HttpSession session){
      System.out.println(request);
      System.out.println(response);
      System.out.println(session);
      request.setAttribute("name","itheima");
      System.out.println(request.getAttribute("name"));
      return "page.jsp";
  }
  ```

* Head数据获取快捷操作方式
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

* Cookie数据获取快捷操作方式
  名称：@CookieValue
  类型：形参注解
  位置：处理器类中的方法形参前方
  作用：绑定请求Cookie数据与对应处理方法形参间的关系
  范例：

  ```java
  @RequestMapping("/cookieApi")
  public String cookieApi(@CookieValue("JSESSIONID") String jsessionid){
      System.out.println(jsessionid);
      return "page";
  }  
  ```

* Session数据获取
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
      session.setAttribute("name","itheima");
      return "page";
  }
  ```

* Session数据设置
  名称： @SessionAttributes
  类型： 类注解
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

  



****



## 异步调用

### 请求参数

名称：@RequestBody
类型：形参注解
位置：处理器类中的方法形参前方
作用：将异步提交数据组织成标准请求参数格式，并赋值给形参
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

* 注解添加到Pojo参数前方时，封装的异步提交数据按照Pojo的属性格式进行关系映射
  * POJO中的属性如果请求数据中没有，属性值为null
  * POJO中没有的属性如果请求数据中有，不进行映射
* 注解添加到集合参数前方时，封装的异步提交数据按照集合的存储结构进行关系映射 

```java
@RequestMapping("/ajaxPojoToController")
//如果处理参数是POJO，且页面发送的请求数据格式与POJO中的属性对应，@RequestBody注解可以自动映射对应请求数据到POJO中
public String  ajaxPojoToController(@RequestBody User user){
    System.out.println("controller pojo :"+user);
    return "page.jsp";
}

@RequestMapping("/ajaxListToController")
//如果处理参数是List集合且封装了POJO，且页面发送的数据是JSON格式的对象数组，数据将自动映射到集合参数中
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
作用：将java对象转为json格式的数据

方法返回值为Pojo时，自动封装数据成Json对象数据：

```java
@RequestMapping("/ajaxReturnJson")
@ResponseBody
public User ajaxReturnJson(){
    System.out.println("controller return json pojo...");
    User user = new User("Jockme",40);
    return user;
}  
```

方法返回值为List时，自动封装数据成json对象数组数据：

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

AJAX文件

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

跨域访问：当通过域名A下的操作访问域名B下的资源时，称为跨域访问。跨域访问时，会出现无法访问的现象。

环境搭建：

* 为当前主机添加备用域名
  * 修改windows安装目录中的host文件
  * 格式： ip 域名
* 动态刷新DNS
  *  命令： ipconfig /displaydns
  *  命令： ipconfig /flushdns   

跨域访问支持：

* 名称： @CrossOrigin
* 类型： 方法注解 、 类注解
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

* jsp文件

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

### 概述

拦截器（ Interceptor）是一种动态拦截方法调用的机制
作用：

1. 在指定的方法调用前后执行预先设定后的的代码
2. 阻止原始方法的执行

核心原理：AOP思想

拦截器链：多个拦截器按照一定的顺序，对原始被调用功能进行增强  

拦截器和过滤器对比：

1. 归属不同： Filter属于Servlet技术， Interceptor属于SpringMVC技术

2. 拦截内容不同： Filter对所有访问进行增强， Interceptor仅针对SpringMVC的访问进行增强  

   ![](https://gitee.com/seazean/images/raw/master/Frame/拦截器-过滤器和拦截器的运行机制.png)



***



### 执行流程

拦截器的执行流程：

![](https://gitee.com/seazean/images/raw/master/Frame/拦截器执行流程.png)



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
  * 返回值为false，被拦截的处理器将不执行  



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



#### 完成处理

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

```xml
<mvc:interceptors>
    <!--开启具体的拦截器的使用，可以配置多个-->
    <mvc:interceptor>
        <!--设置拦截器的拦截路径，支持*通配-->
        <!--/**         表示拦截所有映射-->
        <!--/*          表示拦截所有/开头的映射-->
        <!--/user/*     表示拦截所有/user/开头的映射-->
        <!--/user/add*  表示拦截所有/user/开头，且具体映射名称以add开头的映射-->
        <!--/user/*All  表示拦截所有/user/开头，且具体映射名称以All结尾的映射-->
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

![](https://gitee.com/seazean/images/raw/master/Frame/拦截器-多拦截器配置.png)



***



### 自定义拦截器

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

* 自定义拦截器需要实现HandleInterceptor接口

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

  说明：三个方法的运行顺序为    preHandle -> postHandle -> afterCompletion，如果preHandle返回值为false，三个方法仅运行preHandle

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

### 异常处理器

异常处理器： **HandlerExceptionResolver**接口

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

ControllerAdvice注解：

* 名称：@ControllerAdvice

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

ExceptionHandler注解：

* 名称：@ExceptionHandler

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
  <context:component-scan base-package="com.itheima"/>
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

*  MultipartResolver接口定义了文件上传过程中的相关操作，并对通用性操作进行了封装
*  MultipartResolver接口底层实现类CommonsMultipartResovler
*  CommonsMultipartResovler并未自主实现文件上传下载对应的功能，而是调用了apache文件上传下载组件  

文件上传下载实现：

* 导入坐标

  ```xml
  <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
  </dependency>
  ```

* 页面表单fileupload.jsp

  ```jsp
  <%@page pageEncoding="UTF-8" language="java" contentType="text/html;UTF-8" %>
  
  <form action="/fileupload" method="post" enctype="multipart/form-data">
  <%--文件上传表单的name属性值一定要与controller处理器中方法的参数对应，否则无法实现文件上传--%>
      上传LOGO：<input type="file" name="file"/><br/>
      上传照片：<input type="file" name="file1"/><br/>
      上传任意文件：<input type="file" name="file2"/><br/>
      <input type="submit" value="上传"/>
  </form>
  ```

* web.xml

  ```xml
  DispatcherServlet + CharacterEncodingFilter
  ```

* spring-mvc.xml

  ```xml
  <mvc:annotation-driven/>
  <context:component-scan base-package="com.itheima"/
  <!--配置文件上传处理器-->
  <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
      <property name="maxUploadSize" value="10240000000"/>
  </bean>
  ```

* 控制器

  ```java
  @Controller
  public class FileUploadController {
      @RequestMapping(value = "/fileupload")
  	public void fileupload(MultipartFile file){
          System.out.println("file upload is running ..." + file);
      	file.transferTo(new File("file.png"));
  	}
  }
  ```



***



#### 注意事项

MultipartFile参数中封装了上传的文件的相关信息。

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



***



### Reatful

#### Rest概述

Rest（ REpresentational State Transfer） ：一种网络资源的访问风格，定义了网络资源的访问方式

* 传统风格访问路径
  http://localhost/user/get?id=1
  http://localhost/deleteUser?id=1
* Rest风格访问路径
  http://localhost/user/1

Restful是按照Rest风格访问网络资源

优点：

* 隐藏资源的访问行为，通过地址无法得知做的是何种操作
* 书写简化

Rest行为约定方式：

* GET（查询） http://localhost/user/1 GET

* POST（保存） http://localhost/user POST

* PUT（更新） http://localhost/user PUT

* DELETE（删除） http://localhost/user DELETE

  注意：上述行为是约定方式，约定不是规范，可以打破，所以称Rest风格，而不是Rest规范

***



#### Restful开发

Restful请求路径简化配置方式：**@RestController = @Controller + @ResponseBody**

* restful.jsp
  开启SpringMVC对Restful风格的访问支持过滤器，即可**通过页面表单提交PUT**与DELETE请求
  页面表单使用隐藏域提交请求类型，参数名称固定为_method，必须配合提交类型method=post使用

  * GET请求通过地址栏可以发送，也可以通过设置form的请求方式提交
  * POST请求必须通过form的请求方式提交

  ```html
  <%@page pageEncoding="UTF-8" language="java" contentType="text/html;UTF-8" %>
  <h1>restful风格请求表单</h1>
  <%--切换请求路径为restful风格--%>
  <form action="/user/1" method="post">
      <%--当添加了name为_method的隐藏域时，可以通过设置该隐藏域的值，修改请求的提交方式，切换为PUT请求或DELETE请求，但是form表单的提交方式method属性必须填写post--%>
      <%--该配置需要配合HiddenHttpMethodFilter过滤器使用，单独使用无效，请注意检查web.xml中是否配置了对应过滤器--%>
      <input type="text" name="_method" value="PUT"/>
      <input type="submit"/>
  </form>
  ```


* java / controller / UserController

  ```java
  //@Controller
  //@ResponseBody
  //设置rest风格的控制器
  @RestController
  //设置公共访问路径，配合下方访问路径使用
  @RequestMapping("/user/")
  public class UserController {
  
      //rest风格访问路径完整书写方式
      @RequestMapping("/user/{id}")
      //使用@PathVariable注解获取路径上配置的具名变量，该配置可以使用多次
      public String restLocation(@PathVariable Integer id){
          System.out.println("restful is running ....");
          return "success.jsp";
      }
  
      //rest风格访问路径简化书写方式，配合类注解@RequestMapping使用
      @RequestMapping("{id}")
      public String restLocation2(@PathVariable Integer id){
          System.out.println("restful is running ....get:"+id);
          return "success.jsp";
      }
  
      //接收GET请求配置方式
      @RequestMapping(value = "{id}",method = RequestMethod.GET)
      //接收GET请求简化配置方式
      @GetMapping("{id}")
      public String get(@PathVariable Integer id){
          System.out.println("restful is running ....get:"+id);
          return "success.jsp";
      }
  
      //接收POST请求配置方式
      @RequestMapping(value = "{id}",method = RequestMethod.POST)
      //接收POST请求简化配置方式
      @PostMapping("{id}")
      public String post(@PathVariable Integer id){
          System.out.println("restful is running ....post:"+id);
          return "success.jsp";
      }
  
      //接收PUT请求简化配置方式
      @RequestMapping(value = "{id}",method = RequestMethod.PUT)
      //接收PUT请求简化配置方式
      @PutMapping("{id}")
      public String put(@PathVariable Integer id){
          System.out.println("restful is running ....put:"+id);
          return "success.jsp";
      }
  
      //接收DELETE请求简化配置方式
      @RequestMapping(value = "{id}",method = RequestMethod.DELETE)
      //接收DELETE请求简化配置方式
      @DeleteMapping("{id}")
      public String delete(@PathVariable Integer id){
          System.out.println("restful is running ....delete:"+id);
          return "success.jsp";
      }
  }
  ```

* 配置拦截器 web.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
           version="3.1">
      <filter>
          <filter-name>characterEncodingFilter</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
          <init-param>
              <param-name>encoding</param-name>
              <param-value>UTF-8</param-value>
          </init-param>
      </filter>
      <filter-mapping>
          <filter-name>characterEncodingFilter</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
  
  <!--配置拦截器，解析请求中的参数_method，否则无法发起PUT请求与DELETE请求，配合页面表单使用-->
      <filter>
          <filter-name>HiddenHttpMethodFilter</filter-name>
          <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
      </filter>
      <filter-mapping>
          <filter-name>HiddenHttpMethodFilter</filter-name>
          <servlet-name>DispatcherServlet</servlet-name>
      </filter-mapping>
  
      <servlet>
          <servlet-name>DispatcherServlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
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

  


#### Postman

**postman** 是  一款可以发送Restful风格请求的工具，方便开发调试，首次运行需要联网注册

网址：https://www.postman.com/



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

### 整合流程

整合步骤分析：

SSM（Spring+SpringMVC+MyBatis）

* Spring
  * 框架基础

* MyBatis
  * mysql+druid+pagehelper

* Spring整合MyBatis

* junit测试业务层接口

* SpringMVC
  * rest风格（postman测试请求结果）
  * 数据封装json（jackson）

* Spring整合SpringMVC

  * Controller调用Service

* 其他

  * 表现层数据封装

  * 自定义异常



### 结构搭建

* 创建项目，组织项目结构，创建包

* 创建表与实体类

* 创建三层架构对应的模块、接口与实体类，建立关联关系

* 数据层接口（代理自动创建实现类）

  * 业务层接口+业务层实现类
  * 表现层类

  ![](https://gitee.com/seazean/images/raw/master/Frame/SSM目录结构.png)





***



### 数据准备

* 导入坐标 pom.xml

  ```xml
  <dependencies>
      <!--spring环境-->
      <!--<dependency>
              <groupId>org.springframework</groupId>
              <artifactId>spring-context</artifactId>
              <version>5.1.9.RELEASE</version>
          </dependency>-->
  
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
      <!--<dependency>
              <groupId>com.fasterxml.jackson.core</groupId>
              <artifactId>jackson-core</artifactId>
              <version>2.9.0</version>
          </dependency>
          <dependency>
              <groupId>com.fasterxml.jackson.core</groupId>
              <artifactId>jackson-annotations</artifactId>
              <version>2.9.0</version>
          </dependency>-->
  
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
  //注意：数据层操作不要和业务层操作的名称混淆，通常数据层仅反映与数据库间的信息交换，不体现业务逻辑
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
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
          http://www.springframework.org/schema/beans 
  		http://www.springframework.org/schema/beans/spring-beans.xsd
  		http://www.springframework.org/schema/mvc 
          http://www.springframework.org/schema/mvc/spring-mvc.xsd
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

* Mybatis核心配置：resouces.applicationContext.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:tx="http://www.springframework.org/schema/tx"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
  		http://www.springframework.org/schema/beans 
       	http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/context 	
          http://www.springframework.org/schema/context/spring-context.xsd
          http://www.springframework.org/schema/tx 
          http://www.springframework.org/schema/tx/spring-tx.xsd">
  	<!--开启bean-->
      <context:component-scan base-package="dao,domain,service">
          <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
      </context:component-scan>
  
      <!--事务管理器,开启注解式事务-->
      <tx:annotation-driven transaction-manager="txManager"/>
  
      <!--加载properties文件-->
      <context:property-placeholder location="classpath*:jdbc.properties"/>
  
      <!--数据源-->
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

* 单元测试整合junit

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

* 定义BusinessException

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



#### 返回消息兼容异常信息

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

* 注解替代UserDao映射配置文件：dao.UserDao

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
          //手工调用set方法，等同于set属性注入<property name="driverClassName" value="******"/>
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
      //参数使用自动装配的形式加载dataSource，为set注入提供数据，dataSource来源于JdbcConfig中的配置
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
  //等同于<context:component-scan base-package="com.itheima">
  @ComponentScan(value = {"config","dao","service","system"},excludeFilters =
  //等同于<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
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

* 注解替代spring-mvc.xml：SpringMvcConfig

  ```java
  @Configuration
  //等同于<context:component-scan base-package="com.itheima.controller"/>
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

   1. 支持ConversionService的配置，可以方便配置自定义类型转换器
   2. 支持@NumberFormat注解格式化数字类型
   3. 支持@DateTimeFormat注解格式化日期数据，日期包括Date,Calendar,JodaTime（JodaTime要导包）
   4. 支持@Valid的参数校验(需要导入JSR-303规范)
   5. 配合第三方jar包和SpringMVC提供的注解读写XML和JSON格式数据



***



### web.xml

* 注解替代web.xml：ServletContainersInitConfig

  ```java
  public class ServletContainersInitConfig extends AbstractDispatcherServletInitializer {
  
      //创建Servlet容器时，使用注解的方式加载SPRINGMVC配置类中的信息，并加载成WEB专用的ApplicationContext对象
      //该对象放入了ServletContext范围，后期在整个WEB容器中可以随时获取调用
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
      //基本等同于<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
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
          FilterRegistration.Dynamic registration = servletContext.addFilter("characterEncodingFilter", cef);
          //4.添加映射
          registration.addMappingForUrlPatterns(EnumSet.of(DispatcherType.REQUEST, DispatcherType.FORWARD, DispatcherType.INCLUDE), false, "/*");
      }
  }
  ```

* WebApplicationContext，生成Spring核心容器（主容器/父容器/跟容器）

  * 父容器：Spring环境加载后形成的容器，包含Spring环境下的所有的bean
  * 子容器：当前mvc环境加载后形成的容器，不包含Spring环境下的bean
  * 子容器可以访问父容器中的资源，父容器不可以访问子容器的资源







****





# Boot

## 基本概述

pringBoot提供了一种快速使用Spring的方式，基于约定优于配置的思想，可以让开发人员不必在配置与逻辑业务之间进行思维的切换，全身心的投入到逻辑业务的代码编写中，从而大大提高了开发的效率

SpringBoot功能：

* 自动配置：

  Spring Boot的自动配置是一个运行时（更准确地说，是应用程序启动时）的过程，考虑了众多因素选择使用哪个配置，该过程是SpringBoot自动完成的。

* 起步依赖

  起步依赖本质上是一个Maven项目对象模型（Project Object Model，POM），定义了对其他库的传递依赖，这些东西加在一起即支持某项功能。

  简单的说，起步依赖就是将具备某种功能的坐标打包到一起，并提供一些默认的功能。

* 辅助功能

  提供了一些大型项目中常见的非功能性特性，如嵌入式服务器、安全、指标，健康检测、外部配置等。

**注意：Spring Boot 并不是对 Spring 功能上的增强，而是提供了一种快速使用 Spring 的方式**



***



## 构建工程

普通构建：

1. 创建Maven项目

2. 导入SpringBoot起步依赖

   ```xml
   <!--springboot工程需要继承的父工程-->
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>2.1.8.RELEASE</version>
   </parent>
   
   <dependencies>
       <!--web开发的起步依赖-->
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
   </dependencies>
   ```

3. 定义Controller

   ```java
   @RestController
   public class HelloController {
       @RequestMapping("/hello")
       public String hello(){
           return " hello Spring Boot !";
       }
   }
   ```

4. 编写引导类

   ```java
   //引导类。 SpringBoot项目的入口
   @SpringBootApplication
   public class HelloApplication {
       public static void main(String[] args) {
           SpringApplication.run(HelloApplication.class,args);
       }
   }
   ```

快速构建：

![](https://gitee.com/seazean/images/raw/master/Frame/SpringBoot-IDEA构建工程1.png)

![](https://gitee.com/seazean/images/raw/master/Frame/SpringBoot-IDEA构建工程2.png)





***



## 基本配置

### 起步依赖

- 在spring-boot-starter-parent中定义了各种技术的版本信息，组合了一套最优搭配的技术版本。

- 在各种starter中，定义了完成该功能需要的坐标合集，其中大部分版本信息来自于父工程。

- 我们的工程继承parent，引入starter后，通过依赖传递，就可以简单方便获得需要的jar包，并且不会存在版本冲突等问题



### 配置文件

SpringBoot是基于约定的，很多配置都有默认值，如果想使用自己的配置替换默认配置，可以使用application.properties或者application.yml（application.yaml）进行配置。

1. 默认配置文件名称：application

2. 在同一级目录下优先级为：properties>yml > yaml

例如：配置内置Tomcat的端口

* properties：

  ```properties
  server.port=8080
  ```

* yml：

  ```yaml
  server: port: 8080
  ```

* yaml：

  ```yaml
  server: port: 8080
  ```

  

***



### yaml语法

yml文件优势：

1. YAML配置有序，支持数组，数组中的元素可以是基本数据类型也可以是对象

2. YAML数据在编程语言之间是可移植的

3. YAML匹配敏捷语言的本机数据结构

4. YAML具有一致的模型来支持通用工具

5. YAML支持单程处理

6. YAML具有表现力和可扩展性

7. YAML易于实现和使用

基本语法：

- 大小写敏感

- **数据值前边必须有空格，作为分隔符**

- 使用缩进表示层级关系

- 缩进时不允许使用Tab键，只允许使用空格（各个系统 Tab对应空格数目可能不同，导致层次混乱）

- 缩进的空格数目不重要，只要相同层级的元素左侧对齐即可

- ''#" 表示注释，从这个字符一直到行尾，都会被解析器忽略

  ```yaml
  server: 
  	port: 8080  
      address: 127.0.0.1
  name: abc
  ```



数据格式：

* 对象(map)：键值对的集合。

  ```yaml
  person:  
     name: zhangsan
     age: 20
  # 行内写法
  person: {name: zhangsan}
  ```

* 数组：一组按次序排列的值

  ```yaml
  address:
    - beijing
    - shanghai
  # 行内写法
  address: [beijing,shanghai]
  ```

* 纯量：单个的、不可再分的值

  ```yaml
  msg1: 'hello \n world'  # 单引忽略转义字符
  msg2: "hello \n world"  # 双引识别转义字符
  ```

* 参数引用：

  ```yaml
  name: lisi 
  person:
    name: ${name} # 引用上边定义的name值
  ```



***



### 获取配置

* 注解@Value

  ```java
  @RestController
  public class HelloController {
      @Value("${name}")
      private String name;
  
      @Value("${person.name}")
      private String name2;
  
      @Value("${person.age}")
      private String age;
  
      @Value("${address[0]}")
      private String address1;
  
      @Value("${msg1}")
      private String msg1;
  
      @Value("${msg2}")
      private String msg2;
      
      @RequestMapping("/hello")
      public String hello(){
          System.out.println("所有的数据");
          return " hello Spring Boot !";
      }
  }
  ```

* Evironment对象

  ```java
  @Autowired
  private Environment env;
  
  @RequestMapping("/hello")
  public String hello() {
      System.out.println(env.getProperty("person.name"));
      System.out.println(env.getProperty("address[0]"));
      return " hello Spring Boot !";
  }
  ```

* 注解@ConfigurationProperties 

  **注意**：prefix一定要写

  ```java
  @Component
  @ConfigurationProperties(prefix = "person")
  public class Person {
      private String name;
      private int age;
      private String[] address;
  }
  ```

  ```java
  @Autowired
  private Person person;
  
  @RequestMapping("/hello")
  public String hello() {
      System.out.println(person);
      //Person{name='zhangsan', age=20, address=[beijing, shanghai]}
      return " hello Spring Boot !";
  }
  ```

  

***



### Profile

@Profile：指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件

 * 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
 * 写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效
 * 没有标注环境标识的bean在，任何环境下都是加载的

Profile的配置：

1. **profile是用来完成不同环境下，配置动态切换功能**

2. **profile配置方式**

   多profile文件方式：提供多个配置文件，每个代表一种环境。

   ​		application-dev.properties/yml 开发环境

   ​		application-test.properties/yml 测试环境

   ​		application-pro.properties/yml 生产环境

   yml多文档方式：在yml中使用  --- 分隔不同配置

   ```yaml
   ---
   server:
     port: 8081
   spring:
     profiles:dev
   ---
   server:
     port: 8082
   spring:
     profiles:test
   ---
   server:
     port: 8083
   spring:
     profiles:pro
   ---
   ```

3. **profile激活方式**

- 配置文件：在配置文件中配置：spring.profiles.active=dev

  ```properties
  spring.profiles.active=dev
  ```

- 虚拟机参数：在VM options 指定：`-Dspring.profiles.active=dev`

  ![](https://gitee.com/seazean/images/raw/master/Frame/SpringBoot-profile激活方式虚拟机参数.png)

- 命令行参数：`java –jar xxx.jar  --spring.profiles.active=dev`

  在Program arguments里输入，也可以先package



***



### 加载顺序

项目内部配置文件加载顺序：加载顺序为上文的排列顺序，高优先级配置的属性会生效

- file:./config/：当前项目下的/config目录下

- file:./           ：当前项目的根目录

  ```yaml
  file = Project工程目录
  ```

- classpath:/config/：classpath的/config目录

- classpath:/  ：classpath的根目录

  ```yaml
  classpath = resoureces
  ```

项目外部配置文件加载顺序：外部配置文件的使用是为了对能不文件的配合

* 命令行：在package打包后的target目录下，使用该命令

  ```sh
  java -jar myproject.jar --server.port=9000
  ```

* 指定配置文件位置

  ```sh
  java -jar myproject.jar --spring.config.location=e://application.properties
  ```

* 外部不带profile的properties文件

  ```sh
  classpath:/config/application.properties#优先级更高
  classpath:/application.properties		#和jar同等级的配置文件，无需配置，默认加载
  ```

  加载命令：`java -jar myproject.jar`



***



## 整合框架

### Junit

1. 搭建工程

2. 导入坐标

   ```xml
    <dependencies>
   	<!--不加可能Runwith无法使用-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
   </dependencies>
   ```

3. 测试类

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest(classes = SpringbootJunitApplication.class )
   public class UserServiceTest {
       @Test
       public void test(){
           System.out.println(111);
       }
   }
   ```

   

***



### Mybatis

1. 搭建SpringBoot工程

2. 引入mybatis起步依赖，添加mysql驱动

   ```xml
   <dependencies>
       <dependency>
           <groupId>org.mybatis.spring.boot</groupId>
           <artifactId>mybatis-spring-boot-starter</artifactId>
           <version>2.1.0</version>
       </dependency>
   
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <!--<scope>runtime</scope>  防止编译时报红-->
       </dependency>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   ```

3. 编写DataSource和MyBatis相关配置：application.yml

   ```yaml
   #datasource
   spring:
     datasource:
       url: jdbc:mysql://192.168.0.137:3306/springboot #?serverTimezone=UTC
       username: root
       password: 123456
       driver-class-name: com.mysql.cj.jdbc.Driver
   
   
   #mybatis
   mybatis:
     mapper-locations: classpath:mapper/*Mapper.xml #mapper映射配置文件
     type-aliases-package: com.example.springbootmybatis.domain
   
   
   #  config-location: 指定mybatis的核心配置文件
   ```

4. 定义表和实体类

   ```java
   public class User {
       private int id;
       private String username;
       private String password;
   }
   ```

5. 编写dao和mapper文件/纯注解开发

   编写dao

   ```java
   @Mapper  //必须加Mapper
   @Repository
   public interface UserXmlMapper {
       public List<User> findAll();
   }
   ```

   mapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.itheima.springbootmybatis.mapper.UserXmlMapper">
       <select id="findAll" resultType="user">
           select * from t_user
       </select>
   </mapper>
   ```

6. 纯注解开发

   ```java
   @Mapper
   @Repository
   public interface UserMapper {
       @Select("select * from t_user")
       public List<User> findAll();
   }
   ```



***



### Redis

1. 搭建SpringBoot工程

2. 引入redis起步依赖

   ```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-data-redis</artifactId>
       </dependency>
   
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-test</artifactId>
           <scope>test</scope>
       </dependency>
   </dependencies>
   ```

3. 配置redis相关属性

   ```yaml
   spring:
     redis:
       host: 127.0.0.1 # redis的主机ip
       port: 6379
   ```

4. 注入RedisTemplate模板

   ```java
   @RunWith(SpringRunner.class)
   @SpringBootTest
   public class SpringbootRedisApplicationTests {
       @Autowired
       private RedisTemplate redisTemplate;
   
       @Test
       public void testSet() {
           //存入数据
           redisTemplate.boundValueOps("name").set("zhangsan");
       }
       @Test
       public void testGet() {
           //获取数据
           Object name = redisTemplate.boundValueOps("name").get();
           System.out.println(name);
       }
   }
   ```





***



## 自动配置

### Condition

#### 条件注解

Condition是Spring4.0后引入的条件化配置接口，通过实现Condition接口可以完成有条件的加载相应的Bean

注解：@Conditional

作用：按照一定的条件进行判断，满足条件给容器注册bean

使用：@Conditional要配合Condition的实现类（ClassCondition）进行使用

ConditionContext类API：

| 方法                                                | 说明                       |
| --------------------------------------------------- | -------------------------- |
| ConfigurableListableBeanFactory  getBeanFactory（） | 获取到ioc使用的beanfactory |
| ClassLoader getClassLoader()                        | 获取类加载器               |
| Environment getEnvironment()                        | 获取当前环境信息           |
| BeanDefinitionRegistry getRegistry()                | 获取到bean定义的注册类     |

* ClassCondition

  ```java
  public class ClassCondition implements Condition {
      /**
       * context 上下文对象。用于获取环境，IOC容器，ClassLoader对象
       * metadata 注解元对象。 可以用于获取注解定义的属性值
       */
      @Override
      public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        
          //1.需求： 导入Jedis坐标后创建Bean
          //思路：判断redis.clients.jedis.Jedis.class文件是否存在
          boolean flag = true;
          try {
              Class<?> cls = Class.forName("redis.clients.jedis.Jedis");
          } catch (ClassNotFoundException e) {
              flag = false;
          }
          return flag;
      }
  }
  ```

* UserConfig

  ```java
  @Configuration
  public class UserConfig {
      @Bean
      @Conditional(ClassCondition.class)
      public User user(){
          return new User();
      }
  }
  ```

* 启动类：

  ```java
  @SpringBootApplication
  public class SpringbootConditionApplication {
      public static void main(String[] args) {
          //启动SpringBoot应用，返回Spring的IOC容器
          ConfigurableApplicationContext context = SpringApplication.run(SpringbootConditionApplication.class, args);
  
          Object user = context.getBean("user");
          System.out.println(user);
      }
  }
  ```



***



#### 自定义注解

需求：将类的判断定义为动态的。判断哪个字节码文件存在可以动态指定。

* 自定义条件注解类

  ```java
  @Target({ElementType.TYPE, ElementType.METHOD})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Conditional(ClassCondition.class)
  public @interface ConditionOnClass {
      String[] value();
  }
  ```

* ClassCondition

  ```java
  public class ClassCondition implements Condition {
      @Override
      public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata metadata) {
  
          //需求：通过注解属性值value指定坐标后创建bean
          Map<String, Object> map = metadata.getAnnotationAttributes
              					(ConditionOnClass.class.getName());
          //map = {value={属性值}}
          //获取所有的
          String[] value = (String[]) map.get("value");
  
          boolean flag = true;
          try {
              for (String className : value) {
                  Class<?> cls = Class.forName(className);
              }
          } catch (Exception e) {
              flag = false;
          }
          return flag;
      }
  }
  ```

* UserConfig

  ```java
  @Configuration
  public class UserConfig {
      @Bean
      @ConditionOnClass("com.alibaba.fastjson.JSON")
      public User user(){
          return new User();
      }
  }
  ```

* 测试User对象的创建



***



#### 常用注解

SpringBoot 提供的常用条件注解：

ConditionalOnProperty：判断**配置文件**中是否有对应属性和值才初始化Bean

```java
@Configuration
public class UserConfig {
    @Bean
    @ConditionalOnProperty(name = "itcast",havingValue = "itheima")
    public User user() {
        return new User();
    }
}
```

```properties
itcast=itheima
```

ConditionalOnClass：判断环境中是否有对应字节码文件才初始化Bean

ConditionalOnMissingBean：判断环境中没有对应Bean才初始化Bean





***



### 内置web

pom文件中的排除依赖效果

```xml
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-web</artifactId>
     <!--排除tomcat依赖-->
     <exclusions>
         <exclusion>
             <artifactId>spring-boot-starter-tomcat</artifactId>
             <groupId>org.springframework.boot</groupId>
         </exclusion>
     </exclusions>
</dependency>

<!--引入jetty的依赖-->
<dependency>
    <artifactId>spring-boot-starter-jetty</artifactId>
    <groupId>org.springframework.boot</groupId>
</dependency>
```



***



### Enable

SpringBoot不能直接获取在其他工程中定义的Bean（pom导入springboot-enable-other坐标）

@ComponentScan 扫描范围：当前引导类所在包及其子包

* 所在包：com.example.springbootenable;
* 配置包：com.example.config;

三种解决办法：

1. 使用@ComponentScan扫描com.example.config包
2. 使用Import注解，加载类，这些类都会被Spring创建并放入ioc容器
3. 对Import注解进行封装
   **重点：Enable注解底层原理是使用@Import注解实现Bean的动态加载**

```java
//1.@ComponentScan("com.example.config")
//2.@Import(UserConfig.class)
@EnableUser
@SpringBootApplication
public class SpringbootEnableApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(SpringbootEnableApplication.class, args);
    	//获取Bean
        Object user = context.getBean("user");
        System.out.println(user);

	}
}
```

**UserConfig：**

```java
@Configuration
public class UserConfig {
    @Bean
    public User user() {
        return new User();
    }
}
```

**EnableUser注解类：**

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(UserConfig.class)//@Import注解实现Bean的动态加载
public @interface EnableUser {
}
```



***



### Import

@Enable底层依赖于@Import注解导入一些类，使用@Import导入的类会被Spring加载到IOC容器中
@Import提供4中用法：

1. 导入Bean：`@Import(User.class)`

2. 导入配置类：`@Import(UserConfig.class)`

3. 导入 ImportSelector 实现类，一般用于加载配置文件中的类

   MyImportSelector，配置在springboot-enable-other项目，环境采用Enable，

   ```java
   public class MyImportSelector implements ImportSelector {
       @Override
       public String[] selectImports(AnnotationMetadata importingClassMetadata) {
           return new String[]{"com.example.domain.User",
                               "com.example.domain.Role"};
       }
   }
   ```

   ```java
   @Import(MyImportSelector.class)
   @SpringBootApplication
   public class SpringbootEnableApplication {
   
       public static void main(String[] args) {
           ConfigurableApplicationContext context = SpringApplication.run(SpringbootEnableApplication.class, args);
       	//获取Bean
           Object user = context.getBean("user");
           System.out.println(user);
   
   	}
   }
   ```

4. 导入 ImportBeanDefinitionRegistrar实现类：@Import({MyImportBeanDefinitionRegistrar.class})

   ```java
   public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
       @Override
       public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
           AbstractBeanDefinition beanDefinition = BeanDefinitionBuilder.rootBeanDefinition(User.class).getBeanDefinition();
           registry.registerBeanDefinition("user",beanDefinition);
       }
   }
   ```

   

***



### EAConfig

注解：@SpringBootApplication

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
}
```

注解：@EnableAutoConfiguration

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage	
@Import(AutoConfigurationImportSelector.class)//Import第三种方式
public @interface EnableAutoConfiguration {
}
```

![](https://gitee.com/seazean/images/raw/master/Frame/SpringBoot-解析EnableAutoConfiguration.png)

- @EnableAutoConfiguration 注解内部使用 @Import(AutoConfigurationImportSelector.**class**)来加载配置类。 

- 配置文件位置：META-INF/spring.factories，该配置文件中定义了大量的配置类，当 SpringBoot 应用启动时，会自动加载这些配置类，初始化Bean

- 并不是所有的Bean都会被初始化，在配置类中使用Condition来加载满足条件的Bean



***



### starter

#### 步骤分析

需求：自定义redis-starter，要求当导入redis坐标时，SpringBoot自动创建Jedis的Bean

步骤：

* redis-spring-boot-autoconfigure 模块

* 创建 redis-spring-boot-starter 模块，依赖 redis-spring-boot-autoconfigure的模块

* 在redis-spring-boot-autoconfigure模块中初始化Jedis的Bean，并定义META-INF/spring.factories文件

* 在测试模块中引入自定义的 redis-starter 依赖，测试获取 Jedis 的Bean，操作 redis



#### 功能实现

* 创建redis-spring-boot-starter工程

  ```xml
   <!--引入configure-->
   <dependency>
       <groupId>com.example</groupId>
       <artifactId>redis-spring-boot-autoconfigure</artifactId>
       <version>0.0.1-SNAPSHOT</version>
   </dependency>
  ```

* 创建redis-spring-boot-autoconfigure配置工程

  创建RedisProperties配置文件参数绑定类：

  ```java
  @ConfigurationProperties(prefix = "redis")
  //读取配置文件中redis下的配置，封装到RedisProperties
  public class RedisProperties {
      private String host = "127.0.0.1";//配置文件没有配置的情况下的默认值
      private int port = 6379;		//默认值
      
  }
  ```

  创建RedisAutoConfiguration自动配置类：

  ```java
  @Configuration
  @EnableConfigurationProperties(RedisProperties.class)
  public class RedisAutoConfiguration {
      //提供Jedis的bean
      @Bean
      public Jedis jedis(RedisProperties redisProperties) {
          return new Jedis(redisProperties.getHost(),redisProperties.getPort());
  
      }
  }
  ```

  在resource目录下创建META-INF文件夹并创建spring.factories

  注意：”\ “是换行使用的

  ```properties
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    com.example.redis.config.RedisAutoConfiguration
  ```

  在springboot-enable工程中引入自定义的redis的starter，进行测试：

  ```java
  Jedis jedis = context.getBean(Jedis.class);
  System.out.println(jedis);
  ```

  

***



### actuator

SpringBoot监控actuator基本使用

1. 导入依赖坐标

   ```xml
   <dependency>
   	<groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   ```

2. 访问http://localhost:8080/acruator，json字符串转换

   ```json
   {
       "_links":{
           "self":{
               "href":"http://localhost:8080/actuator",
               "templated":false
           },
           "health":{
               "href":"http://localhost:8080/actuator/health",
               "templated":false
           },
           "health-component-instance":{
               "href":"http://localhost:8080/actuator/health/{component}/{instance}",
               "templated":true
           },
           "health-component":{
               "href":"http://localhost:8080/actuator/health/{component}",
               "templated":true
           },
           "info":{
               "href":"http://localhost:8080/actuator/info",
               "templated":false
           }
       }
   }
   ```

   * http://localhost:8080/actuator/info

     在application.properties中配置

     ```properties
     info.name=lucy
     info.age=99
     ```

   * http://localhost:8080/actuator/health

     开启健康检查详细信息

     ```properties
     management.endpoint.health.show-details=always
     ```

3. 开启所有endpoint，在application.properties中配置：

   ```properties
   management.endpoints.web.exposure.include=*
   ```

   

***



### admin

SpringBoot监控-springboot admin图形化界面使用：

SpringBoot Admin 有两个角色，客户端(Client)和服务端(Server)。

admin-server：

1. 创建 admin-server 模块

2. 导入依赖坐标 admin-starter-server，web

   ![](https://gitee.com/seazean/images/raw/master/Frame/SpringBoot-监控界面admin-server依赖导入.png)

3. 在引导类上启用监控功能@EnableAdminServer

   ```java
   @EnableAdminServer
   @SpringBootApplication
   public class SpringbootAdminServerApplication {
       public static void main(String[] args) {
           SpringApplication.run(SpringbootAdminServerApplication.class, args);
       }
   }
   ```

admin-client：

1. 创建 admin-client 模块

2. 导入依赖坐标 admin-starter-client

3. 配置相关信息：server地址等

   ```properties
   # 执行admin.server地址
   spring.boot.admin.client.url=http://localhost:9000
   
   management.endpoint.health.show-details=always
   management.endpoints.web.exposure.include=*
   ```

4. 启动server和client服务，访问server



***



### 事件监听

 Java中的事件监听机制定义了以下几个角色：

* 事件：Event，继承 java.util.EventObject 类的对象

* 事件源：Source ，任意对象Object

* 监听器：Listener，实现 java.util.EventListener 接口 的对象

SpringBoot 在项目启动时，会对几个监听器进行回调，可以实现监听器接口，在项目启动时完成一些操作

ApplicationContextInitializer, SpringApplicationRunListener, CommandLineRunner, ApplicationRunner

* MyApplicationRunner

  **自定义监听器的启动时机**：MyApplicationRunner和MyCommandLineRunner都是当项目启动后执行，使用@Component放入容器即可使用

  ```java
  //当项目启动后执行run方法
  @Component
  public class MyApplicationRunner implements ApplicationRunner {
      @Override
      public void run(ApplicationArguments args) throws Exception {
          System.out.println("ApplicationRunner...run");
          System.out.println(Arrays.asList(args.getSourceArgs()));//properties配置信息
      }
  } 
  ```

* MyCommandLineRunner

  ```java
  @Component
  public class MyCommandLineRunner implements CommandLineRunner {
      @Override
      public void run(String... args) throws Exception {
          System.out.println("CommandLineRunner...run");
          System.out.println(Arrays.asList(args));
      }
  }
  ```

* MyApplicationContextInitializer的启用要**在resource文件夹下添加META-INF/spring.factories**

  ```properties
  org.springframework.context.ApplicationContextInitializer=com.example.springbootlistener.listener.MyApplicationContextInitializer
  ```

  ```java
  @Component
  public class MyApplicationContextInitializer implements ApplicationContextInitializer {
      @Override
      public void initialize(ConfigurableApplicationContext applicationContext) {
          System.out.println("ApplicationContextInitializer....initialize");
      }
  }
  ```

* MySpringApplicationRunListener的使用要添加**构造器**

  ```java
  public class MySpringApplicationRunListener implements SpringApplicationRunListener {
  	//构造器
      public MySpringApplicationRunListener(SpringApplication sa, String[] args) {
      }
  
      @Override
      public void starting() {
          System.out.println("starting...项目启动中");//输出SPRING之前
      }
  
      @Override
      public void environmentPrepared(ConfigurableEnvironment environment) {
          System.out.println("environmentPrepared...环境对象开始准备");
      }
  
      @Override
      public void contextPrepared(ConfigurableApplicationContext context) {
          System.out.println("contextPrepared...上下文对象开始准备");
      }
  
      @Override
      public void contextLoaded(ConfigurableApplicationContext context) {
          System.out.println("contextLoaded...上下文对象开始加载");
      }
  
      @Override
      public void started(ConfigurableApplicationContext context) {
          System.out.println("started...上下文对象加载完成");
      }
  
      @Override
      public void running(ConfigurableApplicationContext context) {
          System.out.println("running...项目启动完成，开始运行");
      }
  
      @Override
      public void failed(ConfigurableApplicationContext context, Throwable exception) {
          System.out.println("failed...项目启动失败");
      }
  }
  ```

  

***



### 初始化

1. 配置启动引导类（判断是否有启动主类），判断是否是Web环境，获取初始化类、监听器类

   ![](https://gitee.com/seazean/images/raw/master/Frame/SpringBoot-初始化.png)

2. 启动计时器

3. 执行监听器

4. 准备环境

5. 打印banner：可以resource下粘贴自定义的banner

6. 创建context：`refreshContext(context);`，执行refreshContext方法后才真正创建Bean

   ![](https://gitee.com/seazean/images/raw/master/Frame/SpringBoot-初始化run方法.png)





***



## 部署

SpringBoot 项目开发完毕后，支持两种方式部署到服务器：

* jar包 (官方推荐，默认)
* war包

**更改pom文件中的打包方式为war**

* 修改启动类

  ```java
  @SpringBootApplication
  public class SpringbootDeployApplication extends SpringBootServletInitializer {
      public static void main(String[] args) {
          SpringApplication.run(SpringbootDeployApplication.class, args);
      }
  
      @Override
      protected SpringApplicationBuilder configure(SpringApplicationBuilder b) {
          return b.sources(SpringbootDeployApplication.class);
      }
  }
  ```

* 指定打包的名称

  ```xml
  <packaging>war</packaging>
  <build>
       <finalName>springboot</finalName>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
  </build>
  ```

  

