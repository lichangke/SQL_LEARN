**《MySQL必知必会》学习笔记 目录**

[https://www.jianshu.com/p/97bab08c6755](https://www.jianshu.com/p/97bab08c6755)

# 第 十 九 章 插 入 数 据

如何利用SQL的INSERT语句将数据插入表中。

## 19.1 数据插入

INSERT是用来插入（或添加）行到数据库表的。插入可以用几种方式使用：

- 插入完整的行；
- 插入行的一部分；
- 插入多行；
- 插入某些查询的结果。

## 19.2 插入完整的行

把数据插入表中的最简单的方法是使用基本的INSERT语法，它要求指定表名和被插入到新行中的值。

```sql
INSERT INTO customers 
 VALUES ( NULL, 'Pep E. Lapew' , '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL, NULL);
```

![19.2INSERT 1.png](https://upload-images.jianshu.io/upload_images/16846478-b311b19d00100a8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**没有输出** INSERT语句一般不会产生输出。

虽然这种语法很简单，但并不安全，应该尽量避免使用。上面的SQL语句高度依赖于表中列的定义次序，并且还依赖于其次序容易获得的信息。

编写INSERT语句的更安全（不过更烦琐）的方法如下：

```sql
INSERT INTO customers(cust_name, cust_address , cust_city , cust_state, cust_zip, cust_country,  cust_contact, cust_email)
 VALUES (  'Pep E. Lapew' , '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA', NULL, NULL);
```

![19.2INSERT 2.png](https://upload-images.jianshu.io/upload_images/16846478-7c8b3a36e3979389.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


此例子完成与前一个INSERT语句完全相同的工作，但在表名后的括号里明确地给出了列名。在插入行时， MySQL将用VALUES列表中的相应值填入列表中的对应项。 VALUES中的第一个值对应于第一个指定的列名。第二个值对应于第二个列名，如此等等。

下面的INSERT语句填充所有列（与前面的一样），但以一种不同的次序填充。因为给出了列名，所以插入结果仍然正确：

```sql
INSERT INTO customers(cust_name,cust_contact, cust_email ,cust_address , cust_city , cust_state, cust_zip, cust_country  )
 VALUES (  'Pep E. Lapew' , NULL, NULL, '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA');
```

![19.2INSERT 3.png](https://upload-images.jianshu.io/upload_images/16846478-d6647c2e7f7da6c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**总是使用列的列表** 一般不要使用没有明确给出列的列表的INSERT语句。使用列的列表能使SQL代码继续发挥作用，即使表结构发生了变化。

## 19.3 插入多个行

可以使用多条INSERT语句，甚至一次提交它们，每条语句用一个分号结束


```sql
INSERT INTO customers(cust_name, cust_address , cust_city , cust_state, cust_zip, cust_country)
 VALUES (  'Pep E. Lapew' , '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA');
INSERT INTO customers(cust_name, cust_address , cust_city , cust_state, cust_zip, cust_country)
 VALUES (  'M. Martian' , '42 Galaxy Way', 'New York', 'NY', '11213', 'USA');
```

或者，只要每条INSERT语句中的列名（和次序）相同，可以如下组合各语句：

```sql
INSERT INTO customers(cust_name, cust_address , cust_city , cust_state, cust_zip, cust_country)
 VALUES (  'Pep E. Lapew' , '100 Main Street', 'Los Angeles', 'CA', '90046', 'USA'),
(  'M. Martian' , '42 Galaxy Way', 'New York', 'NY', '11213', 'USA');
```

## 19.4 插入检索出的数据

INSERT一般用来给表插入一个指定列值的行。但是， INSERT还存在另一种形式，可以利用它将一条SELECT语句的结果插入表中。这就是所谓的INSERT SELECT，顾名思义，它是由一条INSERT语句和一条SELECT语句组成的

```sql
INSERT INTO customers(cust_id ,cust_contact, cust_email ,cust_name ,cust_address , cust_city , cust_state, cust_zip, cust_country  )
SELECT cust_id ,cust_contact, cust_email ,cust_name ,cust_address , cust_city , cust_state, cust_zip, cust_country FROM custnew;
```

这个例子使用INSERT SELECT从custnew中将所有数据导入customers。 

这个例子导入了cust_id（假设你能够确保cust_id的值不重复）。也可以简单地省略这列（从INSERT和SELECT中），这样MySQL就会生成新值。

INSERT SELECT中SELECT语句可包含WHERE子句以过滤插入的数据。

# 第 二 十 章 更新和删除数据

如何利用UPDATE和DELETE语句进一步操纵表数据。

## 20.1 更新数据

为了更新（修改）表中的数据，可使用UPDATE语句。可采用两种方式使用UPDATE：

- 更新表中特定行
- 更新表中所有行

**不要省略WHERE子句** 在使用UPDATE时一定要注意细心。因为稍不注意，就会更新表中所有行。

UPDATE语句由3部分组成，分别是：
- 要更新的表；
- 列名和它们的新值；
- 确定要更新行的过滤条件。

客户10005现在有了电子邮件地址，因此他的记录需要更新，语句如下：

```sql
UPDATE customers SET cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;
```

![20.1UPDATE1.png](https://upload-images.jianshu.io/upload_images/16846478-67bc9a802b80fa61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

更新多个列的语法稍有不同：

```sql
UPDATE customers SET cust_name = 'The Fudds' , cust_email = 'elmer@fudd.com' WHERE cust_id = 10005;
```

![20.1UPDATE2.png](https://upload-images.jianshu.io/upload_images/16846478-de6ff0a33deebe7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了删除某个列的值，可设置它为NULL（假如表定义允许NULL值）。

```sql
UPDATE customers SET cust_email = NULL WHERE cust_id = 10005;
```

![20.1UPDATE3.png](https://upload-images.jianshu.io/upload_images/16846478-852cf7ce6768f719.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 20.2 删除数据

为了从一个表中删除（去掉）数据，使用DELETE语句。可以两种方式使用DELETE

- 从表中删除特定的行；
- 从表中删除所有行。

下面的语句从customers表中删除一行：

```sql
DELETE FROM customers WHERE cust_id = 10006;
```

![20.2DELETE1.png](https://upload-images.jianshu.io/upload_images/16846478-d145e0a7a94ea8ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这个例子中，只删除客户10006。如果省略WHERE子句，它将删除表中每个客户。

DELETE不需要列名或通配符。 DELETE删除整行而不是删除列。为了删除指定的列，请使用UPDATE语句。


如果省略了WHERE子句，则UPDATE或DELETE将被应用到表中所有的行。换句话说，如果执行UPDATE而不带WHERE子句，则表中每个行都将用新值更新。类似地，如果执行DELETE语句而不带WHERE子句，表的所有数据都将被删除。

# 第 二 十 一 章 更新和删除数据

表的创建、更改和删除的基本知识。

## 21.1 创建表

为了用程序创建表，可使用SQL的CREATE TABLE语句。

### 21.1.1 表创建基础

为利用CREATE TABLE创建表，必须给出下列信息：

- 新表的名字，在关键字CREATE TABLE之后给出；
- 表列的名字和定义，用逗号分隔。

```sql
CREATE TABLE customers
(
  cust_id                 int                NOT NULL AUTO_INCREMENT,
  cust_name          char(50)       NOT NULL,
  cust_address      char(50)       NULL,
  cust_city             char(50)       NULL,
  cust_state           char(50)       NULL,
  cust_zip              char(50)       NULL,
  cust_country       char(50)       NULL,
  cust_contact        char(50)       NULL,
  cust_email           char(255)     NULL,
  PRIMARY KEY (cust_id)
) ENGINE = InnoDB;
```

![21.1.1CREATE.png](https://upload-images.jianshu.io/upload_images/16846478-a481be4e889c5caf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


表的主键可以在创建表时用PRIMARY KEY关键字指定。

**处理现有的表** 在创建新表时，指定的表名必须不存在，否则将出错。如果要防止意外覆盖已有的表， SQL要求首先手工删除该表（请参阅后面的小节），然后再重建它，而不是简单地用创建表语句覆盖它。

### 21.1.2 使用NULL值

允许NULL值的列也允许在插入行时不给出该列的值。不允许NULL值的列不接受该列没有值的行，换句话说，在插入或更新行时，该列必须有值。

每个表列或者是NULL列，或者是NOT NULL列，这种状态在创建时由表的定义规定。

```sql
CREATE TABLE orders
(
  order_num  int NOT NULL AUTO_INCREMENT,
  order_date  datetime  NOT NULL ,
  cust_id    int    NOT NULL,
  PRIMARY KEY (order_num)
) ENGINE = InnoDB;
```

**理解NULL** 不要把NULL值与空串相混淆。 NULL值是没有值，它不是空串。如果指定''（两个单引号，其间没有字符），这在NOT NULL列中是允许的。空串是一个有效的值，它不是无值。 NULL值用关键字NULL而不是空串指定

### 21.1.3 主键再介绍

主键值必须唯一。即，表中的每个行必须具有唯一的主键值。如果主键使用单个列，则它的值必须唯一。如果使用多个列，则这些列的组合值必须唯一。为创建由多个列组成的主键，应该以逗号分隔的列表给出各列名。

**主键和NULL值** 主键为其值唯一标识表中每个行的列。主键中只能使用不允许NULL值的列。允许NULL值的列不能作为唯一标识。

### 21.1.4 使用AUTO_INCREMENT

AUTO_INCREMENT告诉MySQL，本列每当增加一行时自动增量。每次执行一个INSERT操作时， MySQL自动对该列增量（从而才有这个关键字AUTO_INCREMENT），给该列赋予下一个可用的值。这样给每个行分配一个唯一的cust_id，从而可以用作主键值。

每个表只允许一个AUTO_INCREMENT列，而且它必须被索引

### 21.1.5 指定默认值

如果在插入行时没有给出值， MySQL允许指定此时使用的默认值。默认值用CREATE TABLE语句的列定义中的DEFAULT关键字指定。

```sql
CREATE TABLE orderitems
(
  order_num  int          NOT NULL ,
  order_item int          NOT NULL ,
  prod_id    char(10)     NOT NULL ,
  quantity   int          NOT NULL DEFAULT 1,
  item_price decimal(8,2) NOT NULL ,
  PRIMARY KEY (order_num, order_item)
) ENGINE=InnoDB;
```

quantity列包含订单中每项物品的数量。在此例子中，给该列的描述添加文本DEFAULT 1指示MySQL，在未给出数量的情况下使用数量1。

### 21.1.6 引擎类型

MySQL与其他DBMS不一样，它具有多种引擎。它打包多个引擎，这些引擎都隐藏在MySQL服务器内，全都能执行CREATE TABLE和SELECT等命令。

以下是几个需要知道的引擎：

- InnoDB是一个可靠的事务处理引擎，它不支持全文本搜索；
- MEMORY在功能等同于MyISAM， 但由于数据存储在内存（不是磁盘）中，速度很快（特别适合于临时表）；
- MyISAM是一个性能极高的引擎，它支持全文本搜索，但不支持事务处理。

**外键不能跨引擎** 混用引擎类型有一个大缺陷。外键（用于强制实施引用完整性）不能跨引擎，即使用一个引擎的表不能引用具有使用不同引擎的表的外键。

## 21.2 更新表

为更新表定义，可使用ALTER TABLE语句。

为了使用ALTER TABLE更改表结构，必须给出下面的信息：

- 在ALTER TABLE之后给出要更改的表名（该表必须存在，否则将出错）；
- 所做更改的列表。

下面的例子给表添加一个列：
```sql
ALTER TABLE vendors ADD vend_phone CHAR(20);
```

删除刚刚添加的列，可以这样做：
```sql
ALTER TABLE vendors DROP COLUMN vend_phone;
```

![21.2ALTER .png](https://upload-images.jianshu.io/upload_images/16846478-6709174a2b690ffe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ALTER TABLE的一种常见用途是定义外键。

## 21.3 删除表

删除表（删除整个表而不是其内容）非常简单，使用DROP TABLE语句即可：

```sql
DROP TABLE customers3;
```

## 21.4 重命名表

使用RENAME TABLE语句可以重命名一个表：
```sql
RENAME TABLE customers2 TO customers3;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-f7ad235bca2e915d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


# 第 二 十 二 章 使 用 视 图
介绍视图究竟是什么，它们怎样工作，何时使用它们。我们还将看到如何利用视图简化前面章节中执行的某些SQL操作。

## 22.1 视图

视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。

理解视图的最好方法是看一个例子。下面的SELECT语句从3个表中检索数据：

```sql
SELECT cust_name, cust_contact FROM customers, orders, orderitems 
WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num AND prod_id = 'TNT2';
```

此查询用来检索订购了某个特定产品的客户。任何需要这个数据的人都必须理解相关表的结构，并且知道如何创建查询和对表进行联结。为了检索其他产品（或多个产品）的相同数据，必须修改最后的WHERE子句。
现在，假如可以把整个查询包装成一个名为productcustomers的虚拟表，则可以如下轻松地检索出相同的数据：
*SELECT cust_name, cust_contact FROM productcustomers WHERE prod_id = 'TNT2';*
productcustomers是一个视图，作为视图，它不包含表中应该有的任何列或数据，它包含的是一个SQL查询（与上面用以正确联结表的相同的查询）。

### 22.1.1 为什么使用视图

- 重用SQL语句。
- 简化复杂的SQL操作。在编写查询后，可以方便地重用它而不必知道它的基本查询细节。
- 使用表的组成部分而不是整个表。
- 保护数据。可以给用户授予表的特定部分的访问权限而不是整个表的访问权限。
- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据

重要的是知道视图仅仅是用来查看存储在别处的数据的一种设施。视图本身不包含数据，因此它们返回的数据是从其他表中检索出来的。

### 22.1.2 视图的规则和限制

关于视图创建和使用的一些最常见的规则和限制。

- 与表一样，视图必须唯一命名
- 对于可以创建的视图数目没有限制。
- 为了创建视图，必须具有足够的访问权限。
- 视图可以嵌套，即可以利用从其他视图中检索数据的查询来构造一个视图。
- ORDER BY可以用在视图中，但如果从该视图检索数据SELECT中也含有ORDER BY，那么该视图中的ORDER BY将被覆盖。
- 视图不能索引，也不能有关联的触发器或默认值
- 视图可以和表一起使用。

## 22.2 使用视图

视图的创建
- 视图用CREATE VIEW语句来创建
- 使用SHOW CREATE VIEW viewname；来查看创建视图的语句。
- 用DROP删除视图，其语法为DROP VIEW viewname;。
- 更新视图时，可以先用DROP再用CREATE，也可以直接用CREATE OR REPLACE VIEW。

### 22.2.1 利用视图简化复杂的联结

视图的最常见的应用之一是隐藏复杂的SQL，这通常都会涉及联结。

```sql
CREATE VIEW productcustomers AS SELECT cust_name, cust_contact, prod_id 
FROM customers, orders, orderitems 
WHERE customers.cust_id = orders.cust_id AND orderitems.order_num = orders.order_num;
```


![22.2.1VIEW1 ](https://upload-images.jianshu.io/upload_images/16846478-53dbc2094a9e262e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这条语句创建一个名为productcustomers的视图， 它联结三个表，以返回已订购了任意产品的所有客户的列表。
为检索订购了产品TNT2的客户，可如下进行

```sql
SELECT cust_name, cust_contact FROM productcustomers WHERE prod_id = 'TNT2';
```

![22.2.1VIEW2.png](https://upload-images.jianshu.io/upload_images/16846478-1dff43e66ddc101d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 22.2.2 用视图重新格式化检索出的数据

视图的另一常见用途是重新格式化检索出的数据。

```sql
SELECT Concat( RTrim(vend_name), '(', RTrim(vend_country), ')' ) AS vend_title 
FROM vendors ORDER BY vend_name;
```

![22.2.2VIEW1.png](https://upload-images.jianshu.io/upload_images/16846478-d0ada12750012dc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

假如经常需要这个格式的结果。不必在每次需要时执行联结，创建一个视图，每次需要时使用它即可。
```sql
CREATE VIEW vendorlocations AS SELECT Concat( RTrim(vend_name), '(', RTrim(vend_country), ')' ) AS vend_title
FROM vendors ORDER BY vend_name;
```

```sql
SELECT * FROM vendorlocations;
```

![22.2.2VIEW2.png](https://upload-images.jianshu.io/upload_images/16846478-5deb0c9e0b22052c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 22.2.3 用视图过滤不想要的数据

视 图 对 于 应 用 普 通 的 WHERE 子 句 也 很 有 用 。

```sql
CREATE VIEW customeremaillist AS SELECT cust_id, cust_name, cust_email 
FROM customers WHERE cust_email IS NOT NULL;
```

这里的WHERE子句过滤了cust_email列中具有NULL值的那些行，使他们不被检索出来。

```sql
SELECT * FROM customeremaillist;
```

![22.2.3 VIEW.png](https://upload-images.jianshu.io/upload_images/16846478-79e980a71b4838ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 22.2.4 使用视图与计算字段

视图对于简化计算字段的使用特别有用。

```sql
SELECT prod_id, quantity, item_price, quantity*item_price AS expanded_price 
FROM orderitems WHERE order_num = 20005;
```

![22.2.4VIEW.png](https://upload-images.jianshu.io/upload_images/16846478-d6d036452cc73e26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将其转换为一个视图

```sql
CREATE VIEW orderitemsexpanded AS 
SELECT order_num, prod_id, quantity, item_price, quantity*item_price AS expanded_price 
FROM orderitems;
```

为检索订单20005的详细内容

```sql
SELECT * FROM orderitemsexpanded  WHERE order_num = 20005;
```

### 22.2.5 更新视图

迄今为止的所有视图都是和SELECT语句使用的。然而，视图的数据能否更新？答案视情况而定。
基本上可以说，如果MySQL不能正确地确定被更新的基数据，则不允许更新（包括插入和删除）。这实际上意味着，如果视图定义中有以下操作，则不能进行视图的更新：

- 分组（使用GROUP BY和HAVING）；
- 联结；
- 子查询；
- 并；
- 聚集函数（Min()、 Count()、 Sum()等）；
- DISTINCT；
- 导出（计算）列。

# 第 二 十 三 章  使用存储过程
 
介绍什么是存储过程，为什么要使用存储过程以及如何使用存储过程，并且介绍创建和使用存储过程的基本语法。

## 23.1 存储过程

迄今为止，使用的大多数SQL语句都是针对一个或多个表的单条语句。并非所有操作都这么简单，经常会有一个完整的操作需要多条语句才能完成。


存储过程简单来说，就是为以后的使用而保存的一条或多条MySQL语句的集合。可将其视为批文件，虽然它们的作用不仅限于批处理。

## 23.2 为什么要使用存储过程

## 23.3 使用存储过程

### 23.3.1 执行存储过程

MySQL称存储过程的执行为调用，因此MySQL执行存储过程的语句为CALL。 CALL接受存储过程的名字以及需要传递给它的任意参数。

```sql
CALL productpricing ( @pricelow, @pricehigh, @priceaverage);
```

执行名为productpricing的存储过程，它计算并返回产品的最低、最高和平均价格。

### 23.3.2 创建存储过程

一个例子——一个返回产品平均价格的存储过程

```sql
CREATE PROCEDURE productpricing() 
BEGIN
    SELECT Avg(prod_price) AS priceaverage  FROM products;
END;
```
直接这样会报错
此存储过程名为productpricing，用CREATE PROCEDURE productpricing()语句定义。如果存储过程接受参数，它们将在()中列举出来。BEGIN和END语句用来限定存储过程体，过程体本身仅是一个简单的SELECT语句.

**mysql命令行客户机的分隔符** 默认的MySQL语句分隔符为;mysql命令行实用程序也使用;作为语句分隔符。如果命令行实用程序要解释存储过程自身内的;字符，则它们最终不会成为存储过程的成分，这会使存储过程中的SQL出现句法错误。
解决办法是临时更改命令行实用程序的语句分隔符，如下所示：
```sql
DELIMITER //
CREATE PROCEDURE productpricing() 
BEGIN
    SELECT Avg(prod_price) AS priceaverage  FROM products;
END //
DELIMITER ;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-1eb2804c9260cc20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中， DELIMITER //告诉命令行实用程序使用//作为新的语句结束分隔符，可以看到标志存储过程结束的END定义为END//而不是END;。这样，存储过程体内的;仍然保持不动，并且正确地传递给数据库引擎。最后，为恢复为原来的语句分隔符，可使用DELIMITER ;

使用这个存储过程
```sql
CALL productpricing() ;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-b48b32f844e3080b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 23.3.3 删除存储过程

存储过程在创建之后，被保存在服务器上以供使用，直至被删除。

为删除刚创建的存储过程，可使用以下语句：

```sql
DROP PROCEDURE productpricing;
```

### 23.3.4 使用参数

以下是productpricing的修改版本

```sql
CREATE PROCEDURE productpricing(
  OUT pl DECIMAL(8,2),  
  OUT ph DECIMAL(8,2),  
  OUT pa DECIMAL(8,2)
) 
BEGIN
    SELECT Min(prod_price) 
    INTO pl
    FROM products;
     SELECT MAX(prod_price) 
    INTO ph
    FROM products;
     SELECT Avg(prod_price) 
    INTO pa
    FROM products;
END;
```

同样可能需要DELIMITER 改变为如下

```sql
DELIMITER //
CREATE PROCEDURE productpricing(
  OUT pl DECIMAL(8,2),  
  OUT ph DECIMAL(8,2),  
  OUT pa DECIMAL(8,2)
) 
BEGIN
    SELECT Min(prod_price) 
    INTO pl
    FROM products;
     SELECT MAX(prod_price) 
    INTO ph
    FROM products;
     SELECT Avg(prod_price) 
    INTO pa
    FROM products;
END//

DELIMITER ;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-33273af52f0f6ac2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
CALL productpricing ( @pricelow, @pricehigh, @priceaverage);
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-d13403d137ffef97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这条CALL语句给出3个参数。它们是存储过程将保存结果的3个变量的名字。

变量名 所有MySQL变量都必须以@开始。

```sql
SELECT @priceaverage;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-a73e6388d9f8075c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT @pricelow, @pricehigh, @priceaverage;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-3acd822b9aedbdc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 23.3.5 建立智能存储过程

```sql
CREATE PROCEDURE ordertotal(
  IN onumber INT,
  IN taxable BOOLEAN,
  OUT ototal DECIMAL(8,2)
) COMMENT 'Obtain order total, optionally adding tax'
BEGIN
  DECLARE total DECIMAL(8,2);
  DECLARE taxrate INT  DEFAULT 6;
  SELECT Sum(item_price*quantity)
  FROM orderitems WHERE order_num = onumber 
  INTO total;
  IF taxable THEN
      SELECT total+(total/100*taxrate) INTO total;
  END IF;
  SELECT total INTO ototal;
END;
```

注意使用 DELIMITER // 转
```sql
DELIMITER //
CREATE PROCEDURE ordertotal(
  IN onumber INT,
  IN taxable BOOLEAN,
  OUT ototal DECIMAL(8,2)
) COMMENT 'Obtain order total, optionally adding tax'
BEGIN
  DECLARE total DECIMAL(8,2);
  DECLARE taxrate INT  DEFAULT 6;
  SELECT Sum(item_price*quantity)
  FROM orderitems WHERE order_num = onumber 
  INTO total;
  IF taxable THEN
      SELECT total+(total/100*taxrate) INTO total;
  END IF;
  SELECT total INTO ototal;
END//
DELIMITER ;
```
注意 DELIMITER 与 ;之间的空格

![image.png](https://upload-images.jianshu.io/upload_images/16846478-a8637aba4b34a653.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


添加了另外一个参数taxable，它是一个布尔值（如果要增加税则为真，否则为假）。在存储过程体中，用DECLARE语句定义了两个局部变量。 DECLARE要求指定变量名和数据类型，它也支持可选的默认值（这个例子中的taxrate的默认被设置为6%）。 SELECT语句已经改变，因此其结果存储到total（局部变量）而不是ototal。 IF语句检查taxable是否为真，如果为真，则用另一SELECT语句增加营业税到局部变量total。最后，用另一SELECT语句将total（它增加或许不增加营业税）保存到ototal。

**COMMENT关键字** 本例子中的存储过程在CREATE PROCEDURE语句中包含了一个COMMENT值。它不是必需的，但如果给出，将在SHOW PROCEDURE STATUS的结果中显示。

```sql
CALL ordertotal(20005, 0 , @total);
SELECT @total;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-ec1897f2f91428a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
CALL ordertotal(20005, 1 , @total);
SELECT @total;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-99f9e5a509c477ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**IF语句** 这个例子给出了MySQL的IF语句的基本用法。 IF语句还支持ELSEIF和ELSE子句

### 23.3.6 检查存储过程

为显示用来创建一个存储过程的CREATE语句，使用SHOW CREATEPROCEDURE语句：

```sql
SHOW CREATE PROCEDURE ordertotal;
```
为了获得包括何时、由谁创建等详细信息的存储过程列表， 使用SHOW PROCEDURE STATUS。

**限制过程状态结果** SHOW PROCEDURE STATUS列出所有存储过程。为限制其输出，可使用LIKE指定一个过滤模式，例如：
```sql
SHOW PROCEDURE STATUS LIKE 'ordertotal';
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-3c34bb272b3f11ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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


