# MySQL

## 简介

### 数据库

数据库：DataBase，简称 DB，用于存储和管理数据的仓库，它的存储空间很大，可以存放百万上亿条数据。

数据库的优势：

- 可以持久化存储数据
- 方便存储和管理数据
- 使用了统一的方式操作数据库 SQL

数据库、数据表、数据的关系介绍：

- 数据库

  - 用于存储和管理数据的仓库
  - 一个库中可以包含多个数据表

- 数据表

  - 数据库最重要的组成部分之一
  - 它由纵向的列和横向的行组成（类似 excel 表格）
  - 可以指定列名、数据类型、约束等
  - 一个表中可以存储多条数据

- 数据

  - 想要永久化存储的数据

  ![](https://gitee.com/seazean/images/raw/master/DB/数据库、数据表、数据之间的关系.png)



参考视频：https://www.bilibili.com/video/BV1zJ411M7TB（推荐观看）



***



### MySQL

MySQL数据库是一个最流行的关系型数据库管理系统之一

关系型数据库是将数据保存在不同的数据表中，而且表与表之间可以有关联关系，提高了灵活性。

缺点：数据存储在磁盘中，导致读写性能差，而且数据关系复杂，扩展性差

MySQL所使用的SQL语句是用于访问数据库最常用的标准化语言。

MySQL配置：

* MySQL 安装：https://www.jianshu.com/p/ba48f1e386f0

* MySQL 配置：

  * 修改 MySQL 默认字符集：安装 MySQL 之后第一件事就是修改字符集编码

    ```mysql
    vim /etc/mysql/my.cnf
    
    添加如下内容：
    [mysqld]
    character-set-server=utf8
    collation-server=utf8_general_ci
    
    [client]
    default-character-set=utf8
    ```

  * 启动 MySQL 服务： 

    ```shell
    systemctl start/restart mysql
    ```

  * 登录 MySQL：

    ```shell
    mysql -u root -p  敲回车，输入密码
    初始密码查看：cat /var/log/mysqld.log
    在root@localhost:   后面的就是初始密码
    ```
    
  * 查看默认字符集命令：

    ```mysql
    SHOW VARIABLES LIKE 'char%';
    ```

  * 修改MySQL登录密码：
    
    ```mysql
    set global validate_password_policy=0;
    set global validate_password_length=1;
      
    set password=password('密码');
    ```
    
  * 授予远程连接权限（MySQL 内输入）：
    
    ```mysql
    -- 授权
    grant all privileges on *.* to 'root' @'%' identified by '密码';
    -- 刷新
    flush privileges;
    ```
  
* 修改 MySQL 绑定 IP：

  ```shell
  cd /etc/mysql/mysql.conf.d
  sudo chmod 666 mysqld.cnf 
  vim mysqld.cnf 
  #bind-address = 127.0.0.1注释该行
  ```

* 关闭 Linux 防火墙

  ```shell
  systemctl stop firewalld.service
  放行3306端口
  ```

  

***



### 常用工具

#### mysql

mysql 不是指 mysql 服务，而是指 mysql 的客户端工具

```sh
mysql [options] [database]
```

* -u  --user=name：指定用户名
* -p  --password[=name]：指定密码
* -h  --host=name：指定服务器IP或域名
* -P  --port=#：指定连接端口
* -e  --execute=name：执行SQL语句并退出，在控制台执行SQL语句，而不用连接到数据库执行

示例：

```sh
mysql -h 127.0.0.1 -P 3306 -u root -p
mysql -uroot -p2143 db01 -e "select * from tb_book";
```



***



#### admin

mysqladmin 是一个执行管理操作的客户端程序，用来检查服务器的配置和当前状态、创建并删除数据库等

通过 `mysqladmin --help` 指令查看帮助文档

```sh
mysqladmin -uroot -p2143 create 'test01';
```



***



#### binlog

服务器生成的日志文件以二进制格式保存，如果需要检查这些文本，就要使用 mysqlbinlog 日志管理工具

```sh
mysqlbinlog [options]  log-files1 log-files2 ...
```

* -d  --database=name：指定数据库名称，只列出指定的数据库相关操作

* -o  --offset=#：忽略掉日志中的前n行命令。

* -r  --result-file=name：将输出的文本格式日志输出到指定文件。

* -s  --short-form：显示简单格式， 省略掉一些信息。

* --start-datatime=date1  --stop-datetime=date2：指定日期间隔内的所有日志。

* --start-position=pos1 --stop-position=pos2：指定位置间隔内的所有日志。



***



#### dump

##### 命令介绍

mysqldump 客户端工具用来备份数据库或在不同数据库之间进行数据迁移，备份内容包含创建表，及插入表的SQL语句

```sh
mysqldump [options] db_name [tables]
mysqldump [options] --database/-B db1 [db2 db3...]
mysqldump [options] --all-databases/-A
```

连接选项：

* -u  --user=name：指定用户名
* -p  --password[=name]：指定密码
* -h  --host=name：指定服务器IP或域名
* -P  --port=#：指定连接端口

输出内容选项：

* --add-drop-database：在每个数据库创建语句前加上 Drop database 语句
* --add-drop-table：在每个表创建语句前加上 Drop table 语句 , 默认开启 ; 不开启 (--skip-add-drop-table)
* -n  --no-create-db：不包含数据库的创建语句
* -t  --no-create-info：不包含数据表的创建语句
* -d --no-data：不包含数据
* -T, --tab=name：自动生成两个文件：一个.sql文件，创建表结构的语句；一个.txt文件，数据文件，相当于select into outfile  

示例：

```sh
mysqldump -uroot -p2143 db01 tb_book --add-drop-database --add-drop-table > a
mysqldump -uroot -p2143 -T /tmp test city
```



***



##### 数据备份

命令行方式：

* 备份命令：mysqldump -u root -p 数据库名称 > 文件保存路径

* 恢复
  1. 登录MySQL数据库：`mysql -u root p`
  2. 删除已经备份的数据库
  3. 重新创建与备份数据库名称相同的数据库
  4. 使用该数据库
  5. 导入文件执行：`source 备份文件全路径`



图形化界面：

* 备份

  ![图形化界面备份](https://gitee.com/seazean/images/raw/master/DB/图形化界面备份.png)

* 恢复

  ![图形化界面恢复](https://gitee.com/seazean/images/raw/master/DB/图形化界面恢复.png)





***



#### import

mysqlimport 是客户端数据导入工具，用来导入mysqldump 加 -T 参数后导出的文本文件

```sh
mysqlimport [options]  db_name  textfile1  [textfile2...]
```

示例：

```sh
mysqlimport -uroot -p2143 test /tmp/city.txt
```

导入 sql 文件，可以使用 MySQL 中的 source 指令 : 

```mysql
source 文件全路径
```



***



#### show

mysqlshow 客户端对象查找工具，用来很快地查找存在哪些数据库、数据库中的表、表中的列或者索引

```sh
mysqlshow [options] [db_name [table_name [col_name]]]
```

* --count：显示数据库及表的统计信息（数据库，表 均可以不指定）

* -i：显示指定数据库或者指定表的状态信息

示例：

```sh
#查询每个数据库的表的数量及表中记录的数量
mysqlshow -uroot -p1234 --count
#查询test库中每个表中的字段书，及行数
mysqlshow -uroot -p1234 test --count
#查询test库中book表的详细情况
mysqlshow -uroot -p1234 test book --count
```





***



## 体系结构

体系结构详解：

* 第一层：网络连接层
  * 一些客户端和链接服务，包含本地 socket 通信和大多数基于客户端/服务端工具实现的 TCP/IP 通信，主要完成一些类似于连接处理、授权认证、及相关的安全方案
  * 在该层上引入了线程池 Connection Pool 的概念，管理缓冲用户连接，线程处理等需要缓存的需求
  * 在该层上实现基于 SSL 的安全链接，服务器也会为安全接入的每个客户端验证它所具有的操作权限

- 第二层：核心服务层
  * 完成大多数核心服务功能，如 SQL接口，并完成缓存的查询，SQL的分析和优化：
    * Management Serveices & Utilities：系统管理和控制工具，备份、安全、复制、集群等
    * SQL Interface：接受用户的 SQL 命令，并且返回用户需要查询的结果
    * Parser：SQL 语句解析器
    * Optimizer：查询优化器，SQL 语句在查询之前会使用查询优化器进行优化，优化客户端查找请求，根据客户端请求的 query 语句和数据库中的一些统计信息进行分析，得出一个最优策略
    * Caches & Buffers：查询缓存，服务器会查询内部的缓存，如果缓存空间足够大，可以在大量读操作的环境中提升系统的性能
  * 所有**跨存储引擎**的功能在这一层实现，如存储过程、触发器、视图等
  * 在该层服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定表的查询顺序，是否利用索引等， 最后生成相应的执行操作
  * MySQL 中服务器层不管理事务，**事务是由存储引擎实现的**
- 第三层：存储引擎层
  - Pluggable Storage Engines：存储引擎接口，MySQL 区别于其他数据库的最重要的特点就是其插件式的表存储引擎（**存储引擎是基于表的，而不是数据库**）
  - 存储引擎真正的负责了 MySQL 中数据的存储和提取，服务器通过 API 和存储引擎进行通信
  - 不同的存储引擎具有不同的功能，可以根据开发的需要，来选取合适的存储引擎
- 第四层：系统文件层
  - 数据存储层，主要是将数据存储在文件系统之上，并完成与存储引擎的交互
  - File System：文件系统，保存配置文件、数据文件、日志文件、错误文件、二进制文件等

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-体系结构.png)





***



## 单表操作

### SQL

- SQL

  - Structured Query Language：结构化查询语言
  - 定义了操作所有关系型数据库的规则，每种数据库操作的方式可能会存在不一样的地方，称为“方言”

- SQL通用语法

  - SQL 语句可以单行或多行书写，以**分号结尾**。
  - 可使用空格和缩进来增强语句的可读性。
  - MySQL 数据库的 SQL 语句不区分大小写，**关键字建议使用大写**。
  - 数据库的注释：
    - 单行注释：-- 注释内容       #注释内容(mysql特有)
    - 多行注释：/* 注释内容 */

- SQL分类

  - DDL（Data Definition Language）数据定义语言

    - 用来定义数据库对象：数据库，表，列等。关键字：create、drop,、alter 等

  - DML（Data Manipulation Language）数据操作语言

    - 用来对数据库中表的数据进行增删改。关键字：insert、delete、update 等

  - DQL（Data Query Language）数据查询语言

    - 用来查询数据库中表的记录(数据)。关键字：select、where 等

  - DCL（Data Control Language）数据控制语言

    - 用来定义数据库的访问权限和安全级别，及创建用户。关键字：grant， revoke等

    ![](https://gitee.com/seazean/images/raw/master/Java/SQL分类.png)



***



### DDL

#### 数据库

* R(Retrieve)：查询

  * 查询所有数据库：

    ```mysql
    SHOW DATABASES;
    ```

  * 查询某个数据库的创建语句

    ```sql
    SHOW CREATE DATABASE 数据库名称;  -- 标准语法
    
    SHOW CREATE DATABASE mysql;     --查看mysql数据库的创建格式
    ```

    

* C(Create)：创建

  * 创建数据库

    ```mysql
    CREATE DATABASE 数据库名称;-- 标准语法
    
    CREATE DATABASE db1;     -- 创建db1数据库
    ```

  * 创建数据库（判断，如果不存在则创建）

    ```mysql
    CREATE DATABASE IF NOT EXISTS 数据库名称;
    ```

  * 创建数据库，并指定字符集

    ```mysql
    CREATE DATABASE 数据库名称 CHARACTER SET 字符集名称;
    ```

  * 例如：创建db4数据库、如果不存在则创建，指定字符集为gbk

    ```mysql
    -- 创建db4数据库、如果不存在则创建，指定字符集为gbk
    CREATE DATABASE IF NOT EXISTS db4 CHARACTER SET gbk;
    
    -- 查看db4数据库的字符集
    SHOW CREATE DATABASE db4;
    ```

    

* U(Update)：修改

  * 修改数据库的字符集

    ```mysql
    ALTER DATABASE 数据库名称 CHARACTER SET 字符集名称;
    ```

  * 常用字符集：

    ```mysql
    --查询所有支持的字符集
    SHOW CHARSET;
    --查看所有支持的校对规则
    SHOW COLLATION;
    
    -- 字符集: utf8,latinI,GBK,,GBK是utf8的子集
    -- 校对规则: ci 大小定不敏感，cs或bin大小写敏感
    ```

    

* D(Delete)：删除

  * 删除数据库：

    ```mysql
    DROP DATABASE 数据库名称;
    ```

  * 删除数据库(判断，如果存在则删除)：

    ```mysql
    DROP DATABASE IF EXISTS 数据库名称;
    ```
    
    

* 使用数据库：

  * 查询当前正在使用的数据库名称

    ```mysql
    SELECT DATABASE();
    ```

  * 使用数据库

    ```mysql
    USE 数据库名称； -- 标准语法
    USE db4;	   -- 使用db4数据库
    ```

    

#### 数据表

- R(Retrieve)：查询

  - 查询数据库中所有的数据表

    ```mysql
    USE mysql;-- 使用mysql数据库
    
    SHOW TABLES;-- 查询库中所有的表
    ```
  
  - 查询表结构

    ```mysql
  DESC 表名;
    ```
  
  - 查询表字符集
  
    ```mysql
    SHOW TABLE STATUS FROM 库名 LIKE '表名';
    ```

    

- C(Create)：创建

  - 创建数据表

    ```mysql
    CREATE TABLE 表名(
        列名1 数据类型1,
        列名2 数据类型2,
        ....
        列名n 数据类型n
    );
    -- 注意：最后一列，不需要加逗号
    ```

  - 复制表

    ```mysql
    CREATE TABLE 表名 LIKE 被复制的表名;  -- 标准语法
    
    CREATE TABLE product2 LIKE product; -- 复制product表到product2表
    ```

  - 数据类型

    | 数据类型  | 说明                                                         |
    | --------- | ------------------------------------------------------------ |
    | INT       | 整数类型                                                     |
    | DOUBLE    | 小数类型                                                     |
    | DATE      | 日期，只包含年月日：yyyy-MM-dd                               |
    | DATETIME  | 日期，包含年月日时分秒：yyyy-MM-dd HH:mm:ss                  |
    | TIMESTAMP | 时间戳类型，包含年月日时分秒：yyyy-MM-dd HH:mm:ss<br />如果不给这个字段赋值或赋值为null，则默认使用当前的系统时间 |
    | VARCHAR   | 字符串<br />name varchar(20):姓名最大20个字符：zhangsan8个字符,张三2个字符 |

    `INT(n)`：n代表位数

    * 3：int（9）显示结果为000000010
    * 3：int（3）显示结果为010
  
    `varchar(n)`：n表示的是字符数
  
  - 例如：
  
    ```mysql
    -- 使用db3数据库
    USE db3;
    
    -- 创建一个product商品表
    CREATE TABLE product(
    	id INT,				-- 商品编号
    	NAME VARCHAR(30),	-- 商品名称
    	price DOUBLE,		-- 商品价格
    	stock INT,			-- 商品库存
    	insert_time DATE    -- 上架时间
    );
    ```

​    

- U(Update)：修改

  - 修改表名

    ```mysql
    ALTER TABLE 表名 RENAME TO 新的表名;
    ```
  
  - 修改表的字符集
  
    ```mysql
  ALTER TABLE 表名 CHARACTER SET 字符集名称;
    ```

  - 添加一列
  
    ```mysql
    ALTER TABLE 表名 ADD 列名 数据类型;
    ```
  
  - 修改列数据类型
  
    ```mysql
    ALTER TABLE 表名 MODIFY 列名 新数据类型;
    ```

  - 修改列名称和数据类型

    ```mysql
    ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型;
    ```
  
  - 删除列
  
    ```mysql
    ALTER TABLE 表名 DROP 列名;
    ```

    
  
- D(Delete)：删除

  - 删除数据表

    ```mysql
    DROP TABLE 表名;
    ```
  
  - 删除数据表(判断，如果存在则删除)
  
    ```mysql
    DROP TABLE IF EXISTS 表名;
    ```
  
    

***



### DML

#### INSERT

* 新增表数据

  * 新增格式1：给指定列添加数据

    ```mysql
    INSERT INTO 表名(列名1,列名2...) VALUES (值1,值2...);
    ```

  * 新增格式2：默认给全部列添加数据

    ```mysql
    INSERT INTO 表名 VALUES (值1,值2,值3,...);
    ```

  * 新增格式3：批量添加数据

    ```mysql
    -- 给指定列批量添加数据
    INSERT INTO 表名(列名1,列名2,...) VALUES (值1,值2,...),(值1,值2,...)...;
    
    -- 默认给所有列批量添加数据 
    INSERT INTO 表名 VALUES (值1,值2,值3,...),(值1,值2,值3,...)...;
    ```

* 字符串拼接

  ```mysql
  CONCAT(string1,string2,'',...)
  ```
  
  
  
* 注意事项

  - 列名和值的数量以及数据类型要对应
  - 除了数字类型，其他数据类型的数据都需要加引号(单引双引都可以，推荐单引)





#### UPDATE

* 修改表数据语法

  * 标准语法

    ```mysql
    UPDATE 表名 SET 列名1 = 值1,列名2 = 值2,... [where 条件];
    ```

  * 修改电视的价格为1800、库存为36

    ```mysql
    UPDATE product SET price=1800,stock=36 WHERE NAME='电视';
    SELECT * FROM product;-- 查看所有商品信息
    ```

* 注意事项

  - 修改语句中必须加条件
  - 如果不加条件，则将所有数据都修改



#### DELETE

* 删除表数据语法

  ```mysql
  DELETE FROM 表名 [WHERE 条件];
  ```

* 注意事项
  * 删除语句中必须加条件
  * 如果不加条件，则将所有数据删除



​    

***



### DQL

#### 查询语法

数据库查询遵循条件在前的原则

```mysql
SELECT DISTINCT
	<select list>
FROM
	<left_table> <join_type>
JOIN
	<right_table> ON <join_condition>
WHERE
	<where_condition>
GROUP BY
	<group_by_list>
HAVING
	<having_condition>
ORDER BY
	<order_by_condition>
LIMIT
	<limit_params>
```

执行顺序：

```mysql
FROM	<left_table>

ON 		<join_condition>

<join_type>		JOIN	<right_table>

WHERE		<where_condition>

GROUP BY 	<group_by_list>

HAVING		<having_condition>

SELECT DISTINCT		<select list>

ORDER BY	<order_by_condition>

LIMIT		<limit_params>
```



***



#### 查询全部

* 查询全部的表数据

  ```mysql
  -- 标准语法
  SELECT * FROM 表名;
  
  -- 查询product表所有数据(常用)
  SELECT * FROM product;
  ```

* 查询指定字段的表数据

  ```mysql
  SELECT 列名1,列名2,... FROM 表名;
  ```

* 去除重复查询
  注意：只有值全部重复的才可以去除

  ```mysql
  SELECT DISTINCT 列名1,列名2,... FROM 表名;
  ```

* 计算列的值（四则运算）

  ```mysql
  SELECT 列名1 运算符(+ - * /) 列名2 FROM 表名;
  
  /*如果某一列值为null，可以进行替换
  	ifnull(表达式1,表达式2)
  	表达式1：想替换的列
  	表达式2：想替换的值*/
  ```

  例如：

  ```mysql
  -- 查询商品名称和库存，库存数量在原有基础上加10
  SELECT NAME,stock+10 FROM product;
  
  -- 查询商品名称和库存，库存数量在原有基础上加10。进行null值判断
  SELECT NAME,IFNULL(stock,0)+10 FROM product;
  ```

* **起别名**

  ```mysql
  SELECT 列名1,列名2,... AS 别名 FROM 表名;
  ```

  例如：

  ```mysql
  -- 查询商品名称和库存，库存数量在原有基础上加10。进行null值判断，起别名为getSum,AS可以省略。
  SELECT NAME,IFNULL(stock,0)+10 AS getsum FROM product;
  SELECT NAME,IFNULL(stock,0)+10 getsum FROM product;
  ```




***



#### 条件查询

* 条件查询语法

  ```mysql
  SELECT 列名 FROM 表名 WHERE 条件;
  ```

* 条件分类

  | 符号                | 功能                                                         |
  | ------------------- | ------------------------------------------------------------ |
  | >                   | 大于                                                         |
  | <                   | 小于                                                         |
  | >=                  | 大于等于                                                     |
  | <=                  | 小于等于                                                     |
  | =                   | 等于                                                         |
  | <> 或 !=            | 不等于                                                       |
  | BETWEEN ... AND ... | 在某个范围之内(都包含)                                       |
  | IN(...)             | 多选一                                                       |
  | LIKE                | **模糊查询**：_单个任意字符、%任意个字符、[] 匹配集合内的字符<br/>`LIKE '[^AB]%' `：不以 A 和 B 开头的任意文本 |
  | IS NULL             | 是NULL                                                       |
  | IS NOT NULL         | 不是NULL                                                     |
  | AND 或 &&           | 并且                                                         |
  | OR 或 \|\|          | 或者                                                         |
  | NOT 或 !            | 非，不是                                                     |
  | UNION               | 对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序 |
  | UNION ALL           | 对两个结果集进行并集操作，包括重复行，不进行排序             |

* 例如：

  ```mysql
  -- 查询库存大于20的商品信息
  SELECT * FROM product WHERE stock > 20;
  
  -- 查询品牌为华为的商品信息
  SELECT * FROM product WHERE brand='华为';
  
  -- 查询金额在4000 ~ 6000之间的商品信息
  SELECT * FROM product WHERE price >= 4000 AND price <= 6000;
  SELECT * FROM product WHERE price BETWEEN 4000 AND 6000;
  
  -- 查询库存为14、30、23的商品信息
  SELECT * FROM product WHERE stock=14 OR stock=30 OR stock=23;
  SELECT * FROM product WHERE stock IN(14,30,23);
  
  -- 查询库存为null的商品信息
  SELECT * FROM product WHERE stock IS NULL;
  -- 查询库存不为null的商品信息
  SELECT * FROM product WHERE stock IS NOT NULL;
  
  -- 查询名称以'小米'为开头的商品信息
  SELECT * FROM product WHERE NAME LIKE '小米%';
  
  -- 查询名称第二个字是'为'的商品信息
  SELECT * FROM product WHERE NAME LIKE '_为%';
  
  -- 查询名称为四个字符的商品信息 4个下划线
  SELECT * FROM product WHERE NAME LIKE '____';
  
  -- 查询名称中包含电脑的商品信息
  SELECT * FROM product WHERE NAME LIKE '%电脑%';
  ```

  <img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-DQL数据准备.png" style="zoom: 80%;" />



***



#### 函数查询

##### 聚合函数

聚合函数：将一列数据作为一个整体，进行纵向的计算

* 聚合函数语法

  ```mysql
  SELECT 函数名(列名) FROM 表名 [WHERE 条件]
  ```

* 聚合函数分类

  | 函数名      | 功能                             |
  | ----------- | -------------------------------- |
  | COUNT(列名) | 统计数量（一般选用不为null的列） |
  | MAX(列名)   | 最大值                           |
  | MIN(列名)   | 最小值                           |
  | SUM(列名)   | 求和                             |
  | AVG(列名)   | 平均值（AVG() 会忽略 NULL 行）   |

* 例如

  ```mysql
  -- 计算product表中总记录条数 7
  SELECT COUNT(*) FROM product;
  
  -- 获取最高价格
  SELECT MAX(price) FROM product;
  -- 获取最高价格的商品名称
  SELECT NAME,price FROM product WHERE price = (SELECT MAX(price) FROM product);
  
  -- 获取最低库存
  SELECT MIN(stock) FROM product;
  -- 获取最低库存的商品名称
  SELECT NAME,stock FROM product WHERE stock = (SELECT MIN(stock) FROM product);
  
  -- 获取总库存数量
  SELECT SUM(stock) FROM product;
  -- 获取品牌为小米的平均商品价格
  SELECT AVG(price) FROM product WHERE brand='小米';
  ```



***



##### 文本函数

CONCAT()：用于连接两个字段

```sql
SELECT CONCAT(TRIM(col1), '(', TRIM(col2), ')') AS concat_col FROM mytable
-- 许多数据库会使用空格把一个值填充为列宽，连接的结果出现一些不必要的空格，使用TRIM()可以去除首尾空格
```

| 函数名称  | 作 用                                                        |
| --------- | ------------------------------------------------------------ |
| LENGTH    | 计算字符串长度函数，返回字符串的字节长度                     |
| CONCAT    | 合并字符串函数，返回结果为连接参数产生的字符串，参数可以使一个或多个 |
| INSERT    | 替换字符串函数                                               |
| LOWER     | 将字符串中的字母转换为小写                                   |
| UPPER     | 将字符串中的字母转换为大写                                   |
| LEFT      | 从左侧字截取符串，返回字符串左边的若干个字符                 |
| RIGHT     | 从右侧字截取符串，返回字符串右边的若干个字符                 |
| TRIM      | 删除字符串左右两侧的空格                                     |
| REPLACE   | 字符串替换函数，返回替换后的新字符串                         |
| SUBSTRING | 截取字符串，返回从指定位置开始的指定长度的字符换             |
| REVERSE   | 字符串反转（逆序）函数，返回与原始字符串顺序相反的字符串     |



***



##### 数字函数

| 函数名称        | 作 用                                                      |
| --------------- | ---------------------------------------------------------- |
| ABS             | 求绝对值                                                   |
| SQRT            | 求二次方根                                                 |
| MOD             | 求余数                                                     |
| CEIL 和 CEILING | 两个函数功能相同，都是返回不小于参数的最小整数，即向上取整 |
| FLOOR           | 向下取整，返回值转化为一个BIGINT                           |
| RAND            | 生成一个0~1之间的随机数，传入整数参数是，用来产生重复序列  |
| ROUND           | 对所传参数进行四舍五入                                     |
| SIGN            | 返回参数的符号                                             |
| POW 和 POWER    | 两个函数的功能相同，都是所传参数的次方的结果值             |
| SIN             | 求正弦值                                                   |
| ASIN            | 求反正弦值，与函数 SIN 互为反函数                          |
| COS             | 求余弦值                                                   |
| ACOS            | 求反余弦值，与函数 COS 互为反函数                          |
| TAN             | 求正切值                                                   |
| ATAN            | 求反正切值，与函数 TAN 互为反函数                          |
| COT             | 求余切值                                                   |



***



##### 日期函数

| 函数名称                | 作 用                                                        |
| ----------------------- | ------------------------------------------------------------ |
| CURDATE 和 CURRENT_DATE | 两个函数作用相同，返回当前系统的日期值                       |
| CURTIME 和 CURRENT_TIME | 两个函数作用相同，返回当前系统的时间值                       |
| NOW 和  SYSDATE         | 两个函数作用相同，返回当前系统的日期和时间值                 |
| MONTH                   | 获取指定日期中的月份                                         |
| MONTHNAME               | 获取指定日期中的月份英文名称                                 |
| DAYNAME                 | 获取指定曰期对应的星期几的英文名称                           |
| DAYOFWEEK               | 获取指定日期对应的一周的索引位置值                           |
| WEEK                    | 获取指定日期是一年中的第几周，返回值的范围是否为 0〜52 或 1〜53 |
| DAYOFYEAR               | 获取指定曰期是一年中的第几天，返回值范围是1~366              |
| DAYOFMONTH              | 获取指定日期是一个月中是第几天，返回值范围是1~31             |
| YEAR                    | 获取年份，返回值范围是 1970〜2069                            |
| TIME_TO_SEC             | 将时间参数转换为秒数                                         |
| SEC_TO_TIME             | 将秒数转换为时间，与TIME_TO_SEC 互为反函数                   |
| DATE_ADD 和 ADDDATE     | 两个函数功能相同，都是向日期添加指定的时间间隔               |
| DATE_SUB 和 SUBDATE     | 两个函数功能相同，都是向日期减去指定的时间间隔               |
| ADDTIME                 | 时间加法运算，在原始时间上添加指定的时间                     |
| SUBTIME                 | 时间减法运算，在原始时间上减去指定的时间                     |
| DATEDIFF                | 获取两个日期之间间隔，返回参数 1 减去参数 2 的值             |
| DATE_FORMAT             | 格式化指定的日期，根据参数返回指定格式的值                   |
| WEEKDAY                 | 获取指定日期在一周内的对应的工作日索引                       |



****



#### 正则查询

正则表达式（Regular Expression）是指一个用来描述或者匹配一系列符合某个句法规则的字符串的单个字符串

```mysql
SELECT * FROM emp WHERE name REGEXP '^T';	-- 匹配以T开头的name值
SELECT * FROM emp WHERE name REGEXP '2$';	-- 匹配以2结尾的name值
SELECT * FROM emp WHERE name REGEXP '[uvw]';-- 匹配包含 uvw 的name值
```

| 符号   | 含义                          |
| ------ | ----------------------------- |
| ^      | 在字符串开始处进行匹配        |
| $      | 在字符串末尾处进行匹配        |
| .      | 匹配任意单个字符, 包括换行符  |
| [...]  | 匹配出括号内的任意字符        |
| [^...] | 匹配不出括号内的任意字符      |
| a*     | 匹配零个或者多个a(包括空串)   |
| a+     | 匹配一个或者多个a(不包括空串) |
| a?     | 匹配零个或者一个a             |
| a1\|a2 | 匹配a1或a2                    |
| a(m)   | 匹配m个a                      |
| a(m,)  | 至少匹配m个a                  |
| a(m,n) | 匹配m个a 到 n个a              |
| a(,n)  | 匹配0到n个a                   |
| (...)  | 将模式元素组成单一元素        |





***



#### 排序查询

* 排序查询语法

  ```mysql
  SELECT 列名 FROM 表名 [WHERE 条件] ORDER BY 列名1 排序方式1,列名2 排序方式2;
  ```

* 排序方式

  ```mysql
  ASC:升序
  DESC:降序
  ```

  注意：多个排序条件，当前边的条件值一样时，才会判断第二条件

* 例如

  ```mysql
  -- 按照库存升序排序
  SELECT * FROM product ORDER BY stock ASC;
  
  -- 查询名称中包含手机的商品信息。按照金额降序排序
  SELECT * FROM product WHERE NAME LIKE '%手机%' ORDER BY price DESC;
  
  -- 按照金额升序排序，如果金额相同，按照库存降序排列
  SELECT * FROM product ORDER BY price ASC,stock DESC;
  ```

  

***



#### 分组查询

* 分组查询语法

  ````mysql
  SELECT 列名 FROM 表名 [WHERE 条件] GROUP BY 分组列名 [HAVING 分组后条件过滤] [ORDER BY 排序列名 排序方式];
  ````

  WHERE 过滤行，HAVING 过滤分组，行过滤应当先于分组过滤

  分组规定：

  * GROUP BY 子句出现在 WHERE 子句之后，ORDER BY 子句之前
  * NULL 的行会单独分为一组
  * 大多数 SQL 实现不支持 GROUP BY 列具有可变长度的数据类型

* 例如

  ```mysql
  -- 按照品牌分组，获取每组商品的总金额
  SELECT brand,SUM(price) FROM product GROUP BY brand;
  
  -- 对金额大于4000元的商品，按照品牌分组,获取每组商品的总金额
  SELECT brand,SUM(price) FROM product WHERE price > 4000 GROUP BY brand;
  
  -- 对金额大于4000元的商品，按照品牌分组，获取每组商品的总金额，只显示总金额大于7000元的
  SELECT brand,SUM(price) AS getSum FROM product WHERE price > 4000 GROUP BY brand HAVING getSum > 7000;
  
  -- 对金额大于4000元的商品，按照品牌分组，获取每组商品的总金额，只显示总金额大于7000元的、并按照总金额的降序排列
  SELECT brand,SUM(price) AS getSum FROM product WHERE price > 4000 GROUP BY brand HAVING getSum > 7000 ORDER BY getSum DESC;
  ```




***



#### 分页查询

* 分页查询语法

  ```mysql
  SELECT 列名 FROM 表名 [WHERE 条件] GROUP BY 分组列名 [HAVING 分组后条件过滤] [ORDER BY 排序列名 排序方式] LIMIT 开始索引,查询条数;
  ```

* 公式：开始索引 = (当前页码-1) * 每页显示的条数

* 例如

  ```mysql
  SELECT * FROM product LIMIT 0,2;  -- 第一页 开始索引=(1-1) * 2
  SELECT * FROM product LIMIT 2,2;  -- 第二页 开始索引=(2-1) * 2
  SELECT * FROM product LIMIT 4,2;  -- 第三页 开始索引=(3-1) * 2
  SELECT * FROM product LIMIT 6,2;  -- 第四页 开始索引=(4-1) * 2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-DQL分页查询图解.png)






***



## 约束操作

### 约束分类

约束：对表中的数据进行限定，保证数据的正确性、有效性、完整性！

约束的分类：

| 约束                          | 说明           |
| ----------------------------- | -------------- |
| PRIMARY KEY                   | 主键约束       |
| PRIMARY KEY AUTO_INCREMENT    | 主键、自动增长 |
| UNIQUE                        | 唯一约束       |
| NOT NULL                      | 非空约束       |
| FOREIGN KEY                   | 外键约束       |
| FOREIGN KEY ON UPDATE CASCADE | 外键级联更新   |
| FOREIGN KEY ON DELETE CASCADE | 外键级联删除   |



***



### 主键约束

* 主键约束特点：

  * 主键约束默认包含**非空和唯一**两个功能
  * 一张表只能有一个主键
  * 主键一般用于表中数据的唯一标识

* 建表时添加主键约束

  ```mysql
  CREATE TABLE 表名(
  	列名 数据类型 PRIMARY KEY,
      列名 数据类型,
      ...
  );
  ```

* 删除主键约束

  ```mysql
  ALTER TABLE 表名 DROP PRIMARY KEY;
  ```

* 建表后单独添加主键约束

  ```mysql
  ALTER TABLE 表名 MODIFY 列名 数据类型 PRIMARY KEY;
  ```

* 例如

  ```mysql
  -- 创建student表
  CREATE TABLE student(
  	id INT PRIMARY KEY  -- 给id添加主键约束
  );
  
  -- 添加数据
  INSERT INTO student VALUES (1),(2);
  -- 主键默认唯一，添加重复数据，会报错
  INSERT INTO student VALUES (2);
  -- 主键默认非空，不能添加null的数据
  INSERT INTO student VALUES (NULL);
  ```



***



### 主键自增

主键自增约束可以为空，并自动增长。删除某条数据不影响自增的下一个数值，依然按照前一个值自增。

* 建表时添加主键自增约束

  ```mysql
  CREATE TABLE 表名(
  	列名 数据类型 PRIMARY KEY AUTO_INCREMENT,
      列名 数据类型,
      ...
  );
  ```

* 删除主键自增约束

  ```mysql
  ALTER TABLE 表名 MODIFY 列名 数据类型;
  ```

* 建表后单独添加主键自增约束

  ```mysql
  ALTER TABLE 表名 MODIFY 列名 数据类型 AUTO_INCREMENT;
  ```

* 例如

  ```mysql
  -- 创建student2表
  CREATE TABLE student2(
  	id INT PRIMARY KEY AUTO_INCREMENT    -- 给id添加主键自增约束
  );
  
  -- 添加数据
  INSERT INTO student2 VALUES (1),(2);
  -- 添加null值，会自动增长
  INSERT INTO student2 VALUES (NULL),(NULL);-- 3，4
  ```



***



### 唯一约束

唯一约束：约束不能有重复的数据

* 建表时添加唯一约束

  ```mysql
  CREATE TABLE 表名(
  	列名 数据类型 UNIQUE,
      列名 数据类型,
      ...
  );
  ```

* 删除唯一约束

  ```mysql
  ALTER TABLE 表名 DROP INDEX 列名;
  ```

* 建表后单独添加唯一约束

  ```mysql
  ALTER TABLE 表名 MODIFY 列名 数据类型 UNIQUE;
  ```

  

***



### 非空约束

* 建表时添加非空约束

  ```mysql
  CREATE TABLE 表名(
  	列名 数据类型 NOT NULL,
      列名 数据类型,
      ...
  );
  ```

* 删除非空约束

  ```mysql
  ALTER TABLE 表名 MODIFY 列名 数据类型;
  ```

* 建表后单独添加非空约束

  ```mysql
  ALTER TABLE 表名 MODIFY 列名 数据类型 NOT NULL;
  ```

 

***



### 外键约束

  外键约束：让表和表之间产生关系，从而保证数据的准确性！

* 建表时添加外键约束

  ```mysql
  CREATE TABLE 表名(
  	列名 数据类型 约束,
      ...
      CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主表主键列名)
  );
  ```

* 删除外键约束

  ```mysql
  ALTER TABLE 表名 DROP FOREIGN KEY 外键名;
  ```

* 建表后单独添加外键约束

  ```mysql
  ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主表主键列名);
  ```

* 例如

  ```mysql
  -- 创建user用户表
  CREATE TABLE USER(
  	id INT PRIMARY KEY AUTO_INCREMENT,    -- id
  	name VARCHAR(20) NOT NULL             -- 姓名
  );
  -- 添加用户数据
  INSERT INTO USER VALUES (NULL,'张三'),(NULL,'李四'),(NULL,'王五');
  
  -- 创建orderlist订单表
  CREATE TABLE orderlist(
  	id INT PRIMARY KEY AUTO_INCREMENT,    -- id
  	number VARCHAR(20) NOT NULL,          -- 订单编号
  	uid INT,                              -- 订单所属用户
  	CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id)   -- 添加外键约束
  );
  -- 添加订单数据
  INSERT INTO orderlist VALUES (NULL,'hm001',1),(NULL,'hm002',1),
  (NULL,'hm003',2),(NULL,'hm004',2),
  (NULL,'hm005',3),(NULL,'hm006',3);
  
  -- 添加一个订单，但是没有所属用户。无法添加
  INSERT INTO orderlist VALUES (NULL,'hm007',8);
  -- 删除王五这个用户，但是订单表中王五还有很多个订单呢。无法删除
  DELETE FROM USER WHERE NAME='王五';
  ```

  

### 外键级联

级联操作：当把主表中的数据进行删除或更新时，从表中有关联的数据的相应操作，包括 RESTRICT、CASCADE、SET NULL 和 NO ACTION

* RESTRICT 和 NO ACTION相同， 是指限制在子表有关联记录的情况下， 父表不能更新

* CASCADE 表示父表在更新或者删除时，更新或者删除子表对应的记录

* SET NULL 则表示父表在更新或者删除的时候，子表的对应字段被SET NULL

级联操作：

* 添加级联更新

  ```mysql
  ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主表主键列名) ON UPDATE [CASCADE | RESTRICT | SET NULL];
  ```

* 添加级联删除

  ```mysql
  ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主表主键列名) ON DELETE CASCADE;
  ```

* 同时添加级联更新和级联删除

  ```mysql
  ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主表主键列名) ON UPDATE CASCADE ON DELETE CASCADE;
  ```





***



## 多表操作

### 多表设计

#### 多表介绍

多表：有多张数据表，而表与表之间有一定的关联关系，通过外键约束实现

多表分类：一对一、一对多、多对多

#### 一对一

举例：人和身份证

实现原则：在任意一个表建立外键，去关联另外一个表的主键

```mysql
-- 创建person表
CREATE TABLE person(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(20)                        -- 姓名
);
-- 添加数据
INSERT INTO person VALUES (NULL,'张三'),(NULL,'李四');

-- 创建card表
CREATE TABLE card(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	number VARCHAR(20) UNIQUE NOT NULL,	-- 身份证号
	pid INT UNIQUE,                         -- 外键列
	CONSTRAINT cp_fk1 FOREIGN KEY (pid) REFERENCES person(id)
);
-- 添加数据
INSERT INTO card VALUES (NULL,'12345',1),(NULL,'56789',2);
```

![](https://gitee.com/seazean/images/raw/master/DB/多表设计一对一.png)



#### 一对多

举例：用户和订单、商品分类和商品

实现原则：在多的一方，建立外键约束，来关联一的一方主键

```mysql
-- 创建user表
CREATE TABLE USER(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(20)                        -- 姓名
);
-- 添加数据
INSERT INTO USER VALUES (NULL,'张三'),(NULL,'李四');

-- 创建orderlist表
CREATE TABLE orderlist(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	number VARCHAR(20),                     -- 订单编号
	uid INT,				-- 外键列
	CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id)
);
-- 添加数据
INSERT INTO orderlist VALUES (NULL,'hm001',1),(NULL,'hm002',1),(NULL,'hm003',2),(NULL,'hm004',2);
```

![多表设计一对多](https://gitee.com/seazean/images/raw/master/DB/多表设计一对多.png)



#### 多对多

举例：学生和课程。一个学生可以选择多个课程，一个课程也可以被多个学生选择

实现原则：借助第三张表中间表，中间表至少包含两个列，这两个列作为中间表的外键，分别关联两张表的主键

```mysql
-- 创建student表
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(20)			-- 学生姓名
);
-- 添加数据
INSERT INTO student VALUES (NULL,'张三'),(NULL,'李四');

-- 创建course表
CREATE TABLE course(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(10)			-- 课程名称
);
-- 添加数据
INSERT INTO course VALUES (NULL,'语文'),(NULL,'数学');

-- 创建中间表
CREATE TABLE stu_course(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	sid INT,  -- 用于和student表中的id进行外键关联
	cid INT,  -- 用于和course表中的id进行外键关联
	CONSTRAINT sc_fk1 FOREIGN KEY (sid) REFERENCES student(id), -- 添加外键约束
	CONSTRAINT sc_fk2 FOREIGN KEY (cid) REFERENCES course(id)   -- 添加外键约束
);
-- 添加数据
INSERT INTO stu_course VALUES (NULL,1,1),(NULL,1,2),(NULL,2,1),(NULL,2,2);
```

![](https://gitee.com/seazean/images/raw/master/DB/多表设计多对多.png)



***



### 多表查询

#### 查询格式

多表查询分类：

* 内连接查询
* 外连接查询
* 子查询
* 自关联查询

多表查询格式：（笛卡儿积）

```mysql
SELECT
	列名列表
FROM
	表名列表
WHERE
	条件...
```



***



#### 内连接

查询原理：内连接查询的是两张表有交集的部分数据

* 显式内连接

  ```mysql
  SELECT 列名 FROM 表名1 [INNER] JOIN 表名2 ON 条件;
  ```

* 隐式内连接

  ```mysql
  SELECT 列名 FROM 表名1,表名2 WHERE 条件;
  ```




***



#### 外连接

* 左外连接：查询左表的全部数据，和左右两张表有交集部分的数据

  ```mysql
  SELECT 列名 FROM 表名1 LEFT [OUTER] JOIN 表名2 ON 条件;
  ```

* 右外连接：查询右表的全部数据，和左右两张表有交集部分的数据

  ```mysql
  SELECT 列名 FROM 表名1 RIGHT [OUTER] JOIN 表名2 ON 条件;
  ```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-JOIN查询图.png)




***



#### 子查询

子查询概念：查询语句中嵌套了查询语句，**将嵌套查询称为子查询**

* 结果是单行单列：可以将查询的结果作为另一条语句的查询条件，使用运算符判断

  ```mysql
  SELECT 列名 FROM 表名 WHERE 列名=(SELECT 列名/聚合函数(列名) FROM 表名 [WHERE 条件]);
  ```

* 结果是多行单列：可以作为条件，使用运算符in或not in进行判断

  ```mysql
  SELECT 列名 FROM 表名 WHERE 列名 [NOT] IN (SELECT 列名 FROM 表名 [WHERE 条件]); 
  ```

* 结果是多行多列：查询的结果可以作为一张虚拟表参与查询

  ```mysql
  SELECT 列名 FROM 表名 [别名],(SELECT 列名 FROM 表名 [WHERE 条件]) [别名] [WHERE 条件];
  
  -- 查询订单表orderlist中id大于4的订单信息和所属用户USER信息
  SELECT 
  	* 
  FROM 
  	USER u,
  	(SELECT * FROM orderlist WHERE id>4) o 
  WHERE 
  	u.id=o.uid;
  ```




***



#### 自关联

自关联查询：同一张表中有数据关联。可以多次查询这同一个表！

* 数据准备

  ```mysql
  -- 创建员工表
  CREATE TABLE employee(
  	id INT PRIMARY KEY AUTO_INCREMENT,	-- 员工编号
  	NAME VARCHAR(20),					-- 员工姓名
  	mgr INT,							-- 上级编号
  	salary DOUBLE						-- 员工工资
  );
  -- 添加数据
  INSERT INTO employee VALUES (1001,'孙悟空',1005,9000.00),
  (1002,'猪八戒',1005,8000.00),
  (1003,'沙和尚',1005,8500.00),
  (1004,'小白龙',1005,7900.00),
  (1005,'唐僧',NULL,15000.00),
  (1006,'武松',1009,7600.00),
  (1007,'李逵',1009,7400.00),
  (1008,'林冲',1009,8100.00),
  (1009,'宋江',NULL,16000.00);
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/自关联查询数据准备.png)

* 数据查询

  ```mysql
  -- 查询所有员工的姓名及其直接上级的姓名，没有上级的员工也需要查询
  /*
  分析
  	员工信息 employee表
  	条件：employee.mgr = employee.id
  	查询左表的全部数据，和左右两张表有交集部分数据，左外连接
  */
  SELECT
  	e1.id,
  	e1.name,
  	e1.mgr,
  	e2.id,
  	e2.name
  FROM
  	employee e1
  LEFT OUTER JOIN
  	employee e2
  ON
  	e1.mgr = e2.id;	
  ```

* 查询结果

  ```
  id		name	mgr	   id	  name
  1001	孙悟空	  1005	1005	唐僧
  1002	猪八戒	  1005	1005	唐僧
  1003	沙和尚	  1005	1005	唐僧
  1004	小白龙	  1005	1005	唐僧
  1005	唐僧	   NULL	 NULL	 NULL
  1006	武松	   1009	 1009	 宋江
  1007	李逵	   1009	 1009	 宋江
  1008	林冲	   1009	 1009	 宋江
  1009	宋江	   NULL	 NULL	 NULL
  ```

  

***



### 多表练习

#### 数据准备

```mysql
-- 创建db4数据库
CREATE DATABASE db4;
-- 使用db4数据库
USE db4;

-- 创建user表
CREATE TABLE USER(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 用户id
	NAME VARCHAR(20),					-- 用户姓名
	age INT                             -- 用户年龄
);

-- 订单表
CREATE TABLE orderlist(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 订单id
	number VARCHAR(30),					-- 订单编号
	uid INT,   							-- 外键字段
	CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id)
);

-- 商品分类表
CREATE TABLE category(
	id INT PRIMARY KEY AUTO_INCREMENT,  -- 商品分类id
	NAME VARCHAR(10)                    -- 商品分类名称
);

-- 商品表
CREATE TABLE product(
	id INT PRIMARY KEY AUTO_INCREMENT,   -- 商品id
	NAME VARCHAR(30),                    -- 商品名称
	cid INT, -- 外键字段
	CONSTRAINT cp_fk1 FOREIGN KEY (cid) REFERENCES category(id)
);

-- 中间表
CREATE TABLE us_pro(
	upid INT PRIMARY KEY AUTO_INCREMENT,  -- 中间表id
	uid INT, 							  -- 外键字段。需要和用户表的主键产生关联
	pid INT,							  -- 外键字段。需要和商品表的主键产生关联
	CONSTRAINT up_fk1 FOREIGN KEY (uid) REFERENCES USER(id),
	CONSTRAINT up_fk2 FOREIGN KEY (pid) REFERENCES product(id)
);
```

![多表练习架构设计](https://gitee.com/seazean/images/raw/master/DB/多表练习架构设计.png)



#### 数据查询

1. 查询用户的编号、姓名、年龄、订单编号。
   分析：
   	数据：用户的编号、姓名、年龄在user表，订单编号在orderlist表
   	条件：user.id = orderlist.uid

   ```mysql
   SELECT
   	u.*,
   	o.number
   FROM
   	USER u,
   	orderlist o
   WHERE
   	u.id = o.uid;
   ```

2. 查询所有的用户，显示用户的编号、姓名、年龄、订单编号。

   ```mysql
   SELECT
   	u.*,
   	o.number
   FROM
   	USER u
   LEFT OUTER JOIN
   	orderlist o
   ON
   	u.id = o.uid;
   ```

3. 查询用户年龄大于23岁的信息，显示用户的编号、姓名、年龄、订单编号。

   ```mysql
   SELECT
   	u.*,
   	o.number
   FROM
   	USER u,
   	orderlist o
   WHERE
   	u.id = o.uid
   	AND
   	u.age > 23;
   ```

   ```mysql
   SELECT
   	u.*,
   	o.number
   FROM
   	(SELECT * FROM USER WHERE age > 23) u,-- 嵌套查询
   	orderlist o
   WHERE
   	u.id = o.uid;
   ```

4. 查询张三和李四用户的信息，显示用户的编号、姓名、年龄、订单编号。

   ````mysql
   SELECT
   	u.*,
   	o.number
   FROM
   	USER u,
   	orderlist o
   WHERE
   	u.id=o.uid
   	AND
   	u.name IN ('张三','李四');
   ````

5. 查询所有的用户和该用户能查看的所有的商品，显示用户的编号、姓名、年龄、商品名称。
   分析：
   	数据：用户的编号、姓名、年龄在user表，商品名称在product表，中间表 us_pro
   	条件：us_pro.uid = user.id AND us_pro.pid = product.id

   ```mysql
   SELECT
   	u.id,
   	u.name,
   	u.age,
   	p.name
   FROM
   	USER u,
   	product p,
   	us_pro up
   WHERE
   	up.uid = u.id
   	AND
   	up.pid=p.id;
   ```

6. 查询张三和李四这两个用户可以看到的商品，显示用户的编号、姓名、年龄、商品名称。

   ```mysql
   SELECT
   	u.id,
   	u.name,
   	u.age,
   	p.name
   FROM
   	USER u,
   	product p,
   	us_pro up
   WHERE
   	up.uid=u.id
   	AND
   	up.pid=p.id
   	AND
   	u.name IN ('张三','李四');
   ```






***



## 事务机制

### 事务介绍

事务（Transaction）是访问和更新数据库的程序执行单元；事务中可能包含一个或多个sql语句，这些语句要么都执行，要么都不执行。作为一个关系型数据库，MySQL支持事务。

单元中的每条 SQL 语句都相互依赖，形成一个整体

* 如果某条 SQL 语句执行失败或者出现错误，那么整个单元就会回滚，撤回到事务最初的状态

* 如果单元中所有的 SQL 语句都执行成功，则事务就顺利执行



***



### 管理事务

管理事务的三个步骤

1. 开启事务：记录回滚点，并通知服务器，将要执行一组操作，要么同时成功、要么同时失败

2. 执行sql语句：执行具体的一条或多条sql语句

3. 结束事务(提交|回滚)

   - 提交：没出现问题，数据进行更新
   - 回滚：出现问题，数据恢复到开启事务时的状态


事务操作：

* 开启事务

  ```mysql
  START TRANSACTION;
  ```

* 回滚事务

  ```mysql
  ROLLBACK;
  ```

* 提交事务

  ```mysql
  COMMIT;
  ```

  

* 管理实务演示

  ```mysql
  -- 开启事务
  START TRANSACTION;
  
  -- 张三给李四转账500元
  -- 1.张三账户-500
  UPDATE account SET money=money-500 WHERE NAME='张三';
  -- 2.李四账户+500
  UPDATE account SET money=money+500 WHERE NAME='李四';
  
  -- 回滚事务(出现问题)
  ROLLBACK;
  
  -- 提交事务(没出现问题)
  COMMIT;
  ```

  

***



### 提交方式

提交方式：

- 自动提交（MySQL默认为自动提交）
- 手动提交：

工作原理：

* 在自动提交模式下，如果没有 start transaction 显式地开始一个事务，那么每个sql语句都会被当做一个事务执行提交操作
* 在手动提交模式下，所有的 sql 语句都在一个事务中，直到执行了commit 或 rollback，该事务结束，同时开始了另外一个事务

* 存在一些特殊的命令，在事务中执行了这些命令会马上强制执行 COMMIT 提交事务，如DDL语句 (create table/drop table/alter/table)、lock tables 语句等

提交方式语法：

- 查看事务提交方式

  ```mysql
  SELECT @@AUTOCOMMIT;  -- 1代表自动提交    0代表手动提交
  ```

- 修改事务提交方式

  ```mysql
  SET @@AUTOCOMMIT=数字;	-- 系统
  SET AUTOCOMMIT=数字;		-- 会话
  ```



***



### 四大特征

#### ACID

事务的四大特征：ACID

- 原子性(atomicity)
- 一致性(consistency)
- 隔离性(isolaction)
- 持久性(durability)



***



#### 原子性

原子性是指事务是一个不可分割的工作单位，事务的操作如果成功就必须要完全应用到数据库，失败则不能对数据库有任何影响。比如事务中一个 SQL 语句执行失败，则已执行的语句也必须回滚，数据库退回到事务前的状态

InnoDB 存储引擎提供了两种事务日志：redo log（重做日志）和 undo log（回滚日志）

* redo log 用于保证事务持久性
* undo log 用于保证事务原子性和隔离性

undo log 属于逻辑日志，根据每行操作进行记录，记录了 SQL 执行相关的信息，用来回滚行记录到某个版本

当事务对数据库进行修改时，InnoDB 会生成对应的 undo log，如果事务执行失败或调用了 rollback 导致事务回滚，InnoDB 会根据 undo log 的内容**做与之前相反的操作**：

* 对于每个 insert，回滚时会执行 delete

* 对于每个 delete，回滚时会执行 insert

* 对于每个 update，回滚时会执行一个相反的 update，把数据修改回去

undo log 是采用段 (segment) 的方式来记录，每个 undo 操作在记录的时候占用一个 undo log segment

rollback segment 称为回滚段，每个回滚段中有 1024 个 undo log segment

* 在以前老版本，只支持1个 rollback segment，只能记录 1024 个 undo log segment
* MySQL5.5 开始支持 128 个 rollback segment，支持 128*1024 个 undo 操作



***



#### 一致性

一致性是指事务执行前后，数据库的完整性约束没有被破坏，事务执行的前后都是合法的数据状态。

数据库的完整性约束包括但不限于：实体完整性（如行的主键存在且唯一）、列完整性（如字段的类型、大小、长度要符合要求）、外键约束、用户自定义完整性（如转账前后，两个账户余额的和应该不变）

实现一致性的措施：

- 保证原子性、持久性和隔离性，如果这些特性无法保证，事务的一致性也无法保证
- 数据库本身提供保障，例如不允许向整形列插入字符串值、字符串长度不能超过列的限制等
- 应用层面进行保障，例如如果转账操作只扣除转账者的余额，而没有增加接收者的余额，无论数据库实现的多么完美，也无法保证状态的一致



***



#### 隔离性

隔离性是指，事务内部的操作与其他事务是隔离的，多个并发事务之间要相互隔离，不能互相干扰

* 严格的隔离性，对应了事务隔离级别中的 serializable，实际应用中对性能考虑很少使用可串行化

* 与原子性、持久性侧重于研究事务本身不同，隔离性研究的是不同事务之间的相互影响

隔离性让并发情形下的事务之间互不干扰：

- 一个事务的写操作对另一个事务的写操作（写写）：锁机制保证隔离性
- 一个事务的写操作对另一个事务的读操作（读写）：MVCC保证隔离性

锁机制：事务在修改数据之前，需要先获得相应的锁，获得锁之后，事务便可以修改数据；该事务操作期间，这部分数据是锁定的，其他事务如果需要修改数据，需要等待当前事务提交或回滚后释放锁（详解见锁机制）



***



#### 持久性

##### 实现原理

持久性是指一个事务一旦被提交了，那么对数据库中数据的改变就是永久性的，接下来的其他操作或故障不应该对其有任何影响。

redo log，记录**数据页的物理修改**，而不是某一行或某几行的修改，用来恢复提交后的物理数据页，且只能恢复到最后一次提交的位置

InnoDB 作为存储引擎，数据是存放在磁盘中，每次读写数据都需要磁盘 IO，效率会很低。InnoDB 提供了缓存 Buffer Pool，Buffer Pool 中包含了磁盘中部分数据页的映射，作为访问数据库的缓冲：

* 从数据库读取数据时，会首先从缓存中读取，如果缓存中没有，则从磁盘读取后放入 Buffer Pool
* 向数据库写入数据时，会首先写入缓存，缓存中修改的数据会**定期刷新**到磁盘，这一过程称为刷脏

Buffer Pool 的使用提高了读写数据的效率，但是也带了新的问题：如果 MySQL 宕机，此时 Buffer Pool 中修改的数据还没有刷新到磁盘，就会导致数据的丢失，事务的持久性无法保证，所以引入 redo log

* 当数据修改时，除了修改 Buffer Pool 中的数据，还会在 redo log 记录这次操作
* 当事务提交时，会调用 fsync 接口对 redo log 进行刷盘

* 如果 MySQL 宕机，重启时可以读取 redo log 中的数据，对数据库进行恢复

redo log 采用的是 WAL（Write-ahead logging，**预写式日志**），所有修改要先写入日志，再更新到 Buffer Pool，保证了数据不会因 MySQL 宕机而丢失，从而满足了持久性要求

redo log 也需要**在事务提交时**将日志写入磁盘，但是比将 Buffer Pool 中修改的数据写入磁盘快：

* 刷脏是随机 IO，因为每次修改的数据位置随机，但写 redo log 是尾部追加操作，属于顺序 IO
* 刷脏是以数据页 (Page) 为单位的，MySQL 默认页大小是 16KB，一个页上的一个小修改都要整页写入，而 redo log 中只包含真正需要写入的部分，无效 IO 大大减少

刷盘策略，通过修改参数 `innodb_flush_log_at_trx_commit` 设置：

* 0：表示当提交事务时，并不将缓冲区的 redo 日志写入磁盘，而是等待主线程每秒刷新一次
* 1：在事务提交时将缓冲区的 redo 日志**同步写入**到磁盘，保证一定会写入成功
* 2：在事务提交时将缓冲区的 redo 日志异步写入到磁盘，不能保证提交时肯定会写入，只是有这个动作



***



##### 日志对比

MySQL中还存在 binlog(二进制日志) 也可以记录写操作并用于数据的恢复，二者的区别是：

* 作用不同：redo log 是用于 crash recovery （故障恢复），保证MySQL宕机也不会影响持久性；binlog 是用于 point-in-time recovery 的，保证服务器可以基于时间点恢复数据，此外 binlog 还用于主从复制

* 层次不同：redo log 是 InnoDB 存储引擎实现的，而 binlog 是MySQL的服务器层实现的，同时支持 InnoDB 和其他存储引擎，并且二进制日志先于 redo log 被记录。

* 内容不同：redo log 是物理日志，内容基于磁盘的 Page；binlog 的内容是二进制的，根据 binlog_format 参数的不同，可能基于SQL 语句、基于数据本身或者二者的混合（日志部分详解）

* 写入时机不同：binlog 在事务提交时一次写入；redo log 的写入时机相对多元



参考文章：https://www.cnblogs.com/kismetv/p/10331633.html



***



### 隔离级别

事务的隔离级别：多个客户端操作时，各个客户端的事务之间应该是隔离的，**不同的事务之间不该互相影响**，而如果多个事务操作同一批数据时，则需要设置不同的隔离级别，否则就会产生问题。

隔离级别分类：

| 隔离级别         | 名称     | 会引发的问题           | 数据库默认隔离级别  |
| ---------------- | -------- | ---------------------- | ------------------- |
| read uncommitted | 读未提交 | 脏读、不可重复读、幻读 |                     |
| read committed   | 读已提交 | 不可重复读、幻读       | Oracle / SQL Server |
| repeatable read  | 可重复读 | 幻读                   | MySQL（避免幻读）   |
| serializable     | 串行化   | 无                     |                     |

一般来说，隔离级别越低，系统开销越低，可支持的并发越高，但隔离性也越差

* 丢失更新 (Lost Update)：当两个或多个事务选择同一行，最初的事务修改的值，被后面事务修改的值覆盖，所有的隔离级别都可以避免丢失更新

* 脏读 (Dirty Reads)：在事务中先后两次读取同一个数据，两次读取的结果不一样，原因是在一个事务处理过程中读取了另一个**未提交**的事务中的数据

* 不可重复读 (Non-Repeatable Reads)：在事务中先后两次读取同一个数据，两次读取的结果不一样，原因是在一个事务处理过程中读取了另一个事务中修改并**已提交**的数据

  > 可重复读的意思是不管读几次，结果都一样，可以重复的读

* 幻读 (Phantom Reads)：在事务中按某个条件先后两次查询数据库，两次查询结果的数量不同，**数据条目**发生了变化。比如查询某数据不存在，准备插入此记录，但执行插入时发现此记录已存在，无法插入；或查询某数据不存在，执行delete删除，却发现删除成功

**隔离级别操作语法：**

* 查询数据库隔离级别

  ```mysql
  SELECT @@TX_ISOLATION;
  SHOW VARIABLES LIKE 'tx_isolation';
  ```

* 修改数据库隔离级别

  ```mysql
  SET GLOBAL TRANSACTION ISOLATION LEVEL 级别字符串;
  ```



***



### 并发控制

#### MVCC

MVCC 全称 Multi-Version Concurrency Control，即多版本并发控制，用来解决**读写冲突**的无锁并发控制

MVCC 处理读写请求，可以做到在发生读写请求冲突时不用加锁，这个读是指的快照读，而不是当前读

* 快照读：实现基于 MVCC，因为是多版本并发，所以快照读读到的数据不一定是当前最新的数据，有可能是历史版本的数据
* 当前读：读取数据库记录是当前最新的版本，会对读取的数据进行加锁，防止其他事务修改数据，是悲观锁的一种操作，读写操作加共享锁或者排他锁和串行化事务的隔离级别都是当前读

数据库并发场景：

* 读-读：不存在任何问题，也不需要并发控制

* 读-写：有线程安全问题，可能会造成事务隔离性问题，可能遇到脏读，幻读，不可重复读

* 写-写：有线程安全问题，可能会存在丢失更新问题

MVCC 的优点：

* 在并发读写数据库时，做到在读操作时不用阻塞写操作，写操作也不用阻塞读操作，提高了并发读写的性能
* 可以解决脏读，不可重复读等事务隔离问题（加锁也能解决），但不能解决更新丢失问题

提高读写和写写的并发性能：

* MVCC + 悲观锁：MVCC 解决读写冲突，悲观锁解决写写冲突
* MVCC + 乐观锁：MVCC 解决读写冲突，乐观锁解决写写冲突



参考文章：https://www.jianshu.com/p/8845ddca3b23



***



#### 原理

##### 隐藏字段

实现原理主要是隐藏字段，undo日志，Read View 来实现的

数据库中的每行数据，除了自定义的字段，还有数据库隐式定义的字段：

* DB_TRX_ID：6byte，最近修改事务ID，记录创建该数据或最后一次修改（修改/插入）该数据的事务ID。当每个事务开启时，都会被分配一个ID，这个ID是递增的

* DB_ROLL_PTR：7byte，回滚指针，配合 undo 日志，指向上一个旧版本（存储在 rollback segment）

* DB_ROW_ID：6byte，隐含的自增ID（**隐藏主键**），如果数据表没有主键，InnoDB 会自动以 DB_ROW_ID 产生一个聚簇索引

* DELETED_BIT：删除标志的隐藏字段，记录被更新或删除并不代表真的删除，而是删除位变了

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MVCC版本链隐藏字段.png)







***



##### undo

undo log 是逻辑日志，保存修改行的数据的拷贝副本

undo log 的作用：

* 保证事务进行 rollback 时的原子性和一致性，当事务进行回滚的时候可以用 undo log 的数据进行恢复
* 用于 MVCC 快照读的数据，在 MVCC 多版本控制中，通过读取 undo log 的历史版本数据可以实现不同事务版本号都拥有自己独立的快照数据版本

undo log 主要分为两种：

* insert undo log：事务在 insert 新记录时产生的 undo log，只在事务回滚时需要，并且在事务提交后可以被立即丢弃

* update undo log：事务在进行 update 或 delete 时产生的 undo log，在事务回滚时需要，在快照读时也需要。不能随意删除，只有在快速读或事务回滚不涉及该日志时，对应的日志才会被 purge 线程统一清除

每次对数据库记录进行改动，都会将旧值放到一条undo日志中，算是该记录的一个旧版本，随着更新次数的增多，所有的版本都会被 roll_pointer 属性连接成一个链表，把这个链表称之为**版本链**，版本链的头节点就是当前记录最新的值，链尾就是最早的旧记录

<img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-MVCC版本链.png" style="zoom: 80%;" />

* 有个事务插入 persion 表一条新记录，name 为 Jerry，age 为 24

* 事务1修改该行数据时，数据库会先对该行加排他锁，然后先把该行数据拷贝到 undo log 中作为旧记录，拷贝完毕后修改该行 name 为Tom，并且修改隐藏字段的事务ID为当前事务1的ID（默认为 1 之后递增），回滚指针指向拷贝到 undo log 的副本记录，事务提交后，释放锁
* 以此类推

purge 线程：

为了实现 InnoDB 的 MVCC 机制，更新或者删除操作都只是设置一下老记录的 deleted_bit，并不真正将过时的记录删除，为了节省磁盘空间，InnoDB 有专门的 purge 线程来清理 deleted_bit 为true的记录，purge 线程维护了一个 Read view（这个 Read view 相当于系统中最老活跃事务的 Read view），如果某个记录的 deleted_bit 为true，并且 DB_TRX_ID 相对于 purge 线程的 Read view 可见，那么这条记录一定是可以被安全清除的



***



##### 读视图

Read View 是事务进行快照读操作时产生的读视图，该事务执行快照读的那一刻会生成数据库系统当前的一个快照，记录并维护系统当前活跃事务的ID，用来做可见性判断，根据视图判断当前事务能够看到哪个版本的数据

工作流程：将版本链的头节点的事务ID（最新数据事务ID）DB_TRX_ID 取出来，与系统当前活跃事务的 ID 对比，如果 DB_TRX_ID 不符合可见性，那就通过 DB_ROLL_PTR 回滚指针去取出 undo log 中的下一个 DB_TRX_ID 再比较，直到找到满足特定条件的 DB_TRX_ID，该事务 ID 所在的旧记录就是当前事务能看见的最新的记录

Read View 几个属性：

- m_ids：生成 Read View 时当前系统中活跃的事务 id 列表
- up_limit_id：生成 Read View 时当前系统中活跃的最小的事务 id，也就是 m_ids 中的最小值
- low_limit_id：生成 Read View 时系统应该分配给下一个事务的 id 值，m_ids 中的最大值加1
- creator_trx_id：生成该 Read View 的事务的事务id，就是判断该id的事务能读到什么数据

creator 创建一个 Read View，进行可见性算法分析：（**解决了读未提交**）

*  db_trx_id == creator_trx_id：表示这个数据就是当前事务自己生成的，自己生成的数据自己肯定能看见，所以这种情况下此数据对 creator 是可见的
* db_trx_id <  up_limit_id：该版本对应的事务 ID 小于 Read view 中的最小活跃事务ID，则这个事务在当前事务之前就已经被 COMMIT 了，对 creator 可见

* db_trx_id >= low_limit_id：该版本对应的事务 ID 大于 Read view 中当前系统的最大事务ID，则说明该数据是在当前 Read view 创建之后才产生的，对 creator 不可见
* up_limit_id <= db_trx_id < low_limit_id：判断 db_trx_id 是否在活跃事务列表 m_ids 中
  * 在列表中，说明该版本对应的事务正在运行，数据不能显示（不能读到未提交的数据）
  * 不在列表中，说明该版本对应的事务已经被提交，数据可以显示



***



##### 工作流程

表 user 数据

```sh
id		name		age
1		张三		   18	
```

Transaction 20：

```mysql
START TRANSACTION;	-- 开启事务
UPDATE user SET name = '李四' WHERE id = 1;
UPDATE user SET name = '王五' WHERE id = 1;
```

Transaction 60：

```mysql
START TRANSACTION;	-- 开启事务
-- 操作表的其他数据
```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MVCC工作流程1.png)

ID 为 0 的事务创建 Read View：

* m_ids：20、60
* up_limit_id：20
* low_limit_id：61
* creator_trx_id：0

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MVCC工作流程2.png)

只有红框部分才复合条件，所以只有张三对应的版本的数据可以被看到



参考视频：https://www.bilibili.com/video/BV1t5411u7Fg



***



#### RC RR

Read View 用于支持 RC（Read Committed，读已提交）和 RR（Repeatable Read，可重复读）隔离级别的实现

RR、RC 生成时机：

- RC 隔离级别下，每个快照读都会生成并获取最新的 Read View
- RR 隔离级别下，则是同一个事务中的第一个快照读才会创建 Read View

解决幻读问题：

- 快照读：通过 MVCC 来进行控制的，不用加锁，当前事务只生成一次 Read View，外部操作没有影响
- 当前读：通过 next-key 锁（行锁+间隙锁）来解决问题

RC、RR 级别下的 InnoDB 快照读区别

- RC 级别下的，事务中每次快照读都会新生成一个 Read View，这就是在 RC 级别下的事务中可以看到别的事务提交的更新的原因

- RR 级别下的某个事务的对某条记录的第一次快照读会创建一个 Read View， 将当前系统活跃的其他事务记录起来，此后在调用快照读的时候，使用的是同一个Read View，所以一个事务的查询结果每次都是相同的

  当前事务在其他事务提交之前使用过快照读，那么以后其他事务对数据的修改都是不可见的，就算以后其他事务提交了数据也不可见；早于 Read View 创建的事务所做的修改并提交的均是可见的





***



## 存储结构

### 视图

#### 基本介绍

视图概念：视图是一种虚拟存在的数据表，这个虚拟的表并不在数据库中实际存在

本质：将一条 SELECT 查询语句的结果封装到了一个虚拟表中，所以在创建视图的时候，工作重心要放在这条 SELECT 查询语句上

作用：将一些比较复杂的查询语句的结果，封装到一个虚拟表中，再有相同查询需求时，直接查询该虚拟表

优点：

* 简单：使用视图的用户不需要关心表的结构、关联条件和筛选条件，因为虚拟表中已经是过滤好的结果集
* 安全：使用视图的用户只能访问查询的结果集，对表的权限管理并不能限制到某个行某个列

* 数据独立，一旦视图的结构确定，可以屏蔽表结构变化对用户的影响，源表增加列对视图没有影响；源表修改列名，则可以通过修改视图来解决，不会造成对访问者的影响



***



#### 视图创建

* 创建视图

  ```mysql
  CREATE [OR REPLACE] 
  VIEW 视图名称 [(列名列表)] 
  AS 查询语句
  [WITH [CASCADED | LOCAL] CHECK OPTION];
  ```

  `WITH [CASCADED | LOCAL] CHECK OPTION` 决定了是否允许更新数据使记录不再满足视图的条件：

  * LOCAL：只要满足本视图的条件就可以更新
  * CASCADED：必须满足所有针对该视图的所有视图的条件才可以更新， 默认值

* 例如

  ```mysql
  -- 数据准备 city
  id	NAME	cid
  1	深圳	 	1
  2	上海		1
  3	纽约		2
  4	莫斯科	    3
  
  -- 数据准备 country
  id	NAME
  1	中国
  2	美国
  3	俄罗斯
  
  -- 创建city_country视图，保存城市和国家的信息(使用指定列名)
  CREATE 
  VIEW 
  	city_country (city_id,city_name,country_name)
  AS
      SELECT
          c1.id,
          c1.name,
          c2.name
      FROM
          city c1,
          country c2
      WHERE
          c1.cid=c2.id;
  ```

  

***



#### 视图查询

* 查询所有数据表，视图也会查询出来

  ```mysql
  SHOW TABLES;
  SHOW TABLE STATUS [\G];
  ```

* 查询视图

  ```mysql
  SELECT * FROM 视图名称;
  ```

* 查询某个视图创建

  ```mysql
  SHOW CREATE VIEW 视图名称;
  ```



***



#### 视图修改

视图表数据修改，会**自动修改源表中的数据**，因为更新的是视图中的基表中的数据

* 修改视图表中的数据

  ```mysql
  UPDATE 视图名称 SET 列名 = 值 WHERE 条件;
  ```

* 修改视图的结构

  ```mysql
  ALTER [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
  VIEW 视图名称 [(列名列表)] 
  AS 查询语句
  [WITH [CASCADED | LOCAL] CHECK OPTION]
  
  -- 将视图中的country_name修改为name
  ALTER 
  VIEW 
  	city_country (city_id,city_name,name) 
  AS
      SELECT
          c1.id,
          c1.name,
          c2.name
      FROM
          city c1,
          country c2
      WHERE
          c1.cid=c2.id;
  ```



***



#### 视图删除

* 删除视图

  ```mysql
  DROP VIEW 视图名称;
  ```

* 如果存在则删除

  ```mysql
  DROP VIEW IF EXISTS 视图名称;
  ```






***



### 存储过程

#### 基本介绍

存储过程和函数：存储过程和函数是事先经过编译并存储在数据库中的一段 SQL 语句的集合

存储过程和函数的好处：

* 提高代码的复用性
* 减少数据在数据库和应用服务器之间的传输，提高传输效率
* 减少代码层面的业务处理
* **一次编译永久有效**

存储过程和函数的区别：

* 存储函数必须有返回值
* 存储过程可以没有返回值



***



#### 基本操作

DELIMITER：

* DELIMITER关键字用来声明sql语句的分隔符，告诉MySQL该段命令已经结束

* MySQL语句默认的分隔符是分号，但是有时需要一条功能sql语句中包含分号，但是并不作为结束标识，这时使用DELIMITER来指定分隔符：

  ```mysql
  DELIMITER 分隔符
  ```

存储过程的创建调用查看和删除：

* 创建存储过程

  ```mysql
  -- 修改分隔符为$
  DELIMITER $
  
  -- 标准语法
  CREATE PROCEDURE 存储过程名称(参数...)
  BEGIN
  	sql语句;
  END$
  
  -- 修改分隔符为分号
  DELIMITER ;
  ```

* 调用存储过程

  ```mysql
  CALL 存储过程名称(实际参数);
  ```

* 查看存储过程

  ```mysql
  SELECT * FROM mysql.proc WHERE db='数据库名称';
  ```

* 删除存储过程

  ```mysql
  DROP PROCEDURE [IF EXISTS] 存储过程名称;
  ```

练习：

* 数据准备

  ```mysql
  id	NAME	age		gender	score
  1	张三		23		男		95
  2	李四		24		男		98
  3	王五		25		女		100
  4	赵六		26		女		90
  ```

* 创建stu_group()存储过程，封装分组查询总成绩，并按照总成绩升序排序的功能

  ```mysql
  DELIMITER $
  
  CREATE PROCEDURE stu_group()
  BEGIN
  	SELECT gender,SUM(score) getSum FROM student GROUP BY gender ORDER BY getSum ASC; 
  END$
  
  DELIMITER ;
  
  -- 调用存储过程
  CALL stu_group();
  -- 删除存储过程
  DROP PROCEDURE IF EXISTS stu_group;
  ```

  

***



#### 存储语法

##### 变量使用

存储过程是可以进行编程的，意味着可以使用变量、表达式、条件控制语句等，来完成比较复杂的功能

* 定义变量：DECLARE定义的是局部变量，只能用在BEGIN END范围之内
  
  ```mysql
  DECLARE 变量名 数据类型 [DEFAULT 默认值];
  ```
  
* 变量的赋值

  ```mysql
  SET 变量名 = 变量值;
  SELECT 列名 INTO 变量名 FROM 表名 [WHERE 条件];
  ```

* 数据准备：表student

  ```mysql
  id	NAME	age		gender	score
  1	张三		23		男		95
  2	李四		24		男		98
  3	王五		25		女		100
  4	赵六		26		女		90
  ```

* 定义两个int变量，用于存储男女同学的总分数

  ```mysql
  DELIMITER $
  CREATE PROCEDURE pro_test3()
  BEGIN
  	-- 定义两个变量
  	DECLARE men,women INT;
  	-- 查询男同学的总分数，为men赋值
  	SELECT SUM(score) INTO men FROM student WHERE gender='男';
  	-- 查询女同学的总分数，为women赋值
  	SELECT SUM(score) INTO women FROM student WHERE gender='女';
  	-- 使用变量
  	SELECT men,women;
  END$
  DELIMITER ;
  -- 调用存储过程
  CALL pro_test3();
  ```



***



##### IF语句

* if语句标准语法

  ```mysql
  IF 判断条件1 THEN 执行的sql语句1;
  [ELSEIF 判断条件2 THEN 执行的sql语句2;]
  ...
  [ELSE 执行的sql语句n;]
  END IF;
  ```

* 数据准备：表student

  ```mysql
  id	NAME	age		gender	score
  1	张三		23		男		95
  2	李四		24		男		98
  3	王五		25		女		100
  4	赵六		26		女		90
  ```
  
* 根据总成绩判断：全班380分及以上学习优秀、320 ~ 380学习良好、320以下学习一般

  ```mysql
  DELIMITER $
  CREATE PROCEDURE pro_test4()
  BEGIN
  	DECLARE total INT;							-- 定义总分数变量
  	DECLARE description VARCHAR(10);			-- 定义分数描述变量
  	SELECT SUM(score) INTO total FROM student; 	-- 为总分数变量赋值
  	-- 判断总分数
  	IF total >= 380 THEN
  		SET description = '学习优秀';
  	ELSEIF total >=320 AND total < 380 THEN
  		SET description = '学习良好';
  	ELSE
  		SET description = '学习一般';
  	END IF;
  END$
  DELIMITER ;
  -- 调用pro_test4存储过程
  CALL pro_test4();
  ```




***



##### 参数传递

* 参数传递的语法

  IN：代表输入参数，需要由调用者传递实际数据。默认的
  OUT：代表输出参数，该参数可以作为返回值
  INOUT：代表既可以作为输入参数，也可以作为输出参数

  ```mysql
  DELIMITER $
  
  -- 标准语法
  CREATE PROCEDURE 存储过程名称([IN|OUT|INOUT] 参数名 数据类型)
  BEGIN
  	执行的sql语句;
  END$
  
  DELIMITER ;
  ```

* 输入总成绩变量，代表学生总成绩，输出分数描述变量，代表学生总成绩的描述

  ```mysql
  DELIMITER $
  
  CREATE PROCEDURE pro_test6(IN total INT, OUT description VARCHAR(10))
  BEGIN
  	-- 判断总分数
  	IF total >= 380 THEN 
  		SET description = '学习优秀';
  	ELSEIF total >= 320 AND total < 380 THEN 
  		SET description = '学习不错';
  	ELSE 
  		SET description = '学习一般';
  	END IF;
  END$
  
  DELIMITER ;
  -- 调用pro_test6存储过程
  CALL pro_test6(310,@description);
  CALL pro_test6((SELECT SUM(score) FROM student), @description);
  -- 查询总成绩描述
  SELECT @description;
  ```

* 查看参数方法

  * @变量名 : 这种变量要在变量名称前面加上“@”符号，叫做用户会话变量，代表整个会话过程他都是有作用的，类似于全局变量
  * @@变量名 : 这种在变量前加上 "@@" 符号, 叫做系统变量 

 

***



##### CASE

* 标准语法1

  ```mysql
  CASE 表达式
      WHEN 值1 THEN 执行sql语句1;
      [WHEN 值2 THEN 执行sql语句2;]
      ...
      [ELSE 执行sql语句n;]
  END CASE;
  ```

* 标准语法2

  ```mysql
  sCASE
      WHEN 判断条件1 THEN 执行sql语句1;
      [WHEN 判断条件2 THEN 执行sql语句2;]
      ...
      [ELSE 执行sql语句n;]
  END CASE;
  ```

* 演示

  ```mysql
  DELIMITER $
  CREATE PROCEDURE pro_test7(IN total INT)
  BEGIN
  	-- 定义变量
  	DECLARE description VARCHAR(10);
  	-- 使用case判断
  	CASE
  	WHEN total >= 380 THEN
  		SET description = '学习优秀';
  	WHEN total >= 320 AND total < 380 THEN
  		SET description = '学习不错';
  	ELSE 
  		SET description = '学习一般';
  	END CASE;
  	
  	-- 查询分数描述信息
  	SELECT description;
  END$
  DELIMITER ;
  -- 调用pro_test7存储过程
  CALL pro_test7(390);
  CALL pro_test7((SELECT SUM(score) FROM student));
  ```



***



##### WHILE

* while循环语法

  ```mysql
  WHILE 条件判断语句 DO
  	循环体语句;
  	条件控制语句;
  END WHILE;
  ```
  
* 计算1~100之间的偶数和

  ```mysql
  DELIMITER $
  CREATE PROCEDURE pro_test6()
  BEGIN
  	-- 定义求和变量
  	DECLARE result INT DEFAULT 0;
  	-- 定义初始化变量
  	DECLARE num INT DEFAULT 1;
  	-- while循环
  	WHILE num <= 100 DO
  		IF num % 2 = 0 THEN
  			SET result = result + num;
  		END IF;
  		SET num = num + 1;
  	END WHILE;
  	-- 查询求和结果
  	SELECT result;
  END$
  DELIMITER ;
  
  -- 调用pro_test6存储过程
  CALL pro_test6();
  ```



***



##### REPEAT

* repeat循环标准语法

  ```mysql
  初始化语句;
  REPEAT
  	循环体语句;
  	条件控制语句;
  	UNTIL 条件判断语句
  END REPEAT;
  ```

* 计算1~10之间的和

  ```mysql
  DELIMITER $
  CREATE PROCEDURE pro_test9()
  BEGIN
  	-- 定义求和变量
  	DECLARE result INT DEFAULT 0;
  	-- 定义初始化变量
  	DECLARE num INT DEFAULT 1;
  	-- repeat循环
  	REPEAT
  		-- 累加
  		SET result = result + num;
  		-- 让num+1
  		SET num = num + 1;
  		-- 停止循环
  		UNTIL num > 10
  	END REPEAT;
  	-- 查询求和结果
  	SELECT result;
  END$
  
  DELIMITER ;
  -- 调用pro_test9存储过程
  CALL pro_test9();
  ```




***



##### LOOP

LOOP 实现简单的循环，退出循环的条件需要使用其他的语句定义，通常可以使用 LEAVE 语句实现，如果不加退出循环的语句，那么就变成了死循环

* loop循环标准语法

  ```mysql
  [循环名称:] LOOP
  	条件判断语句
  		[LEAVE 循环名称;]
  	循环体语句;
  	条件控制语句;
  END LOOP 循环名称;
  ```
  
* 计算1~10之间的和

  ```mysql
  DELIMITER $
  CREATE PROCEDURE pro_test10()
  BEGIN
  	-- 定义求和变量
  	DECLARE result INT DEFAULT 0;
  	-- 定义初始化变量
  	DECLARE num INT DEFAULT 1;
  	-- loop循环
  	l:LOOP
  		-- 条件成立，停止循环
  		IF num > 10 THEN
  			LEAVE l;
  		END IF;
  		-- 累加
  		SET result = result + num;
  		-- 让num+1
  		SET num = num + 1;
  	END LOOP l;
  	-- 查询求和结果
  	SELECT result;
  END$
  DELIMITER ;
  -- 调用pro_test10存储过程
  CALL pro_test10();
  ```



***



##### 游标

游标是用来存储查询结果集的数据类型，在存储过程和函数中可以使用光标对结果集进行循环的处理
* 游标可以遍历返回的多行结果，每次拿到一整行数据
* 简单来说游标就类似于集合的迭代器遍历
* MySQL中的游标只能用在存储过程和函数中

游标的语法

* 创建游标

  ```mysql
  DECLARE 游标名称 CURSOR FOR 查询sql语句;
  ```

* 打开游标

  ```mysql
  OPEN 游标名称;
  ```

* 使用游标获取数据

  ```mysql
  FETCH 游标名称 INTO 变量名1,变量名2,...;
  ```

* 关闭游标

  ```mysql
  CLOSE 游标名称;
  ```

* Mysql 通过一个 Error handler 声明来判断指针是否到尾部，并且必须和创建游标的 SQL 语句声明在一起：

   ```mysql
   DECLARE EXIT HANDLER FOR NOT FOUND (do some action，一般是设置标志变量)
   ```

  

游标的基本使用

* 数据准备：表 student

  ```mysql
  id	NAME	age		gender	score
  1	张三		23		男		95
  2	李四		24		男		98
  3	王五		25		女		100
  4	赵六		26		女		90
  ```

* 创建 stu_score 表

  ```mysql
  CREATE TABLE stu_score(
  	id INT PRIMARY KEY AUTO_INCREMENT,
  	score INT
  );
  ```

* 将student表中所有的成绩保存到stu_score表中

  ```mysql
  DELIMITER $
  
  CREATE PROCEDURE pro_test12()
  BEGIN
  	-- 定义成绩变量
  	DECLARE s_score INT;
  	-- 定义标记变量
  	DECLARE flag INT DEFAULT 0;
  	
  	-- 创建游标，查询所有学生成绩数据
  	DECLARE stu_result CURSOR FOR SELECT score FROM student;
  	-- 游标结束后，将标记变量改为1  这两个必须声明在一起
  	DECLARE EXIT HANDLER FOR NOT FOUND SET flag = 1;
  	
  	-- 开启游标
  	OPEN stu_result;
  	-- 循环使用游标
  	REPEAT
  		-- 使用游标，遍历结果,拿到数据
  		FETCH stu_result INTO s_score;
  		-- 将数据保存到stu_score表中
  		INSERT INTO stu_score VALUES (NULL,s_score);
  	UNTIL flag=1
  	END REPEAT;
  	-- 关闭游标
  	CLOSE stu_result;
  END$
  
  DELIMITER ;
  
  -- 调用pro_test12存储过程
  CALL pro_test12();
  -- 查询stu_score表
  SELECT * FROM stu_score;
  ```
  
  



***



#### 存储函数

存储函数和存储过程是非常相似的。存储函数可以做的事情，存储过程也可以做到！

存储函数有返回值，存储过程没有返回值(参数的out其实也相当于是返回数据了)

* 创建存储函数

  ```mysql
  DELIMITER $
  -- 标准语法
  CREATE FUNCTION 函数名称(参数 数据类型)
  RETURNS 返回值类型
  BEGIN
  	执行的sql语句;
  	RETURN 结果;
  END$
  
  DELIMITER ;
  ```

* 调用存储函数，因为有返回值，所以使用 SELECT 调用

  ```mysql
  SELECT 函数名称(实际参数);
  ```

* 删除存储函数

  ```mysql
  DROP FUNCTION 函数名称;
  ```

* 定义存储函数，获取学生表中成绩大于95分的学生数量

  ```mysql
  DELIMITER $
  CREATE FUNCTION fun_test()
  RETURN INT
  BEGIN
  	-- 定义统计变量
  	DECLARE result INT;
  	-- 查询成绩大于95分的学生数量，给统计变量赋值
  	SELECT COUNT(score) INTO result FROM student WHERE score > 95;
  	-- 返回统计结果
  	SELECT result;
  END
  DELIMITER ;
  -- 调用fun_test存储函数
  SELECT fun_test();
  ```





***



### 触发器

#### 基本介绍

触发器是与表有关的数据库对象，在insert/update/delete 之前或之后触发并执行触发器中定义的SQL语句

* 触发器的这种特性可以协助应用在数据库端确保数据的完整性 、日志记录 、数据校验等操作

- 使用别名 NEW 和 OLD 来引用触发器中发生变化的记录内容，这与其他的数据库是相似的
- 现在触发器还只支持行级触发，不支持语句级触发

| 触发器类型      | OLD的含义                      | NEW的含义                      |
| --------------- | ------------------------------ | ------------------------------ |
| INSERT 型触发器 | 无 (因为插入前状态无数据)      | NEW 表示将要或者已经新增的数据 |
| UPDATE 型触发器 | OLD 表示修改之前的数据         | NEW 表示将要或已经修改后的数据 |
| DELETE 型触发器 | OLD 表示将要或者已经删除的数据 | 无 (因为删除后状态无数据)      |



***



#### 基本操作

* 创建触发器

  ```mysql
  DELIMITER $
  
  CREATE TRIGGER 触发器名称
  BEFORE|AFTER  INSERT|UPDATE|DELETE
  ON 表名
  [FOR EACH ROW]  -- 行级触发器
  BEGIN
  	触发器要执行的功能;
  END$
  
  DELIMITER ;
  ```

* 查看触发器的状态、语法等信息

  ```mysql
  SHOW TRIGGERS;
  ```

* 删除触发器，如果没有指定 schema_name，默认为当前数据库

  ```mysql
  DROP TRIGGER [schema_name.]trigger_name;
  ```

  

***



#### 触发演示

通过触发器记录账户表的数据变更日志。包含：增加、修改、删除

* 数据准备

  ```mysql
  -- 创建db9数据库
  CREATE DATABASE db9;
  -- 使用db9数据库
  USE db9;
  ```

  ```mysql
  -- 创建账户表account
  CREATE TABLE account(
  	id INT PRIMARY KEY AUTO_INCREMENT,	-- 账户id
  	NAME VARCHAR(20),					-- 姓名
  	money DOUBLE						-- 余额
  );
  -- 添加数据
  INSERT INTO account VALUES (NULL,'张三',1000),(NULL,'李四',2000);
  ```

  ```mysql
  -- 创建日志表account_log
  CREATE TABLE account_log(
  	id INT PRIMARY KEY AUTO_INCREMENT,	-- 日志id
  	operation VARCHAR(20),				-- 操作类型 (insert update delete)
  	operation_time DATETIME,			-- 操作时间
  	operation_id INT,					-- 操作表的id
  	operation_params VARCHAR(200)       -- 操作参数
  );
  ```

* 创建INSERT型触发器

  ```mysql
  DELIMITER $
  
  CREATE TRIGGER account_insert
  AFTER INSERT
  ON account
  FOR EACH ROW
  BEGIN
  	INSERT INTO account_log VALUES (NULL,'INSERT',NOW(),new.id,CONCAT('插入后{id=',new.id,',name=',new.name,',money=',new.money,'}'));
  END$
  
  DELIMITER ;
  ```

  ```mysql
  -- 向account表添加记录
  INSERT INTO account VALUES (NULL,'王五',3000);
  
  -- 查询日志表
  SELECT * FROM account_log;
  /*
  id	operation	operation_time		operation_id	operation_params
  1	INSERT	   	2021-01-26 19:51:11		3	     插入后{id=3,name=王五money=2000}
  */
  ```

  

* 创建UPDATE型触发器

  ```mysql
  DELIMITER $
  
  CREATE TRIGGER account_update
  AFTER UPDATE
  ON account
  FOR EACH ROW
  BEGIN
  	INSERT INTO account_log VALUES (NULL,'UPDATE',NOW(),new.id,CONCAT('修改前{id=',old.id,',name=',old.name,',money=',old.money,'}','修改后{id=',new.id,',name=',new.name,',money=',new.money,'}'));
  END$
  
  DELIMITER ;
  ```

  ```mysql
  -- 修改account表
  UPDATE account SET money=3500 WHERE id=3;
  
  -- 查询日志表
  SELECT * FROM account_log;
  /*
  id	operation	operation_time		operation_id	  operation_params
  2	UPDATE	   	2021-01-26 19:58:54		2		 更新前{id=2,name=李四money=1000}
  												 更新后{id=2,name=李四money=200}
  */
  ```

  

* 创建DELETE型触发器

  ```mysql
  DELIMITER $
  
  CREATE TRIGGER account_delete
  AFTER DELETE
  ON account
  FOR EACH ROW
  BEGIN
  	INSERT INTO account_log VALUES (NULL,'DELETE',NOW(),old.id,CONCAT('删除前{id=',old.id,',name=',old.name,',money=',old.money,'}'));
  END$
  
  DELIMITER ;
  ```

  ```mysql
  -- 删除account表数据
  DELETE FROM account WHERE id=3;
  
  -- 查询日志表
  SELECT * FROM account_log;
  /*
  id	operation	operation_time		operation_id	operation_params
  3	DELETE		2021-01-26 20:02:48		3	    删除前{id=3,name=王五money=2000}
  */
  ```





***



## 存储引擎

### 基本介绍

对比其他数据库，MySQL 的架构可以在不同场景应用并发挥良好作用，主要体现在存储引擎，插件式的存储引擎架构将查询处理和其他的系统任务以及数据的存储提取分离，可以针对不同的存储需求可以选择最优的存储引擎

存储引擎的介绍：

- MySQL 数据库使用不同的机制存取表文件 , 机制的差别在于不同的存储方式、索引技巧、锁定水平等不同的功能和能力，在 MySQL 中，将这些不同的技术及配套的功能称为**存储引擎**
- Oracle , SqlServer 等数据库只有一种存储引擎，MySQL 提供了插件式的存储引擎架构，所以 MySQL 存在多种存储引擎 , 就会让数据库采取了不同的处理数据的方式和扩展功能
- 在关系型数据库中数据的存储是以表的形式存进行，所以存储引擎也称为表类型（存储和操作此表的类型）
- 通过选择不同的引擎，能够获取最佳的方案,  也能够获得额外的速度或者功能，提高程序的整体效果。

MySQL支持的存储引擎：

- MySQL 支持的引擎包括：InnoDB、MyISAM、MEMORY、Archive、Federate、CSV、BLACKHOLE 等
- MySQL5.5 之前的默认存储引擎是 MyISAM，5.5 之后就改为了 InnoDB



****



### 引擎对比

MyISAM 存储引擎：

* 特点：不支持事务和外键，读取速度快，节约资源
* 应用场景：查询和插入操作为主，只有很少更新和删除操作，并对事务的完整性、并发性要求不高
* 存储方式：
  * 每个 MyISAM 在磁盘上存储成 3 个文件，其文件名都和表名相同，拓展名不同
  * 表的定义保存在 .frm 文件，表数据保存在 .MYD (MYData) 文件中，索引保存在 .MYI (MYIndex) 文件中

InnoDB 存储引擎：(MySQL5.5版本后默认的存储引擎)

- 特点：**支持事务**和外键操作，支持并发控制。对比 MyISAM 的存储引擎，InnoDB 写的处理效率差一些，并且会占用更多的磁盘空间以保留数据和索引
- 应用场景：对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，读写频繁的操作
- 存储方式：
  - 使用共享表空间存储， 这种方式创建的表的表结构保存在 .frm 文件中， 数据和索引保存在 innodb_data_home_dir 和 innodb_data_file_path 定义的表空间中，可以是多个文件
  - 使用多表空间存储，创建的表的表结构存在 .frm 文件中，每个表的数据和索引单独保存在 .ibd 中

MEMORY 存储引擎：

- 特点：每个 MEMORY 表实际对应一个磁盘文件 ，该文件中只存储表的结构，表数据保存在内存中，且默认使用 HASH 索引，这样有利于数据的快速处理，在需要快速定位记录可以提供更快的访问，但是服务一旦关闭，表中的数据就会丢失，数据存储不安全
- 应用场景：通常用于更新不太频繁的小表，用以快速得到访问结果，类似缓存
- 存储方式：表结构保存在 .frm 中

MERGE存储引擎：

* 特点：

  * 是一组 MyISAM 表的组合，这些 MyISAM 表必须结构完全相同，通过将不同的表分布在多个磁盘上
  * MERGE 表本身并没有存储数据，对 MERGE 类型的表可以进行查询、更新、删除操作，这些操作实际上是对内部的 MyISAM 表进行的

* 应用场景：将一系列等同的 MyISAM 表以逻辑方式组合在一起，并作为一个对象引用他们，适合做数据仓库

* 操作方式：

  * 插入操作是通过 INSERT_METHOD 子句定义插入的表，使用 FIRST 或 LAST 值使得插入操作被相应地作用在第一或者最后一个表上；不定义这个子句或者定义为 NO，表示不能对 MERGE 表执行插入操作
  * 对 MERGE 表进行 DROP 操作，但是这个操作只是删除MERGE表的定义，对内部的表是没有任何影响的

  ```mysql
  CREATE TABLE order_1(
  )ENGINE = MyISAM DEFAULT CHARSET=utf8;
  
  CREATE TABLE order_2(
  )ENGINE = MyISAM DEFAULT CHARSET=utf8;
  
  CREATE TABLE order_all(
  	-- 结构与MyISAM表相同
  )ENGINE = MERGE UNION = (order_1,order_2) INSERT_METHOD=LAST DEFAULT CHARSET=utf8;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MERGE.png)

| 特性         | MyISAM                       | InnoDB        | MEMORY             |
| ------------ | ---------------------------- | ------------- | ------------------ |
| 存储限制     | 有(平台对文件系统大小的限制) | 64TB          | 有(平台的内存限制) |
| **事务安全** | **不支持**                   | **支持**      | **不支持**         |
| **锁机制**   | **表锁**                     | **表锁/行锁** | **表锁**           |
| B+Tree索引   | 支持                         | 支持          | 支持               |
| 哈希索引     | 不支持                       | 不支持        | 支持               |
| 全文索引     | 支持                         | 支持          | 不支持             |
| 集群索引     | 不支持                       | 支持          | 不支持             |
| 数据索引     | 不支持                       | 支持          | 支持               |
| 数据缓存     | 不支持                       | 支持          | N/A                |
| 索引缓存     | 支持                         | 支持          | N/A                |
| 数据可压缩   | 支持                         | 不支持        | 不支持             |
| 空间使用     | 低                           | 高            | N/A                |
| 内存使用     | 低                           | 高            | 中等               |
| 批量插入速度 | 高                           | 低            | 高                 |
| **外键**     | **不支持**                   | **支持**      | **不支持**         |

面试问题：MyIsam 和 InnoDB 的区别？

* 事务：InnoDB 支持事务，MyISAM 不支持事务
* 外键：InnoDB 支持外键，MyISAM 不支持外键
* 索引：InnoDB 是聚集（聚簇）索引，MyISAM 是非聚集（非聚簇）索引

* 锁粒度：InnoDB 最小的锁粒度是行锁，MyISAM 最小的锁粒度是表锁
* 存储结构：参考本节上半部分



***



### 引擎操作

* 查询数据库支持的存储引擎

  ```mysql
  SHOW ENGINES;
  SHOW VARIABLES LIKE '%storage_engine%'; -- 查看Mysql数据库默认的存储引擎 
  ```

* 查询某个数据库中所有数据表的存储引擎

  ```mysql
  SHOW TABLE STATUS FROM 数据库名称;
  ```

* 查询某个数据库中某个数据表的存储引擎

  ```mysql
  SHOW TABLE STATUS FROM 数据库名称 WHERE NAME = '数据表名称';
  ```

* 创建数据表，指定存储引擎

  ```mysql
  CREATE TABLE 表名(
  	列名,数据类型,
      ...
  )ENGINE = 引擎名称;
  ```

* 修改数据表的存储引擎

  ```mysql
  ALTER TABLE 表名 ENGINE = 引擎名称;
  ```

  



***



## 索引机制

### 索引介绍

#### 基本介绍

MySQL官方对索引的定义为：索引（index）是帮助 MySQL 高效获取数据的一种数据结构，本质是排好序的快速查找数据结构。在表数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式指向数据， 这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

索引使用：一张数据表，用于保存数据；一个索引配置文件，用于保存索引；每个索引都指向了某一个数据
![](https://gitee.com/seazean/images/raw/master/DB/MySQL-索引的介绍.png)

左边是数据表，一共有两列七条记录，最左边的是数据记录的物理地址（注意逻辑上相邻的记录在磁盘上也并不是一定物理相邻的）。为了加快 Col2 的查找，可以维护一个右边所示的二叉查找树，每个节点分别包含索引键值和一个指向对应数据的物理地址的指针，这样就可以运用二叉查找快速获取到相应数据

索引的优点：

* 类似于书籍的目录索引，提高数据检索的效率，降低数据库的 IO 成本
* 通过索引列对数据进行排序，降低数据排序的成本，降低 CPU 的消耗

索引的缺点：

* 一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式**存储在磁盘**上
* 虽然索引大大提高了查询效率，同时却也降低更新表的速度。对表进行 INSERT、UPDATE、DELETE 操作，MySQL 不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，还会调整因为更新所带来的键值变化后的索引信息，**但是更新数据也需要先从数据库中获取**，索引加快了获取速度，所以可以相互抵消一下。
* 索引会影响到 WHERE 的查询条件和排序 ORDER BY 两大功能



***



#### 索引分类

索引一般的分类如下：

- 功能分类 
  - 单列索引：一个索引只包含单个列，一个表可以有多个单列索引（普通索引）
  - 联合索引：顾名思义，就是将单列索引进行组合
  - 唯一索引：索引列的值必须唯一，允许有空值。如果是联合索引，则列值组合必须唯一
  - 主键索引：一种特殊的唯一索引，不允许有空值，一般在建表时同时创建主键索引
  - 外键索引：只有 InnoDB 引擎支持外键索引，用来保证数据的一致性、完整性和实现级联操作

- 结构分类
  - BTree索引：MySQL使用最频繁的一个索引数据结构，是 InnoDB 和 MyISAM 存储引擎默认的索引类型，底层基于 B+Tree 数据结构
  - Hash索引：MySQL中 Memory 存储引擎默认支持的索引类型
  - R-tree 索引（空间索引）：空间索引是 MyISAM 引擎的一个特殊索引类型，主要用于地理空间数据类型
  - Full-text （全文索引） ：快速匹配全部文档的方式。MyISAM支持， InnoDB不支持FULLTEXT类型的索引，但是 InnoDB 可以使用 sphinx 插件支持全文索引，MEMORY引擎不支持
  
  | 索引        | InnoDB引擎      | MyISAM引擎 | Memory引擎 |
  | ----------- | --------------- | ---------- | ---------- |
  | BTREE索引   | 支持            | 支持       | 支持       |
  | HASH 索引   | 不支持          | 不支持     | 支持       |
  | R-tree 索引 | 不支持          | 支持       | 不支持     |
  | Full-text   | 5.6版本之后支持 | 支持       | 不支持     |

组合索引图示：根据身高年龄建立的组合索引（height,age）

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-组合索引图.png)





***



### 聚簇索引

#### 索引对比

聚簇索引是一种数据存储方式，并不是一种单独的索引类型

* 聚簇索引的叶子节点存放的是主键值和数据行，支持覆盖索引

* 非聚簇索引的叶子节点存放的是主键值或指向数据行的指针

在 Innodb 下主键索引是聚簇索引，在 Myisam 下主键索引是非聚簇索引



***



#### Innodb

##### 聚簇索引

在 Innodb 存储引擎，B+ 树索引可以分为聚簇索引（也称聚集索引、clustered index）和辅助索引（也称非聚簇索引或二级索引、secondary index、non-clustered index）

InnoDB 中，聚簇索引是按照每张表的主键构造一颗 B+ 树，同时叶子节点中存放的就是整张表的行记录数据，也将聚集索引的叶子节点称为数据页

* 这个特性决定了数据也是索引的一部分，所以一张表只能有一个聚簇索引
* 辅助索引的存在不影响聚簇索引中数据的组织，所以一张表可以有多个辅助索引

聚簇索引的优点：

* 数据访问更快，聚簇索引将索引和数据保存在同一个 B+ 树中，因此从聚簇索引中获取数据比非聚簇索引更快
* 聚簇索引对于主键的排序查找和范围查找速度非常快

聚簇索引的缺点：

* 插入速度严重依赖于插入顺序，按照主键的顺序插入是最快的方式，否则将会出现页分裂，严重影响性能，所以对于 InnoDB 表，一般都会定义一个自增的 ID 列为主键

* 更新主键的代价很高，将会导致被更新的行移动，所以对于InnoDB表，一般定义主键为不可更新

* 二级索引访问需要两次索引查找，第一次找到主键值，第二次根据主键值找到行数据



***



##### 辅助索引

在聚簇索引之上创建的索引称之为辅助索引，非聚簇索引都是辅助索引，像复合索引、前缀索引、唯一索引等

辅助索引叶子节点存储的是主键值，而不是行的物理地址，所以访问数据需要二次查找，这也是推荐使用覆盖索引的原因，可以减少回表查询

检索过程：辅助索引找到主键值，再通过聚簇索引找到数据页，最后通过数据页中的 Page Directory 找到数据行



***



##### 索引实现

InnoDB 使用 B+Tree 作为索引结构

**主键索引：**

* 在 InnoDB 中，表数据文件本身就是按 B+Tree 组织的一个索引结构，这个索引的 key 是数据表的主键，叶子节点 data 域保存了完整的数据记录

* Innodb 的表数据文件**通过主键聚集数据**，如果没有定义主键，会选择非空唯一索引代替，如果也没有这样的列，MySQL 会自动为 InnoDB 表生成一个**隐含字段**作为主键，这个字段长度为 6 个字节，类型为长整形（MVCC 部分的笔记提及）

**辅助索引：**

InnoDB 的所有辅助索引（二级索引）都引用主键作为 data 域

InnoDB 表是基于聚簇索引建立的，因此 InnoDB 的索引能提供一种非常快速的主键查找性能。不过辅助索引也会包含主键列，所以不建议使用过长的字段作为主键，过长的主索引会令辅助索引变得过大

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB聚簇和辅助索引结构.png)



***



#### MyISAM

##### 非聚簇

MyISAM 的主键索引使用的是非聚簇索引，索引文件和数据文件是分离的，索引文件仅保存数据记录的**地址**

* 主键索引 B+ 树的节点存储了主键，辅助键索引 B+ 树存储了辅助键，表数据存储在独立的地方，这两颗 B+ 树的叶子节点都使用一个地址指向真正的表数据，对于表数据来说，这两个键没有任何差别
* 由于索引树是独立的，通过辅助索引检索无需访问主键的索引树回表查询

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-聚簇索引和辅助索引检锁数据图.jpg)



***



##### 索引实现

MyISAM 的索引方式也叫做非聚集的，之所以这么称呼是为了与 InnoDB 的聚集索引区分

**主键索引：**MyISAM 引擎使用 B+Tree 作为索引结构，叶节点的data域存放的是数据记录的地址

**辅助索引：**MyISAM 中主索引和辅助索引（Secondary key）在结构上没有任何区别，只是主索引要求 key 是唯一的，而辅助索引的 key 可以重复

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM主键和辅助索引结构.png)





参考文章：https://blog.csdn.net/lm1060891265/article/details/81482136



***



### 索引结构

#### 原理

索引是在 MySQL 的存储引擎中实现的，不同的存储引擎的所支持的索引不一定完全相同

BTree 的索引类型是基于 B+Tree 树型数据结构的，B+Tree 又是 BTree 数据结构的变种，用在数据库和操作系统中的文件系统，特点是能够保持数据稳定有序

磁盘存储：

* 系统从磁盘读取数据到内存时是以磁盘块（block）为基本单位的，位于同一个磁盘块中的数据会被一次性读取出来，而不是需要什么取什么。

- InnoDB 存储引擎中有页（Page）的概念，页是其磁盘管理的最小单位，InnoDB 存储引擎中默认每个页的大小为 16KB。
- InnoDB 引擎将若干个地址连接磁盘块，以此来达到页的大小16KB，在查询数据时如果一个页中的每条数据都能有助于定位数据记录的位置，这将会减少磁盘I/O次数，提高查询效率。



***



#### BTree

BTree 又叫多路平衡搜索树，一颗 m 叉的 BTree 特性如下：

- 树中每个节点最多包含m个孩子
- 除根节点与叶子节点外，每个节点至少有 [ceil(m/2)] 个孩子
- 若根节点不是叶子节点，则至少有两个孩子
- 所有的叶子节点都在同一层
- 每个非叶子节点由 n 个key与 n+1 个指针组成，其中 [ceil(m/2)-1] <= n <= m-1 

5叉，key 的数量 [ceil(m/2)-1] <= n <= m-1 为 2 <= n <=4 ，当 n>4 时中间节点分裂到父节点，两边节点分裂

插入 C N G A H E K Q M F W L T Z D P R X Y S 数据的工作流程：

* 插入前4个字母 C N G A 

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程1.png)

* 插入H，n>4，中间元素G字母向上分裂到新的节点

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程2.png)

* 插入E，K，Q不需要分裂

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程3.png)

* 插入M，中间元素M字母向上分裂到父节点G

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程4.png)

* 插入F，W，L，T 不需要分裂

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程5.png)

*  插入Z，中间元素T向上分裂到父节点中

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程6.png)

* 插入D，中间元素D向上分裂到父节点中，然后插入P，R，X，Y不需要分裂

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程7.png)

*  最后插入S，NPQR节点n>5，中间节点Q向上分裂，但分裂后父节点DGMT的n>5，中间节点M向上分裂

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-BTree工作流程8.png)

BTREE 树就已经构建完成了，BTREE 树和二叉树相比， 查询数据的效率更高， 因为对于相同的数据量来说，**BTREE 的层级结构比二叉树小**，所以搜索速度快

BTree 结构的数据可以让系统高效的找到数据所在的磁盘块，定义一条记录为一个二元组 [key, data] ，key 为记录的键值，对应表中的主键值，data 为一行记录中除主键外的数据。对于不同的记录，key 值互不相同，BTree 中的每个节点根据实际情况可以包含大量的关键字信息和分支
![](https://gitee.com/seazean/images/raw/master/DB/索引的原理-BTree.png)

缺点：当进行范围查找时会出现回旋查找



***



#### B+Tree

##### 数据结构

B+Tree 为 BTree 的变种，B+Tree 与 BTree 的区别为：

* n 叉 B+Tree 最多含有 n 个 key（哈希值），而 BTree 最多含有 n-1 个 key

- 所有**非叶子节点只存储键值 key**信息，只进行数据索引，使每个非叶子节点所能保存的关键字大大增加
- 所有**数据都存储在叶子节点**，所以每次数据查询的次数都一样
- 叶子节点按照 key 大小顺序排列，左边结尾数据都会保存右边节点开始数据的**指针**，形成一个链表
- 所有节点中的 key 在叶子节点中也存在（比如 5)，key 允许重复，B 树不同节点不存在重复的 key

<img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-B+Tree数据结构.png" style="zoom: 67%;" />

B* 树：是 B+ 树的变体，在 B+ 树的非根和非叶子结点再增加指向兄弟的指针



***



##### 优化结构

BTree 数据结构中每个节点中不仅包含数据的 key 值，还有 data 值。每一页的存储空间是有限的，如果 data 数据较大时将会导致每个节点（即一个页）能存储的key的数量很小，当存储的数据量很大时同样会导致 B-Tree 的深度较大，增大查询时的磁盘 I/O 次数，进而影响查询效率

MySQL 索引数据结构对经典的 B+Tree 进行了优化，在原 B+Tree 的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的 B+Tree，**提高区间访问的性能，防止回旋查找**

区间访问的意思是访问索引为 5 - 15 的数据，可以直接根据相邻节点的指针遍历

![](https://gitee.com/seazean/images/raw/master/DB/索引的原理-B+Tree.png)

通常在 B+Tree 上有两个头指针，一个指向根节点，另一个指向关键字最小的叶子节点，而且所有叶子节点（即数据节点）之间是一种链式环结构。可以对 B+Tree 进行两种查找运算：

- 有范围：对于主键的范围查找和分页查找
- 有顺序：从根节点开始，进行随机查找，顺序查找

InnoDB 存储引擎中页的大小为 16KB，一般表的主键类型为 INT（4字节）或 BIGINT（8字节），指针类型也一般为4或8个字节，也就是说一个页（B+Tree中的**一个节点**）中大概存储 16KB/(8B+8B)=1K 个键值（估值）。则一个深度为3的 B+Tree 索引可以维护 `10^3 * 10^3 * 10^3 = 10亿` 条记录

实际情况中每个节点可能不能填充满，因此在数据库中，B+Tree的高度一般都在2-4层。MySQL 的 InnoDB 存储引擎在设计时是将根节点常驻内存的，也就是说查找某一键值的行记录时最多只需要1~3次磁盘 I/O 操作

B+Tree 优点：提高查询速度，减少磁盘的 IO 次数，树形结构较小



***



### 索引操作

索引在创建表的时候可以同时创建， 也可以随时增加新的索引

* 创建索引：如果一个表中有一列是主键，那么会**默认为其创建主键索引**（主键列不需要单独创建索引）
  
  ```mysql
  CREATE [UNIQUE|FULLTEXT] INDEX 索引名称 [USING 索引类型] ON 表名(列名...);
  -- 索引类型默认是 B+TREE
  ```
  
* 查看索引

  ```mysql
  SHOW INDEX FROM 表名;
  ```

* 添加索引

  ```mysql
  -- 单列索引
  ALTER TABLE 表名 ADD INDEX 索引名称(列名);
  
  -- 组合索引
  ALTER TABLE 表名 ADD INDEX 索引名称(列名1,列名2,...);
  
  -- 主键索引
  ALTER TABLE 表名 ADD PRIMARY KEY(主键列名); 
  
  -- 外键索引(添加外键约束，就是外键索引)
  ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主键列名);
  
  -- 唯一索引
  ALTER TABLE 表名 ADD UNIQUE 索引名称(列名);
  
  -- 全文索引(mysql只支持文本类型)
  ALTER TABLE 表名 ADD FULLTEXT 索引名称(列名);
  ```

* 删除索引

  ```mysql
  DROP INDEX 索引名称 ON 表名;
  ```

* 案例练习
  数据准备：student

  ```mysql
  id	NAME	 age	score
  1	张三		23		99
  2	李四		24		95
  3	王五		25		98
  4	赵六		26		97
  ```

  索引操作：

  ```mysql
  -- 为student表中姓名列创建一个普通索引
  CREATE INDEX idx_name ON student(NAME);
  
  -- 为student表中年龄列创建一个唯一索引
  CREATE UNIQUE INDEX idx_age ON student(age);
  ```

  

***



### 设计原则

索引的设计可以遵循一些已有的原则，创建索引的时候请尽量考虑符合这些原则，便于提升索引的使用效率

创建索引时的原则：
- 对查询频次较高，且数据量比较大的表建立索引
- 使用唯一索引，区分度越高，使用索引的效率越高
- 索引字段的选择，最佳候选列应当从 where 子句的条件中提取，如果 where 子句中的组合比较多，那么应当挑选最常用、过滤效果最好的列的组合
- 使用短索引，索引创建之后也是使用硬盘来存储的，因此提升索引访问的 I/O 效率，也可以提升总体的访问效率。假如构成索引的字段总长度比较短，那么在给定大小的存储块内可以存储更多的索引值，相应的可以有效的提升 MySQL 访问索引的 I/O 效率
- 索引可以有效的提升查询数据的效率，但索引数量不是多多益善，索引越多，维护索引的代价越高。对于插入、更新、删除等 DML 操作比较频繁的表来说，索引过多，会引入相当高的维护代价，降低 DML 操作的效率，增加相应操作的时间消耗；另外索引过多的话，MySQL 也会犯选择困难病，虽然最终仍然会找到一个可用的索引，但提高了选择的代价

* MySQL 建立联合索引时会遵守**最左前缀匹配原则**，即最左优先，在检索数据时从联合索引的最左边开始匹配
  
  N个列组合而成的组合索引，相当于创建了N个索引，如果查询时 where 句中使用了组成该索引的**前**几个字段，那么这条查询SQL可以利用组合索引来提升查询效率
  
  ```mysql
  -- 对name、address、phone列建一个联合索引
  ALTER TABLE user ADD INDEX index_three(name,address,phone);
  -- 查询语句执行时会依照最左前缀匹配原则，检索时分别会使用索引进行数据匹配。
  (name,address,phone)
  (name,address)
  (name)
  
  -- 索引的字段可以是任意顺序的，优化器会帮助我们调整顺序，下面的SQL语句可以命中索引
  SELECT * FROM user WHERE address = '北京' AND phone = '12345' AND name = '张三';
  ```
  
  ```mysql
  -- 如果联合索引中最左边的列不包含在条件查询中，SQL语句就不会命中索引，比如：
  SELECT * FROM user WHERE address = '北京' AND phone = '12345'; 
  ```

哪些情况不要建立索引：

* 记录太少的表
* 经常增删改的表
* 频繁更新的字段不适合创建索引
* where 条件里用不到的字段不创建索引



***



### 索引优化

#### 覆盖索引

覆盖索引：包含所有满足查询需要的数据的索引（SELECT 后面的字段刚好是索引字段），可以利用该索引返回 SELECT 列表的字段，而不必根据索引去聚簇索引上读取数据文件

回表查询：要查找的字段不在非主键索引树上时，需要通过叶子节点的主键值去主键索引上获取对应的行数据

使用覆盖索引，防止回表查询：

* 表 user 主键为 id，普通索引为 age，查询语句：

  ```mysql
  SELECT * FROM user WHERE age = 30;
  ```

  查询过程：先通过普通索引 age=30 定位到主键值 id=1，再通过聚集索引 id=1 定位到行记录数据，需要两次扫描 B+ 树，这就是回表查询

* 使用覆盖索引：

  ```mysql
  DROP INDEX idx_age ON user;
  CREATE INDEX idx_age_name ON user(age,name);
  SELECT id,age FROM user WHERE age = 30;
  ```

  在一棵索引树上就能获取查询所需的数据，无需回表速度更快

使用覆盖索引，要注意 SELECT 列表中只取出需要的列，不可用 SELECT *，因为如果将所有字段一起做索引会导致索引文件过大，查询性能下降



***



#### 索引下推

索引条件下推优化（Index Condition Pushdown）是 MySQL5.6 添加，用于优化数据查询，减少回表操作

索引下推充分利用了索引中的数据，在查询出整行数据之前过滤掉无效的数据，再去主键索引树上查找

* 不使用索引下推优化时存储引擎通过索引检索到数据返回给 MySQL 服务器，服务器判断数据是否符合条件

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-不使用索引下推.png)
* 使用索引下推优化时，如果存在某些被索引的列的判断条件时，MySQL 服务器将这一部分**判断条件传递给存储引擎**，然后由存储引擎在索引内部判断索引是否符合传递的条件，只有当索引符合条件时才会将数据检索出来返回给 MySQL 服务器，由此减少 IO次数

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-使用索引下推.png)

适用条件：

* 需要存储引擎将索引中的数据与条件进行判断，所以优化是基于存储引擎的，只有特定引擎可以使用，适用于 InnoDB 和 MyISAM
* 存储引擎没有调用跨存储引擎的能力，跨存储引擎的功能有存储过程、触发器、视图，所以调用这些功能的不可以进行索引下推优化
* 对于 InnoDB 引擎只适用于二级索引，InnoDB 的聚簇索引会将整行数据读到缓冲区，因为数据已经在内存中了，不再需要去读取了，索引下推的目的减少 IO 次数也就失去了意义

工作过程：用户表 user，(name,sex) 是联合索引

```mysql
SELECT * FROM user WHERE name LIKE '王%' AND　sex=1;	-- 头部模糊匹配会造成索引失效
```

* 优化前：在非主键索引树上找到满足第一个条件的行，然后通过叶子节点记录的主键值再回到主键索引树上查找到对应的行数据，再对比 AND 后的条件是否符合，符合返回数据，需要 4 次回表

<img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-索引下推优化1.png" style="zoom: 80%;" />

* 优化后：检查索引中存储的列信息是否符合索引条件，然后交由存储引擎用剩余的判断条件判断此行数据是否符合要求，**不满足条件的不去读取表中的数据**，满足下推条件的就根据主键值进行回表查询，2 次回表
  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-索引下推优化2.png)

当使用 EXPLAIN 进行分析时，如果使用了索引条件下推，Extra 会显示 Using index condition



参考文章：https://blog.csdn.net/sinat_29774479/article/details/103470244
参考文章：https://blog.csdn.net/linuxguitu/article/details/113649245



***



#### 前缀索引

当要索引的列字符很多时，索引会变大变慢，可以只索引列开始的部分字符串，节约索引空间，提高索引效率

优化原则：降低重复的索引值

比如地区表：

```mysql
area			gdp		code
chinaShanghai	100		aaa
chinaDalian		200		bbb
usaNewYork		300		ccc
chinaFuxin		400		ddd
chinaBeijing	500		eee
```

发现 area 字段很多都是以 china 开头的，那么如果以前1-5位字符做前缀索引就会出现大量索引值重复的情况，索引值重复性越低，查询效率也就越高，所以需要建立前 6 位字符的索引：

```mysql
CREATE INDEX idx_area ON table_name(area(7));
```





***



## 语句优化

### 优化步骤

#### 执行频率

随着生产数据量的急剧增长，很多 SQL 语句逐渐显露出性能问题，对生产的影响也越来越大，此时有问题的 SQL 语句就成为整个系统性能的瓶颈，因此必须要进行优化

MySQL 客户端连接成功后，查询服务器状态信息：

```mysql
SHOW [SESSION|GLOBAL] STATUS LIKE '';
-- SESSION: 显示当前会话连接的统计结果，默认参数
-- GLOBAL: 显示自数据库上次启动至今的统计结果
```

* 查看SQL执行频率：

  ```mysql
  SHOW STATUS LIKE 'Com_____';
  ```

  Com_xxx 表示每种语句执行的次数

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-SQL语句执行频率.png)

* 查询 SQL 语句影响的行数：

  ```mysql
  SHOW STATUS LIKE 'Innodb_rows_%';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-SQL语句影响的行数.png)

Com_xxxx：这些参数对于所有存储引擎的表操作都会进行累计

Innodb_xxxx：这几个参数只是针对InnoDB 存储引擎的，累加的算法也略有不同

| 参数                 | 含义                                                         |
| :------------------- | ------------------------------------------------------------ |
| Com_select           | 执行 SELECT 操作的次数，一次查询只累加 1                     |
| Com_insert           | 执行 INSERT 操作的次数，对于批量插入的 INSERT 操作，只累加一次 |
| Com_update           | 执行 UPDATE 操作的次数                                       |
| Com_delete           | 执行 DELETE 操作的次数                                       |
| Innodb_rows_read     | 执行 SELECT 查询返回的行数                                   |
| Innodb_rows_inserted | 执行 INSERT 操作插入的行数                                   |
| Innodb_rows_updated  | 执行 UPDATE 操作更新的行数                                   |
| Innodb_rows_deleted  | 执行 DELETE 操作删除的行数                                   |
| Connections          | 试图连接 MySQL 服务器的次数                                  |
| Uptime               | 服务器工作时间                                               |
| Slow_queries         | 慢查询的次数                                                 |



****



#### 定位低效

通过以下两种方式定位执行效率较低的 SQL 语句

* 慢日志查询： 慢查询日志在查询结束以后才记录，执行效率出现问题时查询日志并不能定位问题

  配置文件修改：修改.cnf文件`vim /etc/mysql/my.cnf`，重启MySQL服务器

  ```sh
  slow_query_log=ON
  slow_query_log_file=/usr/local/mysql/var/localhost-slow.log
  long_query_time=1	#记录超过long_query_time秒的SQL语句的日志
  log-queries-not-using-indexes = 1
  ```

  使用命令配置：

  ```mysql
  mysql> SET slow_query_log=ON;
  mysql> SET GLOBAL slow_query_log=ON;
  ```

  查看是否配置成功：

  ```mysql
  SHOW VARIABLES LIKE '%query%'
  ```

* SHOW PROCESSLIST：查看当前 MySQL 在进行的线程，包括线程的状态、是否锁表等，可以实时地查看 SQL 的执行情况，同时对一些锁表操作进行优化

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-SHOW PROCESSLIST命令.png)

  | 参数    | 含义                                                         |
  | ------- | ------------------------------------------------------------ |
  | ID      | 用户登录mysql时系统分配的"connection_id"，可以使用函数 connection_id() 查看 |
  | User    | 显示当前用户，如果不是 root，这个命令就只显示用户权限范围的 sql 语句 |
  | Host    | 显示这个语句是从哪个 ip 的哪个端口上发的，可以用来跟踪出现问题语句的用户 |
  | db      | 显示这个进程目前连接的是哪个数据库                           |
  | Command | 显示当前连接的执行的命令，一般取值为休眠 Sleep、查询 Query、连接 Connect 等 |
  | Time    | 显示这个状态持续的时间，单位是秒                             |
  | State   | 显示使用当前连接的 sql 语句的状态，以查询为例，需要经过 copying to tmp table、sorting result、sending data等状态才可以完成 |
  | Info    | 显示执行的 sql 语句，是判断问题语句的一个重要依据            |





***



#### EXPLAIN

##### 执行计划

通过 EXPLAIN 命令获取执行 SQL 语句的信息，包括在 SELECT 语句执行过程中如何连接和连接的顺序

查询 SQL语句的执行计划：

```mysql
EXPLAIN SELECT * FROM table_1 WHERE id = 1;
```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-explain查询SQL语句的执行计划.png)

| 字段          | 含义                                                         |
| ------------- | ------------------------------------------------------------ |
| id            | select查询的序列号，表示查询中执行select子句或操作表的顺序   |
| select_type   | 表示 SELECT 的类型                                           |
| table         | 输出结果集的表                                               |
| type          | 表示表的连接类型                                             |
| possible_keys | 表示查询时，可能使用的索引                                   |
| key           | 表示实际使用的索引                                           |
| key_len       | 索引字段的长度                                               |
| ref           | 列与索引的比较，表示表的连接匹配条件，即哪些列或常量被用于查找索引列上的值 |
| rows          | 扫描出的行数，表示MySQL根据表统计信息及索引选用情况，估算的找到所需的记录所需要读取的行数 |
| filtered      | 按表条件过滤的行百分比                                       |
| extra         | 执行情况的说明和描述                                         |

MySQL执行计划的局限：

* 只是计划，不是执行 SQL 语句

* EXPLAIN 不会告诉显示关于触发器、存储过程的信息或用户自定义函数对查询的影响情况
* EXPLAIN 不考虑各种 Cache
* EXPLAIN 不能显示 MySQL 在执行查询时所作的优化工作，因为执行计划在执行查询之前生成
* EXPALIN 部分统计信息是估算的，并非精确值
* EXPALIN 只能解释 SELECT 操作，其他操作要重写为 SELECT 后查看执行计划
* 执行计划在优化器之后、执行器之前生成，然后执行器调用存储引擎检索数据
* 执行计划可以随着底层优化器输入的更改而更改。EXPLAIN PLAN 显示的是在解释语句时数据库将如何运行 SQL 语句，由于执行环境和 EXPLAIN PLAN 环境的不同，此计划可能与 SQL 语句实际的执行计划不同

环境准备：

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-执行计划环境准备.png)





***



##### id

SQL 执行的顺序的标识，SQL 从大到小的执行

* id 相同时，执行顺序由上至下

  ```mysql
  EXPLAIN SELECT * FROM t_role r, t_user u, user_role ur WHERE r.id = ur.role_id AND u.id = ur.user_id ;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-explain之id相同.png)

* id 不同时，id值越大优先级越高，越先被执行

  ```mysql
  EXPLAIN SELECT * FROM t_role WHERE id = (SELECT role_id FROM user_role WHERE user_id = (SELECT id FROM t_user WHERE username = 'stu1'))
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-explain之id不同.png)

* id 有相同也有不同时，id相同的可以认为是一组，从上往下顺序执行；在所有的组中，id的值越大，优先级越高，越先执行

  ```mysql
  EXPLAIN SELECT * FROM t_role r , (SELECT * FROM user_role ur WHERE ur.`user_id` = '2') a WHERE r.id = a.role_id ; 
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-explain之id相同和不同.png)



***



##### select

表示查询中每个 select 子句的类型（简单 OR 复杂）

| select_type        | 含义                                                         |
| ------------------ | ------------------------------------------------------------ |
| SIMPLE             | 简单的 SELECT 查询，查询中不包含子查询或者 UNION             |
| PRIMARY            | 查询中若包含任何复杂的子查询，最外层查询标记为该标识         |
| SUBQUERY           | 在 SELECT 或 WHERE 中包含子查询，该子查询被标记为：SUBQUERY  |
| DEPENDENT SUBQUERY | 在 SUBQUERY 基础上，子查询中的第一个SELECT，取决于外部的查询 |
| DERIVED            | 在 FROM 列表中包含的子查询，被标记为 DERIVED（衍生），MYSQL会递归执行这些子查询，把结果放在临时表中 |
| UNION              | UNION 中的第二个或后面的 SELECT 语句，则标记为UNION ； 若 UNION 包含在 FROM 子句的子查询中，外层 SELECT 将被标记为：DERIVED |
| DEPENDENT UNION    | UNION 中的第二个或后面的SELECT语句，取决于外面的查询         |
| UNION RESULT       | UNION 的结果，UNION 语句中第二个 SELECT 开始后面所有 SELECT  |



****



##### table

输出结果集的表，显示这一步所访问数据库中表名称，有时不是真实的表名字，可能是简称，也可能是第几步执行的结果的简称



***



##### type

对表的访问方式，表示MySQL在表中找到所需行的方式，又称访问类型

| type   | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| ALL    | Full Table Scan，MySQL将遍历全表以找到匹配的行，全表扫描     |
| index  | Full Index Scan，index 与 ALL 区别为 index 类型只遍历索引树  |
| range  | 索引范围扫描，常见于between、<、>等的查询                    |
| ref    | 非唯一性索引扫描，返回匹配某个单独值的所有，本质上也是一种索引访问，常见于使用非唯一索引即唯一索引的非唯一前缀进行的查找 |
| eq_ref | 唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配，常见于主键或唯一索引扫描 |
| const  | 当 MySQL 对查询某部分进行优化，并转换为一个常量时，使用该类型访问，如将主键置于where列表中，MySQL就能将该查询转换为一个常量 |
| system | system 是 const 类型的特例，当查询的表只有一行的情况下，使用system |
| NULL   | MySQL在优化过程中分解语句，执行时甚至不用访问表或索引        |

从上到下，性能从差到好，一般来说需要保证查询至少达到 range 级别， 最好达到ref 



***



##### key

possible_keys：

* 指出 MySQL 能使用哪个索引在表中找到记录，查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询使用
* 如果该列是 NULL，则没有相关的索引

key：

* 显示MySQL在查询中实际使用的索引，若没有使用索引，显示为 NULL
* 查询中若使用了**覆盖索引**，则该索引仅出现在 key 列表，不出现在 possible_keys

key_len：

* 表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度
* key_len 显示的值为索引字段的最大可能长度，并非实际使用长度，即 key_len 是根据表定义计算而得，不是通过表内检索出的
* 在不损失精确性的前提下，长度越短越好 



***



##### Extra

其他的额外的执行计划信息，在该列展示：

* Using index：该值表示相应的 SELECT 操作中使用了**覆盖索引**（Covering Index）
* Using index condition：第一种情况是搜索条件中虽然出现了索引列，但是有部分条件无法使用索引，会根据能用索引的条件先搜索一遍再匹配无法使用索引的条件，回表查询数据；第二种是使用了索引下推
* Using where：表示存储引擎收到记录后进行“后过滤”（Post-filter），如果查询未能使用索引，Using where 的作用是提醒我们 MySQL 将用 WHERE 子句来过滤结果集，即需要回表查询
* Using temporary：表示 MySQL 需要使用临时表来存储结果集，常见于排序和分组查询
* Using filesort：MySQL 会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取，无法利用索引完成的排序操作称为文件排序
* Using join buffer：说明在获取连接条件时没有使用索引，并且需要连接缓冲区来存储中间结果
* Impossible where：说明 WHERE 语句会导致没有符合条件的行，通过收集统计信息不可能存在结果
* Select tables optimized away：说明仅通过使用索引，优化器可能仅从聚合函数结果中返回一行

* No tables used：Query 语句中使用 from dual 或不含任何 from 子句



参考文章：https://www.cnblogs.com/ggjucheng/archive/2012/11/11/2765237.html



****



#### PROFILES

SHOW PROFILES 能够在做 SQL 优化时分析当前会话中语句执行的资源消耗情况

* 通过 have_profiling 参数，能够看到当前 MySQL 是否支持profile：
  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL- have_profiling.png)

* 默认 profiling 是关闭的，可以通过 set 语句在 Session 级别开启profiling：

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL- profiling.png)

  ```mysql
  SET profiling=1; //开启profiling 开关；
  ```

* 执行 SHOW PROFILES 指令， 来查看 SQL 语句执行的耗时:

  ```mysql
  SHOW PROFILES;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL- 查看SQL语句执行耗时.png)

* 查看到该 SQL 执行过程中每个线程的状态和消耗的时间：

  ```mysql
  SHOW PROFILE FOR QUERY query_id;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-SQL执行每个状态消耗的时间.png)

  Sending data 状态表示 MySQL 线程开始访问数据行并把结果返回给客户端，而不仅仅是返回给客户端。由于在 Sending data 状态下，MySQL 线程需要做大量磁盘读取操作，所以是整个查询中耗时最长的状态。

* 在获取到最消耗时间的线程状态后，MySQL 支持选择 all、cpu、block io 、context switch、page faults 等类型查看 MySQL 在使用什么资源上耗费了过高的时间。例如，选择查看 CPU 的耗费时间：

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-SQL执行每个状态消耗的CPU.png)

  Status：SQL 语句执行的状态
  Durationsql：执行过程中每一个步骤的耗时
  CPU_user：当前用户占有的 CPU
  CPU_system：系统占有的 CPU



***



#### trace

MySQL 提供了对 SQL 的跟踪， 通过 trace 文件能够进一步了解执行过程。

* 打开 trace，设置格式为 JSON，并设置 trace 最大能够使用的内存大小，避免解析过程中因为默认内存过小而不能够完整展示

  ```mysql
  SET optimizer_trace="enabled=on",end_markers_in_json=ON;
  SET optimizer_trace_max_mem_size=1000000;
  ```

* 执行 SQL 语句：

  ```mysql
  SELECT * FROM tb_item WHERE id < 4;
  ```

* 检查 information_schema.optimizer_trace：

  ```mysql
  SELECT * FROM information_schema.optimizer_trace \G; -- \G代表竖列展示
  ```





****



### 索引失效

#### 创建索引

索引是数据库优化最重要的手段之一，通过索引通常可以帮助用户解决大多数的 MySQL 的性能优化问题

```mysql
CREATE TABLE `tb_seller` (
	`sellerid` varchar (100),
	`name` varchar (100),
	`nickname` varchar (50),
	`password` varchar (60),
	`status` varchar (1),
	`address` varchar (100),
	`createtime` datetime,
    PRIMARY KEY(`sellerid`)
)ENGINE=INNODB DEFAULT CHARSET=utf8mb4;
INSERT INTO `tb_seller` (`sellerid`, `name`, `nickname`, `password`, `status`, `address`, `createtime`) values('xiaomi','小米科技','小米官方旗舰店','e10adc3949ba59abbe56e057f20f883e','1','西安市','2088-01-01 12:00:00');
CREATE INDEX idx_seller_name_sta_addr ON tb_seller(name,status,address);
```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引环境准备.png)



****



#### 避免失效

索引失效的情况：

* 全值匹配：对索引中所有列都指定具体值，这种情况索引生效，执行效率高

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name='小米科技' AND status='1' AND address='西安市';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引1.png)

* **最左前缀法则**：联合索引遵守最左前缀法则

  匹配最左前缀法则，走索引：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name='小米科技';
  EXPLAIN SELECT * FROM tb_seller WHERE name='小米科技' AND status='1';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引2.png)

  违法最左前缀法则 ， 索引失效：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE status='1';
  EXPLAIN SELECT * FROM tb_seller WHERE status='1' AND address='西安市';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引3.png)

  如果符合最左法则，但是出现跳跃某一列，只有最左列索引生效：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name='小米科技' AND address='西安市';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引4.png)

* **范围查询**右边的列，不能使用索引：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name='小米科技' AND status>'1' AND address='西安市';
  ```

  根据前面的两个字段 name ， status 查询是走索引的， 但是最后一个条件 address 没有用到索引

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引5.png)

* 在索引列上进行**运算操作**， 索引将失效：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE SUBSTRING(name,3,2) = '科技';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引6.png)

* **字符串不加单引号**，造成索引失效：

  在查询时，没有对字符串加单引号，MySQL 的查询优化器，会自动的进行类型转换，造成索引失效

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name='小米科技' AND status=1;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引7.png)

* 用 **OR** 分割条件，索引失效，导致全表查询：

  OR 前的条件中的列有索引而后面的列中没有索引或 OR 前后两个列是同一个复合索引，都造成索引失效

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name='阿里巴巴' OR createtime = '2088-01-01 12:00:00';
  EXPLAIN SELECT * FROM tb_seller WHERE name='小米科技' OR status='1';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引10.png)

  AND 分割的条件不影响：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name='阿里巴巴' AND createtime = '2088-01-01 12:00:00';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引11.png)

* 以 % 开头的 LIKE **模糊查询**，索引失效：

  如果是尾部模糊匹配，索引不会失效；如果是头部模糊匹配，索引失效。

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name like '%科技%';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引12.png)

  解决方案：通过覆盖索引来解决 

  ```mysql
  EXPLAIN SELECT sellerid,name,status FROM tb_seller WHERE name like '%科技%';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引13.png)

  原因：在覆盖索引的这棵 B+ 数上只需要进行 like 的匹配，或者是基于覆盖索引查询再进行 WHERE 的判断就可以获得结果

* 如果 MySQL 评估使用索引比全表更慢，则不使用索引，索引失效：

  ```mysql
  CREATE INDEX idx_address ON tb_seller(address);
  EXPLAIN SELECT * FROM tb_seller WHERE address='西安市';
  EXPLAIN SELECT * FROM tb_seller WHERE address='北京市';
  ```

  北京市的键值占 9/10，所以优化为全表扫描，type = ALL

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引14.png)

* IS  NULL、IS NOT NULL  **有时**索引失效：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE name IS NULL;
  EXPLAIN SELECT * FROM tb_seller WHERE name IS NOT NULL;
  ```

  NOT NULL 失效的原因是 name 列全部不是 null，优化为全表扫描，当 NULL 过多时，IS NULL 失效

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引15.png)

* IN 肯定会走索引，但是当 IN 的取值范围较大时会导致索引失效，走全表扫描：

  ```mysql
  EXPLAIN SELECT * FROM tb_seller WHERE sellerId IN ('alibaba','huawei');-- 都走索引
  EXPLAIN SELECT * FROM tb_seller WHERE sellerId NOT IN ('alibaba','huawei');
  ```



***



#### 底层原理

索引失效一般是针对联合索引，联合索引一般由几个字段组成，排序方式是先按照第一个字段进行排序，然后排序第二个，依此类推，图示（a, b）索引，**a 相等的情况下 b 是有序的**

<img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-索引失效底层原理1.png" style="zoom:67%;" />

* 最左前缀法则：当不匹配前面的字段的时候，后面的字段都是无序的。这种无序不仅体现在叶子节点，也会**导致查询时的非叶子节点也是无序的**，因为索引树相当于忽略的第一个字段，就无法使用二分查找

* 范围查询右边的列，不能使用索引，比如语句： `WHERE a > 1 AND b = 1 `，在 a 大于1的时候，b是无序的

  <img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-索引失效底层原理2.png" style="zoom:67%;" />

* 以 % 开头的 LIKE 模糊查询，索引失效，比如语句：`WHERE a LIKE '%d'`，前面的不确定，导致不符合最左匹配，直接去索引中搜索以 d 结尾的节点，所以没有顺序
                                  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-索引失效底层原理3.png)



***



#### 查看索引

```mysql
SHOW STATUS LIKE 'Handler_read%';	
SHOW GLOBAL STATUS LIKE 'Handler_read%';
```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL查看索引使用情况.png)

* Handler_read_first：索引中第一条被读的次数。如果较高，表示服务器正执行大量全索引扫描（这个值越低越好）

* Handler_read_key：如果索引正在工作，这个值代表一个行被索引值读的次数，如果值越低，表示索引不经常使用，性能改善不好（这个值越高越好）

* Handler_read_next：按照键顺序读下一行的请求数，如果范围约束或执行索引扫描来查询索引列，值增加

* Handler_read_prev：按照键顺序读前一行的请求数，该读方法主要用于优化ORDER BY ... DESC

* Handler_read_rnd：根据固定位置读一行的请求数，如果执行大量查询并对结果进行排序则该值较高，可能是使用了大量需要MySQL扫描整个表的查询或连接，这个值较高意味着运行效率低，应该建立索引来解决

* Handler_read_rnd_next：在数据文件中读下一行的请求数。如果你正进行大量的表扫描，该值较高。通常说明你的表索引不正确或写入的查询没有利用索引。



***



### 优化SQL

#### 覆盖索引

复合索引叶子节点不仅保存了复合索引的值，还有主键索引，所以使用覆盖索引的时候，加上主键也会用到索引

尽量使用覆盖索引，避免 SELECT *：

```mysql
EXPLAIN SELECT name,status,address FROM tb_seller WHERE name='小米科技' AND status='1' AND address='西安市';
```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引8.png)

如果查询列，超出索引列，也会降低性能：

```mysql
EXPLAIN SELECT name,status,address,password FROM tb_seller WHERE name='小米科技' AND status='1' AND address='西安市';
```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用索引9.png)





***



#### 批量插入

当使用 load 命令导入数据的时候，适当的设置可以提高导入的效率：

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL load data.png)

```mysql
LOAD DATA LOCAL INFILE = '/home/seazean/sql1.log' INTO TABLE `tb_user_1` FIELD TERMINATED BY ',' LINES TERMINATED BY '\n'; -- 文件格式如上图
```

对于 InnoDB 类型的表，有以下几种方式可以提高导入的效率：

1. 主键顺序插入：因为InnoDB类型的表是按照主键的顺序保存的，所以将导入的数据按照主键的顺序排列，可以有效的提高导入数据的效率，如果InnoDB表没有主键，那么系统会自动默认创建一个内部列作为主键。

   * 插入ID顺序排列数据：

   ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL插入ID顺序排列数据.png)

   * 插入ID无序排列数据：

   ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL插入ID无序排列数据.png)

2. 关闭唯一性校验：在导入数据前执行`SET UNIQUE_CHECKS=0`，关闭唯一性校验；导入结束后执行SET UNIQUE_CHECKS=1，恢复唯一性校验，可以提高导入的效率。

   ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL插入数据关闭唯一性校验.png)

3. 手动提交事务：如果应用使用自动提交的方式，建议在导入前执行`SET AUTOCOMMIT=0`，关闭自动提交；导入结束后再执行 SET AUTOCOMMIT=1，打开自动提交，可以提高导入的效率。

   事务需要控制大小，事务太大可能会影响执行的效率。MySQL 有 innodb_log_buffer_size 配置项，超过这个值的日志会写入磁盘数据，效率会下降。所以在事务大小达到配置项数据级前进行事务提交可以提高效率

   ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL插入数据手动提交事务.png)



***



#### INSERT

当进行数据的 INSERT 操作的时候，可以考虑采用以下几种优化方案：

* 如果需要同时对一张表插入很多行数据时，优化为一条插入语句，这种方式将大大的缩减客户端与数据库之间的连接、关闭等消耗

  ```mysql
  INSERT INTO tb_test VALUES(1,'Tom');
  INSERT INTO tb_test VALUES(2,'Cat');
  INSERT INTO tb_test VALUES(3,'Jerry');	-- 连接三次数据库
  -- 优化为
  INSERT INTO tb_test VALUES(1,'Tom'),(2,'Cat')，(3,'Jerry');	-- 连接一次
  ```

* 在事务中进行数据插入：

  ```mysql
  start transaction;
  INSERT INTO tb_test VALUES(1,'Tom');
  INSERT INTO tb_test VALUES(2,'Cat');
  INSERT INTO tb_test VALUES(3,'Jerry');
  commit;	-- 手动提交，分段提交
  ```

* 数据有序插入：

  ```mysql
  INSERT INTO tb_test VALUES(1,'Tom');
  INSERT INTO tb_test VALUES(2,'Cat');
  INSERT INTO tb_test VALUES(3,'Jerry');
  ```



****



#### ORDER BY

数据准备：

```mysql
CREATE TABLE `emp` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(100) NOT NULL,
  `age` INT(3) NOT NULL,
  `salary` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8mb4;
INSERT INTO `emp` (`id`, `name`, `age`, `salary`) VALUES('1','Tom','25','2300');-- ...
CREATE INDEX idx_emp_age_salary ON emp(age,salary);
```

* 第一种是通过对返回数据进行排序，所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序

  ```mysql
  EXPLAIN SELECT * FROM emp ORDER BY age DESC;	-- 年龄降序
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL ORDER BY排序1.png)

* 第二种通过有序索引顺序扫描直接返回有序数据，这种情况为 Using index，不需要额外排序，操作效率高

  ```mysql
  EXPLAIN SELECT id, age, salary FROM emp ORDER BY age DESC;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL ORDER BY排序2.png)

* 多字段排序：

  ```mysql
  EXPLAIN SELECT id,age,salary FROM emp ORDER BY age DESC, salary DESC;
  EXPLAIN SELECT id,age,salary FROM emp ORDER BY salary DESC, age DESC;
  EXPLAIN SELECT id,age,salary FROM emp ORDER BY age DESC, salary ASC;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL ORDER BY排序3.png)

  尽量减少额外的排序，通过索引直接返回有序数据。需要**满足 Order by 使用相同的索引、Order By 的顺序和索引顺序相同、Order  by 的字段都是升序或都是降序**，否则需要额外的操作，就会出现 FileSort

Filesort 的优化：通过创建合适的索引，能够减少 Filesort 的出现，但是在某些情况，条件限制不能让 Filesort 消失，就需要加快 Filesort 的排序操作。

对于 Filesort ， MySQL 有两种排序算法：

* 两次扫描算法：MySQL4.1 之前，使用该方式排序。首先根据条件取出排序字段和行指针信息，然后在排序区 sort buffer 中排序，如果 sort buffer 不够，则在临时表 temporary table 中存储排序结果。完成排序后，再根据行指针回表读取记录，该操作可能会导致大量随机I/O操作
* 一次扫描算法：一次性取出满足条件的所有字段，然后在排序区 sort  buffer 中排序后直接输出结果集。排序时内存开销较大，但是排序效率比两次扫描算法高

MySQL 通过比较系统变量 max_length_for_sort_data 的大小和 Query 语句取出的字段的大小，来判定使用哪种排序算法。如果前者大，则说明 sort  buffer 空间足够，使用第二种优化之后的算法，否则使用第一种。

可以适当提高 sort_buffer_size  和 max_length_for_sort_data 系统变量，来增大排序区的大小，提高排序的效率

```mysql
SET @@max_length_for_sort_data = 10000; 		-- 设置全局变量
SET max_length_for_sort_data = 10240; 			-- 设置会话变量
SHOW VARIABLES LIKE 'max_length_for_sort_data';	-- 默认1024
SHOW VARIABLES LIKE 'sort_buffer_size';			-- 默认262114
```



***



#### GROUP BY

GROUP BY 也会进行排序操作，与 ORDER BY 相比，GROUP BY 主要只是多了排序之后的分组操作，所以在 GROUP BY 的实现过程中，与 ORDER BY 一样也可以利用到索引

* 分组查询：

  ```mysql
  DROP INDEX idx_emp_age_salary ON emp;
  EXPLAIN SELECT age,COUNT(*) FROM emp GROUP BY age;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL GROUP BY排序1.png)

  Using temporary：表示 MySQL 需要使用临时表来存储结果集，常见于排序和分组查询

* 查询包含 GROUP BY 但是用户想要避免排序结果的消耗， 则可以执行 ORDER BY NULL 禁止排序：

  ```mysql
  EXPLAIN SELECT age,COUNT(*) FROM emp GROUP BY age ORDER BY NULL;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL GROUP BY排序2.png)

* 创建索引：

  ```mysql
  CREATE INDEX idx_emp_age_salary ON emp(age,salary);
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL GROUP BY排序3.png)



***



#### 嵌套查询

MySQL 4.1版本之后，开始支持 SQL 的子查询

* 可以使用 SELECT 语句来创建一个单列的查询结果，然后把结果作为过滤条件用在另一个查询中
* 使用子查询可以一次性的完成逻辑上需要多个步骤才能完成的 SQL 操作，同时也可以避免事务或者表锁死
* 在有些情况下，子查询是可以被更高效的连接（JOIN）替代

例如查找有角色的所有的用户信息：

* 执行计划：

  ```mysql
  EXPLAIN SELECT * FROM t_user WHERE id IN (SELECT user_id FROM user_role);
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL嵌套查询1.png)

* 优化后：

  ```mysql
  EXPLAIN SELECT * FROM t_user u , user_role ur WHERE u.id = ur.user_id;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL嵌套查询2.png)

  连接查询之所以效率更高 ，是因为不需要在内存中创建临时表来完成逻辑上需要两个步骤的查询工作



***



#### OR

对于包含 OR 的查询子句，如果要利用索引，则 OR 之间的每个条件列都必须用到索引，而且不能使用到复合索引，如果没有索引，则应该考虑增加索引

* 执行查询语句：

  ```mysql
  EXPLAIN SELECT * FROM emp WHERE id = 1 OR age = 30;	-- 两个索引，并且不是复合索引
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL OR条件查询1.png)

  ```sh
  Extra: Using sort_union(idx_emp_age_salary,PRIMARY); Using where
  ```

* 使用 UNION 替换 OR，求并集：
  注意：该优化只针对多个索引列有效，如果有 column 没有被索引，查询效率可能会因为没有选择 OR 而降低

  ```mysql
  EXPLAIN SELECT * FROM emp WHERE id = 1 UNION SELECT * FROM emp WHERE age = 30;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL OR条件查询2.png)

* UNION 要优于 OR 的原因：

  * UNION 语句的 type 值为 ref，OR 语句的 type 值为 range
  * UNION 语句的 ref 值为 const，OR 语句的 ref 值为 null，const 表示是常量值引用，非常快



***



#### 分页

一般分页查询时，通过创建覆盖索引能够比较好地提高性能

一个常见的问题是 `LIMIT 200000,10`，此时需要MySQL扫描前 200010 记录，仅仅返回200000 - 200010 之间的记录，其他记录丢弃，查询排序的代价非常大

* 分页查询：

  ```mysql
  EXPLAIN SELECT * FROM tb_user_1 LIMIT 200000,10;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL分页查询1.png)

* 优化方式一：子查询，在索引上完成排序分页操作，最后根据主键关联回原表查询所需要的其他列内容

  ```mysql
  EXPLAIN SELECT * FROM tb_user_1 t,(SELECT id FROM tb_user_1 ORDER BY id LIMIT 200000,10) a WHERE t.id = a.id;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL分页查询2.png)

* 优化方式二：方案适用于主键自增的表，可以把 LIMIT 查询转换成某个位置的查询

  ```mysql
  EXPLAIN SELECT * FROM tb_user_1 WHERE id > 200000 LIMIT 10;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL分页查询3.png)



****



#### 使用提示

SQL 提示，是优化数据库的一个重要手段，就是在SQL语句中加入一些提示来达到优化操作的目的

* USE INDEX：在查询语句中表名的后面，添加 USE INDEX 来提供 MySQL 去参考的索引列表，可以让 MySQL 不再考虑其他可用的索引

  ```mysql
  CREATE INDEX idx_seller_name ON tb_seller(name);
  EXPLAIN SELECT * FROM tb_seller USE INDEX(idx_seller_name) WHERE name='小米科技';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用提示1.png)

* IGNORE INDEX：让 MySQL 忽略一个或者多个索引，则可以使用 IGNORE INDEX 作为提示

  ```mysql
  EXPLAIN SELECT * FROM tb_seller IGNORE INDEX(idx_seller_name) WHERE name = '小米科技';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用提示2.png)

* FORCE INDEX：为强制 MySQL 使用一个特定的索引，可在查询中使用 FORCE INDEX 作为提示

  ```mysql
  EXPLAIN SELECT * FROM tb_seller FORCE INDEX(idx_seller_name_sta_addr) WHERE NAME='小米科技';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-优化SQL使用提示3.png)



***



## 系统优化

### 应用优化

#### 连接池

在实际生产环境中，由于数据库本身的性能局限，就必须要对前台的应用进行一些优化，来降低数据库的访问压力

池化技术：对于访问数据库来说，建立连接的代价是比较昂贵的，频繁的创建关闭连接比较耗费资源，有必要建立数据库连接池，以提高访问的性能



***



#### 减少访问

避免对数据进行重复检索：能够一次连接就获取到结果的，就不用两次连接，这样可以大大减少对数据库无用的重复请求

* 查询数据：

  ```mysql
  SELECT id,name FROM tb_book;
  SELECT id,status FROM tb_book; -- 向数据库提交两次请求，数据库就要做两次查询操作
  -- > 优化为:
  SELECT id,name,statu FROM tb_book;
  ```

* 插入数据：

  ```mysql
  INSERT INTO tb_test VALUES(1,'Tom');
  INSERT INTO tb_test VALUES(2,'Cat');
  INSERT INTO tb_test VALUES(3,'Jerry');	-- 连接三次数据库
  -- >优化为
  INSERT INTO tb_test VALUES(1,'Tom'),(2,'Cat')，(3,'Jerry');	-- 连接一次
  ```

增加 cache 层：在应用中增加缓存层来达到减轻数据库负担的目的。可以部分数据从数据库中抽取出来放到应用端以文本方式存储， 或者使用框架（Mybatis）提供的一级缓存 / 二级缓存，或者使用Redis数据库来缓存数据 



***



#### 负载均衡

负载均衡是应用中使用非常普遍的一种优化方法，机制就是利用某种均衡算法，将固定的负载量分布到不同的服务器上， 以此来降低单台服务器的负载，达到优化的效果

* 分流查询：通过 MySQL 的主从复制，实现读写分离，使增删改操作走主节点，查询操作走从节点，从而可以降低单台服务器的读写压力

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-负载均衡主从复制.jpg)

* 分布式数据库架构：适合大数据量、负载高的情况，具有良好的拓展性和高可用性。通过在多台服务器之间分布数据，可以实现在多台服务器之间的负载均衡，提高访问效率



***



### 缓存优化

#### 工作流程

开启 Mysql 的查询缓存，当执行完全相同的 SQL 语句的时候，服务器就会直接从缓存中读取结果，当数据被修改，之前的缓存会失效，修改比较频繁的表不适合做查询缓存

查询过程：

1. 客户端发送一条查询给服务器
2. 服务器先会检查查询缓存，如果命中了缓存，则立即返回存储在缓存中的结果，否则进入下一阶段
3. 服务器端进行 SQL 解析、预处理，再由优化器生成对应的执行计划
4. MySQL 根据优化器生成的执行计划，调用存储引擎的 API 来执行查询
5. 将结果返回给客户端

<img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-查询的执行过程.jpg" style="zoom: 80%;" />



***



#### 缓存配置

1. 查看当前的 MySQL 数据库是否支持查询缓存：

   ```mysql
   SHOW VARIABLES LIKE 'have_query_cache';	-- YES
   ```

2. 查看当前MySQL是否开启了查询缓存：

   ```mysql
   SHOW VARIABLES LIKE 'query_cache_type';	-- OFF
   ```

   参数说明：

   * OFF 或 0：查询缓存功能关闭

   * ON 或 1：查询缓存功能打开，查询结果符合缓存条件即会缓存，否则不予缓存；可以显式指定 SQL_NO_CACHE 不予缓存

   * DEMAND 或 2：查询缓存功能按需进行，显式指定 SQL_CACHE 的 SELECT 语句才缓存，其它不予缓存

     ```mysql
     SELECT SQL_CACHE id, name FROM customer; -- SQL_CACHE:查询结果可缓存
     SELECT SQL_NO_CACHE id, name FROM customer;-- SQL_NO_CACHE:不使用查询缓存
     ```

3. 查看查询缓存的占用大小：

   ```mysql
   SHOW VARIABLES LIKE 'query_cache_size';-- 单位是字节 1048576 / 1024 = 1024 = 1KB
   ```

4. 查看查询缓存的状态变量：

   ```mysql
   SHOW STATUS LIKE 'Qcache%';
   ```

   <img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-查询缓存的状态变量.png" style="zoom:67%;" />

   | 参数                    | 含义                                                         |
   | ----------------------- | ------------------------------------------------------------ |
   | Qcache_free_blocks      | 查询缓存中的可用内存块数                                     |
   | Qcache_free_memory      | 查询缓存的可用内存量                                         |
   | Qcache_hits             | 查询缓存命中数                                               |
   | Qcache_inserts          | 添加到查询缓存的查询数                                       |
   | Qcache_lowmen_prunes    | 由于内存不足而从查询缓存中删除的查询数                       |
   | Qcache_not_cached       | 非缓存查询的数量（由于 query_cache_type 设置而无法缓存或未缓存） |
   | Qcache_queries_in_cache | 查询缓存中注册的查询数                                       |
   | Qcache_total_blocks     | 查询缓存中的块总数                                           |

5. 配置 my.cnf：

   ```sh
   sudo chmod 666 /etc/mysql/my.cnf
   vim my.cnf
   # mysqld中配置缓存
   query_cache_type=1
   ```

   重启服务既可生效，执行SQL语句进行验证 ，执行一条比较耗时的SQL语句，然后再多执行几次，查看后面几次的执行时间；获取通过查看查询缓存的缓存命中数，来判定是否走查询缓存



***



#### 缓存失效

查询缓存失效的情况：

* SQL 语句不一致的情况，要想命中查询缓存，查询的 SQL 语句必须一致

  ```mysql
  select count(*) from tb_item;
  Select count(*) from tb_item;	-- 不走缓存，首字母不一致
  ```

* 当查询语句中有一些不确定查询时，则不会缓存，比如：now()、current_date()、curdate()、curtime()、rand()、uuid()、user()、database() 

  ```mysql
  SELECT * FROM tb_item WHERE updatetime < NOW() LIMIT 1;
  SELECT USER();
  SELECT DATABASE();
  ```

*  不使用任何表查询语句：

  ```mysql
  SELECT 'A';
  ```

* 查询 mysql、information_schema、performance_schema 等系统表时，不走查询缓存：

  ```mysql
  SELECT * FROM information_schema.engines;
  ```

* 在存储过程、触发器或存储函数的主体内执行的查询，缓存失效

* 如果表更改，则使用该表的所有高速缓存查询都将变为无效并从高速缓存中删除，包括使用 MERGE 映射到已更改表的表的查询，比如：INSERT、UPDATE、DELETE、ALTER TABLE、DROP TABLE、DROP DATABASE 

  

***



### 内存优化

#### 优化原则

三个原则：

* 将尽量多的内存分配给 MySQL 做缓存，但也要给操作系统和其他程序预留足够内存
* MyISAM 存储引擎的数据文件读取依赖于操作系统自身的 IO 缓存，如果有 MyISAM 表，就要预留更多的内存给操作系统做 IO 缓存
* 排序区、连接区等缓存是分配给每个数据库会话（Session）专用的，值的设置要根据最大连接数合理分配，如果设置太大，不但浪费资源，而且在并发数较高时会导致物理内存耗尽



***



#### MyISAM

MyISAM 存储引擎使用 key_buffer 缓存索引块，加速 MyISAM 索引的读写速度。对于 MyISAM 表的数据块没有特别的缓存机制，完全依赖于操作系统的 IO 缓存。

* key_buffer_size：该变量决定 MyISAM 索引块缓存区的大小，直接影响到 MyISAM 表的存取效率

  ```mysql
  SHOW VARIABLES LIKE 'key_buffer_size';	-- 单位是字节
  ```

  在 MySQL 配置文件中设置该值，建议至少将1/4可用内存分配给 key_buffer_size：

  ```sh
  vim /etc/mysql/my.cnf
  key_buffer_size=1024M
  ```

* read_buffer_size：如果需要经常顺序扫描 MyISAM 表，可以通过增大 read_buffer_size 的值来改善性能。但 read_buffer_size 是每个 Session 独占的，如果默认值设置太大，并发环境就会造成内存浪费

* read_rnd_buffer_size：对于需要做排序的 MyISAM 表的查询，如带有 ORDER BY 子句的语句，适当增加该的值，可以改善此类的 SQL 的性能。但是 read_rnd_buffer_size 是每个 Session 独占的，如果默认值设置太大，就会造成内存浪费



***



#### InnoDB

Innodb 用一块内存区做 IO 缓存池，该缓存池不仅用来缓存 Innodb 的索引块，也用来缓存 Innodb 的数据块

* innodb_buffer_pool_size：该变量决定了 Innodb 存储引擎表数据和索引数据的最大缓存区大小

  ```mysql
  SHOW VARIABLES LIKE 'innodb_buffer_pool_size';
  ```

  在保证操作系统及其他程序有足够内存可用的情况下，innodb_buffer_pool_size 的值越大，缓存命中率越高，访问InnoDB表需要的磁盘I/O 就越少，性能也就越高。通过配置文件修改：

  ```sh
  innodb_buffer_pool_size=512M
  ```

* innodb_log_buffer_size：该值决定了 Innodb 日志缓冲区的大小，保存要写入磁盘上的日志文件数据

  对于可能产生大量更新记录的大事务，增加 innodb_log_buffer_size 的大小，可以避免 Innodb 在事务提交前就执行不必要的日志写入磁盘操作，影响执行效率。通过配置文件修改：

  ```sh
  innodb_log_buffer_size=10M
  ```



***



### 并发参数

MySQL Server 是多线程结构，包括后台线程和客户服务线程。多线程可以有效利用服务器资源，提高数据库的并发性能。在 MySQL 中，控制并发连接和线程的主要参数：

* max_connections：控制允许连接到MySQL数据库的最大连接数，默认值是 151

  如果状态变量 connection_errors_max_connections 不为零，并且一直增长，则说明不断有连接请求因数据库连接数已达到允许最大值而失败，这时可以考虑增大max_connections 的值

  Mysql 最大可支持的连接数取决于很多因素，包括操作系统平台的线程库的质量、内存大小、每个连接的负荷、CPU的处理速度、期望的响应时间等。在 Linux 平台下，性能好的服务器，可以支持 500-1000 个连接，需要根据服务器性能进行评估设定

* back_log：控制 MySQL 监听 TCP 端口时的积压请求栈的大小

  如果 Mysql 的连接数达到 max_connections 时，新来的请求将会被存在堆栈中，以等待某一连接释放资源，该堆栈的数量即 back_log。如果等待连接的数量超过 back_log，将不被授予连接资源直接报错

  5.6.6 版本之前默认值为 50，之后的版本默认为 `50 + (max_connections/5)`，但最大不超过900，如果需要数据库在较短的时间内处理大量连接请求， 可以考虑适当增大 back_log 的值

* table_open_cache：控制所有 SQL 语句执行线程可打开表缓存的数量

  在执行 SQL 语句时，每个执行线程至少要打开1个表缓存，该参数的值应该根据设置的最大连接数以及每个连接执行关联查询中涉及的表的最大数量来设定：`max_connections * N`

* thread_cache_size：可控制 MySQL 缓存客户服务线程的数量

  为了加快连接数据库的速度，MySQL 会缓存一定数量的客户服务线程以备重用，池化思想

* innodb_lock_wait_timeout：设置 InnoDB 事务等待行锁的时间，默认值是 50ms

  对于需要快速反馈的业务系统，可以将行锁的等待时间调小，以避免事务被长时间挂起； 对于后台运行的批量处理程序来说，可以将行锁的等待时间调大，以避免发生大的回滚操作



***



## 主从复制

### 基本介绍

复制是指将主数据库的 DDL 和 DML 操作通过二进制日志传到从库服务器中，然后在从库上对这些日志重新执行（也叫重做），从而使得从库和主库的数据保持同步

MySQL 支持一台主库同时向多台从库进行复制，从库同时也可以作为其他从服务器的主库，实现链状复制

MySQL 复制的优点主要包含以下三个方面：

- 主库出现问题，可以快速切换到从库提供服务

- 可以在从库上执行查询操作，从主库中更新，实现读写分离

- 可以在从库中执行备份，以避免备份期间影响主库的服务

**读写分离**：

* 读写分离可以降低主库的访问压力，提高系统的并发能力
* 主库不建查询的索引，从库建查询的索引。因为索引需要维护的，比如插入一条数据，不仅要在聚簇索引上面插入，对应的二级索引也得插入，修改也是一样的。所以将读操作分到从库了之后，可以在主库把查询要用的索引删了，减少写操作对主库的影响



***



### 复制原理

MySQL 的主从复制原理图：

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-主从复制原理图.jpg)

主从复制主要依赖的是 binlog，MySQL 默认是异步复制，需要三个线程：**master（binlog dump thread）、slave（I/O thread 、SQL thread）**

- binlog dump thread：在主库事务提交时，负责把数据变更作为事件 Events 记录在二进制日志文件 binlog 中，并通知 slave 有数据更新
- I/O thread：负责从主服务器上拉取二进制日志，并将 binlog 日志内容依次写到 relay log 文件的最末端，并将新的 binlog 文件名和 offset 记录到 master-info 文件中，以便下一次读取日志时能告诉 master 服务器从指定 binlog 日志文件及位置开始读取新的 binlog 日志内容
- SQL thread：监测本地 relay log 中新增了日志内容，读取中继日志并重做其中的 SQL 语句
- 从库在 relay-log.info 中记录当前应用中继日志的文件名和位置点以便下一次执行

同步与异步：

* 异步复制有数据丢失风险，例如数据还未同步到从库，主库就给客户端响应，然后主库挂了，此时从库晋升为主库的话数据是缺失的
* 同步复制，主库需要将 binlog 复制到所有从库，等所有从库响应了之后才会给客户端响应，这样的话性能很差，一般不会选择同步复制
* MySQL 5.7 之出现了半同步复制，有参数可以选择成功同步几个从库就返回响应

并行复制：MySQL 5.6 版本增加了并行复制功能，为了改善复制延迟问题，在从库中有两个线程 IO Thread 和 SQL Thread，以采用多线程机制来促进执行，减少从库复制延迟



****



### 主从延迟

主从延迟就是主从之间是存在一定时间的数据不一致：

- 主库 A 执行完成一个事务，写入 binlog，该时刻记为 T1
- 传给从库 B，从库接受完这个 binlog 的时刻记为 T2
- 从库 B 执行完这个事务，该时刻记为 T3

同一个事务，从库执行完成的时间和主库执行完成的时间之间的差值，即 T3-T，通过在从库执行 `show slave status` 命令，返回结果会显示 seconds_behind_master 表示当前从库延迟了多少秒

- 每一个事务的 binlog 都有一个时间字段，用于记录主库上写入的时间
- 从库取出当前正在执行的事务的时间字段，跟系统的时间进行相减，得到的就是 seconds_behind_master

主从延迟的原因：

* 从库的查询压力大
* 大事务的执行，主库必须要等到事务完成之后才会写入 binlog，导致从节点出现应用 binlog 延迟
* 主库的 DDL，从库与主库的 DDL 同步是串行进行，DDL 在主库执行时间很长，那么从库也会消耗同样的时间
* 锁冲突问题也可能导致从节点的 SQL 线程执行慢
* 从库的机器性能比主库的差，导致从库的复制能力弱

主从同步问题永远都是**一致性和性能的权衡**，需要根据实际的应用场景，可以采取下面的办法：

* **二次查询**，如果从库查不到数据，则再去主库查一遍，由 API 封装，比较简单，但导致主库压力大
* 降低多线程大事务并发的概率，优化业务逻辑
* 优化 SQL，避免慢 SQL，减少批量操作
* 提高从库机器的配置，减少主库写 binlog 和从库读 binlog 的效率差
* 尽量采用短的链路，主库和从库服务器的距离尽量要短，提升端口带宽，减少 binlog 传输的网络延时
* 实时性要求高的业务读强制走主库，从库只做灾备，备份
* 强制将写之后**立刻读的操作转移到主库**，比如刚注册的用户，直接登录从库查询可能查询不到，先走主库登录



****



### 搭建流程

#### master

1. 在master 的配置文件（/etc/mysql/my.cnf）中，配置如下内容：

   ```sh
   #mysql 服务ID,保证整个集群环境中唯一
   server-id=1
   
   #mysql binlog 日志的存储路径和文件名
   log-bin=/var/lib/mysql/mysqlbin
   
   #错误日志,默认已经开启
   #log-err
   
   #mysql的安装目录
   #basedir
   
   #mysql的临时目录
   #tmpdir
   
   #mysql的数据存放目录
   #datadir
   
   #是否只读,1 代表只读, 0 代表读写
   read-only=0
   
   #忽略的数据, 指不需要同步的数据库
   binlog-ignore-db=mysql
   
   #指定同步的数据库
   #binlog-do-db=db01
   ```

2. 执行完毕之后，需要重启 MySQL

3. 创建同步数据的账户，并且进行授权操作：

   ```mysql
   GRANT REPLICATION SLAVE ON *.* TO 'seazean'@'192.168.0.137' IDENTIFIED BY '123456';
   FLUSH PRIVILEGES;
   ```

4. 查看 master 状态：

   ```mysql
   SHOW MASTER STATUS;
   ```

   ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-查看master状态.jpg)

   * File：从哪个日志文件开始推送日志文件 
   * Position：从哪个位置开始推送日志
   * Binlog_Ignore_DB：指定不需要同步的数据库



***



#### slave

1. 在 slave 端配置文件中，配置如下内容：

   ```sh
   #mysql服务端ID,唯一
   server-id=2
   
   #指定binlog日志
   log-bin=/var/lib/mysql/mysqlbin
   ```

2. 执行完毕之后，需要重启MySQL

3. 指定当前从库对应的主库的IP地址、用户名、密码，从哪个日志文件开始的那个位置开始同步推送日志

   ```mysql
   CHANGE MASTER TO MASTER_HOST= '192.168.0.138', MASTER_USER='seazean', MASTER_PASSWORD='seazean', MASTER_LOG_FILE='mysqlbin.000001', MASTER_LOG_POS=413;
   ```

4. 开启同步操作：

   ```mysql
   START SLAVE;
   SHOW SLAVE STATUS;
   ```

5. 停止同步操作：

   ```mysql
   STOP SLAVE;
   ```



***



#### 验证

1. 在主库中创建数据库，创建表并插入数据：

   ```mysql
   CREATE DATABASE db01;
   USE db01;
   CREATE TABLE user(
   	id INT(11) NOT NULL AUTO_INCREMENT,
   	name VARCHAR(50) NOT NULL,
   	sex VARCHAR(1),
   	PRIMARY KEY (id)
   )ENGINE=INNODB DEFAULT CHARSET=utf8;
   
   INSERT INTO user(id,NAME,sex) VALUES(NULL,'Tom','1');
   INSERT INTO user(id,NAME,sex) VALUES(NULL,'Trigger','0');
   INSERT INTO user(id,NAME,sex) VALUES(NULL,'Dawn','1');
   ```

2. 在从库中查询数据，进行验证：

   在从库中，可以查看到刚才创建的数据库：

   ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-主从复制验证1.jpg)

   在该数据库中，查询表中的数据：

   ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-主从复制验证2.jpg)





****



## 锁机制

### 基本介绍

锁机制：数据库为了保证数据的一致性，在共享的资源被并发访问时变得安全有序所设计的一种规则

作用：锁机制类似于多线程中的同步，可以保证数据的一致性和安全性

锁的分类：

- 按操作分类：
  - 共享锁：也叫读锁。对同一份数据，多个事务读操作可以同时加锁而不互相影响 ，但不能修改数据
  - 排他锁：也叫写锁。当前的操作没有完成前，会阻断其他操作的读取和写入
- 按粒度分类：
  - 表级锁：会锁定整个表，开销小，加锁快；不会出现死锁；锁定力度大，发生锁冲突概率高，并发度最低，偏向MyISAM 存储引擎
  - 行级锁：会锁定当前操作行，开销大，加锁慢；会出现死锁；锁定力度小，发生锁冲突概率低，并发度高，偏向 InnoDB 存储引擎
  - 页级锁：锁的力度、发生冲突的概率和加锁开销介于表锁和行锁之间，会出现死锁，并发性能一般
- 按使用方式分类：
  - 悲观锁：每次查询数据时都认为别人会修改，很悲观，所以查询时加锁
  - 乐观锁：每次查询数据时都认为别人不会修改，很乐观，但是更新时会判断一下在此期间别人有没有去更新这个数据

* 不同存储引擎支持的锁

  | 存储引擎 | 表级锁   | 行级锁   | 页级锁 |
  | -------- | -------- | -------- | ------ |
  | MyISAM   | 支持     | 不支持   | 不支持 |
  | InnoDB   | **支持** | **支持** | 不支持 |
  | MEMORY   | 支持     | 不支持   | 不支持 |
  | BDB      | 支持     | 不支持   | 支持   |

从锁的角度来说：表级锁更适合于以查询为主，只有少量按索引条件更新数据的应用，如 Web 应用；而行级锁则更适合于有大量按索引条件并发更新少量不同数据，同时又有并查询的应用，如一些在线事务处理系统



***



### MyISAM

#### 表级锁

MyISAM 存储引擎只支持表锁，这也是 MySQL 开始几个版本中唯一支持的锁类型

MyISAM 引擎在执行查询语句之前，会**自动**给涉及到的所有表加读锁，在执行增删改之前，会自动给涉及的表加写锁，这个过程并不需要用户干预，所以用户一般不需要直接用 LOCK TABLE 命令给 MyISAM 表显式加锁

* 加锁命令：

  读锁：**所有**连接只能读取数据，不能修改

  写锁：**其他**连接不能查询和修改数据

  ```mysql
  -- 读锁
  LOCK TABLE table_name READ;
  
  -- 写锁
  LOCK TABLE table_name WRITE;
  ```

* 解锁命令：

  ```mysql
  -- 将当前会话所有的表进行解锁
  UNLOCK TABLES;
  ```

锁的兼容性：

* 对 MyISAM 表的读操作，不会阻塞其他用户对同一表的读请求，但会阻塞对同一表的写请求
* 对 MyISAM 表的写操作，则会阻塞其他用户对同一表的读和写操作

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM 锁的兼容性.png)

锁调度：MyISAM 的读写锁调度是写优先，因为写锁后其他线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永远阻塞，所以 MyISAM 不适合做写为主的表的存储引擎



***



#### 锁操作

##### 读锁

两个客户端操作 Client 1和 Client 2，简化为 C1、C2

* 数据准备：

  ```mysql
  CREATE TABLE `tb_book` (
    `id` INT(11) AUTO_INCREMENT,
    `name` VARCHAR(50) DEFAULT NULL,
    `publish_time` DATE DEFAULT NULL,
    `status` CHAR(1) DEFAULT NULL,
    PRIMARY KEY (`id`)
  ) ENGINE=MYISAM DEFAULT CHARSET=utf8 ;
  
  INSERT INTO tb_book (id, NAME, publish_time, STATUS) VALUES(NULL,'java编程思想','2088-08-01','1');
  INSERT INTO tb_book (id, NAME, publish_time, STATUS) VALUES(NULL,'mysql编程思想','2088-08-08','0');
  ```

* C1、C2 加读锁，同时查询可以正常查询出数据

  ```mysql
  LOCK TABLE tb_book READ;	-- C1、C2
  SELECT * FROM tb_book;		-- C1、C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM 读锁1.png)

* C1 加读锁，C1、C2查询未锁定的表，C1 报错，C2 正常查询

  ```mysql
  LOCK TABLE tb_book READ;	-- C1
  SELECT * FROM tb_user;		-- C1、C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM 读锁2.png)

  C1、C2 执行插入操作，C1 报错，C2 等待获取

  ```mysql
  INSERT INTO tb_book VALUES(NULL,'Spring高级','2088-01-01','1');	-- C1、C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM 读锁3.png)

  当在 C1 中释放锁指令 UNLOCK TABLES，C2 中的 INSERT 语句立即执行



***



##### 写锁

两个客户端操作 Client 1和 Client 2，简化为 C1、C2

* C1 加写锁，C1、C2查询表，C1 正常查询，C2 需要等待

  ```mysql
  LOCK TABLE tb_book WRITE;	-- C1
  SELECT * FROM tb_book;		-- C1、C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM 写锁1.png)

  当在 C1 中释放锁指令 UNLOCK TABLES，C2 中的 SELECT 语句立即执行

* C1、C2 同时加写锁

  ```mysql
  LOCK TABLE tb_book WRITE;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM 写锁2.png)

* C1 加写锁，C1、C2查询未锁定的表，C1 报错，C2 正常查询



***



#### 锁状态

* 查看锁竞争：

  ```mysql
  SHOW OPEN TABLES;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-锁争用情况查看1.png)

  In_user：表当前被查询使用的次数，如果该数为零，则表是打开的，但是当前没有被使用

  Name_locked：表名称是否被锁定，名称锁定用于取消表或对表进行重命名等操作

  ```mysql
  LOCK TABLE tb_book READ;	-- 执行命令
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-锁争用情况查看2.png)

* 查看锁状态：

  ```mysql
  SHOW STATUS LIKE 'Table_locks%';
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-MyISAM 锁状态.png)

  Table_locks_immediate：指的是能立即获得表级锁的次数，每立即获取锁，值加 1

  Table_locks_waited：指的是不能立即获取表级锁而需要等待的次数，每等待一次，该值加 1，此值高说明存在着较为严重的表级锁争用情况



***



### InnoDB

#### 行级锁

InnoDB 与 MyISAM 的**最大不同**有两点：一是支持事务；二是采用了行级锁，InnoDB 同时支持表锁和行锁

InnoDB  实现了以下两种类型的行锁：

- 共享锁 (S)：又称为读锁，简称 S 锁，就是多个事务对于同一数据可以共享一把锁，都能访问到数据，但是只能读不能修改
- 排他锁 (X)：又称为写锁，简称 X 锁，就是不能与其他锁并存，如一个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁，包括共享锁和排他锁，只有获取排他锁的事务是可以对数据读取和修改

对于 UPDATE、DELETE 和 INSERT 语句，InnoDB 会自动给涉及数据集加排他锁；对于普通 SELECT 语句，InnoDB 不会加任何锁

锁的兼容性：

- 共享锁和共享锁     兼容
- 共享锁和排他锁     冲突
- 排他锁和排他锁     冲突
- 排他锁和共享锁     冲突

可以通过以下语句显式给数据集加共享锁或排他锁：

```mysql
SELECT * FROM table_name WHERE ... LOCK IN SHARE MODE	-- 共享锁
SELECT * FROM table_name WHERE ... FOR UPDATE			-- 排他锁
```



***



#### 锁操作

两个客户端操作 Client 1和 Client 2，简化为 C1、C2

* 环境准备

  ```mysql
  CREATE TABLE test_innodb_lock(
  	id INT(11),
  	name VARCHAR(16),
  	sex VARCHAR(1)
  )ENGINE = INNODB DEFAULT CHARSET=utf8;
  
  INSERT INTO test_innodb_lock VALUES(1,'100','1');
  -- ..........
  
  CREATE INDEX idx_test_innodb_lock_id ON test_innodb_lock(id);
  CREATE INDEX idx_test_innodb_lock_name ON test_innodb_lock(name);
  ```

* 关闭自动提交功能：

  ```mysql
  SET AUTOCOMMIT=0;	-- C1、C2
  ```

  正常查询数据：

  ```mysql
  SELECT * FROM test_innodb_lock;	-- C1、C2
  ```

* 查询 id 为 3 的数据，正常查询：

  ```mysql
  SELECT * FROM test_innodb_lock WHERE id=3;	-- C1、C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁操作1.png)

* C1 更新 id 为 3 的数据，但不提交：

  ```mysql
  UPDATE test_innodb_lock SET name='300' WHERE id=3;	-- C1
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁操作2.png)

  C2 查询不到 C1 修改的数据，因为隔离界别为 REPEATABLE READ，C1 提交事务，C2 查询：

  ```mysql
  COMMIT;	-- C1
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁操作3.png)

  提交后仍然查询不到 C1 修改的数据，因为隔离级别可以防止脏读、不可重复读，所以 C2 需要提交才可以查询到其他事务对数据的修改：

  ```mysql
  COMMIT;	-- C2
  SELECT * FROM test_innodb_lock WHERE id=3;	-- C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁操作4.png)

* C1 更新 id 为 3 的数据，但不提交，C2 也更新 id 为 3 的数据：

  ```mysql
  UPDATE test_innodb_lock SET name='3' WHERE id=3;	-- C1
  UPDATE test_innodb_lock SET name='30' WHERE id=3;	-- C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁操作5.png)

  当 C1 提交，C2 直接解除阻塞，直接更新

* 操作不同行的数据：

  ```mysql
  UPDATE test_innodb_lock SET name='10' WHERE id=1;	-- C1
  UPDATE test_innodb_lock SET name='30' WHERE id=3;	-- C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁操作6.png)

  由于C1、C2 操作的不同行，获取不同的行锁，所以都可以正常获取行锁



***



#### 锁升级

无索引行锁升级为表锁：不通过索引检索数据，InnoDB 会将对表中的所有记录加锁，实际效果和**表锁**一样

索引失效会造成锁升级，实际开发过程应避免出现索引失效的状况

* 查看当前表的索引：

  ```mysql
  SHOW INDEX FROM test_innodb_lock;
  ```

* 关闭自动提交功能：

  ```mysql
  SET AUTOCOMMIT=0;	-- C1、C2
  ```

* 执行更新语句：

  ```mysql
  UPDATE test_innodb_lock SET sex='2' WHERE name=10;	-- C1
  UPDATE test_innodb_lock SET sex='2' WHERE id=3;		-- C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁升级.png)

  索引失效：执行更新时 name 字段为 varchar 类型，造成索引失效，最终行锁变为表锁 

​	

***



#### 间隙锁

当使用范围条件检索数据，并请求共享或排他锁时，InnoDB 会给符合条件的已有数据进行加锁，对于键值在条件范围内但并不存在的记录，叫做间隙（GAP）， InnoDB 会对间隙进行加锁，就是间隙锁

* 唯一索引加锁只有在值存在时才是行锁，值不存在会变成间隙锁，所以范围查询时容易出现间隙锁
* 对于联合索引且是唯一索引，如果 where 条件只包括联合索引的一部分，那么会加间隙锁

加锁的基本单位是 next-key lock，该锁是行锁和这条记录前面的 gap lock 的组合，就是行锁加间隙锁。

* 加锁遵循前开后闭原则
* 假设有索引值 10、11、13，那么可能的间隙锁包括：(负无穷,10]、(10,11]、(11,13]、(13,20,正无穷)，锁住索引 11 会同时对间隙 (10,11]、(11,13] 加锁

间隙锁优点：RR 级别下间隙锁可以解决事务的**幻读问题**，通过对间隙加锁，防止读取过程中数据条目发生变化

间隙锁危害：当锁定一个范围的键值后，即使某些不存在的键值也会被无辜的锁定，造成在锁定的时候无法插入锁定键值范围内的任何数据，在某些场景下这可能会对性能造成很大的危害

* 关闭自动提交功能：

  ```mysql
  SET AUTOCOMMIT=0;	-- C1、C2
  ```

* 查询数据表：

  ```mysql
  SELECT * FROM test_innodb_lock;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 间隙锁1.png)

* C1 根据 id 范围更新数据，C2 插入数据：

  ```mysql
  UPDATE test_innodb_lock SET name='8888' WHERE id < 4;	-- C1
  INSERT INTO test_innodb_lock VALUES(2,'200','2');		-- C2
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 间隙锁2.png)

  出现间隙锁，C2 被阻塞，等待 C1 提交事务后才能更新



***



#### 意向锁

InnoDB 为了支持多粒度的加锁，允许行锁和表锁同时存在，支持在不同粒度上的加锁操作，InnoDB 增加了意向锁（Intention Lock ）

意向锁是将锁定的对象分为多个层次，意向锁意味着事务希望在更细粒度上进行加锁，意向锁分为两种：

* 意向共享锁（IS）：事务有意向对表中的某些行加共享锁

* 意向排他锁（IX）：事务有意向对表中的某些行加排他锁

InnoDB 存储引擎支持的是行级别的锁，因此意向锁不会阻塞除全表扫描以外的任何请求，表级意向锁与行级锁的兼容性如下所示：

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-意向锁兼容性.png)

插入意向锁是在插入一行记录操作之前设置的一种间隙锁，这个锁释放了一种插入方式的信号，即多个事务在相同的索引间隙插入时如果不是插入间隙中相同的位置就不需要互相等待。假设某列有索引值2，6，只要两个事务插入位置不同，如事务 A 插入3，事务 B 插入4，那么就可以同时插入



****



#### 锁状态

```mysql
SHOW STATUS LIKE 'innodb_row_lock%';
```

<img src="https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB 锁争用.png" style="zoom: 80%;" />

参数说明：

* Innodb_row_lock_current_waits：当前正在等待锁定的数量

* Innodb_row_lock_time：从系统启动到现在锁定总时间长度

* Innodb_row_lock_time_avg：每次等待所花平均时长

* Innodb_row_lock_time_max：从系统启动到现在等待最长的一次所花的时间

* Innodb_row_lock_waits：系统启动后到现在总共等待的次数

当等待的次数很高，而且每次等待的时长也不短的时候，就需要分析系统中为什么会有如此多的等待，然后根据分析结果制定优化计划

查看锁状态：

```mysql
SELECT * FROM information_schema.innodb_locks;	#锁的概况
SHOW ENGINE INNODB STATUS; #InnoDB整体状态，其中包括锁的情况
```

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-InnoDB查看锁状态.png)

lock_id 是锁 id；lock_trx_id 为事务 id；lock_mode 为 X 代表排它锁（写锁）；lock_type 为 RECORD 代表锁为行锁（记录锁）



***



#### 锁优化

InnoDB 存储引擎实现了行级锁定，虽然在锁定机制的实现方面带来了性能损耗可能比表锁会更高，但是在整体并发处理能力方面要远远优于 MyISAM 的表锁，当系统并发量较高的时候，InnoDB 的整体性能远远好于 MyISAM 

InnoDB 的行级锁，如果使用不当可能会让InnoDB 的整体性能表现不仅不能比 MyISAM 高，甚至可能会更差

优化建议：

- 尽可能让所有数据检索都能通过索引来完成，避免无索引行锁升级为表锁
- 合理设计索引，尽量缩小锁的范围
- 尽可能减少索引条件及索引范围，避免间隙锁
- 尽量控制事务大小，减少锁定资源量和时间长度
- 尽可使用低级别事务隔离（需要业务层面满足需求）



***



### 乐观锁

悲观锁和乐观锁使用前提：

- 对于读的操作远多于写的操作的时候，一个更新操作加锁会阻塞所有的读取操作，降低了吞吐量，最后需要释放锁，锁是需要一些开销的，这时候可以选择乐观锁
- 如果是读写比例差距不是非常大或者系统没有响应不及时，吞吐量瓶颈的问题，那就不要去使用乐观锁，它增加了复杂度，也带来了业务额外的风险，这时候可以选择悲观锁

乐观锁的现方式：

* 版本号

  1. 给数据表中添加一个version列，每次更新后都将这个列的值加1

  2. 读取数据时，将版本号读取出来，在执行更新的时候，比较版本号

  3. 如果相同则执行更新，如果不相同，说明此条数据已经发生了变化

  4. 用户自行根据这个通知来决定怎么处理，比如重新开始一遍，或者放弃本次更新

     ```mysql
     -- 创建city表
     CREATE TABLE city(
     	id INT PRIMARY KEY AUTO_INCREMENT,  -- 城市id
     	NAME VARCHAR(20),                   -- 城市名称
     	VERSION INT                         -- 版本号
     );
     
     -- 添加数据
     INSERT INTO city VALUES (NULL,'北京',1),(NULL,'上海',1),(NULL,'广州',1),(NULL,'深圳',1);
     
     -- 修改北京为北京市
     -- 1.查询北京的version
     SELECT VERSION FROM city WHERE NAME='北京';
     -- 2.修改北京为北京市，版本号+1。并对比版本号
     UPDATE city SET NAME='北京市',VERSION=VERSION+1 WHERE NAME='北京' AND VERSION=1;
     ```

* 时间戳

  - 和版本号方式基本一样，给数据表中添加一个列，名称无所谓，数据类型需要是**timestamp**
  - 每次更新后都将最新时间插入到此列
  - 读取数据时，将时间读取出来，在执行更新的时候，比较时间
  - 如果相同则执行更新，如果不相同，说明此条数据已经发生了变化





***



## 日志

### 日志分类

在任何一种数据库中，都会有各种各样的日志，记录着数据库工作的过程，可以帮助数据库管理员追踪数据库曾经发生过的各种事件。

MySQL日志主要包括六种：

1. 重做日志（redo log）
2. 回滚日志（undo log）
3. 归档日志（binlog）（二进制日志）
4. 错误日志（errorlog）
5. 慢查询日志（slow query log）
6. 一般查询日志（general log）
7. 中继日志（relay log）



***



### 错误日志

错误日志是 MySQL 中最重要的日志之一，记录了当 mysqld 启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。当数据库出现任何故障导致无法正常使用时，可以首先查看此日志

该日志是默认开启的，默认位置是：`/var/log/mysql/error.log`

查看指令：

```mysql
SHOW VARIABLES LIKE 'log_error%';
```

查看日志内容：

```sh
tail -f /var/log/mysql/error.log
```



***



### 归档日志

#### 基本介绍

归档日志（BINLOG）记录了所有的 DDL（数据定义语言）语句和 DML（数据操作语言）语句，但**不包括数据查询语句**，在事务提交时写入。归档日志也叫二进制日志，是因为采用二进制进行存储

作用：**灾难时的数据恢复和 MySQL 的主从复制**

归档日志默认情况下是没有开启的，需要在 MySQL 配置文件中开启，并配置MySQL日志的格式：

```sh
cd /etc/mysql
vim my.cnf

# 配置开启binlog日志， 日志的文件前缀为 mysqlbin -----> 生成的文件名如: mysqlbin.000001
log_bin=mysqlbin
# 配置二进制日志的格式
binlog_format=STATEMENT
```

日志存放位置：配置时给定了文件名但是没有指定路径，日志默认写入Mysql的数据目录

日志格式：

* STATEMENT：该日志格式在日志文件中记录的都是 SQL 语句 (statement)，每一条对数据进行修改的 SQL都会记录在日志文件中，通过 mysqlbinlog 工具，可以查看到每条语句的文本。主从复制时，从库 (slave) 会将日志解析为原语句，并在从库重新执行一
* ROW：该日志格式在日志文件中记录的是每一行的数据变更，而不是记录 SQL 语句。比如执行 SQL 语句`update tb_book set status='1'`，如果是 STATEMENT，在日志中会记录一行 SQL 语句； 如果是 ROW，由于是对全表进行更新，就是每一行记录都会发生变更，ROW 格式的日志中会记录每一行的数据变更

* MIXED：这是 MySQL 默认的日志格式，混合了STATEMENT 和 ROW两种格式。MIXED 格式能尽量利用两种模式的优点，而避开他们的缺点



***



#### 日志读取

日志文件存储位置：/var/lib/mysql

由于日志以二进制方式存储，不能直接读取，需要用 mysqlbinlog 工具来查看，语法如下：

```sh
mysqlbinlog log-file;
```

查看 STATEMENT 格式日志：

* 执行插入语句：

  ```mysql
  INSERT INTO tb_book VALUES(NULL,'Lucene','2088-05-01','0');
  ```

* `cd /var/lib/mysql`：

  ```sh
  -rw-r-----  1 mysql mysql      177 5月  23 21:08 mysqlbin.000001
  -rw-r-----  1 mysql mysql       18 5月  23 21:04 mysqlbin.index
  ```

  mysqlbin.index：该文件是日志索引文件 ， 记录日志的文件名；

  mysqlbing.000001：日志文件

* 查看日志内容：

  ```sh
  mysqlbinlog mysqlbing.000001;
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-日志读取1.png)

查看 ROW 格式日志：

* 修改配置：

  ```sh
  # 配置二进制日志的格式
  binlog_format=ROW
  ```

* 插入数据：

  ```mysql
  INSERT INTO tb_book VALUES(NULL,'SpringCloud实战','2088-05-05','0');
  ```

* 查看日志内容：日志格式 ROW，直接查看数据是乱码，可以在 mysqlbinlog 后面加上参数 -vv 

  ```mysql
  mysqlbinlog -vv mysqlbin.000002
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-日志读取2.png)



***



#### 日志删除

对于比较繁忙的系统，生成日志量大，这些日志如果长时间不清除，将会占用大量的磁盘空间，需要删除日志

* Reset Master 指令删除全部 binlog 日志，删除之后，日志编号将从 xxxx.000001重新开始

  ```mysql
  Reset Master	-- MySQL指令
  ```

* 执行指令 `PURGE MASTER LOGS TO 'mysqlbin.***`，该命令将删除 ` ***` 编号之前的所有日志

* 执行指令 `PURGE MASTER LOGS BEFORE 'yyyy-mm-dd hh:mm:ss'` ，该命令将删除日志为 `yyyy-mm-dd hh:mm:ss` 之前产生的所有日志

* 设置参数 `--expire_logs_days=#`，此参数的含义是设置日志的过期天数，过了指定的天数后日志将会被自动删除，这样做有利于减少管理日志的工作量，配置 my.cnf 文件：

  ```sh
  log_bin=mysqlbin
  binlog_format=ROW
  --expire_logs_days=3
  ```



***



### 查询日志

查询日志中记录了客户端的所有操作语句，而二进制日志不包含查询数据的 SQL 语句

默认情况下， 查询日志是未开启的。如果需要开启查询日志，配置 my.cnf：

```sh
# 该选项用来开启查询日志，可选值0或者1，0代表关闭，1代表开启 
general_log=1
# 设置日志的文件名，如果没有指定，默认的文件名为host_name.log，存放在/var/lib/mysql
general_log_file=mysql_query.log
```

配置完毕之后，在数据库执行以下操作：

```mysql
SELECT * FROM tb_book;
SELECT * FROM tb_book WHERE id = 1;
UPDATE tb_book SET name = 'lucene入门指南' WHERE id = 5;
SELECT * FROM tb_book WHERE id < 8
```

执行完毕之后， 再次来查询日志文件：

![](https://gitee.com/seazean/images/raw/master/DB/MySQL-查询日志.png)



***



### 慢日志

慢查询日志记录所有执行时间超过 long_query_time 并且扫描记录数不小于 min_examined_row_limit 的所有的 SQL 语句的日志。long_query_time 默认为 10 秒，最小为 0， 精度到微秒

慢查询日志默认是关闭的，可以通过两个参数来控制慢查询日志，配置文件`/etc/mysql/my.cnf`：

```sh
# 该参数用来控制慢查询日志是否开启，可选值0或者1，0代表关闭，1代表开启 
slow_query_log=1 

# 该参数用来指定慢查询日志的文件名，存放在 /var/lib/mysql
slow_query_log_file=slow_query.log

# 该选项用来配置查询的时间限制，超过这个时间将认为值慢查询，将需要进行日志记录，默认10s
long_query_time=10
```

日志读取：

* 直接通过 cat 指令查询该日志文件：

  ```sh
  cat slow_query.log
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-慢日志读取1.png)

* 如果慢查询日志内容很多，直接查看文件比较繁琐， 可以借助于mysql 自带的 mysqldumpslow 工具， 来对慢查询日志进行分类汇总：

  ```sh
  mysqldumpslow slow_query.log
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/MySQL-慢日志读取2.png)





***



## 范式

建立科学的，规范的数据库就需要满足一些规则来优化数据的设计和存储，这些规则就称为范式。

### 第一范式

**1NF：**数据库表的每一列都是不可分割的原子数据项，不能是集合、数组等非原子数据项。即表中的某个列有多个值时，必须拆分为不同的列。简而言之，**第一范式每一列不可再拆分，称为原子性**

基本表：

![](https://gitee.com/seazean/images/raw/master/DB/普通表.png)
					

第一范式表：

![](https://gitee.com/seazean/images/raw/master/DB/第一范式.png)





****



### 第二范式

**2NF：**在满足第一范式的基础上，非主属性完全依赖于主码（主关键字、主键），消除非主属性对主码的部分函数依赖。简而言之，**表中的每一个字段 （所有列）都完全依赖于主键，记录的唯一性**

作用：遵守第二范式减少数据冗余，通过主键区分相同数据。

1. 函数依赖：A --> B，如果通过A属性(属性组)的值，可以确定唯一B属性的值，则称B依赖于A
   * 学号 --> 姓名；(学号，课程名称) --> 分数
2. 完全函数依赖：A --> B，如果A是一个属性组，则B属性值的确定需要依赖于A属性组的所有属性值
   * (学号，课程名称) --> 分数
3. 部分函数依赖：A --> B，如果A是一个属性组，则B属性值的确定只需要依赖于A属性组的某些属性值
   * (学号，课程名称) --> 姓名
4. 传递函数依赖：A --> B，B --> C，如果通过A属性(属性组)的值，可以确定唯一B属性的值，在通过B属性(属性组)的值，可以确定唯一C属性的值，则称C传递函数依赖于A
   * 学号 --> 系名，系名 --> 系主任
5. 码：如果在一张表中，一个属性或属性组，被其他所有属性所完全依赖，则称这个属性(属性组)为该表的码
   * 该表中的码：(学号，课程名称)
   * 主属性：码属性组中的所有属性
   * 非主属性：除码属性组以外的属性

![](https://gitee.com/seazean/images/raw/master/DB/第二范式.png)





****



### 第三范式

**3NF：**在满足第二范式的基础上，表中的任何属性不依赖于其它非主属性，消除传递依赖。简而言之，**非主键都直接依赖于主键，而不是通过其它的键来间接依赖于主键**。

作用：可以通过主键id区分相同数据，修改数据的时候只需要修改一张表（方便修改），反之需要修改多表。

![](https://gitee.com/seazean/images/raw/master/DB/第三范式.png)







***



### 总结

![](https://gitee.com/seazean/images/raw/master/DB/三大范式.png)







****





# JDBC

## 概述

JDBC（Java DataBase Connectivity，java数据库连接）是一种用于执行SQL语句的Java API，可以为多种关系型数据库提供统一访问，是由一组用Java语言编写的类和接口组成的。

JDBC 是 java 官方提供的一套规范（接口），用于帮助开发人员快速实现不同关系型数据库的连接

使用 JDBC 需要导包



***



## 功能类

### DriverManager

DriverManager：驱动管理对象

* 注册驱动
  * 注册给定的驱动：`public static void registerDriver(Driver driver)`

  * 代码实现语法：`Class.forName("com.mysql.jdbc.Driver)`

  * com.mysql.jdbc.Driver 中存在静态代码块

    ```java
    static {
        try {
            DriverManager.registerDriver(new Driver());
        } catch (SQLException var1) {
            throw new RuntimeException("Can't register driver!");
        }
    }
    ```

  * 不需要通过 DriverManager 调用静态方法 registerDriver，因为 Driver 类被使用，则自动执行静态代码块完成注册驱动

  * jar 包中 META-INF 目录下存在一个 java.sql.Driver 配置文件，文件中指定了 com.mysql.jdbc.Driver

* 获取数据库连接并返回连接对象

  `public static Connection getConnection(String url, String user, String password)`

  * url：指定连接的路径。语法：`jdbc:mysql://ip地址(域名):端口号/数据库名称`
  * user：用户名
  * password：密码



***



### Connection

Connection：数据库连接对象

- 获取执行者对象
  - 获取普通执行者对象：`Statement createStatement()`
  - 获取预编译执行者对象：`PreparedStatement prepareStatement(String sql)`
- 管理事务
  - 开启事务：`setAutoCommit(boolean autoCommit)`，false 开启事务，true 自动提交模式(默认)
  - 提交事务：`void commit()`
  - 回滚事务：`void rollback()`
- 释放资源
  - 释放此 Connection 对象的数据库和 JDBC 资源：`void close()`



***



### Statement

Statement：执行 sql 语句的对象

- 执行 DML 语句：`int executeUpdate(String sql)`
  - 返回值 int：返回影响的行数
  - 参数 sql：可以执行 insert、update、delete 语句
- 执行DQL语句：`ResultSet executeQuery(String sql)`
  - 返回值 ResultSet：封装查询的结果
  - 参数 sql：可以执行 select 语句
- 释放资源
  - 释放此 Statement 对象的数据库和 JDBC 资源：`void close()`



***



### ResultSet

ResultSet：结果集对象，ResultSet 对象维护了一个游标，指向当前的数据行，初始在第一行

- 判断结果集中是否有数据：`boolean next()`
  - 有数据返回 true，并将索引**向下移动一行**
  - 没有数据返回 false
- 获取结果集中**当前行**的数据：`XXX getXxx("列名")`
  - XXX 代表数据类型（要获取某列数据，这一列的数据类型）
  - 例如：String getString("name");   int getInt("age");
- 释放资源
  - 释放 ResultSet 对象的数据库和 JDBC 资源：`void close()`



***



### 代码实现

数据准备

```mysql
-- 创建db14数据库
CREATE DATABASE db14;

-- 使用db14数据库
USE db14;

-- 创建student表
CREATE TABLE student(
	sid INT PRIMARY KEY AUTO_INCREMENT,	-- 学生id
	NAME VARCHAR(20),					-- 学生姓名
	age INT,							-- 学生年龄
	birthday DATE,						-- 学生生日
);

-- 添加数据
INSERT INTO student VALUES (NULL,'张三',23,'1999-09-23'),(NULL,'李四',24,'1998-08-10'),
(NULL,'王五',25,'1996-06-06'),(NULL,'赵六',26,'1994-10-20');
```

JDBC 连接代码：

```java
public class JDBCDemo01 {
    public static void main(String[] args) throws Exception{
        //1.导入jar包
        //2.注册驱动
        Class.forName("com.mysql.jdbc.Driver");

        //3.获取连接
        Connection con = DriverManager.getConnection("jdbc:mysql://192.168.2.184:3306/db2","root","123456");

        //4.获取执行者对象
        Statement stat = con.createStatement();

        //5.执行sql语句，并且接收结果
        String sql = "SELECT * FROM user";
        ResultSet rs = stat.executeQuery(sql);

        //6.处理结果
        while(rs.next()) {
            System.out.println(rs.getInt("id") + "\t" + rs.getString("name"));
        }

        //7.释放资源
        con.close();
        stat.close();
        con.close();
    }
}

```





***



## 工具类

* 配置文件（在 src 下创建 config.properties）

  ```properties
  driverClass=com.mysql.jdbc.Driver
  url=jdbc:mysql://192.168.2.184:3306/db14
  username=root
  password=123456
  ```

* 工具类

  ```java
  public class JDBCUtils {
      //1.私有构造方法
      private JDBCUtils(){
      };
  
      //2.声明配置信息变量
      private static String driverClass;
      private static String url;
      private static String username;
      private static String password;
      private static Connection con;
  
      //3.静态代码块中实现加载配置文件和注册驱动
      static{
          try{
              //通过类加载器返回配置文件的字节流
              InputStream is = JDBCUtils.class.getClassLoader().
                  	getResourceAsStream("config.properties");
  
              //创建Properties集合，加载流对象的信息
              Properties prop = new Properties();
              prop.load(is);
  
              //获取信息为变量赋值
              driverClass = prop.getProperty("driverClass");
              url = prop.getProperty("url");
              username = prop.getProperty("username");
              password = prop.getProperty("password");
  
              //注册驱动
              Class.forName(driverClass);
  
          } catch (Exception e) {
              e.printStackTrace();
          }
      }
  
      //4.获取数据库连接的方法
      public static Connection getConnection() {
          try {
              con = DriverManager.getConnection(url,username,password);
          } catch (SQLException e) {
              e.printStackTrace();
          }
          return con;
      }
  
      //5.释放资源的方法
      public static void close(Connection con, Statement stat, ResultSet rs) {
          if(con != null) {
              try {
                  con.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
  
          if(stat != null) {
              try {
                  stat.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
  
          if(rs != null) {
              try {
                  rs.close();
              } catch (SQLException e) {
                  e.printStackTrace();
              }
          }
      }
  	//方法重载，可能没有返回值对象
      public static void close(Connection con, Statement stat) {
          close(con,stat,null);
      }
  }
  ```

  


****



## 数据封装

从数据库读取数据并封装成Student对象，需要：

- Student类成员变量对应表中的列

- 所有的基本数据类型需要使用包装类，**以防null值无法赋值**

  ```java
  public class Student {
      private Integer sid;
      private String name;
      private Integer age;
      private Date birthday;
      ........

- 数据准备

  ```mysql
  -- 创建db14数据库
  CREATE DATABASE db14;
  
  -- 使用db14数据库
  USE db14;
  
  -- 创建student表
  CREATE TABLE student(
  	sid INT PRIMARY KEY AUTO_INCREMENT,	-- 学生id
  	NAME VARCHAR(20),					-- 学生姓名
  	age INT,							-- 学生年龄
  	birthday DATE						-- 学生生日
  );
  
  -- 添加数据
  INSERT INTO student VALUES (NULL,'张三',23,'1999-09-23'),(NULL,'李四',24,'1998-08-10'),(NULL,'王五',25,'1996-06-06'),(NULL,'赵六',26,'1994-10-20');
  ```

- 操作数据库

  ```java
  public class StudentDaoImpl{
  	//查询所有学生信息
      @Override
      public ArrayList<Student> findAll() {
          //1. 
          ArrayList<Student> list = new ArrayList<>();
          Connection con = null;
          Statement stat = null;
          ResultSet rs = null;
          try{
              //2.获取数据库连接
  			con = JDBCUtils.getConnection();
  
             	//3.获取执行者对象
             	stat = con.createStatement();
  
             	//4.执行sql语句，并且接收返回的结果集
  			String sql = "SELECT * FROM student";
             	rs = stat.executeQuery(sql);
  
            	//5.处理结果集
             	while(rs.next()) {
                  Integer sid = rs.getInt("sid");
                 	String name = rs.getString("name");
                 	Integer age = rs.getInt("age");
                 	Date birthday = rs.getDate("birthday");
  
                 	//封装Student对象
                 	Student stu = new Student(sid,name,age,birthday);
                 	//将student对象保存到集合中
                 	list.add(stu);
             	}
         	} catch(Exception e) {
             	e.printStackTrace();
         	} finally {
             	//6.释放资源
             	JDBCUtils.close(con,stat,rs);
         	}
  		//将集合对象返回
  		return list;
      }
  
  	//添加学生信息
      @Override
      public int insert(Student stu) {
          Connection con = null;
          Statement stat = null;
          int result = 0;
          try{
              con = JDBCUtils.getConnection();
  
              //3.获取执行者对象
              stat = con.createStatement();
  
              //4.执行sql语句，并且接收返回的结果集
              Date d = stu.getBirthday();
              SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
              String birthday = sdf.format(d);
              String sql = "INSERT INTO student VALUES ('"+stu.getSid()+"','"+stu.getName()+"','"+stu.getAge()+"','"+birthday+"')";
              result = stat.executeUpdate(sql);
  
          } catch(Exception e) {
              e.printStackTrace();
          } finally {
              //6.释放资源
              JDBCUtils.close(con,stat);
          }
          //将结果返回
          return result;
      }
  }
  ```





***



## 注入攻击

### 攻击演示

SQL注入攻击演示

* 在登录界面，输入一个错误的用户名或密码，也可以登录成功 

  ![](https://gitee.com/seazean/images/raw/master/DB/SQL注入攻击演示.png)

* 原理：我们在密码处输入的所有内容，都应该认为是密码的组成，但是 Statement 对象在执行 sql 语句时，将一部分内容当做查询条件来执行

  ```mysql
  SELECT * FROM user WHERE loginname='aaa' AND password='aaa' OR '1'='1';
  ```




***



### 攻击解决

PreparedStatement：预编译 sql 语句的执行者对象，继承`PreparedStatement extends Statement`

* 在执行 sql 语句之前，将 sql 语句进行提前编译。明确 sql 语句的格式，剩余的内容都会认为是参数
* sql 语句中的参数使用 ? 作为占位符

为 ? 占位符赋值的方法：setXxx（参数1,参数2）

- 参数1：? 的位置编号(编号从1开始)

- 参数2：? 的实际参数

  ```java
  String sql = "SELECT * FROM user WHERE loginname=? AND password=?";
  pst = con.prepareStatement(sql);
  pst.setString(1,loginName);
  pst.setString(2,password);
  ```

执行 sql 语句的方法

- 执行 insert、update、delete 语句：`int executeUpdate()`
- 执行 select 语句：`ResultSet executeQuery()`





****



## 连接池

### 概念

数据库连接背景：数据库连接是一种关键的、有限的、昂贵的资源，这一点在多用户的网页应用程序中体现得尤为突出。对数据库连接的管理能显著影响到整个应用程序的伸缩性和健壮性，影响到程序的性能指标。

数据库连接池：**数据库连接池负责分配、管理和释放数据库连接**，它允许应用程序**重复使用**一个现有的数据库连接，而不是再重新建立一个，这项技术能明显提高对数据库操作的性能。

数据库连接池原理

![](https://gitee.com/seazean/images/raw/master/DB/数据库连接池原理图解.png)





****



### 自定义池

DataSource 接口概述：

* java.sql.DataSource 接口：数据源（数据库连接池）
* Java 中 DataSource 是一个标准的数据源接口，官方提供的数据库连接池规范，连接池类实现该接口
* 获取数据库连接对象：`Connection getConnection()`

自定义连接池：

```java
public class MyDataSource implements DataSource{
    //1.定义集合容器，用于保存多个数据库连接对象
    private static List<Connection> pool = Collections.synchronizedList(new ArrayList<Connection>());

    //2.静态代码块，生成10个数据库连接保存到集合中
    static {
        for (int i = 0; i < 10; i++) {
            Connection con = JDBCUtils.getConnection();
            pool.add(con);
        }
    }
    //3.返回连接池的大小
    public int getSize() {
        return pool.size();
    }

    //4.从池中返回一个数据库连接
    @Override
    public Connection getConnection() {
        if(pool.size() > 0) {
            //从池中获取数据库连接
            return pool.remove(0);
        }else {
            throw new RuntimeException("连接数量已用尽");
        }
    }
}
```

测试连接池功能：

```java
public class MyDataSourceTest {
    public static void main(String[] args) throws Exception{
        //创建数据库连接池对象
        MyDataSource dataSource = new MyDataSource();

        System.out.println("使用之前连接池数量：" + dataSource.getSize());//10
        
        //获取数据库连接对象
        Connection con = dataSource.getConnection();
        System.out.println(con.getClass());// JDBC4Connection

        //查询学生表全部信息
        String sql = "SELECT * FROM student";
        PreparedStatement pst = con.prepareStatement(sql);
        ResultSet rs = pst.executeQuery();

        while(rs.next()) {
            System.out.println(rs.getInt("sid") + "\t" + rs.getString("name") + "\t" + rs.getInt("age") + "\t" + rs.getDate("birthday"));
        }
        
        //释放资源
        rs.close();
        pst.close();
		//目前的连接对象close方法，是直接关闭连接，而不是将连接归还池中
        con.close();

        System.out.println("使用之后连接池数量：" + dataSource.getSize());//9
    }
}
```

结论：释放资源并没有把连接归还给连接池



***



### 归还连接

归还数据库连接的方式：继承方式、装饰者设计者模式、适配器设计模式、动态代理方式

#### 继承方式

继承（无法解决）

- 通过打印连接对象，发现 DriverManager 获取的连接实现类是 JDBC4Connection
- 自定义一个类，继承 JDBC4Connection 这个类，重写 close() 方法
- 通过查看 JDBC 工具类获取连接的方法我们发现：我们虽然自定义了一个子类，完成了归还连接的操作。但是DriverManager 获取的还是 JDBC4Connection 这个对象，并不是我们的子类对象。

代码实现

* 自定义继承连接类

  ```java
  //1.定义一个类，继承JDBC4Connection
  public class MyConnection1 extends JDBC4Connection{
      //2.定义Connection连接对象和容器对象的成员变量
      private Connection con;
      private List<Connection> pool;
  
      //3.通过有参构造方法为成员变量赋值
      public MyConnection1(String hostToConnectTo, int portToConnectTo, Properties info, String databaseToConnectTo, String url,Connection con,List<Connection> pool) throws SQLException {
          super(hostToConnectTo, portToConnectTo, info, databaseToConnectTo, url);
          this.con = con;
          this.pool = pool;
      }
  
      //4.重写close方法，完成归还连接
      @Override
      public void close() throws SQLException {
          pool.add(con);
      }
  }
  ```

* 自定义连接池类

  ```java
  //将之前的连接对象换成自定义的子类对象
  private static MyConnection1 con;
  
  //4.获取数据库连接的方法
  public static Connection getConnection() {
      try {
          //等效于：MyConnection1 con = new JDBC4Connection();  语法错误！
          con = DriverManager.getConnection(url,username,password);
      } catch (SQLException e) {
          e.printStackTrace();
      }
  
      return con;
  }
  ```

  

***



#### 装饰者

自定义类实现 Connection 接口，通过装饰设计模式，实现和 mysql 驱动包中的 Connection 实现类相同的功能

在实现类对每个获取的 Connection 进行装饰：把连接和连接池参数传递进行包装

特点：通过装饰设计模式连接类我们发现，有很多需要重写的方法，代码太繁琐

* 装饰设计模式类

  ```java
  //1.定义一个类，实现Connection接口
  public class MyConnection2 implements Connection {
      //2.定义Connection连接对象和连接池容器对象的变量
      private Connection con;
      private List<Connection> pool;
  
      //3.提供有参构造方法，接收连接对象和连接池对象，对变量赋值
      public MyConnection2(Connection con,List<Connection> pool) {
          this.con = con;
          this.pool = pool;
      }
  
      //4.在close()方法中，完成连接的归还
      @Override
      public void close() throws SQLException {
          pool.add(con);
      }
      //5.剩余方法，只需要调用mysql驱动包的连接对象完成即可
      @Override
      public Statement createStatement() throws SQLException {
          return con.createStatement();
      }
      ..........
  }
  ```

* 自定义连接池类

  ```java
  @Override
  public Connection getConnection() {
      if(pool.size() > 0) {
          //从池中获取数据库连接
          Connection con = pool.remove(0);
          //通过自定义连接对象进行包装
          MyConnection2 mycon = new MyConnection2(con,pool);
          //返回包装后的连接对象
          return mycon;
      }else {
          throw new RuntimeException("连接数量已用尽");
      }
  }
  ```

  

***



#### 适配器

使用适配器设计模式改进，提供一个适配器类，实现 Connection 接口，将所有功能进行实现（除了 close 方法），自定义连接类只需要继承这个适配器类，重写需要改进的 close() 方法即可。

特点：自定义连接类中很简洁。剩余所有的方法抽取到了适配器类中，但是适配器这个类还是我们自己编写。

* 适配器类

  ```java
  public abstract class MyAdapter implements Connection {
  
      // 定义数据库连接对象的变量
      private Connection con;
  
      // 通过构造方法赋值
      public MyAdapter(Connection con) {
          this.con = con;
      }
  
      // 所有的方法，均调用mysql的连接对象实现
      @Override
      public Statement createStatement() throws SQLException {
          return con.createStatement();
      }
  }
  ```

* 自定义连接类

  ```java
  public class MyConnection3 extends MyAdapter {
      //2.定义Connection连接对象和连接池容器对象的变量
      private Connection con;
      private List<Connection> pool;
  
      //3.提供有参构造方法，接收连接对象和连接池对象，对变量赋值
      public MyConnection3(Connection con,List<Connection> pool) {
          super(con);    // 将接收的数据库连接对象给适配器父类传递
          this.con = con;
          this.pool = pool;
      }
  
      //4.在close()方法中，完成连接的归还
      @Override
      public void close() throws SQLException {
          pool.add(con);
      }
  }
  ```

* 自定义连接池类

  ```java
  //从池中返回一个数据库连接
  @Override
  public Connection getConnection() {
      if(pool.size() > 0) {
          //从池中获取数据库连接
          Connection con = pool.remove(0);
          //通过自定义连接对象进行包装
          MyConnection3 mycon = new MyConnection3(con,pool);
          //返回包装后的连接对象
          return mycon;
      }else {
          throw new RuntimeException("连接数量已用尽");
      }
  }
  ```

  

***



#### 动态代理

使用动态代理的方式来改进

自定义数据库连接池类：

```java
public class MyDataSource implements DataSource {
    //1.准备一个容器。用于保存多个数据库连接对象
    private static List<Connection> pool = Collections.synchronizedList(new ArrayList<>());

    //2.定义静态代码块,获取多个连接对象保存到容器中
    static{
        for(int i = 1; i <= 10; i++) {
            Connection con = JDBCUtils.getConnection();
            pool.add(con);
        }
    }
    //3.提供一个获取连接池大小的方法
    public int getSize() {
        return pool.size();
    }

   	//动态代理方式
    @Override
    public Connection getConnection() throws SQLException {
        if(pool.size() > 0) {
            Connection con = pool.remove(0);

            Connection proxyCon = (Connection) Proxy.newProxyInstance(
                con.getClass().getClassLoader(), new Class[]{Connection.class}, 
                new InvocationHandler() {
                /*
                    执行Connection实现类连接对象所有的方法都会经过invoke
                    如果是close方法，归还连接
                    如果不是，直接执行连接对象原有的功能即可
                 */
                @Override
                public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                    if(method.getName().equals("close")) {
                        //归还连接
                        pool.add(con);
                        return null;
                    }else {
                        return method.invoke(con,args);
                    }
                }
            });
            return proxyCon;
        }else {
            throw new RuntimeException("连接数量已用尽");
        }
    }
}
```



***



### 开源项目

#### C3P0

使用 C3P0 连接池：

* 配置文件名称：c3p0-config.xml，必须放在 src 目录下

  ```xml
  <c3p0-config>
    <!-- 使用默认的配置读取连接池对象 -->
    <default-config>
    	<!--  连接参数 -->
      <property name="driverClass">com.mysql.jdbc.Driver</property>
      <property name="jdbcUrl">jdbc:mysql://192.168.2.184:3306/db14</property>
      <property name="user">root</property>
      <property name="password">123456</property>
      
      <!-- 连接池参数 -->
      <!--初始化数量-->
      <property name="initialPoolSize">5</property>
      <!--最大连接数量-->
      <property name="maxPoolSize">10</property>
      <!--超时时间 3000ms-->
      <property name="checkoutTimeout">3000</property>
    </default-config>
  
    <named-config name="otherc3p0"> 
      <!--  连接参数 -->
      <!-- 连接池参数 -->
    </named-config>
  </c3p0-config>
  ```
  
* 代码演示

  ```java
  public class C3P0Test1 {
      public static void main(String[] args) throws Exception{
          //1.创建c3p0的数据库连接池对象
          DataSource dataSource = new ComboPooledDataSource();
  
          //2.通过连接池对象获取数据库连接
          Connection con = dataSource.getConnection();
  
          //3.执行操作
          String sql = "SELECT * FROM student";
          PreparedStatement pst = con.prepareStatement(sql);
  
          //4.执行sql语句，接收结果集
          ResultSet rs = pst.executeQuery();
  
          //5.处理结果集
          while(rs.next()) {
              System.out.println(rs.getInt("sid") + "\t" + rs.getString("name") + "\t" + rs.getInt("age") + "\t" + rs.getDate("birthday"));
          }
  
          //6.释放资源
          rs.close();   pst.close();   con.close();
      }
  }
  ```

  

#### Druid

Druid 连接池：

* 配置文件：druid.properties，必须放在src目录下

  ```properties
  driverClassName=com.mysql.jdbc.Driver
  url=jdbc:mysql://192.168.2.184:3306/db14
  username=root
  password=123456
  initialSize=5
  maxActive=10
  maxWait=3000
  ```

* 代码演示

  ```java
  public class DruidTest1 {
      public static void main(String[] args) throws Exception{
          //获取配置文件的流对象
          InputStream is = DruidTest1.class.getClassLoader().getResourceAsStream("druid.properties");
  
          //1.通过Properties集合，加载配置文件
          Properties prop = new Properties();
          prop.load(is);
  
          //2.通过Druid连接池工厂类获取数据库连接池对象
          DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);
  
          //3.通过连接池对象获取数据库连接进行使用
          Connection con = dataSource.getConnection();
          
  		//4.执行sql语句，接收结果集
          String sql = "SELECT * FROM student";
          PreparedStatement pst = con.prepareStatement(sql);
          ResultSet rs = pst.executeQuery();
  
          //5.处理结果集
          while(rs.next()) {
              System.out.println(rs.getInt("sid") + "\t" + rs.getString("name") + "\t" + rs.getInt("age") + "\t" + rs.getDate("birthday"));
          }
  
          //6.释放资源
          rs.close();   pst.close();   con.close();
      }
  }
  
  ```



### 工具类

数据库连接池的工具类：

```java
public class DataSourceUtils {
    //1.私有构造方法
    private DataSourceUtils(){}

    //2.声明数据源变量
    private static DataSource dataSource;

    //3.提供静态代码块，完成配置文件的加载和获取数据库连接池对象
    static{
        try{
            //完成配置文件的加载
            InputStream is = DataSourceUtils.class.getClassLoader().getResourceAsStream("druid.properties");
            Properties prop = new Properties();
            prop.load(is);

            //获取数据库连接池对象
            dataSource = DruidDataSourceFactory.createDataSource(prop);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //4.提供一个获取数据库连接的方法
    public static Connection getConnection() {
        Connection con = null;
        try {
            con = dataSource.getConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return con;
    }

    //5.提供一个获取数据库连接池对象的方法
    public static DataSource getDataSource() {
        return dataSource;
    }

    //6.释放资源
    public static void close(Connection con, Statement stat, ResultSet rs) {
        if(con != null) {
            try {
                con.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if(stat != null) {
            try {
                stat.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if(rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
	//方法重载
    public static void close(Connection con, Statement stat) {
        if(con != null) {
            try {
                con.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

        if(stat != null) {
            try {
                stat.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}

```








***





# Redis

## NoSQL

### 概述

NoSQL (Not-Only SQL) ： 泛指非关系型的数据库，作为关系型数据库的补充。 

MySQL 支持 ACID 特性，保证可靠性和持久性，读取性能不高，因此需要缓存的来减缓数据库的访问压力。

作用：应对基于海量用户和海量数据前提下的数据处理问题

特征：

* 可扩容，可伸缩，SQL 数据关系过于复杂，Nosql 不存关系，只存数据
* 大数据量下高性能，数据不存取在磁盘 IO，存取在内存
* 灵活的数据模型，设计了一些数据存储格式，能保证效率上的提高
* 高可用，集群

常见的 Nosql：Redis、memcache、HBase、MongoDB

![](https://gitee.com/seazean/images/raw/master/DB/电商场景解决方案.png)



参考视频：https://www.bilibili.com/video/BV1CJ411m7Gc

参考视频：https://www.bilibili.com/video/BV1Rv41177Af



***



### Redis

Redis (REmote DIctionary Server) ：用 C 语言开发的一个开源的高性能键值对（key-value）数据库。

特征：

* 数据间没有必然的关联关系，**不存关系，只存数据**
* 数据**存储在内存**，存取速度快，解决了磁盘 IO 速度慢的问题
* 内部采用**单线程**机制进行工作
* 高性能，官方测试数据，50个并发执行100000 个请求,读的速度是110000 次/s,写的速度是81000次/s
* 多数据类型支持
  * 字符串类型：string（String）
  * 列表类型：list（LinkedList）
  * 散列类型：hash（HashMap）
  * 集合类型：set（HashSet）
  * 有序集合类型：zset/sorted_set（TreeSet）
* 支持持久化，可以进行数据灾难恢复

应用：

* 为热点数据加速查询（主要场景），如热点商品、热点新闻、热点资讯、推广类等高访问量信息等

* 即时信息查询，如排行榜、网站访问统计、公交到站信息、在线人数（聊天室、网站）、设备信号等

* 时效性信息控制，如验证码控制、投票控制等

* 分布式数据共享，如分布式集群架构中的 session 分离
* 消息队列



***



### 安装启动

#### CentOS

1. 下载 Redis

   下载安装包：

   ```sh
   wget http://download.redis.io/releases/redis-5.0.0.tar.gz
   ```

   解压安装包：

   ```sh
   tar –xvf redis-5.0.0.tar.gz
   ```

   编译（在解压的目录中执行）：

   ```sh
   make
   ```

   安装（在解压的目录中执行）：

   ```sh
   make install
   ```

   

2. 安装 Redis

   redis-server，服务器启动命令 客户端启动命令

   redis-cli，redis核心配置文件

   redis.conf，RDB文件检查工具（快照持久化文件）

   redis-check-dump，AOF文件修复工具

   redis-check-aof



***



#### Ubuntu

安装：

* Redis 5.0 被包含在默认的 Ubuntu 20.04 软件源中

  ```sh
  sudo apt update
  sudo apt install redis-server
  ```

* 检查Redis状态

  ```sh
  sudo systemctl status redis-server
  ```

启动：

* 启动服务器——参数启动

  ```sh
  redis-server [--port port]
  #redis-server --port 6379
  ```

* 启动服务器——配置文件启动

  ```sh
  redis-server config_file_name
  #redis-server /etc/redis/conf/redis-6397.conf
  ```

* 启动客户端：

  ```sh
  redis-cli [-h host] [-p port]
  #redis-cli -h 192.168.2.185 -p 6397
  ```

  注意：服务器启动指定端口使用的是--port，客户端启动指定端口使用的是-p



***



### 基本配置

#### 系统目录

1. 创建文件结构

   创建配置文件存储目录

   ```sh
   mkdir conf
   ```

   创建服务器文件存储目录（包含日志、数据、临时配置文件等）

   ```sh
   mkdir data
   ```

2. 创建配置文件副本放入 conf 目录，Ubuntu系统配置文件 redis.conf 在目录 `/etc/redis` 中

   ```sh
   cat redis.conf | grep -v "#" | grep -v "^$" -> /conf/redis-6379.conf
   ```
   
   去除配置文件的注释和空格，输出到新的文件，命令方式采用 redis-port.conf



***



#### 服务器

* 设置服务器以守护进程的方式运行，关闭后服务器控制台中将打印服务器运行信息（同日志内容相同）：

  ```sh
  daemonize yes|no
  ```

* 绑定主机地址，绑定本地IP地址，否则SSH无法访问：

  ```sh
  bind ip
  ```

* 设置服务器端口：

  ```sh
  port port
  ```

* 设置服务器文件保存地址：

  ```sh
  dir path
  ```

* 设置数据库的数量：

  ```sh
  databases 16
  ```

* 多服务器快捷配置：

  导入并加载指定配置文件信息，用于快速创建 redis 公共配置较多的 redis实例配置文件，便于维护

  ```sh
  include /path/conf_name.conf
  ```

  

***



#### 客户端

* 服务器允许客户端连接最大数量，默认0，表示无限制，当客户端连接到达上限后，Redis会拒绝新的连接：

  ```sh
  maxclients count
  ```

* 客户端闲置等待最大时长，达到最大值后关闭对应连接，如需关闭该功能，设置为 0：

  ```sh
  timeout seconds
  ```



***



#### 日志配置

* 设置服务器以指定日志记录级别：

  ```sh
  loglevel debug|verbose|notice|warning
  ```

* 日志记录文件名

  ```sh
  logfile filename
  ```

注意：日志级别开发期设置为verbose即可，生产环境中配置为notice，简化日志输出量，降低写日志IO的频度



**配置文件：**

```sh
bind 192.168.2.185
port 6379
#timeout 0
daemonize no
logfile /etc/redis/data/redis-6379.log
dir /etc/redis/data
dbfilename "dump-6379.rdb"
```





***



## 体系结构

### 存储对象

Redis 使用对象来表示数据库中的键和值，当在 Redis 数据库中新创建一个键值对时至少会创建两个对象，一个对象用作键值对的键（键对象），另一个对象用作键值对的值（值对象）

Redis 中对象由一个 redisObject 结构表示，该结构中和保存数据有关的三个属性分别是 type、 encoding、ptr：

```c
typedef struct redisObiect{
	//类型
	unsigned type:4;
	//编码
	unsigned encoding:4;
	//指向底层数据结构的指针
	void *ptr;
}
```

Redis 中主要数据结构有：简单动态字符串（SDS）、双端链表、字典、压缩列表、整数集合、跳跃表

Redis 并没有直接使用数据结构来实现键值对数据库，而是基于这些数据结构创建了一个对象系统，包含字符串对象、列表对象、哈希对象、集合对象和有序集合对象这五种类型的对象，而每种对象又通过不同的编码映射到不同的底层数据结构

Redis 自身是一个 Map，其中所有的数据都是采用 key : value 的形式存储，**键对象都是字符串对象**，而值对象有五种基本类型和三种高级类型对象

![](https://gitee.com/seazean/images/raw/master/DB/Redis-对象模型.png)





***



### 线程模型

Redis 基于 Reactor 模式开发了网络事件处理器，这个处理器被称为文件事件处理器（file event handler），这个文件事件处理器是单线程的，所以 Redis 叫做单线程的模型

文件事件处理器以单线程方式运行，但是使用 I/O 多路复用程序来监听多个套接字，既实现了高性能的网络通信模型，又很好地与 Redis 服务器中其他同样以单线程方式运行的模块进行对接，保持了 Redis 单线程设计的简单性

工作原理：

* 文件事件处理器使用 I/O 多路复用（multiplexing）程序来同时监听多个套接字，并根据套接字目前执行的任务来为套接字关联不同的事件处理器

* 当被监听的套接字准备好执行连接应答 (accept)、读取 (read)、写入 (write)、关闭 (close)等操作时，与操作相对应的文件事件就会产生，这时文件事件处理器会调用套接字之前关联好的事件处理器来处理事件

Redis 单线程也能高效的原因：

* 纯内存操作
* 核心是基于非阻塞的 IO 多路复用机制
* 底层使用 C 语言实现，C 语言实现的程序距离操作系统更近，执行速度相对会更快
* 单线程同时也避免了多线程的上下文频繁切换问题，预防了多线程可能产生的竞争问题



****



### 多线程

Redis6.0 引入多线程主要是为了提高网络 IO 读写性能，因为这是 Redis 中的一个性能瓶颈（Redis 的瓶颈主要受限于内存和网络），Redis 的多线程只是用来**处理网络数据的读写和协议解析**， 执行命令仍然是单线程顺序执行，因此不需要担心线程安全问题。

Redis6.0 的多线程默认是禁用的，只使用主线程。如需开启需要修改 redis 配置文件 `redis.conf` ：

```sh
io-threads-do-reads yesCopy to clipboardErrorCopied
```

开启多线程后，还需要设置线程数，否则是不生效的。同样需要修改 redis 配置文件 :

```sh
io-threads 4 #官网建议4核的机器建议设置为2或3个线程，8核的建议设置为6个线程
```



参考文章：https://blog.csdn.net/xp_xpxp/article/details/100999825



***





## 基本指令

### 操作指令

读写数据：

* 设置 key，value 数据：

  ```sh
  set key value
  #set name seazean
  ```

* 根据 key 查询对应的 value，如果**不存在，返回空（nil）**：

  ```sh
  get key
  #get name
  ```

帮助信息：

* 获取命令帮助文档

  ```sh
  help [command]
  #help set
  ```

* 获取组中所有命令信息名称

  ```sh
  help [@group-name]
  #help @string
  ```

退出服务

* 退出客户端：

  ```sh
  quit
  exit
  ```

* 退出客户端服务器快捷键：

  ```sh
  Ctrl+C
  ```

  

***



### key指令

key是一个字符串，通过key获取redis中保存的数据

* 基本操作

  ```sh
  del key				#删除指定key
  unlink key   		#非阻塞删除key，真正的删除会在后续异步操作
  exists key			#获取key是否存在
  type key			#获取key的类型
  sort key [ASC/DESC]	#对key中数据排序，默认对数字排序，并不更改集合中的数据位置，只是查询
  sort key alpha		#对key中字母排序
  rename key newkey	#改名
  renamenx key newkey	#改名
  ```

* 时效性控制

  ```sh
  expire key seconds			#为指定key设置有效期，单位为秒
  pexpire key milliseconds	#为指定key设置有效期，单位为毫秒
  expireat key timestamp		#为指定key设置有效期，单位为时间戳
  pexpireat key mil-timestamp	#为指定key设置有效期，单位为毫秒时间戳
  
  ttl key						#获取key的有效时间，每次获取会自动变化(减小)，类似于倒计时，
  							#-1代表永久性，-2代表不存在/失效
  pttl key					#获取key的有效时间，单位是毫秒，每次获取会自动变化(减小)
  persist key					#切换key从时效性转换为永久性
  ```

* 查询模式

  ```sh
  keys pattern	#查询key
  ```

  查询模式规则：*匹配任意数量的任意符号；?配合一个任意符号；[]匹配一个指定符号

  ```sh
  keys *			#查询所有key
  keys aa*		#查询所有以aa开头
  keys *bb		#查询所有以bb结尾
  keys ??cc		#查询所有前面两个字符任意，后面以cc结尾 
  keys user:?		#查询所有以user:开头，最后一个字符任意
  keys u[st]er:1	#查询所有以u开头，以er:1结尾，中间包含一个字母，s或t
  ```

  



***



### DB指令

Redis 在使用过程中，伴随着操作数据量的增加，会出现大量的数据以及对应的 key，数据不区分种类、类别混在一起，容易引起重复或者冲突

Redis 为每个服务提供 16 个数据库，编码 0-15，每个数据库之间相互独立，**共用 **Redis 内存，不区分大小

* 基本操作

  ```sh
  select index	#切换数据库，index从0-15取值
  ping			#测试数据库是否连接正常，返回PONG
  echo message	#控制台输出信息
  ```

* 扩展操作

  ```sh
  move key db		#数据移动到指定数据库，db是数据库编号
  dbsize			#获取当前数据库的数据总量，即key的个数
  flushdb			#清除当前数据库的所有数据
  flushall		#清除所有数据
  ```




****



### 通信指令

Redis 发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息。

Redis 客户端可以订阅任意数量的频道

![](https://gitee.com/seazean/images/raw/master/DB/Redis-发布订阅.png)

操作命令：

1. 打开一个客户端订阅 channel1：`SUBSCRIBE channel1`
2. 打开另一个客户端，给 channel1发布消息 hello：`publish channel1 hello`
3. 第一个客户端可以看到发送的消息

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-发布订阅指令操作.png" style="zoom:67%;" />

注意：发布的消息没有持久化，订阅的客户端只能收到订阅后发布的消息



****



### ACL指令

Redis ACL 是 Access Control List（访问控制列表）的缩写，该功能允许根据可以执行的命令和可以访问的键来限制某些连接

![](https://gitee.com/seazean/images/raw/master/DB/Redis-ACL指令.png)

* acl cat：查看添加权限指令类别
* acl whoami：查看当前用户

* acl setuser username on >password ~cached:* +get：设置有用户名、密码、ACL 权限（只能 get）





***



## 数据类型

### string

#### 简介

存储的数据：单个数据，最简单的数据存储类型，也是最常用的数据存储类型，实质上是存一个字符串，String 类型是二进制安全的，意味着 Redis 的 string 可以包含任何数据，比如图片或者序列化的对象

存储数据的格式：一个存储空间保存一个数据，每一个空间中只能保存一个字符串信息

存储内容：通常使用字符串，如果字符串以整数的形式展示，可以作为数字操作使用

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-string结构图.png" style="zoom:50%;" />

Redis 所有操作都是**原子性**的，采用**单线程**机制，命令是单个顺序执行，无需考虑并发带来影响，原子性就是有一个失败则都失败



***



#### 操作

指令操作：

* 数据操作：

  ```sh
  set key value			#添加/修改数据添加/修改数据
  del key					#删除数据
  setnx key value			#判定性添加数据，键值为空则设添加
  mset k1 v1 k2 v2...		#添加/修改多个数据，m：Multiple
  append key value		#追加信息到原始信息后部（如果原始信息存在就追加，否则新建）
  ```

* 查询操作

  ```sh
  get key					#获取数据
  mget key1 key2...		#获取多个数据
  strlen key				#获取数据字符个数（字符串长度）
  ```

* 设置数值数据增加/减少指定范围的值

  ```sh
  incr key					#key++
  incrby key increment		#key+increment
  incrbyfloat key increment	#对小数操作
  decr key					#key--
  decrby key increment		#key-increment
  ```

* 设置数据具有指定的生命周期

  ```sh
  setex key seconds value  		#设置key-value存活时间，seconds单位是秒
  psetex key milliseconds value	#毫秒级
  ```

注意事项：

1. 数据操作不成功的反馈与数据正常操作之间的差异

   * 表示运行结果是否成功
     (integer) 0  → false                 失败

     (integer) 1  → true                  成功

   * 表示运行结果值
     (integer) 3  → 3                        3个

     (integer) 1  → 1                         1个

2. 数据未获取到时，对应的数据为（nil），等同于null

3. **数据最大存储量**：512MB

4. string 在 redis 内部存储默认就是一个字符串，当遇到增减类操作 incr，decr 时**会转成数值型**进行计算

5. 按数值进行操作的数据，如果原始数据不能转成数值，或超越了redis 数值上限范围，将报错
   9223372036854775807（java 中 Long 型数据最大值，Long.MAX_VALUE）

6. redis 可用于控制数据库表主键id，为数据库表主键提供生成策略，保障数据库表的主键唯一性


单数据和多数据的选择：

* 单数据执行3条指令的过程：3 次发送 + 3 次处理 + 3次返回
* 多数据执行1条指令的过程：1 次发送 + 3 次处理 + 1次返回（发送和返回的事件略高于单数据）

<img src="https://gitee.com/seazean/images/raw/master/DB/string单数据与多数据操作.png" style="zoom: 33%;" />





***



#### 应用

主页高频访问信息显示控制，例如新浪微博大V主页显示粉丝数与微博数量

* 在 Redis 中为大V用户设定用户信息，以用户主键和属性值作为 key，后台设定定时刷新策略

  ```sh
  set user:id:3506728370:fans 12210947
  set user:id:3506728370:blogs 6164
  set user:id:3506728370:focuses 83
  ```

* 使用 JSON 格式保存数据

  ```sh
  user:id:3506728370 → {"fans":12210947,"blogs":6164,"focuses":83}
  ```

* key的设置约定：表名 : 主键名 : 主键值 : 字段名

  | 表名  | 主键名 | 主键值    | 字段名 |
  | ----- | ------ | --------- | ------ |
  | order | id     | 29437595  | name   |
  | equip | id     | 390472345 | type   |
  | news  | id     | 202004150 | title  |



***



#### 实现

Redis 字符串对象底层的数据结构实现主要是 int 和简单动态字符串 SDS，是可以修改的字符串，内部结构实现上类似于 Java 的 ArrayList，采用预分配冗余空间的方式来减少内存的频繁分配

```c
struct sdshdr{
     //记录buf数组中已使用字节的数量
     //等于 SDS 保存字符串的长度
     int len;
     //记录 buf 数组中未使用字节的数量
     int free;
     //字节数组，用于保存字符串
     char buf[];
}
```

![](https://gitee.com/seazean/images/raw/master/DB/Redis-string数据结构.png)

内部为当前字符串实际分配的空间 capacity 一般要高于实际字符串长度 len，当字符串长度小于 1M 时，扩容都是双倍现有的空间，如果超过 1M，扩容时一次只会多扩 1M 的空间。需要注意的是字符串最大长度为 512M



详解请参考文章：https://www.cnblogs.com/hunternet/p/9957913.html



***



### hash

#### 简介

数据存储需求：对一系列存储的数据进行编组，方便管理，典型应用存储对象信息

数据存储结构：一个存储空间保存多个键值对数据

hash类型：底层使用**哈希表**结构实现数据存储

<img src="https://gitee.com/seazean/images/raw/master/DB/hash结构图.png" style="zoom: 33%;" />

类似Map结构，左边是key，右边是值，中间叫field字段，本质上**hash存了一个key-value的存储空间**

hash 是指的一个数据类型，并不是一个数据

* 如果 field 数量较少，存储结构优化为**压缩列表结构**（有序）
* 如果 field 数量较多，存储结构使用 HashMap 结构（无序）



***



#### 操作

指令操作：

* 数据操作

  ```sh
  hset key field value		#添加/修改数据
  hdel key field1 [field2]	#删除数据，[]代表可选
  hsetnx key field value		#设置field的值，如果该field存在则不做任何操作
  hmset key f1 v1 f2 v2...	#添加/修改多个数据
  ```

* 查询操作

  ```sh
  hget key field				#获取指定field对应数据
  hgetall key					#获取指定key所有数据
  hmget key field1 field2...	#获取多个数据
  hexists key field			#获取哈希表中是否存在指定的字段
  hlen key					#获取哈希表中字段的数量
  ```

* 获取哈希表中所有的字段名或字段值

  ```sh
  hkeys key					#获取所有的field	
  hvals key					#获取所有的value
  ```

* 设置指定字段的数值数据增加指定范围的值

  ```sh
  hincrby key field increment		#指定字段的数值数据增加指定的值，increment为负数则减少
  hincrbyfloat key field increment#操作小数
  ```


注意事项

1. hash类型中value只能存储字符串，不允许存储其他数据类型，不存在嵌套现象，如果数据未获取到，对应的值为（nil）
2. 每个hash可以存储2^32 - 1个键值对
3. hash类型和对象的数据存储形式相似，并且可以灵活添加删除对象属性。但hash设计初衷不是为了存储大量对象而设计的，不可滥用，不可将hash作为对象列表使用
4. hgetall 操作可以获取全部属性，如果内部field过多，遍历整体数据效率就很会低，有可能成为数据访问瓶颈



***



#### 应用

```sh
user:id:3506728370 → {"name":"春晚","fans":12210862,"blogs":83}
```

对于以上数据，使用单条去存的话，存的条数会很多。但如果用 json 格式，存一条数据就够了。

假如现在粉丝数量发生了变化，要把整个值都改变，但是用单条存就不存在这个问题，只需要改其中一个就可以

<img src="https://gitee.com/seazean/images/raw/master/DB/hash应用场景结构图.png" style="zoom: 33%;" />



***



#### 实现

##### 底层结构

哈希类型的内部编码有两种：ziplist（压缩列表）、hashtable（哈希表、字典）

当存储的数据量比较小的情况下，Redis 才使用压缩列表来实现字典类型，具体需要满足两个条件：

- 当键值对个数小于 hash-max-ziplist-entries 配置（默认512个）
- 所有键值都小于 hash-max-ziplist-value 配置（默认64字节）

ziplist 使用更加紧凑的结构实现多个元素的连续存储，所以在节省内存方面比 hashtable 更加优秀，当 ziplist 无法满足哈希类型时，Redis 会使用 hashtable 作为哈希的内部实现，因为此时 ziplist 的读写效率会下降，而 hashtable 的读写时间复杂度为 O(1)



****



##### 压缩列表

压缩列表（ziplist）是列表和哈希的底层实现之一，压缩列表用来紧凑数据存储，节省内存，有序：

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-压缩列表数据结构.png" style="zoom:67%;" />

压缩列表是由一系列特殊编码的连续内存块组成的顺序型（sequential）数据结枃，一个压缩列表可以包含任意多个节点（entry），每个节点可以保存一个字节数组或者一个整数值



***



##### 哈希表

Redis 字典使用散列表为底层实现，一个散列表里面有多个散列表节点，每个散列表节点就保存了字典中的一个键值对，发生哈希冲突采用链表法解决，存储无序

* 为了避免散列表性能的下降，当装载因子大于 1 的时候，Redis 会触发扩容，将散列表扩大为原来大小的 2 倍左右
* 当数据动态减少之后，为了节省内存，当装载因子小于 0.1 的时候，Redis 就会触发缩容，缩小为字典中数据个数的大约 2 倍大小



***



### list

#### 简介

数据存储需求：存储多个数据，并对数据进入存储空间的顺序进行区分

数据存储结构：一个存储空间保存多个数据，且通过数据可以体现进入顺序，允许重复元素

list 类型：保存多个数据，底层使用**双向链表**存储结构实现，类似于 LinkedList

<img src="https://gitee.com/seazean/images/raw/master/DB/list结构图.png" style="zoom:33%;" />

如果两端都能存取数据的话，这就是双端队列，如果只能从一端进一端出，这个模型叫栈



***



#### 操作

指令操作：

* 数据操作

  ```sh
  lpush key value1 [value2]...#从左边添加/修改数据
  rpush key value1 [value2]...#从右边添加/修改数据
  lpop key					#从左边获取并移除第一个数据，类似于出栈/出队
  rpop key					#从右边获取并移除第一个数据
  lrem key count value		#删除指定数据，count=2删除2个，该value可能有多个(重复数据)
  ```

* 查询操作

  ```sh
  lrange key start stop	#从左边遍历数据并指定开始和结束索引，0是第一个索引，-1是终索引
  lindex key index		#获取指定索引数据，没有则为nil，没有索引越界
  llen key				#list中数据长度/个数
  ```

* 规定时间内获取并移除数据

  ```sh
  b							#代表阻塞
  blpop key1 [key2] timeout	#在指定时间内获取指定key(可以多个)的数据，超时则为(nil)
  							#可以从其他客户端写数据，当前客户端阻塞读取数据
  brpop key1 [key2] timeout	#从右边操作
  ```
  
* 复制操作

  ```sh
  brpoplpush source destination timeout	#从source获取数据放入destination，假如在指定时间内没有任何元素被弹出，则返回一个nil和等待时长。反之，返回一个含有两个元素的列表，第一个元素是被弹出元素的值，第二个元素是等待时长
  ```

注意事项

1. list 中保存的数据都是 string 类型的，数据总容量是有限的，最多 2^32 - 1 个元素（4294967295）
2. list 具有索引的概念，但操作数据时通常以队列的形式进行入队出队，或以栈的形式进行入栈出栈
3. 获取全部数据操作结束索引设置为 -1
4. list 可以对数据进行分页操作，通常第一页的信息来自于 list，第 2 页及更多的信息通过数据库的形式加载



***



#### 应用

企业运营过程中，系统将产生出大量的运营数据，如何保障多台服务器操作日志的统一顺序输出？

* 依赖 list 的数据具有顺序的特征对信息进行管理，右进左查或者左近左查
* 使用队列模型解决多路信息汇总合并的问题
* 使用栈模型解决最新消息的问题



****



#### 实现

##### 底层结构

在 Redis3.2 版本以前列表类型的内部编码有两种：ziplist（压缩列表）和 linkedlist（链表）

列表中存储的数据量比较小的时候，列表就会使用一块连续的内存存储，采用压缩列表的方式实现：

* 列表中保存的单个数据（有可能是字符串类型的）小于 64 字节
* 列表中数据个数少于 512 个

在 Redis3.2 版本 以后对列表数据结构进行了改造，使用 **quicklist（快速列表）**代替了 linkedlist



****



##### 链表结构

Redis 链表为**双向无环链表**，使用 listNode 结构表示

```c
typedef struct listNode
{ 
	// 前置节点 
	struct listNode *prev; 
	// 后置节点 
	struct listNode *next; 
	// 节点的值 
	void *value; 
} listNode;
```

![](https://gitee.com/seazean/images/raw/master/DB/Redis-链表数据结构.png)

- 双向：链表节点带有前驱、后继指针，获取某个节点的前驱、后继节点的时间复杂度为O(1)
- 无环：链表为非循环链表，表头节点的前驱指针和表尾节点的后继指针都指向NULL，对链表的访问以 NULL 为终点



***



##### 快速列表

quicklist 实际上是 ziplist 和 linkedlist 的混合体，将 linkedlist 按段切分，每一段使用 ziplist 来紧凑存储，多个 ziplist 之间使用双向指针串接起来，既满足了快速的插入删除性能，又不会出现太大的空间冗余

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-快速列表数据结构.png" style="zoom: 50%;" />



***



### set

#### 简介

数据存储需求：存储大量的数据，在查询方面提供更高的效率

数据存储结构：能够保存大量的数据，高效的内部存储机制，便于查询

set 类型：与 hash 存储结构哈希表完全相同，只是仅存储键不存储值（nil），所以添加，删除，查找的复杂度都是O(1)**，并且值是**不允许重复且无序**的，

<img src="https://gitee.com/seazean/images/raw/master/DB/set结构图.png" style="zoom: 33%;" />



***



#### 操作

指令操作：

* 数据操作

  ```sh
  sadd key member1 [member2]	#添加数据
  srem key member1 [member2]	#删除数据
  ```
  
* 查询操作

  ```sh
  smembers key				#获取全部数据
  scard key					#获取集合数据总量
  sismember key member		#判断集合中是否包含指定数据
  ```

* 随机操作

  ```sh
  spop key [count]			#随机获取集中的某个数据并将该数据移除集合
  srandmember key [count]		#随机获取集合中指定(数量)的数据

* 集合的交、并、差

  ```sh
  sinter key1 [key2...]  					#两个集合的交集，不存在为(empty list or set)
  sunion key1 [key2...]  					#两个集合的并集
  sdiff key1 [key2...]					#两个集合的差集
  
  sinterstore destination key1 [key2...]	#两个集合的交集并存储到指定集合中
  sunionstore destination key1 [key2...]	#两个集合的并集并存储到指定集合中
  sdiffstore destination key1 [key2...]	#两个集合的差集并存储到指定集合中
  ```

* 复制

  ```sh
  smove source destination member	#将指定数据从原始集合中移动到目标集合中
  ```


注意事项

1. set 类型不允许数据重复，如果添加的数据在 set 中已经存在，将只保留一份
2. set 虽然与 hash 的存储结构相同，但是无法启用 hash 中存储值的空间



***



#### 应用

应用场景：

1. 黑名单：资讯类信息类网站追求高访问量，但是由于其信息的价值，往往容易被不法分子利用，通过爬虫技术，快速获取信息，个别特种行业网站信息通过爬虫获取分析后，可以转换成商业机密。

   注意：爬虫不一定做摧毁性的工作，有些小型网站需要爬虫为其带来一些流量。

2. 白名单：对于安全性更高的应用访问，仅仅靠黑名单是不能解决安全问题的，此时需要设定可访问的用户群体， 依赖白名单做更为苛刻的访问验证


解决方案：

* 设定用户鉴别规则，周期性更新满足规则的黑名单加入 set 集合，用户行为信息达到后与黑名单进行对比
* 黑名单过滤IP地址：应用于开放游客访问权限的信息源
* 黑名单过滤设备信息：应用于限定访问设备的信息源
* 黑名单过滤用户：应用于基于访问权限的信息源



***



#### 实现

集合类型的内部编码有两种：

* intset（整数集合）：当集合中的元素都是整数且元素个数小于 set-maxintset-entries配置（默认512个）时，Redis 会选用 intset 来作为集合的内部实现，从而减少内存的使用

* hashtable（哈希表，字典）：当无法满足 intset 条件时，Redis 会使用 hashtable 作为集合的内部实现

整数集合（intset）是 Redis 用于保存整数值的集合抽象数据结构，可以保存类型为 int16_t、int32_t 或者 int64_t的整数值，并且保证集合中的元素是**有序不重复**的





***



### sorted

#### 简介

数据存储需求：数据排序有利于数据的有效展示，需要提供一种可以根据自身特征进行排序的方式

数据存储结构：新的存储模型，可以保存可排序的数据

sorted_set类型：在 set 的存储结构基础上添加可排序字段，类似于 TreeSet

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-sorted_set结构图.png" style="zoom: 67%;" />



****



#### 操作

指令操作：

* 数据操作

  ```sh
  zadd key score1 member1 [score2 member2]	#添加数据
  zrem key member [member ...]				#删除数据
  zremrangebyrank key start stop 				#删除指定索引的数据
  zremrangebyscore key min max				#删除指定分数区间内的数据
  zscore key member							#获取指定值的分数
  zincrby key increment member				#指定值的分数增加increment
  ```

* 查询操作

  ```sh
  zrange key start stop [WITHSCORES]		#获取全部数据，升序，WITHSCORES代表显示分数
  zrevrange key start stop [WITHSCORES]	#获取全部数据，降序
  
  zrangebyscore key min max [WITHSCORES] [LIMIT offset count]	#按条件获取数据，从小到大
  zrevrangebyscore key max min [WITHSCORES] [...]				#从大到小
  
  zcard key										#获取集合数据的总量
  zcount key min max								#获取指定分数区间内的数据总量
  zrank key member								#获取数据对应的索引（排名）升序
  zrevrank key member								#获取数据对应的索引（排名）降序
  ```

  * min 与 max 用于限定搜索查询的条件
  * start 与 stop 用于限定查询范围，作用于索引，表示开始和结束索引
  * offset 与 count 用于限定查询范围，作用于查询结果，表示开始位置和数据总量

* 集合的交、并操作

  ```sh
  zinterstore destination numkeys key [key ...]	#两个集合的交集并存储到指定集合中
  zunionstore destination numkeys key [key ...]	#两个集合的并集并存储到指定集合中
  ```

注意事项：

1. score保存的数据存储空间是64位，如果是整数范围是-9007199254740992~9007199254740992
2. score保存的数据也可以是一个双精度的double值，基于双精度浮点数的特征可能会丢失精度，慎重使用
3. sorted_set 底层存储还是基于 set 结构的，因此数据不能重复，如果重复添加相同的数据，score值将被反复覆盖，保留最后一次修改的结果



***



#### 应用

* 排行榜
* 对于基于时间线限定的任务处理，将处理时间记录为 score 值，利用排序功能区分处理的先后顺序
* 当任务或者消息待处理，形成了任务队列或消息队列时，对于高优先级的任务要保障对其优先处理，采用 score 记录权重



***



#### 实现

##### 底层结构

有序集合是由 ziplist（压缩列表）或 skiplist（跳跃表）组成的

当数据比较少时，有序集合使用的是 ziplist 存储的，使用 ziplist 格式存储需要满足以下两个条件：

- 有序集合保存的元素个数要小于 128 个；
- 有序集合保存的所有元素大小都小于 64 字节



***



##### 跳跃表

Redis 使用跳跃表作为有序集合键的底层实现之一，如果一个有序集合包含的**元素数量比较多**，又或者有序集合中元素的**成员是比较长的字符串**时，Redis 就会使用跳跃表来作为有序集合健的底层实现

跳跃表在链表的基础上增加了多级索引以提升查找的效率，索引是占内存的，所以是一个**空间换时间**的方案。原始链表中存储的有可能是很大的对象，而索引结点只需要存储关键值和几个指针，并不需要存储对象，因此当节点本身比较大或者元素数量比较多的时候，其优势可以被放大，而缺点则可以忽略

* 基于单向链表加索引的方式实现

- Redis 的跳跃表实现由 zskiplist 和 zskiplistnode 两个结构组成，其中 zskiplist 用于保存跳跃表信息（比如表头节点、表尾节点、长度），而 zskiplistnode 则用于表示跳跃表节点
- Redis 每个跳跃表节点的层高都是 1 至 32 之间的随机数（Redis5 之后最大层数为 64）
- 在同一个跳跃表中，多个节点可以包含相同的分值，但每个节点的成员对象必须是唯一的。跳跃表中的节点按照分值大小进行排序，当分值相同时节点按照成员对象的大小进行排序

![](https://gitee.com/seazean/images/raw/master/DB/Redis-跳跃表数据结构.png)



个人笔记：JUC → 并发包 → ConcurrentSkipListMap详解跳跃表

参考文章：https://www.cnblogs.com/hunternet/p/11248192.html



***



### Bitmaps

#### 操作

Bitmaps 本身不是一种数据类型， 实际上就是字符串（key-value） ， 但是它可以对字符串的位进行操作

数据结构的详解查看 Java → Algorithm → 位图

指令操作：

* 获取指定 key 对应偏移量上的 bit 值

  ```sh
  getbit key offset
  ```

* 设置指定 key 对应偏移量上的 bit 值，value 只能是 1 或 0

  ```sh
  setbit key offset value
  ```

* 对指定 key 按位进行交、并、非、异或操作，并将结果保存到 destKey 中

  ```sh
  bitop option destKey key1 [key2...]
  ```

  option：and 交、or 并、not 非、xor 异或

* 统计指定 key 中1的数量

  ```sh
  bitcount key [start end]
  ```



***



#### 应用

- 解决 Redis 缓存穿透，判断给定数据是否存在， 防止缓存穿透

  <img src="https://gitee.com/seazean/images/raw/master/DB/Redis-Bitmaps应用之缓存穿透.png" style="zoom: 67%;" />

- 垃圾邮件过滤，对每一个发送邮件的地址进行判断是否在布隆的黑名单中，如果在就判断为垃圾邮件

- 爬虫去重，爬给定网址的时候对已经爬取过的 URL 去重

- 信息状态统计



***



### Hyper

基数是数据集去重后元素个数，HyperLogLog 是用来做基数统计的，运用了 LogLog 的算法

```java
{1, 3, 5, 7, 5, 7, 8} 	基数集： {1, 3, 5 ,7, 8} 	基数：5
{1, 1, 1, 1, 1, 7, 1} 	基数集： {1,7} 				基数：2
```

相关指令：

* 添加数据

  ```sh
  pfadd key element [element ...]
  ```

* 统计数据

  ```sh
  pfcount key [key ...]
  ```

* 合并数据

  ```sh
  pfmerge destkey sourcekey [sourcekey...]
  ```

应用场景：

* 用于进行基数统计，不是集合不保存数据，只记录数量而不是具体数据，比如网站的访问量
* 核心是基数估算算法，最终数值存在一定误差
* 误差范围：基数估计的结果是一个带有 0.81% 标准错误的近似值
* 耗空间极小，每个 hyperloglog key 占用了12K的内存用于标记基数
* pfadd 命令不是一次性分配12K内存使用，会随着基数的增加内存逐渐增大
* Pfmerge 命令合并后占用的存储空间为12K，无论合并之前数据量多少



***



### GEO

GeoHash 是一种地址编码方法，把二维的空间经纬度数据编码成一个字符串

* 添加坐标点

  ```sh
  geoadd key longitude latitude member [longitude latitude member ...]
  georadius key longitude latitude radius m|km|ft|mi [withcoord] [withdist] [withhash] [count count]
  ```

* 获取坐标点

  ```sh
  geopos key member [member ...]
  georadiusbymember key member radius m|km|ft|mi [withcoord] [withdist] [withhash] [count count]
  ```

* 计算距离

  ```sh
  geodist key member1 member2 [unit]	#计算坐标点距离
  geohash key member [member ...]		#计算经纬度
  ```

redis 应用于地理位置计算





***



## Jedis

### 基本使用

Jedis 用于 Java 语言连接 redis 服务，并提供对应的操作 API

1. jar 包导入

   * 下载地址：https://mvnrepository.com/artifact/redis.clients/jedis

   * 基于 maven：

     ```xml
     <dependency>
     	<groupId>redis.clients</groupId>
     	<artifactId>jedis</artifactId>
     	<version>2.9.0</version>
     </dependency>
     ```

2. 客户端连接 redis
   API 文档：http://xetorthio.github.io/jedis/

   连接 redis：`Jedis jedis = new Jedis("192.168.0.185", 6379);`
   操作 redis：`jedis.set("name", "seazean");  jedis.get("name");`
   关闭 redis：`jedis.close();`

代码实现：

```java
public class JedisTest {
    public static void main(String[] args) {
        //1.获取连接对象
        Jedis jedis = new Jedis("192.168.2.185",6379);
        //2.执行操作
        jedis.set("age","39");
        String hello = jedis.get("hello");
        System.out.println(hello);
        jedis.lpush("list1","a","b","c","d");
        List<String> list1 = jedis.lrange("list1", 0, -1);
        for (String s:list1 ) {
            System.out.println(s);
        }
        jedis.sadd("set1","abc","abc","def","poi","cba");
        Long len = jedis.scard("set1");
        System.out.println(len);
        //3.关闭连接
        jedis.close();
    }
}
```



***



### 工具类

连接池对象：
	JedisPool：Jedis 提供的连接池技术  
	poolConfig：连接池配置对象 
	host：redis 服务地址
	port：redis 服务端口号

JedisPool 的构造器如下：

```java
public JedisPool(GenericObjectPoolConfig poolConfig, String host, int port) {
	this(poolConfig, host, port, 2000, (String)null, 0, (String)null);
}
```



* 创建配置文件 redis.properties

  ```properties
  redis.maxTotal=50
  redis.maxIdel=10
  redis.host=192.168.2.185
  redis.port=6379
  ```

* 工具类：

  ```java
  public class JedisUtils {
      private static int maxTotal;
      private static int maxIdel;
      private static String host;
      private static int port;
      private static JedisPoolConfig jpc;
      private static JedisPool jp;
  
      static {
          ResourceBundle bundle = ResourceBundle.getBundle("redis");
          //最大连接数
          maxTotal = Integer.parseInt(bundle.getString("redis.maxTotal"));
          //活动连接数
          maxIdel = Integer.parseInt(bundle.getString("redis.maxIdel"));
          host = bundle.getString("redis.host");
          port = Integer.parseInt(bundle.getString("redis.port"));
  
          //Jedis连接配置
          jpc = new JedisPoolConfig();
          jpc.setMaxTotal(maxTotal);
          jpc.setMaxIdle(maxIdel);
          //连接池对象
          jp = new JedisPool(jpc, host, port);
      }
  
      //对外访问接口，提供jedis连接对象，连接从连接池获取
      public static Jedis getJedis() {
          return jp.getResource();
      }
  }
  ```

  

****



### 可视化

Redis Desktop Manager

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-可视化工具.png" style="zoom:80%;" />





****



## 持久化

### 概述

持久化：利用永久性存储介质将数据进行保存，在特定的时间将保存的数据进行恢复的工作机制称为持久化 

作用：持久化用于防止数据的意外丢失，确保数据安全性，因为 Redis 是内存级，所以需要持久化到磁盘

计算机中的数据全部都是二进制，保存一组数据有两种方式
<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-持久化的两种方式.png" style="zoom: 33%;" />

第一种：将当前数据状态进行保存，快照形式，存储数据结果，存储格式简单

第二种：将数据的操作过程进行保存，日志形式，存储操作过程，存储格式复杂



***



### RDB

#### save

save指令：手动执行一次保存操作

配置redis.conf：

```sh
dir path				#设置存储.rdb文件的路径，通常设置成存储空间较大的目录中，目录名称data
dbfilename "x.rdb"		#设置本地数据库文件名，默认值为dump.rdb，通常设置为dump-端口号.rdb
rdbcompression yes|no	#设置存储至本地数据库时是否压缩数据，默认yes，设置为no节省CPU运行时间
rdbchecksum yes|no		#设置读写文件过程是否进行RDB格式校验，默认yes，设置为no，节约读写10%时间
						#消耗，但存在数据损坏的风险
```

工作原理：redis 是个单线程的工作模式，会创建一个任务队列，所有的命令都会进到这个队列排队执行。当某个指令在执行的时候，队列后面的指令都要等待，所以这种执行方式会非常耗时。

save 指令的执行会阻塞当前 Redis 服务器，直到当前 RDB 过程完成为止，有可能会造成长时间阻塞，线上环境不建议使用



***



#### bgsave

指令：bgsave（bg是background，后台执行的意思）

配置redis.conf

```sh
stop-writes-on-bgsave-error yes|no	#后台存储过程中如果出现错误，是否停止保存操作，默认yes
dbfilename filename  
dir path  
rdbcompression yes|no  
rdbchecksum yes|no
```

bgsave指令工作原理：

![](https://gitee.com/seazean/images/raw/master/DB/Redis-bgsave工作原理.png)

流程：当执行 bgsave 的时候，客户端发出 bgsave 指令给到 redis 服务器，服务器返回后台已经开始执行的信息给客户端，同时使用 fork 函数创建一个子进程，让子进程去执行 save 相关的操作。持久化过程是先将数据写入到一个临时文件中，持久化操作结束再用这个临时文件**替换**上次持久化的文件，在这个过程中主进程是不进行任何 IO 操作的，这确保了极高的性能 

bgsave 分成两个过程：第一个是服务端收到指令直接告诉客户端开始执行；另外一个过程是一个子进程在完成后台的保存操作，操作完以后返回消息。**两个进程不相互影响**，所以在持久化期间 Redis 可以正常工作

注意：bgsave 命令是针对 save 阻塞问题做的优化，Redis 内部所有涉及到 RDB 操作都采用 bgsave 的方式，save 命令可以放弃使用



***



#### 自动

配置文件自动 RDB，无需显式调用相关指令，save 配置启动后底层执行的是 bgsave 操作

配置 redis.conf：

```sh
save second changes #设置自动持久化条件，满足限定时间范围内key的变化数量就进行持久化(bgsave)
```

参数：

* second：监控时间范围
* changes：监控key的变化量

说明： save 配置中对于 second 与 changes 设置通常具有互补对应关系，尽量不要设置成包含性关系

示例：

```sh
save 300 10	#300s内10个key发生变化就进行持久化
```

判定 key 变化的原理：

* 对数据产生了影响
* 不进行数据比对，比如 name 键存在，重新 set name seazean 也算一次变化

save 配置要根据实际业务情况进行设置，频度过高或过低都会出现性能问题，结果可能是灾难性的

RDB三种启动方式对比：

| 方式           | save指令 | bgsave指令 |
| -------------- | -------- | ---------- |
| 读写           | 同步     | 异步       |
| 阻塞客户端指令 | 是       | 否         |
| 额外内存消耗   | 否       | 是         |
| 启动新进程     | 否       | 是         |



***



#### 总结

* RDB特殊启动形式的指令（客户端输入）

  * 服务器运行过程中重启

    ```sh
    debug reload
    ```

  * 关闭服务器时指定保存数据

    ```sh
    shutdown save
    ```

    默认情况下执行 shutdown 命令时，自动执行 bgsave（如果没有开启AOF持久化功能）

  * 全量复制：主从复制部分详解

* RDB优点：
  - RDB 是一个紧凑压缩的二进制文件，存储效率较高，但存储数据量较大时，存储效率较低
  - RDB 内部存储的是 redis 在某个时间点的数据快照，非常适合用于数据备份，全量复制等场景
  - RDB 恢复数据的速度要比 AOF 快很多，因为是快照，直接恢复
  - 应用：服务器中每 X 小时执行 bgsave 备份，并将 RDB 文件拷贝到远程机器中，用于灾难恢复

* RDB缺点：

  - bgsave 指令每次运行要执行 fork 操作创建子进程，会牺牲一些性能
  - RDB 方式无论是执行指令还是利用配置，无法做到实时持久化，具有较大的可能性丢失数据，**最后一次持久化后的数据可能丢失**
  - Redis 的众多版本中未进行 RDB 文件格式的版本统一，可能出现各版本之间数据格式无法兼容



***



### AOF

#### 概述

AOF（append only file）持久化：以独立日志的方式记录每次写命令（不记录读），**增量保存**，只许追加文件但不可以改写文件，重启时再重新执行 AOF 文件中命令达到恢复数据的目的，**与 RDB 相比可以简单理解为由记录数据改为记录数据的变化**

AOF 主要作用是解决了数据持久化的实时性，目前已经是 Redis 持久化的主流方式

AOF 写数据过程：
<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-AOF工作原理.png" style="zoom: 80%;" />



***



#### 策略

客户端的请求写命令会被 append 追加到 AOF 缓冲区内

启动 AOF 基本配置：

```sh
appendonly yes|no		#开启AOF持久化功能，默认no，即不开启状态
appendfilename filename	#AOF持久化文件名，默认appendonly.aof，建议设置appendonly-端口号.aof
dir						#AOF持久化文件保存路径，与RDB持久化文件路径保持一致即可
```

```sh
appendfsync always|everysec|no	#AOF写数据策略：默认为everysec
```

AOF 写数据三种策略（appendfsync）：

- always（每次）：每次写入操作均同步到 AOF 文件中，**数据零误差，性能较低**，不建议使用。


- everysec（每秒）：每秒将缓冲区中的指令同步到 AOF 文件中，在系统突然宕机的情况下丢失1秒内的数据 数据**准确性较高，性能较高**，建议使用，也是默认配置


- no（系统控制）：由操作系统控制每次同步到 AOF 文件的周期，整体过程**不可控**

**AOF 缓冲区同步文件策略**，系统调用 write 和 fsync：

* write 操作会触发延迟写（delayed write）机制，Linux 在内核提供页缓冲区用来提高硬盘 IO 性能，write 操作在写入系统缓冲区后直接返回
* 同步硬盘操作依赖于系统调度机制，比如缓冲区页空间写满或达到特定时间周期。同步文件之前，如果此时系统故障宕机，缓冲区内数据将丢失
* fsync 针对单个文件操作（比如 AOF 文件）做强制硬盘同步，fsync 将阻塞到写入硬盘完成后返回，保证了数据持久化

异常恢复：AOF 文件损坏，通过 **redis-check-aof--fix appendonly.aof** 进行恢复，重启 redis，然后重新加载



***



#### 重写

##### 介绍

随着命令不断写入 AOF，文件会越来越大，为了解决这个问题 Redis 引入了 AOF 重写机制压缩文件体积

AOF 重写：将 Redis 进程内的数据转化为写命令同步到**新** AOF 文件的过程，简单说就是将对同一个数据的若干个条命令执行结果转化成最终结果数据对应的指令进行记录

AOF 重写作用：

- 降低磁盘占用量，提高磁盘利用率
- 提高持久化效率，降低持久化写时间，提高 IO 性能
- 降低数据恢复的用时，提高数据恢复效率

AOF 重写规则：

- 进程内具有时效性的数据，并且数据已超时将不再写入文件 


- 非写入类的无效指令将被忽略，只保留最终数据的写入命令

  如 del key1、 hdel key2、srem key3、set key4 111、set key4 222等，select 指令虽然不更改数据，但是更改了数据的存储位置，此类命令同样需要记录

- 对同一数据的多条写命令合并为一条命令

  如 lpushlist1 a、lpush list1 b、lpush list1 c 可以转化为：lpush list1 a b c。

  为防止数据量过大造成客户端缓冲区溢出，对 list、set、hash、zset 等类型，每条指令最多写入64个元素





***



##### 方式

* 手动重写

  ```sh
  bgrewriteaof
  ```

  原理分析：

  ![](https://gitee.com/seazean/images/raw/master/DB/Redis-AOF手动重写原理.png)

* 自动重写

  触发时机：Redis 会记录上次重写时的 AOF 大小，默认配置是当 AOF 文件大小是上次重写后大小的一倍且文件大于 64M 时触发
  
  ```sh
  auto-aof-rewrite-min-size size		#设置重写的基准值，最小文件 64MB，达到这个值开始重写
  auto-aof-rewrite-percentage percent	#触发AOF文件执行重写的增长率，当前AOF文件大小超过上一次重写的AOF文件大小的百分之多少才会重写，比如文件达到 100% 时开始重写就是两倍时触发
  ```

  自动重写触发比对参数（ 运行指令 `info Persistence` 获取具体信息 ）：
  
  ```sh
  aof_current_size	#AOF文件当前尺寸大小（单位:字节）
  aof_base_size		#AOF文件上次启动和重写时的尺寸大小（单位:字节）
  ```

  自动重写触发条件公式：
  
  - aof_current_size > auto-aof-rewrite-min-size
  - (aof_current_size - aof_base_size) / aof_base_size >= auto-aof-rewrite-percentage



***





#### 流程

持久化流程：

![](https://gitee.com/seazean/images/raw/master/DB/Redis-AOF重写流程1.png)

重写流程：

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-AOF重写流程2.png" style="zoom:80%;" />

使用**新的 AOF 文件覆盖旧的 AOF 文件**，完成 AOF 重写



****



### 对比

AOF 和 RDB 同时开启，系统默认取 AOF 的数据（数据不会存在丢失）

 RDB 与 AOF 对比：

| 持久化方式   | RDB                | AOF                |
| ------------ | ------------------ | ------------------ |
| 占用存储空间 | 小（数据级：压缩） | 大（指令级：重写） |
| **存储速度** | 慢                 | 快                 |
| **恢复速度** | 快                 | 慢                 |
| 数据安全性   | 会丢失数据         | 依据策略决定       |
| 资源消耗     | 高/重量级          | 低/轻量级          |
| 启动优先级   | 低                 | 高                 |

应用场景：

- 对数据**非常敏感**，建议使用默认的 AOF 持久化方案

  AOF 持久化策略使用 everysecond，每秒钟 fsync 一次，该策略 redis 仍可以保持很好的处理性能，当出现问题时，最多丢失0-1秒内的数据。

  注意：AOF文件存储体积较大，恢复速度较慢，因为要执行每条指令

- 数据呈现**阶段有效性**，建议使用 RDB 持久化方案

  数据可以良好的做到阶段内无丢失，且恢复速度较快，阶段内数据恢复通常采用 RDB 方案

  注意：利用 RDB 实现紧凑的数据持久化，存储数据量较大时，存储效率较低

综合对比：

- RDB 与 AOF 的选择实际上是在做一种权衡，每种都有利有弊
- 如不能承受数分钟以内的数据丢失，对业务数据非常敏感，选用 AOF
- 如能承受数分钟以内的数据丢失，且追求大数据集的恢复速度，选用 RDB
- 灾难恢复选用 RDB
- 双保险策略，同时开启 RDB和 AOF，重启后 Redis 优先使用 AOF 来恢复数据，降低丢失数据的量
- 不建议单独用 AOF，因为可能会出现 Bug，如果只是做纯内存缓存，可以都不用



***



### fork

#### 介绍

fork() 函数创建一个子进程，子进程与父进程几乎是完全相同的进程，系统先给子进程分配资源，然后把原来的进程的所有数据都复制到子进程中，只有少数值与父进程的值不同，相当于克隆了一个进程

在完成对其调用之后，会产生2个进程，且每个进程都会**从 fork() 的返回处开始执行**，这两个进程将执行相同的程序段，但是拥有各自不同的堆段，栈段，数据段，每个子进程都可修改各自的数据段，堆段，和栈段

```c
#include<unistd.h>
pid_t fork(void);
// 父进程返回子进程的pid，子进程返回0，错误返回负值
```

fork 调用一次，却能够**返回两次**，可能有三种不同的返回值：

* 在父进程中，fork返回新创建子进程的进程ID
* 在子进程中，fork返回0
* 如果出现错误，fork返回一个负值，错误原因：
  * 当前的进程数已经达到了系统规定的上限，这时errno的值被设置为EAGAIN
  * 系统内存不足，这时errno的值被设置为ENOMEM

fpid 的值在父子进程中不同：进程形成了链表，父进程的 fpid 指向子进程的进程 id，因为子进程没有子进程，所以其 fpid 为0

创建新进程成功后，系统中出现两个基本完全相同的进程，这两个进程执行没有固定的先后顺序，哪个进程先执行要看系统的进程调度策略

每个进程都有一个独特（互不相同）的进程标识符 process ID，可以通过 getpid() 函数获得；还有一个记录父进程 pid 的变量，可以通过 getppid() 函数获得变量的值



***



#### 使用

基本使用：

```c
#include <unistd.h>  
#include <stdio.h>   
int main ()   
{   
    pid_t fpid; // fpid表示fork函数返回的值  
    int count=0;  
    fpid=fork();   
    if (fpid < 0)   
        printf("error in fork!");   
    else if (fpid == 0) {  
        printf("i am the child process, my process id is %d/n", getpid());    
        count++;  
    }  
    else {  
        printf("i am the parent process, my process id is %d/n", getpid());   
        count++;  
    }  
    printf("count: %d/n",count);// 1  
    return 0;  
}  
/*输出内容：
    i am the child process, my process id is 5574
    count: 1
    i am the parent process, my process id is 5573
    count: 1
*/
```

进阶使用：

```c
#include <unistd.h>  
#include <stdio.h>  
int main(void)  
{  
   int i=0;  
   // ppid 指当前进程的父进程pid  
   // pid 指当前进程的pid,  
   // fpid 指fork返回给当前进程的值，在这可以表示子进程
   for(i=0; i<2; i++){  
       pid_t fpid = fork();  
       if(fpid == 0)  
           printf("%d child  %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
       else  
           printf("%d parent %4d %4d %4d/n",i,getppid(),getpid(),fpid);  
   }  
   return 0;  
} 
/*输出内容：
	i        父id  id  子id
	0 parent 2043 3224 3225
    0 child  3224 3225    0
    1 parent 2043 3224 3226
    1 parent 3224 3225 3227
    1 child     1 3227    0
    1 child     1 3226    0 
*/
```

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-fork函数使用演示.png" style="zoom: 80%;" />

在 p3224 和 p3225 执行完第二个循环后，main函数退出，进程死亡。所以 p3226，p3227 就没有父进程了，成为孤儿进程，所以 p3226 和 p3227 的父进程就被置为 ID 为 1的 init 进程（笔记 Tool -> Linux -> 进程管理详解）

参考文章：https://blog.csdn.net/love_gaohz/article/details/41727415



***



#### 内存

fork() 调用之后父子进程的内存关系

早期 Linux 的 fork() 实现时，就是全部复制，这种方法效率太低，而且造成了很大的内存浪费，现在 Linux 实现采用了两种方法来规避这种浪费：

* 父子进程的代码段是相同的，所以代码段是没必要复制的，只需内核将代码段标记为只读，父子进程就共享此代码段。fork() 之后在进程创建代码段时，子进程的进程级页表项都指向和父进程相同的物理页帧

  <img src="https://gitee.com/seazean/images/raw/master/DB/Redis-fork以后内存关系1.png" style="zoom: 67%;" />

* 对于父进程的数据段，堆段，栈段中的各页，由于父子进程要相互独立，采用**写时复制**的技术，来最大化的提高内存以及内核的利用率

  在 fork 之后两个进程用的是相同的物理空间（内存区），子进程的代码段、数据段、堆栈都是指向父进程的物理空间，**两者的虚拟空间不同，但其对应的物理空间是同一个**。当父子进程中有更改相应段的行为发生时，再为子进程相应的段分配物理空间，如果两者的代码完全相同，代码段继续共享父进程的物理空间；而如果两者执行的代码不同，子进程的代码段也会分配单独的物理空间。   

  fork之后内核会将子进程放在队列的前面，让子进程先执行，以免父进程执行导致写时复制，而后子进程再执行，因无意义的复制而造成效率的下降

  <img src="https://gitee.com/seazean/images/raw/master/DB/Redis-fork以后内存关系2.png" style="zoom:67%;" />

补充知识：

vfork（虚拟内存 fork virtual memory fork）：调用 vfork() 父进程被挂起，子进程使用父进程的地址空间。不采用写时复制，如果子进程修改父地址空间的任何页面，这些修改过的页面对于恢复的父进程是可见的



参考文章：https://blog.csdn.net/Shreck66/article/details/47039937





****



## 事务机制

### 基本操作

Redis 事务的主要作用就是串联多个命令防止别的命令插队

* 开启事务

  ```sh
  multi	#设定事务的开启位置，此指令执行后，后续的所有指令均加入到事务中
  ```

* 执行事务

  ```sh
  exec	#设定事务的结束位置，同时执行事务，与multi成对出现，成对使用
  ```

  加入事务的命令暂时进入到任务队列中，并没有立即执行，只有执行 exec 命令才开始执行

* 取消事务

  ```sh
  discard	#终止当前事务的定义，发生在multi之后，exec之前
  ```

  一般用于事务执行过程中输入了错误的指令，直接取消这次事务，类似于回滚

Redis 事务的三大特性：

* Redis 事务是一个单独的隔离操作，将一系列预定义命令包装成一个整体（一个队列），当执行时按照添加顺序依次执行，中间不会被打断或者干扰
* Redis 事务**没有隔离级别**的概念，队列中的命令在事务没有提交之前都不会实际被执行
* Redis 单条命令式保存原子性的，但是事务不保证原子性，事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚



***



### 工作流程

事务机制整体工作流程：

![](https://gitee.com/seazean/images/raw/master/DB/Redis-事务的工作流程.png)

几种常见错误：

* 定义事务的过程中，命令格式输入错误，出现语法错误造成，**整体事务中所有命令均不会执行**，包括那些语法正确的命令

  <img src="https://gitee.com/seazean/images/raw/master/DB/Redis-事务中出现语法错误.png" style="zoom:80%;" />

* 定义事务的过程中，命令执行出现错误，例如对字符串进行 incr 操作，能够正确运行的命令会执行，运行错误的命令不会被执行

  <img src="https://gitee.com/seazean/images/raw/master/DB/Redis-事务中出现执行错误.png" style="zoom:80%;" />

* 已经执行完毕的命令对应的数据不会自动回滚，需要程序员在代码中实现回滚，应该尽可能避免：

  事务操作之前记录数据的状态

  * 单数据：string
  * 多数据：hash、list、set、zset

  设置指令恢复所有的被修改的项

  * 单数据：直接 set（注意周边属性，例如时效）
  * 多数据：修改对应值或整体克隆复制



***



### 监控锁

对 key 添加监视锁，是一种乐观锁，在执行 exec 前如果其他客户端的操作导致 key 发生了变化，执行结果为 nil

* 添加监控锁

  ```sh
  watch key1 [key2……]	#可以监控一个或者多个key
  ```

* 取消对所有 key 的监视

  ```sh
  unwatch
  ```

应用：基于状态控制的批量任务执行，防止其他线程对变量的修改



***



### 分布式锁

#### 基本操作

由于分布式系统多线程并发分布在不同机器上，这将使单机部署情况下的并发控制锁策略失效，需要分布式锁

Redis 分布式锁的基本使用，悲观锁

* 使用 setnx 设置一个公共锁

  ```sh
  setnx lock-key value	# value任意数，返回为1设置成功，返回为0设置失败
  ```

  * 对于返回设置成功的，拥有控制权，进行下一步的具体业务操作
  * 对于返回设置失败的，不具有控制权，排队或等待

  NX：只在键不存在时，才对键进行设置操作，`SET key value NX` 效果等同于 `SETNX key value`

  XX ：只在键已经存在时，才对键进行设置操作

* 操作完毕通过 del 操作释放锁

  ```sh
  del lock-key 
  ```

* 使用 expire 为锁 key 添加存活（持有）时间，过期自动删除（放弃）锁

  ```sh
  expire lock-key second 
  pexpire lock-key milliseconds
  ```
  
  通过 expire 设置过期时间缺乏原子性，如果在 setnx 和 expire 之间出现异常，锁也无法释放
  
* 在 set 时指定过期时间

  ```sh
  SET key value [EX seconds | PX milliseconds] NX

应用：解决抢购时出现超卖现象



****



#### 防误删

setnx 获取锁时，设置一个指定的唯一值（uuid），释放前获取这个值，判断是否自己的锁，防止出现线程之间误删了其他线程的锁

```java
// 加锁, unique_value作为客户端唯一性的标识
SET lock_key unique_value NX PX 10000
```

unique_value 是客户端的唯一标识，可以用一个随机生成的字符串来表示，PX 10000 则表示 lock_key 会在 10s 后过期，以免客户端在这期间发生异常而无法释放锁



***



## 删除策略

### 过期数据

Redis 是一种内存级数据库，所有数据均存放在内存中，内存中的数据可以通过 TTL 指令获取其状态

TTL 返回的值有三种情况：正数，-1，-2

- 正数：代表该数据在内存中还能存活的时间
- -1：永久有效的数据
- 2 ：已经过期的数据或被删除的数据或未定义的数据

删除策略：**删除策略就是针对已过期数据的处理策略**，已过期的数据不一定被立即删除，在不同的场景下使用不同的删除方式会有不同效果，这就是删除策略的问题

过期数据是一块独立的存储空间，Hash结构，field是内存地址，value是过期时间，保存了所有key的过期描述，在最终进行过期处理的时候，对该空间的数据进行检测， 当时间到期之后通过field找到内存该地址处的数据，然后进行相关操作

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-时效性数据的存储结构.png" style="zoom:67%;" />



****



### 数据删除

#### 删除策略

在内存占用与 CPU 占用之间寻找一种平衡，顾此失彼都会造成整体 redis 性能的下降，甚至引发服务器宕机或内存泄露

针对过期数据有三种删除策略：

- 定时删除
- 惰性删除
- 定期删除



***



#### 定时删除

创建一个定时器，当 key 设置有过期时间，且过期时间到达时，由定时器任务立即执行对键的删除操作

- 优点：节约内存，到时就删除，快速释放掉不必要的内存占用
- 缺点：无论 CPU 此时负载量多高，均占用 CPU，会影响 redis 服务器响应时间和指令吞吐量
- 总结：用处理器性能换取存储空间（拿时间换空间）



***



#### 惰性删除

数据到达过期时间，不做处理，等下次访问该数据时，我们需要判断：

* 如果未过期，返回数据
* 如果已过期，删除，返回不存在

在任何 get 操作之前都要执行 **expireIfNeeded()**，相当于绑定在一起

特点：

* 优点：节约CPU性能，发现必须删除的时候才删除
* 缺点：内存压力很大，出现长期占用内存的数据
* 总结：用存储空间换取处理器性能（拿空间换时间）



***



#### 定期删除 

定时删除和惰性删除这两种方案都是走的极端，定期删除就是折中方案

定期删除是周期性轮询 redis 库中的时效性数据，采用随机抽取的策略，利用过期数据占比的方式控制删除频度

定期删除方案：

- Redis启动服务器初始化时，读取配置 server.hz 的值，默认为10。执行指令可以查看：info server

- 每秒钟执行 server.hz 次 serverCron() → databasesCron() → activeExpireCycle() 

- databasesCron() 操作是轮询每个数据库

- activeExpireCycle() 对某个数据库中的每个 expires 进行检测，每次执行耗时：250ms/server.hz

  对某个 expires[*] 检测时，随机挑选 W 个 key 检测

  - 如果 key 超时，删除 key
  - 如果一轮中删除的 key 的数量 > W*25%，循环该过程
  - 如果一轮中删除的 key 的数量 ≤ W*25%，检查下一个expires[]，0-15循环
  - W取值 = ACTIVE_EXPIRE_CYCLE_LOOKUPS_PER_LOOP 属性值，自定义值

* 参数 current_db 用于记录 activeExpireCycle() 进入哪个expires[*] 执行
* 如果 activeExpireCycle() 执行时间到期，下次从 current_db 继续向下执行

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-定期删除.png" style="zoom: 67%;" />

定期删除特点：

- CPU 性能占用设置有峰值，检测频度可自定义设置
- 内存压力不是很大，长期占用内存的冷数据会被持续清理
- 周期性抽查存储空间（随机抽查，重点抽查）



***



#### 策略对比

|          | 优点             | 缺点                          | 特点               |
| -------- | ---------------- | ----------------------------- | ------------------ |
| 定时删除 | 节约内存，无占用 | 不分时段占用CPU资源，频度高   | 拿时间换空间       |
| 惰性删除 | 内存占用严重     | 延时执行，CPU利用率高         | 拿空间换时间       |
| 定期删除 | 内存定期随机清理 | 每秒花费固定的CPU资源维护内存 | 随机抽查，重点抽查 |



***



### 数据淘汰

#### 逐出算法

数据淘汰策略：当新数据进入redis时，在执行每一个命令前，会调用 **freeMemoryIfNeeded()** 检测内存是否充足。如果内存不满足新加入数据的最低存储要求，redis要临时删除一些数据为当前指令清理存储空间，清理数据的策略称为**逐出算法**

注意：逐出数据的过程不是 100% 能够清理出足够的可使用的内存空间，如果不成功则反复执行，当对所有数据尝试完毕，如不能达到内存清理的要求，将出现错误信息如下：

```shell
(error) OOM command not allowed when used memory >'maxmemory'
```



****



#### 策略配置

影响数据淘汰的相关配置如下，配置 conf 文件：

* 最大可使用内存，即占用物理内存的比例，默认值为0，表示不限制。生产环境中根据需求设定，通常设置在50%以上

  ```sh
  maxmemory ?mb
  ```

* 每次选取待删除数据的个数，采用随机获取数据的方式作为待检测删除数据，防止全库扫描，导致严重的性能消耗，降低读写性能

  ```sh
  maxmemory-samples count
  ```

* 达到最大内存后的，对被挑选出来的数据进行删除的策略

  ```sh
  maxmemory-policy policy
  ```

  数据删除的策略policy：3类8种

  第一类：检测易失数据（可能会过期的数据集server.db[i].expires ）：

  ```sh
  volatile-lru	#挑选最近最久未使用使用的数据淘汰
  volatile-lfu	#挑选最近使用次数最少的数据淘汰
  volatile-ttl	#挑选将要过期的数据淘汰
  volatile-random	#任意选择数据淘汰
  ```

  第二类：检测全库数据（所有数据集server.db[i].dict ）：

  ```sh
  allkeys-lru		#挑选最近最少使用的数据淘汰
  allkeLyRs-lfu	#挑选最近使用次数最少的数据淘汰
  allkeys-random	#任意选择数据淘汰，相当于随机
  ```

  第三类：放弃数据驱逐

  ```sh
  no-enviction	#禁止驱逐数据(redis4.0中默认策略)，会引发OOM(Out Of Memory)
  ```

数据淘汰策略配置依据：使用INFO命令输出监控信息，查询缓存 hit 和 miss 的次数，根据需求调优 Redis 配置





***



## 主从复制

### 基本介绍

**三高**架构：

- 高并发：应用提供某一业务要能支持很多客户端同时访问的能力，我们称为并发

- 高性能：性能带给我们最直观的感受就是：速度快，时间短

- 高可用：
  - 可用性：应用服务在全年宕机的时间加在一起就是全年应用服务不可用的时间
  - 业界可用性目标5个9，即99.999%，即服务器年宕机时长低于315秒，约5.25分钟

主从复制：

* 概念：将 master 中的数据即时、有效的复制到 slave 中
* 特征：一个 master 可以拥有多个 slave，一个slave 只对应一个 master
* 职责：master 和 slave 各自的职责不一样

  master：
  * **写数据**，执行写操作时，将出现变化的数据自动同步到 slave
  * 读数据（可忽略）

  slave
  * **读数据**
  * 写数据（禁止）

主从复制的机制：

* **薪火相传**：一个 slave 可以是下一个 slave 的 master，slave 同样可以接收其他 slave 的连接和同步请求，那么该 slave 作为了链条中下一个的 master, 可以有效减轻 master 的写压力，去中心化降低风险

  注意：主机挂了，从机还是从机，无法写数据了

* **反客为主**：当一个 master 宕机后，后面的 slave 可以立刻升为 master，其后面的 slave 不做任何修改

  将从机变为主机的命令：`slaveof no one`

主从复制的作用：

- **读写分离**：master 写、slave 读，提高服务器的读写负载能力
- **负载均衡**：基于主从结构，配合读写分离，由 slave 分担 master 负载，并根据需求的变化，改变 slave 的数量，通过多个从节点分担数据读取负载，大大提高 Redis 服务器并发量与数据吞吐量
- **故障恢复**：当 master 出现问题时，由 slave 提供服务，实现快速的故障恢复
- **数据冗余**：实现数据热备份，是持久化之外的一种数据冗余方式
- 高可用基石：基于主从复制，构建哨兵模式与集群，实现 Redis 的高可用方案

主从复制的应用场景：

* 机器故障：硬盘故障、系统崩溃，造成数据丢失，对业务形成灾难性打击，基本上会放弃使用redis

* 容量瓶颈：内存不足，放弃使用 redis

* 解决方案：为了避免单点 Redis 服务器故障，准备多台服务器，互相连通。将数据复制多个副本保存在不同的服务器上连接在一起，并保证数据是同步的。即使有其中一台服务器宕机，其他服务器依然可以继续提供服务，实现Redis的高可用，同时实现数据冗余备份

  <img src="https://gitee.com/seazean/images/raw/master/DB/Redis-主从复制多台服务器连接方案.png" style="zoom: 80%;" />



***



### 工作流程

主从复制过程大体可以分为3个阶段

* 建立连接阶段（即准备阶段）
* 数据同步阶段
* 命令传播阶段

![](https://gitee.com/seazean/images/raw/master/DB/Redis-主从复制工作流程.png)



***



### 建立连接

#### 建立流程

建立连接阶段：建立 slave 到 master 的连接，使 master 能够识别 slave，并保存 slave 端口号

流程如下：

1. 设置 master 的地址和端口，保存 master 信息
2. 建立 socket 连接
3. 发送 ping 命令（定时器任务）
4. 身份验证（可能没有）
5. 发送 slave 端口信息
6. 主从连接成功

连接成功的状态：

* slave：保存 master 的地址与端口

* master：保存 slave 的端口

* 主从之间创建了连接的 socket

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-主从复制建立连接.png" style="zoom: 80%;" />



***



#### 相关指令

* master 和 slave 互联

  方式一：客户端发送命令

  ```sh
  slaveof masterip masterport
  ```

  方式二：服务器带参启动

  ```sh
  redis-server --slaveof masterip masterport
  ```

  方式三：服务器配置（主流方式）

  ```sh
  slaveof masterip masterport
  ```

  * slave 系统信息：info 指令

    ```sh
    master_link_down_since_seconds
    masterhost & masterport
    ```

  * master 系统信息：

    ```sh
    uslave_listening_port(多个)
    ```
    
  * 系统信息：

    ```sh
    info replication
    ```

* 主从断开连接

  断开 slave 与 master 的连接，slave 断开连接后，不会删除已有数据，只是不再接受 master 发送的数据

  slave客户端执行命令：

  ```sh
  slaveof no one	
  ```

* 授权访问

  master 有服务端和客户端，slave 也有服务端和客户端，不仅服务端之间可以发命令，客户端也可以

  master 客户端发送命令设置密码：

  ```sh
  requirepass password
  ```

  master 配置文件设置密码：

  ```sh
  config set requirepass password
  config get requirepass
  ```

  slave 客户端发送命令设置密码：

  ```sh
  auth password
  ```

  slave 配置文件设置密码：

  ```sh
  masterauth password
  ```

  slave 启动服务器设置密码：

  ```sh
  redis-server –a password
  ```

  

***



### 数据同步

#### 同步流程

数据同步需求：

- 在 slave 初次连接 master 后，复制 master 中的所有数据到 slave
- 将 slave 的数据库状态更新成 master 当前的数据库状态

同步过程如下：

1. 请求同步数据
2. 创建 RDB 同步数据
3. 恢复 RDB 同步数据（清空原有数据）
4. 请求部分同步数据
5. 恢复部分同步数据
6. 数据同步工作完成

同步完成的状态：

* slave：具有 master 端全部数据，包含 RDB 过程接收的数据

* master：保存 slave 当前数据同步的位置

* 主从之间完成了数据克隆

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-主从复制数据同步.png" style="zoom:80%;" />



***



#### 同步优化

* 数据同步阶段 master 说明

  1. master 数据量巨大，数据同步阶段应避开流量高峰期，避免造成 master 阻塞，影响业务正常执行

  2. 复制缓冲区大小设定不合理，会导致**数据溢出**。比如进行全量复制周期太长，进行部分复制时发现数据已经存在丢失的情况，必须进行第二次全量复制，致使 slave 陷入死循环状态

     ```sh
     repl-backlog-size ?mb
     ```

     建议设置如下：

     * 测算从 master 到 slave 的重连平均时长 second
     * 获取 master 平均每秒产生写命令数据总量 write_size_per_second
     * 最优复制缓冲区空间 = 2 * second * write_size_per_second

  3. master 单机内存占用主机内存的比例不应过大，建议使用 50%-70% 的内存，留下 30%-50% 的内存用于执行 bgsave 命令和创建复制缓冲区

* 数据同步阶段 slave 说明

  1. 为避免 slave 进行全量复制、部分复制时服务器响应阻塞或数据不同步，建议关闭此期间的对外服务

     ```sh
     slave-serve-stale-data yes|no
     ```

  2. 数据同步阶段，master 发给 slave 信息可以理解 master是 slave 的一个客户端，主动向 slave 发送命令

  3. 多个 slave 同时对 master 请求数据同步，master 发送的 RDB 文件增多，会对带宽造成巨大冲击，如果master 带宽不足，因此数据同步需要根据业务需求，适量错峰

  4. slave 过多时，建议调整拓扑结构，由一主多从结构变为树状结构，中间的节点既是 master，也是slave。注意使用树状结构时，由于层级深度，导致深度越高的 slave 与最顶层 master 间数据同步延迟较大，数据一致性变差，应谨慎选择



***



### 命令传播

#### 传播原理

命令传播：当 master 数据库状态被修改后，导致主从服务器数据库状态不一致，此时需要让主从数据同步到一致的状态，同步的动作称为命令传播

命令传播的过程：master 将接收到的数据变更命令发送给 slave，slave 接收命令后执行命令

命令传播阶段出现了断网现象：

* 网络闪断闪连：忽略
* 短时间网络中断：部分复制
* 长时间网络中断：全量复制

部分复制的三个核心要素：服务器的运行 id（run id）、主服务器的复制积压缓冲区、主从服务器的复制偏移量

* 服务器运行ID（runid）：服务器运行 ID 是每一台服务器每次运行的身份识别码，一台服务器多次运行可以生成多个运行 ID，由 40 位字符组成，是一个随机的十六进制字符

  作用：用于在服务器间进行传输识别身份，如果想两次操作均对同一台服务器进行，必须每次操作携带对应的运行 ID，用于对方识别

  实现：运行 ID 在每台服务器启动时自动生成，master 在首次连接 slave 时，将运行 ID 发送给 slave，slave保存此ID，通过 info Server 命令，可以查看节点的 runid

* 复制缓冲区：复制积压缓冲区，是一个先进先出（FIFO）的队列，用于存储服务器执行过的命令

  作用：用于保存 master 收到的所有指令（仅影响数据变更的指令，例如 set，select）

  实现方式：每次传播命令，master 都会将传播的命令记录下来，并存储在复制缓冲区，复制缓冲区默认数据存储空间大小是 1M，当入队元素的数量大于队列长度时，最先入队的元素被弹出，新元素会被放入队列

* 复制偏移量：一个数字，描述复制缓冲区中的指令字节位置

  - master 复制偏移量：记录发送给所有 slave 的指令字节对应的位置（多个）
  - slave 复制偏移量：记录 slave 接收 master 发送过来的指令字节对应的位置（一个）
  
  作用：同步信息，比对 master 与 slave 的差异，当 slave 断线后，恢复数据使用
  
  数据来源：
  
  - master 端：发送一次记录一次
  - slave 端：接收一次记录一次

**工作原理**：

- 通过 offset 区分不同的 slave 当前数据传播的差异
- master 记录已发送的信息对应的 offset
- slave 记录已接收的信息对应的 offset

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-主从复制复制缓冲区原理.png" style="zoom:67%;" />



****



#### 复制流程

全量复制/部分复制

![](https://gitee.com/seazean/images/raw/master/DB/Redis-主从复制流程更新.png)





***



#### 心跳机制

心跳机制：进入命令传播阶段，master 与 slave 间需要信息交换，使用心跳机制维护，实现双方连接保持在线

master 心跳任务：

- 内部指令：PING
- 周期：由 `repl-ping-slave-period` 决定，默认10秒
- 作用：判断 slave 是否在线
- 查询：INFO replication  获取 slave 最后一次连接时间间隔，lag 项维持在0或1视为正常

slave 心跳任务

- 内部指令：REPLCONF ACK {offset}
- 周期：1秒
- 作用：汇报 slave 自己的复制偏移量，获取最新的数据变更指令；判断master是否在线

心跳阶段注意事项：

* 当 slave 多数掉线，或延迟过高时，master 为保障数据稳定性，将拒绝所有信息同步

  slave 数量少于2个，或者所有 slave 的延迟都大于等于8秒时，强制关闭 master 写功能，停止数据同步

  ```sh
  min-slaves-to-write 2
  min-slaves-max-lag 8
  ```

* slave 数量由 slave 发送 REPLCONF ACK 命令做确认


- slave 延迟由 slave 发送 REPLCONF ACK 命令做确认



****



### 常见问题

#### 重启恢复

系统不断运行，master 的数据量会越来越大，一旦 master 重启，runid 将发生变化，会导致全部 slave 的全量复制操作

解决方法：本机保存上次 runid，重启后恢复该值，使所有 slave 认为还是之前的 master

优化方案：

* master 内部创建 master_replid 变量，使用 runid 相同的策略生成，长度41位，并发送给所有 slave

* 在master关闭时执行命令 `shutdown save`，进行RDB持久化，将 runid 与 offset 保存到RDB文件中

  `redis-check-rdb dump.rdb` 命令可以查看该信息，保存为 repl-id 和 repl-offset

* master 重启后加载 RDB 文件，恢复数据

  重启后，将RDB文件中保存的 repl-id 与 repl-offset 加载到内存中

  * master_repl_id = repl-id，master_repl_offset = repl-offset
  * 通过 info 命令可以查看该信息

 

***



#### 网络中断

master 的 CPU 占用过高或 slave 频繁断开连接

* 出现的原因：
  * slave 每1秒发送 REPLCONF ACK 命令到 master
  * 当 slave 接到了慢查询时（keys * ，hgetall等），会大量占用 CPU 性能
  * master 每1秒调用复制定时函数 replicationCron()，比对 slave 发现长时间没有进行响应

  最终导致 master 各种资源（输出缓冲区、带宽、连接等）被严重占用

* 解决方法：通过设置合理的超时时间，确认是否释放 slave

  ```sh
  repl-timeout	# 该参数定义了超时时间的阈值（默认60秒），超过该值，释放slave
  ```

slave 与 master 连接断开

* 出现的原因：
  * master 发送 ping 指令频度较低
  * master 设定超时时间较短
  * ping 指令在网络中存在丢包

* 解决方法：提高 ping 指令发送的频度

  ```sh
  repl-ping-slave-period	
  ```

  超时时间 repl-time 的时间至少是 ping 指令频度的5到10倍，否则 slave 很容易判定超时



****



#### 一致性

网络信息不同步，数据发送有延迟，导致多个 slave 获取相同数据不同步

解决方案：

* 优化主从间的网络环境，通常放置在同一个机房部署，如使用阿里云等云服务器时要注意此现象

* 监控主从节点延迟（通过offset）判断，如果 slave 延迟过大，暂时屏蔽程序对该 slave 的数据访问

  ```sh
  slave-serve-stale-data yes|no
  ```

  开启后仅响应 info、slaveof 等少数命令（慎用，除非对数据一致性要求很高）





***



## 哨兵模式

### 哨兵概述

如果 redis 的 master 宕机了，需要从 slave 中重新选出一个 master，要实现这些功能就需要 redis 的哨兵

哨兵 (sentinel) 是一个分布式系统，用于对主从结构中的每台服务器进行**监控**，当出现故障时通过**投票机制选择**新的 master 并将所有 slave 连接到新的 master

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-哨兵模式.png" style="zoom:67%;" />

哨兵的作用：

- 监控：监控master和slave，不断的检查master和slave是否正常运行，master存活检测、master与slave运行情况检测

- 通知 (提醒)：当被监控的服务器出现问题时，向其他（哨兵间，客户端）发送通知


- 自动故障转移：断开 master 与 slave 连接，选取一个 slave 作为 master，将其他 slave 连接新的 master，并告知客户端新的服务器地址

注意：哨兵也是一台redis服务器，只是不提供数据相关服务，通常哨兵的数量配置为单数（投票）



***



### 启用哨兵

配置哨兵：

* 配置一拖二的主从结构

* 配置三个哨兵（配置相同，端口不同），sentinel.conf

  ```sh
  port 26401
  dir "/redis/data"
  sentinel monitor mymaster 127.0.0.1 6401 2
  sentinel down-after-milliseconds mymaster 5000
  sentinel failover-timeout mymaster 20000
  sentinel parallel-sync mymaster 1
  sentinel deny-scripts-reconfig yes
  ```

  配置说明：

  * 设置哨兵监听的主服务器信息， sentinel_number 表示参与投票的哨兵数量

    ```sh
    sentinel monitor master_name master_host master_port sentinel_number
    ```

  * 指定哨兵在监控Redis服务时，设置判定服务器宕机的时长，该设置控制是否进行主从切换

    ```sh
    sentinel down-after-milliseconds master_name million_seconds
    ```

  * 出现故障后，故障切换的最大超时时间，超过该值，认定切换失败，默认3分钟

    ```sh
    sentinel failover-timeout master_name	million_seconds
    ```

  * 指定同时进行主从的slave数量，数值越大，要求网络资源越高，要求约小，同步时间约长

    ```sh
    sentinel parallel-syncs master_name sync_slave_number
    ```

启动哨兵：

* 服务端命令（Linux命令）：

  ```sh
  redis-sentinel filename
  ```



***



### 工作原理

#### 三个阶段

哨兵在进行主从切换过程中经历三个阶段

- 监控
- 通知
- 故障转移



#### 监控阶段

作用：同步各个节点的状态信息

* 获取各个sentinel的状态（是否在线）


- 获取master的状态

  ```markdown
  master属性
  	prunid
  	prole：master
  	各个slave的详细信息	
  ```

- 获取所有slave的状态（根据master中的slave信息）

  ```markdown
  slave属性
  	prunid
  	prole：slave
  	pmaster_host、master_port
  	poffset
  ```

内部的工作原理：

sentinel 1首先连接 master，建立 cmd 通道，根据主节点访问从节点，连接完成

sentinel 2 首先连接 master，然后通过 master 中的 sentinels 发现其他哨兵，然后寻找哨兵建立连接，同步数据

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-哨兵模式监控工作原理.png" style="zoom:67%;" />



***



#### 通知阶段

sentinel 在通知阶段不断的去获取 master/slave 的信息，然后在各个 sentinel 之间进行共享，流程如下：

![](https://gitee.com/seazean/images/raw/master/DB/Redis-哨兵模式通知工作流程.png)



***



#### 故障转移

当 master 宕机后，sentinel 会判断出 master 是否真的宕机，具体的操作流程：

* 检测 master

  sentinel1 检测到 master 下线后会做 flag:SRI_S_DOWN 标志，此时 master 的状态是主观下线，并通知其他哨兵，其他哨兵也会尝试与 master 连接，如果大于 (n/2) + 1 个sentinel 检测到 master 下线，就达成共识更改 flag，此时 master 的状态是客观下线

  ![](https://gitee.com/seazean/images/raw/master/DB/Redis-哨兵模式故障转移工作流程1.png)

* 当 sentinel 认定 master 下线之后，此时需要决定更换 master，选举某个 sentinel 处理事故

  在选举的时候每一个 sentinel 都有一票，于是每个 sentinel 都会发出一个指令，在内网广播要做主持人；比如 sentinel1 和 sentinel4 发出这个选举指令了，那么 sentinel2 既能接到 sentinel1 的也能接到 sentinel4 的，sentinel2 会把一票投给其中一方，投给指令最先到达的 sentinel。选举最终得票多的，就成为了处理事故的哨兵，需要注意在这个过程中有可能会存在失败的现象，就是一轮选举完没有选取，那就会接着进行第二轮第三轮直到完成选举。

  ![](https://gitee.com/seazean/images/raw/master/DB/Redis-哨兵模式故障转移工作流程2.png)

选择新的 master，在服务器列表中挑选备选 master 的原则：

- 不在线的 OUT

  - 响应慢的 OUT

  - 与原 master 断开时间久的 OUT

  - 优先原则：优先级 → offset → runid

选出新的 master之后，发送指令（sentinel ）给其他的 slave

* 向新的 master 发送 slaveof no one
* 向其他 slave 发送 slaveof 新 masterIP 端口





****



## 集群模式

### 集群概述

集群就是使用网络将若干台计算机联通起来，并提供统一的管理方式，使其对外呈现单机的服务效果

![](https://gitee.com/seazean/images/raw/master/DB/Redis-集群图示.png)

**集群作用：**

- 分散单台服务器的访问压力，实现负载均衡
- 分散单台服务器的存储压力，实现可扩展性
- 降低单台服务器宕机带来的业务灾难



***



### 结构设计

**数据存储设计：**

1. 通过算法设计，计算出key应该保存的位置

   ```markdown
   key -> CRC16(key) -> 值 -> %16384 -> 存储位置
   ```

2. 将所有的存储空间计划切割成16384份，每台主机保存一部分

   注意：每份代表的是一个存储空间，不是一个key的保存空间，可以存储多个key

3. 将key按照计算出的结果放到对应的存储空间

   <img src="https://gitee.com/seazean/images/raw/master/DB/Redis-集群存储空间.png" style="zoom: 67%;" />

查找数据：

- 各个数据库相互通信，保存各个库中槽的编号数据
- 一次命中，直接返回
- 一次未命中，告知具体位置，最多两次命中

设置数据：系统默认存储到某一个

<img src="https://gitee.com/seazean/images/raw/master/DB/Redis-集群查找数据.png" style="zoom:67%;" />



***



### 结构搭建

整体框架：

- 配置服务器（3主3从）
- 建立通信（Meet）
- 分槽（Slot）
- 搭建主从（master-slave）

创建集群conf配置文件：

* redis-6501.conf

  ```sh
  port 6501
  dir "/redis/data"
  dbfilename "dump-6501.rdb"
  cluster-enabled yes
  cluster-config-file "cluster-6501.conf"
  cluster-node-timeout 5000
  
  #其他配置文件参照上面的修改端口即可，内容完全一样
  ```

* 服务端启动：

  ```sh
  redis-server config_file_name
  ```

* 客户端启动：

  ```sh
  redis-cli -p 6504 -c
  ```

**cluster配置：**

- 是否启用cluster，加入cluster节点

  ```properties
  cluster-enabled yes|no
  ```

- cluster配置文件名，该文件属于自动生成，仅用于快速查找文件并查询文件内容

  ```properties
  cluster-config-file filename
  ```

- 节点服务响应超时时间，用于判定该节点是否下线或切换为从节点

  ```properties
  cluster-node-timeout milliseconds
  ```

- master连接的slave最小数量

  ```properties
  cluster-migration-barrier min_slave_number
  ```

客户端启动命令：


**cluster节点操作命令（客户端命令）：**

- 查看集群节点信息

  ```properties
  cluster nodes
  ```

- 更改slave指向新的master

  ```properties
  cluster replicate master-id
  ```

- 发现一个新节点，新增master

  ```properties
  cluster meet ip:port
  ```

- 忽略一个没有solt的节点

  ```properties
  cluster forget server_id
  ```

- 手动故障转移

  ```properties
  cluster failover
  ```

**集群操作命令（Linux）：**

* 创建集群

  ```properties
  redis-cli –-cluster create masterhost1:masterport1 masterhost2:masterport2  masterhost3:masterport3 [masterhostn:masterportn …] slavehost1:slaveport1  slavehost2:slaveport2 slavehost3:slaveport3 -–cluster-replicas n
  ```

  注意：master与slave的数量要匹配，一个master对应n个slave，由最后的参数n决定。master与slave的匹配顺序为第一个master与前n个slave分为一组，形成主从结构


* 添加master到当前集群中，连接时可以指定任意现有节点地址与端口

  ```properties
  redis-cli --cluster add-node new-master-host:new-master-port now-host:now-port
  ```

* 添加slave

  ```properties
  redis-cli --cluster add-node new-slave-host:new-slave-port master-host:master-port --cluster-slave --cluster-master-id masterid
  ```

* 删除节点，如果删除的节点是master，必须保障其中没有槽slot

  ```properties
  redis-cli --cluster del-node del-slave-host:del-slave-port del-slave-id
  ```

* 重新分槽，分槽是从具有槽的master中划分一部分给其他master，过程中不创建新的槽

  ```properties
  redis-cli --cluster reshard new-master-host:new-master:port --cluster-from src-  master-id1, src-master-id2, src-master-idn --cluster-to target-master-id --  cluster-slots slots
  ```

  注意：将需要参与分槽的所有masterid不分先后顺序添加到参数中，使用`,`分隔，指定目标得到的槽的数量，所有的槽将平均从每个来源的master处获取


* 重新分配槽，从具有槽的master中分配指定数量的槽到另一个master中，常用于清空指定master中的槽

  ```properties
  redis-cli --cluster reshard src-master-host:src-master-port --cluster-from src-  master-id --cluster-to target-master-id --cluster-slots slots --cluster-yes
  ```

  



***



## 缓存方案

### 缓存模式

#### 旁路缓存

缓存本质：弥补 CPU 的高算力和 IO 的慢读写之间巨大的鸿沟

旁路缓存模式 Cache Aside Pattern 是平时使用比较多的一个缓存读写模式，比较适合读请求比较多的场景

Cache Aside Pattern 中服务端需要同时维系 DB 和 cache，并且是以 DB 的结果为准

* 写操作：先更新 DB，然后直接删除 cache
* 读操作：从 cache 中读取数据，读取到就直接返回；读取不到就从 DB 中读取数据返回，并放到 cache 

时序导致的不一致问题：

* 在写数据的过程中，不能先删除 cache 再更新 DB，因为会造成缓存的不一致。比如请求 1 先写数据 A，请求2随后读数据 A，当请求 1 删除 cache 后，请求 2 直接读取了 DB，此时请求 1 还没写入 DB

* 在写数据的过程中，先更新 DB 再删除 cache 也会出现问题，但是概率很小，因为缓存的写入速度非常快

旁路缓存的缺点：

* 首次请求数据一定不在 cache 的问题，一般采用缓存预热的方法，将热点数据可以提前放入 cache 中
* 写操作比较频繁的话导致 cache 中的数据会被频繁被删除，影响缓存命中率



****



#### 读写穿透

读写穿透模式 Read/Write Through Pattern：服务端把 cache 视为主要数据存储，从中读取数据并将数据写入其中，cache 负责将此数据读取和写入 DB，从而减轻了应用程序的职责

* 写操作：先查 cache，cache 中不存在，直接更新 DB；cache 中存在则先更新 cache，然后 cache 服务更新 DB（同步更新 cache 和 DB）

* 读操作：从 cache 中读取数据，读取到就直接返回 ；读取不到先从 DB 加载，写入到 cache 后返回响应

  Read-Through Pattern 实际只是在 Cache-Aside Pattern 之上进行了封装。在 Cache-Aside Pattern 下，发生读请求的时候，如果 cache 中不存在对应的数据，是由客户端负责把数据写入 cache，而 Read Through Pattern 则是 cache 服务自己来写入缓存的，对客户端是透明的

Read-Through Pattern 也存在首次不命中的问题，采用缓存预热解决



***



#### 异步缓存

异步缓存写入 Write Behind Pattern 由 cache 服务来负责 cache 和 DB 的读写，对比读写穿透不同的是 Write Behind Caching 是只更新缓存，不直接更新 DB，改为**异步批量**的方式来更新 DB，可以减小写的成本

缺点：这种模式对数据一致性没有高要求，可能出现 cache 还没异步更新 DB，服务就挂掉了

应用：

* DB 的写性能非常高，适合一些数据经常变化又对数据一致性要求不高的场景，比如浏览量、点赞量

* MySQL 的 InnoDB Buffer Pool 机制用到了这种策略



参考文章：https://snailclimb.gitee.io/javaguide



****



### 缓存一致

使用缓存代表不需要强一致性，只需要最终一致性

缓存不一致的方法：

* 数据库和缓存数据**强一致**场景 ：
  * 更新 DB 时同样更新 cache，加一个锁来保证更新 cache 时不存在线程安全问题，这样可以增加命中率
  * 延迟双删：先淘汰缓存再写数据库，休眠1秒再次淘汰缓存，可以将1秒内造成的缓存脏数据再次删除
  * CDC 同步：通过 canal 订阅 MySQL binlog 的变更上报给 Kafka，系统监听 Kafka 消息触发缓存失效
* 可以短暂地允许数据库和缓存数据**不一致**场景：更新 DB 的时候同样更新 cache，但是给缓存加一个比较短的过期时间，这样就可以保证即使数据不一致影响也比较小



参考文章：http://cccboke.com/archives/2020-09-30-21-29-56



***



### 企业方案

#### 缓存预热

场景：宕机，服务器启动后迅速宕机

问题排查：

1. 请求数量较高，大量的请求过来之后都需要去从缓存中获取数据，但是缓存中又没有，此时从数据库中查找数据然后将数据再存入缓存，造成了短期内对redis的高强度操作从而导致问题

2. 主从之间数据吞吐量较大，数据同步操作频度较高

解决方案：

- 前置准备工作：

  1. 日常例行统计数据访问记录，统计访问频度较高的热点数据

  2. 利用 LRU 数据删除策略，构建数据留存队列例如：storm与kafka配合

- 准备工作：

  1. 将统计结果中的数据分类，根据级别，redis优先加载级别较高的热点数据

  2. 利用分布式多服务器同时进行数据读取，提速数据加载过程

  3. 热点数据主从同时预热

- 实施：

  4. 使用脚本程序固定触发数据预热过程

  5. 如果条件允许，使用了CDN（内容分发网络），效果会更好

总的来说：缓存预热就是系统启动前，提前将相关的缓存数据直接加载到缓存系统。避免在用户请求的时候，先查询数据库，然后再将数据缓存的问题！用户直接查询事先被预热的缓存数据！



***



#### 缓存雪崩

场景：数据库服务器崩溃，一连串的问题会随之而来。系统平稳运行过程中，忽然数据库连接量激增，应用服务器无法及时处理请求，大量 408，500 错误页面出现，客户反复刷新页面获取数据，造成数据库崩溃、应用服务器崩溃、重启应用服务器无效、Redis 服务器崩溃、Redis 集群崩溃、重启数据库后再次被瞬间流量放倒

问题排查：在一个较短的时间内，缓存中**较多的 key 集中过期**，此周期内请求访问过期的数据 Redis 未命中，Redis 向数据库获取数据，数据库同时收到大量的请求无法及时处理。

解决方案：

* 思路：

  1. 更多的页面静态化处理

  2. 构建**多级缓存**架构：Nginx 缓存 + redis 缓存 + ehcache 缓存

  3. 检测 Mysql 严重耗时业务进行优化：对数据库的瓶颈排查，例如超时查询、耗时较高事务等

  4. 灾难预警机制：监控 redis 服务器性能指标，CPU 使用率、内存容量、平均响应时间、线程数

  5. 限流、降级：短时间范围内牺牲一些客户体验，限制一部分请求访问，降低应用服务器压力，待业务低速运转后再逐步放开访问

* 实践：

  1. LRU 与 LFU切换

  2. 数据有效期策略调整：根据业务数据有效期进行分类错峰，A 类 90 分钟，B 类 80 分钟，C 类 70 分钟，过期时间使用固定时间 + 随机值的形式，稀释集中到期的 key 的数量

  3. 超热数据使用永久 key

  4. 定期维护：对即将过期数据做访问量分析，确认是否延时，配合访问量统计，做热点数据的延时

  5. 加锁：慎用


总的来说：缓存雪崩就是瞬间过期数据量太大，导致对数据库服务器造成压力。如能够有效避免过期时间集中，可以有效解决雪崩现象的出现（约40%），配合其他策略一起使用，并监控服务器的运行数据，根据运行记录做快速调整。



***



#### 缓存击穿

场景：系统平稳运行过程中，数据库连接量瞬间激增，Redis 服务器无大量 key 过期，Redis 内存平稳，无波动，Redis 服务器 CPU 正常，但是数据库崩溃

问题排查：

1. Redis 中某个 key 过期，该 key 访问量巨大

2. 多个数据请求从服务器直接压到 Redis 后，均未命中

3. Redis 在短时间内发起了大量对数据库中同一数据的访问

简而言之两点：单个 key 高热数据，key 过期

解决方案：

1. 预先设定：以电商为例，每个商家根据店铺等级，指定若干款主打商品，在购物节期间，加大此类信息 key 的过期时长 注意：购物节不仅仅指当天，以及后续若干天，访问峰值呈现逐渐降低的趋势

2. 现场调整：监控访问量，对自然流量激增的数据延长过期时间或设置为永久性 key

3. 后台刷新数据：启动定时任务，高峰期来临之前，刷新数据有效期，确保不丢失

4. 二级缓存：设置不同的失效时间，保障不会被同时淘汰就行

5. 加锁：分布式锁，防止被击穿，但是要注意也是性能瓶颈，慎重！

总的来说：缓存击穿就是单个高热数据过期的瞬间，数据访问量较大，未命中 redis 后，发起了大量对同一数据的数据库访问，导致对数据库服务器造成压力。应对策略应该在业务数据分析与预防方面进行，配合运行监控测试与即时调整策略，毕竟单个 key 的过期监控难度较高，配合雪崩处理策略即可



***



#### 缓存穿透

场景：系统平稳运行过程中，应用服务器流量随时间增量较大，Redis 服务器命中率随时间逐步降低，Redis 内存平稳，内存无压力，Redis 服务器 CPU 占用激增，数据库服务器压力激增，数据库崩溃

问题排查：

1. Redis 中大面积出现未命中

2. 出现非正常 URL 访问

问题分析：

- 获取的数据在数据库中也不存在，数据库查询未得到对应数据
- Redis 获取到 null 数据未进行持久化，直接返回
- 下次此类数据到达重复上述过程
- 出现黑客攻击服务器

解决方案：

1. 缓存 null：对查询结果为null的数据进行缓存 (长期使用，定期清理) ，设定短时限，例如30-60秒，最高5分钟

2. 白名单策略：提前预热各种分类数据 id 对应的 **bitmaps**，id 作为 bitmaps 的 offset，相当于设置了数据白名单。当加载正常数据时放行，加载异常数据时直接拦截（效率偏低），也可以使用布隆过滤器（有关布隆过滤器的命中问题对当前状况可以忽略）

3. 实时监控：实时监控 redis 命中率（业务正常范围时，通常会有一个波动值）与null数据的占比

   * 非活动时段波动：通常检测3-5倍，超过5倍纳入重点排查对象
   * 活动时段波动：通常检测10-50倍，超过50倍纳入重点排查对象

   ​	根据倍数不同，启动不同的排查流程。然后使用黑名单进行防控（运营）

4. key 加密：临时启动防灾业务 key，对 key 进行业务层传输加密服务，设定校验程序，过来的 key 校验；例如每天随机分配60个加密串，挑选2到3个，混淆到页面数据 id 中，发现访问 key 不满足规则，驳回数据访问

总的来说：缓存击穿是指访问了不存在的数据，跳过了合法数据的 redis 数据缓存阶段，每次访问数据库，导致对数据库服务器造成压力。通常此类数据的出现量是一个较低的值，当出现此类情况以毒攻毒，并及时报警。无论是黑名单还是白名单，都是对整体系统的压力，警报解除后尽快移除

参考视频：https://www.bilibili.com/video/BV15y4y1r7X3





***



### 性能指标

redis中的监控指标如下：

* 性能指标：Performance

  响应请求的平均时间：

  ```sh
  latency
  ```

  平均每秒处理请求总数：

  ```sh
  instantaneous_ops_per_sec
  ```

  缓存查询命中率（通过查询总次数与查询得到非nil数据总次数计算而来）：

  ```sh
  hit_rate(calculated)
  ```

* 内存指标：Memory

  当前内存使用量：

  ```sh
  used_memory
  ```

  内存碎片率（关系到是否进行碎片整理）：

  ```sh
  mem_fragmentation_ratio
  ```

  为避免内存溢出删除的key的总数量：

  ```sh
  evicted_keys
  ```

  基于阻塞操作（BLPOP等）影响的客户端数量：

  ```sh
  blocked_clients
  ```

* 基本活动指标：Basic_activity

  当前客户端连接总数：

  ```sh
  connected_clients
  ```

  当前连接slave总数：

  ```sh
  connected_slaves
  ```

  最后一次主从信息交换距现在的秒：

  ```sh
  master_last_io_seconds_ago
  ```

  key的总数：

  ```sh
  keyspace
  ```

* 持久性指标：Persistence

  当前服务器其最后一次RDB持久化的时间：

  ```sh
  rdb_last_save_time
  ```

  当前服务器最后一次RDB持久化后数据变化总量：

  ```sh
  rdb_changes_since_last_save
  ```

* 错误指标：Error

  被拒绝连接的客户端总数（基于达到最大连接值的因素）：

  ```sh
  rejected_connections
  ```

  key未命中的总次数：

  ```sh
  keyspace_misses
  ```

  主从断开的秒数：

  ```sh
  master_link_down_since_seconds
  ```

要对redis的相关指标进行监控，我们可以采用一些用具：

- CloudInsight Redis
- Prometheus
- Redis-stat
- Redis-faina
- RedisLive
- zabbix

命令工具：

* benchmark

  测试当前服务器的并发性能：

  ```sh
  redis-benchmark [-h ] [-p ] [-c ] [-n <requests]> [-k ]
  ```

  范例：100个连接，5000次请求对应的性能

  ```sh
  redis-benchmark -c 100 -n 5000
  ```

  ![](https://gitee.com/seazean/images/raw/master/DB/Redis-redis-benchmark指令.png)

* redis-cli

  monitor：启动服务器调试信息

  ```sh
  monitor
  ```

  slowlog：慢日志

  ```sh
  slowlog [operator]    #获取慢查询日志
  ```

  * get ：获取慢查询日志信息
  * len ：获取慢查询日志条目数
  * reset ：重置慢查询日志

  相关配置：

  ```sh
  slowlog-log-slower-than 1000 #设置慢查询的时间下线，单位：微妙
  slowlog-max-len 100	#设置慢查询命令对应的日志显示长度，单位：命令数
  ```

  