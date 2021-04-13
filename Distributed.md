# Git

## Git概述

### Git与SVN

SVN是集中式版本控制系统，版本库是集中放在中央服务器的，而开发人员工作的时候，用的都是自己的电脑，所以首先要从中央服务器下载最新的版本，然后开发，开发完后，需要把自己开发的代码提交到中央服务器。

集中式版本控制工具缺点：服务器单点故障、容错性差

Git是分布式版本控制系统（Distributed Version Control System，简称 DVCS） ，分为两种类型的仓库：

本地仓库和远程仓库：

* 本地仓库：是在开发人员自己电脑上的Git仓库		
* 远程仓库：是在远程服务器上的Git仓库

Clone：克隆，就是将远程仓库复制到本地 
Push：推送，就是将本地仓库代码上传到远程仓库
Pull：拉取，就是将远程仓库代码下载到本地仓库



### Git工作流程

1．从远程仓库中克隆代码到本地仓库

2．从本地仓库中checkout代码然后进行代码修改

3．在提交前先将代码提交到**暂存区**

4．提交到本地仓库。本地仓库中保存修改的各个历史版本

5．修改完成后，需要和团队成员共享代码时，将代码push到远程仓库



### Git安装

下载地址： <https://git-scm.com/download>



### Git代码托管服务

Git中存在两种类型的仓库，即本地仓库和远程仓库。那么我们如何搭建Git远程仓库呢？我们可以借助互联网上提供的一些代码托管服务来实现，其中比较常用的有GitHub、码云、GitLab等。

gitHub（ 地址：https://github.com/ ）是一个面向开源及私有软件项目的托管平台，因为只支持Git 作为唯一的版本库格式进行托管，故名gitHub

码云（地址： https://gitee.com/ ）是国内的一个代码托管平台，由于服务器在国内，所以相比于GitHub，码云速度会更快

GitLab （地址： https://about.gitlab.com/ ）是一个用于仓库管理系统的开源项目，使用Git作为代码管理工具，并在此基础上搭建起来的web服务





***



## 环境配置

安装Git后首先要设置用户名称和email地址，因为每次Git提交都会使用该用户信息。此信息和我们注册的代码托管平台的信息无关。

设置用户信息：
	git config --global user.name “Seazean”
	git config --global user.email “zhyzhyang@sina.com”  //用户名和邮箱可以随意填写，不会校对

查看配置信息：
	git config --list
	git config user.name

通过上面的命令设置的信息会保存在用户目录下/.gitconfig文件中



***



## 获取仓库

* **本地仓库初始化**
  1.在电脑的任意位置创建一个空目录（例如repo1）作为我们的本地Git仓库
  2.进入这个目录中，点击右键打开Git bash窗口
  3.执行命令**git init**

  如果在当前目录中看到.git文件夹（此文件夹为隐藏文件夹）则说明Git仓库创建成功

* **远程仓库克隆**
  通过Git提供的命令从远程仓库进行克隆，将远程仓库克隆到本地
  命令：git clone 远程Git仓库地址 (HTTPS或者SSH)

* 生成SSH公钥步骤

  * 设置账户
  * cd ~/.ssh（查看是否生成过SSH公钥）//user目录下
  * 生成SSH公钥：`ssh-keygen –t rsa –C "email"`
    * -t 指定密钥类型，默认是 rsa ，可以省略。
    * -C 设置注释文字，比如邮箱。
    * -f 指定密钥文件存储文件名。
  * 查看命令: cat ~/.ssh/id_rsa.pub
  * 公钥测试命令: ssh -T git@gitee.com





***






## 工作过程

![](https://gitee.com/seazean/images/raw/master/Frame/Git基本工作流程.png)

版本库：.git隐藏文件夹就是版本库，版本库中存储了很多配置信息、日志信息和文件版本信息等

工作目录（工作区）：包含.git文件夹的目录就是工作目录，主要用于存放开发的代码

暂存区：.git文件夹中有很多文件，其中有一个index文件就是暂存区，也可以叫做stage。暂存区是一个临时保存修改文件的地方

![](https://gitee.com/seazean/images/raw/master/Frame/文件流程图.png)



***



## 文件操作

### 常用命令

| 命令                    | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| git status              | 查看 git 状态 （文件是否进行了添加、提交操作）               |
| git add filename        | 添加，将指定文件添加到暂存区                                 |
| git commit -m 'message' | 提交，将暂存区文件提交到本地仓库，删除暂存区的该文件         |
| git rm filename         | 删除，删除工作区的文件，不是仓库，需要提交                   |
| git mv filename         | 移动或重命名工作区文件                                       |
| git reset filename      | 使用当前分支上的修改覆盖暂存区，**将暂存区的文件取消暂存**   |
| git checkout filename   | 使用暂存区的修改覆盖工作目录，用来撤销本次修改(危险)         |
| git log                 | 查看日志（ git 提交的历史日志）                              |
| git reflog              | 可以查看所有分支的所有操作记录（包括已经被删除的 commit 记录的操作） |



**其他指令**：可以跳过暂存区域直接从分支中取出修改，或者直接提交修改到分支中。

* git commit -a 直接把所有文件的修改添加到暂存区然后执行提交
* git checkout HEAD -- files 取出最后一次修改，可以用来进行回滚操作



### 文件状态

* Git工作目录下的文件存在两种状态：
  * untracked 未跟踪（未被纳入版本控制）
  * tracked 已跟踪（被纳入版本控制）
    * Unmodified 未修改状态
    * Modified 已修改状态
    * Staged 已暂存状态

* 查看文件状态
  文件的状态会随着我们执行Git的命令发生变化
  * git status 查看文件状态
  * git status –s 查看更简洁的文件状态

### 文件忽略

一般我们总会有些文件无需纳入Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以在工作目录中创建一个名为 .gitignore 的文件（文件名称固定），列出要忽略的文件模式。下面是一个示例：

```
# no .a files
*.a
# but do track lib.a, even though you're ignoring .a files above
!lib.a
# only ignore the TODO file in the current directory, not subdir/TODO
/TODO
# ignore all files in the build/ directory
build/
# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt
# ignore all .pdf files in the doc/ directory
doc/**/*.pdf
```



***





## 远程仓库

### 工作流程

git有四个工作空间的概念，分别为 工作空间、暂存区、本地仓库、远程仓库。

pull=fetch+merge
fetch是从远程仓库更新到本地仓库，pull是从远程仓库直接更新到工作空间中

![](https://gitee.com/seazean/images/raw/master/Frame/图解远程仓库工作流程.png)





### 查看远程仓库

git remote：显示所有远程仓库的简写  

git remote -v：显示所有远程仓库  

git remote show <shortname>：显示某个远程仓库的详细信息



### 添加远程仓库

git remote add <shortname><url> ：添加一个新的远程仓库，并指定一个可以引用的简写



### 克隆远程仓库

git clone <url>(HTTPS or SSH)：克隆远程仓库

Git 克隆的是该 Git 仓库服务器上的几乎所有数据（包括日志信息、历史记录等），而不仅仅是复制工作所需要的文件。 当你执行 git clone 命令的时候，默认配置下远程 Git 仓库中的每一个文件的每一个版本都将被拉取下来。



### 删除远程仓库

git remote rm <shortname>：移除远程仓库，从本地移除远程仓库的记录，并不会影响到远程仓库



### 抓取与拉取

git fetch  <shortname>  从远程仓库获取最新版本到本地仓库，不会自动merge  

git pull <shortname> <branchname>  从远程仓库获取最新版本并merge到本地仓库

注意：如果当前本地仓库不是从远程仓库克隆，而是本地创建的仓库，并且**仓库中存在文件**，此时再从远程仓库拉取文件的时候会报错（fatal: refusing to merge unrelated histories ），解决此问题可以在git pull命令后加入参数--allow-unrelated-histories



### 推送

git push <shortname><branchname>  上传本地指定分支到远程仓库





***





## 版本管理

![](https://gitee.com/seazean/images/raw/master/Frame/版本切换.png)

命令：git reset --hard 版本唯一索引值



## 分支管理

### 查看分支

git branch  列出所有本地分支  

git branch -r  列出所有远程分支  

git branch -a  列出所有本地分支和远程分支



### 创建分支

git branch  branch-name  新建一个分支，但依然停留在当前分支

git checkout -b branch-name新建一个分支，并切换到该分支



### 推送分支 

git push origin branch-name 推送到远程仓库，origin是引用名



### 切换分支

git checkout branch-name  切换到branch-name分支



### 合并分支

git merge branch-name 合并指定分支到当前分支

有时候合并操作不会如此顺利。 如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没办法合并它们，同时会提示文件冲突。此时需要我们打开冲突的文件并修复冲突内容，最后执行git add命令来标识冲突已解决

​	![](https://gitee.com/seazean/images/raw/master/Frame/合并分支冲突.png)



### 删除分支

git branch -d branch-name   删除分支

git push origin –d branch-name 删除远程仓库中的分支   （origin是引用名）

如果要删除的分支中进行了开发动作，此时执行删除命令并不会删除分支，如果坚持要删除此分支，可以将命令中的-d参数改为-D    git branch -D branch-name



***



## 标签管理

### 查看标签

git tag：列出所有tag

git show tag-name：查看tag详细信息



### 新建标签

git tag tag-name：新建标签，如（git tag v1.0.1）



### 推送标签

git push [remotename] [tagname]：推送到远程仓库

git push [remotename] --tags：推送所有的标签



### 切换标签

git checkout tag-name：切换标签



### 删除标签

git tag -d tag-name：删除本地标签

git push origin :refs/tags/ tag-name ：删除远程标签



***



## IDEA集成Git

### 环境配置

File→Settings打开设置窗口，找到Version Control下的git选项

选择git的安装目录后可以点击“Test”按钮测试是否正确配置：D:\Program Files\Git\cmd\git.exe



### 创建本地仓库

1、VCS->Import into Version Control->Create Git Repository

2、选择工程所在的目录,这样就创建好本地仓库了

3、点击git后边的对勾,将当前项目代码提交到本地仓库

​	注意: 项目中的配置文件不需要提交到本地仓库中,提交时,忽略掉即可



### 文件操作

右键项目名打开菜单Git->Add

commit



### 版本管理

* 版本对比
  	![](https://gitee.com/seazean/images/raw/master/Frame/版本对比.png)

* 版本切换方式一：控制台Version Control->Log->右键Reset Current Branch...->Reset
  	这种切换的特点是会抛弃原来的提交记录
     		![](https://gitee.com/seazean/images/raw/master/Frame/版本切换方式一.png)
* 版本切换方式二：控制台Version Control->Log->Revert Commit->Merge->处理代码->commit
      这种切换的特点是会当成一个新的提交记录,之前的提交记录也都保留
   ![](https://gitee.com/seazean/images/raw/master/Frame/版本切换方式二.png)

​           ![](https://gitee.com/seazean/images/raw/master/Frame/版本切换方式二(1).png)





### 分支管理

* 创建分支
  VCS->Git->Branches->New Branch->给分支起名字->ok
* 切换分支
  idea右下角Git->选择要切换的分支->checkout
* 合并分支
  VCS->Git->Merge changes->选择要合并的分支->merge
* 删除分支
  idea右下角->选中要删除的分支->Delete





### 推送远程仓库

1. VCS->Git->Push->点击master Define remote
2. 将远程仓库的url路径复制过来->Push
   ![](https://gitee.com/seazean/images/raw/master/Frame/本地仓库推送到远程仓库.png)

### 克隆远程仓库

File->Close Project->Checkout from Version Control->Git->指定远程仓库的路径->指定本地存放的路径->clone

![](https://gitee.com/seazean/images/raw/master/Frame/远程仓库克隆到本地仓库.png)







****





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





****





# Docker

## 基本概述

Docker 是一个开源的应用容器引擎，诞生于 2013 年初，基于 Go 语言实现， dotCloud 公司出品

Docker 让开发者打包开发应用以及依赖包到一个轻量级、可移植的容器中，可以发布到任何Linux机器上

* 容器是完全使用沙箱机制，相互隔离

* 容器性能开销极低。

Docker架构：

* **镜像（Image）：**Docker 镜像，就相当于一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu16.04 最小系统的 root 文件系统

* **容器（Container）**：镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的类和对象一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等
* **仓库（Repository）**：仓库可看成一个代码控制中心，用来保存镜像

![](https://gitee.com/seazean/images/raw/master/Frame/Docker-docker架构.png)

安装步骤：

```sh
# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: 安装GPG证书
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# Step 3: 写入软件源信息
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# Step 4: 更新并安装Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce
```

配置镜像加速器：

```sh
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://hicqe4pi.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```





***



## 操作命令

### 进程相关

* 启动docker服务：

  ```sh
  systemctl start docker
  ```

* 停止docker服务：

  ```sh
  systemctl stop docker
  ```

* 重启doker服务：

  ```sh
  systemctl restart docker
  ```

* 查看doker服务状态：

  ```sh
  systemctl status docker
  ```

* 设置开机启动docker服务：

  ```sh
  systemctl enable docker
  ```

  



### 镜像相关

* 查看镜像：查看本地所有的镜像

  ```sh
  docker images
  docker images –q # 查看所用镜像的id
  ```

* 搜索镜像：从网络中查找需要的镜像

  ```sh
  docker search 镜像名称
  ```

* 拉取镜像：从Docker仓库下载镜像到本地，镜像名称格式为 名称:版本号，如果版本号不指定则是最新的版本。如果不知道镜像版本，可以去docker hub 搜索对应镜像查看

  ```sh
  docker pull 镜像名称
  ```

* 删除镜像：删除本地镜像

  ```sh
  docker rmi 镜像id # 删除指定本地镜像
  docker rmi `docker images -q`  # 删除所有本地镜像 tab上面的键
  ```





### 容器相关

* 查看容器：

  ```sh
  docker ps # 查看正在运行的容器
  docker ps –a # 查看所有容器
  ```

* 创建并启动容器：

  ```sh
  docker run 参数  --name=... /bin/bash
  ```

  参数说明：

  * -i：保持容器运行，通常与 -t 同时使用，加入it这两个参数后，容器创建后自动进入容器中，退出容器后，容器自动关闭
  * -t：为容器重新分配一个伪输入终端，通常与 -i 同时使用
  * -d：以守护（后台）模式运行容器。创建一个容器在后台运行，需要使用docker exec 进入容器。退出后，容器不会关闭
  * **-it 创建的容器一般称为交互式容器，-id 创建的容器一般称为守护式容器**
  * **--name：为创建的容器命名**

* 进入容器：

  ```sh
  docker exec 参数 # 退出容器，容器不会关闭
  ```

* 停止容器：

  ```sh
  docker stop 容器名称
  ```

* 启动容器：

  ```sh
  docker start 容器名称
  ```

* 删除容器：如果容器是运行状态则删除失败，需要停止容器才能删除

  ```sh
  docker rm 容器名称
  ```

* 查看容器信息：

  ```sh
  docker inspect 容器名称
  ```

  



****



## 数据卷

> Docker 容器删除后，在容器中产生的数据也会随之销毁
> Docker 容器和外部机器可以直接交换文件吗？
> 容器之间想要进行数据交互？

<img src="https://gitee.com/seazean/images/raw/master/Frame/Docker-容器的数据卷.png" style="zoom:67%;" />

**数据卷**：数据卷是宿主机中的一个目录或文件，当容器目录和数据卷目录绑定后，对方的修改会立即同步

* 一个数据卷可以被多个容器同时挂载

* 一个容器也可以被挂载多个数据卷

数据卷的作用：

* 容器数据持久化
* 外部机器和容器间接通信
* 容器之间数据交换

配置数据卷

* 创建启动容器时，使用-v参数设置数据卷

  ```sh
  docker run ... –v 宿主机目录(文件):容器内目录(文件) ... 
  docker run -it --name=c1 -v /root(or~)/data:/root/data_container centos:7
  ```

  注意事项：

  1. 目录必须是绝对路径

  2. 如果目录不存在，会自动创建

  3. 可以挂载多个数据卷

多容器进行数据交换：

* 多个容器挂载同一个数据卷
* 数据卷容器

<img src="https://gitee.com/seazean/images/raw/master/Frame/Docker-多容器数据交换.png" style="zoom:50%;" />

* 创建启动c3数据卷容器，使用 –v 参数设置数据卷

  ```sh
  docker run –it --name=c3 –v /volume centos:7 /bin/bash 
  ```

* 创建启动 c1 c2 容器，使用 –-volumes-from 参数设置数据卷

  ```sh
  docker run –it --name=c1 --volumes-from c3 centos:7 /bin/bash
  docker run –it --name=c2 --volumes-from c3 centos:7 /bin/bash  
  ```





***



## 应用部署

### MySQL

在Docker容器中部署MySQL，通过外部mysql客户端操作MySQL Server

端口映射：

* 容器内的网络服务和外部机器不能直接通信，外部机器和宿主机可以直接通信，宿主机和容器可以直接通信

* 当容器中的网络服务需要被外部机器访问时，可以将容器中提供服务的端口映射到宿主机的端口上。外部机器访问宿主机的该端口，从而间接访问容器的服务。这种操作称为：**端口映射**

  ![](https://gitee.com/seazean/images/raw/master/Frame/Docker-MySQL部署.png)
  

MySQL部署步骤：搜索mysql镜像，拉取mysql镜像，创建容器，操作容器中的mysql

1. 搜索mysql镜像

   ```sh
   docker search mysql
   ```

2. 拉取mysql镜像

   ```mysql
   docker pull mysql:5.6
   ```

3. 创建容器，设置端口映射、目录映射

   ```sh
   # 在/root目录下创建mysql目录用于存储mysql数据信息
   mkdir ~/mysql
   cd ~/mysql
   ```

   ```sh
   docker run -id \
   -p 3307:3306 \
   --name=c_mysql \
   -v $PWD/conf:/etc/mysql/conf.d \
   -v $PWD/logs:/logs \
   -v $PWD/data:/var/lib/mysql \
   -e MYSQL_ROOT_PASSWORD=123456 \
   mysql:5.6
   ```

   参数说明：

   * `-p 3307:3306`：将容器的 3306 端口映射到宿主机的 3307 端口
   * `-v $PWD/conf:/etc/mysql/conf.d`：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf，配置目录
   * `-v $PWD/logs:/logs`：将主机当前目录下的 logs目录挂载到容器的 /logs，日志目录
   * `-v $PWD/data:/var/lib/mysql` ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 。数据目录
   * `-e MYSQL_ROOT_PASSWORD=123456`**：**初始化 root 用户的密码。

4. 进入容器，操作mysql

   ```sh
   docker exec –it c_mysql /bin/bash
   ```

5. 使用外部机器连接容器中的mysql



****



### Tomcat

1. 搜索tomcat镜像

   ```sh
   docker search tomcat
   ```

2. 拉取tomcat镜像

   ```sh
   docker pull tomcat
   ```

3. 创建容器，设置端口映射、目录映射

   ```sh
   # 在/root目录下创建tomcat目录用于存储tomcat数据信息
   mkdir ~/tomcat
   cd ~/tomcat
   ```

   ```sh
   docker run -id --name=c_tomcat \
   -p 8080:8080 \
   -v $PWD:/usr/local/tomcat/webapps \
   tomcat 
   ```

   参数说明：

   * `-p 8080:8080`：将容器的8080端口映射到主机的8080端口

   * `-v $PWD:/usr/local/tomcat/webapps`：将主机中当前目录挂载到容器的webapps

4. 使用外部机器访问tomcat



***



### Nginx

1. 搜索nginx镜像

   ```sh
   docker search nginx
   ```

2. 拉取nginx镜像

   ```sh
   docker pull nginx
   ```

3. 创建容器，设置端口映射、目录映射

   ```sh
   # 在/root目录下创建nginx目录用于存储nginx数据信息
   mkdir ~/nginx
   cd ~/nginx
   mkdir conf
   cd conf
   # 在~/nginx/conf/下创建nginx.conf文件,粘贴下面内容
   vim nginx.conf
   ```

   ```sh
   user  nginx;
   worker_processes  1;
   
   error_log  /var/log/nginx/error.log warn;
   pid        /var/run/nginx.pid;
   
   
   events {
       worker_connections  1024;
   }
   
   
   http {
       include       /etc/nginx/mime.types;
       default_type  application/octet-stream;
   
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" '
                         '"$http_user_agent" "$http_x_forwarded_for"';
   
       access_log  /var/log/nginx/access.log  main;
   
       sendfile        on;
       #tcp_nopush     on;
   
       keepalive_timeout  65;
   
       #gzip  on;
   
       include /etc/nginx/conf.d/*.conf;
   }
   ```

   ```sh
   docker run -id --name=c_nginx \
   -p 80:80 \
   -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf \
   -v $PWD/logs:/var/log/nginx \
   -v $PWD/html:/usr/share/nginx/html \
   nginx
   ```

   参数说明：

   * `-p 80:80`：将容器的 80端口映射到宿主机的 80 端口
   * `-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf`：将主机当前目录下的 /conf/nginx.conf 挂载到容器的 :/etc/nginx/nginx.conf，配置目录
   * `-v $PWD/logs:/var/log/nginx`：将主机当前目录下的 logs 目录挂载到容器的/var/log/nginx，日志目录

4. 使用外部机器访问nginx



***



### Redis

1. 搜索redis镜像

   ```sh
   docker search redis
   ```

2. 拉取redis镜像

   ```sh
   docker pull redis:5.0
   ```

3. 创建容器，设置端口映射

   ```sh
   docker run -id --name=c_redis -p 6379:6379 redis:5.0
   ```

4. 使用外部机器连接redis

   ```sh
   ./redis-cli.exe -h 192.168.149.135 -p 6379
   ```





***



## Dockerfile

### 镜像原理

> Docker 镜像本质是什么？
> Docker 中一个centos镜像为什么只有200MB，而一个centos操作系统的iso文件要几个个G？
> Docker 中一个tomcat镜像为什么有500MB，而一个tomcat安装包只有70多MB？

操作系统的组成部分：进程调度子系统、进程通信子系统、内存管理子系统、设备管理子系统、文件管理子系统、网络通信子系统、作业控制子系统

Linux文件系统由bootfs和rootfs两部分组成：

* bootfs：包含bootloader（引导加载程序）和 kernel（内核）

* rootfs： root文件系统，包含的就是典型 Linux 系统中的/dev，/proc，/bin，/etc等标准目录和文件

* 不同的linux发行版，bootfs基本一样，而rootfs不同，如ubuntu，centos

Docker镜像原理：

* Docker镜像是一个**分层文件系统**，是由特殊的文件系统叠加而成，最底端是 bootfs，并复用宿主机的bootfs ，第二层是 root文件系统rootfs称为base image，然后再往上可以叠加其他的镜像文件

* 统一文件系统（Union File System）技术能够将不同的层整合成一个文件系统，为这些层提供了一个统一的视角，这样就隐藏了多层的存在，在用户的角度看来，只存在一个文件系统

* 一个镜像可以放在另一个镜像的上面。位于下面的镜像称为父镜像，最底部的镜像成为基础镜像。

* 当从一个镜像启动容器时，Docker会在最顶层加载一个读写文件系统作为容器

问题：

* Docker 中一个Ubuntu镜像为什么只有200MB，而一个Ubuntu操作系统的iso文件要几个个G？
  Ubuntu的iso镜像文件包含bootfs和rootfs，而docker的Ubuntu镜像复用操作系统的bootfs，只有rootfs和其他镜像层
* Docker 中一个tomcat镜像为什么有500MB，而一个tomcat安装包只有70多MB？
  由于docker中镜像是分层的，tomcat虽然只有70多MB，但他需要依赖于父镜像和基础镜像，所有整个对外暴露的tomcat镜像大小500多MB



***



### 镜像制作

![](https://gitee.com/seazean/images/raw/master/Frame/Docker-Docker镜像原理.png)

****



### Dockerfile

#### 基本概述

Dockerfile是一个文本文件，包含一条条的指令，每一条指令构建一层，基于基础镜像最终构建出新的镜像

* 对于开发人员：可以为开发团队提供一个完全一致的开发环境

* 对于测试人员：可以直接拿开发时所构建的镜像或者通过Dockerfile文件构建一个新的镜像开始工作了 

* 对于运维人员：在部署时，可以实现应用的无缝移植 



| 关键字      | 作用                     | 备注                                                         |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| FROM        | 指定父镜像               | 指定dockerfile基于那个image构建                              |
| MAINTAINER  | 作者信息                 | 用来标明这个dockerfile谁写的                                 |
| LABEL       | 标签                     | 用来标明dockerfile的标签 可以使用Label代替Maintainer 最终都是在docker image基本信息中可以查看 |
| RUN         | 执行命令                 | 执行一段命令 默认是/bin/sh 格式: RUN command 或者 RUN ["command" , "param1","param2"] |
| CMD         | 容器启动命令             | 提供启动容器时候的默认命令 和ENTRYPOINT配合使用.格式 CMD command param1 param2 或者 CMD ["command" , "param1","param2"] |
| ENTRYPOINT  | 入口                     | 一般在制作一些执行就关闭的容器中会使用                       |
| COPY        | 复制文件                 | build的时候复制文件到image中                                 |
| ADD         | 添加文件                 | build的时候添加文件到image中 不仅仅局限于当前build上下文 可以来源于远程服务 |
| ENV         | 环境变量                 | 指定build时候的环境变量 可以在启动的容器的时候 通过-e覆盖 格式ENV name=value |
| ARG         | 构建参数                 | 构建参数 只在构建的时候使用的参数 如果有ENV 那么ENV的相同名字的值始终覆盖arg的参数 |
| VOLUME      | 定义外部可以挂载的数据卷 | 指定build的image那些目录可以启动的时候挂载到文件系统中 启动容器的时候使用 -v 绑定 格式 VOLUME ["目录"] |
| EXPOSE      | 暴露端口                 | 定义容器运行的时候监听的端口 启动容器的使用-p来绑定暴露端口 格式: EXPOSE 8080 或者 EXPOSE 8080/udp |
| WORKDIR     | 工作目录                 | 指定容器内部的工作目录 如果没有创建则自动创建 如果指定/ 使用的是绝对地址 如果不是/开头那么是在上一条workdir的路径的相对路径 |
| USER        | 指定执行用户             | 指定build或者启动的时候 用户 在RUN CMD ENTRYPONT执行的时候的用户 |
| HEALTHCHECK | 健康检查                 | 指定监测当前容器的健康监测的命令 基本上没用 因为很多时候 应用本身有健康监测机制 |
| ONBUILD     | 触发器                   | 当存在ONBUILD关键字的镜像作为基础镜像的时候 当执行FROM完成之后 会执行 ONBUILD的命令 但是不影响当前镜像 用处也不怎么大 |
| STOPSIGNAL  | 发送信号量到宿主机       | 该STOPSIGNAL指令设置将发送到容器的系统调用信号以退出。       |
| SHELL       | 指定执行脚本的shell      | 指定RUN CMD ENTRYPOINT 执行命令的时候 使用的shell            |



#### Centos

自定义centos7镜像：

1. 默认登录路径为 /usr

2. 可以使用vim

实现步骤：

1. 定义父镜像：FROM centos:7
2. 定义作者信息：MAINTAINER  seazean < zhyzhyang@sina.com>
3. 执行安装vim命令： RUN yum install -y vim
4. 定义默认的工作目录：WORKDIR /usr
5. 定义容器启动执行的命令：CMD /bin/bash
6. 通过dockerfile构建镜像：docker bulid –f dockerfile文件路径 –t 镜像名称:版本



#### Boot

定义dockerfile，发布springboot项目：

实现步骤：

1. 定义父镜像：FROM java:8

2. 定义作者信息：MAINTAINER  itheima <itheima@itcast.cn>

3. 将jar包添加到容器： ADD springboot.jar app.jar

4. 定义容器启动执行的命令：CMD java–jar app.jar

5. 通过dockerfile构建镜像：docker bulid –f dockerfile文件路径 –t 镜像名称:版本





***



## Compose

### 服务编排

微服务架构的应用系统中一般包含若干个微服务，每个微服务一般都会部署多个实例，如果每个微服务都要手动启停，维护的工作量会很大。

* 从Dockerfile build image 或者去dockerhub拉取image；
* 创建多个container，管理这些container（启动停止删除）

**服务编排**：按照一定的业务规则批量管理容器

Docker Compose是一个编排多容器分布式部署的工具，提供命令集管理容器化应用的完整开发周期，包括服务构建，启动和停止。使用步骤：

1. 利用 Dockerfile 定义运行环境镜像

2. 使用 docker-compose.yml 定义组成应用的各服务

3. 运行 docker-compose up 启动应用

![](https://gitee.com/seazean/images/raw/master/Frame/Docker-Compose原理.png)



***



### 功能实现

使用docker compose编排nginx+springboot项目

1. 安装Docker Compose

2. 创建docker-compose目录

   ```sh
   mkdir ~/docker-compose
   cd ~/docker-compose
   ```

3. 编写 docker-compose.yml 文件

   ```sh
   version: '3'
   services:
     nginx:
      image: nginx
      ports:
       - 80:80
      links:
       - app
      volumes:
       - ./nginx/conf.d:/etc/nginx/conf.d
     app:
       image: app
       expose:
         - "8080"
   ```

4. 创建./nginx/conf.d目录

   ```sh
   mkdir -p ./nginx/conf.d
   ```

5. 在./nginx/conf.d目录下编写***.conf文件

   ```sh
   server {
       listen 80;
       access_log off;
   
       location / {
           proxy_pass http://app:8080;
       }
   }
   ```

6. 在~/docker-compose 目录下使用docker-compose启动容器

   ```sh
   docker-compose up
   ```

7. 测试访问

   ```sh
   http://192.168.0.137/hello
   ```

   

***





## 私有仓库

Docker官方的Docker hub（https://hub.docker.com）是一个用于管理公共镜像的仓库，我们可以从上面拉取镜像 到本地，也可以把我们自己的镜像推送上去。但是当服务器无法访问互联网，或者不希望将自己的镜像放到公网当中，那么我们就需要搭建自己的私有仓库来存储和管理自己的镜像

* 私有仓库搭建

  ```sh
  # 1、拉取私有仓库镜像 
  docker pull registry
  # 2、启动私有仓库容器 
  docker run -id --name=registry -p 5000:5000 registry
  # 3、输入地址http://私有仓库服务器ip:5000/v2/_catalog，显示{"repositories":[]} 
  # 4、修改daemon.json   
  vim /etc/docker/daemon.json    
  # 在上述文件中添加一个key，保存退出。此步用于让 docker 信任私有仓库地址；注意将私有仓库服务器ip修改为自己私有仓库服务器真实ip 
  {"insecure-registries":["192.168.0.137:5000"]} 
  # 5、重启docker 服务 
  systemctl restart docker
  docker start registry
  ```

* 将镜像上传至私有仓库

  ```sh
  # 1、标记镜像为私有仓库的镜像     
  docker tag centos:7 私有仓库服务器IP:5000/centos:7
   
  # 2、上传标记的镜像     
  docker push 私有仓库服务器IP:5000/centos:7
  ```

* 从私有仓库拉取镜像 

  ```sh
  #拉取镜像 
  docker pull 私有仓库服务器ip:5000/centos:7
  ```

  

***



## 对比虚拟机

容器：

* 容器是将软件打包成标准化单元，以用于开发、交付和部署

* 容器镜像是轻量的、可执行的独立软件包 ，包含软件运行所需的所有内容：代码、运行时环境、系统工具、系统库和设置

* 容器化软件在任何环境中都能够始终如一地运行。

* 容器赋予了软件独立性，使其免受外在环境差异的影响，从而有助于减少团队间在相同基础设施上运行不同软件时的冲突

容器和虚拟机对比：

* 相同：容器和虚拟机具有相似的资源隔离和分配优势

* 不同：

  * 容器虚拟化的是操作系统，虚拟机虚拟化的是硬件。
  * 传统虚拟机可以运行不同的操作系统，容器只能运行同一类型操作系统

  ![](https://gitee.com/seazean/images/raw/master/Frame/Docker-容器和虚拟机对比.png)

  | 特性       | 容器               | 虚拟机     |
  | ---------- | ------------------ | ---------- |
  | 启动       | 秒级               | 分钟       |
  | 硬盘使用   | 一般为MB           | 一般为GB   |
  | 性能       | 接近原生           | 弱于原生   |
  | 系统支持量 | 单机支持上千个容器 | 一般几十个 |

  