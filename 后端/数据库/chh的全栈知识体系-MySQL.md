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

## 4.数据表操作

### 3.1.表约束

>约束保证数据的完整性和一致性，约束分为表级约束和列级约束（针对列的数目划分，针对某一个字段的称为列级约束，针对两个或者两个以上的称为表级约束）

#### 3.1.1.主键：PRIMARY KEY

一张表中，我们为了区分每一条记录的唯一性，必须有一个字段是永远不会重复，并且不会为空的，这个字段我们通常会将它设置为主键：

* 主键是表中唯一的索引；

* 并且必须是NOT NULL的，如果没有设置 NOT NULL，那么MySQL也会隐式的设置为NOT NULL； 

* 主键也可以是多列索引，PRIMARY KEY(*key_part, ...)，我们一般称之为联合主键；*

> 建议：开发中主键字段应该是和业务无关的，尽量不要使用业务字段来作为主键；

#### 3.1.2.唯一：UNIQUE

某些字段在开发中我们希望是唯一的，不会重复的，比如手机号码、身份证号码等，这个字段我们可以使用UNIQUE来约束：

* 使用UNIQUE约束的字段在表中必须是不同的；

* 对于所有引擎，UNIQUE 索引允许NULL包含的列具有多个值NULL。

#### 3.1.3.不能为空：NOT NULL

* 某些字段我们要求用户必须插入值，不可以为空，这个时候我们可以使用 NOT NULL 来约束； 

#### 3.1.4.默认值：DEFAULT

* 某些字段我们希望在没有设置值时给予一个默认值，这个时候我们可以使用 DEFAULT来完成；

#### 3.1.5.自动递增：AUTO_INCREMENT

* 某些字段我们希望不设置值时可以进行递增，比如用户的id，这个时候可以使用AUTO_INCREMENT来完成； 

#### 3.1.6.外键约束：FOREIGN KEY(补)

目的： 

1. 保证数据完整性和一致性
2. 实现一对一或一对多关系

外键约束的要求：

1. 父表和子表必须使用相同的存储引擎，而且禁止使用临时表
2. 数据表的存储引擎只能为INNODB
3. 外键列和参照列必须同时具有相似的数据类型。其中数字的长度或是否有符号位必须相同；而字符长度则可以不同
4. 外键列和参照列必须创建索引。如果外键列不存在索引的话，MySQL将自动创建索引

外键约束的参照操作：

1. CASCADE :从父表删除或更新且自动删除或更新子表中匹配的行
2. SET NULL：从父表删除或更新行，并设置子表中的外键列
3. RESTRICT：拒绝对父表的删除或更新操作
4. NO ACTION：标准SQL的关键字，在MySQL中与RESTRICT相同

### 3.2.常用数据表的操作

1. 查看所有的数据表：`SHOW TABLES`

2. 查看某一数据表结构`SHOW COLUMNS FROM tbl_name`或者`DESC tbl_name`

3. 创建数据表：

```
CREATE TABLE [IF NOT EXISTS] table_name(

	colomn_name(列名称)  data_type,

	...

)
```

demo：创建数据表

```
CREATE TABLE IF NOT EXISTS user (
	id PRIMARY KEY AUTO_INCREMENT,
	name VERCHAT(20) NOT NULL,
	age INT DEFAULT 0,
	phoneNum VERCHAR(20) UNIQUE DEFAULT '',
	createTime TIMESTAMP
)
```

5. 查看创建表结构时的语句：`SHOW CREATE TABLE tbl_name`

demo：查看创建`student`表时的sql语句

```
SHOW CREATE TABLE `student`
```

6. 修改表：

* 修改表的名字：`ALTER TABLE tbl_name RENAME TO new_tbl_name`
* 添加一个新的列：`ALTER TABLE tbl_name ADD column_name data_type`
* 修改字段的名称：`ALTER TABLE tbl_name CHANGE old_column_name new_column_name data_type`
* 修改字段类型：`ALTER TABLE tbl_name MODIFY column_name new_data_type`
* 删除某一个字段：`ALTER TABLE tbl_name DROP column_name`
* 根据一个表结构去创建另外一张表：`CREATE TABLE new_tbl_name LIKE tbl_name`
* 根据另外一个表的所有内容，创建一个新的表：`CREATE TABLE new_table_name (SELECT * FROM tbl_name)`

### 3.3.DML(数据操作语言)

#### 1.插入记录

语法：`INSERT INTO tbl_name (col_name,...) VALUES (...)  `

```
INSERT INTO `user` VALUES (110, 'why', '020-110110', '2020-10-20', '2020-11-11');
INSERT INTO `user` (name, telPhone, createTime, updateTime)
						VALUES ('kobe', '000-1111111', '2020-10-10', '2030-10-20');
						
INSERT INTO `user` (name, telPhone)
						VALUES ('lilei', '000-1111112');
```

#### 2.删除记录

语法：`DELETE FROM tbl_name [WHERE where_condition] `

```
# 删除所有的数据
DELETE FROM `user`;
# 删除符合条件的数据
DELETE FROM `user` WHERE id = 110;
```

#### 3.更新记录

语法：`UPDATE tbl_name SET col_name1={expr1|DEFAULT} [,col_name2={expr1|DEFAULT}] ...[WHERE where_condition] `

```
# 更新所有的数据
UPDATE `user` SET name = 'lily', telPhone = '010-110110';
# 更新符合条件的数据
UPDATE `user` SET name = 'lily', telPhone = '010-110110' WHERE id = 115;
```

### 3.4.DQL(数据查询语言)

准备数据：

1.一张表

```
CREATE TABLE IF NOT EXISTS `products` (
    id INT PRIMARY KEY AUTO_INCREMENT,
    brand VARCHAR(20),
    title VARCHAR(100) NOT NULL,
    price DOUBLE NOT NULL,
    score DECIMAL(2,1),
    voteCnt INT,
    url VARCHAR(100),
    pid INT
);
```

2.安装mysql2：`npm install mysql2`执行下列代码：

```
const mysql = require('mysql2');
const connection = mysql.createConnection({
    host: 'localhost',
    port: 3306,
    user: 'root',
    password: 'Coderwhy888.',
    database: 'coderhub'
    });
    const statement = `INSERT INTO products SET ?;`
    const phoneJson = require('./phone.json');
    for (let phone of phoneJson) {
    connection.query(statement, phone);
}
```



#### 1. 基本查询

```
# 查询表中所有的字段以及所有的数据
SELECT * FROM `products`;
# 查询指定的字段
SELECT title, price FROM `products`;
# 对字段结果起一个别名
SELECT title as phoneTitle, price as currentPrice FROM `products`;
```

#### 2.WHERE条件

**条件判断语句**

```
# 案例：查询价格小于1000的手机
SELECT title, price FROM `products` WHERE price < 1000;
# 案例二：价格等于999的手机
SELECT * FROM `products` WHERE price = 999;
# 案例三：价格不等于999的手机
SELECT * FROM `products` WHERE price != 999;
SELECT * FROM `products` WHERE price <> 999;
# 案例四：查询品牌是华为的手机
SELECT * FROM `products` WHERE brand = '华为';
```

**逻辑运算语句**

```
# 案例一：查询1000到2000之间的手机
SELECT * FROM `products` WHERE price > 1000 AND price < 2000;
SELECT * FROM `products` WHERE price > 1000 && price < 2000;
# BETWEEN AND 包含等于
SELECT * FROM `products` WHERE price BETWEEN 1099 AND 2000;

# 案例二：价格在5000以上或者是品牌是华为的手机
SELECT * FROM `products` WHERE price > 5000 || brand = '华为';

# 将某些值设置为NULL
-- UPDATE `products` SET url = NULL WHERE id >= 85 and id <= 88;
# 查询某一个值为NULL
SELECT * FROM `products` WHERE url IS NULL;
-- SELECT * FROM `products` WHERE url = NULL;
-- SELECT * FROM `products` WHERE url IS NOT NULL;
```

>BETWEEN和AND后面直接带数字：包含等于

#### 3.模糊查询(LIKE)

```
SELECT * FROM `products` WHERE title LIKE '%M%';
SELECT * FROM `products` WHERE title LIKE '%P%';
SELECT * FROM `products` WHERE title LIKE '_P%';
```

#### 4.查询结果排序(ORDER BY)

```
SELECT * FROM `products` WHERE brand = '华为' || brand = '小米' || brand = '苹果';
SELECT * FROM `products` WHERE brand IN ('华为', '小米', '苹果') ORDER BY price ASC, score DESC;
```

#### 5.分页查询(LIMIT)

```
# LIMIT limit OFFSET offset;
# Limit offset, limit;
SELECT * FROM `products` LIMIT 20 OFFSET 0;
SELECT * FROM `products` LIMIT 20 OFFSET 20;
SELECT * FROM `products` LIMIT 40, 20;
```

#### 6.分组查询(GROUP BY)

GROUP BY通常和聚合函数一起使用，表示我们先对数据进行分组，再对每一组数据，进行聚合函数的计算

需求：根据品牌进行分组；计算各个品牌中：商品的个数、平均价格，也包括：最高价格、最低价格、平均评分；

```
SELECT brand,
COUNT(*) as count,
AVG(price) as avgPrice,
MAX(price) as maxPrice,
MIN(price) as minPrice,
AVG(score) as avgScore
FROM `products` GROUP BY brand;
```

**分组约束**

使用我们希望给Group By查询到的结果添加一些约束，那么我们可以使用：HAVING。
比如：如果我们还希望在分组后的基础上筛选出平均价格在4000以下，并且平均分在7以上的品牌

```
SELECT brand,
COUNT(*) as count,
ROUND(AVG(price),2) as avgPrice,
MAX(price) as maxPrice,
MIN(price) as minPrice,
AVG(score) as avgScore
FROM `products` GROUP BY brand
HAVING avgPrice < 4000 and avgScore > 7;
```

## 5.多表操作

#### 5.1.多表设计-外键(GROUP BY)

假如我们的下面的商品表中，对应的品牌还需要包含其他的信息：

* 比如品牌的官网，品牌的世界排名，品牌的市值等等；

如果我们直接在商品中去体现品牌相关的信息，会存在一些问题：

* 一方面，products表中应该表示的都是商品相关的数据，应该又另外一张表来表示brand的数据；
* 另一方面，多个商品使用的品牌是一致时，会存在大量的冗余数据；

![](https://imgkr2.cn-bj.ufileos.com/7907acf9-a564-48c4-9cec-acab4c82ee86.png?UCloudPublicKey=TOKEN_8d8b72be-579a-4e83-bfd0-5f6ce1546f13&Signature=R%252FVOEGQntfcjcVx8IsRMG%252FE%252FMZs%253D&Expires=1609408836)

所以，我们可以将所有的批评数据，单独放到一张表中，创建一张品牌的表并插入模拟数据：

```
CREATE TABLE IF NOT EXISTS `brand`(
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(20) NOT NULL,
    website VARCHAR(100),
    worldRank INT
);

INSERT INTO `brand` (name, website, worldRank) VALUES ('华为', 'www.huawei.com', 1);
INSERT INTO `brand` (name, website, worldRank) VALUES ('小米', 'www.mi.com', 10);
INSERT INTO `brand` (name, website, worldRank) VALUES ('苹果', 'www.apple.com', 5);
INSERT INTO `brand` (name, website, worldRank) VALUES ('oppo', 'www.oppo.com', 15);
INSERT INTO `brand` (name, website, worldRank) VALUES ('京东', 'www.jd.com', 3);
INSERT INTO `brand` (name, website, worldRank) VALUES ('Google', 'www.google.com', 8);
```

**添加外键**

新表：在创建表的()最后添加如下语句`FOREIGN KEY (brand_id) REFERENCES brand(id)`

已有表：`ALTER TABLE products ADD FOREIGN KEY (brand_id) REFERENCES brand(id);`

**修改外键的action**

1. 显示创建表的语句：`SHOW CREATE TABLE 表名`
2. 删除原来的外键（只是把外键关系删除）：`ALTER TABLE products DROP FOREIGH KEY 1查询出来的外键名`
3. 添加新的外键并设置action：`ALTER TABLE products ADD FRORIGN KEY (brand_id) REFERENCES brand(id) ON UPDATE CASCADE ON DELETE RESTRICT` 

#### 5.2.笛卡尔乘积

如果我们希望查询到产品的同时，显示对应的品牌相关的信息，因为数据是存放在两张表中，所以这个时候就需要

进行多表查询。如果我们直接通过查询语句希望在多张表中查询到数据，这个时候是什么效果呢？

`SELECT * FROM products brand;`

我们会发现一共有648条数据，这个数据量是如何得到的呢？

* 第一张表的108条 * 第二张表的6条数据；

* 也就是说第一张表中每一个条数据，都会和第二张表中的每一条数据结合一次； 

* 这个结果我们称之为 **笛卡尔乘积**，也称之为直积，表示为 X*Y； 

但是事实上很多的数据是没有意义的，比如华为和苹果、小米的品牌结合起来的数据就是没有意义的，我们可不可

以进行筛选呢？

* 使用where来进行筛选；

* 这个表示查询到笛卡尔乘积后的结果中，符合products.brand_id = brand.id条件的数据过滤出来；

`SELECT * FROM products, brand WHERE products.brand_id = brand.id;`

#### 5.3.连接

**左连接**

如果我们希望获取到的是左边所有的数据（以左表为主）：

* 这个时候就表示无论左边的表是否有对应的brand_id的值对应右边表的id，左边的数据都会被查询出来；

* 这个也是开发中使用最多的情况，它的完整写法是LEFT [OUTER] JOIN，但是OUTER可以省略的；

`SELECT * FROM products LEFT JOIN brand ON products.brand_id = brand.id;`

`SELECT * FROM products LEFT JOIN brand ON products.brand_id = brand.id WHERE brand.id IS NULL;`

**右连接**

如果我们希望获取到的是右边所有的数据（以由表为主）：

* 这个时候就表示无论左边的表中的brand_id是否有和右边表中的id对应，右边的数据都会被查询出来；

* 右连接在开发中没有左连接常用，它的完整写法是RIGHT [OUTER] JOIN，但是OUTER可以省略的；

`SELECT * FROM products RIGHT JOIN brand  ON products.brand_id = brand.id;`

`SELECT * FROM products RIGHT JOIN brand ON products.brand_id = brand.id WHERE brand.id IS NULL;`

**内连接**

事实上内连接是表示左边的表和右边的表都有对应的数据关联：

* 内连接在开发中偶尔也会常见使用，看自己的场景。 

* 内连接有其他的写法：CROSS JOIN或者 JOIN都可以；

`SELECT * FROM products INNER JOIN brand  ON products.brand_id = brand.id;`

* 我们会发现它和之前的下面写法是一样的效果：

`SELECT * FROM products, brand WHERE products.brand_id = brand.id;`

* 但是他们代表的含义并不相同： 

* SQL语句一：内连接，代表的是在两张表连接时就会约束数据之间的关系，来决定之后查询的结果；

* SQL语句二：where条件，代表的是先计算出笛卡尔乘积，在笛卡尔乘积的数据基础之上进行where条件的帅

选；

**全连接**

SQL规范中全连接是使用FULL JOIN，但是MySQL中并没有对它的支持，我们需要使用 UNION 来实现：

```
(SELECT * FROM `products` LEFT JOIN `brand` ON `products`.brand_id = `brand`.id)
UNION
(SELECT * FROM `products` RIGHT JOIN `brand` ON `products`.brand_id = `brand`.id);
```

```
(SELECT * FROM `products` LEFT JOIN `brand` ON `products`.brand_id = `brand`.id WHERE `brand`.id IS NULL)
UNION
(SELECT * FROM `products` RIGHT JOIN `brand` ON `products`.brand_id = `brand`.id WHERE `products`.id IS NULL);
```

## 6.函数

### 5.1.聚合函数

聚合函数表示对值集合进行操作的组（集合）函数：

```
# 华为手机价格的平均值
SELECT AVG(price) FROM `products` WHERE brand = '华为';
# 计算所有手机的平均分
SELECT AVG(score) FROM `products`;
# 手机中最低和最高分数
SELECT MAX(score) FROM `products`;
SELECT MIN(score) FROM `products`;
# 计算总投票人数
SELECT SUM(voteCnt) FROM `products`;
# 计算所有条目的数量
SELECT COUNT(*) FROM `products`;
# 华为手机的个数
SELECT COUNT(*) FROM `products` WHERE brand = '华为';
SELECT COUNT(url) FROM `products` WHERE brand = '苹果';

SELECT COUNT(price) FROM `products`;
SELECT COUNT(DISTINCT price) FROM `products`;
```



1.下载免安装版

**2.执行以下步骤**

* 跳转到mysql的bin目录下
* 安装mysql的服务：mysqld --install
* 初始化mysql，在这里，初始化会产生一个随机密码,如下图框框所示，记住这个密码，后面会用到(mysqld --initialize --console)

**3.修改密码**

* alter user 'root'@'localhost' identified by '新密码'



