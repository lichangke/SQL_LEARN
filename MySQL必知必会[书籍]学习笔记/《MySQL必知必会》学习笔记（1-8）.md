**《MySQL必知必会》学习笔记 目录**

[https://www.jianshu.com/p/97bab08c6755](https://www.jianshu.com/p/97bab08c6755)


**MySQL安装**  自行谷歌


**《MySQL 必知必会》创建样例表**

mysql_scripts.zip 下载 下面链接

链接：https://pan.baidu.com/s/1y-OZG9fbdKFP3r-XlIjeMA 
提取码：ehg5 

（1）创建一个新的数据源

```sql
create database crashcourse; （这里使用书中的数据源名称）
```

（2）选择新的数据源

```sql
use crashcourse;
```

（3）执行creat.sql脚本

```sql 
  source create.sql;   （使用mysql命令行使用程序的方法，source+文件路径 如有必要可能为绝对路径）
```

（4）用populate.sql文件填充各个新表

```sql
source populate.sql; 
```


检测样例创建是否成功

![image.png](https://upload-images.jianshu.io/upload_images/16846478-b60f0f59f299b1a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

更多可参看：
https://www.runoob.com/mysql/mysql-tutorial.html


# 第一章 了解SQL
介绍数据库和SQL，它们是学习MySQL的先决条件。

## 1.1 数据库基础

### 1.1.1 什么是数据库

数据库是一个以某种有组织的方式存储的数据集合。理解数据库的一种最简单的办法是将其想象为一个文件柜。此文件柜是一个存放数据的物理位置，不管数据是什么以及如何组织的。

**数据库（database）**
*保存有组织的数据的容器（通常是一个文件或一组文件）。*

### 1.1.2 表

表是一种结构化的文件，可用来存储某种特定类型的数据。表可以保存顾客清单、产品目录，或者其他信息清单。

**表（table）**  
*某种特定类型数据的结构化清单。*

关键的一点在于，存储在表中的数据是一种类型的数据或一个清单。

数据库中的每个表都有一个名字，用来标识自己。此名字是唯一的，这表示数据库中没有其他表具有相同的名字。

**表名** 
*表名的唯一性取决于多个因素，如数据库名和表名等的结合。这表示，虽然在相同数据库中不能两次使用相同的表名，但在不同的数据库中却可以使用相同的表名。*

**模式（schema）**
*关于数据库和表的布局及特性的信息。*

### 1.1.3 列和数据类型

表由列组成。列中存储着表中某部分的信息。

**列（column）**
*表中的一个字段。所有表都是由一个或多个列组成的*

数据库中每个列都有相应的数据类型。数据类型定义列可以存储的数据种类。

**数据类型（datatype）** 
*所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据。*

### 1.1.4 行

表中的数据是按行存储的，所保存的每个记录存储在自己的行内。

**行（row）** *表中的一个记录。*

### 1.1.5 主键

表中每一行都应该有可以唯一标识自己的一列（或一组列）。

**主键（primary key）** 
*一列（或一组列），其值能够唯一区分表中每个行。*
唯一标识表中每行的这个列（或这组列）称为主键。主键用来表示一个特定的行。没有主键，更新或删除表中特定行很困难，因为没有安全的方法保证只涉及相关的行。

## 1.2 什么是SQL

SQL（发音为字母S-Q-L或sequel）是结构化查询语言（Structured Query Language）的缩写。 SQL是一种专门用来与数据库通信的语言。

# 第 2 章MySQL简介

介绍什么是MySQL，以及在MySQL中可以应用什么工具。

## 2.1 什么是MySQL

数据的所有存储、检索、管理和处理实际上是由数据库软件——DBMS（数据库管理系统）完成的。 MySQL是一种DBMS，即它是一种数据库软件

MySQL、 Oracle以及Microsoft SQL Server等数据库是基于客户机—服务器的数据库。客户机—服务器应用分为两个不同的部分。 **服务器**部分是负责所有数据访问和处理的一个软件。这个软件运行在称为数据库服务器的计算机上。

与数据文件打交道的只有服务器软件。关于数据、数据添加、删除和数据更新的所有请求都由服务器软件完成。**客户机**是与用户打交道的软件。

## 2.2 MySQL工具

MySQL是一个客户机—服务器DBMS，因此，为了使用MySQL，需要有一个客户机，即你需要用来与MySQL打交道（给MySQL提供要执行的命令）的一个应用。

### 2.2.1 mysql命令行实用程序

在操作系统命令提示符下输入mysql -u root -p，提示输入密码

**MySQL选项和参数**

如果仅输入mysql， 可能会出现一个错误消息。mysql接受你可以（和可能需要）使用的一组命令行参数。例如，为了指定用户登录名ben，应该使用mysql -u ben。为了给出用户名、主机名、端口和口令，应该使用mysql -u ben -p -h myserver -P 9999。 mysql -u root -p 

请注意：
- 命令输入在mysql>之后；
- 命令用;或\g结束，换句话说，仅按Enter不执行命令；
- 输入help或\h获得帮助，也可以输入更多的文本获得特定命令的帮助（如，输入help select获得使用SELECT语句的帮助）；
- 输入quit或exit退出命令行实用程序。

![MySQL.png](https://upload-images.jianshu.io/upload_images/16846478-2a8d65fcd51d52b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.2.2 MySQL Administrator
MySQL Administrator（MySQL管理器）是一个图形交互客户机，用来简化MySQL服务器的管理。

获得MySQL Administrator MySQL Administrator不作为核心MySQL 的 组 成 部 分 安 装 。 必 须 从 http://dev.mysql.com/downloads/下载它

Ubuntu 中 mysql数据库路径
/var/lib/mysql           日志文件，数据库  ［重点要知道这个］

现使用MySQL Workbench 

### 2.2.3 MySQL Query Browser

MySQL Query Browser为一个图形交互客户机，用来编写和执行MySQL命令。

# 第３章 使用MySQL

学习如何连接和登录到MySQL，如何执行MySQL语句，以及如何获得数据库和表的信息。

## 3.1 连接

MySQL与所有客户机—服务器DBMS一样，要求在能执行命令之前登录到DBMS。

## 3.2 选择数据库

在你最初连接到MySQL时，没有任何数据库打开供你使用。在你能执行任意数据库操作前，需要选择一个数据库。为此，可使用USE关键字。

**关键字(key word)** 作为MySQL语言组成部分的一个保留字。决不要用关键字命名一个表或列。附录E列出了MySQL的关键字。

![USE.png](https://upload-images.jianshu.io/upload_images/16846478-579169ca4c5d7351.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3.3 了解数据库和表

内部的表一般不直接访问。可用MySQL的SHOW命令来显示这些信息（MySQL从内部表中提取这些信息）。

```sql
SHOW DATABASES;
```

![3.3DATABASES.png](https://upload-images.jianshu.io/upload_images/16846478-caa71225b2b345fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SHOW DATABASES;返回可用数据库的一个列表。包含在这个列表中的可能是MySQL内部使用的数据库

```sql
SHOW TABLES;
```

![3.3TABLES.png](https://upload-images.jianshu.io/upload_images/16846478-a733d572a4146357.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SHOW TABLES;返回当前选择的数据库内可用表的列表。

```sql
SHOW COLUMNS FROM customer;
```

![3.3COLUMNS .png](https://upload-images.jianshu.io/upload_images/16846478-a6ade5d801679fc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SHOW COLUMNS 要 求 给 出 一 个 表 名 （ 这 个 例 子 中 的 FROMcustomers），它对每个字段返回一行，行中包含字段名、数据类型、是否允许NULL、键信息、默认值以及其他信息（如字段cust_id的auto_increment）

**DESCRIBE语句** MySQL支持用DESCRIBE作为SHOW COLUMNS FROM的一种快捷方式。换句话说， DESCRIBE customers;是SHOW COLUMNS FROM customers;的一种快捷方式。

![3.3DESCRIBE.png](https://upload-images.jianshu.io/upload_images/16846478-cf1c193f1c263e5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所支持的其他SHOW语句还有：

- SHOW STATUS，用于显示广泛的服务器状态信息；

- SHOW CREATE DATABASE和SHOW CREATE TABLE，分别用来显示创建特定数据库或表的MySQL语句；

- SHOW GRANTS，用来显示授予用户（所有用户或特定用户）的安全权限；

- SHOW ERRORS和SHOW WARNINGS， 用来显示服务器错误或警告消息。

 **HELP SHOW;** 显示允许的SHOW语句。

# 第 4 章 检 索 数 据

介绍如何使用SELECT语句从表中检索一个或多个数据列。

## 4.1 SELECT语句

SQL语句是由简单的英语单词构成的。这些单词称为关键字，每个SQL语句都是由一个或多个关键字构成的。

最经常使用的SQL语句就是SELECT语句了。它的用途是从一个或多个表中检索信息

使用**SELECT**检索表数据，必须至少给出两条信息——**想选择什么**，以及**从什么地方选择**

## 4.2 检索单个列

```sql
SELECT prod_name FROM products;
```
![4.2SELECT.png](https://upload-images.jianshu.io/upload_images/16846478-849c1e7a5717a177.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.3 检索多个列

要想从一个表中检索多个列，使用相同的SELECT语句。唯一的不同是必须在SELECT关键字后给出多个列名，列名之间必须以逗号分隔。

```sql
SELECT   prod_id, prod_name, prod_price FROM products;
```

![4.3SELECT.png](https://upload-images.jianshu.io/upload_images/16846478-81c1e6e2353f29ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.4 检索所有列

除了指定所需的列外， SELECT语句还可以检索所有的列而不必逐个列出它们。这可以通过在实际列名的位置使用星号（*）通配符来达到

```sql
SELECT * FROM products;
```

![4.4SELECT.png](https://upload-images.jianshu.io/upload_images/16846478-5587ffb7f211765e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.5 检索不同的行

DISTINCT关键字指示MySQL只返回不同的值。

```sql
SELECT DISTINCT vend_id FROM products;
```

![4.5DISTINCT.png](https://upload-images.jianshu.io/upload_images/16846478-92db1d5d21087ebe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SELECT DISTINCT vend_id告诉MySQL只返回不同（唯一）的vend_id行，因此只返回4行

## 4.6 限制结果

SELECT语句返回所有匹配的行，它们可能是指定表中的每个行。为了返回第一行或前几行，可使用LIMIT子句。

```sql
SELECT prod_name FROM products LIMIT 5;
```

![4.6LIMIT.png](https://upload-images.jianshu.io/upload_images/16846478-f36910792b1b8d3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此语句使用SELECT语句检索单个列。 LIMIT 5指示MySQL返回不多于5行。

可指定要检索的开始行和行数
```sql
SELECT prod_name FROM products LIMIT 7,5;
```

![4.6LIMIT2.png](https://upload-images.jianshu.io/upload_images/16846478-9812d85e3a1dbf97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

LIMIT 7, 5指示MySQL返回从行7开始的5行。第一个数为开始位置，第二个数为要检索的行数。

**行0** 检索出来的第一行为行0而不是行1。因此， LIMIT 1, 1将检索出第二行而不是第一行。

**MySQL 5的LIMIT语法** MySQL 5支持LIMIT的另一种替代语法。 LIMIT 4 OFFSET 3意为从行3开始取4行，就像LIMIT 3, 4一样。

```sql
SELECT prod_name FROM products LIMIT 7 OFFSET 5;
```

![4.6OFFSET.png](https://upload-images.jianshu.io/upload_images/16846478-01b8eb7e97a73fd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4.7 使用完全限定的表名

可能会使用完全限定的名字来引用列（同时使用表名和列字）。

```sql
SELECT products.prod_name FROM products;
```

表名也可以是完全限定的

```sql
SELECT products.prod_name FROM crashcourse.products;
```


# 第５章 排序检索数据

如何使用SELECT语句的ORDER BY子句，根据需要排序检索出的数据

## 5.1 排序数据

**子句（clause）** SQL语句由子句构成，有些子句是必需的，而有的是可选的。一个子句通常由一个关键字和所提供的数据组成。

为了明确地排序用SELECT语句检索出的数据，可使用ORDER BY子句。ORDER BY子句取一个或多个列的名字，据此对输出进行排序。

```sql
SELECT prod_name FROM products ORDER BY prod_name;
```

## 5.2 按多个列排序

为了按多个列排序，只要指定列名，列名之间用逗号分开即可

下面的代码检索3个列，并按其中两个列对结果进行排序——首先按价格，然后再按名称排序。

```sql
SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price, prod_name;
```

![5.2ORDER.png](https://upload-images.jianshu.io/upload_images/16846478-1ccbfb180b1a6fc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5.3 指定排序方向

数据排序不限于升序排序（从A到Z）。这只是默认的排序顺序，还可以使用ORDER BY子句以降序（从Z到A）顺序排序。为了进行降序排序，必须指定DESC关键字。

```sql
SELECT prod_id, prod_price, prod_name FROM products ORDER BY prod_price DESC;
```

![5.3DESC.png](https://upload-images.jianshu.io/upload_images/16846478-099aabdf276d07b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**在多个列上降序排序** 如果想在多个列上进行降序排序， 必须对每个列指定DESC关键字。

使用ORDER BY和LIMIT的组合，能够找出一个列中最高或最低的值。

```sql
SELECT prod_price FROM products ORDER BY prod_price DESC LIMIT 1;
```

![5.3ORDER.png](https://upload-images.jianshu.io/upload_images/16846478-689ee9fbe35249bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 第６章 过 滤 数 据

讲授如何使用SELECT语句的WHERE子句指定搜索条件。

## 6.1 使用WHERE子句

数据库表一般包含大量的数据，很少需要检索表中所有行。通常只会根据特定操作或报告的需要提取表数据的子集。只检索所需数据需要指定搜索条件（ search criteria） ，搜索条件也称为过滤条件（ filtercondition） 。

在SELECT语句中，数据根据WHERE子句中指定的搜索条件进行过滤。

```sql
SELECT prod_name, prod_price FROM products WHERE prod_price = 2.50;
```

![6.1WHERE.png](https://upload-images.jianshu.io/upload_images/16846478-5ebe9df7c5c16a0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 6.2 WHERE子句操作符

**WHERE子句操作符**
| 操 作 符|    说 明 | 
|------| -----------|
|=| 等于|
|<> |不等于|
|!= |不等于|
|< |小于|
|<= |小于等于|
|>| 大于|
|>= |大于等于|
|BETWEEN |在指定的两个值之间|

### 6.2.1 检查单个值

```sql
SELECT prod_name, prod_price FROM products WHERE prod_name = 'fuses';
```

![6.2.1WHERE1.png](https://upload-images.jianshu.io/upload_images/16846478-0bf5a01b9482a4ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT prod_name, prod_price FROM products WHERE prod_price < 10;
```

![6.2.1WHERE2.png](https://upload-images.jianshu.io/upload_images/16846478-b13e6ab7cedcc86c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6.2.2 不匹配检查

```sql
SELECT vend_id, prod_name FROM products WHERE vend_id <> 1003;
```

![6.2.2WHERE1.png](https://upload-images.jianshu.io/upload_images/16846478-982ac33f9707ba9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT vend_id, prod_name FROM products WHERE vend_id != 1003;
```

![6.2.2WHERE2.png](https://upload-images.jianshu.io/upload_images/16846478-07f98c94f7319bd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6.2.3 范围值检查

为了检查某个范围的值，可使用BETWEEN操作符。其语法与其他WHERE子句的操作符稍有不同，因为它需要两个值，即范围的开始值和结束值。

```sql
SELECT prod_name, prod_price FROM products WHERE prod_price BETWEEN 5 AND 10;
```

![6.2.3BETWEEN.png](https://upload-images.jianshu.io/upload_images/16846478-7f202c93bfe8022e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 6.2.4 空值检查

在创建表时，表设计人员可以指定其中的列是否可以不包含值。在一个列不包含值时，称其为包含空值NULL。

**NULL 无值（no value）**，它与字段包含0、空字符串或仅仅包含空格不同。

SELECT语句有一个特殊的WHERE子句，可用来检查具有NULL值的列。这个WHERE子句就是IS NULL子句。

```sql
SELECT prod_name FROM products WHERE prod_price IS NULL;
```
![6.2.4WHEREISNULL1.png](https://upload-images.jianshu.io/upload_images/16846478-23f7aa7fe78cedad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT cust_id FROM customers WHERE cust_email IS NULL;
```

![6.2.4WHEREISNULL2.png](https://upload-images.jianshu.io/upload_images/16846478-185112a5a5b90038.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 第 7 章 数 据 过 滤

如何组合WHERE子句以建立功能更强的更高级的搜索条件。

## 7.1 组合WHERE子句

为了进行更强的过滤控制， MySQL允许给出多个WHERE子句。这些子句可以两种方式使用：以AND子句的方式或OR子句的方式使用。

### 7.1.1 AND操作符

为了通过不止一个列进行过滤，可使用AND操作符给WHERE子句附加条件。

```sql
SELECT prod_id, prod_price, prod_name FROM products WHERE vend_id = 1003 AND prod_price <= 10;
```

![7.1.1WHEREAND.png](https://upload-images.jianshu.io/upload_images/16846478-79c51548e2f9cb96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**AND** 用在WHERE子句中的关键字，用来指示检索满足所有给定条件的行。

### 7.1.2 OR操作符

OR操作符与AND操作符不同，它指示MySQL检索匹配任一条件的行。

```sql
SELECT prod_price, prod_name FROM products WHERE vend_id = 1002 OR vend_id = 1003;
```

![7.1.2WHEREOR.png](https://upload-images.jianshu.io/upload_images/16846478-efa2ed785c617a4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**OR** WHERE子句中使用的关键字，用来表示检索匹配任一给定条件的行。

### 7.1.3 计算次序

WHERE可包含任意数目的AND和OR操作符。允许两者结合以进行复杂和高级的过滤。
但是，组合AND和OR带来了一个有趣的问题。SQL（像多数语言一样）在处理OR操作符前，优先处理AND操作符。此问题的解决方法是使用圆括号明确地分组相应的操作符。

```sql
SELECT prod_price, prod_name FROM products WHERE (vend_id = 1002 OR vend_id = 1003) AND prod_price >= 10;
```

![7.1.3WHERE.png](https://upload-images.jianshu.io/upload_images/16846478-0a08bc1987859a95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 7.2 IN操作符

圆括号在WHERE子句中还有另外一种用法。 IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。

```sql
SELECT prod_price, prod_name FROM products WHERE vend_id IN (1002,1003) ORDER BY prod_name;
```

![7.2IN.png](https://upload-images.jianshu.io/upload_images/16846478-4fce5f645fd7a11e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为什么要使用IN操作符？其优点具体如下

- 在使用长的合法选项清单时， IN操作符的语法更清楚且更直观。
- 在使用IN时，计算的次序更容易管理（因为使用的操作符更少）。
- IN操作符一般比OR操作符清单执行更快。
- IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建立WHERE子句。第14章将对此进行详细介绍

**IN** WHERE子句中用来指定要匹配值的清单的关键字，功能与OR相当。

## 7.3 NOT操作符

WHERE子句中的NOT操作符有且只有一个功能，那就是否定它之后所跟的任何条件。

**NOT** WHERE子句中用来否定后跟条件的关键字。

```sql
SELECT prod_price, prod_name FROM products WHERE vend_id NOT IN (1002,1003) ORDER BY prod_name;
```

![7.3NOT.png](https://upload-images.jianshu.io/upload_images/16846478-235ca5d0f2c1e42f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**MySQL中的 NOT** MySQL 支 持 使 用 NOT 对 IN 、 BETWEEN 和EXISTS子句取反，这与多数其他DBMS允许使用NOT对各种条件取反有很大的差别。

# 第 8 章 用通配符进行过滤

介绍什么是通配符、如何使用通配符以及怎样使用LIKE操作符进行通配搜索，以便对数据进行复杂过滤。

## 8.1 LIKE操作符

**通配符（wildcard）** 用来匹配值的一部分的特殊字符。

**搜索模式（search pattern）** 由字面值、通配符或两者组合构成的搜索条件。

为在搜索子句中使用通配符，必须使用LIKE操作符。 LIKE指示MySQL，后跟的搜索模式利用通配符匹配而不是直接相等匹配进行比较。

### 8.1.1 百分号（%）通配符

最常使用的通配符是百分号（%）。在搜索串中， %表示任何字符出现任意次数。

```sql
SELECT prod_id, prod_name FROM products WHERE prod_name LIKE 'jet%';
```

![8.1.1LIKE.png](https://upload-images.jianshu.io/upload_images/16846478-370ced837ae661e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**区分大小写** 根据MySQL的配置方式，搜索可以是区分大小写的。

### 8.1.2 下划线（_）通配符

另一个有用的通配符是下划线（_）。下划线的用途与%一样，但下划线只匹配单个字符而不是多个字符。

```sql
SELECT prod_id, prod_name FROM products WHERE prod_name LIKE '_ ton anvil';
```

![8.1.2LIKE1.png](https://upload-images.jianshu.io/upload_images/16846478-afc01ac3fdf7fb4e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

区别：
```sql
SELECT prod_id, prod_name FROM products WHERE prod_name LIKE '% ton anvil';
```

![8.1.2LIKE 2.png](https://upload-images.jianshu.io/upload_images/16846478-d2a6add6f0736d12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 8.2 使用通配符的技巧

MySQL的通配符很有用。但这种功能是有代价的：通配符搜索的处理一般要比前面讨论的其他搜索所花时间更长。

- 不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。
- 在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。把通配符置于搜索模式的开始处，搜索起来是最慢的。
- 仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。

----
>*GitHub链接：*
>*[https://github.com/lichangke/LeetCode](https://github.com/lichangke/LeetCode)*

>*知乎个人首页：*
>*[https://www.zhihu.com/people/lichangke/](https://www.zhihu.com/people/lichangke/)*

>*简书个人首页：*
>*[https://www.jianshu.com/u/3e95c7555dc7](https://www.jianshu.com/u/3e95c7555dc7)*

>*个人Blog:*
>*[https://lichangke.github.io/](https://lichangke.github.io/)*

>*欢迎大家来一起交流学习*