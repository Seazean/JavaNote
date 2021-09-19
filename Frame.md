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

1. 在 E 盘下创建目录 `mvnproject 进入该目录，作为我们的操作目录

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

2. 创建 Maven，New Module → Maven→ 不选中 Create from archetype

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



web 工程：

1. 选择 web 对应的原型骨架（选择 Maven 开头的是简化的）

   ![](https://gitee.com/seazean/images/raw/master/Frame/IDEA创建Maven-webapp.png)

2. 通过原型创建 web 项目得到的目录结构是不全的，因此需要我们自行补全，同时要标记正确

3. web 工程创建之后需要启动运行，使用 tomcat 插件来运行项目，在 `pom.xml` 中添加插件的坐标：

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



**可选依赖：**对外隐藏当前所依赖的资源，不透明

```xml
<dependency>    
    <groupId>junit</groupId>    
    <artifactId>junit</artifactId>    
    <version>4.11</version>    
    <optional>true</optional> 
    <!--默认是false，true以后就变得不透明-->
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

* Setting 属性

  - 使用 Maven 配置文件 setting.xml 中的标签属性，用于动态配置

  调用格式：

  ```xml
  ${settings.localRepository} 
  ```

* Java 系统属性：

  作用：读取 Java 系统属性

  调用格式：

  ```
  ${user.home}
  ```

  系统属性查询方式 cmd 命令：

  ```sh
  mvn help:system 
  ```

* 环境变量属性

  作用：使用 Maven 配置文件 setting.xml 中的标签属性，用于动态配置

  调用格式：

  ```
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

范例：

- 5.1.9.RELEASE



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

Netty 是一个异步事件驱动的网络应用程序框架，用于快速开发可维护的高性能协议服务器和客户端

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



***



### Reactor

#### 设计思想

Reactor 模式，通过一个或多个输入同时传递给服务处理器的服务请求的事件驱动处理模式。 服务端程序处理传入多路请求，并将它们同步分派给对应的处理线程，Reactor 模式也叫 Dispatcher 模式，即 I/O 多路复用统一监听事件，收到事件后分发（Dispatch给某进程），是编写高性能网络服务器的必备技术之一

I/O 复用结合线程池，就是 Reactor 模式基本设计思想：

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-Reactor模型.png" style="zoom: 50%;" />

Reactor 模式关键组成：

- Reactor：在一个单独的线程中运行，负责监听和分发事件，分发给适当的处理程序来对 I/O 事件做出反应
- Handlers：处理程序执行 I/O 事件要完成的实际事件，Reactor 通过调度适当的处理程序来响应 I/O 事件，处理程序执行非阻塞操作

Reactor 模式具有如下的优点：

- 响应快，不必为单个同步时间所阻塞，虽然 Reactor 本身依然是同步的
- 编程相对简单，可以最大程度的避免复杂的多线程及同步问题，并且避免了多线程/进程的切换开销
- 可扩展性，可以方便的通过增加 Reactor 实例个数来充分利用 CPU 资源
- 可复用性，Reactor 模型本身与具体事件处理逻辑无关，具有很高的复用性

根据Reactor的数量和处理资源池线程的数量不同，有3种典型的实现：

- 单 Reactor 单线程
- 单 Reactor 多线程
- 主从 Reactor 多线程



参考文章：https://www.jianshu.com/p/2965fca6bb8f



***



#### 单R单线程

Reactor 对象通过 select 监控客户端请求事件，收到事件后通过 dispatch 进行分发：

* 如果是建立连接请求事件，则由 Acceptor 通过 accept 处理连接请求，然后创建一个 Handler 对象处理连接完成后的后续业务处理

* 如果不是建立连接事件，则 Reactor 会分发给连接对应的 Handler 来响应，Handler 会完成 read、业务处理、send 的完整业务流程

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

模型优点：可以充分利用多核CPU的处理能力

模型缺点：

* 多线程数据共享和访问比较复杂
* Reactor 承担所有事件的监听和响应，在单线程中运行，高并发场景下容易成为性能瓶颈



***



#### 主从模型

采用多个 Reactor ，执行流程：

* Reactor 主线程 MainReactor 通过 select 监控建立连接事件，收到事件后通过 Acceptor 接收，处理建立连接事件，处理完成后 MainReactor 会将连接分配给 Reactor 子线程的 SubReactor 处理

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

Reactor 模式中，Reactor 等待某个事件的操作状态发生变化（文件描述符可读写，socket 可读写），然后把事件传递给事先注册的 Handler 来做实际的读写操作，其中的读写操作都需要应用程序同步操作，所以 Reactor 是非阻塞同步网络模型（NIO）

把 I/O操作改为异步，交给操作系统来完成就能进一步提升性能，这就是异步网络模型 Proactor（AIO）：

<img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-Proactor模型.png" style="zoom:50%;" />

工作流程：

* ProactorInitiator 创建 Proactor 和 Handler 对象，并将 Proactor 和 Handler 通过 Asynchronous Operation Processor（AsyOptProcessor）注册到内核
* AsyOptProcessor 处理注册请求，并处理 I/O 操作，完成I/O后通知 Proactor
* Proactor 根据不同的事件类型回调不同的 Handler 进行业务处理，最后由 Handler 完成业务处理

对比 Reactor：Reactor 在事件发生时就通知事先注册的处理器（读写在应用程序线程中处理完成）；Proactor是在事件发生时基于异步 I/O 完成读写操作（内核完成），I/O 完成后才回调应用程序的处理器进行业务处理

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

3. NioEventLoop 表示一个不断循环的执行处理任务的线程，每个 NioEventLoop 都有一个 Selector，用于监听绑定在其上的 socket 的网络通讯

4. 每个 BossNioEventLoop 循环执行的步骤：

   - 轮询 accept 事件
   - 处理 accept 事件，与 client 建立连接，生成 NioScocketChannel，并将其注册到某个 Worker 中的某个 NioEventLoop 上的 Selector
   - 处理任务队列的任务，即 runAllTasks

5. 每个 Worker NioEventLoop 循环执行的步骤：

   - 轮询 read、write 事件
   - 处理 I/O 事件，即 read，write 事件，在对应 NioScocketChannel 处理
   - 处理任务队列的任务，即 runAllTasks

6. 每个 Worker NioEventLoop 处理业务时，会使用 pipeline（管道），pipeline 中包含了 channel，即通过 pipeline 可以获取到对应通道，管道中维护了很多的处理器 Handler

   <img src="https://gitee.com/seazean/images/raw/master/Frame/Netty-Channel与Pipeline.png" style="zoom: 50%;" />



参考文章：https://www.jianshu.com/p/2965fca6bb8f



***



## 基本操作

### 服务端

* pom.xml

  ```xml
  <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-all</artifactId>
      <version>4.1.20.Final</version>
  </dependency>
  ```

* NettyServer.java

  ```java
  public class NettyServer {
      public static void main(String[] args) {
          //创建 BossGroup 和 WorkerGroup，两个都是无限循环
          //bossGroup 只是处理连接请求，真正的和客户端业务处理会交给 workerGroup完成
          //两个 Group 含有的子线程(NioEventLoop)的个数为默认为 cpu核数 * 2
          EventLoopGroup bossGroup = new NioEventLoopGroup(1);
          EventLoopGroup workerGroup = new NioEventLoopGroup(); //8
          
          try {
              //创建服务器端的启动对象，配置参数
              ServerBootstrap bootstrap = new ServerBootstrap();
              //使用链式编程来进行设置，设置两个线程组
              bootstrap.group(bossGroup, workerGroup) 
                  	//使用NioSocketChannel 作为服务器的通道实现
                      .channel(NioServerSocketChannel.class) 
                  	// 设置线程队列得到连接个数
                      .option(ChannelOption.SO_BACKLOG, 128) 
                  	//设置保持活动连接状态
                      .childOption(ChannelOption.SO_KEEPALIVE, true) 
                 		// 该 handler对应 bossGroup , childHandler 对应 workerGroup
                      //.handler(null) 
                 		//创建一个通道初始化对象(匿名对象)
                      .childHandler(new NettyServerInitializer());
  
              System.out.println(".....Server is ready...");
  
              //绑定一个端口并且同步, 生成了一个ChannelFuture对象，启动服务器
              ChannelFuture channelFuture = bootstrap.bind(6668).sync();
  
              //给cf 注册监听器，监控我们关心的事件
              channelFuture.addListener(new ChannelFutureListener() {
                  @Override
                  public void operationComplete(ChannelFuture future) throws Exception {
                      if (channelFuture.isSuccess()) {
                          System.out.println("listening on port 6668 succeeded");
                      } else {
                          System.out.println("listening on port 6668 failed");
                      }
                  }
              });
  
              //对关闭通道进行监听
              channelFuture.channel().closeFuture().sync();
          } catch (InterruptedException e) {
              e.printStackTrace();
          } finally {
              bossGroup.shutdownGracefully();
              workerGroup.shutdownGracefully();
          }
      }
  }
  ```

* NettyServerInitializer.java

  ```java
  public class NettyServerInitializer extends ChannelInitializer<SocketChannel> {
      //给pipeline设置处理器
      @Override
      protected void initChannel(SocketChannel ch) throws Exception {
          // 可以使用一个集合管理所有的SocketChannel
          // 推送消息时直接拿到对应的 NIOEventLoop 的 taskQueue
          System.out.println("Client socket channel hashcode : " + ch.hashCode());
          // 给 workerGroup 的 EventLoop 对应的管道设置处理器
          ch.pipeline().addLast(new NettyServerHandler());
      }
  }
  ```

* NettyServerHandler.java

  ```java
  //自定义一个Handler，需要继承netty，规定好的某个HandlerAdapter
  public class NettyServerHandler extends ChannelInboundHandlerAdapter {
      /**
       * 读取数据的方法
       * @param ctx   上下文对象，含有管道pipeline、通道channel、地址
       * @param msg   客户端发送的数据
       * @throws Exception
       */
      @Override
      public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
          System.out.println("server read thread " + Thread.currentThread().getName());
          System.out.println("server ctx = " + ctx);
          System.out.println("the relationship between channels and pipeline");
          Channel channel = ctx.channel();
          ChannelPipeline pipeline = ctx.pipeline();//本质是一个双向链表
  
          //将msg转成ByteBuf，ByteBuf是Netty提供的，不是NIO的ByteBuffer
          ByteBuf buf = (ByteBuf) msg;
          sout("Client send data is : " + buf.toString(CharsetUtil.UTF_8));
          sout("Client address is : " + ctx.channel().remoteAddress());
  
      }
      
      //数据读取完毕执行该方法
      @Override
      public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
          // writeAndFlush 是 write + flush，写入到缓存并刷新，对发送的数据进行编码
          ctx.writeAndFlush(Unpooled.copiedBuffer("Hello client", CharsetUtil.UTF_8));
      }
  
      //处理异常，一般是需要关闭通道
      @Override
      public void exceptionCaught(ChannelHandlerContext ctx, Thro，wable cause) throws Exception {
          cause.printStackTrace();
          ctx.close();
      }
  }
  ```

  

***



### 客户端

* NettyClient.java

  ```java
  public class NettyClient {
      public static void main(String[] args){
          //客户端需要一个事件循环组
          NioEventLoopGroup group = new NioEventLoopGroup();
          try {
              //创建客户端启动对象，客户端使用的不是 ServerBootstrap 而是 Bootstrap
              Bootstrap bootstrap = new Bootstrap();
              //设置相关参数
              bootstrap.group(group) //设置线程组
                 		 //使用NioSocketChannel客户端通道，反射实现
                      .channel(NioSocketChannel.class)
                      .handler(new NettyClientInitializer());
  
              System.out.println("...Client is ok...");
  
              //启动客户端连接服务器端
              ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 6668).sync();
              //对关闭通道进行监听，只是监听不是关闭
              channelFuture.channel().closeFuture().sync();
          } catch (InterruptedException e) {
              e.printStackTrace();
          } finally {
              group.shutdownGracefully();
          }
      }
  }
  ```

* NettyClientInitializer.java

  ```java
  public class NettyClientInitializer extends ChannelInitializer<SocketChannel> {
      @Override
      protected void initChannel(SocketChannel ch) throws Exception {
          ch.pipeline().addLast(new NettyClientHandler());
      }
  }
  ```

* NettyClientHandler.java

  ```java
  public class NettyClientHandler extends ChannelInboundHandlerAdapter {
      // 通道就绪就会触发该方法
      @Override
      public void channelActive(ChannelHandlerContext ctx) throws Exception {
          System.out.println("client : " + ctx);
          ctx.writeAndFlush(Unpooled.copiedBuffer("hello server", CharsetUtil.UTF_8));
      }
  
      // 通道有读取事件时，会触发
      @Override
      public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
          ByteBuf buf = (ByteBuf) msg;
          System.out.println("Server reply : " + buf.toString(CharsetUtil.UTF_8));
          System.out.println("Server address : " + ctx.channel().remoteAddress());
      }
  
      //异常处理
      @Override
      public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
          cause.printStackTrace();
          ctx.close();
      }
  }
  ```

  



***







