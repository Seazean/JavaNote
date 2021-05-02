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







# Dubbo

## 相关概念

### 互联网架构

**衡量网站的性能指标：**

* 响应时间：指执行一个请求从开始到最后收到响应数据所花费的总体时间

* 并发数：指系统同时能处理的请求数量

* 并发连接数：指的是客户端向服务器发起请求，并建立了TCP连接。每秒钟服务器连接的总TCP数量

* 请求数：也称为QPS(Query Per Second)指每秒多少请求

* 并发用户数：单位时间内有多少用户

* 吞吐量：指单位时间内系统能处理的请求数量。

  ```java
  QPS: Query Per Second每秒查询数。
  TPS: Transactions Per Second每秒事务数。
  ●一个事务是指一 个客户机向服务器发送请求然后服务器做出反应的过程。客户机在发送请求时开始计时，收到服务器响应后结束计时，以此来计算使用的时间和完成的事务个数。
  ●一个页面的一次访问，只会形成一个TPS; 但1次页面请求，可能产生多次对服务器的请求，就会有多个QPS: QPS>=并发连接数>= TPS
  ```

  

**大型互联网项目架构目标：**

* 高性能：提供快速的访问体验。
* 高可用：网站服务- 可以正常访问



***



### 架构演进

#### 单体架构

**单体架构**的优点：简单，开发部署都很方便，小型项目首选

单体架构存在的问题：

- 项目启动慢

- 可靠性差

- 可伸缩性差

- 扩展性和可维护性差

- 性能低

![](https://gitee.com/seazean/images/raw/master/Frame/Dubbo-单体架构.png)

#### 垂直架构

**垂直架构**：指将单体架构中的多个模块拆分为多个独立的项目，形成多个独立的单体架构

垂直架构存在的问题：重复功能太多

![](https://gitee.com/seazean/images/raw/master/Frame/Dubbo-垂直架构.png)



#### 分布式架构

**分布式架构**：在垂直架构的基础上，将公共业务模块抽取出来。作为独立的服务供其他调用者消费，以实现服务的共享和重用，底层通过RPC（远程过程调用实现）

RPC：Remote Procedure Call 远程过程调用。有非常多的协议和技术来都实现了RPC的过程。比如: HTTP REST风格，Java RMI规范、WebService SOAP协议Hession等

分布式架构存在的问题：服务提供方一旦产生变更，所有消费方都需要变更

![](https://gitee.com/seazean/images/raw/master/Frame/Dubbo-分布式架构.png)

#### SOA架构

**SOA (Service- Oriented Architecture，面向服务的架构)**：是一个组件模型，将应用程序的不同功能单元 (称为服务) 进行拆分，并通过这些服务之间定义良好的接口和契约联系起来

**ESB (Enterparise Servce Bus)**：企业服务总线，服务中介，主要是提供了一个服务于服务之间的交互。ESB包含的功能如：负载均衡、流量控制、加密处理、服务的监控、异常处理，监控告急等等

![Dubbo-SOA架构](https://gitee.com/seazean/images/raw/master/Frame/Dubbo-SOA架构.png)



#### 微服务架构

**微服务架构**：在SOA上做的提升，微服务架构强调的重点是“业务需要彻底的组件化和服务化”，原有的单个业务系统会拆分为多个可以独立开、运行的小应用，这些小应用之间通过服务完成交互和集成。

特点：

* 微服务架构 = 80%的SOA服务架构思想 + 100%的组件化架构思想 + 80%的领域建模思想

* 服务实现组件化：开发者可以自由选择开发技术。也不需要协调其他团队
* 服务之间交互一 般使用REST API
* 去中心化：每个微服务有自己私有的数据库持久化业务数据
* 自动化部署：把应用拆分成为一 个个独立的单个服务，方便自动化部署、测试、运维

![](https://gitee.com/seazean/images/raw/master/Frame/Dubbo-微服务架构.png)





***



## Dubbo

分布式：通过网络连接的多个组件，通过交换信息协作而形成的系统。简单说是一个业务分拆多个子业务，部署在不同的服务器上。

集群：同一种组件的多个实例，形成的逻辑上的整体。简单说是同一个业务，部署在多个服务器上。

Dubbo概念：

* Dubbo是阿里巴巴公司开源的一个高性能、轻量级的Java RPC框架。
* 致力于提供高性能和透明化的RPC远程服务调用方案，以及SOA服务治理方案。
* 官网: https://dubbo.apache.org/zh/

![](https://gitee.com/seazean/images/raw/master/Frame/Dubbo架构图.png)

节点角色说明：

* Provider：暴露服务的服务提供方
* Contahier：服务运行容器
* Consumer：调用远程服务的服务消费方
* Registry：服务注册与发现的注册中心
* Monitor：统计服务的调用次数和调用时间的监控中心

Dubbo目前支持4种**注册中心**：multicast、zookeeper、redis、simple 

安装zookeeper ：

* 第一步：安装JDK

* 第二步：把 zookeeper 的压缩包（zookeeper-3.4.6.tar.gz）上传到 linux 系统

* 第三步：解压缩压缩包

  ```sh
  tar -zxvf  zookeeper-3.4.6.tar.gz
  ```

* 第四步：进入zookeeper-3.4.6目录，创建data目录

  ```sh
  mkdir data
  ```

* 第五步：进入conf目录 ，把zoo_sample.cfg 改名为zoo.cfg

  ```sh
  cd conf
  mv zoo_sample.cfg zoo.cfg
  ```

* 第六步：打开zoo.cfg文件,  修改data属性

  ```sh
  dataDir=/root/zookeeper-3.4.6/data
  ```

* 第七步：进入ZooKeeper的bin目录

  ```sh
  ./zkServer.sh start		#启动服务命令:STARTED
  ./zkServer.sh stop		#停止服务命令
  ./zkServer.sh status	#查看服务状态:standalone 单节点
  ```

  

***



## 基本操作

### SS整合

Spring和SpringMVC整合步骤：
	1.创建服务提供者Provider模块
	2.创建服务消费者Consumer模块
	3.在服务提供者模块编写UserServicelmpl提供服务
	4.在服务消费者中的UserC ontroller远程调用
	5.UserServicelmpl提供的服务
	6.分别启动两个服务，测试

Dubbo作为一个RPC框架，其最核心的功能就是要**实现跨网络的远程调用**。创建两个应用，一个作为服务的提供方，一个作为服务的消费方，通过Dubbo来实现服务消费方远程调用服务提供方的方法。

1. 服务提供方开发

   （1）创建maven工程（打包方式为war）dubbodemo_provider，在pom.xml文件中导入如下坐标

   ```xml
   <properties>
       <spring.version>5.1.9.RELEASE</spring.version>
       <dubbo.version>2.7.4.1</dubbo.version>
       <zookeeper.version>4.0.0</zookeeper.version>
   </properties>
   
   <dependencies>
       <!-- servlet3.0规范的坐标 -->
       <!--spring的坐标-->
       <!--springmvc的坐标-->
       <!--日志  配置方式：Myabtis.基本操作-->
       <!--Dubbo的起步依赖，版本2.7之后统一为rg.apache.dubbo -->
       <dependency>
           <groupId>org.apache.dubbo</groupId>
           <artifactId>dubbo</artifactId>
           <version>${dubbo.version}</version>
       </dependency>
       <!--ZooKeeper客户端实现 -->
       <dependency>
           <groupId>org.apache.curator</groupId>
           <artifactId>curator-framework</artifactId>
           <version>${zookeeper.version}</version>
       </dependency>
       <!--ZooKeeper客户端实现 -->
       <dependency>
           <groupId>org.apache.curator</groupId>
           <artifactId>curator-recipes</artifactId>
           <version>${zookeeper.version}</version>
       </dependency>
   </dependencies>
   <build>
       <plugins>
           <!--tomcat插件-->
       </plugins>
   </build>
   ```

   （2）配置web.xml文件

   ```xml
   <!-- spring -->
   <context-param>
       <param-name>contextConfigLocation</param-name>
       <param-value>classpath*:spring/applicationContext*.xml</param-value>
   </context-param>
   <listener>
       <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
   </listener>
   ```

   （3）创建服务接口

   ```java
   public interface HelloService {
       public String sayHello(String name);
   }
   ```

   （4）创建服务实现类

   **注意：**服务实现类上使用的Service注解是Dubbo提供的，用于对外发布服务

   ```java
   import com.alibaba.dubbo.config.annotation.Service;
   import service.HelloService;
   
   //@Service，Spring类注解，将该类对象创建，放到Spring的IOC容器中
   @Service //Dubbo注解
   public class HelloServiceImpl implements HelloService {
       public String sayHello(String name) {
           return "hello " + name;
       }
   }
   ```

   （5）tomcat7:run

2. 服务消费方开发

   （1）创建maven工程（打包方式为war）dubbodemo_consumer，pom.xml配置同上，将Tomcat插件的端口号修改，防止冲突

   （2）配置web.xml文件

   ```xml
   <!-- Spring-mvc -->
   <servlet>
       <servlet-name>springmvc</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!-- 指定加载的配置文件 ，通过参数contextConfigLocation加载-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:spring/springmvc.xml</param-value>
       </init-param>
   </servlet>
   
   <servlet-mapping>
       <servlet-name>springmvc</servlet-name>
       <url-pattern>*.do</url-pattern>
   </servlet-mapping>
   ```

   （3）将服务提供者工程中的HelloService接口复制到当前工程，java.service.HelloService

   ​		一般**创建dubbodemo_interface模块，**把所有接口放入其中，让其他模块依赖接口模块

   （4）编写Controller

   注意：Controller中注入HelloService使用的是Dubbo提供的@Reference注解

   ```java
   @RestController
   @RequestMapping("/demo")
   public class HelloController {
       //@Autowired//本地注入
       /*
           1.从zookeeper注册中心获取UserService的访问url
           2.进行远程调用RPC
           3.将结果封装为一个代理对象，给变量赋值
        */
       @Reference//远程注入
       private HelloService helloService;
       
       @RequestMapping("/hello")
       public String getName(String name){
           //远程调用
           String result = helloService.sayHello(name);
           System.out.println(result);
           return result;
       }
   }
   ```

3. 执行流程：
   先install dubbodemo_provider，然后执行dubbodemo_consumer tomcat7:run



***



### 服务提供者

在dubbodemo_provider工程中src/main/resources下创建applicationContext-service.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xmlns:p="http://www.springframework.org/schema/p"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
	    xmlns:mvc="http://www.springframework.org/schema/mvc"
		xsi:schemaLocation="http://www.springframework.org/schema/beans
		http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/mvc
         http://www.springframework.org/schema/mvc/spring-mvc.xsd
         http://code.alibabatech.com/schema/dubbo
         http://code.alibabatech.com/schema/dubbo/dubbo.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd">
	<!-- 当前应用名称，用于注册中心计算应用间依赖关系，注意：消费者和提供者应用名不要一样 -->
	<dubbo:application name="dubbodemo_provider" />
	<!-- 连接服务注册中心zookeeper ip为zookeeper所在服务器的ip地址-->
	<dubbo:registry address="zookeeper://192.168.0.137:2181"/>
	<!-- 注册协议和port   端口默认是20880 -->
	<dubbo:protocol name="dubbo" port="20881"></dubbo:protocol>
    
	<!-- 扫描指定包，加上@Service注解的类会被发布为服务  -->
	<dubbo:annotation package="service.impl" />
</beans>
```





### 服务消费者

在dubbodemo_consumer工程中src/main/resources下创建applicationContext-web.xml

```xml
<beans .....>

	<!-- 当前应用名称，用于注册中心计算应用间依赖关系，注意:消费者和提供者应用名不要一样-->
	<dubbo:application name="dubbodemo-consumer" />
	<!-- 连接服务注册中心zookeeper ip为zookeeper所在服务器的ip地址-->
	<dubbo:registry address="zookeeper://192.168.0.137:2181"/>
	<!-- 包扫描的方式，引用服务，扫描@Reference -->
	<dubbo:annotation package="controller" />
</beans>
```

运行测试：tomcat7:run启动

在浏览器输入http://localhost:8082/demo/hello.do?name=Jack，查看浏览器输出结果





****



## 高级特性

### admin

dubbo-admin安装：

dubbo-admin使用：





### 序列化

dubbo 内部已经将序列化和反序列化的过程内部封装了，我们只需要在定义pojo类时**实现serializable接口**即可，一般会定义一个公共的pojo模块，让生产者和消费者都依赖该模块。

```java
public class User implements Serializable
```





### 地址缓存

注册中心关闭，服务是否可以正常访问？

1.  可以，因为dubbo服务消费者在第一次调用时，会将服务提供方地址缓存到本地，以后在调用则不会访问注册中心
2.  当服务提供者地址发生变化时，注册中心会通知服务消费者



****



### 超时重传

超时原因：

- 服务消费者在调用服务提供者的时候发生了阻塞、等待的情形,服务消费者会直等待下去
- 在某个峰值时刻，大量的请求都在同时请求服务消费者，会造成线程的大量堆积，势必会造成雪崩

解决方法：

- dubbo利用超时机制来解决这个问题，设置一个超时时间，在这个时间段内，无法完成服务访问，则自动断开连接
- 使用timeout属性配置超时时间，默认值1000，单位毫秒
- 配置服务提供者类

```java
//timeout 超时时间 单位毫秒 
@Service(timeout = 3000)
```



重传原因：

* 设置了超时时间，在这个时间段内，无法完成服务访问，则自动断开连接
* 如果出现网络抖动，则这一次请求就会失败

解决方法：

* Dubbo提供重试机制来避免类似问题的发生
* 通过retries属性来设置重试次数，默认为2次

```java
//timeout 超时时间 单位毫秒  retries 重试次数
@Service(timeout = 3000,retries=0)
```



***



### 多版本

**灰度发布：**当出现新功能会让一部分用户先使用新功能，用户反馈没问题，再将所有用户迁移到新功能

dubbo中使用version属性来设置和调用同一个接口的不同版本

生产者配置：

```java
@Service(version="v2.0")
public class UserServiceImp12 implements UserService {...}
```

消费者配置：

```java
@RestController
@RequestMapping("/user")
public class UserController {
    @Reference(version = "v2.0")//远程注入
    private UserService userService;
    //......
}
```



***



### 负载均衡

**负载均衡策略(4种) :** 

* Random：按权重设置随机概率，是负载均衡策略的默认值

* RoundRobin：按权重轮询
* LeastActive：最少活跃调用数，相同活跃数的随机

* ConsistentHash：一 致性Hash，相同参数的请求总是发到同一提供者

<img src="https://gitee.com/seazean/images/raw/master/Frame/Dubbo-负载均衡图.png" style="zoom:33%;" />

服务提供者配置：

```java
@Service(weight = 100)
public class UserServiceImp12 implements UserService {...}
```

配置applicationContext.xml

```xml
<dubbo:protocol port="20883"/>
<!--dubbo配置-->
<!--1.配置项目的名称，唯一-->
<dubbo:application name="dubbo-service">
    <dubbo:parameter key="qos.port" value="11111更改为不冲突的"/>
</dubbo:application>
<!--2.配置注册中心的地址，目前是zookeeper-->
<dubbo:registry address="zookeeper://192.168.0.137:2181"/>
<!--3.配置dubbo包扫描-->
<dubbo:annotation package="service.impl"/>
```

消费者配置：

```java
@RestController
@RequestMapping("/user")
public class UserController {
    //@Reference(loadbalance = "roundrobin")
    //@Reference(loadbalance = "leastactive")
    //@Reference(loadbalance = "consistenthash")
    @Reference(loadbalance = "random")//默认 按权重随机
    private UserService userService;
    //.....
}
```



***



### 集群容错

**集群容错模式：**

* Failover Cluster：失败重试，当出现失败，重试其它服务器，默认重试2次，使用retries配置，是模式的默认值，一般用于读操作
* Failfast Cluster：快速失败，发起一次调用，失败立即报错，通常用于写操作
* Failsafe Cluster：失败安全，出现异常时，直接忽略，返回一个空结果
* Failback Cluster：失败自动恢复，后台记录失败请求，定时重发
* Forking Cluster：并行调用多个服务器，只要一个成功即返回
* Broadcast Cluster：广播调用所有提供者，逐个调用，任意一台报错则报错

消费者配置：

```java
@Reference(cluster = "failover")//远程注入
private UserService userService;
```



***



### 服务降级

**服务降级**：当服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理或换种简单的方式处理，从而释放服务器资源以保证**核心交易**正常运作或高效运作

**服务降级方式：**

* mock= force:return null：表示消费方对该服务的方法调用都直接返回null值，不发起远程调用。用来屏蔽不重要服务不可用时对调用方的影响

* mock=fail:return null：表示消费方对该服务的方法调用在失败后，再返回null值，不抛异常。用来容忍不重要服务不稳定时对调用方的影响

消费方配置：

```java
//远程注入
@Reference(mock ="force:return null")//不再调用userService的服务
private UserService userService;
```







***







# ZooKeeper

## 基本概述

Zookeeper 是 Apache Hadoop 项目下的一个子项目，是一个树形目录服务

Zookeeper 翻译为动物园管理员，用来管理 Hadoop(大象)、Hive(蜜蜂)、Pig(小猪)的管理员，简称zk

Zookeeper 是一个分布式的、开源的分布式应用程序的协调服务

Zookeeper 提供的主要功能包括：

* 配置管理

* 分布式锁

* 集群管理



安装配置：

安装：Dubbo章节详解了安装步骤





***



## 命令操作

### 数据模型

ZooKeeper 是一个树形目录服务，其数据模型和Unix的文件系统目录树很类似，拥有一个层次化结构，这里面的每一个节点都被称为：ZNode，每个节点上都会保存自己的数据和节点信息。节点可以拥有子节点，同时也允许少量（1MB）数据存储在该节点之下。

节点可以分为四大类：

* PERSISTENT 持久化节点 
* EPHEMERAL 临时节点 ：-e
* PERSISTENT_SEQUENTIAL 持久化顺序节点 ：-s
* EPHEMERAL_SEQUENTIAL 临时顺序节点  ：-es

![](https://gitee.com/seazean/images/raw/master/Frame/ZooKeeper树形目录节点.png)



***



### 服务端命令

启动 ZooKeeper 服务：`./zkServer.sh start`

查看 ZooKeeper 服务：`./zkServer.sh status`

停止 ZooKeeper 服务：`./zkServer.sh stop`

重启 ZooKeeper 服务：`./zkServer.sh restart `





### 客户端命令

连接ZooKeeper服务端：

```shell
./zkCli.sh –server ip:port
```

断开连接：

```shell
quit
```

查看命令帮助：

```shell
help
```

显示指定目录下节点：

```sh
ls /目录  (/代表根目录)
```

创建节点：

```sh
create /节点path (可选value)
```

获取节点值：

```sh
get /节点path
```

设置节点值

```sh
set /节点path value
```

删除单个节点

```sh
delete /节点path
```

删除带有子节点的节点

```sh
deleteall /节点path
```

创建临时节点：

```sh
create -e /节点path value
```

创建顺序节点

```sh
create -s /节点path value
```

创建临时顺序节点：

```sh
create -es /节点path value    #app10000012 删除12后也会继续从13开始，只会增加
```

查询节点详细信息

```sh
ls –s /节点path 
#属性
czxid：节点被创建的事务ID 
ctime: 创建时间 
mzxid: 最后一次被更新的事务ID 
mtime: 修改时间 
pzxid：子节点列表最后一次被更新的事务ID
cversion：子节点的版本号 
dataversion：数据版本号 
aclversion：权限版本号 
ephemeralOwner：用于临时节点，代表临时节点的事务ID，如果为持久节点则为0 
dataLength：节点存储的数据的长度 
numChildren：当前节点的子节点个数 
```



***



## JavaAPI

### Curator

Curator 是 Apache ZoCoKeeper 的Java客户端库。

常见的ZooKeeper Java API：原生Java API、ZkClient、Curator

Curator 项目的目标是简化 ZooKeeper 客户端的使用

官网：http://curator.apache.org/



***



### 建立连接

#### 搭建建构

搭建Maven项目架构：

第一步：导入坐标：

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.10</version>
        <scope>test</scope>
    </dependency>

    <!--curator-->
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>4.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
        <version>4.0.0</version>
    </dependency>
    
    <!--日志，properties文件参考Maven-->
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
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```



#### 建立连接

创建测试类，使用curator连接zookeeper：

```java
public class CuratorTest {
    private CuratorFramework client;
    
    @Before //在所有测试类运行之前运行
    public void testConnect() {
        /*
            * @param connectString       连接字符串。zk server地址和端口: 
                                      "192.168.149.135:2181,192.168.149.135:2182"
            * @param sessionTimeoutMs    会话超时时间 单位ms
            * @param connectionTimeoutMs 连接超时时间 单位ms
            * @param retryPolicy         重试策略
        */
        //重试策略
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000,10);
        //1.第一种方式
        CuratorFramework client = CuratorFrameworkFactory.
            newClient("192.168.149.135:2181",60 * 1000, 15 * 1000, retryPolicy);
        
        //重试策略
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000, 10);
        //2.第二种方式 建议使用
        client = CuratorFrameworkFactory.builder()
            .connectString("192.168.200.130:2181")
            .sessionTimeoutMs(60 * 1000)
            .connectionTimeoutMs(15 * 1000)
            .retryPolicy(retryPolicy)
            .namespace("itheima")//名称空间
            .build();

        //开启连接
        client.start();
    }
}
```

名称空间：所有的操作都基于名称空间节点下，默认不创建，进行操作节点自动创建



***



### 结点操作

#### 创建节点

 创建节点：create 持久 临时 顺序 数据

1. 基本创建：`create().forPath("")`

2. 创建节点带有数据：`create().forPath("",data)`

3. 设置节点的类型：`create().withMode().forPath("",data)`

4. 创建多级节点  /app1/p1：`create().creatingParentsIfNeeded().forPath("",data)`

```java
@Test
public void testCreate1() throws Exception {
    //1. 基本创建
    //如果创建节点，没有指定数据，则默认将当前客户端的ip作为数据存储
    String path = client.create().forPath("/app1");
    System.out.println(path);
}

@Test
public void testCreate2() throws Exception {
    //2. 创建节点 带有数据
    //如果创建节点，没有指定数据，则默认将当前客户端的ip作为数据存储
    String path = client.create().forPath("/app2", "hehe".getBytes());
    System.out.println(path);
}

@Test
public void testCreate3() throws Exception {
    //3. 设置节点的类型
    //默认类型：持久化PERSISTENT
    //PERSISTENT_SEQUENTIAL:顺序结点   EPHEMERAL:临时节点  EPHEMERAL_SEQUENTIAL:es
    String path = client.create().withMode(CreateMode.EPHEMERAL).forPath("/app3");
    System.out.println(path);
}

@Test
public void testCreate4() throws Exception {
    //4. 创建多级节点  /app1/p1
    //creatingParentsIfNeeded():如果父节点不存在，则创建父节点
    String path = client.create().creatingParentsIfNeeded().forPath("/app4/p1");
    System.out.println(path);
}
```



#### 查询节点

查询节点：

1. 查询数据：get   `getData().forPath()`
2. 查询子节点： ls  `getChildren().forPath()`
3. 查询节点状态信息：ls -s  `getData().storingStatIn(状态对象).forPath()`

```java
@Test
public void testGet1() throws Exception {
    //1. 查询数据：get
    byte[] data = client.getData().forPath("/app1");
    System.out.println(new String(data));
}

@Test
public void testGet2() throws Exception {
    // 2. 查询子节点： ls
    List<String> path = client.getChildren().forPath("/");
    System.out.println(path);
}

@Test
public void testGet3() throws Exception {
    Stat status = new Stat();
    System.out.println(status);//0,0,0,0,0,0,0,0,0,0,0
    //3. 查询节点状态信息：ls -s
    client.getData().storingStatIn(status).forPath("/app1");
    System.out.println(status);
    //40194,40194,1615468041638,1615468041638,0,0,0,0,15,0,40194
}
```





#### 修改节点

修改节点的数据：

1. 基本修改数据：`setData().forPath()`
2. 根据版本修改：`setData().withVersion().forPath()`
   * version 是通过查询获取的，目的为了让其他客户端或者线程不干扰此客户端的执行

```java
@Test
public void testSet() throws Exception {
    client.setData().forPath("/app1", "itcast".getBytes());
}

@Test
public void testSetForVersion() throws Exception {
    Stat status = new Stat();
    //3. 查询节点状态信息：ls -s
    client.getData().storingStatIn(status).forPath("/app1");
    int version = status.getVersion();//查询出来的 3
    System.out.println(version);
    client.setData().withVersion(version).forPath("/app1", "hehe".getBytes());
}
```





#### 删除节点

删除节点： delete deleteall

1. 删除单个节点：`delete().forPath("/app1");`
2. 删除带有子节点的节点：`delete().deletingChildrenIfNeeded().forPath("/app1");`
3. 必须成功删除：`client.delete().guaranteed().forPath("/app2");`
   * 为了防止网络抖动，本质是重试
4. 回调：inBackground

```java
@Test
public void testDelete() throws Exception {
    // 1. 删除单个节点
    client.delete().forPath("/app1");
}

@Test
public void testDelete2() throws Exception {
    //2. 删除带有子节点的节点
    client.delete().deletingChildrenIfNeeded().forPath("/app4");
}
@Test
public void testDelete3() throws Exception {
    //3. 必须成功的删除
    client.delete().guaranteed().forPath("/app2");
}

@Test
public void testDelete4() throws Exception {
    //4. 回调
    client.delete().guaranteed().inBackground(new BackgroundCallback(){
        @Override
        public void processResult(CuratorFramework client, CuratorEvent event) throws Exception {
            System.out.println("我被删除了~");
            System.out.println("client:" + client);
            System.out.println("event:" + event);
        }
    }).forPath("/app1");
}
```

testDelete4输出信息：

```java
client:org.apache.curator.framework.imps.CuratorFrameworkImpl@29c9e168
event:CuratorEventImpl{type=DELETE, resultCode=0, path='/app1', name='null', children=null, context=null, stat=null, data=null, watchedEvent=null, aclList=null, opResults=null}
```



***



### Watch监听

#### 监听概述

ZooKeeper 允许用户在指定节点上注册一些Watcher，并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去，该机制是 ZooKeeper 实现分布式协调服务的重要特性

ZooKeeper 中引入了Watcher机制来实现了**发布/订阅功能**，能够让多个订阅者同时监听某一个对象，当一个对象自身状态变化时，会通知所有订阅者

Curator引入了 Cache 来实现对 ZooKeeper 服务端事件的监听

ZooKeeper提供了三种Watcher：

* NodeCache：只是监听某一个特定的节点
* PathChildrenCache：监控一个ZNode的子节点
* TreeCache：可以监控整个树上的所有节点，类似于PathChildrenCache和NodeCache的组合

![](https://gitee.com/seazean/images/raw/master/Frame/ZooKeeper-Watch监听.png)





#### NodeCache

NodeCache：给指定一个节点注册监听器

```java
@Test
public void testNodeCache() throws Exception {
    //1. 创建NodeCache对象
    final NodeCache nodeCache = new NodeCache(client,"/app1");
    //2. 注册监听
    nodeCache.getListenable().addListener(new NodeCacheListener() {
        @Override
        public void nodeChanged() throws Exception {
            System.out.println("节点变化了~");
            //获取修改节点后的数据
            byte[] data = nodeCache.getCurrentData().getData();
            System.out.println(new String(data));
        }
    });

    //3. 开启监听.如果设置为true，则开启监听是，加载缓冲数据
    nodeCache.start(true);

    while (true){
		//不循环直接结束，无法监听到信息
    }
}
```





#### PathChildren

PathChildrenCache：监听某个节点的所有子节点们

```java
@Test
public void testPathChildrenCache() throws Exception {
    //1.创建监听对象
    PathChildrenCache pathChildrenCache = new PathChildrenCache(client,"/app2",true);

    //2. 绑定监听器
    pathChildrenCache.getListenable().addListener(new PathChildrenCacheListener() {
        @Override
        public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
            System.out.println("子节点变化了~");
            System.out.println(event);
            //监听子节点的数据变更，并且拿到变更后的数据
            //1.获取类型
            PathChildrenCacheEvent.Type type = event.getType();
            //2.判断类型是否是update
            if(type.equals(PathChildrenCacheEvent.Type.CHILD_UPDATED)){
                System.out.println("数据变了！！！");
                byte[] data = event.getData().getData();
                System.out.println(new String(data));
            }
        }
    });
    //3. 开启
    pathChildrenCache.start();

    while (true){

    }
}
```





#### TreeCache

TreeCache：监听某个节点自己和所有子节点们

```java
@Test
public void testTreeCache() throws Exception {
    //1. 创建监听器
    TreeCache treeCache = new TreeCache(client,"/app2");

    //2. 注册监听
    treeCache.getListenable().addListener(new TreeCacheListener() {
        @Override
        public void childEvent(CuratorFramework client, TreeCacheEvent event) throws Exception {
            System.out.println("节点变化了");
            System.out.println(event);
        }
    });
    //3. 开启
    treeCache.start();

    while (true){

    }
}
```





***



## 分布式锁

### 基本概述

为了防止分布式系统中的多个进程之间相互干扰，我们需要一种分布式协调技术来对这些进程进行调度，而这个分布式协调技术的核心就是来实现这个**分布式锁**

在我们进行单机应用开发，涉及并发同步的时候，我们往往采用synchronized或者Lock的方式来解决多线程间的代码同步问题，这时多线程的运行都是在同一个JVM之下，不会出现问题。当我们的应用是分布式集群工作的情况下，属于多JVM下的工作环境，跨JVM之间已经无法通过多线程的锁解决同步问题，需要一种更加高级的锁机制，来处理种跨机器的进程之间的数据同步问题—这就是分布式锁。

![](https://gitee.com/seazean/images/raw/master/Frame/ZooKeeper分布式锁.png)





***



### 锁原理

核心思想：当客户端要获取锁，则创建节点，使用完锁，则删除该节点。

1. 客户端获取锁时，在lock节点下创建**临时顺序**节点
   * 使用临时节点是为了防止当服务器或客户端宕机以后节点无法删除（持久节点），导致锁无法释放
   * 使用顺序节点是为了系统自动编号排序，找最小的节点，防止客户端饥饿现象

2. 然后获取lock下面的所有子节点，客户端获取到所有的子节点之后，如果发现自己创建的子节点序号最小，那么就认为该客户端获取到了锁，使用完锁后，将该节点删除

3. 如果发现自己创建的节点并非lock所有子节点中最小的，说明自己还没有获取到锁，此时客户端需要找到比自己小的那个节点，同时**对其注册事件监听器，监听删除事件**

4. 如果发现比自己小的那个节点被删除，则客户端的Watcher会收到相应通知，此时再次判断自己创建的节点是否是lock子节点中序号最小的，如果是则获取到了锁， 如果不是则重复以上步骤继续获取到比自己小的一个节点并注册监听

![](https://gitee.com/seazean/images/raw/master/Frame/ZooKeeper分布式锁原理.png)



***



### 模拟售票

Curator实现分布式锁API，在Curator中有五种锁方案：

- InterProcessSemaphoreMutex：分布式排它锁（非可重入锁）

- InterProcessMutex：分布式可重入排它锁

- InterProcessReadWriteLock：分布式读写锁

- InterProcessMultiLock：将多个锁作为单个实体管理的容器

- InterProcessSemaphoreV2：共享信号量

![](https://gitee.com/seazean/images/raw/master/Frame/ZooKeeper分布式锁售票案例.png)

注意：要在可以访问数据库的服务加锁，图中是12306服务器

1. 创建线程进行加锁设置

   ```java
   public class Ticket12306 implements Runnable{
       private int tickets = 10;//数据库的票数
       private InterProcessMutex lock;//锁
   
       public Ticket12306(){
           //重试策略
           RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000, 10);
           //2.第二种方式
           CuratorFramework client = CuratorFrameworkFactory.builder()
                   .connectString("192.168.149.135:2181")
                   .sessionTimeoutMs(60 * 1000)
                   .connectionTimeoutMs(15 * 1000)
                   .retryPolicy(retryPolicy)
                   .build();
   
           //开启连接
           client.start();
           lock = new InterProcessMutex(client,"/lock");
       }
   
       @Override
       public void run() {
           while(true){
               //获取锁
               try {
                   lock.acquire(3, TimeUnit.SECONDS);
                   if(tickets > 0){
                       System.out.println(Thread.currentThread()+":"+tickets);
                       Thread.sleep(100);
                       tickets--;
                   }
               } catch (Exception e) {
                   e.printStackTrace();
               }finally {
                   //释放锁
                   try {
                       lock.release();
                   } catch (Exception e) {
                       e.printStackTrace();
                   }
               }
           }
       }
   }
   ```

2. 创建连接，并且初始化锁

   ```java
   public class LockTest {
       public static void main(String[] args) {
           Ticket12306 ticket12306 = new Ticket12306();
   
           //创建客户端
           Thread t1 = new Thread(ticket12306,"携程");
           Thread t2 = new Thread(ticket12306,"飞猪");
           t1.start();
           t2.start();
       }
   }
   ```



***



## 集群介绍

### 核心理论

Leader选举：

* Serverid：服务器ID
  比如有三台服务器，编号分别是1、2、3，编号越大在选择算法中的权重越大

* Zxid：数据ID
  服务器中存放的最大数据ID值越大说明数据越新，在选举算法中数据越新权重越大

* 在Leader选举的过程中，如果某台ZooKeeper获得了超过半数的选票，则此ZooKeeper就可以成为Leader了



**Zookeepe集群角色**，在ZooKeeper集群服中务中有三个角色：

* Leader 领导者 ：          

  ​	1. 处理事务请求

  ​	2. 集群内部各服务器的调度者

* Follower 跟随者 ：

  ​	1. 处理客户端非事务请求，转发事务请求给Leader服务器

  ​	2. 参与Leader选举投票

* Observer 观察者：

  1. 处理客户端非事务请求，转发事务请求给Leader服务器

![](https://gitee.com/seazean/images/raw/master/Frame/Zookeeper集群角色.png)



***



### 集群搭建

#### 搭建要求

真实的集群是需要部署在不同的服务器上的，但是在我们测试时同时启动很多个虚拟机内存会吃不消，所以我们通常会搭建**伪集群**，也就是把所有的服务都搭建在一台虚拟机上，用端口进行区分。

我们这里要求搭建一个三个节点的Zookeeper集群（伪集群）。

#### 准备工作

重新部署一台虚拟机作为我们搭建集群的测试服务器。

（1）安装JDK  

（2）Zookeeper压缩包上传到服务器
（3）将Zookeeper解压 ，建立/usr/local/zookeeper-cluster目录，将解压后的Zookeeper复制到以下三个目录

```shell
[root@localhost ~]# mkdir /usr/local/zookeeper-cluster
[root@localhost ~]# cp -r  apache-zookeeper-3.5.6-bin /usr/local/zookeeper-cluster/zookeeper-1
[root@localhost ~]# cp -r  apache-zookeeper-3.5.6-bin /usr/local/zookeeper-cluster/zookeeper-2
[root@localhost ~]# cp -r  apache-zookeeper-3.5.6-bin /usr/local/zookeeper-cluster/zookeeper-3
```

（4）创建data目录 ，并且将 conf下zoo_sample.cfg 文件改名为 zoo.cfg

```shell
mkdir /usr/local/zookeeper-cluster/zookeeper-1/data
mkdir /usr/local/zookeeper-cluster/zookeeper-2/data
mkdir /usr/local/zookeeper-cluster/zookeeper-3/data

mv  /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo_sample.cfg  /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg
mv  /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo_sample.cfg  /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg
mv  /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo_sample.cfg  /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg
```


（5） 配置每一个Zookeeper 的dataDir 和 clientPort 分别为2181  2182  2183

修改/usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg

```shell
vim /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg

clientPort=2181
dataDir=/usr/local/zookeeper-cluster/zookeeper-1/data
```

修改/usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg

```shell
vim /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg

clientPort=2182
dataDir=/usr/local/zookeeper-cluster/zookeeper-2/data
```

修改/usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg

```shell
vim /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg

clientPort=2183
dataDir=/usr/local/zookeeper-cluster/zookeeper-3/data
```




#### 配置集群

（1）在每个zookeeper的 data 目录下创建一个 myid 文件，内容分别是1、2、3 。这个文件就是记录每个服务器的ID

```shell
echo 1 >/usr/local/zookeeper-cluster/zookeeper-1/data/myid
echo 2 >/usr/local/zookeeper-cluster/zookeeper-2/data/myid
echo 3 >/usr/local/zookeeper-cluster/zookeeper-3/data/myid
```

（2）在每一个zookeeper 的 zoo.cfg配置客户端访问端口（clientPort）和集群服务器IP列表。

集群服务器IP列表如下

```shell
vim /usr/local/zookeeper-cluster/zookeeper-1/conf/zoo.cfg
vim /usr/local/zookeeper-cluster/zookeeper-2/conf/zoo.cfg
vim /usr/local/zookeeper-cluster/zookeeper-3/conf/zoo.cfg

server.1=192.168.149.135:2881:3881
server.2=192.168.149.135:2882:3882
server.3=192.168.149.135:2883:3883
```

解释：server.服务器ID=服务器IP地址：服务器之间通信端口：服务器之间投票选举端口



#### 启动集群

启动集群就是分别启动每个实例。

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh start
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh start
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh start 
```

启动后我们查询一下每个实例的运行状态

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh status
```

先查询第一个服务：Mode: follower，表示是**跟随者**（从）

再查询第二个服务Mode: leader，表示是**领导者**（主） 

查询第三个服务：Mode: follower，表示是跟随者（从）



#### 模拟集群异常

（1）首先测试如果是从服务器挂掉，会怎么样？把3号服务器停掉，观察1号和2号，发现状态并没有变化

```shell
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh stop

/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh status
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
```

* 结论：3个节点的集群，从服务器挂掉，集群正常

（2）再把1号服务器（从服务器）也停掉，查看2号（主服务器）的状态，发现已经停止运行了

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh stop

/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
```

* 结论：3个节点的集群，2个从服务器都挂掉，主服务器也无法运行，因为可运行的机器**没有超过集群总数量的半数**

（3）再次把1号服务器启动起来，2号服务器又开始正常工作了，而且依然是领导者

```shell
/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh start

/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status
```

（4）把3号服务器也启动起来，把2号服务器停掉，停掉后观察1号和3号的状态，新的leader产生

```shell
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh start
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh stop

/usr/local/zookeeper-cluster/zookeeper-1/bin/zkServer.sh status	#Mode:follower
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh status #Mode:leader
```

* 结论：当集群中的主服务器挂了，集群中的其他服务器会自动进行选举状态，然后产生新得leader 

（5）再次测试，当我们把2号服务器重新启动起来启动后，会发生什么？2号服务器会再次成为新的领导吗？
         2号服务器启动后依然是跟随者（从服务器），3号服务器依然是领导者（主服务器）

```shell
/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh start

/usr/local/zookeeper-cluster/zookeeper-2/bin/zkServer.sh status	#Mode:follower
/usr/local/zookeeper-cluster/zookeeper-3/bin/zkServer.sh status	#Mode:leader
```

结论：当领导者产生后，再次有新服务器加入集群，不会影响到现任领导者。







****







# RabbitMQ

## 基本概述

### 消息中间件

MQ全称为Message Queue，消息队列是应用程序和应用程序之间的通信方法。

MQ作用：在项目中，可将一些无需即时返回且耗时的操作提取出来，进行**异步处理**，而这种异步处理的方式大大的节省了服务器的请求响应时间，从而**提高**了**系统**的**吞吐量**

消息队列的应用场景：

* **任务异步处理**：将不需要同步处理的并且耗时长的操作由消息队列通知消息接收方进行异步处理。提高了应用程序的响应时间

* 应用程序**解耦合**：MQ相当于一个中介，生产方通过MQ与消费方交互，将应用程序进行解耦合

* **削峰填谷**：如订单系统，在下单的时候就会往数据库写数据。但是数据库只能支撑每秒1000左右的并发写入，并发量再高就容易宕机，在高峰期时候，并发量会突然激增到5000以上，这个时候数据库就会宕机。消息被MQ保存起来了，然后系统就可以按照自己的消费能力来消费，比如每秒1000个数据，这样慢慢写入数据库，但是使用了MQ之后，限制消费消息的速度为1000，高峰期产生的数据势必会被积压在MQ中，高峰就被“削”掉了。因为消息积压，在高峰期过后的一段时间内，消费消息的速度还是会维持在1000QPS，直到消费完积压的消息，这就叫做“填谷”

  ![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-削峰填谷.jpg)





### 实现MQ

MQ是消息通信的模型，实现MQ的大致有两种主流方式：AMQP、JMS

AMQP是一种协议，更准确的说是一种binary wire-level protocol（链接协议），AMQP不从API层进行限定，而是直接定义网络交换的数据格式

JMS即Java消息服务（JavaMessage Service）应用程序接口，是一个Java平台中关于面向消息中间件（MOM）的API，用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信

AMQP与JMS的区别：

- JMS是定义了统一的接口，来对消息操作进行统一；AMQP是通过规定协议来统一数据交互的格式
- JMS限定了必须使用Java语言；AMQP只是协议，不规定实现方式，因此是跨语言的
- JMS规定了两种消息模式，而AMQP的消息模式更加丰富

市场上常见的消息队列有如下：

- ActiveMQ：基于JMS
- ZeroMQ：基于C语言开发
- RabbitMQ：基于AMQP协议，erlang语言开发，稳定性好
- RocketMQ：基于JMS，阿里巴巴产品
- Kafka：类似MQ的产品；分布式消息系统，高吞吐量



### RabbitMQ

RabbitMQ是由erlang语言开发，基于AMQP（Advanced Message Queue 高级消息队列协议）协议实现的消息队列，它是一种应用程序之间的通信方法，消息队列在分布式系统开发中应用非常广泛

RabbitMQ官方地址：http://www.rabbitmq.com/

RabbitMQ提供了6种模式：简单模式，work模式，Publish/Subscribe发布与订阅模式，Routing路由模式，Topics主题模式，RPC远程调用模式

官网对应模式介绍：https://www.rabbitmq.com/getstarted.html

安装步骤：https://www.jianshu.com/p/d3f10f539925

1.安装erlang，由于rabbitMq需要erlang语言的支持，在安装rabbitMq之前需要安装erlang，执行命令：

> apt-get install erlang-nox   # 安装erlang
>
> erl  # 查看relang语言版本，成功执行则说明relang安装成功

2.添加公钥

> wget -O- https://www.rabbitmq.com/rabbitmq-release-signing-key.asc | sudo apt-key add -

3.更新软件包 

> apt-get update

4.安装 RabbitMQ

> apt-get install rabbitmq-server #安装成功自动启动

5.查看 RabbitMq状态

> \#Active: active (running) 说明处于运行状态
>
> systemctl status rabbitmq-server
>
> \# 用service指令也可以查看，同systemctl指令
>
> service rabbitmq-server status

6.启动、停止、重启

> service rabbitmq-server start  # 启动
>
> service rabbitmq-server stop   # 停止
>
> service rabbitmq-server restart # 重启

7.启用 web端可视化操作界面，我们还需要配置Management Plugin插件

> \# 启用插件
>
> rabbitmq-plugins enable rabbitmq_management
>
>  \# 装完后重启 
>
> service rabbitmq-server restart  

8.查看rabbitmq用户

> rabbitmqctl list_users

9.添加管理用户

> \# 增加普通用户
>
> rabbitmqctl add_user admin yourpassword 
>
> \# 给普通用户分配管理员角色
>
> rabbitmqctl set_user_tags admin administrator  

10.访问web控制台

> 打开浏览器
>
> http://服务器IP:15672/ 来访问你的rabbitmq监控页面。使用刚刚添加的新用户(admin)登录





***





## AMQP

### 相关概念

AMQP：一个提供统一消息服务的应用层标准高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计

AMQP：是一个二进制协议，拥有一些现代化特点：多信道、协商式，异步，安全，扩平台，中立，高效

RabbitMQ是AMQP协议的Erlang的实现

| 概念           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| 连接Connection | 一个网络连接，比如TCP/IP套接字连接                           |
| 会话Session    | 端点之间的命名对话。在一个会话上下文中，保证“恰好传递一次”   |
| 信道Channel    | 多路复用连接中的一条独立的双向数据流通道。为会话提供物理传输介质 |
| 客户端Client   | AMQP连接或者会话的发起者。AMQP是非对称的，客户端生产和消费消息，服务器存储和路由这些消息 |
| 服务节点Broker | 消息中间件的服务节点；一般情况下可以将一个RabbitMQ Broker看作一台RabbitMQ 服务器 |
| 端点           | AMQP对话的任意一方，一个AMQP连接包括两个端点（一个是客户端，一个是服务器） |
| 消费者Consumer | 一个从消息队列里请求消息的客户端程序                         |
| 生产者Producer | 一个向交换机发布消息的客户端应用程序                         |



***



### 运转流程

基本的运转流程：

- 生产者发送消息
  1. 生产者创建连接（Connection），开启一个信道（Channel），连接到RabbitMQ Broker
  2. 声明队列并设置属性；如是否排它，是否持久化，是否自动删除
  3. 将路由键（空字符串）与队列绑定起来
  4. 发送消息至RabbitMQ Broker
  5. 关闭信道
  6. 关闭连接
- 消费者接收消息
  1. 消费者创建连接（Connection），开启一个信道（Channel），连接到RabbitMQ Broker
  2. 向Broker 请求消费相应队列中的消息，设置相应的回调函数
  3. 等待Broker回应闭关投递响应队列中的消息，消费者接收消息
  4. 确认（ack，自动确认）接收到的消息
  5. RabbitMQ从队列中删除相应已经被确认的消息
  6. 关闭信道
  7. 关闭连接

![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-RabbitMQ运转流程.jpg)



生产者运转流程说明：

1. 客户端与代理服务器Broker建立连接。会调用newConnection() 方法,这个方法会进一步封装Protocol Header 0-9-1 的报文头发送给Broker ，以此通知Broker 本次交互采用的是AMQPO-9-1 协议，紧接着Broker 返回Connection.Start 来建立连接，在连接的过程中涉及Connection.Start/.Start-OK 、Connection.Tune/.Tune-Ok ，Connection.Open/ .Open-Ok 这6 个命令的交互。

2. 客户端调用connection.createChannel方法。此方法开启信道，其包装的channel.open命令发送给Broker，等待channel.basicPublish方法，对应的AMQP命令为Basic.Publish，这个命令包含了content Header 和content Body()。content Header 包含了消息体的属性，例如：投递模式，优先级等，content Body 包含了消息体本身。

3. 客户端发送完消息需要关闭资源时，涉及到Channel.Close和Channl.Close-Ok 与Connetion.Close和Connection.Close-Ok的命令交互。

   ![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-生产者流转过程图.bmp)

消费者运转流程说明：

1. 消费者客户端与代理服务器Broker建立连接。会调用newConnection() 方法,这个方法会进一步封装Protocol Header 0-9-1 的报文头发送给Broker ，以此通知Broker 本次交互采用的是AMQPO-9-1 协议，紧接着Broker 返回Connection.Start 来建立连接，在连接的过程中涉及Connection.Start/.Start-OK 、Connection.Tune/.Tune-Ok ，Connection.Open/ .Open-Ok 这6 个命令的交互。

2. 消费者客户端调用connection.createChannel方法。和生产者客户端一样，协议涉及Channel . Open/Open-Ok命令。

3. 在真正消费之前，消费者客户端需要向Broker 发送Basic.Consume 命令(即调用channel.basicConsume 方法〉将Channel 置为接收模式，之后Broker 回执Basic . Consume - Ok 以告诉消费者客户端准备好消费消息。

4. Broker 向消费者客户端推送(Push) 消息，即Basic.Deliver 命令，这个命令和Basic.Publish 命令一样会携带Content Header 和Content Body。

5. 消费者接收到消息并正确消费之后，向Broker 发送确认，即Basic.Ack 命令。

6. 客户端发送完消息需要关闭资源时，涉及到Channel.Close和Channl.Close-Ok 与Connetion.Close和Connection.Close-Ok的命令交互。

   ![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-消费者流转过程图.bmp)



****



## 工作模式

### Hello World

入门案例中其实使用的是如下的简单模式，在上图的模型中，有以下概念：

- P：生产者，也就是要发送消息的程序
- C：消费者，消息的接受者，会一直等待消息到来
- queue：消息队列，图中红色部分，类似一个邮箱，可以缓存消息；生产者向其中投递消息，消费者从其中取出消息

![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-简单模式.jpg)

* 两个模块添加依赖：pom.xml 

  ```xml
  <dependency>
      <groupId>com.rabbitmq</groupId>
      <artifactId>amqp-client</artifactId>
      <version>5.6.0</version>
  </dependency>
  ```

* 生产者模块

  在执行消息发送之后；可以登录rabbitMQ的管理控制台，可以发现队列和其消息

  ```java
  public class Producer {
      static final String QUEUE_NAME = "simple_queue";
      public static void main(String[] args) throws Exception {
          //创建连接工厂
          ConnectionFactory connectionFactory = new ConnectionFactory();
          //主机地址;默认为 localhost
          connectionFactory.setHost("192.168.0.137");
          //连接端口;默认为 5672
          connectionFactory.setPort(5672);
          //虚拟主机名称;默认为 /
          connectionFactory.setVirtualHost("/sea");
          //连接用户名；默认为guest
          connectionFactory.setUsername("admin");
          //连接密码；默认为guest
          connectionFactory.setPassword("admin");
  
          //创建连接
          Connection connection = connectionFactory.newConnection();
  
          //创建频道
          Channel channel = connection.createChannel();
  
          // 声明（创建）队列
          /**
           * 参数1 queue:		队列名称
           * 参数2 durable:		是否定义持久化队列，当mq重启之后，还存在
           * 参数3 exclusive:	是否独占本次连接，只能有一个消费者监听这队列,
           					  当Connection关闭时，是否删除队列
           * 参数4 autoDelete:	是否在不使用的时候自动删除队列,没有Consumer时，自动删除
           * 参数5 arguments:	队列其它参数
           */
          channel.queueDeclare(QUEUE_NAME, true, false, false, null);
  
          // 要发送的信息
          String message = "Hello RabbitMQ！";
          /**
           * 参数1 exchange:	交换机名称，如果没有指定则使用默认Default Exchage
           * 参数2 routingKey:	路由key,简单模式可以传递队列名称
           * 参数3 props:		消息其它属性，配置消息
           * 参数4 body:		消息内容
           */
          channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
          System.out.println("已发送消息：" + message);
  
          // 关闭资源
          channel.close();
          connection.close();
      }
  }
  ```

* 消费者模块

  ```java
  public class Consumer {
      public static void main(String[] args) throws Exception {
          //创建连接
  		
          // 创建频道
          Channel channel = connection.createChannel();
  
          // 声明（创建）队列
          //如果没有一个名字叫simple_queue的队列，则会创建该队列，如果有则不会创建
          channel.queueDeclare(Producer.QUEUE_NAME, true, false, false, null);
  
          //创建消费者；并设置消息处理
          Consumer consumer = new DefaultConsumer(channel){
              @Override
              /**
               * consumerTag:	消息者标签，在channel.basicConsume时候可以指定
               * envelope: 	消息包的内容，可从中获取消息id，消息routingkey，交换机，
               				消息和重传标志(收到消息失败后是否需要重新发送)
               * properties: 	配置属性信息
               * body:		消息
               */
              public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                  //路由key
                  System.out.println("路由key为：" + envelope.getRoutingKey());
                  //交换机
                  System.out.println("交换机为：" + envelope.getExchange());
                  //消息id
                  System.out.println("消息id为：" + envelope.getDeliveryTag());
                  //收到的消息
                  System.out.println("接收到的消息为：" + new String(body, "utf-8"));
              }
          };
          //监听消息
          /**
           * String:	队列名称
           * boolean:	是否自动确认，设置为true为表示消息接收到自动向mq回复接收到了，
           			mq接收到回复会删除消息，设置为false则需要手动确认
           * Consumer:消息接收到后回调
           */
          channel.basicConsume(Producer.QUEUE_NAME, true, consumer);
  
          //不关闭资源，应该一直监听消息
      }
  }
  ```



****



### Work queues

Work queues官网链接：https://www.rabbitmq.com/tutorials/tutorial-two-python.html

Work Queues与入门程序的简单模式相比，多了一个或一些消费端，多个消费端共同消费同一个队列中的消息

* 生产者

  ```java
  public class Producer {
      static final String QUEUE_NAME = "work_queue";
      public static void main(String[] args) throws Exception {
          // 创建连接
          Connection connection = ConnectionUtil.getConnection();
          // 创建频道
          Channel channel = connection.createChannel();
  		//创建队列
          channel.queueDeclare(QUEUE_NAME, true, false, false, null);
  
          for (int i = 1; i <= 30; i++) {
              // 发送信息
              String message = "你好；小兔子！work模式--" + i;
              channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
              System.out.println("已发送消息：" + message);
          }
          // 关闭资源
          channel.close();
          connection.close();
      }
  }
  ```

* 消费者1

  ```java
  public class Consumer1 {
      public static void main(String[] args) throws Exception {
          Connection connection = ConnectionUtil.getConnection();
          // 创建频道
          Channel channel = connection.createChannel();
          // 创建队列
          channel.queueDeclare(Producer.QUEUE_NAME, true, false, false, null);
  
          //一次只能接收并处理一个消息
          channel.basicQos(1);
  
          //创建消费者；并设置消息处理
          DefaultConsumer consumer = new DefaultConsumer(channel){
              @Override
              public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                  try {
                      //路由key
                      System.out.println("路由key为：" + envelope.getRoutingKey());
                      //交换机
                      System.out.println("交换机为：" + envelope.getExchange());
                      //消息id
                      System.out.println("消息id为：" + envelope.getDeliveryTag());
                      //收到的消息
                      System.out.println("消费者1-接收到的消息为：" + 
                                         new String(body, "utf-8"));
                      Thread.sleep(1000);
                      //确认消息
                      channel.basicAck(envelope.getDeliveryTag(), false);
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  }
              }
          };
          //监听消息
          channel.basicConsume(Producer.QUEUE_NAME, false, consumer);
      }
  }
  ```

* 消费者2同1

* 测试结果：

  ```markdown
  消费者1：
  	1 3 5...
  消费者2：
  	2 4 6...
  ```

结论：在一个队列中如果有多个消费者，那么消费者之间对于同一个消息的关系是**竞争**的关系



***



### Pub/Sub

Publish/Subscribe：https://www.rabbitmq.com/tutorials/tutorial-three-python.html

在订阅模型中，多了一个exchange角色，过程略有变化：

- P：生产者，也就是要发送消息的程序，但是不再发送到队列中，而是发给X（交换机）
- C：消费者，消息的接受者，会一直等待消息到来
- Queue：消息队列，接收消息、缓存消息
- Exchange：交换机。一方面，接收生产者发送的消息，另一方面，知道如何处理消息，例如递交给某个特别队列、递交给所有队列、或是将消息丢弃。到底如何操作，取决于Exchange的类型。
  Exchange有常见以下3种类型：
  - Fanout：广播，将消息交给所有绑定到交换机的队列
  - Direct：定向，把消息交给符合指定routing key 的队列
  - Topic：通配符，把消息交给符合routing pattern（路由模式） 的队列

**Exchange（交换机）只负责转发消息，不具备存储消息的能力**，因此如果没有任何队列与Exchange绑定，或者没有符合路由规则的队列，那么消息会丢失

* 生产者，发布与订阅使用的交换机类型为：fanout

  ```java
  public class Producer {
      //交换机名称
      static final String FANOUT_EXCHAGE = "fanout_exchange";
      //队列名称
      static final String FANOUT_QUEUE_1 = "fanout_queue_1";
      //队列名称
      static final String FANOUT_QUEUE_2 = "fanout_queue_2";
      public static void main(String[] args) throws Exception {
          // 创建连接
          Connection connection = ConnectionUtil.getConnection();
          // 创建频道
          Channel channel = connection.createChannel();
  
          /**
           * 声明交换机
           * 参数1：交换机名称
           * 参数2：交换机类型，fanout、topic、direct、headers
           */
          channel.exchangeDeclare(FANOUT_EXCHAGE, BuiltinExchangeType.FANOUT);
  
          // 声明（创建）队列
          channel.queueDeclare(FANOUT_QUEUE_1, true, false, false, null);
          channel.queueDeclare(FANOUT_QUEUE_2, true, false, false, null);
  
           /**
           * 队列绑定交换机
           * queue:		队列名称
           * exchange:	交换机名称
           * routingKey:	路由键，绑定规则,若交换机的类型为fanout，routingKey设置""
           */
          channel.queueBind(FANOUT_QUEUE_1, FANOUT_EXCHAGE, "");
          channel.queueBind(FANOUT_QUEUE_2, FANOUT_EXCHAGE, "");
  
          for (int i = 1; i <= 10; i++) {
              // 发送信息
              String message = "你好；小兔子！发布订阅模式--" + i;
              channel.basicPublish(FANOUT_EXCHAGE, "", null, message.getBytes());
              System.out.println("已发送消息：" + message);
          }
  
          // 关闭资源
          channel.close();
          connection.close();
      }
  }
  ```

* 消费者1

  ```java
  ublic class Consumer1 {
      public static void main(String[] args) throws Exception {
          Connection connection = ConnectionUtil.getConnection();
          // 创建频道
          Channel channel = connection.createChannel();
  
          //声明交换机
  
          // 声明（创建）队列
          channel.queueDeclare(Producer.FANOUT_QUEUE_1, true, false, false, null);
  
          //队列绑定交换机，生产者和消费者写一处，一般写在消费者端
          //channel.queueBind(Producer.FANOUT_QUEUE_1, Producer.FANOUT_EXCHAGE, "");
  
          //创建消费者；并设置消息处理
          DefaultConsumer consumer = new DefaultConsumer(channel){
              @Override
              public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                  //路由key
                  System.out.println("路由key为：" + envelope.getRoutingKey());
                  //交换机
                  System.out.println("交换机为：" + envelope.getExchange());
                  //消息id
                  System.out.println("消息id为：" + envelope.getDeliveryTag());
                  //收到的消息
                  System.out.println("消费者1-接收到的消息为：" + new String(body, "utf-8"));
              }
  
          channel.basicConsume(Producer.FANOUT_QUEUE_1, true, consumer);
      }
  }
  ```
  
* 消费者2同1

* 启动所有消费者，然后使用生产者发送消息；在每个消费者对应的控制台可以查看到生产者发送的所有消息；到达**广播**的效果

**发布订阅模式与工作队列模式的区别**

1. 工作队列模式不用定义交换机，而发布/订阅模式需要定义交换机。 

2. 发布/订阅模式的生产方是面向交换机发送消息，工作队列模式的生产方是面向队列发送消息(底层使用默认交换机)

3. 发布/订阅模式需要设置队列和交换机的绑定，工作队列模式不需要设置，实际上工作队列模式会将队列绑 定到默认的交换机 



***



### Routing

Routing：https://www.rabbitmq.com/tutorials/tutorial-four-python.html

路由模式特点：

- 队列与交换机的绑定，不能是任意绑定了，而是要指定一个`RoutingKey`（路由key）
- 消息的发送方在向 Exchange发送消息时，也必须指定消息的 `RoutingKey`。
- Exchange不再把消息交给每一个绑定的队列，而是根据消息的`Routing Key`进行判断，只有队列的`Routingkey`与消息的 `Routing key`完全一致，才会接收到消息

在编码上与 Publish/Subscribe发布与订阅模式 的区别是交换机的类型为：Direct，还有队列绑定交换机的时候需要指定routing key

* 生产者

  ```java
  public class Producer {
      //交换机名称
      static final String DIRECT_EXCHAGE = "direct_exchange";
      //队列名称
      static final String DIRECT_QUEUE_INSERT = "direct_queue_insert";
      //队列名称
      static final String DIRECT_QUEUE_UPDATE = "direct_queue_update";
  
      public static void main(String[] args) throws Exception {
  
          //创建连接
          Connection connection = ConnectionUtil.getConnection();
  
          // 创建频道
          Channel channel = connection.createChannel();
  
  
       	//声明交换机：交换机类型，fanout、topic、direct、headers
          channel.exchangeDeclare(DIRECT_EXCHAGE, BuiltinExchangeType.DIRECT);
  
          // 声明（创建）队列
          channel.queueDeclare(DIRECT_QUEUE_INSERT, true, false, false, null);
          channel.queueDeclare(DIRECT_QUEUE_UPDATE, true, false, false, null);
  
          //队列绑定交换机
          channel.queueBind(DIRECT_QUEUE_INSERT, DIRECT_EXCHAGE, "insert");
          channel.queueBind(DIRECT_QUEUE_UPDATE, DIRECT_EXCHAGE, "update");
  
          // 发送信息
          /**
           * 参数1：交换机名称，如果没有指定则使用默认Default Exchage
           * 参数2：路由key,简单模式可以传递队列名称
           * 参数3：消息其它属性
           * 参数4：消息内容
           */
          String message = "新增了商品。路由模式；routing key 为 insert " ;
          channel.basicPublish(DIRECT_EXCHAGE, "insert", null, message.getBytes());
          System.out.println("已发送消息：" + message);
  
          // 发送信息
          message = "修改了商品。路由模式；routing key 为 update" ;
          channel.basicPublish(DIRECT_EXCHAGE, "update", null, message.getBytes());
          System.out.println("已发送消息：" + message);
  
          // 关闭资源
          channel.close();
          connection.close();
      }
  }
  ```
  
* 消费者1

  ```java
  public class Consumer1 {
      public static void main(String[] args) throws Exception {
          Connection connection = ConnectionUtil.getConnection();
          // 创建频道
          Channel channel = connection.createChannel();
  
          //创建消费者；并设置消息处理
          DefaultConsumer consumer = new DefaultConsumer(channel){
              @Override
              public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                  //路由key
                  System.out.println("路由key为：" + envelope.getRoutingKey());
                  //交换机
                  System.out.println("交换机为：" + envelope.getExchange());
                  //消息id
                  System.out.println("消息id为：" + envelope.getDeliveryTag());
                  //收到的消息
                  System.out.println("消费者1-接收到的消息为：" + new String(body, "utf-8"));
              }
          };
          //监听消息
          channel.basicConsume(Producer.DIRECT_QUEUE_INSERT, true, consumer);
      }
  }
  ```
  
* 消费者2同1，更改队列名称：Producer.DIRECT_QUEUE_UPDATE

* 启动所有消费者，然后使用生产者发送消息；在消费者对应的控制台可以查看到生产者发送对应routing key对应队列的消息；到达**按照需要接收**的效果

结论：Routing模式中队列在绑定交换机时要指定routing key，消息会转发到符合routing key的队列



****



### Topics

Topics通配符：https://www.rabbitmq.com/tutorials/tutorial-five-python.html

Topic类型与Direct相比，都是可以根据RoutingKey把消息路由到不同的队列。Topic类型Exchange可以让队列在绑定Routing key 的时候**使用通配符**

`Routingkey` 一般都是有一个或多个单词组成，多个单词之间以”.”分割，例如： `item.insert`

通配符规则：

`#`：匹配一个或多个词

`*`：匹配恰好1个词

举例：

`item.#`：能够匹配`item.insert.abc` 或者 `item.insert`

`item.*`：只能匹配`item.insert`

* 生产者：使用topic类型的Exchange，发送消息的routing key有3种： `item.insert`、`item.update`、`item.delete`

  ```java
  public class Producer {
      //交换机名称
      static final String TOPIC_EXCHAGE = "topic_exchange";
      //队列名称
      static final String TOPIC_QUEUE_1 = "topic_queue_1";
      //队列名称
      static final String TOPIC_QUEUE_2 = "topic_queue_2";
      public static void main(String[] args) throws Exception {
          //创建连接
          Connection connection = ConnectionUtil.getConnection();
          // 创建频道
          Channel channel = connection.createChannel();
          //声明交换机
          channel.exchangeDeclare(TOPIC_EXCHAGE, BuiltinExchangeType.TOPIC);
          
          
          // 发送信息
          String message = "新增了商品。Topic模式；routing key 为 item.insert " ;
          channel.basicPublish(TOPIC_EXCHAGE, "item.insert", null, message.getBytes());
          System.out.println("已发送消息：" + message);
  
          // 发送信息
          message = "修改了商品。Topic模式；routing key 为 item.update" ;
          channel.basicPublish(TOPIC_EXCHAGE, "item.update", null, message.getBytes());
          System.out.println("已发送消息：" + message);
  
          // 发送信息
          message = "删除了商品。Topic模式；routing key 为 item.delete" ;
          channel.basicPublish(TOPIC_EXCHAGE, "item.delete", null, message.getBytes());
          System.out.println("已发送消息：" + message);
  
          // 关闭资源
          channel.close();
          connection.close();
      }
  }
  ```

* 消费者1：接收两种类型的消息：更新商品和删除商品

  ```java
  public class Consumer1 {
      public static void main(String[] args) throws Exception {
          Connection connection = ConnectionUtil.getConnection();
          // 创建频道
          Channel channel = connection.createChannel();
  		//声明交换机
          channel.exchangeDeclare(Producer.TOPIC_EXCHAGE,BuiltinExchangeType.TOPIC);
          //声明（创建）队列
          channel.queueDeclare(Producer.TOPIC_QUEUE_1, true, false, false, null);
  
          // 队列绑定交换机
          channel.queueBind(Producer.TOPIC_QUEUE_1, Producer.TOPIC_EXCHAGE, "item.update");
          channel.queueBind(Producer.TOPIC_QUEUE_1, Producer.TOPIC_EXCHAGE, "item.delete");
          
          //创建消费者；并设置消息处理
          DefaultConsumer consumer = new DefaultConsumer(channel){
              @Override
              public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties properties, byte[] body) throws IOException {
                  //路由key
                  System.out.println("路由key为：" + envelope.getRoutingKey());
                  //交换机
                  System.out.println("交换机为：" + envelope.getExchange());
                  //消息id
                  System.out.println("消息id为：" + envelope.getDeliveryTag());
                  //收到的消息
                  System.out.println("消费者1-接收到的消息为：" + new String(body, "utf-8"));
              }
          };
          //监听消息
          channel.basicConsume(Producer.TOPIC_QUEUE_1, true, consumer);
      }
  }
  ```

* 消费者2：接收所有类型的消息：新增商品，更新商品和删除商品

  ```java
  // 声明（创建）队列
  channel.queueDeclare(Producer.TOPIC_QUEUE_2, true, false, false, null);
  // 队列绑定交换机
  channel.queueBind(Producer.TOPIC_QUEUE_2, Producer.TOPIC_EXCHAGE, "item.*");
  //监听消息
  channel.basicConsume(Producer.TOPIC_QUEUE_2, true, consumer);
  ```

* 启动所有消费者，然后使用生产者发送消息；在消费者对应的控制台可以查看到生产者发送对应routing key对应队列的消息；到达**按照需要接收**的效果；并且这些routing key可以使用通配符

结论：Topic主题模式可以实现 `Publish/Subscribe发布与订阅模式` 和 ` Routing路由模式` 的功能；只是Topic在配置routing key 的时候可以使用通配符，显得更加灵活



***



### 模式总结

RabbitMQ工作模式：

**1、简单模式 HelloWorld**
一个生产者、一个消费者，不需要设置交换机（使用默认的交换机）

**2、工作队列模式 Work Queue**
一个生产者、多个消费者（竞争关系），不需要设置交换机（使用默认的交换机）

**3、发布订阅模式 Publish/subscribe**
需要设置类型为fanout的交换机，并且交换机和队列进行绑定，当发送消息到交换机后，交换机会将消息发送到绑定的队列

**4、路由模式 Routing**
需要设置类型为direct的交换机，交换机和队列进行绑定，并且指定routing key，当发送消息到交换机后，交换机会根据routing key将消息发送到对应的队列

**5、通配符模式 Topic**
需要设置类型为topic的交换机，交换机和队列进行绑定，并且指定通配符方式的routing key，当发送消息到交换机后，交换机会根据routing key将消息发送到对应的队列





***



## Spring

### Spring整合

#### 生产者工程

##### 添加依赖

pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.1.7.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework.amqp</groupId>
        <artifactId>spring-rabbit</artifactId>
        <version>2.1.8.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.1.7.RELEASE</version>
    </dependency>
</dependencies>
```



##### 配置整合

创建`spring-rabbitmq-producer\src\main\resources\properties\rabbitmq.properties`连接参数等配置文件

```properties
rabbitmq.host=192.168.0.137
rabbitmq.port=5672
rabbitmq.username=admin
rabbitmq.password=admin
rabbitmq.virtual-host=/sea
```

创建 `spring-rabbitmq-producer\src\main\resources\spring\spring-rabbitmq.xml` 整合配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:properties/rabbitmq.properties"/>

    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>
    <!--定义管理交换机、队列-->
    <rabbit:admin connection-factory="connectionFactory"/>

    <!--定义持久化队列，不存在则自动创建；不绑定到交换机则绑定到默认交换机
    默认交换机类型为direct，名字为：""，路由键为队列的名称
    -->
    <rabbit:queue id="spring_queue" name="spring_queue" auto-declare="true"/>

    <!-- ~~~~~~~~~~~广播；所有队列都能收到消息 ~~~~~~~~~~~ -->
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_fanout_queue_1" name="spring_fanout_queue_1" auto-declare="true"/>

    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_fanout_queue_2" name="spring_fanout_queue_2" auto-declare="true"/>

    <!--定义广播类型交换机；并绑定上述两个队列-->
    <rabbit:fanout-exchange id="spring_fanout_exchange" name="spring_fanout_exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding queue="spring_fanout_queue_1"/>
            <rabbit:binding queue="spring_fanout_queue_2"/>
        </rabbit:bindings>
    </rabbit:fanout-exchange>

    <!-- ~~~~~~~~~~~ 通配符；*匹配一个单词，#匹配多个单词 ~~~~~~~~~~~ -->
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_topic_queue_star" name="spring_topic_queue_star" auto-declare="true"/>
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_topic_queue_well" name="spring_topic_queue_well" auto-declare="true"/>
    <!--定义广播交换机中的持久化队列，不存在则自动创建-->
    <rabbit:queue id="spring_topic_queue_well2" name="spring_topic_queue_well2" auto-declare="true"/>

    <rabbit:topic-exchange id="spring_topic_exchange" name="spring_topic_exchange" auto-declare="true">
        <rabbit:bindings>
            <rabbit:binding pattern="heima.*" queue="spring_topic_queue_star"/>
            <rabbit:binding pattern="heima.#" queue="spring_topic_queue_well"/>
            <rabbit:binding pattern="itcast.#" queue="spring_topic_queue_well2"/>
        </rabbit:bindings>
    </rabbit:topic-exchange>

    <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
    <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>
</beans>
```





##### 发送消息

创建测试文件 `spring-rabbitmq-producer\src\test\java\com\itheima\rabbitmq\ProducerTest.java`

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:spring/spring-rabbitmq.xml")
public class ProducerTest {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * 只发队列消息
     * 默认交换机类型为 direct
     * 交换机的名称为空，路由键为队列的名称
     */
    @Test
    public void queueTest(){
        //路由键与队列同名
        rabbitTemplate.convertAndSend("spring_queue", "只发队列spring_queue的消息。");
    }

    /**
     * 发送广播
     * 交换机类型为 fanout
     * 绑定到该交换机的所有队列都能够收到消息
     */
    @Test
    public void fanoutTest(){
        /**
         * 参数1：交换机名称
         * 参数2：路由键名（广播设置为空）
         * 参数3：发送的消息内容
         */
        rabbitTemplate.convertAndSend("spring_fanout_exchange", "", "发送到spring_fanout_exchange交换机的广播消息");
    }

    /**
     * 通配符
     * 交换机类型为 topic
     * 匹配路由键的通配符，*表示一个单词，#表示多个单词
     * 绑定到该交换机的匹配队列能够收到对应消息
     */
    @Test
    public void topicTest(){
        /**
         * 参数1：交换机名称
         * 参数2：路由键名
         * 参数3：发送的消息内容
         */
        rabbitTemplate.convertAndSend("spring_topic_exchange", "heima.bj", "发送到spring_topic_exchange交换机heima.bj的消息");
        rabbitTemplate.convertAndSend("spring_topic_exchange", "heima.bj.1", "发送到spring_topic_exchange交换机heima.bj.1的消息");
        rabbitTemplate.convertAndSend("spring_topic_exchange", "heima.bj.2", "发送到spring_topic_exchange交换机heima.bj.2的消息");
        rabbitTemplate.convertAndSend("spring_topic_exchange", "itcast.cn", "发送到spring_topic_exchange交换机itcast.cn的消息");
    }
}
```





***





#### 消费者工程

##### 配置整合

创建 `spring-rabbitmq-consumer\src\main\resources\spring\spring-rabbitmq.xml` 整合配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:rabbit="http://www.springframework.org/schema/rabbit"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       https://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/rabbit
       http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">
    <!--加载配置文件-->
    <context:property-placeholder location="classpath:properties/rabbitmq.properties"/>

    <!-- 定义rabbitmq connectionFactory -->
    <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                               port="${rabbitmq.port}"
                               username="${rabbitmq.username}"
                               password="${rabbitmq.password}"
                               virtual-host="${rabbitmq.virtual-host}"/>

    <bean id="springQueueListener" class="com.itheima.rabbitmq.listener.SpringQueueListener"/>
    <bean id="fanoutListener1" class="com.itheima.rabbitmq.listener.FanoutListener1"/>
    <bean id="fanoutListener2" class="com.itheima.rabbitmq.listener.FanoutListener2"/>
    <bean id="topicListenerStar" class="com.itheima.rabbitmq.listener.TopicListenerStar"/>
    <bean id="topicListenerWell" class="com.itheima.rabbitmq.listener.TopicListenerWell"/>
    <bean id="topicListenerWell2" class="com.itheima.rabbitmq.listener.TopicListenerWell2"/>

    <rabbit:listener-container connection-factory="connectionFactory" auto-declare="true">
        <rabbit:listener ref="springQueueListener" queue-names="spring_queue"/>
        <rabbit:listener ref="fanoutListener1" queue-names="spring_fanout_queue_1"/>
        <rabbit:listener ref="fanoutListener2" queue-names="spring_fanout_queue_2"/>
        <rabbit:listener ref="topicListenerStar" queue-names="spring_topic_queue_star"/>
        <rabbit:listener ref="topicListenerWell" queue-names="spring_topic_queue_well"/>
        <rabbit:listener ref="topicListenerWell2" queue-names="spring_topic_queue_well2"/>
    </rabbit:listener-container>
</beans>
```



##### 消息监听器

###### 队列监听器

创建 `spring-rabbitmq-consumer\src\main\java\com\itheima\rabbitmq\listener\SpringQueueListener.java`

```java
public class SpringQueueListener implements MessageListener {
    public void onMessage(Message message) {
        try {
            String msg = new String(message.getBody(), "utf-8");

            System.out.printf("接收路由名称为：%s，路由键为：%s，队列名为：%s的消息：%s \n",
                    message.getMessageProperties().getReceivedExchange(),
                    message.getMessageProperties().getReceivedRoutingKey(),
                    message.getMessageProperties().getConsumerQueue(),
                    msg);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



###### 广播监听器

广播监听器1，创建 `spring-rabbitmq-consumer\src\main\java\com\itheima\rabbitmq\listener\FanoutListener1.java`

```java
public class FanoutListener1 implements MessageListener {
    public void onMessage(Message message) {
        try {
            String msg = new String(message.getBody(), "utf-8");

            System.out.printf("广播监听器1：接收路由名称为：%s，路由键为：%s，队列名为：%s的消息：%s \n",
                    message.getMessageProperties().getReceivedExchange(),
                    message.getMessageProperties().getReceivedRoutingKey(),
                    message.getMessageProperties().getConsumerQueue(),
                    msg);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



广播监听器2

```java
public class FanoutListener2 implements MessageListener {
    public void onMessage(Message message) {
        try {
            String msg = new String(message.getBody(), "utf-8");
            System.out.printf("广播监听器2：接收路由名称为：%s，路由键为：%s，队列名为：%s的消息：%s \n",
                    message.getMessageProperties().getReceivedExchange(),
                    message.getMessageProperties().getReceivedRoutingKey(),
                    message.getMessageProperties().getConsumerQueue(),
                    msg);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



###### 星号通配符监听器

创建 `spring-rabbitmq-consumer\src\main\java\com\itheima\rabbitmq\listener\TopicListenerStar.java`

```java
public class TopicListenerStar implements MessageListener {
    public void onMessage(Message message) {
        try {
            String msg = new String(message.getBody(), "utf-8");

            System.out.printf("通配符*监听器：接收路由名称为：%s，路由键为：%s，队列名为：%s的消息：%s \n",
                    message.getMessageProperties().getReceivedExchange(),
                    message.getMessageProperties().getReceivedRoutingKey(),
                    message.getMessageProperties().getConsumerQueue(),
                    msg);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```



###### #号通配符监听器

创建 `spring-rabbitmq-consumer\src\main\java\com\itheima\rabbitmq\listener\TopicListenerWell.java`

```java
public class TopicListenerWell implements MessageListener {
    public void onMessage(Message message) {
        try {
            String msg = new String(message.getBody(), "utf-8");

            System.out.printf("通配符#监听器：接收路由名称为：%s，路由键为：%s，队列名为：%s的消息：%s \n",
                    message.getMessageProperties().getReceivedExchange(),
                    message.getMessageProperties().getReceivedRoutingKey(),
                    message.getMessageProperties().getConsumerQueue(),
                    msg);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```





***



### Boot整合

#### 实现流程

在Spring项目中，可以使用Spring-Rabbit去操作RabbitMQ
https://github.com/spring-projects/spring-amqp

尤其是在spring boot项目中只需要引入对应的amqp启动器依赖即可，方便的使用RabbitTemplate发送消息，使用注解接收消息

**生产者工程：**

1. application.yml文件配置RabbitMQ相关信息；
2. 在生产者工程中编写配置类，用于创建交换机和队列，并进行绑定

3. 注入RabbitTemplate对象，通过RabbitTemplate对象发送消息到交换机



**消费者工程：**

1. application.yml文件配置RabbitMQ相关信息

2. 创建消息处理类，用于接收队列中的消息并进行处理



#### 生产者工程

##### 添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
</dependencies>
```



##### 启动类

````java
@SpringBootApplication
public class ProducerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProducerApplication.class);
    }
}
````





##### 配置MQ

创建application.yml，内容如下

```yaml
spring:
  rabbitmq:
    host: 192.168.0.137
    port: 5672
    username: admin
    password: admin
    virtual-host: /sea
```



绑定交换机和队列：

```java
@Configuration
public class RabbitMqConfig {
    public static final String EXCHANGE_NAME = "boot_topic_exchange";
    public static final String QUEUE_NAME = "boot_queue";

    //1.交换机
    @Bean("bootExchange")
    public Exchange bootExchange() {
        return ExchangeBuilder.topicExchange(EXCHANGE_NAME).durable(true).build();
    }

    //2.Queue队列
    @Bean("bootQueue")
    public Queue bootQueue() {
        return QueueBuilder.durable(QUEUE_NAME).build();
    }

    //3.队列和交换机绑定关系
    /*
        1.确定队列
        2.确定交换机
        3.routing key
    */
    @Bean
    public Binding bindQueueExchange(@Qualifier("bootQueue") Queue queue, @Qualifier("bootExchange") Exchange exchange) {
        return BindingBuilder.bind(queue).to(exchange).with("boot.#").noargs();
    }
}
```



***



#### 消费者工程

##### 配置文件

application.yml + 启动类





##### 监听器

编写消息监听器com.example.rabbitmq.listener.RabbitMqListener

```java
@Component
public class RabbitMqListener {
    @RabbitListener(queues = "boot_queue")
    public void ListenerQueue(Message message) {
        System.out.println(message);
    }
}
```



***



#### 测试类

在生产者工程springboot-rabbitmq-producer中创建测试类，发送消息：

```java
@SpringBootTest(classes = ProducerApplication.class)
@RunWith(SpringRunner.class)
public class ProducerTest {
    //1.注入RabbitTemplate
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void testSend(){
     	rabbitTemplate.convertAndSend(
            RabbitMqConfig.EXCHANGE_NAME,"boot.jaskajks","boot mq hello");
    }
}
```

先运行上述测试程序（交换机和队列才能先被声明和绑定），然后启动消费者；在消费者工程springboot-rabbitmq-consumer中控制台查看是否接收到对应消息





****



## 高级特性

### 可靠性

RabbitMQ 提供了两种方式用来控制消息的投递可靠性模式：

* confirm 确认模式

* return 退回模式

rabbitmq 整个消息投递的路径为：producer-->rabbitmq broker-->exchange-->queue-->consumer

* 消息从 producer 到 exchange 则会返回一个 confirmCallback 。

* 消息从 exchange-->queue 投递失败则会返回一个 returnCallback 。

利用这两个 callback 控制消息的可靠性投递，防止消息丢失或者投递失败场景

实现方法：

* 设置ConnectionFactory的`publisher-confirms="true"` 开启确认模式

* 使用`rabbitTemplate.setConfirmCallback`设置回调函数，当消息发送到exchange后回调confirm方法，在方法中判断ack，如果为true，则发送成功，如果为false，则发送失败，需要处理

* 设置ConnectionFactory的`publisher-returns="true"`开启退回模式

* 使用`rabbitTemplate.setReturnCallback`设置退回函数，当消息从exchange路由到queue失败后，如果设置了`rabbitTemplate.setMandatory(true)`参数，则会将消息退回给producer，并执行回调函数returnedMessage

在RabbitMQ中也提供了事务机制，但是性能较差，使用channel下列方法，完成事务控制：

* txSelect()：用于将当前channel设置成transaction模式

* txCommit()：用于提交事务

* txRollback()：用于回滚事务

确认模式代码实现：

* 生产者配置文件：spring-rabbitmq-producer.xml

  ```xml
  <!--加载配置文件-->
  <context:property-placeholder location="classpath:rabbitmq.properties"/>
  
  <!-- 定义rabbitmq connectionFactory -->
  <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                             port="${rabbitmq.port}"
                             username="${rabbitmq.username}"
                             password="${rabbitmq.password}"
                             virtual-host="${rabbitmq.virtual-host}"
                             publisher-confirms="true"
                             publisher-returns="true"
                             />
  <!--定义管理交换机、队列-->
  <rabbit:admin connection-factory="connectionFactory"/>
  
  <!--定义rabbitTemplate对象操作可以在代码中方便发送消息-->
  <rabbit:template id="rabbitTemplate" connection-factory="connectionFactory"/>
  
  <!--消息可靠性投递（生产端）-->
  <rabbit:queue id="test_queue_confirm" name="test_queue_confirm"></rabbit:queue>
  <rabbit:direct-exchange name="test_exchange_confirm">
      <rabbit:bindings>
          <rabbit:binding queue="test_queue_confirm" key="confirm"></rabbit:binding>
      </rabbit:bindings>
  </rabbit:direct-exchange>
  ```

* ProducerTest

  确认模式：

  1. 确认模式开启：ConnectionFactory中开启publisher-confirms="true"
  2. 在rabbitTemplate定义ConfirmCallBack回调函数

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations = "classpath:spring-rabbitmq-producer.xml")
  public class ProducerTest {
      @Autowired
      private RabbitTemplate rabbitTemplate;
  
      @Test
      public void testConfirm() {
  
          //2. 定义回调
          rabbitTemplate.setConfirmCallback(new RabbitTemplate.ConfirmCallback() {
              /**
               * @param correlationData 相关配置信息
               * @param ack   exchange交换机 是否成功收到了消息。true 成功，false代表失败
               * @param cause 失败原因
               */
              @Override
              public void confirm(CorrelationData correlationData, boolean ack, String cause) {
                  System.out.println("confirm方法被执行了....");
                  if (ack) {
                      //接收成功
                      System.out.println("接收成功消息" + cause);
                  } else {
                      //接收失败
                      System.out.println("接收失败消息" + cause);
                      //做一些处理，让消息再次发送。
                  }
              }
          });
  
          //3. 发送消息					//错误的交换机，发送失败
          rabbitTemplate.convertAndSend("test_exchange_confirm111", "confirm", "message confirm....");
      }
  }
  ```

退回模式代码实现：

* ProducerTest

  回退模式：当消息发送给Exchange后，Exchange路由到Queue失败时才会执行ReturnCallBack

  1. 开启回退模式：publisher-returns="true"
  2. 设置ReturnCallBack
  3. 设置Exchange处理消息的模式：
     * 如果消息没有路由到Queue，则丢弃消息（默认）
     * 如果消息没有路由到Queue，返回给消息发送方ReturnCallBack

  ```java
  @Test
  public void testReturn() {
      //设置交换机处理失败消息的模式
      rabbitTemplate.setMandatory(true);
  
      //2.设置ReturnCallBack
      rabbitTemplate.setReturnCallback(new RabbitTemplate.ReturnCallback() {
          /**
               *
               * @param message   消息对象
               * @param replyCode 错误码
               * @param replyText 错误信息
               * @param exchange  交换机
               * @param routingKey 路由键
               */
          @Override
          public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
              System.out.println("return 执行了....");
  
              System.out.println(message);
              System.out.println(replyCode);
              System.out.println(replyText);
              System.out.println(exchange);
              System.out.println(routingKey);
  
              //处理
          }
      });
  
      //3. 发送消息
      rabbitTemplate.convertAndSend("test_exchange_confirm", "confirm", "message confirm....");
  }
  ```


* 消费者测试代码

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)
  @ContextConfiguration(locations = "classpath:spring-rabbitmq-consumer.xml")
  public class ConsumerTest {
      @Test
      public void test(){
          while (true){
  
          }
      }
  }
  ```

  

***



### ACK

ACK指Acknowledge，确认。 表示消费端收到消息后的确认方式，有三种确认方式：

* 自动确认：acknowledge="none"

* 手动确认：acknowledge="manual"

* 根据异常情况确认：acknowledge="auto"

其中自动确认是指，当消息一旦被Consumer接收到，则自动确认收到，并将相应 message 从 RabbitMQ 的消息缓存中移除。但是在实际业务处理中，很可能消息接收到，业务处理出现异常，那么该消息就会丢失。如果设置了手动确认方式，则需要在业务处理成功后，调用channel.basicAck()，手动签收，如果出现异常，则调用channel.basicNack()方法，让其自动重新发送消息

Consumer ACK机制：
1. 设置手动签收，在rabbit:listener-container标签中设置acknowledge属性，设置ack方式 none：自动确认，manual：手动确认
2. 让监听器类实现ChannelAwareMessageListener接口
3. 如果消息成功处理，则调用channel的 basicAck()签收
4. 如果消息处理失败，则调用channel的basicNack()拒绝签收，broker重新发送给consumer

消费者代码实现：

* 配置文件spring-rabbitmq-consumer.xml：

  ```xml
  <!--加载配置文件-->
  <context:property-placeholder location="classpath:rabbitmq.properties"/>
  
  <!-- 定义rabbitmq connectionFactory -->
  <rabbit:connection-factory id="connectionFactory" host="${rabbitmq.host}"
                             port="${rabbitmq.port}"
                             username="${rabbitmq.username}"
                             password="${rabbitmq.password}"
                             virtual-host="${rabbitmq.virtual-host}"/>
  
  <context:component-scan base-package="com.itheima.listener" />
  
  <!--定义监听器容器-->
  <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" >
      <rabbit:listener ref="ackListener" queue-names="test_queue_confirm">	
      </rabbit:listener>
  </rabbit:listener-container>
  ```

* 监听器：com.itheima.listener.AckListener

  ```java
  @Component
  public class AckListener implements ChannelAwareMessageListener {
      @Override
      public void onMessage(Message message, Channel channel) throws Exception {
          long deliveryTag = message.getMessageProperties().getDeliveryTag();
  
          try {
              //1.接收转换消息
              System.out.println(new String(message.getBody()));
              //2. 处理业务逻辑
              System.out.println("处理业务逻辑...");
              int i = 3/0;//出现错误
              //3. 手动签收
              channel.basicAck(deliveryTag,true);
          } catch (Exception e) {
              //e.printStackTrace();
  
              //4.拒绝签收
              //第三个参数：requeue：重回队列。如果设置为true，则消息重新回到queue，broker会重新发送该消息给消费端
              channel.basicNack(deliveryTag,true,true);
              //channel.basicReject(deliveryTag,true);
          }
      }
  }
  ```



***



### 限流

消费端限流模型：
![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-消费端限流.png)

Consumer 限流机制：
1. 确保ack机制为手动确认
2. <rabbit:listener-container > 配置属性：perfetch = 1，表示消费端每次从mq拉去一条消息来消费，直到手动确认消费完毕后，才会继续拉去下一条消息。

消费者代码实现：

* 配置文件：spring-rabbitmq-consumer.xml

  ```xml
  <!--定义监听器容器-->
  <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" prefetch="1" >
      <rabbit:listener ref="ackListener" queue-names="test_queue_confirm">	
      </rabbit:listener>
  </rabbit:listener-container>
  ```

* 监听器：com.itheima.listener.QosListener

  ```java
  @Component
  public class QosListener implements ChannelAwareMessageListener {
      @Override
      public void onMessage(Message message, Channel channel) throws Exception {
          Thread.sleep(1000);
          //1.获取消息
          System.out.println(new String(message.getBody()));
  
          //2. 处理业务逻辑
  
          //3. 签收
          channel.basicAck(message.getMessageProperties().getDeliveryTag(),true);
  
      }
  }
  ```

生产者代码实现：

* ProducerTest

  ```java
  @Test
  public void testSend() {
      for (int i = 0; i < 10; i++) {
          // 发送消息
          rabbitTemplate.convertAndSend("test_exchange_confirm", "confirm", "message confirm....");
      }
  }
  ```

  

***



### TTL

TTL 全称 Time To Live（存活时间/过期时间）

* 当消息到达存活时间后，还没有被消费，会被自动清除

* RabbitMQ可以**对消息设置过期时间**，也可以**对整个队列（Queue）设置过期时间**

基本规则：

* 如果设置了消息的过期时间，也设置了队列的过期时间，它以时间短的为准

  * 设置队列过期时间使用参数：x-message-ttl，单位 ms，会对整个队列消息统一过期

  * 设置消息过期时间使用参数：expiration，单位 ms，当该消息在队列头部时（消费时），会单独判断这一消息是否过期

* 队列过期后，会将队列所有消息全部移除

* 消息过期后，只有消息在队列顶端，才会判断其是否过期(移除掉)

生产者代码实现：

* 配置文件：spring-rabbitmq-consumer.xml

  ```xml
  <!--ttl-->
  <rabbit:queue name="test_queue_ttl" id="test_queue_ttl">
      <!--设置queue的参数-->
      <rabbit:queue-arguments>
          <!--x-message-ttl指队列的过期时间-->
          <entry key="x-message-ttl" value="10000" value-type="java.lang.Integer"/>
      </rabbit:queue-arguments>
  </rabbit:queue>
  <rabbit:topic-exchange name="test_exchange_ttl" >
      <rabbit:bindings>
          <rabbit:binding pattern="ttl.#" queue="test_queue_ttl"></rabbit:binding>
      </rabbit:bindings>
  </rabbit:topic-exchange>
  ```

* ProducerTest

  ```java
  @Test
  public void testTtl() {
      // 消息后处理对象，设置一些消息的参数信息
      MessagePostProcessor messagePostProcessor = new MessagePostProcessor() {
          @Override
          public Message postProcessMessage(Message message) throws AmqpException {
              //1.设置message的信息
              message.getMessageProperties().setExpiration("5000");//消息的过期时间
              //2.返回该消息
              return message;
          }
      };
  
      //消息单独过期
      //rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "message ttl....",messagePostProcessor);
  
  
      for (int i = 0; i < 10; i++) {
          if(i == 5){
              //消息单独过期
              rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "message ttl....",messagePostProcessor);
          }else{
              //不过期的消息
              rabbitTemplate.convertAndSend("test_exchange_ttl", "ttl.hehe", "message ttl....");
          }
      }
  }
  ```



***



### 死信队列

死信队列，英文缩写：DLX (Dead Letter Exchange 死信交换机)，当消息成为Dead message后，可以被重新发送到另一个交换机，这个交换机就是DLX

**消息成为死信的三种情况：**

* 队列消息长度到达限制
* 消费者拒接消费消息，basicNack/basicReject，并且不把消息重新放入原目标队列 requeue=false
* 原队列存在消息过期设置，消息到达超时时间未被消费

队列绑定死信交换机：给队列设置参数： x-dead-letter-exchange 和 x-dead-letter-routing-key

![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-死信交换机.png)

生产者代码实现

* 配置文件：spring-rabbitmq-producer.xml

  1. 声明正常的队列(test_queue_dlx)和交换机(test_exchange_dlx)
  2. 声明死信队列(queue_dlx)和死信交换机(exchange_dlx)
  3. 正常队列绑定死信交换机，设置两个参数：
         * x-dead-letter-exchange：死信交换机名称
         * dead-letter-routing-key：发送给死信交换机的routingkey

  ```xml
  <!--1. 声明正常的队列(test_queue_dlx)和交换机(test_exchange_dlx) -->
  <rabbit:queue name="test_queue_dlx" id="test_queue_dlx">
      <!--3. 正常队列绑定死信交换机-->
      <rabbit:queue-arguments>
          <!--3.1 x-dead-letter-exchange：死信交换机名称-->
          <entry key="x-dead-letter-exchange" value="exchange_dlx" />
          <!--3.2 x-dead-letter-routing-key：发送给死信交换机的routingkey-->
          <entry key="x-dead-letter-routing-key" value="dlx.hehe" />
  
          <!--4.1 设置队列的过期时间 ttl-->
          <entry key="x-message-ttl" value="10000" value-type="java.lang.Integer" />
          <!--4.2 设置队列的长度限制 max-length -->
          <entry key="x-max-length" value="10" value-type="java.lang.Integer" />
      </rabbit:queue-arguments>
  </rabbit:queue>
  <rabbit:topic-exchange name="test_exchange_dlx">
      <rabbit:bindings>
          <rabbit:binding pattern="test.dlx.#" queue="test_queue_dlx"></rabbit:binding>
      </rabbit:bindings>
  </rabbit:topic-exchange>
  
  <!-- 2. 声明死信队列(queue_dlx)和死信交换机(exchange_dlx) -->
  <rabbit:queue name="queue_dlx" id="queue_dlx"></rabbit:queue>
  <rabbit:topic-exchange name="exchange_dlx">
      <rabbit:bindings>
          <rabbit:binding pattern="dlx.#" queue="queue_dlx"></rabbit:binding>
      </rabbit:bindings>
  </rabbit:topic-exchange>
  ```

* ProducerTest

  ```java
  /**
   * 发送测试死信消息：
   *  1. 过期时间
   *  2. 长度限制
   *  3. 消息拒收
   */
  @Test
  public void testDlx(){
      //1. 测试过期时间，死信消息
      //rabbitTemplate.convertAndSend("test_exchange_dlx","test.dlx.haha","我是一条消息，我会死吗？");
  
      //2. 测试长度限制后，消息死信
      /* for (int i = 0; i < 20; i++) {
              rabbitTemplate.convertAndSend("test_exchange_dlx","test.dlx.haha","我是一条消息，我会死吗？");
          }*/
  
      //3. 测试消息拒收
      rabbitTemplate.convertAndSend("test_exchange_dlx","test.dlx.haha","我是一条消息，我会死吗？");
  }
  ```

消费者代码实现：

* 监听器：com.itheima.listener.DlxListener

  ```java
  @Component
  public class DlxListener implements ChannelAwareMessageListener {
      @Override
      public void onMessage(Message message, Channel channel) throws Exception {
          long deliveryTag = message.getMessageProperties().getDeliveryTag();
          try {
              //1.接收转换消息
              System.out.println(new String(message.getBody()));
              //2. 处理业务逻辑
              System.out.println("处理业务逻辑...");
              int i = 3/0;//出现错误
              //3. 手动签收
              channel.basicAck(deliveryTag,true);
          } catch (Exception e) {
              //e.printStackTrace();
              System.out.println("出现异常，拒绝接受");
              //4.拒绝签收，不重回队列 requeue=false
              channel.basicNack(deliveryTag,true,false);
          }
      }
  }
  ```

* 配置文件：spring-rabbitmq-consumer.xml

  ```xml
  <!--定义监听器容器-->
  <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" prefetch="1" >
      <!--定义监听器，监听正常队列-->
      <rabbit:listener ref="dlxListener" queue-names="test_queue_dlx">
      </rabbit:listener>
  </rabbit:listener-container>
  ```



***



### 延迟队列

延迟队列，即消息进入队列后不会立即被消费，只有到达指定时间后，才会被消费

应用场景：下单后，30分钟未支付，取消订单，回滚库存

实现方式：定时器、延迟队列

RabbitMQ中并未提供延迟队列功能，可以使用：**TTL+死信队列**组合实现延迟队列的效果

![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-延迟队列.png)

生产者代码实现：

* 配置文件：spring-rabbitmq-producer.xml

  延迟队列：
         1. 定义正常交换机（order_exchange）和队列(order_queue)
         2. 定义死信交换机（order_exchange_dlx）和队列(order_queue_dlx)
         3. 绑定，设置正常队列过期时间为30分钟

  ```xml
  <!-- 1. 定义正常交换机（order_exchange）和队列(order_queue)-->
  <rabbit:queue id="order_queue" name="order_queue">
      <!-- 3. 绑定，设置正常队列过期时间为30分钟-->
      <rabbit:queue-arguments>
          <entry key="x-dead-letter-exchange" value="order_exchange_dlx" />
          <entry key="x-dead-letter-routing-key" value="dlx.order.cancel" />
          <entry key="x-message-ttl" value="30*60*1000" value-type="java.lang.Integer"/>
      </rabbit:queue-arguments>
  </rabbit:queue>
  <rabbit:topic-exchange name="order_exchange">
      <rabbit:bindings>
          <rabbit:binding pattern="order.#" queue="order_queue"></rabbit:binding>
      </rabbit:bindings>
  </rabbit:topic-exchange>
  
  <!--  2. 定义死信交换机（order_exchange_dlx）和队列(order_queue_dlx)-->
  <rabbit:queue id="order_queue_dlx" name="order_queue_dlx"></rabbit:queue>
  <rabbit:topic-exchange name="order_exchange_dlx">
      <rabbit:bindings>
          <rabbit:binding pattern="dlx.order.#" queue="order_queue_dlx"></rabbit:binding>
      </rabbit:bindings>
  </rabbit:topic-exchange>
  ```

* ProducerTest

  ```java
  @Test
  public  void testDelay() throws InterruptedException {
      //1.发送订单消息。 将来是在订单系统中，下单成功后，发送消息
      rabbitTemplate.convertAndSend("order_exchange","order.msg","订单信息：id=1,time=202年3月17日16:41:47");
  }
  ```

消费者代码实现：

* 配置文件：spring-rabbitmq-consumer.xml

  ```xml
   <!--定义监听器容器-->
  <rabbit:listener-container connection-factory="connectionFactory" acknowledge="manual" prefetch="1" >
      <!--延迟队列效果实现：  一定要监听的是 死信队列！！！-->
      <rabbit:listener ref="orderListener" queue-names="order_queue_dlx">
      </rabbit:listener>
  </rabbit:listener-container>
  ```

* 监听器：com.itheima.listener.OrderListener

  ```java
  @Component
  public class OrderListener implements ChannelAwareMessageListener {
      @Override
      public void onMessage(Message message, Channel channel) throws Exception {
          long deliveryTag = message.getMessageProperties().getDeliveryTag();
          try {
              //1.接收转换消息
              System.out.println(new String(message.getBody()));
              //2. 处理业务逻辑
              System.out.println("处理业务逻辑...");
              System.out.println("根据订单id查询其状态...");
              System.out.println("判断状态是否为支付成功");
              System.out.println("取消订单，回滚库存....");
              //3. 手动签收
              channel.basicAck(deliveryTag,true);
          } catch (Exception e) {
              //e.printStackTrace();
              System.out.println("出现异常，拒绝接受");
              //4.拒绝签收，不重回队列 requeue=false
              channel.basicNack(deliveryTag,true,false);
          }
      }
  }
  ```



****



### 日志监控

RabbitMQ默认日志存放路径： /var/log/rabbitmq/rabbit@xxx.log

日志包含了RabbitMQ的版本号、Erlang的版本号、RabbitMQ服务节点名称、cookie的hash值、RabbitMQ配置文件地址、内存限制、磁盘限制、默认账户guest的创建以及权限配置等

```sh
rabbitmqctl list_queues			#查看队列	
rabbitmqctl list_exchanges		#查看exchanges
rabbitmqctl list_users			#查看用户
rabbitmqctl list_connections	#查看连接
rabbitmqctl list_consumers		#查看消费者信息
rabbitmqctl environment			#查看环境变量
rabbitmqctl list_queues name memory			#查看单个队列的内存使用
rabbitmqctl list_queues name messages_ready	#查看准备就绪的队列
rabbitmqctl list_queues name messages_unacknowledged	#查看未被确认的队列
```



***



### 消息追踪

RabbitMQ中使用Firehose和rabbitmq_tracing插件功能来实现消息追踪

应用：在使用任何消息中间件的过程中，可能会出现某条消息异常丢失的情况。生产者或消费者与RabbitMQ断开了连接；交换器与队列之间不同的转发策略；交换器并没有与任何队列进行绑定，生产者又不感知或者没有采取相应的措施；RabbitMQ本身的集群策略也可能导致消息的丢失。这时需要有一个较好的机制跟踪记录消息的投递过程，有助于进行问题的定位

**firehose**：firehose机制是将生产者投递给rabbitmq的消息，rabbitmq投递给消费者的消息按照指定的格式发送到默认的exchange上，这个默认的exchange的名称为amq.rabbitmq.trace，它是一个topic类型的exchange。发送到这个exchange上的消息的routing key为 publish.exchangename 和 deliver.queuename。其中exchangename和queuename为实际exchange和queue的名称，分别对应生产者投递到exchange的消息，和消费者从queue上获取的消息。

注意：打开 trace 会影响消息写入功能，适当打开后请关闭，Linux命令

* rabbitmqctl trace_on：开启Firehose命令

* rabbitmqctl trace_off：关闭Firehose命令 

**rabbitmq_tracing**和Firehose在实现上如出一辙，只不过rabbitmq_tracing的方式比Firehose多了一层GUI的包装，更容易使用和管理

* 启用插件：rabbitmq-plugins enable rabbitmq_tracing





***



## 应用问题

### 可靠性保障

需求：100%确保消息发送成功

**消息补偿机制**：

![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-消息补偿.png)



***



### 幂等性保障

**幂等性**指一次和多次请求某一个资源，对于资源本身应该具有同样的结果。也就是其任意多次执行对资源本身所产生的影响均与一次执行的影响相同。

在MQ中指，消费多条相同的消息，得到与消费该消息一次相同的结果，防止双重支付问题

MySQL乐观锁机制：

![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-幂等性.png)



****



## 集群搭建

### 原理概述

RabbitMQ这款消息队列中间件产品是基于Erlang编写，Erlang语言天生具备分布式特性（通过同步Erlang集群各节点的magic cookie来实现）。RabbitMQ支持Clustering，这使得RabbitMQ本身不需要像ActiveMQ、Kafka那样通过ZooKeeper分别来实现HA方案和保存集群的元数据。集群是保证可靠性的一种方式，同时可以通过水平扩展以达到增加消息吞吐量能力的目的

![](https://gitee.com/seazean/images/raw/master/Frame/RabbitMQ-集群方案.png)



### 单机部署

单机多实例部署

参考官方文档：https://www.rabbitmq.com/clustering.html



****



### 集群管理

**rabbitmqctl join_cluster {cluster_node} [–ram]**
将节点加入指定集群中。在这个命令执行前需要停止RabbitMQ应用并重置节点。

**rabbitmqctl cluster_status**
显示集群的状态。

**rabbitmqctl change_cluster_node_type {disc|ram}**
修改集群节点的类型。在这个命令执行前需要停止RabbitMQ应用。

**rabbitmqctl forget_cluster_node [–offline]**
将节点从集群中删除，允许离线执行。

**rabbitmqctl update_cluster_nodes {clusternode}**
在集群中的节点应用启动前咨询clusternode节点的最新信息，并更新相应的集群信息。这个和join_cluster不同，它不加入集群。考虑这样一种情况，节点A和节点B都在集群中，当节点A离线了，节点C又和节点B组成了一个集群，然后节点B又离开了集群，当A醒来的时候，它会尝试联系节点B，但是这样会失败，因为节点B已经不在集群中了

**rabbitmqctl cancel_sync_queue [-p vhost] {queue}**
取消队列queue同步镜像的操作

**rabbitmqctl set_cluster_name {name}**
设置集群名称。集群名称在客户端连接时会通报给客户端。Federation和Shovel插件也会有用到集群名称的地方。集群名称默认是集群中第一个节点的名称，通过这个命令可以重新设置



***



### 负载均衡

HAProxy提供高可用性、负载均衡以及基于TCP和HTTP应用的代理，支持虚拟主机，它是免费、快速并且可靠的一种解决方案,包括Twitter，Reddit，StackOverflow，GitHub在内的多家知名互联网公司在使用。HAProxy实现了一种事件驱动、单一进程模型，此模型支持非常大的并发连接数



