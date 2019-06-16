**《MySQL必知必会》学习笔记 目录**

[https://www.jianshu.com/p/97bab08c6755](https://www.jianshu.com/p/97bab08c6755)

# 第 十 五 章 联 结 表

介绍什么是联结，为什么要使用联结，如何编写使用联结的SELECT语句

## 15.1 联结
SQL最强大的功能之一就是能在数据检索查询的执行中联结（join）表。

### 15.1.1 关系表

可建立两个表，一个存储供应商信息，另一个存储产品信息。 vendors表包含所有供应商信息，每个供应商占一行，每个供应商具有唯一的标识。此标识称为主键（ primary key） ，可以是供应商ID或任何其他唯一值。

products表只存储产品信息，它除了存储供应商ID（vendors表的主键）外不存储其他供应商信息。vendors表的主键又叫作products的外键，它将vendors表与products表关联，利用供应商ID能从vendors表中找出相应供应商的详细信息。

![image.png](https://upload-images.jianshu.io/upload_images/16846478-82b496c74b7fb1df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**外键（foreign key）** 外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。

**可伸缩性（scale）** 能够适应不断增加的工作量而不失败。

### 15.1.2 为什么要使用联结

分解数据为多个表能更有效地存储，更方便地处理，并且具有更大的可伸缩性。但这些好处是有代价的。如果数据存储在多个表中，怎样用单条SELECT语句检索出数据？答案是使用联结。简单地说，联结是一种机制，用来在一条SELECT语句中关联表，因此称之为联结。

## 15.2 创建联结

联结的创建非常简单，规定要联结的所有表以及它们如何关联即可。

```sql
SELECT vend_name, prod_name, prod_price FROM vendors, products WHERE vendors.vend_id = products.vend_id ORDER BY vend_name, prod_name;
```

![15.2SELECT.png](https://upload-images.jianshu.io/upload_images/16846478-e785d97b78c458fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

两个列（prod_name和prod_price）在一个表中，而另一个列（vend_name）在另一个表中。

这条语句的FROM子句列出了两个表，分别是vendors和products。它们就是这条SELECT语句联结的两个表的名字。这两个表用WHERE子句正确联结， WHERE子句指示MySQL匹配vendors表中的vend_id和products表中的vend_id。
可 以 看 到 要 匹 配 的 两 个 列 以 vendors.vend_id 和 products.vend_id指定。这里需要这种完全限定列名，因为如果只给出vend_id，则MySQL不知道指的是哪一个（它们有两个，每个表中一个）。


### 15.2.1 WHERE子句的重要性

**笛卡儿积（cartesian product）** 由没有联结条件的表关系返回的结果为笛卡儿积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数

```sql
SELECT vend_name, prod_name, prod_price FROM vendors, products ORDER BY vend_name, prod_name;
```

![15.2.1SELECT.png](https://upload-images.jianshu.io/upload_images/16846478-692acc506a71457b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里返回的数据用每个供应商匹配了每个产品，它包括了供应商不正确的产品。实际上有的供应商根本就没有产品。

**不要忘了WHERE子句** 应该保证所有联结都有WHERE子句，否则MySQL将返回比想要的数据多得多的数据。同理，应该保证WHERE子句的正确性。不正确的过滤条件将导致MySQL返回不正确的数据。

### 15.2.2 内部联结

目前为止所用的联结称为等值联结（equijoin），它基于两个表之间的相等测试。这种联结也称为内部联结。

```sql
SELECT vend_name, prod_name, prod_price FROM vendors INNER JOIN products ON vendors.vend_id = products.vend_id;
```

![15.2.2JOIN .png](https://upload-images.jianshu.io/upload_images/16846478-5b876a3f1c270aaf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


此语句中的SELECT与前面的SELECT语句相同，但FROM子句不同。这里，两个表之间的关系是FROM子句的组成部分，以INNERJOIN指定。在使用这种语法时，联结条件用特定的ON子句而不是WHERE子句给出。传递给ON的实际条件与传递给WHERE的相同。

### 15.2.3 联结多个表

SQL对一条SELECT语句中可以联结的表的数目没有限制。创建联结的基本规则也相同。首先列出所有表，然后定义表之间的关系。

```sql
SELECT prod_name, vend_name, prod_price, quantity FROM orderitems, products, vendors WHERE products.vend_id = vendors.vend_id AND orderitems.prod_id = products.prod_id  AND order_num = 20005;
```

![15.2.3SELECT.png](https://upload-images.jianshu.io/upload_images/16846478-476ee642b2dfb327.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此例子显示编号为20005的订单中的物品。订单物品存储在orderitems表中。每个产品按其产品ID存储，它引用products表中的产品。这些产品通过供应商ID联结到vendors表中相应的供应商，供应商ID存储在每个产品的记录中。这里的FROM子句列出了3个表，而WHERE子句定义了这两个联结条件，而第三个联结条件用来过滤出订单20005中的物品。

# 第 十 六 章 创建高级联结

讲解另外一些联结类型（包括它们的含义和使用方法），介绍如何对被联结的表使用表别名和聚集函数。

## 16.1 使用表别名

```sql
SELECT cust_name, cust_contact FROM customers AS c, orders AS o, orderitems AS oi WHERE c.cust_id = o.cust_id AND oi.order_num = o.order_num AND prod_id = 'TNT2';
```

![16.1.png](https://upload-images.jianshu.io/upload_images/16846478-a9b33210b87d8577.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 16.2 使用不同类型的联结

### 16.2.1 自联结

查询要求首先找到生产ID为DTNTR的物品的供应商，然后找出这个供应商生产的其他物品

```sql
SELECT prod_id, prod_name FROM products WHERE vend_id = (SELECT vend_id FROM products WHERE prod_id = 'DTNTR');
```

![16.2.1.png](https://upload-images.jianshu.io/upload_images/16846478-22bd16d28faac58d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第一种解决方案，它使用了子查询。内部的SELECT语句做了一个简单的检索，返回生产ID为DTNTR的物品供应商的vend_id。该ID用于外部查询的WHERE子句中，以便检索出这个供应商生产的所有物品。

```sql
SELECT p1.prod_id, p1.prod_name FROM products AS p1, products AS p2 WHERE p1.vend_id = p2.vend_id AND p2.prod_id = 'DTNTR';
```

![16.2.1.png](https://upload-images.jianshu.io/upload_images/16846478-c730563542574990.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此查询中需要的两个表实际上是相同的表，因此products表在FROM子句中出现了两次。虽然这是完全合法的，但对products的引用具有二义性，为解决此问题，使用了表别名。 

**用自联结而不用子查询** 自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。虽然最终的结果是相同的，但有时候处理联结远比处理子查询快得多。

### 16.2.2 自然联结

自然联结是这样一种联结，其中你只能选择那些唯一的列。这一般是通过对表使用通配符（SELECT *），对所有其他表的列使用明确的子集来完成的。

```sql
SELECT c.*, o.order_num, o.order_date, oi.prod_id, oi.quantity, oi.item_price  
FROM customers AS c, orders AS o, orderitems AS oi 
WHERE c.cust_id = o.cust_id AND oi.order_num = o.order_num AND prod_id = 'FB';
```

### 16.2.3 外部联结

许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行。

联结包含了那些在相关表中没有关联行的行。这种类型的联结称为**外部联结**。

下面的SELECT语句给出一个简单的内部联结。它检索所有客户及其订单：

```sql
SELECT customers.cust_id, orders.order_num FROM customers INNER JOIN orders ON customers.cust_id = orders.cust_id;
```

![16.2.3](https://upload-images.jianshu.io/upload_images/16846478-0b6bbc5249dbeb0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

外部联结语法类似。为了检索所有客户，包括那些没有订单的客户

```sql
SELECT customers.cust_id, orders.order_num FROM customers LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id;
```

![16.2.3.png](https://upload-images.jianshu.io/upload_images/16846478-2678b4ab397149c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这条SELECT语句使用了关键字OUTER JOIN来指定联结的类型（而不是在WHERE子句中指定）。但是，与内部联结关联两个表中的行不同的是，外部联结还包括没有关联行的行。在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字指定包括其所有行的表（RIGHT指出的是OUTER JOIN右边的表，而LEFT指出的是OUTER JOIN左边的表）。

```sql
SELECT customers.cust_id, orders.order_num FROM customers RIGHT OUTER JOIN orders ON customers.cust_id = orders.cust_id;
```

![16.2.3.png](https://upload-images.jianshu.io/upload_images/16846478-811d98756bc51915.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**外部联结的类型** 存在两种基本的外部联结形式：左外部联结和右外部联结。它们之间的唯一差别是所关联的表的顺序不同。换句话说，左外部联结可通过颠倒FROM或WHERE子句中表的顺序转换为右外部联结。

## 16.3 使用带聚集函数的联结

聚集函数用来汇总数据。虽然至今为止聚集函数的所有例子只是从单个表汇总数据，但这些函数也可以与联结一起使用。

如果要检索所有客户及每个客户所下的订单数，下面使用了COUNT()函数的代码可完成此工作：

```sql
SELECT customers.cust_name, customers.cust_id, COUNT(orders.order_num) AS num_ord FROM customers INNER JOIN orders ON customers.cust_id = orders.cust_id GROUP BY customers.cust_id;
```

![16.3.png](https://upload-images.jianshu.io/upload_images/16846478-434e268339440cdb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

聚集函数也可以方便地与其他联结一起使用。

```sql
SELECT customers.cust_name, customers.cust_id, COUNT(orders.order_num) AS num_ord FROM customers LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id GROUP BY customers.cust_id;
```

![16.3](https://upload-images.jianshu.io/upload_images/16846478-46a8ef98ee6a03f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 16.4 使用联结和联结条件

汇总一下关于联结及其使用的某些要点

- 注意所使用的联结类型。一般我们使用内部联结，但使用外部联结也是有效的。
- 保证使用正确的联结条件，否则将返回不正确的数据。
- 应该总是提供联结条件，否则会得出笛卡儿积。
- 在一个联结中可以包含多个表，甚至对于每个联结可以采用不同的联结类型。虽然这样做是合法的，一般也很有用，但应该在一起测试它们前，分别测试每个联结。这将使故障排除更为简单。


# 第 十 七 章 组 合 查 询

如何利用UNION操作符将多条SELECT语句组合成一个结果集。

## 17.1 组合查询

多数SQL查询都只包含从一个或多个表中返回数据的单条SELECT语句。 MySQL也允许执行多个查询（多条SELECT语句），并将结果作为单个查询结果集返回。这些组合查询通常称为并（ union） 或复合查询（compound query）。

有两种基本情况，其中需要使用组合查询：

- 在单个查询中从不同的表返回类似结构的数据；
- 对单个表执行多个查询，按单个查询返回数据。

**组合查询和多个WHERE条件** 多数情况下，组合相同表的两个查询完成的工作与具有多个WHERE子句条件的单条查询完成的工作相同。换句话说，任何具有多个WHERE子句的SELECT语句都可以作为一个组合查询给出

## 17.2 创建组合查询

可用UNION操作符来组合数条SQL查询。利用UNION，可给出多条SELECT语句，将它们的结果组合成单个结果集。

### 17.2.1 使用UNION

创建UNION涉及编写多条SELECT语句。首先来看单条语句：

```sql
SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5;
```

![17.2.1.png](https://upload-images.jianshu.io/upload_images/16846478-3143e37311d7378b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT vend_id, prod_id, prod_price FROM products WHERE vend_id IN (1001,1002);
```

![17.2.1.png](https://upload-images.jianshu.io/upload_images/16846478-6c41ac8df346a8e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第一条SELECT检索价格不高于5的所有物品。第二条SELECT使用IN找出供应商1001和1002生产的所有物品。

为了组合这两条语句，按如下进行：

```sql
SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5 UNION SELECT vend_id, prod_id, prod_price FROM products WHERE vend_id IN (1001,1002);
```

![17.2.1UNION.png](https://upload-images.jianshu.io/upload_images/16846478-8c4e8812c84b5178.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用多条WHERE子句而不是使用UNION的相同查询：

```sql
SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5 OR vend_id IN (1001,1002);
```

![17.2.1.png](https://upload-images.jianshu.io/upload_images/16846478-3bc55b38f379b263.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这个简单的例子中，使用UNION可能比使用WHERE子句更为复杂。但对于更复杂的过滤条件，或者从多个表（而不是单个表）中检索数据的情形，使用UNION可能会使处理更简单。

### 17.2.2 UNION规则

有几条规则需要注意。

- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔（因此，如果组合4条SELECT语句，将要使用3个UNION关键字）。
- UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）。
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。

### 17.2.3 包含或取消重复的行

UNION从查询结果集中自动去除了重复的行。这是UNION的默认行为，但是如果需要，可以改变它。事实上，如果想返回所有匹配行，可使用UNION ALL而不是UNION。

```sql
SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5 UNION ALL SELECT vend_id, prod_id, prod_price FROM products WHERE vend_id IN (1001,1002);
```

![17.2.3.png](https://upload-images.jianshu.io/upload_images/16846478-be67ede615972c09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用UNION ALL， MySQL不取消重复的行。因此这里的例子返回9行，其中有一行出现两次。

### 17.2.4 对组合查询结果排序

SELECT语句的输出用ORDER BY子句排序。对于结果集，不存在用一种方式排序一部分，而又用另一种方式排序另一部分的情况，因此不允许使用多条ORDER BY子句。

```sql
SELECT vend_id, prod_id, prod_price FROM products WHERE prod_price <= 5 
UNION SELECT vend_id, prod_id, prod_price FROM products WHERE vend_id IN (1001,1002) 
ORDER BY vend_id, prod_price;
```

![17.2.4.png](https://upload-images.jianshu.io/upload_images/16846478-bb0d74ad4cfe7ed0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 第 十 八 章 全文本搜索

如何使用MySQL的全文本搜索功能进行高级的数据查询和选择。

## 18.1 理解全文本搜索

在使用全文本搜索时， MySQL不需要分别查看每个行，不需要分别分析和处理每个词。 MySQL创建指定列中各词的一个索引，搜索可以针对这些词进行。

## 18.2 使用全文本搜索

### 18.2.1 启用全文本搜索支持

一般在创建表时启用全文本搜索。 CREATE TABLE语句接受FULLTEXT子句，它给出被索引列的一个逗号分隔的列表。
下面的CREATE语句演示了FULLTEXT子句的使用：

![18.2.1FULLTEXT.png](https://upload-images.jianshu.io/upload_images/16846478-64c06a6fb8168447.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这些列中有一个名为note_text的列，为了进行全文本搜索，MySQL根据子句FULLTEXT(note_text)的指示对它进行索引。这里的FULLTEXT索引单个列，如果需要也可以指定多个列。

**不要在导入数据时使用FULLTEXT** 更新索引要花时间，虽然不是很多，但毕竟要花时间。如果正在导入数据到一个新表，此时不应该启用FULLTEXT索引。应该首先导入所有数据，然后再修改表， 定义FULLTEXT。 

### 18.2.2 进行全文本搜索

在索引之后，使用两个函数Match()和Against()执行全文本搜索，其中Match()指定被搜索的列， Against()指定要使用的搜索表达式。

```sql
SELECT note_text FROM productnotes WHERE Match(note_text) Against('rabbit');
```

![18.2.2.png](https://upload-images.jianshu.io/upload_images/16846478-0f7a80f8352da049.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**使 用 完 整 的 Match() 说 明** 传 递 给 Match() 的 值 必 须 与FULLTEXT()定义中的相同。如果指定多个列，则必须列出它们（而且次序正确）。

**搜索不区分大小写** 除非使用BINARY方式，否则全文本搜索不区分大小写。

事实是刚才的搜索可以简单地用LIKE子句完成

```sql
SELECT note_text FROM productnotes WHERE note_text LIKE '%rabbit%';
```

![18.2.2.png](https://upload-images.jianshu.io/upload_images/16846478-a10753850dfd4de5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 18.2.3 使用查询扩展

查询扩展用来设法放宽所返回的全文本搜索结果的范围。

在使用查询扩展时， MySQL对数据和索引进行两遍扫描来完成搜索：

- 首先，进行一个基本的全文本搜索，找出与搜索条件匹配的所有行；
- 其次， MySQL检查这些匹配行并选择所有有用的词（我们将会简要地解释MySQL如何断定什么有用，什么无用）。
- 再其次， MySQL再次进行全文本搜索，这次不仅使用原来的条件，而且还使用所有有用的词。

首先进行一个简单的全文本搜索，没有查询扩展：

```sql
SELECT note_text FROM productnotes WHERE Match(note_text ) Against('anvils');
```

![18.2.3.png](https://upload-images.jianshu.io/upload_images/16846478-693e51dfa5741baa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
只有一行包含词anvils，因此只返回一行。

下面是相同的搜索，这次使用查询扩展：

```sql
SELECT note_text FROM productnotes WHERE Match(note_text ) Against('anvils' WITH QUERY EXPANSION);
```

![18.2.3EXPANSION.png](https://upload-images.jianshu.io/upload_images/16846478-e7e8dfaa3d38200d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 18.2.4 布尔文本搜索

MySQL支持全文本搜索的另外一种形式，称为布尔方式（boolean mode）。

以布尔方式，可以提供关于如下内容的细节：
- 要匹配的词；
- 要排斥的词（如果某行包含这个词，则不返回该行，即使它包含其他指定的词也是如此）；
- 排列提示（指定某些词比其他词更重要，更重要的词等级更高）；
- 表达式分组；
- 另外一些内容

演示IN BOOLEAN MODE的作用，举一个简单的例子

```sql
SELECT note_text FROM productnotes WHERE Match(note_text ) Against('heavy' IN BOOLEAN MODE);
```

![18.2.4.png](https://upload-images.jianshu.io/upload_images/16846478-5bf6239f0d6990e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了匹配包含heavy但不包含任意以rope开始的词的行， 可使用以下查询：

```sql
SELECT note_text FROM productnotes WHERE Match(note_text ) Against('heavy -rope*' IN BOOLEAN MODE);
```

![18.2.4.png](https://upload-images.jianshu.io/upload_images/16846478-5252ee00eea1e1c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**全文本布尔操作符**
|布尔操作符 |说 明|
|-|-|
|+ |包含，词必须存在|
|- |排除，词必须不出现|
|> |包含，而且增加等级值|
|< |包含，且减少等级值|
|() |把词组成子表达式（允许这些子表达式作为一个组被包含、排除、排列等）|
|~ |取消一个词的排序值|
|* |词尾的通配符|
|"" |定义一个短语（与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语）|


```sql
SELECT note_text FROM productnotes WHERE Match(note_text ) Against('+rabbit +bait' IN BOOLEAN MODE);
```
这个搜索匹配包含词rabbit和bait的行,同时包含。

![18.2.4.png](https://upload-images.jianshu.io/upload_images/16846478-e8ae8b2d241aaba0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT note_text FROM productnotes WHERE Match(note_text ) Against('rabbit  bait' IN BOOLEAN MODE);
```

没有指定操作符，这个搜索匹配包含rabbit和bait中的至少一个词的行。

![18.2.4.png](https://upload-images.jianshu.io/upload_images/16846478-fc5f108fff57cedd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT note_text FROM productnotes WHERE Match(note_text ) Against(' " rabbit  bait " ' IN BOOLEAN MODE);
```

这个搜索匹配短语rabbit bait而不是匹配两个词rabbit和bait。

![18.2.4.png](https://upload-images.jianshu.io/upload_images/16846478-d0ca3722e45d7c6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



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