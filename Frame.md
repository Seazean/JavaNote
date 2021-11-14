# Maven

## 基本介绍

### Mvn介绍

Maven：本质是一个项目管理工具，将项目开发和管理过程抽象成一个项目对象模型（POM）

POM：Project Object Model 项目对象模型。Maven 是用 Java 语言编写的，管理的东西以面向对象的形式进行设计，最终把一个项目看成一个对象，这个对象叫做 POM

pom.xml：Maven 需要一个  pom.xml 文件，Maven 通过加载这个配置文件可以知道项目的相关信息，这个文件代表就一个项目。如果做 8 个项目，对应的是 8 个 pom.xml 文件

依赖管理：Maven 对项目所有依赖资源的一种管理，它和项目之间是一种双向关系，即做项目时可以管理所需要的其他资源，当其他项目需要依赖我们项目时，Maven 也会把我们的项目当作一种资源去进行管理。

管理资源的存储位置：本地仓库，私服，中央仓库

![](https://gitee.com/seazean/images/raw/master/Frame/Maven介绍.png)

基本作用：

* 项目构建：提供标准的，跨平台的自动化构建项目的方式

* 依赖管理：方便快捷的管理项目依赖的资源（jar 包），避免资源间的版本冲突等问题

* 统一开发结构：提供标准的，统一的项目开发结构

  ![](https://gitee.com/seazean/images/raw/master/Frame/Maven标准结构.png)

各目录存放资源类型说明：

* src/main/java：项目 java 源码

* src/main/resources：项目的相关配置文件（比如 mybatis 配置，xml 映射配置，自定义配置文件等）

* src/main/webapp：web 资源（比如 html、css、js 等）

* src/test/java：测试代码

* src/test/resources：测试相关配置文件

* src/pom.xml：项目 pom 文件



***



### 基础概念

仓库：用于存储资源，主要是各种 jar 包。有本地仓库，私服，中央仓库，私服和中央仓库都是远程仓库

* 中央仓库：Maven 团队自身维护的仓库，属于开源的

* 私服：各公司/部门等小范围内存储资源的仓库，私服也可以从中央仓库获取资源，作用：
  * 保存具有版权的资源，包含购买或自主研发的 jar
  * 一定范围内共享资源，能做到仅对内不对外开放

* 本地仓库：开发者自己电脑上存储资源的仓库，也可从远程仓库获取资源



坐标：Maven 中的坐标用于描述仓库中资源的位置

* 作用：使用唯一标识，唯一性定义资源位置，通过该标识可以将资源的识别与下载工作交由机器完成
  * https://mvnrepository.com：查询 maven 某一个资源的坐标，输入资源名称进行检索

 * 依赖设置：
   * groupId：定义当前资源隶属组织名称（通常是域名反写，如：org.mybatis）
 * artifactId：定义当前资源的名称（通常是项目或模块名称，如：crm、sms）
   * version：定义当前资源的版本号

* packaging：定义资源的打包方式，取值一般有如下三种

  * jar：该资源打成 jar 包，默认是 jar

  * war：该资源打成 war 包

  * pom：该资源是一个父资源（表明使用 Maven 分模块管理），打包时只生成一个 pom.xml 不生成 jar 或其他包结构





***



## 环境搭建

### 环境配置

Maven 的官网：http://maven.apache.org/

下载安装：Maven 是一个绿色软件，解压即安装

目录结构：

* bin：可执行程序目录
* boot：Maven 自身的启动加载器
* conf：Maven 配置文件的存放目录
* lib：Maven运行所需库的存放目录

配置 MAVEN_HOME：

![](https://gitee.com/seazean/images/raw/master/Frame/Maven配置环境变量.png)

Path 下配置：`%MAVEN_HOME%\bin`

环境变量配置好之后需要测试环境配置结果，在 DOS 命令窗口下输入以下命令查看输出：`mvn -v`



***



### 仓库配置

默认情况 Maven 本地仓库在系统用户目录下的 `.m2/repository`，修改 Maven 的配置文件 `conf/settings.xml` 来修改仓库位置

* 修改本地仓库位置：找到 <localRepository> 标签，修改默认值

  ```xml
  <!-- localRepository
  | The path to the local repository maven will use to store artifacts.
  | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
  <localRepository>E:\Workspace\Java\Project\.m2\repository</localRepository>
  ```

  注意：在仓库的同级目录即 `.m2` 也应该包含一个 `settings.xml` 配置文件，局部用户配置优先与全局配置

  * 全局 setting 定义了 Maven 的公共配置
  * 用户 setting 定义了当前用户的配置

* 修改远程仓库：在配置文件中找到 `<mirrors>` 标签，在这组标签下添加国内镜像

  ```xml
  <mirror>
      <id>nexus-aliyun</id>
      <mirrorOf>central</mirrorOf>  <!--必须是central-->
      <name>Nexus aliyun</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
  </mirror>
  ```

* 修改默认 JDK：在配置文件中找到 `<profiles>` 标签，添加配置

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

1. 在 E 盘下创建目录 mvnproject 进入该目录，作为我们的操作目录

2. 创建我们的 Maven 项目，创建一个目录 `project-java` 作为我们的项目文件夹，并进入到该目录

3. 创建 Java 代码（源代码）所在目录，即创建 `src/main/java`

4. 创建配置文件所在目录，即创建 `src/main/resources`

5. 创建测试源代码所在目录，即创建 `src/test/java`

6. 创建测试存放配置文件存放目录，即 `src/test/resources`

7. 在 `src/main/java` 中创建一个包（注意在 Windos 文件夹下就是创建目录）`demo`，在该目录下创建 `Demo.java` 文件，作为演示所需 Java 程序，内容如下

   ```java
   package demo;
   public class Demo{
   	public String say(String name){
   		System.out.println("hello "+name);
   		return "hello "+name;
   	}
   }
   ```

8. 在 `src/test/java` 中创建一个测试包（目录）`demo`，在该包下创建测试程序 `DemoTest.java`

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

9. **在 `project-java/src` 下创建 `pom.xml` 文件，格式如下：**

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

10. 搭建完成 Maven 的项目结构，通过 Maven 来构建项目。Maven 的构建命令以 `mvn` 开头，后面添加功能参数，可以一次性执行多个命令，用空格分离

    * `mvn compile`：编译
    * `mvn clean`：清理
    * `mvn test`：测试
    * `mvn package`：打包
    * `mvn install`：安装到本地仓库

    注意：执行某一条命令，则会把前面所有的都执行一遍



***



### 插件构建

![](https://gitee.com/seazean/images/raw/master/Frame/Maven-插件构建.png)



***



### IDEA搭建

#### 不用原型

1. 在 IDEA 中配置 Maven，选择 maven3.6.1 防止依赖问题
   <img src="https://gitee.com/seazean/images/raw/master/Frame/IDEA配置Maven.png" alt="IDEA配置Maven" style="zoom:67%;" />

2. 创建 Maven，New Module → Maven → 不选中 Create from archetype

3. 填写项目的坐标

   * GroupId：demo
   * ArtifactId：project-java

4. 查看各目录颜色标记是否正确

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA创建Maven目录结构.png)

5. IDEA 右侧侧栏有 Maven Project，打开后有 Lifecycle 生命周期

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA-Maven生命周期.png)

6. 自定义 Maven 命令：Run → Edit Configurations → 左上角 +  → Maven

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA配置Maven命令.png)



***



#### 使用原型

普通工程：

1. 创建 Maven 项目的时候选择使用原型骨架

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA创建Maven-quickstart.png)

2. 创建完成后发现通过这种方式缺少一些目录，需要手动去补全目录，并且要对补全的目录进行标记



Web 工程：

1. 选择 Web 对应的原型骨架（选择 Maven 开头的是简化的）

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA创建Maven-webapp.png)

2. 通过原型创建 Web 项目得到的目录结构是不全的，因此需要我们自行补全，同时要标记正确

3. Web 工程创建之后需要启动运行，使用 tomcat 插件来运行项目，在 `pom.xml` 中添加插件的坐标：

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

4. 插件配置以后，在 IDEA 右侧 `maven-project` 操作面板看到该插件，并且可以利用该插件启动项目，web01 → Plugins → tomcat7 → tomcat7:run

   

***



## 依赖管理

### 依赖配置

依赖是指在当前项目中运行所需的 jar，依赖配置的格式如下：

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

**可选依赖**：对外隐藏当前所依赖的资源，不透明

```xml
<dependency>    
    <groupId>junit</groupId>    
    <artifactId>junit</artifactId>    
    <version>4.11</version>    
    <optional>true</optional> 
    <!--默认是false，true以后就变得不透明-->
</dependency>
```

**排除依赖**：主动断开依赖的资源，被排除的资源无需指定版本

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

依赖的 jar 默认情况可以在任何地方可用，可以通过 `scope` 标签设定其作用范围，有三种：

* 主程序范围有效（src/main 目录范围内）

* 测试程序范围内有效（src/test 目录范围内）

* 是否参与打包（package 指令范围内）

`scope` 标签的取值有四种：`compile,test,provided,runtime`

![](https://gitee.com/seazean/images/raw/master/Frame/Maven依赖范围.png)



**依赖范围的传递性：**

![](https://gitee.com/seazean/images/raw/master/Frame/Maven依赖范围的传递性.png)





***



## 生命周期

### 相关事件

Maven 的构建生命周期描述的是一次构建过程经历了多少个事件

最常用的一套流程：compile → test-compile → test → package → install

* clean：清理工作

  * pre-clean：执行一些在 clean 之前的工作
  * clean：移除上一次构建产生的所有文件
  * post-clean：执行一些在 clean 之后立刻完成的工作

* default：核心工作，例如编译，测试，打包，部署等

  对于 default 生命周期，每个事件在执行之前都会**将之前的所有事件依次执行一遍**

  ![](https://gitee.com/seazean/images/raw/master/Frame/Maven-default生命周期.png)

* site：产生报告，发布站点等

  * pre-site：执行一些在生成站点文档之前的工作
  * site：生成项目的站点文档
  * post-site：执行一些在生成站点文档之后完成的工作，并为部署做准备
  * site-deploy：将生成的站点文档部署到特定的服务器上



***



### 执行事件

Maven 的插件用来执行生命周期中的相关事件

- 插件与生命周期内的阶段绑定，在执行到对应生命周期时执行对应的插件

- Maven 默认在各个生命周期上都绑定了预先设定的插件来完成相应功能

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

* ssm_pojo 拆分

  * 新建模块，拷贝原始项目中对应的相关内容到 ssm_pojo 模块中
  * 实体类（User）
  * 配置文件（无） 

* ssm_dao 拆分

  * 新建模块

  * 拷贝原始项目中对应的相关内容到 ssm_dao 模块中

    - 数据层接口（UserDao）

    - 配置文件：保留与数据层相关配置文件(3 个）

    - 注意：分页插件在配置中与 SqlSessionFactoryBean 绑定，需要保留

    - pom.xml：引入数据层相关坐标即可，删除 SpringMVC 相关坐标

      - Spring
      - MyBatis
      - Spring 整合 MyBatis
      - MySQL
      - druid
      - pagehelper
      - 直接依赖 ssm_pojo（对 ssm_pojo 模块执行 install 指令，将其安装到本地仓库）

      ```xml
      <dependencies>    <!--导入资源文件pojo-->    
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

* ssm_service 拆分

  * 新建模块
  * 拷贝原始项目中对应的相关内容到 ssm_service 模块中

    - 业务层接口与实现类（UserService、UserServiceImpl）
    - 配置文件：保留与数据层相关配置文件(1 个）
    - pom.xml：引入数据层相关坐标即可，删除 SpringMVC 相关坐标

      - spring

      - junit

      - spring 整合 junit

      - 直接依赖 ssm_dao（对 ssm_dao 模块执行 install 指令，将其安装到本地仓库）

      - 间接依赖 ssm_pojo（由 ssm_dao 模块负责依赖关系的建立）
    - 修改 service 模块 Spring 核心配置文件名，添加模块名称，格式：applicationContext-service.xml
    - 修改 dao 模块 Spring 核心配置文件名，添加模块名称，格式：applicationContext-dao.xml
    - 修改单元测试引入的配置文件名称，由单个文件修改为多个文件

* ssm_control 拆分

  * 新建模块（使用 webapp 模板）

  * 拷贝原始项目中对应的相关内容到 ssm_controller 模块中

    - 现层控制器类与相关设置类（UserController、异常相关……）

    - 配置文件：保留与表现层相关配置文件(1 个）、服务器相关配置文件（1 个）

    - pom.xml：引入数据层相关坐标即可，删除 SpringMVC 相关坐标

      - spring

      - springmvc

      - jackson

      - servlet

      - tomcat 服务器插件

      - 直接依赖 ssm_service（对 ssm_service 模块执行 install 指令，将其安装到本地仓库）

      - 间接依赖 ssm_dao、ssm_pojo

    
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
    
    - 修改 web.xml 配置文件中加载 Spring 环境的配置文件名称，使用*通配，加载所有 applicationContext- 开始的配置文件：
    
      ```xml
      <!--加载配置文件-->
      <context-param>   
          <param-name>contextConfigLocation</param-name>  
          <param-value>classpath*:applicationContext-*.xml</param-value>
      </context-param>
      ```
    
    - spring-mvc
    
      ```xml
      <mvc:annotation-driven/><context:component-scan base-package="controller"/>
      ```
    
      

***



### 聚合

作用：聚合用于快速构建 Maven 工程，一次性构建多个项目/模块

制作方式：

- 创建一个空模块，打包类型定义为 pom

  ```xml
  <packaging>pom</packaging>
  ```

- 定义当前模块进行构建操作时关联的其他模块名称

  ```xml
  <?xml version="1.0" encoding="UTF-8"?><project xmlns="............">   
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
      </modules></project>
  ```

注意事项：参与聚合操作的模块最终执行顺序与模块间的依赖关系有关，与配置顺序无关



***



### 继承

作用：通过继承可以实现在子工程中沿用父工程中的配置

- Maven 中的继承与 Java 中的继承相似，在子工程中配置继承关系

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
      </dependencies>
  </dependencyManagement>
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
  scm：项目的版本控制系统信息
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

  - 聚合与继承的 pom.xml 文件打包方式均为 pom，可以将两种关系制作到同一个 pom 文件中

  - 聚合与继承均属于设计型模块，并无实际的模块内容

  不同点：

  - 聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些

  - 继承是在子模块中配置关系，父模块无法感知哪些子模块继承了自己



***



### 属性

* 版本统一的重要性： 

  ![](https://gitee.com/seazean/images/raw/master/Frame/Maven版本统一的重要性.png)

* 属性类别：

  1. 自定义属性  
  2. 内置属性
  3. setting 属性
  4. Java 系统属性
  5. 环境变量属性

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

  - 聚合与继承的 pom.xml 文件打包方式均为 pom，可以将两种关系制作到同一个 pom 文件中

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

  作用：使用 Maven 内置属性，快速配置

  调用格式：

  ```xml
  ${project.basedir} or ${project.basedir}  <!--../ssm根目录-->${version} or ${project.version}
  ```

  * vresion 是 1.0-SNAPSHOT


  ```xml
  <groupId>demo</groupId>
  <artifactId>ssm</artifactId>
  <version>1.0-SNAPSHOT</version>
  ```

* setting 属性

  - 使用 Maven 配置文件 setting.xml 中的标签属性，用于动态配置

  调用格式：

  ```xml
  ${settings.localRepository} 
  ```

* Java 系统属性：

  作用：读取 Java 系统属性

  调用格式：

  ```xml
  ${user.home}
  ```

  系统属性查询方式 cmd 命令：

  ```sh
  mvn help:system 
  ```

* 环境变量属性

  作用：使用 Maven 配置文件 setting.xml 中的标签属性，用于动态配置

  调用格式：

  ```xml
  ${env.JAVA_HOME} 
  ```

  环境变量属性查询方式：

  ```sh
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

- 主版本：表示项目重大架构的变更，如：Spring5 相较于 Spring4 的迭代

- 次版本：表示有较大的功能增加和变化，或者全面系统地修复漏洞

- 增量版本：表示有重大漏洞的修复

- 里程碑版本：表明一个版本的里程碑（版本内部）。这样的版本同下一个正式版本相比，相对来说不是很稳定，有待更多的测试





***





### 资源配置

作用：在任意配置文件中加载 pom 文件中定义的属性

* 父文件 pom.xml

  ```xml
  <properties>    
      <jdbc.url>jdbc:mysql://192.168.0.137:3306/ssm_db?useSSL=false</jdbc.url></properties>
  ```

- 开启配置文件加载 pom 属性：

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

* properties 文件中调用格式：

  ```properties
  jdbc.driver=com.mysql.jdbc.Driverjdbc.url=${jdbc.url}
  jdbc.username=rootjdbc.password=123456
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

  ```sh
  mvn 指令 –P 环境定义id
  ```

  范例：

  ```sh
  mvn install –P pro_env
  ```




***



## 跳过测试

命令：

```sh
mvn 指令 –D skipTests
```

注意事项：执行的指令生命周期必须包含测试环节



IEDA 界面：

![](https://gitee.com/seazean/images/raw/master/Frame/IDEA使用界面操作跳过测试.png)



配置跳过：

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

Nexus 是 Sonatype 公司的一款 Maven 私服产品

下载地址：https://help.sonatype.com/repomanager3/download 

启动服务器（命令行启动）：

```sh
nexus.exe /run nexus
```

访问服务器（默认端口：8081）：

```sh
http://localhost:8081
```

修改基础配置信息

- 安装路径下 etc 目录中 nexus-default.properties 文件保存有 nexus 基础配置信息，例如默认访问端口

修改服务器运行配置信息

- 安装路径下 bin 目录中 nexus.vmoptions 文件保存有 nexus 服务器启动的配置信息，例如默认占用内存空间



***



### 资源操作

![](https://gitee.com/seazean/images/raw/master/Frame/Maven私服资源获取.png)



仓库分类：

* 宿主仓库 hosted 
  * 保存无法从中央仓库获取的资源
    * 自主研发
    * 第三方非开源项目

* 代理仓库 proxy 
  * 代理远程仓库，通过 nexus 访问其他公共仓库，例如中央仓库

* 仓库组 group 
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



#### 访问私服

##### 本地访问

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



***



##### 工程访问

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

```sh
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

Log4j 是 Apache 的一个开源项目。使用 Log4j，通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。我们可以控制日志信息输送的目的地是控制台、文件等位置，也可以控制每一条日志的输出格式。

<img src="https://gitee.com/seazean/images/raw/master/Frame/日志体系结构.png" style="zoom:50%;" />



***



### 配置文件

配置文件的三个核心：

+ 配置根 Logger

  + 格式：log4j.rootLogger=日志级别，appenderName1，appenderName2，…

  + 日志级别：常见的五个级别：**DEBUG < INFO < WARN < ERROR < FATAL**（可以自定义）
    Log4j规则：只输出级别不低于设定级别的日志信息

  + appenderName1：指定日志信息要输出地址。可以同时指定多个输出目的地，用逗号隔开：

    例如：log4j.rootLogger＝INFO，ca，fa

+ Appenders（输出源）：日志要输出的地方，如控制台（Console）、文件（Files）等

  + Appenders 取值：
    + org.apache.log4j.ConsoleAppender（控制台）
    + org.apache.log4j.FileAppender（文件）

  + ConsoleAppender 常用参数
    + `ImmediateFlush=true`：表示所有消息都会被立即输出，设为 false 则不输出，默认值是 true
    + `Target=System.err`：默认值是 System.out
  + FileAppender常用的选项
    + `ImmediateFlush=true`：表示所有消息都会被立即输出。设为 false 则不输出，默认值是 true

    + `Append=false`：true 表示将消息添加到指定文件中，原来的消息不覆盖。默认值是 true

    + `File=E:/logs/logging.log4j`：指定消息输出到 logging.log4j 文件中

+ Layouts (布局)：日志输出的格式，常用的布局管理器：

  + org.apache.log4j.PatternLayout（可以灵活地指定布局模式）

+ org.apache.log4j.SimpleLayout（包含日志信息的级别和信息字符串）

+ org.apache.log4j.TTCCLayout（包含日志产生的时间、线程、类别等信息）

+ PatternLayout 常用的选项
  <img src="https://gitee.com/seazean/images/raw/master/Frame/日志-PatternLayout常用的选项.png" style="zoom:80%;" />



***



### 日志应用

* log4j 的配置文件,名字为 log4j.properties, 放在 src 根目录下

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
      private static final Logger LOGGER = LoggerFactory.getLogger(Log4JTest01.class);  
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

## 基本介绍

Netty 是一个异步事件驱动的网络应用程序框架，用于快速开发可维护、高性能的网络服务器和客户端

Netty 官网：https://netty.io/

Netty 的对 JDK 自带的 NIO 的 API 进行封装，解决上述问题，主要特点有：

- 设计优雅，适用于各种传输类型的统一 API， 阻塞和非阻塞 Socket 基于灵活且可扩展的事件模型
- 使用方便，详细记录的 Javadoc、用户指南和示例，没有其他依赖项
- 高性能，吞吐量更高，延迟更低，减少资源消耗，最小化不必要的内存复制
- 安全，完整的 SSL/TLS 和 StartTLS 支持

Netty 的功能特性：

* 传输服务：支持 BIO 和 NIO
* 容器集成：支持 OSGI、JBossMC、Spring、Guice 容器
* 协议支持：HTTP、Protobuf、二进制、文本、WebSocket 等一系列协议都支持，也支持通过实行编码解码逻辑来实现自定义协议
* Core 核心：可扩展事件模型、通用通信 API、支持零拷贝的 ByteBuf 缓冲对象

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-功能特性.png" style="zoom:50%;" />





***





## 线程模型

### 阻塞模型

传统阻塞型 I/O 模式，每个连接都需要独立的线程完成数据的输入，业务处理，数据返回

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-传统阻塞IO服务模型.png" style="zoom:50%;" />

模型缺点：

- 当并发数较大时，需要创建大量线程来处理连接，系统资源占用较大
- 连接建立后，如果当前线程暂时没有数据可读，则线程就阻塞在 read 操作上，造成线程资源浪费



参考文章：https://www.jianshu.com/p/2965fca6bb8f



***



### Reactor

#### 设计思想

Reactor 模式，通过一个或多个输入同时传递给服务处理器的**事件驱动处理模式**。 服务端程序处理传入的多路请求，并将它们同步分派给对应的处理线程，Reactor 模式也叫 Dispatcher 模式，即 I/O 多路复用统一监听事件，收到事件后分发（Dispatch 给某线程）

**I/O 复用结合线程池**，就是 Reactor 模式基本设计思想：

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-Reactor模型.png" style="zoom: 50%;" />

Reactor 模式关键组成：

- Reactor：在一个单独的线程中运行，负责监听和分发事件，分发给适当的处理程序来对 I/O 事件做出反应
- Handler：处理程序执行 I/O 要完成的实际事件，Reactor 通过调度适当的处理程序来响应 I/O 事件，处理程序执行**非阻塞操作**

Reactor 模式具有如下的优点：

- 响应快，不必为单个同步时间所阻塞，虽然 Reactor 本身依然是同步的
- 编程相对简单，可以最大程度的避免复杂的多线程及同步问题，并且避免了多线程/进程的切换开销
- 可扩展性，可以方便的通过增加 Reactor 实例个数来充分利用 CPU 资源
- 可复用性，Reactor 模型本身与具体事件处理逻辑无关，具有很高的复用性

根据 Reactor 的数量和处理资源池线程的数量不同，有三种典型的实现：

- 单 Reactor 单线程
- 单 Reactor 多线程
- 主从 Reactor 多线程



***



#### 单R单线程

Reactor 对象通过 select 监控客户端请求事件，收到事件后通过 dispatch 进行分发：

* 如果是建立连接请求事件，则由 Acceptor 通过 accept 处理连接请求，然后创建一个 Handler 对象处理连接完成后的后续业务处理

* 如果不是建立连接事件，则 Reactor 会分发给连接对应的 Handler 来响应，Handler 会完成 read、业务处理、send 的完整流程

  说明：Handler 和 Acceptor 属于同一个线程

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-单Reactor单线程.png" style="zoom:50%;" />

模型优点：模型简单，没有多线程、进程通信、竞争的问题，全部都在一个线程中完成

模型缺点：

* 性能问题：只有一个线程，无法发挥多核 CPU 的性能，Handler 在处理某个连接上的业务时，整个进程无法处理其他连接事件，很容易导致性能瓶颈
* 可靠性问题：线程意外跑飞，或者进入死循环，会导致整个系统通信模块不可用，不能接收和处理外部消息，造成节点故障

使用场景：客户端的数量有限，业务处理非常快速，比如 Redis，业务处理的时间复杂度 O(1)



***



#### 单R多线程

执行流程通同单 Reactor 单线程，不同的是：

* Handler 只负责响应事件，不做具体业务处理，通过 read 读取数据后，会分发给后面的 Worker 线程池进行业务处理

* Worker 线程池会分配独立的线程完成真正的业务处理，将响应结果发给 Handler 进行处理，最后由 Handler 收到响应结果后通过 send 将响应结果返回给 Client

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-单Reactor多线程.png" style="zoom:50%;" />

模型优点：可以充分利用多核 CPU 的处理能力

模型缺点：

* 多线程数据共享和访问比较复杂
* Reactor 承担所有事件的监听和响应，在单线程中运行，高并发场景下容易成为性能瓶颈



***



#### 主从模型

采用多个 Reactor ，执行流程：

* Reactor 主线程 MainReactor 通过 select 监控建立连接事件，收到事件后通过 Acceptor 接收，处理建立连接事件，处理完成后 MainReactor 会将连接分配给 Reactor 子线程的 SubReactor（有多个）处理

* SubReactor 将连接加入连接队列进行监听，并创建一个 Handler 用于处理该连接的事件，当有新的事件发生时，SubReactor 会调用连接对应的 Handler 进行响应

* Handler 通过 read 读取数据后，会分发给 Worker 线程池进行业务处理

* Worker 线程池会分配独立的线程完成真正的业务处理，将响应结果发给 Handler 进行处理，最后由 Handler 收到响应结果后通过 send 将响应结果返回给 Client

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-主从Reactor多线程.png" style="zoom: 50%;" />

模型优点

- 父线程与子线程的数据交互简单职责明确，父线程只需要接收新连接，子线程完成后续的业务处理
- 父线程与子线程的数据交互简单，Reactor 主线程只需要把新连接传给子线程，子线程无需返回数据

使用场景：Nginx 主从 Reactor 多进程模型，Memcached 主从多线程，Netty 主从多线程模型的支持



***



### Proactor

Reactor 模式中，Reactor 等待某个事件的操作状态发生变化（文件描述符可读写，socket 可读写），然后把事件传递给事先注册的 Handler 来做实际的读写操作，其中的读写操作都需要应用程序同步操作，所以 **Reactor 是非阻塞同步网络模型（NIO）**

把 I/O 操作改为异步，交给操作系统来完成就能进一步提升性能，这就是异步网络模型 Proactor（AIO）：

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-Proactor模型.png" style="zoom:50%;" />

工作流程：

* ProactorInitiator 创建 Proactor 和 Handler 对象，并将 Proactor 和 Handler 通过 Asynchronous Operation Processor（AsyOptProcessor）注册到内核
* AsyOptProcessor 处理注册请求，并处理 I/O 操作，完成I/O后通知 Proactor
* Proactor 根据不同的事件类型回调不同的 Handler 进行业务处理，最后由 Handler 完成业务处理

对比：Reactor 在事件发生时就通知事先注册的处理器（读写在应用程序线程中处理完成）；Proactor 是在事件发生时基于异步 I/O 完成读写操作（内核完成），I/O 完成后才回调应用程序的处理器进行业务处理

模式优点：异步 I/O 更加充分发挥 DMA（Direct Memory Access 直接内存存取）的优势

模式缺点：

* 编程复杂性，由于异步操作流程的事件的初始化和事件完成在时间和空间上都是相互分离的，因此开发异步应用程序更加复杂，应用程序还可能因为反向的流控而变得更加难以调试
* 内存使用，缓冲区在读或写操作的时间段内必须保持住，可能造成持续的不确定性，并且每个并发操作都要求有独立的缓存，Reactor 模式在 socket 准备好读或写之前是不要求开辟缓存的
* 操作系统支持，Windows 下通过 IOCP 实现了真正的异步 I/O，而在 Linux 系统下，Linux2.6 才引入异步 I/O，目前还不完善，所以在 Linux 下实现高并发网络编程都是以 Reactor 模型为主



****



### Netty

Netty 主要基于主从 Reactors 多线程模型做了一定的改进，Netty 的工作架构图：

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-工作模型.png" style="zoom:50%;" />

工作流程：

1. Netty 抽象出两组线程池 BossGroup 专门负责接收客户端的连接，WorkerGroup 专门负责网络的读写

2. BossGroup 和 WorkerGroup 类型都是 NioEventLoopGroup，该 Group 相当于一个事件循环组，含有多个事件循环，每一个事件循环是 NioEventLoop，所以可以有多个线程

3. NioEventLoop 表示一个循环处理任务的线程，每个 NioEventLoop 都有一个 Selector，用于监听绑定在其上的 socket 的网络通讯

4. 每个 Boss NioEventLoop 循环执行的步骤：

   - 轮询 accept 事件
   - 处理 accept 事件，与 client 建立连接，生成 NioScocketChannel，并将其**注册到某个 Worker 中**的某个 NioEventLoop 上的 Selector，连接就与 NioEventLoop 绑定
   - 处理任务队列的任务，即 runAllTasks

5. 每个 Worker NioEventLoop 循环执行的步骤：

   - 轮询 read、write 事件
   - 处理 I/O 事件，即 read，write 事件，在对应 NioSocketChannel 处理
   - 处理任务队列的任务，即 runAllTasks

6. 每个 Worker NioEventLoop 处理业务时，会使用 pipeline（管道），pipeline 中包含了 channel，即通过 pipeline 可以获取到对应通道，管道中维护了很多的处理器 Handler

   <img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-Channel与Pipeline.png" style="zoom: 50%;" />





***





## 基本实现

开发简单的服务器端和客户端，基本介绍：

* channel 理解为数据的通道，把 msg 理解为流动的数据，最开始输入是 ByteBuf，但经过 pipeline 的加工，会变成其它类型对象，最后输出又变成 ByteBuf
* handler 理解为数据的处理工序，pipeline 负责发布事件传播给每个 handler，handler 对自己感兴趣的事件进行处理（重写了相应事件处理方法），分 Inbound 和 Outbound 两类
* eventLoop 理解为处理数据的执行者，既可以执行 IO 操作，也可以进行任务处理。每个执行者有任务队列，队列里可以堆放多个 channel 的待处理任务，任务分为普通任务、定时任务。按照 pipeline 顺序，依次按照 handler 的规划（代码）处理数据

代码实现：

* pom.xml

  ```xml
  <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-all</artifactId>
      <version>4.1.20.Final</version>
  </dependency>
  ```


* Server.java

  ```java
  public class HelloServer {
      public static void main(String[] args) {
          EventLoopGroup boss = new NioEventLoopGroup();
          EventLoopGroup worker = new NioEventLoopGroup(2);
          // 1. 启动器，负责组装 netty 组件，启动服务器
          new ServerBootstrap()
                  // 2. 线程组，boss 只负责【处理 accept 事件】， worker 只【负责 channel 上的读写】
                  .group(boss, worker)
             	 	//.option() 		// 给 ServerSocketChannel 配置参数
              	//.childOption()   	// 给 SocketChannel 配置参数
                  // 3. 选择服务器的 ServerSocketChannel 实现
                  .channel(NioServerSocketChannel.class)
                  // 4. boss 负责处理连接，worker(child) 负责处理读写，决定了能执行哪些操作(handler)
                  .childHandler(new ChannelInitializer<NioSocketChannel>() {
                      // 5. channel 代表和客户端进行数据读写的通道 Initializer 初始化，负责添加别的 handler
                      // 7. 连接建立后，执行初始化方法
                      @Override
                      protected void initChannel(NioSocketChannel ch) throws Exception {
                          // 添加具体的 handler
                          ch.pipeline().addLast(new StringDecoder());// 将 ByteBuf 转成字符串
                          ch.pipeline().addLast(new ChannelInboundHandlerAdapter() { // 自定义 handler
                              // 读事件
                              @Override
                              public void channelRead(ChannelHandlerContext ctx, Object msg) {
                                  // 打印转换好的字符串
                                  System.out.println(msg);
                              }
                          });
                      }
                  })
                  // 6. 绑定监听端口
                  .bind(8080);
      }
  }
  ```

* Client.java

  ```java
  public class HelloClient {
      public static void main(String[] args) throws InterruptedException {
          // 1. 创建启动器类
          new Bootstrap()
                  // 2. 添加 EventLoop
                  .group(new NioEventLoopGroup())
              	//.option() 	//给 SocketChannel 配置参数
                  // 3. 选择客户端 channel 实现
                  .channel(NioSocketChannel.class)
                  // 4. 添加处理器
                  .handler(new ChannelInitializer<NioSocketChannel>() {
                      // 4.1 连接建立后被调用
                      @Override
                      protected void initChannel(NioSocketChannel ch) throws Exception {
                          // 将 Hello World 转为 ByteBuf
                          ch.pipeline().addLast(new StringEncoder());
                      }
                  })
                  // 5. 连接到服务器，然后调用 4.1
                  .connect(new InetSocketAddress("127.0.0.1",8080))
                  // 6. 阻塞方法，直到连接建立
                  .sync()
                  // 7. 代表连接对象
                  .channel()
                  // 8. 向服务器发送数据
                  .writeAndFlush("Hello World");
      }
  }
  ```



参考视频：https://www.bilibili.com/video/BV1py4y1E7oA




****





## 组件介绍

### EventLoop

#### 基本介绍

事件循环对象 EventLoop，本质是一个单线程执行器（同时维护了一个 selector），里面有 run 方法处理 Channel 上源源不断的 IO 事件

事件循环组 EventLoopGroup 是一组 EventLoop，Channel 会调用 Boss EventLoopGroup 的 register 方法来绑定其中一个 Worker 的 EventLoop，后续这个 Channel 上的 IO 事件都由此 EventLoop 来处理，保证了事件处理时的线程安全

EventLoopGroup 类 API：

* `EventLoop next()`：获取集合中下一个 EventLoop，EventLoopGroup 实现了 Iterable 接口提供遍历 EventLoop 的能力
* `Future<?> shutdownGracefully()`：优雅关闭的方法，会首先切换 `EventLoopGroup` 到关闭状态从而拒绝新的任务的加入，然后在任务队列的任务都处理完成后，停止线程的运行。从而确保整体应用是在正常有序的状态下退出的

* `<T> Future<T> submit(Callable<T> task)`：提交任务
* `ScheduledFuture<?> scheduleWithFixedDelay`：提交定时任务



***



#### 任务传递

把要调用的代码封装为一个任务对象，由下一个 handler 的线程来调用

```java
public class EventLoopServer {
    public static void main(String[] args) {
        EventLoopGroup group = new DefaultEventLoopGroup();
        new ServerBootstrap()
                .group(new NioEventLoopGroup(), new NioEventLoopGroup(2))
                .channel(NioServerSocketChannel.class)
                .childHandler(new ChannelInitializer<NioSocketChannel>() {
                    @Override
                    protected void initChannel(NioSocketChannel ch) {
                        ch.pipeline().addLast("handler1", new ChannelInboundHandlerAdapter() {
                            @Override
                            public void channelRead(ChannelHandlerContext ctx, Object msg) {
                                ByteBuf buf = (ByteBuf) msg;
                                log.debug(buf.toString(Charset.defaultCharset()));
                                ctx.fireChannelRead(msg);   // 让消息【传递】给下一个 handler
                            }
                        }).addLast(group, "handler2", new ChannelInboundHandlerAdapter() {
                            @Override
                            public void channelRead(ChannelHandlerContext ctx, Object msg) {
                                ByteBuf buf = (ByteBuf) msg;
                                log.debug(buf.toString(Charset.defaultCharset()));

                            }
                        });
                    }
                })
                .bind(8080);
    }
}
```

源码分析：

```java
public ChannelHandlerContext fireChannelRead(final Object msg) {
    invokeChannelRead(findContextInbound(MASK_CHANNEL_READ), msg);
    return this;
}
static void invokeChannelRead(final AbstractChannelHandlerContext next, Object msg) {
    final Object m = next.pipeline.touch(ObjectUtil.checkNotNull(msg, "msg"), next);
    EventExecutor executor = next.executor();
    // 下一个 handler 的事件循环是否与当前的事件循环是同一个线程
    if (executor.inEventLoop()) {
        // 是，直接调用
        next.invokeChannelRead(m);
    } else {
        // 不是，将要执行的代码作为任务提交给下一个 handler 处理
        executor.execute(new Runnable() {
            @Override
            public void run() {
                next.invokeChannelRead(m);
            }
        });
    }
}
```





****



### Channel

#### 基本介绍

Channel 类 API：

* `ChannelFuture close()`：关闭通道
* `ChannelPipeline pipeline()`：添加处理器
* `ChannelFuture write(Object msg)`：数据写入缓冲区
* `ChannelFuture writeAndFlush(Object msg)`：数据写入缓冲区并且刷出

ChannelFuture 类 API：

* `ChannelFuture sync()`：同步阻塞等待连接成功
* `ChannelFuture addListener(GenericFutureListener listener)`：异步等待

代码实现：

* connect 方法是异步的，不等连接建立完成就返回，因此 channelFuture 对象中不能立刻获得到正确的 Channel 对象，需要等待
* 连接未建立 channel 打印为 `[id: 0x2e1884dd]`；建立成功打印为 `[id: 0x2e1884dd, L:/127.0.0.1:57191 - R:/127.0.0.1:8080]`

```java
public class ChannelClient {
    public static void main(String[] args) throws InterruptedException {
        ChannelFuture channelFuture = new Bootstrap()
                .group(new NioEventLoopGroup())
                .channel(NioSocketChannel.class)
                .handler(new ChannelInitializer<NioSocketChannel>() {
                    @Override
                    protected void initChannel(NioSocketChannel ch) throws Exception {
                        ch.pipeline().addLast(new StringEncoder());
                    }
                })
                // 1. 连接服务器，【异步非阻塞】，main 调用 connect 方法，真正执行连接的是 nio 线程
                .connect(new InetSocketAddress("127.0.0.1", 8080));
        // 2.1 使用 sync 方法【同步】处理结果，阻塞当前线程，直到 nio 线程连接建立完毕
        channelFuture.sync();
        Channel channel = channelFuture.channel();
        System.out.println(channel); // 【打印】
        // 向服务器发送数据
        channel.writeAndFlush("hello world");
        
**************************************************************************************二选一
        // 2.2 使用 addListener 方法【异步】处理结果
        channelFuture.addListener(new ChannelFutureListener() {
            @Override
            // nio 线程连接建立好以后，回调该方法
            public void operationComplete(ChannelFuture future) throws Exception {
                Channel channel = future.channel();
                channel.writeAndFlush("hello, world");
            }
        });
    }
}
```



***



#### 关闭操作

关闭 EventLoopGroup 的运行，分为同步关闭和异步关闭

```java
public class CloseFutureClient {
    public static void main(String[] args) throws InterruptedException {
        NioEventLoopGroup group = new NioEventLoopGroup();
        ChannelFuture channelFuture = new Bootstrap()
                .group(group)
                .channel(NioSocketChannel.class)
                .handler(new ChannelInitializer<NioSocketChannel>() {
                    @Override
                    protected void initChannel(NioSocketChannel ch) throws Exception {
                        ch.pipeline().addLast(new LoggingHandler(LogLevel.DEBUG));
                        ch.pipeline().addLast(new StringEncoder());
                    }
                })
                .connect(new InetSocketAddress("127.0.0.1", 8080));
        Channel channel = channelFuture.sync().channel();
        // 发送数据
        new Thread(() -> {
            Scanner sc = new Scanner(System.in);
            while (true) {
                String line = sc.nextLine();
                if (line.equals("q")) {
                    channel.close();
                    break;
                }
                channel.writeAndFlush(line);
            }
        }, "input").start();
        // 获取 CloseFuture 对象
        ChannelFuture closeFuture = channel.closeFuture();
        
        // 1. 同步处理关闭
        System.out.println("waiting close...");
        closeFuture.sync();
        System.out.println("处理关闭后的操作");
****************************************************
        // 2. 异步处理关闭
        closeFuture.addListener(new ChannelFutureListener() {
            @Override
            public void operationComplete(ChannelFuture future) throws Exception {
                System.out.println("处理关闭后的操作");
                group.shutdownGracefully();
            }
        });
    }
}
```



****



### Future

#### 基本介绍

Netty 中的 Future 与 JDK 中的 Future 同名，但是功能的实现不同

```java
package io.netty.util.concurrent;
public interface Future<V> extends java.util.concurrent.Future<V>
```

Future 类 API：

* `V get()`：阻塞等待获取任务执行结果
* `V getNow()`：非阻塞获取任务结果，还未产生结果时返回 null
* `Throwable cause()`：非阻塞获取失败信息，如果没有失败，返回 null
* `Future<V> sync()`：等待任务结束，如果任务失败，抛出异常
* `boolean cancel(boolean mayInterruptIfRunning)`：取消任务
* `Future<V> addListener(GenericFutureListener listener)`：添加回调，异步接收结果
* `boolean isSuccess()`：判断任务是否成功
* `boolean isCancellable()`：判断任务是否取消

```java
public class NettyFutureDemo {
    public static void main(String[] args) throws Exception {
        NioEventLoopGroup group = new NioEventLoopGroup();
        EventLoop eventLoop = group.next();
        Future<Integer> future = eventLoop.submit(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                System.out.println("执行计算");
                Thread.sleep(1000);
                return 70;
            }
        });
        future.getNow();
        System.out.println(new Date() + "等待结果");
        System.out.println(new Date() + "" + future.get());
    }
}
```



****



#### 扩展子类

Promise 类是 Future 的子类，可以脱离任务独立存在，作为两个线程间传递结果的容器

```java
public interface Promise<V> extends Future<V>
```

Promise 类 API：

* `Promise<V> setSuccess(V result)`：设置成功结果
* `Promise<V> setFailure(Throwable cause)`：设置失败结果

```java
public class NettyPromiseDemo {
    public static void main(String[] args) throws Exception {
        // 1. 准备 EventLoop 对象
        EventLoop eventLoop = new NioEventLoopGroup().next();
        // 2. 主动创建 promise
        DefaultPromise<Integer> promise = new DefaultPromise<>(eventLoop);
        // 3. 任意一个线程执行计算，计算完毕后向 promise 填充结果
        new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            promise.setSuccess(200);
        }).start();

        // 4. 接受结果的线程
        System.out.println(new Date() + "等待结果");
        System.out.println(new Date() + "" + promise.get());
    }
}
```



****



### Pipeline

ChannelHandler 用来处理 Channel 上的各种事件，分为入站出站两种，所有 ChannelHandler 连接成双向链表就是 Pipeline

* 入站处理器通常是 ChannelInboundHandlerAdapter 的子类，主要用来读取客户端数据，写回结果
* 出站处理器通常是 ChannelOutboundHandlerAdapter 的子类，主要对写回结果进行加工（入站和出站是对于服务端来说的）

```java
public static void main(String[] args) {
    new ServerBootstrap()
        .group(new NioEventLoopGroup())
        .channel(NioServerSocketChannel.class)
        .childHandler(new ChannelInitializer<NioSocketChannel>() {
            @Override
            protected void initChannel(NioSocketChannel ch) throws Exception {
                // 1. 通过 channel 拿到 pipeline
                ChannelPipeline pipeline = ch.pipeline();
                // 2. 添加处理器 head -> h1 -> h2 -> h3 -> h4 -> h5 -> h6 -> tail
                pipeline.addLast("h1", new ChannelInboundHandlerAdapter() {
                    @Override
                    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                        log.debug("1");
                        ByteBuf buf = (ByteBuf) msg;
                        String s = buf.toString(Charset.defaultCharset());
                        // 将数据传递给下一个【入站】handler，如果不调用该方法则链会断开
                        super.channelRead(ctx, s);
                    }
                });
                pipeline.addLast("h2", new ChannelInboundHandlerAdapter() {
                    @Override
                    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
                        log.debug("2");
                        // 从【尾部开始向前触发】出站处理器
                        ch.writeAndFlush(ctx.alloc().buffer().writeBytes("server".getBytes()));
                        // 该方法会让管道从【当前 handler 向前】寻找出站处理器
                        // ctx.writeAndFlush();
                    }
                });
                pipeline.addLast("h3", new ChannelOutboundHandlerAdapter() {
                    @Override
                    public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) throws Exception {
                        log.debug("3");
                        super.write(ctx, msg, promise);
                    }
                });
                pipeline.addLast("h4", new ChannelOutboundHandlerAdapter() {
                    @Override
                    public void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) throws Exception {
                        log.debug("4");
                        super.write(ctx, msg, promise);
                    }
                });
            }
        })
        .bind(8080);
}

```

服务器端依次打印：1 2 4 3 ，所以**入站是按照 addLast 的顺序执行的，出站是按照 addLast 的逆序执行**

一个 Channel 包含了一个 ChannelPipeline，而 ChannelPipeline 中又维护了一个由 ChannelHandlerContext 组成的双向链表，并且每个 ChannelHandlerContext 中关联着一个 ChannelHandler

入站事件和出站事件在一个双向链表中，两种类型的 handler 互不干扰：

* 入站事件会从链表 head 往后传递到最后一个入站的 handler
* 出站事件会从链表 tail 往前传递到最前一个出站的 handler

![](https://gitee.com/seazean/images/raw/master/Frame/Netty-ChannelPipeline.png)



****



### ByteBuf

#### 基本介绍

ByteBuf 是对字节数据的封装，优点：

* 池化，可以重用池中 ByteBuf 实例，更节约内存，减少内存溢出的可能
* 读写指针分离，不需要像 ByteBuffer 一样切换读写模式
* 可以自动扩容
* 支持链式调用，使用更流畅
* 零拷贝思想，例如 slice、duplicate、CompositeByteBuf



****



#### 创建方法

创建方式

*  `ByteBuf buffer = ByteBufAllocator.DEFAULT.buffer(10)`：创建了一个默认的 ByteBuf，初始容量是 10

  ```java
  public ByteBuf buffer() {
      if (directByDefault) {
          return directBuffer();
      }
      return heapBuffer();
  }
  ```

* `ByteBuf buffer = ByteBufAllocator.DEFAULT.heapBuffer(10)`：创建池化基于堆的 ByteBuf

* `ByteBuf buffer = ByteBufAllocator.DEFAULT.directBuffer(10)`：创建池化基于直接内存的 ByteBuf

* **推荐**的创建方式：在添加处理器的方法中

  ```java
  pipeline.addLast(new ChannelInboundHandlerAdapter() {
      @Override
      public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
          ByteBuf buffer = ctx.alloc().buffer();
      }
  });
  ```

直接内存对比堆内存：

* 直接内存创建和销毁的代价昂贵，但读写性能高（少一次内存复制），适合配合池化功能一起用
* 直接内存对 GC 压力小，因为这部分内存不受 JVM 垃圾回收的管理，但也要注意及时主动释放

池化的意义在于可以**重用 ByteBuf**，高并发时池化功能更节约内存，减少内存溢出的可能，与非池化对比：

* 非池化，每次都要创建新的 ByteBuf 实例，这个操作对直接内存代价昂贵，堆内存会增加 GC 压力
* 池化，可以重用池中 ByteBuf 实例，并且采用了与 jemalloc 类似的内存分配算法提升分配效率

池化功能的开启，可以通过下面的系统环境变量来设置：

```sh
-Dio.netty.allocator.type={unpooled|pooled}	 # VM 参数
```

* 4.1 以后，非 Android 平台默认启用池化实现，Android 平台启用非池化实现
* 4.1 之前，池化功能还不成熟，默认是非池化实现



****



#### 读写操作

ByteBuf 由四部分组成，最开始读写指针（**双指针**）都在 0 位置

![](https://gitee.com/seazean/images/raw/master/Frame/Netty-ByteBuf组成.png)

写入方法：

| 方法名                                           | 说明                   | 备注                                        |
| ------------------------------------------------ | ---------------------- | ------------------------------------------- |
| writeBoolean(boolean value)                      | 写入 boolean 值        | 用一字节 01\|00 代表 true\|false            |
| writeByte(int value)                             | 写入 byte 值           |                                             |
| writeInt(int value)                              | 写入 int 值            | Big Endian，即 0x250，写入后 00 00 02 50    |
| writeIntLE(int value)                            | 写入 int 值            | Little Endian，即 0x250，写入后 50 02 00 00 |
| writeBytes(ByteBuf src)                          | 写入 ByteBuf           |                                             |
| writeBytes(byte[] src)                           | 写入 byte[]            |                                             |
| writeBytes(ByteBuffer src)                       | 写入 NIO 的 ByteBuffer |                                             |
| int writeCharSequence(CharSequence s, Charset c) | 写入字符串             |                                             |

* 这些方法的未指明返回值的，其返回值都是 ByteBuf，意味着可以链式调用
* 写入几位写指针后移几位，指向可以写入的位置
* 网络传输，默认习惯是 Big Endian

扩容：写入数据时，容量不够了（初始容量是 10），这时会引发扩容

* 如果写入后数据大小未超过 512，则选择下一个 16 的整数倍，例如写入后大小为 12 ，则扩容后 capacity 是 16
* 如果写入后数据大小超过 512，则选择下一个 2^n，例如写入后大小为 513，则扩容后 capacity 是 2^10 = 1024（2^9=512 不够）
* 扩容不能超过 max capacity 会报错

读取方法：

* `byte readByte()`：读取一个字节，读指针后移
* `byte getByte(int index)`：读取指定索引位置的字节，读指针不动
* `ByteBuf markReaderIndex()`：标记读数据的位置
* `ByteBuf resetReaderIndex()`：重置到标记位置，可以重复读取标记位置向后的数据



****



#### 内存释放

Netty 中三种内存的回收：

* UnpooledHeapByteBuf 使用的是 JVM 内存，只需等 GC 回收内存
* UnpooledDirectByteBuf 使用的就是直接内存了，需要特殊的方法来回收内存
* PooledByteBuf 和子类使用了池化机制，需要更复杂的规则来回收内存

Netty 采用了引用计数法来控制回收内存，每个 ByteBuf 都实现了 ReferenceCounted 接口，回收的规则：

* 每个 ByteBuf 对象的初始计数为 1
* 调用 release 方法计数减 1，如果计数为 0，ByteBuf 内存被回收
* 调用 retain 方法计数加 1，表示调用者没用完之前，其它 handler 即使调用了 release 也不会造成回收
* 当计数为 0 时，底层内存会被回收，这时即使 ByteBuf 对象还在，其各个方法均无法正常使用

```java
ByteBuf buf = .ByteBufAllocator.DEFAULT.buffer(10)
try {
    // 逻辑处理
} finally {
    buf.release();
}
```

Pipeline 的存在，需要将 ByteBuf 传递给下一个 ChannelHandler，如果在 finally 中 release 了，就失去了传递性，处理规则：

* 创建 ByteBuf 放入 pipeline

* 入站 ByteBuf 处理原则

  * 对原始 ByteBuf 不做处理，调用 ctx.fireChannelRead(msg) 向后传递，这时无须 release

  * 将原始 ByteBuf 转换为其它类型的 Java 对象，这时 ByteBuf 就没用了，此时必须 release

  * 如果不调用 ctx.fireChannelRead(msg) 向后传递，那么也必须 release

  * 如果出现异常，ByteBuf 没有成功传递到下一个 ChannelHandler，必须 release

  * 假设消息一直向后传，那么 TailContext 会负责释放未处理消息（原始的 ByteBuf）

    ```java
    // io.netty.channel.DefaultChannelPipeline#onUnhandledInboundMessage(java.lang.Object)
    protected void onUnhandledInboundMessage(Object msg) {
        try {
            logger.debug();
        } finally {
            ReferenceCountUtil.release(msg);
        }
    }
    // io.netty.util.ReferenceCountUtil#release(java.lang.Object)
    public static boolean release(Object msg) {
        if (msg instanceof ReferenceCounted) {
            return ((ReferenceCounted) msg).release();
        }
        return false;
    }
    ```

* 出站 ByteBuf 处理原则

  * 出站消息最终都会转为 ByteBuf 输出，一直向前传，由 HeadContext flush 后 release

* 不确定 ByteBuf 被引用了多少次，但又必须彻底释放，可以循环调用 release 直到返回 true



****



#### 拷贝操作

零拷贝方法：

* `ByteBuf slice(int index, int length)`：对原始 ByteBuf 进行切片成多个 ByteBuf，切片后的 ByteBuf 并没有发生内存复制，**共用原始 ByteBuf 的内存**，切片后的 ByteBuf 维护独立的 read，write 指针

  ```java
  public static void main(String[] args) {
      ByteBuf buf = ByteBufAllocator.DEFAULT.buffer(10);
      buf.writeBytes(new byte[]{'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j'});
      // 在切片过程中并没有发生数据复制
      ByteBuf f1 = buf.slice(0, 5);
      f1.retain();
      ByteBuf f2 = buf.slice(5, 5);
      f2.retain();
      // 对 f1 进行相关的操作也会体现在 buf 上
  }
  ```

* `ByteBuf duplicate()`：截取原始 ByteBuf 所有内容，并且没有 max capacity 的限制，也是与原始 ByteBuf 使用同一块底层内存，只是读写指针是独立的

* `CompositeByteBuf addComponents(boolean increaseWriterIndex, ByteBuf... buffers)`：合并多个 ByteBuf

  ```java
  public static void main(String[] args) {
      ByteBuf buf1 = ByteBufAllocator.DEFAULT.buffer();
      buf1.writeBytes(new byte[]{1, 2, 3, 4, 5});
  
      ByteBuf buf2 = ByteBufAllocator.DEFAULT.buffer();
      buf1.writeBytes(new byte[]{6, 7, 8, 9, 10});
  
      CompositeByteBuf buf = ByteBufAllocator.DEFAULT.compositeBuffer();
      // true 表示增加新的 ByteBuf 自动递增 write index, 否则 write index 会始终为 0
      buf.addComponents(true, buf1, buf2);
  }
  ```
  
  CompositeByteBuf 是一个组合的 ByteBuf，内部维护了一个 Component 数组，每个 Component 管理一个 ByteBuf，记录了这个 ByteBuf 相对于整体偏移量等信息，代表着整体中某一段的数据
  
  * 优点：对外是一个虚拟视图，组合这些 ByteBuf 不会产生内存复制
  * 缺点：复杂了很多，多次操作会带来性能的损耗

深拷贝：

* `ByteBuf copy()`：将底层内存数据进行深拷贝，因此无论读写，都与原始 ByteBuf 无关

池化相关：

* Unpooled 是一个工具类，提供了非池化的 ByteBuf 创建、组合、复制等操作

  ```java
  ByteBuf buf1 = ByteBufAllocator.DEFAULT.buffer(5);
  buf1.writeBytes(new byte[]{1, 2, 3, 4, 5});
  ByteBuf buf2 = ByteBufAllocator.DEFAULT.buffer(5);
  buf2.writeBytes(new byte[]{6, 7, 8, 9, 10});
  
  // 当包装 ByteBuf 个数超过一个时, 底层使用了 CompositeByteBuf，零拷贝思想
  ByteBuf buf = Unpooled.wrappedBuffer(buf1, buf2);
  ```






****





## 粘包半包

### 现象演示

在 TCP 传输中，客户端发送消息时，实际上是将数据写入TCP的缓存，此时数据的大小和缓存的大小就会造成粘包和半包

* 当数据超过 TCP 缓存容量时，就会被拆分成多个包，通过 Socket 多次发送到服务端，服务端每次从缓存中取数据，产生半包问题

* 当数据小于 TCP 缓存容量时，缓存中可以存放多个包，客户端和服务端一次通信就可能传递多个包，这时候服务端就可能一次读取多个包，产生粘包的问题

代码演示：

* 客户端代码：

  ```java
  public class HelloWorldClient {
      public static void main(String[] args) {
          send();
      }
  
      private static void send() {
          NioEventLoopGroup worker = new NioEventLoopGroup();
          try {
              Bootstrap bootstrap = new Bootstrap();
              bootstrap.channel(NioSocketChannel.class);
              bootstrap.group(worker);
              bootstrap.handler(new ChannelInitializer<SocketChannel>() {
                  @Override
                  protected void initChannel(SocketChannel ch) throws Exception {
                      ch.pipeline().addLast(new ChannelInboundHandlerAdapter() {
                          // 【在连接 channel 建立成功后，会触发 active 方法】
                          @Override
                          public void channelActive(ChannelHandlerContext ctx) throws Exception {
                              // 发送内容随机的数据包
                              Random r = new Random();
                              char c = '0';
                              ByteBuf buf = ctx.alloc().buffer();
                              for (int i = 0; i < 10; i++) {
                                  byte[] bytes = new byte[10];
                                  for (int j = 0; j < r.nextInt(9) + 1; j++) {
                                      bytes[j] = (byte) c;
                                  }
                                  c++;
                                  buf.writeBytes(bytes);
                              }
                              ctx.writeAndFlush(buf);
                          }
                      });
                  }
              });
              ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 8080).sync();
              channelFuture.channel().closeFuture().sync();
  
          } catch (InterruptedException e) {
              log.error("client error", e);
          } finally {
              worker.shutdownGracefully();
          }
      }
  }
  ```

* 服务器代码：

  ```java
  public class HelloWorldServer {
      public static void main(String[] args) {
          NioEventLoopGroup boss = new NioEventLoopGroup(1);
          NioEventLoopGroup worker = new NioEventLoopGroup();
          try {
              ServerBootstrap serverBootstrap = new ServerBootstrap();
              serverBootstrap.channel(NioServerSocketChannel.class);
              // 调整系统的接受缓冲区【滑动窗口】
              //serverBootstrap.option(ChannelOption.SO_RCVBUF, 10);
              // 调整 netty 的接受缓冲区（ByteBuf）
              //serverBootstrap.childOption(ChannelOption.RCVBUF_ALLOCATOR, 
              //                            new AdaptiveRecvByteBufAllocator(16, 16, 16));
              serverBootstrap.group(boss, worker);
              serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
                  @Override
                  protected void initChannel(SocketChannel ch) throws Exception {
                      // 【这里可以添加解码器】
                      // LoggingHandler 用来打印消息
                      ch.pipeline().addLast(new LoggingHandler(LogLevel.DEBUG));
                  }
              });
              ChannelFuture channelFuture = serverBootstrap.bind(8080);
              channelFuture.sync();
              channelFuture.channel().closeFuture().sync();
          } catch (InterruptedException e) {
              log.error("server error", e);
          } finally {
              boss.shutdownGracefully();
              worker.shutdownGracefully();
              log.debug("stop");
          }
      }
  }
  ```

* 粘包效果展示：

  ```java
  09:57:27.140 [nioEventLoopGroup-3-1] DEBUG io.netty.handler.logging.LoggingHandler - [id: 0xddbaaef6, L:/127.0.0.1:8080 - R:/127.0.0.1:8701] READ: 100B	// 读了 100 字节，发生粘包
           +-------------------------------------------------+
           |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
  +--------+-------------------------------------------------+----------------+
  |00000000| 30 30 30 30 30 00 00 00 00 00 31 00 00 00 00 00 |00000.....1.....|
  |00000010| 00 00 00 00 32 32 32 32 00 00 00 00 00 00 33 00 |....2222......3.|
  |00000020| 00 00 00 00 00 00 00 00 34 34 00 00 00 00 00 00 |........44......|
  |00000030| 00 00 35 35 35 35 00 00 00 00 00 00 36 36 36 00 |..5555......666.|
  |00000040| 00 00 00 00 00 00 37 37 37 37 00 00 00 00 00 00 |......7777......|
  |00000050| 38 38 38 38 38 00 00 00 00 00 39 39 00 00 00 00 |88888.....99....|
  |00000060| 00 00 00 00                                     |....            |
  +--------+-------------------------------------------------+----------------+
  ```

解决方法：通过调整系统的接受缓冲区的滑动窗口和 Netty 的接受缓冲区保证每条包只含有一条数据，滑动窗口大小，仅决定了 Netty 读取的**最小单位**，实际每次读取的一般是它的整数倍



***



### 解决方案

#### 短连接

发一个包建立一次连接，这样连接建立到连接断开之间就是消息的边界，缺点就是效率很低

客户端代码改造：

```java
public class HelloWorldClient {
    public static void main(String[] args) {
        // 分 10 次发送
        for (int i = 0; i < 10; i++) {
            send();
        }
    }
}
```



****



#### 固定长度

服务器端加入定长解码器，每一条消息采用固定长度，缺点浪费空间

```java
serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ch.pipeline().addLast(new FixedLengthFrameDecoder(8));
        // LoggingHandler 用来打印消息
        ch.pipeline().addLast(new LoggingHandler(LogLevel.DEBUG));
    }
});
```

```java
10:29:06.522 [nioEventLoopGroup-3-1] DEBUG io.netty.handler.logging.LoggingHandler - [id: 0x38a70fbf, L:/127.0.0.1:8080 - R:/127.0.0.1:10144] READ: 10B
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 31 31 00 00 00 00 00 00 00 00                   |11........      |
+--------+-------------------------------------------------+----------------+
10:29:06.522 [nioEventLoopGroup-3-1] DEBUG io.netty.handler.logging.LoggingHandler - [id: 0x38a70fbf, L:/127.0.0.1:8080 - R:/127.0.0.1:10144] READ: 10B
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 32 32 32 32 32 32 00 00 00 00                   |222222....      |
+--------+-------------------------------------------------+----------------+
```



****



#### 分隔符

服务端加入行解码器，默认以 `\n` 或 `\r\n` 作为分隔符，如果超出指定长度仍未出现分隔符，则抛出异常：

```java
serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ch.pipeline().addLast(new FixedLengthFrameDecoder(8));
        ch.pipeline().addLast(new LoggingHandler(LogLevel.DEBUG));
    }
});
```

客户端在每条消息之后，加入 `\n` 分隔符：

```java
public void channelActive(ChannelHandlerContext ctx) throws Exception {
    Random r = new Random();
    char c = 'a';
    ByteBuf buffer = ctx.alloc().buffer();
    for (int i = 0; i < 10; i++) {
        for (int j = 1; j <= r.nextInt(16)+1; j++) {
            buffer.writeByte((byte) c);
        }
        // 10 代表 '\n'
        buffer.writeByte(10);
        c++;
    }
    ctx.writeAndFlush(buffer);
}
```



****



#### 预设长度

LengthFieldBasedFrameDecoder 解码器自定义长度解决 TCP 粘包黏包问题

```java
int maxFrameLength		// 数据最大长度
int lengthFieldOffset 	// 长度字段偏移量，从第几个字节开始是内容的长度字段
int lengthFieldLength	// 长度字段本身的长度
int lengthAdjustment 	// 长度字段为基准，几个字节后才是内容
int initialBytesToStrip	// 从头开始剥离几个字节解码后显示
```

```java
lengthFieldOffset   = 1 (= the length of HDR1)
lengthFieldLength   = 2
lengthAdjustment    = 1 (= the length of HDR2)
initialBytesToStrip = 3 (= the length of HDR1 + LEN)

BEFORE DECODE (16 bytes)                       AFTER DECODE (13 bytes)//解码
+------+--------+------+----------------+      +------+----------------+
| HDR1 | Length | HDR2 | Actual Content |----->| HDR2 | Actual Content |
| 0xCA | 0x000C | 0xFE | "HELLO, WORLD" |      | 0xFE | "HELLO, WORLD" |
+------+--------+------+----------------+      +------+----------------+
```

代码实现：

```java
public class LengthFieldDecoderDemo {
    public static void main(String[] args) {
        EmbeddedChannel channel = new EmbeddedChannel(
                // int 占 4 字节，版本号一个字节
                new LengthFieldBasedFrameDecoder(1024, 0, 4, 1,5),
                new LoggingHandler(LogLevel.DEBUG)
        );

        // 4 个字节的内容长度， 实际内容
        ByteBuf buffer = ByteBufAllocator.DEFAULT.buffer();
        send(buffer, "Hello, world");
        send(buffer, "Hi!");
        // 写出缓存
        channel.writeInbound(buffer);
    }
    // 写入缓存
    private static void send(ByteBuf buffer, String content) {
        byte[] bytes = content.getBytes();  // 实际内容
        int length = bytes.length;          // 实际内容长度
        buffer.writeInt(length);
        buffer.writeByte(1);                // 表示版本号
        buffer.writeBytes(bytes);
    }
}
```

```java
10:49:59.344 [main] DEBUG io.netty.handler.logging.LoggingHandler - [id: 0xembedded, L:embedded - R:embedded] READ: 12B
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 48 65 6c 6c 6f 2c 20 77 6f 72 6c 64             |Hello, world    |
+--------+-------------------------------------------------+----------------+
10:49:59.344 [main] DEBUG io.netty.handler.logging.LoggingHandler - [id: 0xembedded, L:embedded - R:embedded] READ: 3B
         +-------------------------------------------------+
         |  0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f |
+--------+-------------------------------------------------+----------------+
|00000000| 48 69 21                                        |Hi!             |
+--------+-------------------------------------------------+----------------+
```



****



### 协议设计

#### HTTP协议

访问URL：http://localhost:8080/

```java
public class HttpDemo {
    public static void main(String[] args) {
        NioEventLoopGroup boss = new NioEventLoopGroup();
        NioEventLoopGroup worker = new NioEventLoopGroup();
        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.channel(NioServerSocketChannel.class);
            serverBootstrap.group(boss, worker);
            serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
                @Override
                protected void initChannel(SocketChannel ch) throws Exception {
                    ch.pipeline().addLast(new LoggingHandler(LogLevel.DEBUG));
                    ch.pipeline().addLast(new HttpServerCodec());
                    // 只针对某一种类型的请求处理，此处针对 HttpRequest
                    ch.pipeline().addLast(new SimpleChannelInboundHandler<HttpRequest>() {
                        @Override
                        protected void channelRead0(ChannelHandlerContext ctx, HttpRequest msg) throws Exception {
                            // 获取请求
                            log.debug(msg.uri());

                            // 返回响应
                            DefaultFullHttpResponse response = new DefaultFullHttpResponse(
                                msg.protocolVersion(), HttpResponseStatus.OK);

                            byte[] bytes = "<h1>Hello, world!</h1>".getBytes();

                            response.headers().setInt(CONTENT_LENGTH, bytes.length);
                            response.content().writeBytes(bytes);

                            // 写回响应
                            ctx.writeAndFlush(response);
                        }
                    });
                }
            });
            ChannelFuture channelFuture = serverBootstrap.bind(8080).sync();
            channelFuture.channel().closeFuture().sync();
        } catch (InterruptedException e) {
            log.error("n3.server error", e);
        } finally {
            boss.shutdownGracefully();
            worker.shutdownGracefully();
        }
    }
}
```





***



#### 自定义协议

处理器代码：

```java
@Slf4j
public class MessageCodec extends ByteToMessageCodec<Message> {
    // 编码
    @Override
    public void encode(ChannelHandlerContext ctx, Message msg, ByteBuf out) throws Exception {
        // 4 字节的魔数
        out.writeBytes(new byte[]{1, 2, 3, 4});
        // 1 字节的版本,
        out.writeByte(1);
        // 1 字节的序列化方式 jdk 0 , json 1
        out.writeByte(0);
        // 1 字节的指令类型
        out.writeByte(msg.getMessageType());
        // 4 个字节
        out.writeInt(msg.getSequenceId());
        // 无意义，对齐填充, 1 字节
        out.writeByte(0xff);
        // 获取内容的字节数组，msg 对象序列化
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream oos = new ObjectOutputStream(bos);
        oos.writeObject(msg);
        byte[] bytes = bos.toByteArray();
        // 长度
        out.writeInt(bytes.length);
        // 写入内容
        out.writeBytes(bytes);
    }

    // 解码
    @Override
    protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {
        int magicNum = in.readInt();
        byte version = in.readByte();
        byte serializerType = in.readByte();
        byte messageType = in.readByte();
        int sequenceId = in.readInt();
        in.readByte();
        int length = in.readInt();
        byte[] bytes = new byte[length];
        in.readBytes(bytes, 0, length);
        ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(bytes));
        Message message = (Message) ois.readObject();
        log.debug("{}, {}, {}, {}, {}, {}", magicNum, version, serializerType, messageType, sequenceId, length);
        log.debug("{}", message);
        out.add(message);
    }
}
```

测试代码：

```java
public static void main(String[] args) throws Exception {
    EmbeddedChannel channel = new EmbeddedChannel(new LoggingHandler(), new MessageCodec());
    // encode
    LoginRequestMessage message = new LoginRequestMessage("zhangsan", "123");
    channel.writeOutbound(message);

    // decode
    ByteBuf buf = ByteBufAllocator.DEFAULT.buffer();
    new MessageCodec().encode(null, message, buf);
    // 入站
    channel.writeInbound(buf);
}
public class LoginRequestMessage extends Message {
    private String username;
    private String password;
    // set + get 
}
```

![](https://gitee.com/seazean/images/raw/master/Frame/Netty-自定义协议.png)



***



#### Sharable

@Sharable 注解的添加时机：

* 当 handler 不保存状态时，就可以安全地在多线程下被共享

* 对于编解码器类不能继承 ByteToMessageCodec 或 CombinedChannelDuplexHandler，它们的构造方法对 @Sharable 有限制

  ```java
  protected ByteToMessageCodec(boolean preferDirect) {
      ensureNotSharable();
      outboundMsgMatcher = TypeParameterMatcher.find(this, ByteToMessageCodec.class, "I");
      encoder = new Encoder(preferDirect);
  }
  ```

  ```java
  protected void ensureNotSharable() {
      // 如果类上有该注解
      if (isSharable()) {
          throw new IllegalStateException();
      }
  }
  ```

* 如果能确保编解码器不会保存状态，可以继承 MessageToMessageCodec 父类

  ````java
  @Slf4j
  @ChannelHandler.Sharable
  // 必须和 LengthFieldBasedFrameDecoder 一起使用，确保接到的 ByteBuf 消息是完整的
  public class MessageCodecSharable extends MessageToMessageCodec<ByteBuf, Message> {
      @Override
      protected void encode(ChannelHandlerContext ctx, Message msg, List<Object> outList) throws Exception {
          ByteBuf out = ctx.alloc().buffer();
          // 4 字节的魔数
          out.writeBytes(new byte[]{1, 2, 3, 4});
  		// ....
          outList.add(out);
      }
  
      @Override
      protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {
          //....
      }
  }
  ````





***





## 场景优化

### 空闲检测

#### 连接假死

连接假死就是客户端数据发不出去，服务端也一直收不到数据，保持这种状态，假死的连接占用的资源不能自动释放，而且向假死连接发送数据，得到的反馈是发送超时

解决方案：每隔一段时间就检查这段时间内是否接收到客户端数据，没有就可以判定为连接假死

IdleStateHandler 是 Netty 提供的处理空闲状态的处理器，用来判断是不是读空闲时间或写空闲时间过长

* 参数一 long readerIdleTime：读空闲，表示多长时间没有读
* 参数二 long writerIdleTime：写空闲，表示多长时间没有写
* 参数三 long allIdleTime：读写空闲，表示多长时间没有读写

```java
serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
	@Override
	protected void initChannel(SocketChannel ch) throws Exception {
        ch.pipeline().addLast(new LengthFieldBasedFrameDecoder(1024, 12, 4, 0, 0));
        ch.pipeline().addLast(new MessageCodec());
        // 5s 内如果没有收到 channel 的数据，会触发一个 IdleState#READER_IDLE 事件，
        ch.pipeline().addLast(new IdleStateHandler(5, 0, 0));
        // ChannelDuplexHandler 【可以同时作为入站和出站】处理器
        ch.pipeline().addLast(new ChannelDuplexHandler() {
            // 用来触发特殊事件
            @Override
            public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception{
                IdleStateEvent event = (IdleStateEvent) evt;
                // 触发了读空闲事件
                if (event.state() == IdleState.READER_IDLE) {
                    log.debug("已经 5s 没有读到数据了");
                    ctx.channel().close();
                }
            }
        });
    }
}
```



***



#### 心跳机制

客户端定时向服务器端发送数据，**时间间隔要小于服务器定义的空闲检测的时间间隔**，就能防止误判连接假死，这就是心跳机制

```java
bootstrap.handler(new ChannelInitializer<SocketChannel>() {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ch.pipeline().addLast(new LengthFieldBasedFrameDecoder(1024, 12, 4, 0, 0));
        ch.pipeline().addLast(new MessageCodec());
        // 3s 内如果没有向服务器写数据，会触发一个 IdleState#WRITER_IDLE 事件
        ch.pipeline().addLast(new IdleStateHandler(0, 3, 0));
        // ChannelDuplexHandler 可以同时作为入站和出站处理器
        ch.pipeline().addLast(new ChannelDuplexHandler() {
            // 用来触发特殊事件
            @Override
            public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
                IdleStateEvent event = (IdleStateEvent) evt;
                // 触发了写空闲事件
                if (event.state() == IdleState.WRITER_IDLE) {
                    // 3s 没有写数据了，【发送一个心跳包】
                    ctx.writeAndFlush(new PingMessage());
                }
            }
        });
    }
}
```





****



### 序列化

#### 普通方式

序列化，反序列化主要用在消息正文的转换上

* 序列化时，需要将 Java 对象变为要传输的数据（可以是 byte[]，或 json 等，最终都需要变成 byte[]）
* 反序列化时，需要将传入的正文数据还原成 Java 对象，便于处理

代码实现：

* 抽象一个 Serializer 接口

  ```java
  public interface Serializer {
      // 反序列化方法
      <T> T deserialize(Class<T> clazz, byte[] bytes);
      // 序列化方法
      <T> byte[] serialize(T object);
  }
  ```

* 提供两个实现

  ```java
  enum SerializerAlgorithm implements Serializer {
  	// Java 实现
      Java {
          @Override
          public <T> T deserialize(Class<T> clazz, byte[] bytes) {
              try {
                  ObjectInputStream in = 
                      new ObjectInputStream(new ByteArrayInputStream(bytes));
                  Object object = in.readObject();
                  return (T) object;
              } catch (IOException | ClassNotFoundException e) {
                  throw new RuntimeException("SerializerAlgorithm.Java 反序列化错误", e);
              }
          }
  
          @Override
          public <T> byte[] serialize(T object) {
              try {
                  ByteArrayOutputStream out = new ByteArrayOutputStream();
                  new ObjectOutputStream(out).writeObject(object);
                  return out.toByteArray();
              } catch (IOException e) {
                  throw new RuntimeException("SerializerAlgorithm.Java 序列化错误", e);
              }
          }
      }, 
      // Json 实现(引入了 Gson 依赖)
      Json {
          @Override
          public <T> T deserialize(Class<T> clazz, byte[] bytes) {
              return new Gson().fromJson(new String(bytes, StandardCharsets.UTF_8), clazz);
          }
  
          @Override
          public <T> byte[] serialize(T object) {
              return new Gson().toJson(object).getBytes(StandardCharsets.UTF_8);
          }
      };
  
      // 需要从协议的字节中得到是哪种序列化算法
      public static SerializerAlgorithm getByInt(int type) {
          SerializerAlgorithm[] array = SerializerAlgorithm.values();
          if (type < 0 || type > array.length - 1) {
              throw new IllegalArgumentException("超过 SerializerAlgorithm 范围");
          }
          return array[type];
      }
  }
  ```

  



***



#### ProtoBuf

##### 基本介绍

Codec（编解码器）的组成部分有两个：Decoder（解码器）和 Encoder（编码器）。Encoder 负责把业务数据转换成字节码数据，Decoder 负责把字节码数据转换成业务数据

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-编码解码.png" style="zoom: 67%;" />



Protobuf 是 Google 发布的开源项目，全称  Google Protocol Buffers ，是一种轻便高效的结构化数据存储格式，可以用于结构化数据串行化，或者说序列化。很适合做数据存储或  RPC（远程过程调用  remote procedure call）数据交换格式。目前很多公司从 HTTP + Json 转向 TCP + Protobuf ，效率会更高

Protobuf 是以 message 的方式来管理数据，支持跨平台、跨语言（客户端和服务器端可以是不同的语言编写的），高性能、高可靠性

工作过程：使用 Protobuf 编译器自动生成代码，Protobuf 是将类的定义使用 .proto 文件进行描述，然后通过 protoc.exe 编译器根据  .proto 自动生成 .java 文件



***



##### 代码实现

* 单个 message：

  ```protobuf
  syntax = "proto3"; 								// 版本
  option java_outer_classname = "StudentPOJO";	// 生成的外部类名，同时也是文件名
  // protobuf 使用 message 管理数据
  message Student { 	// 在 StudentPOJO 外部类种生成一个内部类 Student，是真正发送的 POJO 对象
      int32 id = 1; 	// Student 类中有一个属性：名字为 id 类型为 int32(protobuf类型) ，1表示属性序号，不是值
      string name = 2;
  }
  ```

  <img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-Protobuf编译文件.png" style="zoom:80%;" />

  编译 `protoc.exe --java_out=.Student.proto`（cmd 窗口输入） 将生成的 StudentPOJO 放入到项目使用

  Server 端：

  ```java
  new ServerBootstrap() //...
      .childHandler(new ChannelInitializer<SocketChannel>() {	// 创建一个通道初始化对象
          // 给pipeline 设置处理器
          @Override
          protected void initChannel(SocketChannel ch) throws Exception {
              // 在pipeline加入ProtoBufDecoder，指定对哪种对象进行解码
              ch.pipeline().addLast("decoder", new ProtobufDecoder(
                  							StudentPOJO.Student.getDefaultInstance()));
              ch.pipeline().addLast(new NettyServerHandler());
          }
      }); 
  }
  ```

  Client 端：

  ```java
  new Bootstrap().group(group) 			// 设置线程组
      .channel(NioSocketChannel.class) 	// 设置客户端通道的实现类(反射)
      .handler(new ChannelInitializer<SocketChannel>() {
          @Override
          protected void initChannel(SocketChannel ch) throws Exception {
              // 在pipeline中加入 ProtoBufEncoder
              ch.pipeline().addLast("encoder", new ProtobufEncoder());
              ch.pipeline().addLast(new NettyClientHandler()); // 加入自定义的业务处理器
          }
      });
  ```

* 多个 message：Protobuf 可以使用 message 管理其他的 message。假设某个项目需要传输 20 个对象，可以在一个文件里定义 20 个 message，最后再用一个总的 message 来决定在实际传输时真正需要传输哪一个对象

  ```protobuf
  syntax = "proto3";
  option optimize_for = SPEED; 					// 加快解析
  option java_package="com.atguigu.netty.codec2";	// 指定生成到哪个包下
  option java_outer_classname="MyDataInfo"; 		// 外部类名, 文件名
  
  message MyMessage {
      // 定义一个枚举类型，DataType 如果是 0 则表示一个 Student 对象实例，DataType 这个名称自定义
      enum DataType {
          StudentType = 0; //在 proto3 要求 enum 的编号从 0 开始
          WorkerType = 1;
      }
  
      // 用 data_type 来标识传的是哪一个枚举类型，这里才真正开始定义 Message 的数据类型
      DataType data_type = 1;  // 所有后面的数字都只是编号而已
  
      // oneof 关键字，表示每次枚举类型进行传输时，限制最多只能传输一个对象。
      // dataBody名称也是自定义的
      // MyMessage 里出现的类型只有两个 DataType 类型，Student 或者 Worker 类型，在真正传输的时候只会有一个出现
      oneof dataBody {
          Student student = 2;  //注意这后面的数字也都只是编号而已，上面DataType data_type = 1  占了第一个序号了
          Worker worker = 3;
      }
  
  
  }
  
  message Student {
      int32 id = 1;		// Student类的属性
      string name = 2; 	//
  }
  message Worker {
      string name=1;
      int32 age=2;
  }
  ```

  编译：

  Server 端：

  ```java
  ch.pipeline().addLast("decoder", new ProtobufDecoder(MyDataInfo.MyMessage.getDefaultInstance()));
  ```

  Client 端：

  ```java
  pipeline.addLast("encoder", new ProtobufEncoder());
  ```





***



### 长连接

HTTP 协议是无状态的，浏览器和服务器间的请求响应一次，下一次会重新创建连接。实现基于 WebSocket 的长连接的全双工的交互，改变 HTTP 协议多次请求的约束

开发需求：

* 实现长连接，服务器与浏览器相互通信客户端
* 浏览器和服务器端会相互感知，比如服务器关闭了，浏览器会感知，同样浏览器关闭了，服务器会感知

代码实现：

* WebSocket：

  * WebSocket 的数据是**以帧（frame）形式传递**，WebSocketFrame 下面有六个子类，代表不同的帧格式

  * 浏览器请求 URL：ws://localhost:8080/xxx

  ```java
  public class MyWebSocket {
      public static void main(String[] args) throws Exception {
          // 创建两个线程组
          EventLoopGroup bossGroup = new NioEventLoopGroup(1);
          EventLoopGroup workerGroup = new NioEventLoopGroup();
          try {
  
              ServerBootstrap serverBootstrap = new ServerBootstrap();
              serverBootstrap.group(bossGroup, workerGroup);
              serverBootstrap.channel(NioServerSocketChannel.class);
              serverBootstrap.handler(new LoggingHandler(LogLevel.INFO));
              serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
                  @Override
                  protected void initChannel(SocketChannel ch) throws Exception {
                      ChannelPipeline pipeline = ch.pipeline();
  
                      // 基于 http 协议，使用 http 的编码和解码器
                      pipeline.addLast(new HttpServerCodec());
                      // 是以块方式写，添加 ChunkedWriteHandler 处理器
                      pipeline.addLast(new ChunkedWriteHandler());
  
                      // http 数据在传输过程中是分段, HttpObjectAggregator 就是可以将多个段聚合
                      //  这就就是为什么，当浏览器发送大量数据时，就会发出多次 http 请求
                      pipeline.addLast(new HttpObjectAggregator(8192));
          
                      // WebSocketServerProtocolHandler 核心功能是【将 http 协议升级为 ws 协议】，保持长连接
                      pipeline.addLast(new WebSocketServerProtocolHandler("/hello"));
  
                      // 自定义的handler ，处理业务逻辑
                      pipeline.addLast(new MyTextWebSocketFrameHandler());
                  }
              });
  
              //启动服务器
              ChannelFuture channelFuture = serverBootstrap.bind(8080).sync();
              channelFuture.channel().closeFuture().sync();
  
          } finally {
              bossGroup.shutdownGracefully();
              workerGroup.shutdownGracefully();
          }
      }
  }
  ```

* 处理器：

  ```java
  public class MyTextWebSocketFrameHandler extends SimpleChannelInboundHandler<TextWebSocketFrame> {
      // TextWebSocketFrame 类型，表示一个文本帧(frame)
      @Override
      protected void channelRead0(ChannelHandlerContext ctx, TextWebSocketFrame msg) throws Exception {
          System.out.println("服务器收到消息 " + msg.text());
          // 回复消息
          ctx.writeAndFlush(new TextWebSocketFrame("服务器时间" + LocalDateTime.now() + " " + msg.text()));
      }
  
      // 当web客户端连接后， 触发方法
      @Override
      public void handlerAdded(ChannelHandlerContext ctx) throws Exception {
          // id 表示唯一的值，LongText 是唯一的 ShortText 不是唯一
          System.out.println("handlerAdded 被调用" + ctx.channel().id().asLongText());
          System.out.println("handlerAdded 被调用" + ctx.channel().id().asShortText());
      }
  
      @Override
      public void handlerRemoved(ChannelHandlerContext ctx) throws Exception {
          System.out.println("handlerRemoved 被调用" + ctx.channel().id().asLongText());
      }
  
      @Override
      public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
          System.out.println("异常发生 " + cause.getMessage());
          ctx.close(); // 关闭连接
      }
  }
  ```

* HTML：

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
  <script>
      var socket;
      // 判断当前浏览器是否支持websocket
      if(window.WebSocket) {
          //go on
          socket = new WebSocket("ws://localhost:8080/hello");
          //相当于channelReado, ev 收到服务器端回送的消息
          socket.onmessage = function (ev) {
              var rt = document.getElementById("responseText");
              rt.value = rt.value + "\n" + ev.data;
          }
  
          //相当于连接开启(感知到连接开启)
          socket.onopen = function (ev) {
              var rt = document.getElementById("responseText");
              rt.value = "连接开启了.."
          }
  
          //相当于连接关闭(感知到连接关闭)
          socket.onclose = function (ev) {
  
              var rt = document.getElementById("responseText");
              rt.value = rt.value + "\n" + "连接关闭了.."
          }
      } else {
          alert("当前浏览器不支持websocket")
      }
  
      // 发送消息到服务器
      function send(message) {
          // 先判断socket是否创建好
          if(!window.socket) {
              return;
          }
          if(socket.readyState == WebSocket.OPEN) {
              // 通过socket 发送消息
              socket.send(message)
          } else {
              alert("连接没有开启");
          }
      }
  </script>
      <form onsubmit="return false">
          <textarea name="message" style="height: 300px; width: 300px"></textarea>
          <input type="button" value="发生消息" onclick="send(this.form.message.value)">
          <textarea id="responseText" style="height: 300px; width: 300px"></textarea>
          <input type="button" value="清空内容" onclick="document.getElementById('responseText').value=''">
      </form>
  </body>
  </html>
  ```





***



### 参数调优

#### CONNECT

参数配置方式：

* 客户端通过 .option() 方法配置参数，给 SocketChannel 配置参数
* 服务器端：
  * new ServerBootstrap().option()： 给 ServerSocketChannel 配置参数
  * new ServerBootstrap().childOption()：给 SocketChannel 配置参数

CONNECT_TIMEOUT_MILLIS 参数：

* 属于 SocketChannal 参数
* 在客户端建立连接时，如果在指定毫秒内无法连接，会抛出 timeout 异常

* SO_TIMEOUT 主要用在阻塞 IO，阻塞 IO 中 accept，read 等都是无限等待的，如果不希望永远阻塞，可以调整超时时间

```java
public class ConnectionTimeoutTest {
    public static void main(String[] args) {
        NioEventLoopGroup group = new NioEventLoopGroup();
        try {
            Bootstrap bootstrap = new Bootstrap()
                    .group(group)
                    .option(ChannelOption.CONNECT_TIMEOUT_MILLIS, 5000)
                    .channel(NioSocketChannel.class)
                    .handler(new LoggingHandler());
            ChannelFuture future = bootstrap.connect("127.0.0.1", 8080);
            future.sync().channel().closeFuture().sync();
        } catch (Exception e) {
            e.printStackTrace();
            log.debug("timeout");
        } finally {
            group.shutdownGracefully();
        }
    }
}
```



****



#### SO_BACKLOG

属于 ServerSocketChannal 参数，通过 `option(ChannelOption.SO_BACKLOG, value)` 来设置大小

在 Linux 2.2 之前，backlog 大小包括了两个队列的大小，在 2.2 之后，分别用下面两个参数来控制

* sync queue：半连接队列，大小通过 `/proc/sys/net/ipv4/tcp_max_syn_backlog` 指定，在 `syncookies` 启用的情况下，逻辑上没有最大值限制
* accept queue：全连接队列，大小通过 `/proc/sys/net/core/somaxconn` 指定，在使用 listen 函数时，内核会根据传入的 backlog 参数与系统参数，取二者的较小值。如果 accpet queue 队列满了，server 将**发送一个拒绝连接的错误信息**到 client

![](https://gitee.com/seazean/images/raw/master/Frame/Netty-TCP三次握手.png)



****



#### 其他参数

ALLOCATOR：属于 SocketChannal 参数，用来分配 ByteBuf， ctx.alloc()

RCVBUF_ALLOCATOR：属于 SocketChannal 参数

* 控制 Netty 接收缓冲区大小
* 负责入站数据的分配，决定入站缓冲区的大小（并可动态调整），统一采用 direct 直接内存，具体池化还是非池化由 allocator 决定







***







# RocketMQ

## 基本介绍

### 消息队列

消息队列是一种先进先出的数据结构，常见的应用场景：

* 应用解耦：系统的耦合性越高，容错性就越低

  实例：用户创建订单后，耦合调用库存系统、物流系统、支付系统，任何一个子系统出了故障都会造成下单异常，影响用户使用体验。使用消息队列解耦合，比如物流系统发生故障，需要几分钟恢复，将物流系统要处理的数据缓存到消息队列中，用户的下单操作正常完成。等待物流系统正常后处理存在消息队列中的订单消息即可，终端系统感知不到物流系统发生过几分钟故障

  ![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-解耦.png)

* 流量削峰：应用系统如果遇到系统请求流量的瞬间猛增，有可能会将系统压垮，使用消息队列可以将大量请求缓存起来，分散到很长一段时间处理，这样可以提高系统的稳定性和用户体验。

  ![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-流量削峰.png)

* 数据分发：让数据在多个系统更加之间进行流通，数据的产生方不需要关心谁来使用数据，只需要将数据发送到消息队列，数据使用方直接在消息队列中直接获取数据

  ![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-数据分发.png)

主要缺点包含以下几点：

* 系统可用性降低：系统引入的外部依赖越多，系统稳定性越差，一旦 MQ 宕机，就会对业务造成影响

  引申问题：如何保证 MQ 的高可用？

* 系统复杂度提高：MQ 的加入大大增加了系统的复杂度，以前系统间是同步的远程调用，现在是通过 MQ 进行异步调用

  引申问题：如何保证消息没有被重复消费？怎么处理消息丢失情况？那么保证消息传递的顺序性？

* 一致性问题：A 系统处理完业务，通过 MQ 给 B、C、D 三个系统发消息数据，如果 B 系统、C 系统处理成功，D 系统处理失败

  引申问题：如何保证消息数据处理的一致性？





****



### 安装测试

安装需要 Java 环境，下载解压后进入安装目录，进行启动：

* 启动 NameServer

  ```sh
  # 1.启动 NameServer
  nohup sh bin/mqnamesrv &
  # 2.查看启动日志
  tail -f ~/logs/rocketmqlogs/namesrv.log
  ```

  RocketMQ 默认的虚拟机内存较大，需要编辑如下两个配置文件，修改 JVM 内存大小

  ```shell
  # 编辑runbroker.sh和runserver.sh修改默认JVM大小
  vi runbroker.sh
  vi runserver.sh
  ```

  参考配置：JAVA_OPT="${JAVA_OPT} -server -Xms256m -Xmx256m -Xmn128m -XX:MetaspaceSize=128m  -XX:MaxMetaspaceSize=320m"

* 启动 Broker

  ```sh
  # 1.启动 Broker
  nohup sh bin/mqbroker -n localhost:9876 autoCreateTopicEnable=true &
  # 2.查看启动日志
  tail -f ~/logs/rocketmqlogs/broker.log 
  ```

* 发送消息：

  ```sh
  # 1.设置环境变量
  export NAMESRV_ADDR=localhost:9876
  # 2.使用安装包的 Demo 发送消息
  sh bin/tools.sh org.apache.rocketmq.example.quickstart.Producer
  ```

* 接受消息：

  ```sh
  # 1.设置环境变量
  export NAMESRV_ADDR=localhost:9876
  # 2.接收消息
  sh bin/tools.sh org.apache.rocketmq.example.quickstart.Consumer

* 关闭 RocketMQ：

  ```sh
  # 1.关闭 NameServer
  sh bin/mqshutdown namesrv
  # 2.关闭 Broker
  sh bin/mqshutdown broker



***



### 相关概念

RocketMQ 主要由 Producer、Broker、Consumer 三部分组成，其中 Producer 负责生产消息，Consumer 负责消费消息，Broker 负责存储消息，NameServer 负责管理 Broker

* 代理服务器（Broker Server）：消息中转角色，负责**存储消息、转发消息**。在 RocketMQ 系统中负责接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备，也存储消息相关的元数据，包括消费者组、消费进度偏移和主题和队列消息等
* 名字服务（Name Server）：充当**路由消息**的提供者。生产者或消费者能够通过名字服务查找各主题相应的 Broker IP 列表
* 消息生产者（Producer）：负责**生产消息**，把业务应用系统里产生的消息发送到 Broker 服务器。RocketMQ 提供多种发送方式，同步发送、异步发送、顺序发送、单向发送，同步和异步方式均需要 Broker 返回确认信息，单向发送不需要；可以通过 MQ 的负载均衡模块选择相应的 Broker 集群队列进行消息投递，投递的过程支持快速失败并且低延迟
* 消息消费者（Consumer）：负责**消费消息**，一般是后台系统负责异步消费，一个消息消费者会从 Broker 服务器拉取消息、并将其提供给应用程序。从用户应用的角度而提供了两种消费形式：
  * 拉取式消费（Pull Consumer）：应用通主动调用 Consumer 的拉消息方法从 Broker 服务器拉消息，主动权由应用控制，一旦获取了批量消息，应用就会启动消费过程
  * 推动式消费（Push Consumer）：该模式下 Broker 收到数据后会主动推送给消费端，实时性较高
* 生产者组（Producer Group）：同一类 Producer 的集合，发送同一类消息且发送逻辑一致。如果发送的是事务消息且原始生产者在发送之后崩溃，**则 Broker 服务器会联系同一生产者组的其他生产者实例以提交或回溯消费**
* 消费者组（Consumer Group）：同一类 Consumer 的集合，消费者实例必须订阅完全相同的 Topic，消费同一类消息且消费逻辑一致。消费者组使得在消息消费方面更容易的实现负载均衡和容错。RocketMQ 支持两种消息模式：
  *  集群消费（Clustering）：相同 Consumer Group 的每个 Consumer 实例平均分摊消息
  *  广播消费（Broadcasting）：相同 Consumer Group 的每个 Consumer 实例都接收全量的消息

每个 Broker 可以存储多个 Topic 的消息，每个 Topic 的消息也可以分片存储于不同的 Broker，Message Queue（消息队列）是用于存储消息的物理地址，每个 Topic 中的消息地址存储于多个 Message Queue 中

* 主题（Topic）：表示一类消息的集合，每个主题包含若干条消息，每条消息只属于一个主题，是 RocketMQ 消息订阅的基本单位

* 消息（Message）：消息系统所传输信息的物理载体，生产和消费数据的最小单位，每条消息必须属于一个主题。RocketMQ 中每个消息拥有唯一的 Message ID，且可以携带具有业务标识的 Key，系统提供了通过 Message ID 和 Key 查询消息的功能

* 标签（Tag）：为消息设置的标志，用于同一主题下区分不同类型的消息。标签能够有效地保持代码的清晰度和连贯性，并优化 RocketMQ 提供的查询系统，消费者可以根据 Tag 实现对不同子主题的不同消费逻辑，实现更好的扩展性

* 普通顺序消息（Normal Ordered Message）：消费者通过同一个消息队列（Topic 分区）收到的消息是有顺序的，不同消息队列收到的消息则可能是无顺序的

* 严格顺序消息（Strictly Ordered Message）：消费者收到的所有消息均是有顺序的



官方文档：https://github.com/apache/rocketmq/tree/master/docs/cn（基础知识部分的笔记参考官方文档编写）





****





## 消息操作

### 基本样例

#### 订阅发布

消息的发布是指某个生产者向某个 Topic 发送消息，消息的订阅是指某个消费者关注了某个 Topic 中带有某些 Tag 的消息，进而从该 Topic 消费数据

导入 MQ 客户端依赖

```xml
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-client</artifactId>
    <version>4.4.0</version>
</dependency>
```

消息发送者步骤分析：

1. 创建消息生产者 producer，并制定生产者组名
2. 指定 Nameserver 地址
3. 启动 producer
4. 创建消息对象，指定主题 Topic、Tag 和消息体
5. 发送消息
6. 关闭生产者 producer

消息消费者步骤分析：

1. 创建消费者 Consumer，制定消费者组名
2. 指定 Nameserver 地址
3. 订阅主题 Topic 和 Tag
4. 设置回调函数，处理消息
5. 启动消费者 consumer



官方文档：https://github.com/apache/rocketmq/blob/master/docs/cn/RocketMQ_Example.md



***



#### 发送消息

##### 同步发送

使用 RocketMQ 发送三种类型的消息：同步消息、异步消息和单向消息，其中前两种消息是可靠的，因为会有发送是否成功的应答

这种可靠性同步地发送方式使用的比较广泛，比如：重要的消息通知，短信通知

```java
public class SyncProducer {
	public static void main(String[] args) throws Exception {
    	// 实例化消息生产者Producer
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
    	// 设置NameServer的地址
    	producer.setNamesrvAddr("localhost:9876");
    	// 启动Producer实例
        producer.start();
    	for (int i = 0; i < 100; i++) {
    	    // 创建消息，并指定Topic，Tag和消息体
    	    Message msg = new Message(
                "TopicTest" /* Topic */,
                "TagA" /* Tag */,
        		("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET) /* Message body */);
            
        	// 发送消息到一个Broker
            SendResult sendResult = producer.send(msg);
            // 通过sendResult返回消息是否成功送达
            System.out.printf("%s%n", sendResult);
    	}
    	// 如果不再发送消息，关闭Producer实例。
    	producer.shutdown();
    }
}
```



***



##### 异步发送

异步消息通常用在对响应时间敏感的业务场景，即发送端不能容忍长时间地等待 Broker 的响应

```java
public class AsyncProducer {
	public static void main(String[] args) throws Exception {
    	// 实例化消息生产者Producer
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
    	// 设置NameServer的地址
        producer.setNamesrvAddr("localhost:9876");
    	// 启动Producer实例
        producer.start();
        producer.setRetryTimesWhenSendAsyncFailed(0);
	
        int messageCount = 100;
		// 根据消息数量实例化倒计时计算器
        final CountDownLatch2 countDownLatch = new CountDownLatch2(messageCount);
        for (int i = 0; i < messageCount; i++) {
            final int index = i;
            // 创建消息，并指定Topic，Tag和消息体
            Message msg = new Message("TopicTest", "TagA", "OrderID188",
                                      "Hello world".getBytes(RemotingHelper.DEFAULT_CHARSET));

            // SendCallback接收异步返回结果的回调
            producer.send(msg, new SendCallback() {
                // 发送成功回调函数
                @Override
                public void onSuccess(SendResult sendResult) {
                    countDownLatch.countDown();
                    System.out.printf("%-10d OK %s %n", index, sendResult.getMsgId());
                }
                
                @Override
                public void onException(Throwable e) {
                    countDownLatch.countDown();
                    System.out.printf("%-10d Exception %s %n", index, e);
                    e.printStackTrace();
                }
            });
        }
        // 等待5s
        countDownLatch.await(5, TimeUnit.SECONDS);
        // 如果不再发送消息，关闭Producer实例。
        producer.shutdown();
    }
}
```



***



##### 单向发送

单向发送主要用在不特别关心发送结果的场景，例如日志发送

```java
public class OnewayProducer {
	public static void main(String[] args) throws Exception{
    	// 实例化消息生产者Producer
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
    	// 设置NameServer的地址
        producer.setNamesrvAddr("localhost:9876");
    	// 启动Producer实例
        producer.start();
    	for (int i = 0; i < 100; i++) {
        	// 创建消息，并指定Topic，Tag和消息体
        	Message msg = new Message("TopicTest","TagA",
                          ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
        	// 发送单向消息，没有任何返回结果
        	producer.sendOneway(msg);
    	}
    	// 如果不再发送消息，关闭Producer实例。
    	producer.shutdown();
    }
}
```



****



#### 消费消息

```java
public class Consumer {
	public static void main(String[] args) throws InterruptedException, MQClientException {
    	// 实例化消费者
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("please_rename_unique_group_name");
    	// 设置NameServer的地址
        consumer.setNamesrvAddr("localhost:9876");

    	// 订阅一个或者多个Topic，以及Tag来过滤需要消费的消息
        consumer.subscribe("TopicTest", "*");
    	// 注册消息监听器，回调实现类来处理从broker拉取回来的消息
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            // 接受消息内容
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
                System.out.printf("%s Receive New Messages: %s %n", Thread.currentThread().getName(), msgs);
                // 标记该消息已经被成功消费
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        // 启动消费者实例
        consumer.start();
        System.out.printf("Consumer Started.%n");
	}
}
```





****



### 顺序消息

#### 原理解析

消息有序指的是一类消息消费时，能按照发送的顺序来消费。例如：一个订单产生了三条消息分别是订单创建、订单付款、订单完成。消费时要按照这个顺序消费才能有意义，但是同时订单之间是可以并行消费的，RocketMQ 可以严格的保证消息有序。

顺序消息分为全局顺序消息与分区顺序消息，

- 全局顺序：对于指定的一个 Topic，所有消息按照严格的先入先出（FIFO）的顺序进行发布和消费。 适用于性能要求不高，所有的消息严格按照 FIFO 原则进行消息发布和消费的场景
- 分区顺序：对于指定的一个 Topic，所有消息根据 sharding key 进行分区，同一个分组内的消息按照严格的 FIFO 顺序进行发布和消费。Sharding key 是顺序消息中用来区分不同分区的关键字段，和普通消息的 Key 是完全不同的概念。 适用于性能要求高，以 sharding key 作为分区字段，在同一个区中严格的按照 FIFO 原则进行消息发布和消费的场景

在默认的情况下消息发送会采取 Round Robin 轮询方式把消息发送到不同的 queue（分区队列），而消费消息是从多个 queue 上拉取消息，这种情况发送和消费是不能保证顺序。但是如果控制发送的顺序消息只依次发送到同一个 queue 中，消费的时候只从这个 queue 上依次拉取，则就保证了顺序。当发送和消费参与的 queue 只有一个，则是全局有序；如果多个queue参与，则为分区有序，即相对每个 queue，消息都是有序的



***



#### 代码实现

一个订单的顺序流程是：创建、付款、推送、完成，订单号相同的消息会被先后发送到同一个队列中，消费时同一个 OrderId 获取到的肯定是同一个队列

```java
public class Producer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
        producer.setNamesrvAddr("127.0.0.1:9876");
        producer.start();
		// 标签集合
        String[] tags = new String[]{"TagA", "TagC", "TagD"};

        // 订单列表
        List<OrderStep> orderList = new Producer().buildOrders();

        Date date = new Date();
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String dateStr = sdf.format(date);
        for (int i = 0; i < 10; i++) {
            // 加个时间前缀
            String body = dateStr + " Hello RocketMQ " + orderList.get(i);
            Message msg = new Message("OrderTopic", tags[i % tags.length], "KEY" + i, body.getBytes());
			/**
             * 参数一：消息对象
             * 参数二：消息队列的选择器
             * 参数三：选择队列的业务标识（订单 ID）
             */
            SendResult sendResult = producer.send(msg, new MessageQueueSelector() {
                @Override
                /**
                 * mqs：队列集合
                 * msg：消息对象
                 * arg：业务标识的参数
                 */
                public MessageQueue select(List<MessageQueue> mqs, Message msg, Object arg) {
                    Long id = (Long) arg;
                    long index = id % mqs.size(); // 根据订单id选择发送queue
                    return mqs.get((int) index);
                }
            }, orderList.get(i).getOrderId());//订单id

            System.out.println(String.format("SendResult status:%s, queueId:%d, body:%s",
                    sendResult.getSendStatus(),
                    sendResult.getMessageQueue().getQueueId(),
                    body));
        }

        producer.shutdown();
    }

    // 订单的步骤
    private static class OrderStep {
        private long orderId;
        private String desc;
        // set + get
    }

    // 生成模拟订单数据
    private List<OrderStep> buildOrders() {
        List<OrderStep> orderList = new ArrayList<OrderStep>();

        OrderStep orderDemo = new OrderStep();
        orderDemo.setOrderId(15103111039L);
        orderDemo.setDesc("创建");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103111065L);
        orderDemo.setDesc("创建");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103111039L);
        orderDemo.setDesc("付款");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103117235L);
        orderDemo.setDesc("创建");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103111065L);
        orderDemo.setDesc("付款");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103117235L);
        orderDemo.setDesc("付款");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103111065L);
        orderDemo.setDesc("完成");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103111039L);
        orderDemo.setDesc("推送");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103117235L);
        orderDemo.setDesc("完成");
        orderList.add(orderDemo);

        orderDemo = new OrderStep();
        orderDemo.setOrderId(15103111039L);
        orderDemo.setDesc("完成");
        orderList.add(orderDemo);

        return orderList;
    }
}
```

```java
// 顺序消息消费，带事务方式（应用可控制Offset什么时候提交）
public class ConsumerInOrder {
    public static void main(String[] args) throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("please_rename_unique_group_name_3");
        consumer.setNamesrvAddr("127.0.0.1:9876");
        // 设置Consumer第一次启动是从队列头部开始消费还是队列尾部开始消费
        // 如果非第一次启动，那么按照上次消费的位置继续消费
        consumer.setConsumeFromWhere(ConsumeFromWhere.CONSUME_FROM_FIRST_OFFSET);
		// 订阅三个tag
        consumer.subscribe("OrderTopic", "TagA || TagC || TagD");
        consumer.registerMessageListener(new MessageListenerOrderly() {
            Random random = new Random();
            @Override
            public ConsumeOrderlyStatus consumeMessage(List<MessageExt> msgs, ConsumeOrderlyContext context) {
                context.setAutoCommit(true);
                for (MessageExt msg : msgs) {
                    // 可以看到每个queue有唯一的consume线程来消费, 订单对每个queue(分区)有序
                    System.out.println("consumeThread=" + Thread.currentThread().getName() + "queueId=" + msg.getQueueId() + ", content:" + new String(msg.getBody()));
                }
                return ConsumeOrderlyStatus.SUCCESS;
            }
        });
        consumer.start();
        System.out.println("Consumer Started.");
    }
}
```





*****



### 延时消息

#### 原理解析

定时消息（延迟队列）是指消息发送到 Broker 后，不会立即被消费，等待特定时间投递给真正的 Topic

RocketMQ 并不支持任意时间的延时，需要设置几个固定的延时等级，从 1s 到 2h 分别对应着等级 1 到 18，消息消费失败会进入延时消息队列，消息发送时间与设置的延时等级和重试次数有关，详见代码 `SendMessageProcessor.java`

```java
private String messageDelayLevel = "1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h";
```

Broker 可以配置 messageDelayLevel，该属性是 Broker 的属性，不属于某个 Topic

发消息时，可以设置延迟等级 `msg.setDelayLevel(level)`，level 有以下三种情况：

- level == 0：消息为非延迟消息
- 1<=level<=maxLevel：消息延迟特定时间，例如 level==1，延迟 1s
- level > maxLevel：则 level== maxLevel，例如 level==20，延迟 2h

定时消息会暂存在名为 SCHEDULE_TOPIC_XXXX 的 Topic 中，并根据 delayTimeLevel 存入特定的 queue，队列的标识`queueId = delayTimeLevel – 1`，即一个 queue 只存相同延迟的消息，保证具有相同发送延迟的消息能够顺序消费。Broker 会调度地消费 SCHEDULE_TOPIC_XXXX，将消息写入真实的 Topic

注意：定时消息在第一次写入和调度写入真实 Topic 时都会计数，因此发送数量、tps 都会变高。



***



#### 代码实现

提交了一个订单就可以发送一个延时消息，1h 后去检查这个订单的状态，如果还是未付款就取消订单释放库存

```java
public class ScheduledMessageProducer {
    public static void main(String[] args) throws Exception {
        // 实例化一个生产者来产生延时消息
        DefaultMQProducer producer = new DefaultMQProducer("ExampleProducerGroup");
        producer.setNamesrvAddr("127.0.0.1:9876");
        // 启动生产者
        producer.start();
        int totalMessagesToSend = 100;
        for (int i = 0; i < totalMessagesToSend; i++) {
            Message message = new Message("DelayTopic", ("Hello scheduled message " + i).getBytes());
            // 设置延时等级3,这个消息将在10s之后发送(现在只支持固定的几个时间,详看delayTimeLevel)
            message.setDelayTimeLevel(3);
            // 发送消息
            producer.send(message);
        }
        // 关闭生产者
        producer.shutdown();
    }
}
```

```java
public class ScheduledMessageConsumer {
   public static void main(String[] args) throws Exception {
      // 实例化消费者
      DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("ExampleConsumer");
      consumer.setNamesrvAddr("127.0.0.1:9876");
      // 订阅Topics
      consumer.subscribe("DelayTopic", "*");
      // 注册消息监听者
      consumer.registerMessageListener(new MessageListenerConcurrently() {
          @Override
          public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> messages, ConsumeConcurrentlyContext context) {
              for (MessageExt message : messages) {
                  // 打印延迟的时间段
                  System.out.println("Receive message[msgId=" + message.getMsgId() + "] " + (System.currentTimeMillis() - message.getBornTimestamp()) + "ms later");}
              return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
          }
      });
      // 启动消费者
      consumer.start();
  }
}
```



****



### 批量消息

批量发送消息能显著提高传递小消息的性能，限制是这些批量消息应该有相同的 topic，相同的 waitStoreMsgOK，而且不能是延时消息，并且这一批消息的总大小不应超过 4MB

```java
public class Producer {

    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("ExampleProducerGroup")
        producer.setNamesrvAddr("127.0.0.1:9876");
        //启动producer
        producer.start();

        List<Message> msgs = new ArrayList<Message>();
        // 创建消息对象，指定主题Topic、Tag和消息体
        Message msg1 = new Message("BatchTopic", "Tag1", ("Hello World" + 1).getBytes());
        Message msg2 = new Message("BatchTopic", "Tag1", ("Hello World" + 2).getBytes());
        Message msg3 = new Message("BatchTopic", "Tag1", ("Hello World" + 3).getBytes());

        msgs.add(msg1);
        msgs.add(msg2);
        msgs.add(msg3);

        // 发送消息
        SendResult result = producer.send(msgs);
        System.out.println("发送结果:" + result);
        // 关闭生产者producer
        producer.shutdown();
    }
}
```

当发送大批量数据时，可能不确定消息是否超过了大小限制（4MB），所以需要将消息列表分割一下

```java
public class ListSplitter implements Iterator<List<Message>> {
    private final int SIZE_LIMIT = 1024 * 1024 * 4;
    private final List<Message> messages;
    private int currIndex;

    public ListSplitter(List<Message> messages) {
        this.messages = messages;
    }

    @Override
    public boolean hasNext() {
        return currIndex < messages.size();
    }

    @Override
    public List<Message> next() {
        int startIndex = getStartIndex();
        int nextIndex = startIndex;
        int totalSize = 0;
        for (; nextIndex < messages.size(); nextIndex++) {
            Message message = messages.get(nextIndex);
            int tmpSize = calcMessageSize(message);
            // 单个消息超过了最大的限制
            if (tmpSize + totalSize > SIZE_LIMIT) {
                break;
            } else {
                totalSize += tmpSize;
            }
        }
        List<Message> subList = messages.subList(startIndex, nextIndex);
        currIndex = nextIndex;
        return subList;
    }

    private int getStartIndex() {
        Message currMessage = messages.get(currIndex);
        int tmpSize = calcMessageSize(currMessage);
        while (tmpSize > SIZE_LIMIT) {
            currIndex += 1;
            Message message = messages.get(curIndex);
            tmpSize = calcMessageSize(message);
        }
        return currIndex;
    }

    private int calcMessageSize(Message message) {
        int tmpSize = message.getTopic().length() + message.getBody().length;
        Map<String, String> properties = message.getProperties();
        for (Map.Entry<String, String> entry : properties.entrySet()) {
            tmpSize += entry.getKey().length() + entry.getValue().length();
        }
        tmpSize = tmpSize + 20; // 增加⽇日志的开销20字节
        return tmpSize;
    }

    public static void main(String[] args) {
        //把大的消息分裂成若干个小的消息
        ListSplitter splitter = new ListSplitter(messages);
        while (splitter.hasNext()) {
            try {
                List<Message> listItem = splitter.next();
                producer.send(listItem);
            } catch (Exception e) {
                e.printStackTrace();
                //处理error
            }
        }
    }
}
```





***



### 过滤消息

#### 基本语法

RocketMQ 定义了一些基本语法来支持过滤特性，可以很容易地扩展：

- 数值比较，比如：>，>=，<，<=，BETWEEN，=
- 字符比较，比如：=，<>，IN
- IS NULL 或者 IS NOT NULL
- 逻辑符号 AND，OR，NOT

常量支持类型为：

- 数值，比如 123，3.1415
- 字符，比如 'abc'，必须用单引号包裹起来
- NULL，特殊的常量
- 布尔值，TRUE 或 FALSE

只有使用 push 模式的消费者才能用使用 SQL92 标准的 sql 语句，接口如下：

```java
public void subscribe(final String topic, final MessageSelector messageSelector)
```

例如：消费者接收包含 TAGA 或 TAGB 或 TAGC 的消息

```java
DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("CID_EXAMPLE");
consumer.subscribe("TOPIC", "TAGA || TAGB || TAGC");
```



***



#### 原理解析

RocketMQ 分布式消息队列的消息过滤方式是在 Consumer 端订阅消息时再做消息过滤的，所以是在 Broker 端实现的，优点是减少了对于 Consumer 无用消息的网络传输，缺点是增加了 Broker 的负担、而且实现相对复杂

RocketMQ 在 Producer 端写入消息和在 Consumer 端订阅消息采用**分离存储**的机制实现，Consumer 端订阅消息是需要通过 ConsumeQueue 这个消息消费的逻辑队列拿到一个索引，然后再从 CommitLog 里面读取真正的消息实体内容

ConsumeQueue 的存储结构如下，有 8 个字节存储的 Message Tag 的哈希值，基于 Tag 的消息过滤就是基于这个字段

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-消费队列结构.png)

* Tag 过滤：Consumer 端订阅消息时指定 Topic 和 TAG，然后将订阅请求构建成一个 SubscriptionData，发送一个 Pull 消息的请求给 Broker 端。Broker 端用这些数据先构建一个 MessageFilter，然后传给文件存储层 Store。Store 从 ConsumeQueue 读取到一条记录后，会用它记录的消息 tag hash 值去做过滤。因为在服务端只是根据  hashcode 进行判断，无法精确对 tag 原始字符串进行过滤，所以消费端拉取到消息后，还需要对消息的原始 tag 字符串进行比对，如果不同，则丢弃该消息，不进行消息消费

* SQL92 过滤：工作流程和 Tag 过滤大致一样，只是在 Store 层的具体过滤方式不一样。真正的 SQL expression 的构建和执行由 rocketmq-filter 模块负责，每次过滤都去执行 SQL 表达式会影响效率，所以 RocketMQ 使用了 BloomFilter 来避免了每次都去执行





****



#### 代码实现

发送消息时，通过 putUserProperty 来设置消息的属性，SQL92 的表达式上下文为消息的属性

```java
public class Producer {
    public static void main(String[] args) throws Exception {
        DefaultMQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
        producer.setNamesrvAddr("127.0.0.1:9876");
        producer.start();
        for (int i = 0; i < 10; i++) {
            Message msg = new Message("FilterTopic", "tag",
               ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
            // 设置一些属性
            msg.putUserProperty("i", String.valueOf(i));
            SendResult sendResult = producer.send(msg);
        }
        producer.shutdown();
    }
}
```

使用 SQL 筛选过滤消息：

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("please_rename_unique_group_name");
        consumer.setNamesrvAddr("127.0.0.1:9876");
        // 过滤属性大于 5  的消息
        consumer.subscribe("FilterTopic", MessageSelector.bySql("i>5"));

        // 设置回调函数，处理消息
        consumer.registerMessageListener(new MessageListenerConcurrently() {
            //接受消息内容
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> msgs, ConsumeConcurrentlyContext context) {
                for (MessageExt msg : msgs) {
                    System.out.println("consumeThread=" + Thread.currentThread().getName() + "," + new String(msg.getBody()));
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        // 启动消费者consumer
        consumer.start();
    }
}
```





***



### 事务消息

#### 工作流程

RocketMQ 支持分布式事务消息，采用了 2PC 的思想来实现了提交事务消息，同时增加一个补偿逻辑来处理二阶段超时或者失败的消息，如下图所示：

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-事务消息.png)

事务消息的大致方案分为两个流程：正常事务消息的发送及提交、事务消息的补偿流程

1. 事务消息发送及提交：

   * 发送消息（Half 消息）

   * 服务端响应消息写入结果

   * 根据发送结果执行本地事务（如果写入失败，此时 Half 消息对业务不可见，本地逻辑不执行）
   * 根据本地事务状态执行 Commit 或者 Rollback（Commit 操作生成消息索引，消息对消费者可见）

2. 补偿流程：

   * 对没有 Commit/Rollback 的事务消息（pending 状态的消息），从服务端发起一次回查
   * Producer 收到回查消息，检查回查消息对应的本地事务的状态

   * 根据本地事务状态，重新 Commit 或者 Rollback

   补偿阶段用于解决消息 Commit 或者 Rollback 发生超时或者失败的情况



****



#### 原理解析

##### 不可见性

事务消息相对普通消息最大的特点就是**一阶段发送的消息对用户是不可见的**，因为对于 Half 消息，会备份原消息的主题与消息消费队列，然后改变主题为 RMQ_SYS_TRANS_HALF_TOPIC，由于消费组未订阅该主题，故消费端无法消费 Half 类型的消息

RocketMQ 会开启一个定时任务，从 Topic 为 RMQ_SYS_TRANS_HALF_TOPIC 中拉取消息进行消费，根据生产者组获取一个服务提供者发送回查事务状态请求，根据事务状态来决定是提交或回滚消息

RocketMQ 的具体实现策略：如果写入的是事务消息，对消息的 Topic 和 Queue 等属性进行替换，同时将原来的 Topic 和 Queue 信息存储到**消息的属性**中，因为消息的主题被替换，所以消息不会转发到该原主题的消息消费队列，消费者无法感知消息的存在，不会消费



****



##### OP消息

一阶段写入不可见的消息后，二阶段操作：

* 如果执行 Commit 操作，则需要让消息对用户可见，构建出 Half 消息的索引。一阶段的 Half 消息写到一个特殊的 Topic，构建索引时需要读取出 Half 消息，然后通过一次普通消息的写入操作将 Topic 和 Queue 替换成真正的目标 Topic 和 Queue，生成一条对用户可见的消息。其实就是利用了一阶段存储的消息的内容，在二阶段时恢复出一条完整的普通消息，然后走一遍消息写入流程

* 如果是 Rollback 则需要撤销一阶段的消息，因为消息本就不可见，所以并不需要真正撤销消息（实际上 RocketMQ 也无法去删除一条消息，因为是顺序写文件的）。RocketMQ 为了区分这条消息没有确定状态的消息（Pending 状态），采用 Op 消息标识已经确定状态的事务消息（Commit 或者 Rollback）

事务消息无论是 Commit 或者 Rollback 都会记录一个 Op 操作，两者的区别是 Commit 相对于 Rollback 在写入 Op 消息前创建 Half 消息的索引。如果一条事务消息没有对应的 Op 消息，说明这个事务的状态还无法确定（可能是二阶段失败了）

RocketMQ 将 Op 消息写入到全局一个特定的 Topic 中，通过源码中的方法 `TransactionalMessageUtil.buildOpTopic()`，这个主题是一个内部的 Topic（像 Half 消息的 Topic 一样），不会被用户消费。Op 消息的内容为对应的 Half 消息的存储的 Offset，这样**通过 Op  消息能索引到 Half 消息**进行后续的回查操作

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-OP消息.png)



****



##### 补偿机制

如果在 RocketMQ 事务消息的二阶段过程中失败了，例如在做 Commit 操作时，出现网络问题导致 Commit 失败，那么需要通过一定的策略使这条消息最终被 Commit，RocketMQ 采用了一种补偿机制，称为回查

Broker 端通过对比 Half 消息和 Op 消息，对未确定状态的消息发起回查并且推进 CheckPoint（记录哪些事务消息的状态是确定的），将消息发送到对应的 Producer 端（同一个 Group 的 Producer），由 Producer 根据消息来检查本地事务的状态，然后执行提交或回滚

注意：RocketMQ 并不会无休止的进行事务状态回查，默认回查 15 次，如果 15 次回查还是无法得知事务状态，则默认回滚该消息





****



#### 基本使用

##### 使用方式

事务消息共有三种状态，提交状态、回滚状态、中间状态：

- TransactionStatus.CommitTransaction：提交事务，允许消费者消费此消息。
- TransactionStatus.RollbackTransaction：回滚事务，代表该消息将被删除，不允许被消费
- TransactionStatus.Unknown：中间状态，代表需要检查消息队列来确定状态

使用限制：

1. 事务消息不支持延时消息和批量消息
2. Broker 配置文件中的参数 `transactionTimeout` 为特定时间，事务消息将在特定时间长度之后被检查。当发送事务消息时，还可以通过设置用户属性 `CHECK_IMMUNITY_TIME_IN_SECONDS` 来改变这个限制，该参数优先于 `transactionTimeout` 参数
3. 为了避免单个消息被检查太多次而导致半队列消息累积，默认将单个消息的检查次数限制为 15 次，开发者可以通过 Broker 配置文件的 `transactionCheckMax` 参数来修改此限制。如果已经检查某条消息超过 N 次（N = `transactionCheckMax`）， 则 Broker 将丢弃此消息，在默认情况下会打印错误日志。可以通过重写 `AbstractTransactionalMessageCheckListener` 类来修改这个行为
4. 事务性消息可能不止一次被检查或消费
5. 提交给用户的目标主题消息可能会失败，可以查看日志的记录。事务的高可用性通过 RocketMQ 本身的高可用性机制来保证，如果希望事务消息不丢失、并且事务完整性得到保证，可以使用同步的双重写入机制
6. 事务消息的生产者 ID 不能与其他类型消息的生产者 ID 共享。与其他类型的消息不同，事务消息允许反向查询，MQ 服务器能通过消息的生产者 ID 查询到消费者



***



##### 代码实现

实现事务的监听接口，当发送半消息成功时：

* `executeLocalTransaction` 方法来执行本地事务，返回三个事务状态之一
* `checkLocalTransaction` 方法检查本地事务状态，响应消息队列的检查请求，返回三个事务状态之一

```java
public class TransactionListenerImpl implements TransactionListener {
    private AtomicInteger transactionIndex = new AtomicInteger(0);
    private ConcurrentHashMap<String, Integer> localTrans = new ConcurrentHashMap<>();

    @Override
    public LocalTransactionState executeLocalTransaction(Message msg, Object arg) {
        int value = transactionIndex.getAndIncrement();
        int status = value % 3;
        // 将事务ID和状态存入 map 集合
        localTrans.put(msg.getTransactionId(), status);
        return LocalTransactionState.UNKNOW;
    }

    @Override
    public LocalTransactionState checkLocalTransaction(MessageExt msg) {
        // 从 map 集合读出当前事务对应的状态
        Integer status = localTrans.get(msg.getTransactionId());
        if (null != status) {
            switch (status) {
                case 0:
                    return LocalTransactionState.UNKNOW;
                case 1:
                    return LocalTransactionState.COMMIT_MESSAGE;
                case 2:
                    return LocalTransactionState.ROLLBACK_MESSAGE;
            }
        }
        return LocalTransactionState.COMMIT_MESSAGE;
    }
}
```

使用 **TransactionMQProducer** 类创建事务性生产者，并指定唯一的 `ProducerGroup`，就可以设置自定义线程池来处理这些检查请求，执行本地事务后、需要根据执行结果对消息队列进行回复

```java
public class Producer {
	public static void main(String[] args) throws MQClientException, InterruptedException {
        // 创建消息生产者
       	TransactionMQProducer producer = new TransactionMQProducer("please_rename_unique_group_name");
       	ExecutorService executorService = new ThreadPoolExecutor(2, 5, 100, TimeUnit.SECONDS);
        producer.setExecutorService(executorService);
        
        // 创建事务监听器
		TransactionListener transactionListener = new TransactionListenerImpl();
        // 生产者的监听器
        producer.setTransactionListener(transactionListener);
       	// 启动生产者
        producer.start();
        String[] tags = new String[] {"TagA", "TagB", "TagC", "TagD", "TagE"};
        for (int i = 0; i < 10; i++) {
            try {
                Message msg = new Message("TransactionTopic", tags[i % tags.length], "KEY" + i,
                                ("Hello RocketMQ " + i).getBytes(RemotingHelper.DEFAULT_CHARSET));
                // 发送消息
                SendResult sendResult = producer.sendMessageInTransaction(msg, null);
                System.out.printf("%s%n", sendResult);
                Thread.sleep(10);
            } catch (MQClientException | UnsupportedEncodingException e) {
                e.printStackTrace();
            }
        }
       	//Thread.sleep(1000000);
        //producer.shutdown();暂时不关闭
    }
}
```

消费者代码和前面的实例相同的





****





## 系统特性

### 工作机制

#### 模块介绍

NameServer 是一个简单的 Topic 路由注册中心，支持 Broker 的动态注册与发现，生产者或消费者能够通过名字服务查找各主题相应的 Broker IP 列表

NameServer 主要包括两个功能：

* Broker 管理，NameServer 接受 Broker 集群的注册信息并保存下来作为路由信息的基本数据，提供**心跳检测**检查 Broker 活性
* 路由信息管理，每个 NameServer 将保存关于 Broker 集群的整个路由信息和用于客户端查询的队列信息，然后 Producer 和 Conumser 通过 NameServer 就可以知道整个 Broker 集群的路由信息，从而进行消息的投递和消费

NameServer 特点：

* NameServer 通常是集群的方式部署，各实例间相互不进行信息通讯
* Broker 向每一台 NameServer 注册自己的路由信息，所以每个 NameServer 实例上面**都保存一份完整的路由信息**
* 当某个 NameServer 因某种原因下线了，Broker 仍可以向其它 NameServer 同步其路由信息

BrokerServer 主要负责消息的存储、投递和查询以及服务高可用保证，在 RocketMQ 系统中接收从生产者发送来的消息并存储、同时为消费者的拉取请求作准备，也存储消息相关的元数据，包括消费者组、消费进度偏移和主题和队列消息等

Broker 包含了以下几个重要子模块：

* Remoting Module：整个 Broker 的实体，负责处理来自 clients 端的请求

* Client Manager：负责管理客户端（Producer/Consumer）和维护 Consumer 的 Topic 订阅信息

* Store Service：提供方便简单的 API 接口处理消息存储到物理硬盘和查询功能

* HA Service：高可用服务，提供 Master Broker 和 Slave Broker 之间的数据同步功能

* Index Service：根据特定的 Message key 对投递到 Broker 的消息进行索引服务，以提供消息的快速查询

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-Broker工作流程.png)



***



#### 工作流程

RocketMQ 的工作流程：

- 启动 NameServer 监听端口，等待 Broker、Producer、Consumer 连上来，相当于一个路由控制中心
- Broker 启动，跟所有的 NameServer 保持长连接，每隔 30s 时间向 NameServer 上报 Topic 路由信息（心跳包）。心跳包中包含当前 Broker 信息（IP、端口等）以及存储所有 Topic 信息。注册成功后，NameServer 集群中就有 Topic 跟 Broker 的映射关系
- 收发消息前，先创建 Topic，创建 Topic 时需要指定该 Topic 要存储在哪些 Broker 上，也可以在发送消息时自动创建 Topic
- Producer 启动时先跟 NameServer 集群中的**其中一台**建立长连接，并从 NameServer 中获取当前发送的 Topic 存在哪些 Broker 上，同时 Producer 会默认每隔 30s 向 NameServer 拉取一次路由信息
- Producer 发送消息时，根据消息的 Topic 从本地缓存的 TopicPublishInfoTable 获取路由信息，如果没有则会从 NameServer 上重新拉取并更新，轮询队列列表并选择一个队列 MessageQueue，然后与队列所在的 Broker 建立长连接，向 Broker 发消息
- Consumer 跟 Producer 类似，跟其中一台 NameServer 建立长连接获取路由信息，根据当前订阅 Topic 存在哪些 Broker 上，直接跟 Broker 建立连接通道，在完成客户端的负载均衡后，选择其中的某一个或者某几个 MessageQueue 来拉取消息并进行消费



****



#### 协议设计

在 Client 和 Server 之间完成一次消息发送时，需要对发送的消息进行一个协议约定，所以自定义 RocketMQ 的消息协议。为了高效地在网络中传输消息和对收到的消息读取，就需要对消息进行编解码。在 RocketMQ 中，RemotingCommand 这个类在消息传输过程中对所有数据内容的封装，不但包含了所有的数据结构，还包含了编码解码操作

| Header字段 | 类型                    | Request 说明                                                 | Response 说明                               |
| ---------- | ----------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| code       | int                     | 请求操作码，应答方根据不同的请求码进行不同的处理             | 应答响应码，0 表示成功，非 0 则表示各种错误 |
| language   | LanguageCode            | 请求方实现的语言                                             | 应答方实现的语言                            |
| version    | int                     | 请求方程序的版本                                             | 应答方程序的版本                            |
| opaque     | int                     | 相当于 requestId，在同一个连接上的不同请求标识码，与响应消息中的相对应 | 应答不做修改直接返回                        |
| flag       | int                     | 区分是普通 RPC 还是 onewayRPC 的标志                         | 区分是普通 RPC 还是 onewayRPC的标志         |
| remark     | String                  | 传输自定义文本信息                                           | 传输自定义文本信息                          |
| extFields  | HashMap<String, String> | 请求自定义扩展信息                                           | 响应自定义扩展信息                          |

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-消息协议.png)

传输内容主要可以分为以下四部分：

* 消息长度：总长度，四个字节存储，占用一个 int 类型

* 序列化类型&消息头长度：同样占用一个 int 类型，第一个字节表示序列化类型，后面三个字节表示消息头长度

* 消息头数据：经过序列化后的消息头数据

* 消息主体数据：消息主体的二进制字节数据内容



*****



#### 通信原理

==todo：后期学习了源码会进行扩充，现在暂时 copy 官方文档==

在 RocketMQ 消息队列中支持通信的方式主要有同步（sync）、异步（async）、单向（oneway）三种，其中单向通信模式相对简单，一般用在发送心跳包场景下，无需关注其 Response

RocketMQ 的异步通信流程：

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-异步通信流程.png)

RocketMQ 的 RPC 通信采用 Netty 组件作为底层通信库，同样也遵循了 Reactor 多线程模型，同时又在这之上做了一些扩展和优化

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-Reactor设计.png)

RocketMQ 基于 NettyRemotingServer 的 Reactor 多线程模型：

* 一个 Reactor 主线程（eventLoopGroupBoss）负责监听 TCP 网络连接请求，建立好连接，创建 SocketChannel，并注册到 selector 上。RocketMQ 会自动根据 OS 的类型选择 NIO 和 Epoll，也可以通过参数配置），然后监听真正的网络数据

* 拿到网络数据交给 Worker 线程池（eventLoopGroupSelector，默认设置为 3），在真正执行业务逻辑之前需要进行 SSL 验证、编解码、空闲检查、网络连接管理，这些工作交给 defaultEventExecutorGroup（默认设置为 8）去做
* 处理业务操作放在业务线程池中执行，根据 RomotingCommand 的业务请求码 code 去 processorTable 这个本地缓存变量中找到对应的 processor，封装成 task 任务提交给对应的业务 processor 处理线程池来执行（sendMessageExecutor，以发送消息为例）
* 从入口到业务逻辑的几个步骤中线程池一直再增加，这跟每一步逻辑复杂性相关，越复杂，需要的并发通道越宽

| 线程数 | 线程名                         | 线程具体说明              |
| ------ | ------------------------------ | ------------------------- |
| 1      | NettyBoss_%d                   | Reactor 主线程            |
| N      | NettyServerEPOLLSelector_%d_%d | Reactor 线程池            |
| M1     | NettyServerCodecThread_%d      | Worker 线程池             |
| M2     | RemotingExecutorThread_%d      | 业务 processor 处理线程池 |



官方文档：https://github.com/apache/rocketmq/blob/master/docs/cn/design.md#2-%E9%80%9A%E4%BF%A1%E6%9C%BA%E5%88%B6





***



### 消息存储

#### 生产消费

At least Once：至少一次，指每个消息必须投递一次，Consumer 先 Pull 消息到本地，消费完成后才向服务器返回 ACK，如果没有消费一定不会 ACK 消息

回溯消费：指 Consumer 已经消费成功的消息，由于业务上需求需要重新消费，Broker 在向 Consumer 投递成功消息后，消息仍然需要保留。并且重新消费一般是按照时间维度，例如由于 Consumer 系统故障，恢复后需要重新消费 1 小时前的数据，RocketMQ 支持按照时间回溯消费，时间维度精确到毫秒

分布式队列因为有高可靠性的要求，所以数据要进行持久化存储

1. 消息生产者发送消息
2. MQ 收到消息，将消息进行持久化，在存储中新增一条记录
3. 返回 ACK 给生产者
4. MQ push 消息给对应的消费者，然后等待消费者返回 ACK
5. 如果消息消费者在指定时间内成功返回 ACK，那么 MQ 认为消息消费成功，在存储中删除消息；如果 MQ 在指定时间内没有收到 ACK，则认为消息消费失败，会尝试重新 push 消息，重复执行 4、5、6 步骤
6. MQ 删除消息

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-消息存取.png)





***



#### 存储结构

Broker 负责存储消息转发消息，所以以下的结构是存储在 Broker Server 上的

RocketMQ 消息的存储是由 ConsumeQueue 和 CommitLog 配合完成 的，消息真正的物理存储文件是 CommitLog，ConsumeQueue 是消息的逻辑队列，类似数据库的索引节点，存储的是指向物理存储的地址。**每个 Topic 下的每个 Message Queue 都有一个对应的 ConsumeQueue 文件**

每条消息都会有对应的索引信息，Consumer 通过 ConsumeQueue 这个结构来读取消息实体内容

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-消息存储结构.png)

* CommitLog：消息主体以及元数据的存储主体，存储 Producer 端写入的消息内容，消息内容不是定长的。消息主要是顺序写入日志文件，单个文件大小默认1G，偏移量代表下一次写入的位置，当文件写满了就继续写入下一个文件
* ConsumerQueue：消息消费队列，存储消息在 CommitLog 的索引。RocketMQ 消息消费时要遍历 CommitLog 文件，并根据主题 Topic 检索消息，这是非常低效的。引入 ConsumeQueue 作为消费消息的索引，保存了指定 Topic 下的队列消息在 CommitLog 中的起始物理偏移量 offset，消息大小 size 和消息 Tag 的 HashCode 值，每个 ConsumeQueue 文件大小约 5.72M
* IndexFile：为了消息查询提供了一种通过 Key 或时间区间来查询消息的方法，通过 IndexFile 来查找消息的方法不影响发送与消费消息的主流程。IndexFile 的底层存储为在文件系统中实现的 HashMap 结构，故 RocketMQ 的索引文件其底层实现为 hash 索引

RocketMQ 采用的是混合型的存储结构，即为 Broker 单个实例下所有的队列共用一个日志数据文件（CommitLog）来存储。混合型存储结构（多个 Topic 的消息实体内容都存储于一个 CommitLog 中）**针对 Producer 和 Consumer 分别采用了数据和索引部分相分离的存储结构**，Producer 发送消息至 Broker 端，然后 Broker 端使用同步或者异步的方式对消息刷盘持久化，保存至 CommitLog 中。只要消息被持久化至磁盘文件 CommitLog 中，Producer 发送的消息就不会丢失，Consumer 也就肯定有机会去消费这条消息

服务端支持长轮询模式，当消费者无法拉取到消息后，可以等下一次消息拉取，Broker 允许等待 30s 的时间，只要这段时间内有新消息到达，将直接返回给消费端。RocketMQ 的具体做法是，使用 Broker 端的后台服务线程 ReputMessageService 不停地分发请求并异步构建 ConsumeQueue（逻辑消费队列）和 IndexFile（索引文件）数据



****



#### 存储优化

##### 存储媒介

两种持久化的方案：

* 关系型数据库 DB：IO 读写性能比较差，如果 DB 出现故障，则 MQ 的消息就无法落盘存储导致线上故障，可靠性不高

* 文件系统：消息刷盘至所部署虚拟机/物理机的文件系统来做持久化，分为异步刷盘和同步刷盘两种模式。消息刷盘为消息存储提供了一种高效率、高可靠性和高性能的数据持久化方式，除非部署 MQ 机器本身或是本地磁盘挂了，一般不会出现无法持久化的问题

  注意：磁盘的顺序读写要比随机读写快很多，可以匹配上网络的速度，RocketMQ 的消息采用的顺序写

页缓存（PageCache）是 OS 对文件的缓存，用于加速对文件的读写。程序对文件进行顺序读写的速度几乎接近于内存的读写速度，就是因为 OS 将一部分的内存用作 PageCache，对读写访问操作进行了性能优化

* 对于数据的写入，OS 会先写入至 Cache 内，随后通过异步的方式由 pdflush 内核线程将 Cache 内的数据刷盘至物理磁盘上
* 对于数据的读取，如果一次读取文件时出现未命中 PageCache 的情况，OS 从物理磁盘上访问读取文件的同时，会顺序对其他相邻块的数据文件进行预读取（局部性原理）

在 RocketMQ 中，ConsumeQueue 逻辑消费队列存储的数据较少，并且是顺序读取，在 PageCache 机制的预读取作用下，Consume Queue 文件的读性能几乎接近读内存，即使在有消息堆积情况下也不会影响性能。CommitLog 消息存储的日志数据文件读取内容时会产生较多的随机访问读取，严重影响性能。选择合适的系统 IO 调度算法和固态硬盘，比如设置调度算法为 Deadline，随机读的性能也会有所提升



***



##### 内存映射

操作系统分为用户态和内核态，文件操作、网络操作需要涉及这两种形态的切换，需要进行数据复制。一台服务器把本机磁盘文件的内容发送到客户端，分为两个步骤：

* read：读取本地文件内容

* write：将读取的内容通过网络发送出去

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-文件与网络操作.png)

补充：Prog → NET → I/O → 零拷贝部分的笔记详解相关内容

通过使用 mmap 的方式，可以省去向用户态的内存复制，RocketMQ 充分利用零拷贝技术，提高消息存盘和网络发送的速度。

RocketMQ 主要通过 MappedByteBuffer 对文件进行读写操作，利用了 NIO 中的 FileChannel 模型将磁盘上的物理文件直接映射到用户态的内存地址中，将对文件的操作转化为直接对内存地址进行操作，从而极大地提高了文件的读写效率

MappedByteBuffer 内存映射的方式限制一次只能映射 1.5~2G 的文件至用户态的虚拟内存，所以 RocketMQ 默认设置单个 CommitLog 日志数据文件为 1G。RocketMQ 的文件存储使用定长结构来存储，方便一次将整个文件映射至内存



***



#### 刷盘机制

同步刷盘：只有在消息真正持久化至磁盘后 RocketMQ 的 Broker 端才会真正返回给 Producer 端一个成功的 ACK 响应，保障 MQ消息的可靠性，但是性能上会有较大影响，一般适用于金融业务应用该模式较多

异步刷盘：利用 OS 的 PageCache 的优势，只要消息写入内存 PageCache 即可将成功的 ACK 返回给 Producer 端，降低了读写延迟，提高了 MQ 的性能和吞吐量。消息刷盘采用**后台异步线程**提交的方式进行，当内存里的消息量积累到一定程度时，触发写磁盘动作

通过 Broker 配置文件里的 flushDiskType 参数设置采用什么方式，可以配置成 SYNC_FLUSH、ASYNC_FLUSH 中的一个

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-刷盘机制.png)



官方文档：https://github.com/apache/rocketmq/blob/master/docs/cn/design.md





****



### 消息查询

#### Message ID

RocketMQ 支持按照两种维度进行消息查询：按照 Message ID 查询消息、按照 Message Key 查询消息

RocketMQ 中的 MessageId 的长度总共有 16 字节，其中包含了消息存储主机地址（IP 地址和端口），消息 Commit Log offset

实现方式：Client 端从 MessageId 中解析出 Broker 的地址（IP 地址和端口）和 Commit Log 的偏移地址，封装成一个 RPC 请求后通过 Remoting 通信层发送（业务请求码 VIEW_MESSAGE_BY_ID）。Broker 端走的是 QueryMessageProcessor，读取消息的过程用其中的 CommitLog 的 offset 和 size 去 CommitLog 中找到真正的记录并解析成一个完整的消息返回



***



#### Message Key 

按照 Message Key 查询消息，主要是基于 RocketMQ 的 IndexFile 索引文件来实现的，RocketMQ 的索引文件逻辑结构，类似 JDK 中 HashMap 的实现，具体结构如下：

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-IndexFile索引文件.png)

IndexFile 索引文件为提供了通过 Message Key 查询消息的服务，IndexFile 文件的存储在 `$HOME\store\index${fileName}`，文件名 fileName 是以创建时的时间戳命名，文件大小是固定的，等于 `40+500W*4+2000W*20= 420000040` 个字节大小。如果消息的 properties 中设置了 UNIQ_KEY 这个属性，就用 `topic + “#” + UNIQ_KEY` 作为 key 来做写入操作；如果消息设置了 KEYS 属性（多个 KEY 以空格分隔），也会用 `topic + “#” + KEY` 来做索引

整个 Index File 的结构如图，40 Byte 的 Header 用于保存一些总的统计信息，`4*500W` 的 Slot Table 并不保存真正的索引数据，而是保存每个槽位对应的单向链表的头，即一个 Index File 可以保存 2000W 个索引，`20*2000W` 是真正的索引数据

索引数据包含了 Key Hash/CommitLog Offset/Timestamp/NextIndex offset 这四个字段，一共 20 Byte

* NextIndex offset 即前面读出来的 slotValue，如果有 hash 冲突，就可以用这个字段将所有冲突的索引用链表的方式串起来
* Timestamp 记录的是消息 storeTimestamp 之间的差，并不是一个绝对的时间

实现方式：通过 Broker 端的 QueryMessageProcessor 业务处理器来查询，读取消息的过程用 Topic 和 Key 找到 IndexFile 索引文件中的一条记录，根据其中的 CommitLog Offset 从 CommitLog 文件中读取消息的实体内容





***



### 集群设计

#### 集群模式

常用的以下几种模式：

* 单 Master 模式：这种方式风险较大，一旦 Broker 重启或者宕机，会导致整个服务不可用
* 多 Master 模式：一个集群无 Slave，全是 Master

  - 优点：配置简单，单个 Master 宕机或重启维护对应用无影响，在磁盘配置为 RAID10 时，即使机器宕机不可恢复情况下，由于 RAID10 磁盘非常可靠，消息也不会丢（异步刷盘丢失少量消息，同步刷盘一条不丢），性能最高

  - 缺点：单台机器宕机期间，这台机器上未被消费的消息在机器恢复之前不可订阅，消息实时性会受到影响
* 多 Master 多 Slave 模式（同步）：每个 Master 配置一个 Slave，有多对 Master-Slave，HA 采用同步双写方式，即只有主备都写成功，才向应用返回成功

  * 优点：数据与服务都无单点故障，Master 宕机情况下，消息无延迟，服务可用性与数据可用性都非常高
  * 缺点：性能比异步复制略低（大约低 10% 左右），发送单个消息的 RT 略高，目前不能实现主节点宕机，备机自动切换为主机
* 多 Master 多 Slave 模式（异步）：HA 采用异步复制的方式，会造成主备有短暂的消息延迟（毫秒级别）

  - 优点：即使磁盘损坏，消息丢失的非常少，且消息实时性不会受影响，同时 Master 宕机后，消费者仍然可以从 Slave 消费，而且此过程对应用透明，不需要人工干预，性能同多 Master 模式几乎一样
  - 缺点：Master 宕机，磁盘损坏情况下会丢失少量消息



***



#### 集群架构

RocketMQ 网络部署特点：

- NameServer 是一个几乎**无状态节点**，节点之间相互独立，无任何信息同步

- Broker 部署相对复杂，Broker 分为 Master 与 Slave，Master 可以部署多个，一个 Master 可以对应多个 Slave，但是一个 Slave 只能对应一个 Master，Master 与 Slave 的对应关系通过指定相同 BrokerName、不同 BrokerId 来定义，BrokerId 为 0 是 Master，非 0 表示 Slave。**每个 Broker 与 NameServer 集群中的所有节点建立长连接**，定时注册 Topic 信息到所有 NameServer

  注意：部署架构上也支持一 Master 多 Slave，但只有 BrokerId=1 的从服务器才会参与消息的读负载（读写分离）

- Producer 与 NameServer 集群中的其中**一个节点（随机选择）建立长连接**，定期从 NameServer 获取 Topic 路由信息，并向提供 Topic 服务的 Master 建立长连接，且定时向 Master **发送心跳**。Producer 完全无状态，可集群部署

- Consumer 与 NameServer 集群中的其中一个节点（随机选择）建立长连接，定期从 NameServer 获取 Topic 路由信息，并向提供  Topic 服务的 Master、Slave 建立长连接，且定时向 Master、Slave 发送心跳

  Consumer 既可以从 Master 订阅消息，也可以从 Slave 订阅消息，在向 Master 拉取消息时，Master 服务器会根据拉取偏移量与最大偏移量的距离（判断是否读老消息，产生读 I/O），以及从服务器是否可读等因素建议下一次是从 Master 还是 Slave 拉取

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-集群架构.png)

集群工作流程：参考通信机制 → 工作流程



官方文档：https://github.com/apache/rocketmq/blob/master/docs/cn/architecture.md



****



#### 高可用

在 Consumer 的配置文件中，并不需要设置是从 Master 读还是从 Slave 读，当 Master 不可用或者繁忙的时候，Consumer 会被自动切换到从 Slave 读。有了自动切换的机制，当一个 Master 机器出现故障后，Consumer 仍然可以从 Slave 读取消息，不影响 Consumer 程序，达到了消费端的高可用性

在创建 Topic 的时候，把 Topic 的多个 Message Queue 创建在多个 Broker 组上（相同 Broker 名称，不同 brokerId 的机器组成一个 Broker 组），当一个 Broker 组的 Master 不可用后，其他组的 Master 仍然可用，Producer 仍然可以发送消息。

RocketMQ 目前还不支持把 Slave 自动转成 Master，需要手动停止 Slave 角色的 Broker，更改配置文件，用新的配置文件启动 Broker

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-高可用.png)



****



#### 主从复制

如果一个 Broker 组有 Master 和 Slave，消息需要从 Master 复制到 Slave 上，有同步和异步两种复制方式：

* 同步复制方式：Master 和 Slave 均写成功后才反馈给客户端写成功状态。在同步复制方式下，如果 Master 出故障， Slave 上有全部的备份数据，容易恢复，但是同步复制会增大数据写入延迟，降低系统吞吐量

* 异步复制方式：只要 Master 写成功，即可反馈给客户端写成功状态，系统拥有较低的延迟和较高的吞吐量，但是如果 Master 出了故障，有些数据因为没有被写入 Slave，有可能会丢失

同步复制和异步复制是通过 Broker 配置文件里的 brokerRole 参数进行设置的，可以设置成 ASYNC_MASTE、RSYNC_MASTER、SLAVE 三个值中的一个

一般把刷盘机制配置成 ASYNC_FLUSH，主从复制为 SYNC_MASTER，这样即使有一台机器出故障，仍然能保证数据不丢

RocketMQ 支持消息的高可靠，影响消息可靠性的几种情况：

1. Broker 非正常关闭
2. Broker 异常 Crash
3. OS Crash
4. 机器掉电，但是能立即恢复供电情况
5. 机器无法开机（可能是 CPU、主板、内存等关键设备损坏）
6. 磁盘设备损坏

前四种情况都属于硬件资源可立即恢复情况，RocketMQ 在这四种情况下能保证消息不丢，或者丢失少量数据（依赖刷盘方式）

后两种属于单点故障，且无法恢复，一旦发生，在此单点上的消息全部丢失。RocketMQ 在这两种情况下，通过主从异步复制，可保证 99% 的消息不丢，但是仍然会有极少量的消息可能丢失。通过**同步双写技术**可以完全避免单点，但是会影响性能，适合对消息可靠性要求极高的场合，RocketMQ 从 3.0 版本开始支持同步双写



****



### 负载均衡

#### 生产端

RocketMQ 中的负载均衡可以分为 Producer 端发送消息时候的负载均衡和 Consumer 端订阅消息的负载均衡

Producer 端在发送消息时，会先根据 Topic 找到指定的 TopicPublishInfo，在获取了 TopicPublishInfo 路由信息后，RocketMQ 的客户端在默认方式调用 selectOneMessageQueue() 方法从 TopicPublishInfo 中的 messageQueueList 中选择一个队列 MessageQueue 进行发送消息

默认会轮询所有的 Message Queue 发送，以让消息平均落在不同的 queue 上，而由于 queue可以散落在不同的 Broker，所以消息就发送到不同的 Broker 下，图中箭头线条上的标号代表顺序，发布方会把第一条消息发送至 Queue 0，然后第二条消息发送至 Queue 1，以此类推：

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-producer负载均衡.png)

容错策略均在 MQFaultStrategy 这个类中定义，有一个 sendLatencyFaultEnable 开关变量：

* 如果开启，会在随机递增取模的基础上，再过滤掉 not available 的 Broker 代理
* 如果关闭，采用随机递增取模的方式选择一个队列（MessageQueue）来发送消息

latencyFaultTolerance 机制是实现消息发送高可用的核心关键所在，对之前失败的，按一定的时间做退避。例如上次请求的 latency 超过 550Lms，就退避 3000Lms；超过 1000L，就退避 60000L



***



#### 消费端

在 RocketMQ 中，Consumer 端的两种消费模式（Push/Pull）都是基于拉模式来获取消息的，而在 Push 模式只是对 Pull 模式的一种封装，其本质实现为消息拉取线程在从服务器拉取到一批消息，提交到消息消费线程池后，又继续向服务器再次尝试拉取消息，如果未拉取到消息，则延迟一下又继续拉取

在两种基于拉模式的消费方式（Push/Pull）中，均需要 Consumer 端在知道从 Broker 端的哪一个消息队列—队列中去获取消息，所以在 Consumer 端来做负载均衡，即 Broker 端中多个 MessageQueue 分配给同一个 ConsumerGroup 中的哪些 Consumer 消费

* 广播模式下要求一条消息需要投递到一个消费组下面所有的消费者实例，所以不存在负载均衡，在实现上，Consumer 分配 queue 时，所有 Consumer 都分到所有的queue。

* 在集群消费模式下，每条消息只需要投递到订阅这个 Topic 的 Consumer Group 下的一个实例即可，RocketMQ 采用主动拉取的方式拉取并消费消息，在拉取的时候需要明确指定拉取哪一条 Message Queue

集群模式下，每当实例的数量有变更，都会触发一次所有实例的负载均衡，这时候会按照 queue 的数量和实例的数量平均分配 queue 给每个实例。默认的分配算法是 AllocateMessageQueueAveragely：

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-consumer负载均衡1.png)

  还有一种平均的算法是 AllocateMessageQueueAveragelyByCircle，以环状轮流均分 queue 的形式：

![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-consumer负载均衡2.png)

集群模式下，queue 都是只允许分配只一个实例，如果多个实例同时消费一个 queue 的消息，由于拉取哪些消息是 Consumer 主动控制的，会导致同一个消息在不同的实例下被消费多次

通过增加 Consumer 实例去分摊 queue 的消费，可以起到水平扩展的消费能力的作用。而当有实例下线时，会重新触发负载均衡，这时候原来分配到的 queue 将分配到其他实例上继续消费。但是如果 Consumer 实例的数量比 Message Queue 的总数量还多的话，多出来的 Consumer 实例将无法分到 queue，也就无法消费到消息，也就无法起到分摊负载的作用了，所以需要控制让 queue 的总数量大于等于 Consumer 的数量



***



#### 原理解析

==todo：暂时 copy 官方文档，学习源码后更新，建议粗略看一下，真想搞懂过程还需要研究一下源码==

在 Consumer 启动后，会通过定时任务不断地向 RocketMQ 集群中的所有 Broker 实例发送心跳包。Broke r端在收到 Consumer 的心跳消息后，会将它维护 在ConsumerManager 的本地缓存变量 consumerTable，同时并将封装后的客户端网络通道信息保存在本地缓存变量 channelInfoTable 中，为 Consumer 端的负载均衡提供可以依据的元数据信息

Consumer 端实现负载均衡的核心类 **RebalanceImpl**

在 Consumer 实例的启动流程中的启动 MQClientInstance 实例部分，会完成负载均衡服务线程 RebalanceService 的启动（每隔 20s 执行一次），RebalanceService 线程的 run() 方法最终调用的是 RebalanceImpl 类的 rebalanceByTopic() 方法，该方法是实现 Consumer 端负载均衡的核心。rebalanceByTopic() 方法会根据消费者通信类型为广播模式还是集群模式做不同的逻辑处理。这里主要看下集群模式下的处理流程：

* 从 rebalanceImpl 实例的本地缓存变量 topicSubscribeInfoTable 中，获取该 Topic 主题下的消息消费队列集合 mqSet

* 根据 Topic 和 consumerGroup 为参数调用 `mQClientFactory.findConsumerIdList()` 方法向 Broker 端发送获取该消费组下消费者 ID 列表的 RPC 通信请求（Broker 端基于前面 Consumer 端上报的心跳包数据而构建的 consumerTable 做出响应返回，业务请求码 `GET_CONSUMER_LIST_BY_GROUP`）

* 先对 Topic 下的消息消费队列、消费者 ID 排序，然后用消息队列分配策略算法（默认是消息队列的平均分配算法），计算出待拉取的消息队列。平均分配算法类似于分页的算法，将所有 MessageQueue 排好序类似于记录，将所有消费端 Consumer 排好序类似页数，并求出每一页需要包含的平均 size 和每个页面记录的范围 range，最后遍历整个 range 而计算出当前 Consumer 端应该分配到的记录（这里即为 MessageQueue）

  ![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-负载均衡平均分配算法.png)

* 调用 updateProcessQueueTableInRebalance() 方法，先将分配到的消息队列集合 mqSet 与 processQueueTable 做一个过滤比对

  ![](https://gitee.com/seazean/images/raw/master/Frame/RocketMQ-负载均衡重新平衡算法.png)

* processQueueTable 标注的红色部分，表示与分配到的消息队列集合 mqSet 互不包含，将这些队列设置 Dropped 属性为 true，然后查看这些队列是否可以移除出 processQueueTable 缓存变量。具体执行 removeUnnecessaryMessageQueue() 方法，即每隔 1s  查看是否可以获取当前消费处理队列的锁，拿到的话返回 true；如果等待 1s 后，仍然拿不到当前消费处理队列的锁则返回 false。如果返回 true，则从 processQueueTable 缓存变量中移除对应的 Entry

* processQueueTable 的绿色部分，表示与分配到的消息队列集合 mqSet 的交集，判断该 ProcessQueue 是否已经过期了，在 Pull 模式的不用管，如果是 Push 模式的，设置 Dropped 属性为 true，并且调用 removeUnnecessaryMessageQueue() 方法，像上面一样尝试移除 Entry

* 为过滤后的消息队列集合 mqSet 中每个 MessageQueue 创建 ProcessQueue 对象存入 RebalanceImpl 的 processQueueTable 队列中（其中调用 RebalanceImpl 实例的 `computePullFromWhere(MessageQueue mq)` 方法获取该 MessageQueue 对象的下一个进度消费值 offset，随后填充至接下来要创建的 pullRequest 对象属性中），并创建拉取请求对象 pullRequest 添加到拉取列表 pullRequestList 中，最后执行 dispatchPullRequest() 方法，将 Pull 消息的请求对象 PullRequest 依次放入 PullMessageService 服务线程的阻塞队列 pullRequestQueue 中，待该服务线程取出后向 Broker 端发起 Pull 消息的请求。

  对比下 RebalancePushImpl 和 RebalancePullImpl 两个实现类的 dispatchPullRequest() 方法，RebalancePullImpl 类里面的该方法为空

消息消费队列在同一消费组不同消费者之间的负载均衡，其核心设计理念是在一个消息消费队列在同一时间只允许被同一消费组内的一个消费者消费，一个消息消费者能同时消费多个消息队列



****



### 消息重试

#### 重试机制

Consumer 消费消息失败后，提供了一种重试机制，令消息再消费一次。Consumer 消费消息失败可以认为有以下几种情况：

- 由于消息本身的原因，例如反序列化失败，消息数据本身无法处理等。这种错误通常需要跳过这条消息，再消费其它消息，而这条失败的消息即使立刻重试消费，99% 也不成功，所以需要提供一种定时重试机制，即过 10秒 后再重试
- 由于依赖的下游应用服务不可用，例如 DB 连接不可用，外系统网络不可达等。这种情况即使跳过当前失败的消息，消费其他消息同样也会报错，这种情况建议应用 sleep 30s，再消费下一条消息，这样可以减轻 Broker 重试消息的压力

RocketMQ 会为每个消费组都设置一个 Topic 名称为 `%RETRY%+consumerGroup` 的重试队列（这个 Topic 的重试队列是针对消费组，而不是针对每个 Topic 设置的），用于暂时保存因为各种异常而导致 Consumer 端无法消费的消息

* 顺序消息的重试，当消费者消费消息失败后，消息队列 RocketMQ 会自动不断进行消息重试（每次间隔时间为 1 秒），这时应用会出现消息消费被阻塞的情况。所以在使用顺序消息时，必须保证应用能够及时监控并处理消费失败的情况，避免阻塞现象的发生

* 无序消息（普通、定时、延时、事务消息）的重试，可以通过设置返回状态达到消息重试的结果。无序消息的重试只针对集群消费方式生效，广播方式不提供失败重试特性，即消费失败后，失败消息不再重试，继续消费新的消息

**无序消息情况下**，因为异常恢复需要一些时间，会为重试队列设置多个重试级别，每个重试级别都有对应的重新投递延时，重试次数越多投递延时就越大。RocketMQ 对于重试消息的处理是先保存至 Topic 名称为 `SCHEDULE_TOPIC_XXXX` 的延迟队列中，后台定时任务按照对应的时间进行 Delay 后重新保存至 `%RETRY%+consumerGroup` 的重试队列中

消息队列 RocketMQ 默认允许每条消息最多重试 16 次，每次重试的间隔时间如下：

| 第几次重试 | 与上次重试的间隔时间 | 第几次重试 | 与上次重试的间隔时间 |
| :--------: | :------------------: | :--------: | :------------------: |
|     1      |        10 秒         |     9      |        7 分钟        |
|     2      |        30 秒         |     10     |        8 分钟        |
|     3      |        1 分钟        |     11     |        9 分钟        |
|     4      |        2 分钟        |     12     |       10 分钟        |
|     5      |        3 分钟        |     13     |       20 分钟        |
|     6      |        4 分钟        |     14     |       30 分钟        |
|     7      |        5 分钟        |     15     |        1 小时        |
|     8      |        6 分钟        |     16     |        2 小时        |

如果消息重试 16 次后仍然失败，消息将**不再投递**，如果严格按照上述重试时间间隔计算，某条消息在一直消费失败的前提下，将会在接下来的 4 小时 46 分钟之内进行 16 次重试，超过这个时间范围消息将不再重试投递

说明：一条消息无论重试多少次，消息的 Message ID 是不会改变的



***



#### 重试操作

集群消费方式下，消息消费失败后期望消息重试，需要在消息监听器接口的实现中明确进行配置（三种方式任选一种）：

- 返回 Action.ReconsumeLater （推荐）
- 返回 null
- 抛出异常

```java
public class MessageListenerImpl implements MessageListener {
    @Override
    public Action consume(Message message, ConsumeContext context) {
        // 处理消息
        doConsumeMessage(message);
        //方式1：返回 Action.ReconsumeLater，消息将重试
        return Action.ReconsumeLater;
        //方式2：返回 null，消息将重试
        return null;
        //方式3：直接抛出异常， 消息将重试
        throw new RuntimeException("Consumer Message exceotion");
    }
}
```

集群消费方式下，消息失败后期望消息不重试，需要捕获消费逻辑中可能抛出的异常，最终返回 Action.CommitMessage，此后这条消息将不会再重试

```java
public class MessageListenerImpl implements MessageListener {
    @Override
    public Action consume(Message message, ConsumeContext context) {
        try {
            doConsumeMessage(message);
        } catch (Throwable e) {
            // 捕获消费逻辑中的所有异常，并返回 Action.CommitMessage;
            return Action.CommitMessage;
        }
        //消息处理正常，直接返回 Action.CommitMessage;
        return Action.CommitMessage;
    }
}
```

自定义消息最大重试次数，RocketMQ 允许 Consumer 启动的时候设置最大重试次数，重试时间间隔将按照如下策略：

- 最大重试次数小于等于 16 次，则重试时间间隔同上表描述
- 最大重试次数大于 16 次，超过 16 次的重试时间间隔均为每次 2 小时

```java
Properties properties = new Properties();
// 配置对应 Group ID 的最大消息重试次数为 20 次
properties.put(PropertyKeyConst.MaxReconsumeTimes,"20");
Consumer consumer = ONSFactory.createConsumer(properties);
```

注意：

- 消息最大重试次数的设置对相同 Group ID 下的所有 Consumer 实例有效。例如只对相同 Group ID 下两个 Consumer 实例中的其中一个设置了 MaxReconsumeTimes，那么该配置对两个 Consumer 实例均生效
- 配置采用覆盖的方式生效，即最后启动的 Consumer 实例会覆盖之前的启动实例的配置

消费者收到消息后，可按照如下方式获取消息的重试次数：

```java
public class MessageListenerImpl implements MessageListener {
    @Override
    public Action consume(Message message, ConsumeContext context) {
        // 获取消息的重试次数
        System.out.println(message.getReconsumeTimes());
        return Action.CommitMessage;
    }
}
```



***



#### 重投机制

生产者在发送消息时，同步消息失败会重投，异步消息有重试，oneway 没有任何保证。消息重投保证消息尽可能发送成功、不丢失，但当出现消息量大、网络抖动时，可能会造成消息重复；生产者主动重发、Consumer 负载变化也会导致重复消息。

消息重复在 RocketMQ 中是无法避免的问题，如下方法可以设置消息重试策略：

- retryTimesWhenSendFailed：同步发送失败重投次数，默认为 2，因此生产者会最多尝试发送 retryTimesWhenSendFailed + 1 次。不会选择上次失败的 Broker，尝试向其他 Broker 发送，最大程度保证消息不丢。超过重投次数抛出异常，由客户端保证消息不丢。当出现 RemotingException、MQClientException 和部分 MQBrokerException 时会重投
- retryTimesWhenSendAsyncFailed：异步发送失败重试次数，异步重试不会选择其他 Broker，仅在同一个 Broker 上做重试，不保证消息不丢
- retryAnotherBrokerWhenNotStoreOK：消息刷盘（主或备）超时或 slave 不可用（返回状态非 SEND_OK），是否尝试发送到其他  Broker，默认 false，十分重要消息可以开启





***



### 死信队列

正常情况下无法被消费的消息称为死信消息（Dead-Letter Message），存储死信消息的特殊队列称为死信队列（Dead-Letter Queue）

当一条消息初次消费失败，消息队列 RocketMQ 会自动进行消息重试，达到最大重试次数后，若消费依然失败，则表明消费者在正常情况下无法正确地消费该消息，此时 RocketMQ 不会立刻将消息丢弃，而是将其发送到该消费者对应的死信队列中

死信消息具有以下特性：

- 不会再被消费者正常消费
- 有效期与正常消息相同，均为 3 天，3 天后会被自动删除，所以请在死信消息产生后的 3 天内及时处理

死信队列具有以下特性：

- 一个死信队列对应一个 Group ID， 而不是对应单个消费者实例
- 如果一个 Group ID 未产生死信消息，消息队列 RocketMQ 不会为其创建相应的死信队列
- 一个死信队列包含了对应 Group ID 产生的所有死信消息，不论该消息属于哪个 Topic

一条消息进入死信队列，需要排查可疑因素并解决问题后，可以在消息队列 RocketMQ 控制台重新发送该消息，让消费者重新消费一次





****



### 幂等消费

消息队列 RocketMQ 消费者在接收到消息以后，需要根据业务上的唯一 Key 对消息做幂等处理

在互联网应用中，尤其在网络不稳定的情况下，消息队列 RocketMQ 的消息有可能会出现重复，几种情况：

- 发送时消息重复：当一条消息已被成功发送到服务端并完成持久化，此时出现了网络闪断或客户端宕机，导致服务端对客户端应答失败。此时生产者意识到消息发送失败并尝试再次发送消息，消费者后续会收到两条内容相同并且 Message ID 也相同的消息

- 投递时消息重复：消息消费的场景下，消息已投递到消费者并完成业务处理，当客户端给服务端反馈应答的时候网络闪断。为了保证消息至少被消费一次，消息队列 RocketMQ 的服务端将在网络恢复后再次尝试投递之前已被处理过的消息，消费者后续会收到两条内容相同并且 Message ID 也相同的消息

- 负载均衡时消息重复：当消息队列 RocketMQ 的 Broker 或客户端重启、扩容或缩容时，会触发 Rebalance，此时消费者可能会收到重复消息


处理方式：

* 因为 Message ID 有可能出现冲突（重复）的情况，所以真正安全的幂等处理，不建议以 Message ID 作为处理依据，最好的方式是以业务唯一标识作为幂等处理的关键依据，而业务的唯一标识可以通过消息 Key 进行设置：

  ```java
  Message message = new Message();
  message.setKey("ORDERID_100");
  SendResult sendResult = producer.send(message);
  ```

* 订阅方收到消息时可以根据消息的 Key 进行幂等处理：

  ```java
  consumer.subscribe("ons_test", "*", new MessageListener() {
      public Action consume(Message message, ConsumeContext context) {
          String key = message.getKey()
          // 根据业务唯一标识的 key 做幂等处理
      }
  });
  ```

  



***



### 流量控制

生产者流控，因为 Broker 处理能力达到瓶颈；消费者流控，因为消费能力达到瓶颈

生产者流控：

- CommitLog 文件被锁时间超过 osPageCacheBusyTimeOutMills 时，参数默认为 1000ms，返回流控
- 如果开启 transientStorePoolEnable == true，且 Broker 为异步刷盘的主机，且 transientStorePool 中资源不足，拒绝当前 send 请求，返回流控
- Broker 每隔 10ms 检查 send 请求队列头部请求的等待时间，如果超过 waitTimeMillsInSendQueue，默认 200ms，拒绝当前 send 请求，返回流控。
- Broker 通过拒绝 send 请求方式实现流量控制

注意：生产者流控，不会尝试消息重投

消费者流控：

- 消费者本地缓存消息数超过 pullThresholdForQueue 时，默认 1000
- 消费者本地缓存消息大小超过 pullThresholdSizeForQueue 时，默认 100MB
- 消费者本地缓存消息跨度超过 consumeConcurrentlyMaxSpan 时，默认 2000

消费者流控的结果是降低拉取频率





***





## 源码分析
