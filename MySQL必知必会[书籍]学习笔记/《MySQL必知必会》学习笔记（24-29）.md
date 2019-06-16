**《MySQL必知必会》学习笔记 目录**

[https://www.jianshu.com/p/97bab08c6755](https://www.jianshu.com/p/97bab08c6755)

# 第 二 十 四 章 使 用 游 标

讲授什么是游标以及如何使用游标

## 24.1 游标

有时，需要在检索出来的行中前进或后退一行或多行。这就是使用游标的原因。

游标（cursor） 是一个存储在MySQL服务器上的数据库查询，它不是一条SELECT语句，而是被该语句检索出来的结果集。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据。


## 24.2 使用游标

使用游标涉及几个明确的步骤

- 在能够使用游标前，必须声明（定义）它。这个过程实际上没有检索数据，它只是定义要使用的SELECT语句。
- 一旦声明后，必须打开游标以供使用。这个过程用前面定义的SELECT语句把数据实际检索出来。
- 对于填有数据的游标，根据需要取出（检索）各行。
- 在结束游标使用时，必须关闭游标。

### 24.2.1 创建游标

游标用DECLARE语句创建。DECLARE命名游标，并定义相应的SELECT语句，根据需要带WHERE和其他子句。

```sql
CREATE PROCEDURE processorders()
  BEGIN
    DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;
  END;*
注意使用 DELIMITER // 切换改变输入结束符为//，不然 ； 会被解析为结束符导致命令失败

DELIMITER //
CREATE PROCEDURE processorders()
  BEGIN
    DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;
  END//
DELIMITER ;
```

定义了名为ordernumbers的游标，使用了可以检索所有订单的SELECT语句



### 24.2.2 打开和关闭游标

```sql
OPEN  ordernumbers;
```
在处理OPEN语句时执行查询，存储检索出的数据以供浏览和滚动。

游标处理完成后，应当使用如下语句关闭游标：

```sql
CLOSE ordernumbers;
```

CLOSE释放游标使用的所有内部内存和资源，因此在每个游标不再需要时都应该关闭。

**隐含关闭** 如果你不明确关闭游标， MySQL将会在到达END语句时自动关闭它。

```sql
CREATE PROCEDURE processorders()
  BEGIN
    -- Declare the cursor
    DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;
    -- Open the cursor
    OPEN ordernumbers;
    -- Close the cursor
    CLOSE ordernumbers;
  END;
```
注意使用 DELIMITER // 切换改变输入结束符为//

```sql
DELIMITER //
CREATE PROCEDURE processorders()
  BEGIN
    -- Declare the cursor
    DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;
    -- Open the cursor
    OPEN ordernumbers;
    -- Close the cursor
    CLOSE ordernumbers;
  END//
DELIMITER ;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-5f96d93b02ecc922.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个存储过程声明、打开和关闭一个游标。但对检索出的数据什么也没做。

### 24.2.3 使用游标数据

在一个游标被打开后，可以使用FETCH语句分别访问它的每一行。

从游标中检索单个行（第一行）：

```sql
DELIMITER //
CREATE PROCEDURE processorders()
  BEGIN
    -- Declare local variables
    DECLARE o INT;
    -- Declare the cursor
    DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;
    -- Open the cursor
    OPEN ordernumbers;
    -- Get order number
    FETCH ordernumbers INTO o;
    -- Close the cursor
    CLOSE ordernumbers;
  END//
DELIMITER ;
```

其中FETCH用来检索当前行的order_num列（将自动从第一行开始）到一个名为o的局部声明的变量中。对检索出的数据不做任何处理。

![image.png](https://upload-images.jianshu.io/upload_images/16846478-852a9ed737e0dbb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

循环检索数据，从第一行到最后一行：

```sql
DELIMITER //
CREATE PROCEDURE processorders()
  BEGIN
    -- Declare local variables
    DECLARE done BOOLEAN DEFAULT 0;
    DECLARE o INT;
    -- Declare the cursor
    DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;

    -- Declare continue handler
    DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;

    -- Open the cursor
    OPEN ordernumbers;

    -- Loop through all rows
    REPEAT
       
    -- Get order number
    FETCH ordernumbers INTO o;
    -- End of loop
    UNTIL done END REPEAT;

    -- Close the cursor
    CLOSE ordernumbers;
  END//
DELIMITER ;
```
与前一个例子一样，这个例子使用FETCH检索当前order_num到声明的名为o的变量中。但与前一个例子不一样的是，这个例子中的FETCH是在REPEAT内，因此它反复执行直到done为真（由UNTILdone END REPEAT;规定）。为使它起作用，用一个DEFAULT 0（假，不结束）定义变量done。那么， done怎样才能在结束时被设置为真呢？答案是用以下语句：

```sql
DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;
```
这条语句定义了一个CONTINUE HANDLER，它是在条件出现时被执行的代码。这里， 它指出当SQLSTATE '02000'出现时， SET done=1。SQLSTATE'02000'是一个未找到条件， 当REPEAT由于没有更多的行供循环而不能继续时，出现这个条件。

![image.png](https://upload-images.jianshu.io/upload_images/16846478-2fad7e9df3f9c9c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


为了把这些内容组织起来，下面给出我们的游标存储过程样例的更进一步修改的版本，这次对取出的数据进行某种实际的处理

```sql
DELIMITER //
CREATE PROCEDURE processorders()
  BEGIN

    -- Declare local variables
    DECLARE done BOOLEAN DEFAULT 0;
    DECLARE o INT;
    DECLARE t DECIMAL(8,2);

    -- Declare the cursor
    DECLARE ordernumbers CURSOR FOR SELECT order_num FROM orders;

    -- Declare continue handler
    DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;

    -- Create a table to store the results
    CREATE TABLE IF NOT EXISTS ordertotals (order_num INT , total DECIMAL(8,2));

    -- Open the cursor
    OPEN ordernumbers;

    -- Loop through all rows
    REPEAT
       
      -- Get order number
      FETCH ordernumbers INTO o;
  
      -- Get the total for this order
      CALL ordertotal(o,1,t);
  
      -- Insert order and total into ordertotals
      INSERT INTO ordertotals(order_num, total) VALUES(o,t);

    -- End of loop
    UNTIL done END REPEAT;

    -- Close the cursor
    CLOSE ordernumbers;
  END//
DELIMITER ;
```

增加了另一个名为t的变量（存储每个订单的合计）。
此存储过程还在运行中创建了一个新表（如果它不存在的话），名为ordertotals,这个表将保存存储过程生成的结果。
FETCH像以前一样取每个order_num，然后用CALL执行另一个存储过程（我们在前一章中创建）来计算每个订单的带税的合计（结果存储到t）。最后，用INSERT保存每个订单的订单号和合计。
![image.png](https://upload-images.jianshu.io/upload_images/16846478-bf1ae44065f002f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查询上述命令的结果
```sql
SELECT * FROM ordertotals;
```
# 第 二 十 五 章 使用触发器

什么是触发器，为什么要使用触发器以及如何使用触发器

## 25.1 触发器

触发器是MySQL响应以下任意语句而自动执行的一条MySQL语句（或位于BEGIN和END语句之间的一组语句）：

- DELETE；
- INSERT；
- UPDATE。
其他MySQL语句不支持触发器。

## 25.2 创建触发器

在创建触发器时，需要给出4条信息：
- 唯一的触发器名；
- 触发器关联的表；
- 触发器应该响应的活动（DELETE、 INSERT或UPDATE）；
- 触发器何时执行（处理之前或之后）。

触发器用CREATE TRIGGER语句创建

```sql
CREATE TRIGGER newproduct AFTER INSERT ON products
FOR EACH ROW SELECT 'Product added';
```
CREATE TRIGGER用来创建名为newproduct的新触发器。触发器可在一个操作发生之前或之后执行，这里给出了AFTER INSERT，所以此触发器将在INSERT语句成功执行后执行。
这个触发器还指定FOR EACH ROW，因此代码对每个插入行执行。

触发器按每个表每个事件每次地定义，每个表每个事件每次只允许一个触发器。因此，每个表最多支持6个触发器（每条INSERT、 UPDATE和DELETE的之前和之后）。单一触发器不能与多个事件或多个表关联，所以，如果你需要一个对INSERT和UPDATE操作执行的触发器，则应该定义两个触发器。

## 25.3 删除触发器

为了删除一个触发器，可使用DROP TRIGGER语句

```sql
DROP TRIGGER newproduct;
```
触发器不能更新或覆盖。为了修改一个触发器，必须先删除它，然后再重新创建。

## 25.4 使用触发器

### 25.4.1 INSERT触发器

INSERT触发器在INSERT语句执行之前或之后执行。需要知道以下几点：

- 在INSERT触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行；
- 在BEFORE INSERT触发器中， NEW中的值也可以被更新（允许更改被插入的值）；
- 对于AUTO_INCREMENT列， NEW在INSERT执行之前包含0，在INSERT执行之后包含新的自动生成值。

```sql
CREATE TRIGGER neworder AFTER INSERT ON orders 
FOR EACH ROW SELECT NEW.order_num;
```
创建一个名为neworder的触发器，它按照AFTER INSERT ON orders执行。在插入一个新订单到orders表时， MySQL生成一个新订单号并保存到order_num中。触发器从NEW. order_num取得这个值并返回它。此触发器必须按照AFTER INSERT执行，因为在BEFOREINSERT语句执行之前，新order_num还没有生成。对于orders的每次插入使用这个触发器将总是返回新的订单号。

![image.png](https://upload-images.jianshu.io/upload_images/16846478-8144b5bc84d126d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Mysql版本差异：
https://blog.csdn.net/chenmoshashou2/article/details/80461213

### 25.4.2 DELETE触发器

DELETE触发器在DELETE语句执行之前或之后执行。需要知道以下两点：
- 在DELETE触发器代码内，你可以引用一个名为OLD的虚拟表，访问被删除的行；
- OLD中的值全都是只读的，不能更新。
```sql
CREATE TRIGGER deleteorder BEFORE DELECTE ON orders 
FOR EACH ROW
BEGIN
  INSERT INTO archive_orders(order_num,order_date,cust_id)
  VALUES(OLD.order_num,OLD.order_date,OLD_cust_id);
END;
```

### 25.4.3 UPDATE触发器

UPDATE触发器在UPDATE语句执行之前或之后执行。需要知道以下几点：

- 在UPDATE触发器代码中，你可以引用一个名为OLD的虚拟表访问以前（UPDATE语句前）的值，引用一个名为NEW的虚拟表访问新更新的值；
- 在BEFORE UPDATE触发器中， NEW中的值可能也被更新（允许更改将要用于UPDATE语句中的值）；
- OLD中的值全都是只读的，不能更新。

# 第 二 十 六 章 管理事务处理

介绍什么是事务处理以及如何利用COMMIT和ROLLBACK语句来管理事务处理。

## 26.1 事务处理

事务处理（transaction processing）可以用来维护数据库的完整性，它保证成批的MySQL操作要么完全执行，要么完全不执行，以保证数据库不包含不完整的操作结果。

利用事务处理，可以保证一组操作不会中途停止，它们或者作为整体执行，或者完全不执行（除非明确指示）。如果没有错误发生，整组语句提交给（写到）数据库表。如果发生错误，则进行回退（撤销）以恢复数据库到某个已知且安全的状态。

关于事务处理需要知道的几个术语：

- 事务（transaction）指一组SQL语句；
- 回退（rollback）指撤销指定SQL语句的过程；
- 提交（commit）指将未存储的SQL语句结果写入数据库表；
- 保留点（savepoint）指事务处理中设置的临时占位符（placeholder），你可以对它发布回退（与回退整个事务处理不同）。

## 26.2 控制事务处理

管理事务处理的关键在于将SQL语句组分解为逻辑块，并明确规定数据何时应该回退，何时不应该回退。

标识事务的开始：
```sql
START TRANSACION
```
### 26.2.1 使用ROLLBACK

ROLLBACK命令用来回退（撤销） MySQL语句
```sql
SELECT * FROM ordertotals;
START TRANSATION;
DELETE FROM ordertotal;
SELECT * FROM ordertotals;
ROLLBACK;
SELECT * FROM ordertotals;
```
ROLLBACK只能在一个事务处理内使用（在执行一条STARTTRANSACTION命令之后）。

**哪些语句可以回退？**
事务处理用来管理INSERT、 UPDATE和DELETE语句。你不能回退SELECT语句。你不能回退CREATE或DROP操作。事务处理块中可以使用这两条语句，但如果你执行回退，它们不会被撤销。

### 26.2.2 使用COMMIT

在事务处理块中，提交不会隐含地进行。为进行明确的提交，使用COMMIT语句

```sql
START TRANSATION;
DELETE FROM orderitems WHERE order_num = 20010;
DELETE FROM orders WHERE prder_num = 20010;
COMMIT;
```
最后的COMMIT语句仅在不出错时写出更改。如果第一条DELETE起作用，但第二条失败，则DELETE不会提交,实际上它是被自动撤销的。

**隐含事务关闭** 当COMMIT或ROLLBACK语句执行后，事务会自动关闭

### 26.2.3 使用保留点

复杂的事务处理可能需要部分提交或回退，为了支持回退部分事务处理，必须能在事务处理块中合适的位置放置占位符。这些占位符称为保留点。

```sql
SAVEPOINT delete1;
```
每个保留点都取标识它的唯一名字，以便在回退时

回退到delete1保留点

```sql
ROLLBACK TO delete1;
```
**释放保留点** 保留点在事务处理完成（执行一条ROLLBACK或COMMIT）后自动释放。自MySQL 5以来，也可以用RELEASESAVEPOINT明确地释放保留点。

### 26.2.4 更改默认的提交行为
默认的MySQL行为是自动提交所有更改。任何时候你执行一条MySQL语句，该语句实际上都是针对表执行的，而且所做的更改立即生效。

为指示MySQL不自动提交更改
```sql
SET autocommit = 0;
```
autocommit标志决定是否自动提交更改，不管有没有COMMIT语句。设置autocommit为0（假）指示MySQL不自动提交更改（直到autocommit被设置为真为止）。

**标志为连接专用** autocommit标志是针对每个连接而不是服务器的。

# 第 二十七 章 全球化和本地化

介绍MySQL处理不同字符集和语言的基础知识

## 27.1 字符集和校对顺序

数据库表被用来存储和检索数据。不同的语言和字符集需要以不同的方式存储和检索。因此， MySQL需要适应不同的字符集（不同的字母和字符），适应不同的排序和检索数据的方法

- **字符集**为字母和符号的集合；
- **编码**为某个字符集成员的内部表示；
- **校对**为规定字符如何比较的指令。

## 27.2 使用字符集和校对顺序

MySQL支持众多的字符集。为查看所支持的字符集完整列表，使用

```sql
SHOW CHARACTER SET;
```
![27.2SET.png](https://upload-images.jianshu.io/upload_images/16846478-f015dd2bba8dfed1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查看所支持校对的完整列表
```sql
SHOW COLLATION;
```
此语句显示所有可用的校对，以及它们适用的字符集。

为了确定所用的字符集和校对，可以使用以下语句：
```sql
SHOW VARIABLES LIKE 'character%';
SHOW VARIABLES LIKE 'collation%';
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-c7fb321d542b6b2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


常系统管理在安装时定义一个默认的字符集和校对。此外，也可以在创建数据库时，指定默认的字符集和校对。

为了给表指定字符集和校对，可使用带子句的CREATE TABLE

```sql
CREATE TABLE mytable
(
  columnn1 INT,
  columnn2 VARCHAR(10)
)DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;
```

除了能指定字符集和校对的表范围外， MySQL还允许对每个列设置它们

```sql
CREATE TABLE mytable
(
  columnn1 INT,
  columnn2 VARCHAR(10),
  columnn3 VARCHAR(10)  CHARACTER SET latin1 COLLATE latin1_general_ci
)DEFAULT CHARACTER SET hebrew COLLATE hebrew_general_ci;
```

如果你需要用与创建表时不同的校对顺序排序特定的SELECT语句，可以在SELECT语句自身中进行

```sql
SELECT * FROM customers
ORDER BY lastname, firstname COLLATE latin1_general_cs;
```
# 第 二 十 八 章 安 全 管 理

MySQL的访问控制和用户管理。

## 28.1 访问控制

MySQL服务器的安全基础是： 用户应该对他们需要的数据具有适当的访问权，既不能多也不能少。换句话说，用户不能对过多的数据具有过多的访问权。

访问控制的目的不仅仅是防止用户的恶意企图。数据梦魇更为常见的是无意识错误的结果，如错打MySQL语句，在不合适的数据库中操作或其他一些用户错误。

## 28.2 管理用户

MySQL用户账号和信息存储在名为mysql的MySQL数据库中。一般不需要直接访问mysql数据库和表，但有时需要直接访问。需要直接访问它的时机之一是在需要获得所有用户账号列表时。

```sql
USE mysql
SELECT user FROM user;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-3777c3e62854cce9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 28.2.1 创建用户账号 

了创建一个新用户账号，使用CREATE USER语句

```sql
CREATE USER ben IDENTIFIED BY 'p@$$w0rd';
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-ec6657ae9253c8e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

重新命名一个用户账号，使用RENAME USER语句

```sql
RENAME USER ben to bforta;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-eb0b5b5416394981.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 28.2.2 删除用户账号

删除一个用户账号（以及相关的权限），使用DROP USER语句

```sql
DROP USER bforta;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-519e7bdf944e693e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 28.2.3 设置访问权限

在创建用户账号后，必须接着分配访问权限。新创建的用户账号没有访问权限。它们能登录MySQL，但不能看到数据，不能执行任何数据库操作。

为看到赋予用户账号的权限，使用SHOW GRANTS FOR

```sql
SHOW GRANTS FOR bforta;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-0b81fefa6597ea28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为设置权限，使用GRANT语句,要求你至少给出以下信息：

- 要授予的权限；
- 被授予访问权限的数据库或表；
- 用户名。

```sql
GRANT SELECT ON crashcourse.* TO bforta;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-795bdb94e52e4943.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


此GRANT允许用户在crashcourse.*（crashcourse数据库的所有表）上使用SELECT。通过只授予SELECT访问权限，用户bforta对crashcourse数据库中的所有数据具有只读访问权限。

GRANT的反操作为REVOKE，用它来撤销特定的权限。

```sql
REVOKE SELECT ON crashcourse.* FROM bforta;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-9e20cd7314fd12e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

GRANT和REVOKE可在几个层次上控制访问权限：

- 整个服务器，使用GRANT ALL和REVOKE ALL；
- 整个数据库，使用ON database.*； *例子中就是这个*
- 特定的表，使用ON database.table；
- 特定的列；
- 特定的存储过程

**权限**

|权 限 | 说 明 |
|--------|--------|
|ALL |除GRANT OPTION外的所有权限|
|ALTER| 使用ALTER TABLE|
|ALTER ROUTINE |使用ALTER PROCEDURE和DROP PROCEDURE|
|CREATE |使用CREATE TABLE|
|CREATE ROUTINE | 使用CREATE PROCEDURE|
|CREATE TEMPORARY TABLES |使用CREATE TEMPORARY TABLE|
|CREATE USER |使用CREATE USER、 DROP USER、 RENAME USER和REVOK ALL PRIVILEGES|
|CREATE VIEW |使用CREATE VIEW|
|DELETE |使用DELETE|
|DROP| 使用DROP TABLE|
|EXECUTE |使用CALL和存储过程|
|FILE| 使用SELECT INTO OUTFILE和LOAD DATA INFILE|
|GRANT |OPTION 使用GRANT和REVOKE|
|INDEX |使用CREATE INDEX和DROP INDEX|
|INSERT |使用INSERT|
|LOCK |TABLES 使用LOCK TABLES|
|PROCESS| 使用SHOW FULL PROCESSLIST|
|RELOAD |使用FLUSH|
|REPLICATION CLIENT |服务器位置的访问|
|REPLICATION SLAVE |由复制从属使用|
|SELECT |使用SELECT|
|SHOW DATABASES |使用SHOW DATABASES|
|SHOW VIEW |使用SHOW CREATE VIEW|
|SHUTDOWN |使用mysqladmin shutdown（用来关闭MySQL）|
|SUPER |使用CHANGE MASTER、 KILL、 LOGS、 PURGE、 MASTER和SET GLOBAL。还允许mysqladmin调试登录|
|UPDATE |使用UPDATE|
|USAGE |无访问权限|

**简化多次授权** 可通过列出各权限并用逗号分隔，将多条GRANT语句串在一起，如下所示：
```sql
GRANT SELECT , INSERT ON crashcourse.* TO bforta;
```
### 28.2.4 更改口令

为了更改用户口令，可使用SET PASSWORD语句。

```sql
SET PASSWORD FOR bforta = Password('n3wp@$$w0rd');
```
SET PASSWORD更新用户口令。新口令必须传递到Password()函数进行加密。

SET PASSWORD还可以用来设置你自己的口令

```sql
SET PASSWORD  = Password('n3wp@$$w0rd');
```
在不指定用户名时， SET PASSWORD更新当前登录用户的口令。

# 第 二 十 九 章 数据库维护

如何进行常见的数据库维护。

## 29.1 备份数据

- 使用命令行实用程序mysqldump转储所有数据库内容到某个外部文件。
- 可用命令行实用程序mysqlhotcopy从一个数据库复制所有数据（并非所有数据库引擎都支持这个实用程序）。
- 可以使用MySQL的BACKUP TABLE或SELECT INTO OUTFILE转储所有数据到某个外部文件。

**首先刷新未写数据** 为了保证所有数据被写到磁盘（包括索引数据），可能需要在进行备份前使用FLUSH TABLES语句。

## 29.2 进行数据库维护

- ANALYZE TABLE，用来检查表键是否正确。
```sql
ANALYZE TABLE orders;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-6f04de7516c0dd10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- CHECK TABLE用来针对许多问题对表进行检查。

```sql
CHECK TABLE orders, orderitems;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-7a94cc6d0451c732.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 29.3 诊断启动问题

在排除系统启动问题时，首先应该尽量用手动启动服务器。 MySQL服务器自身通过在命令行上执行mysqld启动。下面是几个重要的mysqld命令行选项：

- --help显示帮助——一个选项列表；
- --safe-mode装载减去某些最佳配置的服务器；
- --verbose显示全文本消息（为获得更详细的帮助消息与--help联合使用）；
- --version显示版本信息然后退出。

## 29.4 查看日志文件

主要的日志文件有以下几种。

- 错误日志。

```sql
show variables like 'log_error';
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-6bcfbea7a2fdd868.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 查询日志。
它记录所有MySQL活动

```sql
show variables like 'general_log_file';
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-7cf54357fbfcb3a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 二进制日志。
它记录更新过数据（或者可能更新过数据）的所有语句。



- 缓慢查询日志。
顾名思义，此日志记录执行缓慢的任何查询。这个日志在确定数据库何处需要优化很有用。

```sql
show variables like 'slow_query_log_file';
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-850b04eb94b1fcb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



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
