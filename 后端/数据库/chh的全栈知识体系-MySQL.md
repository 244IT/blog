## 1.邂逅MySQL

### 1.1.MySQL的介绍：

* MySQL原本是一个开源的数据库，原开发者为瑞典的MySQL AB公司；

* 在2008年被Sun公司收购；在2009年，Sun被Oracle收购；

* 所以目前MySQL归属于Oracle； 

**MySQL是一个关系型数据库，其实本质上就是一款软件、一个程序：**

* 这个程序中管理着多个数据库；

* 每个数据库中可以有多张表；

* 每个表中可以有多条数据；

### 1.2.启动和停止MySQL服务

**启动**

1. 通过计算机管理中的服务启动
2. 启动cmd -> 输入`net start mysql`

**停止**

1. 通过计算机管理中的服务停止
2. 启动cmd -> 输入`net stop mysql`

### 1.3.MySQL登录和退出

**登录**

登录： mysql+参数 

* -D --database = name 打开指定数据库
* --delimiter = name 指点分隔符
* -h --host = name 服务器名称
* -p --password [=name] 密码
* -P --port = # 端口号
* -u --user = name 用户名
* -V --version 输出版本信息并退出

```
C:\Users\H>mysql -uroot -p -P3306 -hlocalhost
Enter password: ****
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.26 MySQL Community Server (GPL)

...

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

>通常也使用mysql -uroot -p[密码]更便捷地登录

**退出**

* mysql > exit;
* mysql > quit;
* mysql > \q

```
mysql> \q
Bye
```

### 1.4.修改MySQL提示符

通常我们登录mysql之后命令行前面会跟着一个`mysql>`，这就是mysql提示符，我们可以修改它

**方式一**

* 连接客户端时通过参数指定`mysql -uroot -proot --prompt 提示符  (-proot = --password[name])`

**方式二**

* 连接上客户端后，通过prompt命令修改`prompt 提示符`

**可修改的提示符：**

1. \D 完整的日期
2. \d 当前数据库
3. \h 服务器名称
4. \u 当前用户

demo：修改提示符

```
C:\Users\H>mysql -uroot -proot --prompt \D
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.26 MySQL Community Server (GPL)

...

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

Sun Dec 20 15:13:13 2020
```

### 1.5.MySQL常用命令和语法规范

**常用命令**

* 显示当前服务器名称： SELECT VERSION();
* 显示当前日期： SELECT NOW();
* 显示当前用户： SELECT USER();
* 显示当前数据库： SELECT DATABASE();

**语法规范**

* 关键字与函数名称全部大写
* 数据库名称、表名称、字段名称全部小写
* SQL语句必须以分号结尾
*  如果遇到关键字作为表名或者字段名称，可以使用``包裹;

## 2.初识SQL

### 2.1.认识SQL语句

我们希望操作数据库（特别是在程序中），就需要有和数据库沟通的语言，这个语言就是SQL： 

* SQL是Structured Query Language，称之为结构化查询语言，简称SQL； 
* 使用SQL编写出来的语句，就称之为SQL语句； 
* SQL语句可以用于对数据库进行操作；

事实上，常见的关系型数据库SQL语句都是比较相似的，所以你学会了MySQL中的SQL语句，之后去操作比如

* Oracle或者其他关系型数据库，也是非常方便的。

### 2.2.SQL语句的分类

**常见的SQL语句我们可以分成四类：**

1. DDL（Data Definition Language）：数据定义语言； 
   * 可以通过DDL语句对数据库或者表进行：创建、删除、修改等操作；
2. DML（Data Manipulation Language）：数据操作语言； 
   * 可以通过DML语句对表进行：添加、删除、修改等操作；

3. DQL（Data Query Language）：数据查询语言； 
   * 可以通过DQL从数据库中查询记录；（重点）

4. DCL（Data Control Language）：数据控制语言； 
   * 对数据库、表格的权限进行相关访问控制操作；

### 2.3.SQL的数据类型

#### 2.3.1.数字类型

 我们知道不同的数据会划分为不同的数据类型，在数据库中也是一样：

* MySQL支持的数据类型有：数字类型，日期和时间类型，字符串（字符和字节）类型，空间类型和 JSON数
  据类型。

数字类型

* MySQL的数字类型有很多：
* 整数数字类型：INTEGER，INT，SMALLINT，TINYINT，MEDIUMINT，BIGINT；
* 浮点数字类型：FLOAT，DOUBLE（FLOAT是4个字节，DOUBLE是8个字节）；
* 精确数字类型：DECIMAL，NUMERIC（DECIMAL是NUMERIC的实现形式）；

| 类型         | 大小                                     | 范围（有符号）                                               | 范围（无符号）                                               | 用途            |
| ------------ | ---------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------- |
| TINYINT      | 1 byte                                   | (-128，127)                                                  | (0，255)                                                     | 小整数值        |
| SMALLINT     | 2 bytes                                  | (-32 768，32 767)                                            | (0，65 535)                                                  | 大整数值        |
| MEDIUMINT    | 3  bytes                                 | (-8 388 608，8 388 607)                                      | (0，16 777 215)                                              | 大整数值        |
| INT或INTEGER | 4  bytes                                 | (-2 147 483 648，2 147 483 647)                              | (0，4 294 967 295)                                           | 大整数值        |
| BIGINT       | 8  bytes                                 | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)      | (0，18 446 744 073 709 551 615)                              | 极大整数值      |
| FLOAT        | 4  bytes                                 | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)                  | 单精度 浮点数值 |
| DOUBLE       | 8  bytes                                 | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
| DECIMAL      | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值                                               | 依赖于M和D的值                                               | 小数值          |

>float和double的区别是：存储的范围不同，小数点后面的位数不同

#### 2.3.2.日期类型

表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。

每个时间类型有一个有效值范围和一个"零"值，当指定不合法的MySQL不能表示的值时使用"零"值。

TIMESTAMP类型有专有的自动更新特性，将在后面描述。

| 类型      | 大小 ( bytes) | 范围                                                         | 格式                | 用途                     |
| --------- | ------------- | ------------------------------------------------------------ | ------------------- | ------------------------ |
| DATE      | 3             | 1000-01-01/9999-12-31                                        | YYYY-MM-DD          | 日期值                   |
| TIME      | 3             | '-838:59:59'/'838:59:59'                                     | hh:mm:ss            | 时间值或持续时间         |
| YEAR      | 1             | 1901/2155/0000                                               | YYYY                | 年份值                   |
| DATETIME  | 8             | 1000-01-01 00:00:00/9999-12-31 23:59:59                      | YYYY-MM-DD hh:mm:ss | 混合日期和时间值         |
| TIMESTAMP | 4             | 1970-01-01 00:00:00/2038  结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYYMMDD hhmmss     | 混合日期和时间值，时间戳 |

>DATETIME或TIMESTAMP值可以包括在高达微秒（6位）精度的后小数秒一部分
>
>* 比如DATETIME表示的范围可以是'1000-01-01 00:00:00.000000'到'9999-12-31 23:59:59.999999

#### 2.3.3.字符类型

| 类型       | 大小                  | 用途                            |
| ---------- | --------------------- | ------------------------------- |
| CHAR       | 0-255 bytes           | 定长字符串                      |
| VARCHAR    | 0-65535 bytes         | 变长字符串                      |
| TINYBLOB   | 0-255 bytes           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255 bytes           | 短文本字符串                    |
| BLOB       | 0-65 535 bytes        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535 bytes        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据                    |

## 3.数据库操作

* 查看当前数据库管理系统下的数据库列表：`SHOW DATABASES`
* 创建数据库：`CREATE DATABASE [IF NOT EXISTS] db_name`

* 删除数据库：`DROP DATABASE [IF EXISTS] db_name`
* 修改数据库：`ALTER DATABASE [db_name][DEFAULT] CHARACTER SET [=] charset_name`
* 使用数据库：`USE DATABASES`

demo:修改数据库的字符集和排序规则

```
ALTER DATABASE bilibili CHARACTER SET = utf8 COLLATE = utf8_unicode_ci;
```

## 4.数据表的操作

### 3.1.常用数据表的操作

* 查看所有的数据表：`SHOW TABLES`
* 查看某一数据表结构`SHOW COLUMNS FROM tbl_name`或者`DESC tbl_name`
* 创建数据表：

```
CREATE TABLE [IF NOT EXISTS] table_name(

	colomn_name(列名称)  data_type,

	...

)
```

demo：创建数据表

```
CREATE TABLE IF NOT EXISTS `users`(
    name VARCHAR(20),
    age INT,
    height DOUBLE
);
```

* 查看创建表结构时的语句：`SHOW CREATE TABLE tbl_name`

demo：查看创建`student`表时的sql语句







1.下载免安装版

**2.执行以下步骤**

* 跳转到mysql的bin目录下
* 安装mysql的服务：mysqld --install
* 初始化mysql，在这里，初始化会产生一个随机密码,如下图框框所示，记住这个密码，后面会用到(mysqld --initialize --console)

**3.修改密码**

* alter user 'root'@'localhost' identified by '新密码'

