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
     * groupId：定义当前资源隶属组织名称（通常是域名反写，如：org.mybatis；com.seazean）
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

![](https://gitee.com/seazean/images/raw/master/Frame/Maven模块划分.png)

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
      <groupId>com.seazean</groupId>
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





# Netty



