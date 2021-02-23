# mysql数据库学习笔记

### 1-1.mysql概述

* mysql由瑞典的MySQL AB公司开发，目前被orecle公司收购。
* MySQL是一个开源的关系型数据库管理系统。
* MySQL分为社区版和企业版。

### 1-2.mysql登录和退出

1. 登录： mysql+参数 
   * -D --database = name 打开指定数据库
   * --delimiter = name 指点分隔符（告诉mysql解释器，该段命令是否已经结束了，默认是分号';'）
   * -h --host = name 服务器名称
   * -p --password [=name] 密码
   * -P --port = # 端口号
   * -u --user = name 用户名
   * -V --version 输出版本信息并退出

<img src='img/QQ图片20200127230841.png'  style="float: left">

2. 退出:  1.exit	2.quit	3.\q

### 1-3.修改mysql修饰符

* 连接客户端时通过参数指定

  > mysql -uroot -proot --prompt 提示符  (-proot = --password[name])

* 连接上客户端后，通过prompt命令修改

  >prompt 提示符

* 提示符可选参数

  >1. \D 完整的日期
  >2. \d 当前数据库
  >3. \h 服务器名称
  >4. \u 当前用户

<IMG src="img/QQ图片20200127232948.png">

### 1-4.mysql常用命令及规范

> 常用命令

* 显示当前服务器名称： SELECT VERSION();
* 显示当前日期： SELECT NOW();
* 显示当前用户： SELECT USER();
* 显示当前数据库： SELECT DATABASE();

> 规范

* 关键字与函数名称全部大写
* 数据库名称、表名称、字段名称全部小写
* SQL语句必须以分号结尾

### 1-5.操作数据库

* 创建数据库

  >CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name 
  >
  >[DEFAULT] CHARACTER SET [=] charset_name

* 查看当前数据库下的服务器列表

  > SHOW {DATABASES | SCHEMAS}
  >
  > [LIKE 'parttern' | Where expr]

* 查看警告信息

  > SHOW WARNINGS

* 查看数据库创建时候使用的指令

  > SHOW CREATE DATABASE db_name

* 修改数据库

  >ALTER {DATABASE | SCHEMA} [db_name]
  >
  >[DEFAULT] CHARACTER SET [=] charset_name

* 删除数据库

  > DROP {DATABASE | SCHEMA} [IF EXISTS] db_name



### 2-1.数据类型之整型

> 数据类型是指列：列、存储过程参数、表达式和局部变量的数据特征，它决定了存储格式，代表了不同的信息类型（指的是数据的基本特征，决定了存储的格式）
>
> 整型的无符号值: UNSIGNED

![image-20200128130712245](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20200128130712245.png)

### 2-2.数据类型之浮点型

> float和double的区别是：存储的范围不同，小数点后面的位数不同

![image-20200128131036566](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20200128131036566.png)

### 2-3.数据类型之日期时间型

![image-20200128131410390](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20200128131410390.png)

### 2-4.数据类型之字符型

![image-20200128133330614](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20200128133330614.png)

### 2-5.操作数据表

* 打开数据库： USE 数据库名称

* 创建数据表： 

  CREATE TABLE [IF NOT EXISTS] table_name(

  ​		colomn_name(列名称)  data_type,

  ​		...

  )

> example: CREATE TABLE t1(
>
> username  varchar(20),
>
> age  TINYINT UNSIGNED,
>
> salary  FLOAT(8,2) UNSIGNED
>
> );

* 查看数据表:	SHOW TABLES [FROM db_name] [LIKE 'pattern' | WHERE expr]
* 查看数据表结构： SHOW COLUMNS FROM tbl_name
* 查看数据表索引： SHOW INDEXES FROM tbl_name\G
* 记录的插入与查找：
  1. 插入：INSERT [INFO] tbl_name [(col_name),...] VALUE(val,...)
  2. 查找：SELECT expr,...FROM tbl_name
* 空值与非空: 
  1. NULL：字段值可以为空
  2. NOT NULL：字段值不能为空

### 2-6.主键和自动编号

* 自动编号：AUTO_INCREMENT
  1. 自动编号，且必须与主键组合使用(而主键不一定要和自动编号一起使用)，
  2. 默认情况下，起始值为1，没次增量为1
* 主键：PRIMARY KEY
  1. 主键约束
  2. 每张数据表只能存在一个主键
  3. 主键保证记录的唯一性
  4. 主键自动为NOT NULL
* 唯一约束：UNIQUE KEY
  1. 唯一约束
  2. 唯一约束可以保证记录的唯一性
  3. 唯一约束的字段可以为空值(NULL)
  4. 每张数据表可以存在多个唯一约束
* 默认约束：DEFAULT
  1. 默认值
  2. 当插入记录时，如果没有明确为字段赋值，则自动赋予默认值

### 3-1.约束

* 约束保证数据的完整性和一致性
* 约束分为表级约束和列级约束（针对列的数目划分，针对某一个字段的称为列级约束，针对两个或者两个以上的称为表级约束）
* 约束的类型包括：（针对功能划分）
  1. NOT NULL(非空约束)
  2. PRIMARY KEY(主键约束)
  3. UNIQUE KEY(唯一约束)
  4. DEFAULT(默认约束)
  5. FOREIGN KEY(外键约束)

> 外键约束： FOREIGN KEY
>
> 目的： 
>
> 1. 保证数据完整性和一致性
> 2. 实现一对一或一对多关系
>
> 外键约束的要求：
>
> 1. 父表和子表必须使用相同的存储引擎，而且禁止使用临时表
> 2. 数据表的存储引擎只能为INNODB
> 3. 外键列和参照列必须同时具有相似的数据类型。其中数字的长度或是否有符号位必须相同；而字符长度则可以不同
> 4. 外键列和参照列必须创建索引。如果外键列不存在索引的话，MySQL将自动创建索引
>
> 外键约束的参照操作：
>
> 1. CASCADE :从父表删除或更新且自动删除或更新子表中匹配的行
> 2. SET NULL：从父表删除或更新行，并设置子表中的外键列
> 3. RESTRICT：拒绝对父表的删除或更新操作
> 4. NO ACTION：标准SQL的关键字，在MySQL中与RESTRICT相同

### 3-2修改数据表

> 1. 添加单列： ALTER TABLE tbl_name ADD [COLUMN] col_name column_definition [FIRST | AFTER col_name]
>
> 2. 添加多列： ALTER TABLE tbl_name ADD [COLUMN] (col_name column_definition,...) 不能指定位置
>
> 3. 删除列： ALTER TABLE tbl_name DROP [COLUMN] col_name
>
> 4. 添加主键约束： ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]] PRIMARY KEY [index_type]
>
>    (index_col_name,...)
>
> 5. 添加唯一约束： ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]] UNIQUE [INDEX | KEY] [index_name] [index_type] (index_col_name,...)
>
> 6. 添加外键约束： ALTER TABLE tbl_name ADD [CONSTRAINT [symbol]] FOREIGN KEY [index_name] (index_col_name,...) REFERENCES reference_definition
>
> 7. 添加/删除默认约束： ALTER TABLE tbl_name ALTER [COLUMN] col_name {SET DEFAULT literal | DROP DEFAULT}
> 8. 删除主键约束： ALTER TABLE tbl_name DROP PRIMARY KEY (不需要指定名字是因为任何一个表只有一个主键)
> 9. 删除唯一约束： ALTER TABLE tbl_name DROP  {INDEX | KEY} index_name
> 10. 删除外键约束： ALTER TABLE tbl_name DROP  FOREIGN KEY fk_symbol
> 11. 修改列定义： ALTER TABLE tbl_name MODIFY [COLUMN] col_name column_definitioin [FIRST | AFTER col_name]
> 12. 修改列名称(也可以修改列名称)： ALTER TABLE tbl_name CHANGE [COLUMN] old_col_name new_col_name column_definitioin [FIRST | AFTER col_name]
> 13. 数据表更名：
>     * 方法一： ALTER TABLE tbl_name RENAME [TO | AS] new_tbl_name
>     * 方法二： RENAME TABLE tbl_name TO new_tbl_name [tbl_name2 TO new_tbl_name2]...

### 4-1.记录操作

1. 插入记录-1： INSERT [INFO] tbl_name [(col_name,...)]  {VALUES| VALUE} ({expr | DEFAULT},...),(...),...

   * 有设置主键和自动编号的可以设置null或者default(前提是没有设置not null)
   * 可以为表达式
   * 有设置default的可以设置值为default，值为设置时default的值
   * 可以同时插入多个数据，用，好分隔。

   <img src="img/QQ图片20200129160511.png">

   <img src='img/QQ截图20200129160753.png'>

   插入记录-2：INSERT [INTO] tbl_name SET col_name={expr | DEFAULT}

ep: insert users set username='james',password='12345',sex=1;

与-1的区别：只能插入一条记录，可以用于子查询

​	   插入记录-3：INSERT [INFO] tbl_name [(col_name,...)] SELECT ... 

​	   说明：将查询的结果插入到指定数据表

2. 修改-更新记录(单表更新)：UPDATE [LOW_PRIORITY] [IGNORE] table_reference SET col_name1={expr1|DEFAULT} [,col_name2={expr1|DEFAULT}] ...[WHERE where_condition]

   ep: 

   * update user3 set age = age + 5;

   * update user3 set age = age  - id ,sex = 0 ;

   * update user3 set age = age  - 10  where  id % 2 = 0;

3. 删除记录(单表删除)：DELETE FROM tbl_name [WHERE where_condition]

   ep:

   * delete from users where id = 6;

4. 查找记录：

   SELECE select_expr [,select_expr ...]

   [

   ​	FROM table_references

   ​	[WHERE where_condition]

   ​	[GROUP BY {col_name | position} [ASC | DESC], ...]

   ​	[HAVING where_condition]

   ​	[ORDER BY {col_name | expr | position} [ASC | DESC], ...]

   ​	[LIMIT {[offset,] row_count | row_count OFFSET offset}]

   ]

   查询表达式： select_expr

   * 每个表达式表示想要的一列，必须有至少一个。
   * 多个列之间以英文逗号分隔
   * 星号(*)表示所有列。tbl_name.\* 可以表示命名表的所有列
   * 查询表达式可以使用[AS] alias_name为其赋予别名
   * 别名可用于GROUP BY, ORDRE BY 或 HAVING字句

   ep：

   * select id,username from users;

   * select username,id from users;

   * select users.username,users.id from users;

   * select id as userid,username as uname from users;

     

5. 条件表达式：WHERE

   * 对记录进行过滤，如果没有指定WHERE子句，则显示所有记录
   * 在WHERE表达式中，可以使用MySQL支持的函数过运算符

6. 查询结果分组：GROUP BY

   * [GROUP BY {col_name | position } [ASC | DESC], ... ]
   * ASC(ascend升序)   DESC(descent降序)

   ep：

   * select username from users group by username;

7. 分组条件：HAVING

   * [HAVING where_condition]

   ep:

   * select username from users group by username having count(id) >= 2;

8. 对查询结果进行排序：ORDER BY 

   * [ORDER BY {col_name | expr | position} [ASC | DESC], ...]

   ep:

   * select * from users order by age, id desc;

9. 限制查询结果返回的数量：LIMIT

   * [LIMIT {[offset,] row_count | row_count OFFSET offset}]

   ep:

   * select * from users order by id desc limit 2,2;
   * insert users2 select username from users where id > 5;

### 5-1.子查询简介

1. 子查询

   * 子查询(Subquery)是指出现在其他SQL语句内的SELECT子句。

   * 列如：

     SELECT * FROM t1 WHERE col1 = (SELECT col2 FROM t2);

     其中SELECT * FROM t1，称为Outer Query/Outer Statement

     SELECT col2 FROM t2，称为SubQuery

   * 子查询指嵌套在查询内部，且必须始终除出现在圆括号内。

   * 子查询可以包含多个关键字或条件，如DISTINCT、GROUP BY、ORDER BY、LIMIT、函数等。

   * 子查询的外层查询可以是：SELECT,INSERT,UPDATE,SET或DO

   * 子查询可以返回标量、一行、一列、或子查询。

### 5-2.使用比较运算符的子查询

1. 使用比较运算符的子查询：=、>、<、>=、<=、<>、!=、<=>

2. 语法结构：operand comparison_operator subquery

   ep:

   * 求所有电脑产品的平均价格,并且保留两位小数，AVG,MAX,MIN、COUNT、SUM为聚合函数

     SELECT ROUND(AVG(goods_price),2) AS avg_price FROM tdb_goods;

   * 查询所有价格大于平均价格的商品，并且按价格降序排序

     SELECT goods_id,goods_name,goods_price FROM tdb_goods WHERE goods_price > 5845.10 ORDER BY goods_price DESC;

   * 用子查询实现：

     SELECT goods_id,goods_name,goods_price FROM tdb_goods 


     WHERE goods_price > (SELECT ROUND(AVG(goods_price),2) AS avg_price FROM tdb_goods) 
    
     ORDER BY goods_price DESC;

   * 查询类型为“超记本”的商品价格

      SELECT goods_price FROM tdb_goods WHERE goods_cate = '超级本';

   * 查询价格大于或等于"超级本"价格的商品，并且按价格降序排列


   ​       SELECT goods_id,goods_name,goods_price FROM tdb_goods 

   ​       WHERE goods_price = ANY(SELECT goods_price FROM tdb_goods WHERE goods_cate = '超级本')

   ​       ORDER BY goods_price DESC;

### 5-3.由[NOT] IN/EXISTS 引发的子查询

1. 使用[NOT] IN的子查询

   语法结构：operand comparison_operator [NOT] IN (subquery) = ANY运算符与IN等效。

   ！=ANY或<>ALL运算符与NOT IN 等效

2. 使用[NOT] EXISTS的子查询

   如果子查询返回任何行，EXISTS将返回TRUE；否则为FALSE;

   ep:

   * ELECT goods_id,goods_name,goods_price FROM tdb_goods 

     WHERE goods_price IN (SELECT goods_price FROM tdb_goods WHERE goods_cate = '超级本')

     ORDER BY goods_price DESC; 

### 5-4.使用INSERT...SELECT插入记录

1. 将查询结果写入数据表

   * INSERT [INFO] tbl_name [(col_name,...)] SELECT ...

   ep:

   * 首先创建一个tdb_goods_cates表：

     CREATE TABLE IF NOT EXISTS tdb_goods_cates(

     cate_id SMALLINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
     cate_name VARCHAR(40)

     );

   * 查询tdb_goods表的所有记录，并且按"类别"分组

     SELECT goods_cate FROM tdb_goods GROUP BY goods_cate;

   * 将分组结果写入到tdb_goods_cates数据表

     INSERT tdb_goods_cates (cate_name) SELECT goods_cate FROM tdb_goods GROUP BY goods_cate;

### 5-5.多表更新

1. UPDATE table_references SET col_name1 = {expr1 | DEFAULT}[,col_name2={expr2 | DEFAULT}]...[WHERE where_condition]

2. 语法结构：

   table_reference

   {[INNER | CROSS] JOIN | {LIFT | RIGHT} [OUTER] JOIN}

   table_reference

   ON conditional_expr

3. 连接类型：INNER JOIN,内连接

   在MySQL中,JOIN,CROSS JOIN和INNER JOIN是等价的。

   LEFT [OUTER] JOIN,左外连接

   RIGHT [OUTER] JOIN,右外连接

ep：

*   UPDATE tdb_goods INNER JOIN tdb_goods_cates ON goods_cate = cate_name 

    SET goods_cate = cate_id ;

### 5-6多表更新之一步到位

1. 创建数据表同时将查询结果写入到数据表：CREATE...SELECT

   * CREATE TABLE [IF NOT EXISTS] tbl_name

     [(create_definition,...)]

     select_statement

   ep:

   * 首先发现品牌名称也需要用多表更新的方式

     select brand_name from tdb_goods group by brand_name;

   * 那么就可以用一步到位的方法

     create table tdb_good_brands(

     brand_id smallint unsigned primary key auto_increment,

     brand_name varchar(40) not null

     )

     select brand_name from tdb_goods group by brand_name;

   * 创建完成后建立连接

     update tdb_goods inner join tdb_good_brands on brand_name = brand_name

     set brand_name = brand_id;

     会发现报错Column 'brand_name' in field list is ambigous

     是因为brand_name = brand_name 会让系统分辨不清是哪一个表的brand_name

     这时我们有两种方法解决：

     1. 起别名：

        * update tdb_goods as a inner join tdb_good_brands as b on a.brand_name = b.brand_name

          set a.brand_name = b.brand_id;

   * 这时会发现一个新的问题，就是原数据库中修改成数字的数据类型是varchar(brand_name,goods_cate)

     所以往往这时我们要修改原表数据的类型

     ALTER TABLE tdb_goods 

     CHANGE goods_cate cate_id SMALLINT UNSIGNED NOT NULL,

     CHANGE brand_name brand_id SMALLINT UNSIGNED NOT NULL;

   * 接下来我们要新建一些品牌记录，为下节课的操作做准备

     -- 分别在tdb_goods_cates和tdb_goods_brands表插入记录

        INSERT tdb_goods_cates(cate_name) VALUES('路由器'),('交换机'),('网卡');

        INSERT tdb_goods_brands(brand_name) VALUES('海尔'),('清华同方'),('神舟');

     -- 在tdb_goods数据表写入任意记录

        INSERT tdb_goods(goods_name,cate_id,brand_id,goods_price) VALUES(' LaserJet Pro P1606dn 黑白激光打印机','12','4','1849');

### 5-7.连接

1. MySQL在SELECT语句、多表更行、多表删除语句中支持JOIN操作

2. 语法结构

   table_reference

   {[INNER | CROSS] JOIN | {LIFT | RIGHT} [OUTER] JOIN}

   table_reference

   ON conditional_expr

3. 数据表参照

   tbl_name [[AS] alias] | table_subquery [AS] alias

   数据表可以使用tbl_name AS alias_name或tbl_name alias_name 赋予别名

   table_subquery可以作为子查询使用在FROM子句中，

   这样的子查询必须为其赋予别名

### 5-8.内连接

1. 连接类型：INNER JOIN,内连接

   在MySQL中,JOIN,CROSS JOIN和INNER JOIN是等价的。

   LEFT [OUTER] JOIN,左外连接

   RIGHT [OUTER] JOIN,右外连接

2. 连接条件

   使用ON关键字来设定连接条件，也可以使用WHERE来代替

   通常使用ON关键字来设定连接条件

   使用WHERE关键字进行结果集记录的过滤

<img src='img/QQ图片20200131051623.png'/>

​	ep：

* 查询所有商品的详细信息(通过内连接实现)

   SELECT goods_id,goods_name,cate_name FROM tdb_goods

   INNER JOIN tdb_goods_cates

   ON tdb_goods.cate_id = tdb_goods_cates.cate_id;

发现cate_id为12的黑白打印机没有，因为在另一个表中没有cate_id为12。也没有‘路由器’，‘交换机’，‘网卡’。而只有两个表都符合的条件的记录。

### 5-9.左外连接和右外连接

1. 左外连接

   <img src='img/QQ图片20200131054149.png'>

2. 右外连接

   <img src='img/QQ图片20200131054734.png'/>

   ep:

   * SELECT goods_id,goods_name,cate_name FROM tdb_goods

     LEFT JOIN tdb_goods_cates

     ON tdb_goods.cate_id = tdb_goods_cates.cate_id;

### 5-10.多表连接

ep:

*    SELECT goods_id,goods_name,cate_name,brand_name,goods_price FROM tdb_goods AS g

     INNER JOIN tdb_goods_cates AS c ON g.cate_id = c.cate_id

     INNER JOIN tdb_goods_brands AS b ON g.brand_id = b.brand_id\G;

### 5-11.关于连接的几点说明

1. 外连接

   * A LEFT JOIN B join_condition

     数据表B的结果集依赖数据表A

     数据表A的结果集根据左连接条件依赖所有数据表（B表除外）

     左外连接条件决定如何检索数据表B（在没有指定WHERE条件的情况下）

     如果数据表A的某条记录符合WHERE条件，但是在数据表B不存在符合连接条件的记录，将生成一个所有列为空的额外的B行

   * 如果使用内连接查找的记录在连接数据表中不存在，并且在WHERE子句中尝试以下操作：col_name被定义为NOT NULL, MySQL将在找到符合连接条件的记录后停止搜索更多的行

### 5-12.无限级分类表设计

1. 自身连接：同一个数据表对其自身进行连接

2. 无限分类的数据表设计

   ep：

   * CREATE TABLE tdb_goods_types(
          type_id   SMALLINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
          type_name VARCHAR(20) NOT NULL,
          parent_id SMALLINT UNSIGNED NOT NULL DEFAULT 0
       ); 

       INSERT tdb_goods_types(type_name,parent_id) VALUES('家用电器',DEFAULT);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('电脑、办公',DEFAULT);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('大家电',1);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('生活电器',1);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('平板电视',3);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('空调',3);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('电风扇',4);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('饮水机',4);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('电脑整机',2);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('电脑配件',2);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('笔记本',9);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('超级本',9);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('游戏本',9);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('CPU',10);
       INSERT tdb_goods_types(type_name,parent_id) VALUES('主机',10);

     查找所有分类及其父类

     SELECT s.type_id,s.type_name,p.type_name FROM tdb_goods_types AS s LEFT JOIN tdb_goods_types AS  p ON s.parent_id = p.type_id;

     查找所有分类及其子类

     SELECT p.type_id,p.type_name,s.type_name FROM tdb_goods_types AS p LEFT JOIN tdb_goods_types AS  s ON s.parent_id = p.type_id;

     查找所有分类及其子类的数目

     SELECT p.type_id,p.type_name,count(s.type_name) AS children_count FROM tdb_goods_types AS p LEFT JOIN tdb_goods_types AS s ON s.parent_id = p.type_id GROUP BY p.type_name ORDER BY p.type_id;

### 5-13.多表删除

1. DELETE tbl_name[.*] [,tbl_name[.\*]]...

   FROM table_references

   [WHERE where_condition]

   ep:

   * 查找重复记录

     SELECT goods_id,goods_name FROM tdb_goods GROUP BY goods_name HAVING count(goods_name) >= 2;

   * 删除重复记录

     DELETE t1 FROM tdb_goods AS t1 LEFT JOIN (SELECT goods_id,goods_name FROM tdb_goods GROUP BY goods_name HAVING count(goods_name) >= 2 ) AS t2  ON t1.goods_name = t2.goods_name  WHERE t1.goods_id > t2.goods_id;

### 6-1.字符函数

1. 







 









