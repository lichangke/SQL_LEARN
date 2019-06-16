**《MySQL必知必会》学习笔记 目录**

[https://www.jianshu.com/p/97bab08c6755](https://www.jianshu.com/p/97bab08c6755)


# 第九章 用正则表达式进行搜索

如何在MySQL WHERE子句内使用正则表达式来更好地控制数据过滤

## 9.1 正则表达式介绍

https://www.runoob.com/regexp/regexp-tutorial.html

## 9.2 使用MySQL正则表达式

可参见 [MySQL 正则表达式](https://www.runoob.com/mysql/mysql-regexp.html)

正则表达式的作用是匹配文本，将一个模式（正则表达式）与一个文本串进行比较。MySQL用WHERE子句对正则表达式提供了初步的支持，允许你指定正则表达式，过滤SELECT检索出的数据。

**仅为正则表达式语言的一个子集**  如果你熟悉正则表达式， 需要注意： MySQL仅支持多数正则表达式实现的一个很小的子集。

### 9.2.1 基本字符匹配

```sql
SELECT prod_name FROM prooducts WHERE prod_name REGEXP '1000' ORDER BY prod_name;
```

![9.2.1REGEXP1.png](https://upload-images.jianshu.io/upload_images/16846478-226109f311a13439.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT prod_name FROM prooducts WHERE prod_name REGEXP '.000' ORDER BY prod_name;*
```

![9.2.1REGEXP2.png](https://upload-images.jianshu.io/upload_images/16846478-7bb5fe843770e41f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

.是正则表达式语言中一个特殊的字符。它表示匹配任意一个字符。

### 9.2.2 进行OR匹配

为搜索两个串之一（或者为这个串，或者为另一个串），使用|

```sql
SELECT prod_name FROM products WHERE prod_name REGEXP '1000|2000' ORDER BY prod_name;
```

![9.2.2REGEXP.png](https://upload-images.jianshu.io/upload_images/16846478-a8fccc6562f07826.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 9.2.3 匹配几个字符之一

匹配任何单一字符。但是.如果你只想匹配特定的字符，怎么办？可通过指定一组用[和]括起来的字符来完成

```sql
SELECT prod_name FROM products WHERE prod_name REGEXP '[123] Ton' ORDER BY prod_name;
```

![9.2.3REGEXP.png](https://upload-images.jianshu.io/upload_images/16846478-7b10427d90eac9d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 9.2.4 匹配范围

集合可用来定义要匹配的一个或多个字符。[0123456789] 等同于 [0 - 9]

```sql
SELECT prod_name FROM products WHERE prod_name REGEXP '[1-5] Ton' ORDER BY prod_name;
```

![9.2.4REGEXP .png](https://upload-images.jianshu.io/upload_images/16846478-d9575484fbad618a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 9.2.5 匹配特殊字符

为了匹配特殊字符，必须用\\\\为前导。\\\\-表示查找-， \\\\.表示查找.。

```sql
SELECT vend_name FROM vendors WHERE vend_name REGEXP '\\\\.' ORDER BY vend_name;
```

![9.2.5REGEXP.png](https://upload-images.jianshu.io/upload_images/16846478-3f216c16efcb4ff1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**空白元字符**
| 元 字 符|    说 明 | 
|------| -----------|
|\\\\f|换页|
|\\\\n|换行|
|\\\\r|回车|
|\\\\t|制表|
|\\\\v|纵向制表|

**匹配\\** 为了匹配反斜杠（ \）字符本身，需要使用\\\\\。

**\\或\\\\?** 多数正则表达式实现使用单个反斜杠转义特殊字符，以便能使用这些字符本身。但MySQL要求两个反斜杠（ MySQL自己解释一个，正则表达式库解释另一个）。

### 9.2.6 匹配字符类

为更方便工作，可以使用预定义的字符集，称为字符类（character class）。
**字符类**
| 类|    说 明 | 
|------| -----------|
|[:alnum:]| 任意字母和数字（同[a-zA-Z0-9]）|
|[:alpha:]| 任意字符（同[a-zA-Z]）|
|[:blank:]| 空格和制表（同[\\\\t]）|
|[:cntrl:]| ASCII控制字符（ASCII 0到31和127）|
|[:digit:]| 任意数字（同[0-9]）|
|[:graph:]| 与[:print:]相同，但不包括空格|
|[:lower:]| 任意小写字母（同[a-z]）|
|[:print:]| 任意可打印字符|
|[:punct:]| 既不在[:alnum:]又不在[:cntrl:]中的任意字符|
|[:space:]| 包括空格在内的任意空白字符（同[\\\\f\\\\n\\\\r\\\\t\\\\v]）|
|[:upper:]| 任意大写字母（同[A-Z]）|
|[:xdigit:]| 任意十六进制数字（同[a-fA-F0-9]）|


### 9.2.7 匹配多个实例

**重复元字符**
| 类|    说 明 | 
|------| -----------|
|*| 0个或多个匹配|
|+| 1个或多个匹配（等于{1,}）|
|?| 0个或1个匹配（等于{0,1}）|
|{n} |指定数目的匹配|
|{n,}| 不少于指定数目的匹配|
|{n,m}| 匹配数目的范围（m不超过255）|

```sql
SELECT prod_name FROM products WHERE prod_name REGEXP '\\\\([0-9] sticks?\\\\)' ORDER BY prod_name;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-a7bfd340dfa993f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT prod_name FROM products WHERE prod_name REGEXP '[[:digit:]]{4}' ORDER BY prod_name;
```

![image.png](https://upload-images.jianshu.io/upload_images/16846478-2dd2d8128b4a170e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 9.2.8 定位符
目前为止的所有例子都是匹配一个串中任意位置的文本。
**定位元字符**
| 类|    说 明 | 
|------| -----------|
|^| 文本的开始|
|$| 文本的结尾|
|[[:<:]] |词的开始|
|[[:>:]] |词的结尾|

```sql
SELECT prod_name FROM products WHERE prod_name REGEXP '^[0-9\\\\.]' ORDER BY prod_name;
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-4311f2b069ae8665.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 第 十 章 创建计算字段
## 10.1 计算字段
**字段（field）** 基本上与列（ column） 的意思相同，经常互换使用，不过数据库列一般称为列，而术语字段通常用在计算字段的连接上。

## 10.2 拼接字段
**拼接（concatenate）** 将值联结到一起构成单个值。

把两个列拼接起来可使用Concat()函数来拼接两个列。

```sql
SELECT Concat(vend_name,'(',vend_country,')') FROM vendors ORDER BY vend_name;
```

![10.2Concat1.png](https://upload-images.jianshu.io/upload_images/16846478-1ce12a7b4453660b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过删除数据右侧多余的空格来整理数据，这可以使用MySQL的RTrim()函数来完成

```sql
SELECT Concat(RTrim(vend_name), '(', RTrim(vend_country), ')')  FROM vendors ORDER BY vend_name;
```

![10.2Concat2.png](https://upload-images.jianshu.io/upload_images/16846478-7a26e7bc306277b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**Trim函数** MySQL除了支持RTrim()（正如刚才所见，它去掉串右边的空格），还支持LTrim()（去掉串左边的空格）以及Trim()（去掉串左右两边的空格）。

**别名（ alias）** 是一个字段或值的替换名。别名用AS关键字赋予。

```sql
SELECT Concat(RTrim(vend_name), '(', RTrim(vend_country), ')')  AS vend_title FROM vendors ORDER BY vend_name;
```

![10.2AS.png](https://upload-images.jianshu.io/upload_images/16846478-023471ec8384085a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从输出中可以看到，结果与以前的相同，但现在列名为vend_title，任何客户机应用都可以按名引用这个列，就像它是一个实际的表列一样。

## 10.3 执行算术计算

```sql
SELECT prod_id, quantity, item_price , quantity*item_price AS expanded_price
FROM orderitems WHERE order_num = 20005;
```

![10.3AS.png](https://upload-images.jianshu.io/upload_images/16846478-b6984b0fbea56a92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**MySQL算术操作符**
|操 作 符|说 明|
|-|-|
|+| 加|
|- |减|
|*| 乘|
|/ |除|

# 第 十一 章 使用数据处理函数

介绍什么是函数， MySQL支持何种函数，以及如何使用这些函数

## 11.1 函数

与其他大多数计算机语言一样， SQL支持利用函数来处理数据。函数一般是在数据上执行的，它给数据的转换和处理提供了方便。

## 11.2 使用函数

大多数SQL实现支持以下类型的函数。

- 用于处理文本串（如删除或填充值，转换值为大写或小写）的文本函数。
- 用于在数值数据上进行算术操作（如返回绝对值，进行代数运算）的数值函数。
- 用于处理日期和时间值并从这些值中提取特定成分（例如，返回两个日期之差，检查日期有效性等）的日期和时间函数。
- 返回DBMS正使用的特殊信息（如返回用户登录信息，检查版本细节）的系统函数。

### 11.2.1 文本处理函数

上一章中我们已经看过一个文本处理函数的例子，其中使用RTrim()函数来去除列值右边的空格。

使用Upper()函数

```sql
SELECT vend_name, Upper(vend_name) AS vend_name_upcase FROM vendors ORDER BY vend_name;
```

![11.2.1Upper.png](https://upload-images.jianshu.io/upload_images/16846478-d824f5ca00c43b65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**常用的文本处理函数**

|函 数 |说 明|
|--|--|
|Left() |返回串左边的字符|
|Length()| 返回串的长度|
|Locate()| 找出串的一个子串|
|Lower() |将串转换为小写|
|LTrim()| 去掉串左边的空格|
|Right() |返回串右边的字符|
|RTrim() |去掉串右边的空格|
|Soundex()| 返回串的SOUNDEX值|
|SubString()| 返回子串的字符|
|Upper() |将串转换为大写|

SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。 SOUNDEX考虑了类似的发音字符和音节，使得能对串进行发音比较而不是字母比较。

 customers表中有一个顾客Coyote Inc.，其联系名为Y.Lee。但如果这是输入错误，此联系名实际应该是Y.Lie，怎么办？显然，按正确的联系名搜索不会返回数据

```sql
SELECT cust_name , cust_contact FROM customers WHERE cust_contact = 'Y. Lie';
```

![11.2.1image.png](https://upload-images.jianshu.io/upload_images/16846478-da7b2f644af43b51.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*SELECT cust_name , cust_contact FROM customers WHERE Soundex( cust_contact ) = Soundex( 'Y. Lie' );*

![11.2.1Soundex.png](https://upload-images.jianshu.io/upload_images/16846478-90d51173b79e1973.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 11.2.2 日期和时间处理函数

**常用日期和时间处理函数**

|函 数 |说 明|
|--|--|
|AddDate() |增加一个日期（天、周等）|
|AddTime() |增加一个时间（时、分等）|
|CurDate() |返回当前日期|
|CurTime() |返回当前时间|
|Date() |返回日期时间的日期部分|
|DateDiff() |计算两个日期之差|
|Date_Add() |高度灵活的日期运算函数|
|Date_Format() |返回一个格式化的日期或时间串|
|Day()| 返回一个日期的天数部分|
|DayOfWeek() |对于一个日期，返回对应的星期几|
|Hour() |返回一个时间的小时部分|
|Minute() |返回一个时间的分钟部分|
|Month() |返回一个日期的月份部分|
|Now()| 返回当前日期和时间|
|Second()| 返回一个时间的秒部分|
|Time()| 返回一个日期时间的时间部分|
|Year() |返回一个日期的年份部分|

不管是插入或更新表值还是用WHERE子句进行过滤，日期必须为格式yyyy-mm-dd。

```sql
SELECT cust_id, order_num FROM orders WHERE order_date = '2005-09-01';
```

![11.2.2date.png](https://upload-images.jianshu.io/upload_images/16846478-99be124d8d916d43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用WHERE order_date = '2005-09-01'可靠吗？ 比如,存 储 的 order_date 值 为2005-09-01 11:30:05，则WHERE order_date = '2005-09-01'失败

```sql
SELECT cust_id, order_num FROM orders WHERE Date(order_date) = '2005-09-01';
```

![11.2.2Date.png](https://upload-images.jianshu.io/upload_images/16846478-6290de86a544dc23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**如果要的是日期，请使用Date()** 如果你想要的仅是日期，则使用Date()是一个良好的习惯，即使你知道相应的列只包含日期也是如此。当然，也存在一个Time()函数，在你只想要时间时应该使用它。

```sql
SELECT cust_id, order_num FROM orders WHERE Date(order_date) BETWEEN '2005-09-01' AND '2005-09-30';
```

![11.2.2BETWEEN.png](https://upload-images.jianshu.io/upload_images/16846478-8310f24f6a7665bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中， BETWEEN操作符用来把2005-09-01和2005-09-30定义为一个要匹配的日期范围。

还有另外一种办法

```sql
SELECT cust_id, order_num FROM orders WHERE Year(order_date) = 2005 AND Month(order_date) = 9;
```

![11.2.2Year.png](https://upload-images.jianshu.io/upload_images/16846478-5186404d3a0de106.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 11.2.3 数值处理函数

**常用数值处理函数**

|函 数 |说 明|
|--|--|
|Abs()| 返回一个数的绝对值|
|Cos()| 返回一个角度的余弦|
|Exp()| 返回一个数的指数值|
|Mod()| 返回除操作的余数|
|Pi() |返回圆周率|
|Rand()| 返回一个随机数|
|Sin() |返回一个角度的正弦|
|Sqrt() |返回一个数的平方根|
|Tan()| 返回一个角度的正切|


# 第 十二 章 汇 总 数 据

介绍什么是SQL的聚集函数以及如何利用它们汇总表的数据。

## 12.1 聚集函数

**聚集函数（ aggregate function）** 运行在行组上，计算和返回单个值的函数

**SQL聚集函数**

|函 数 |说 明|
|--|--|
|AVG()| 返回某列的平均值|
|COUNT()| 返回某列的行数|
|MAX() |返回某列的最大值|
|MIN() |返回某列的最小值|
|SUM() |返回某列值之和|

### 12.1.1 AVG()函数

AVG()通过对表中行数计数并计算特定列值之和，求得该列的平均值。 AVG()可用来返回所有列的平均值，也可以用来返回特定列或行的平均值。

```sql
SELECT AVG(prod_price) AS avg_price FROM products;
```

![12.1.1AVG1.png](https://upload-images.jianshu.io/upload_images/16846478-1100dc23b0ee041a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

AVG()也可以用来确定特定列或行的平均值。

```sql
SELECT AVG(prod_price) AS avg_price FROM products WHERE vend_id = 1003;
```

![12.1.1AVG2.png](https://upload-images.jianshu.io/upload_images/16846478-6f24291fbab94dbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**只用于单个列** AVG()只能用来确定特定数值列的平均值，而且列名必须作为函数参数给出。为了获得多个列的平均值，必须使用多个AVG()函数。

*NULL值* AVG()函数忽略列值为NULL的行

### 12.1.2 COUNT()函数

COUNT()函数进行计数。可利用COUNT()确定表中行的数目或符合特定条件的行的数目。

- 使用COUNT(*)对表中行的数目进行计数， 不管表列中包含的是空值（NULL）还是非空值。
- 使用COUNT(column)对特定列中具有值的行进行计数，忽略NULL值。

```sql
SELECT COUNT(*) AS num_cust FROM customers;
```

![12.1.2COUNT1.png](https://upload-images.jianshu.io/upload_images/16846478-23733b8b1a30eced.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```sql
SELECT COUNT(cust_email) AS num_cust FROM customers;
```

![12.1.2COUNT2.png](https://upload-images.jianshu.io/upload_images/16846478-46a1c1980c99e6e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 12.1.3 MAX()函数

MAX()返回指定列中的最大值。 MAX()要求指定列名。

```sql
SELECT MAX(prod_price) AS max_price FROM products;
```

![12.1.3MAX1.png](https://upload-images.jianshu.io/upload_images/16846478-98cc4349cce8423d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**对非数值数据使用MAX() ** 虽然MAX()一般用来找出最大的数值或日期值，但MySQL允许将它用来返回任意列中的最大值，包括返回文本列中的最大值。在用于文本数据时，如果数据按相应的列排序，则MAX()返回最后一行。

**NULL值** MAX()函数忽略列值为NULL的行

```sql
SELECT MAX(prod_id) AS max_price FROM products;
```

![12.1.3MAX2.png](https://upload-images.jianshu.io/upload_images/16846478-47f08e827afacff2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 12.1.4 MIN()函数

MIN()的功能正好与MAX()功能相反，它返回指定列的最小值。与MAX()一样， MIN()要求指定列名。

```sql
SELECT MIN(prod_price) AS max_price FROM products;
```
![12.1.4MIN1.png](https://upload-images.jianshu.io/upload_images/16846478-81c129da71b196ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**对非数值数据使用MIN()** MIN()函数与MAX()函数类似，MySQL允许将它用来返回任意列中的最小值，包括返回文本列中的最小值。

**NULL值** MIN()函数忽略列值为NULL的行

```sql
SELECT MIN(prod_id) AS max_price FROM products;
```

![12.1.4MIN2.png](https://upload-images.jianshu.io/upload_images/16846478-ff193a7317178007.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 12.1.5 SUM()函数

SUM()用来返回指定列值的和（总计）。

```sql
SELECT SUM(quantity) AS items_ordered FROM orderitems WHERE order_num = 20005;
```

![12.1.5SUM1.png](https://upload-images.jianshu.io/upload_images/16846478-526352fff1cd9cff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SUM()也可以用来合计计算值。在下面的例子中，合计每项物品的item_price*quantity，得出总的订单金额

```sql
SELECT SUM(quantity*item_price) AS items_ordered FROM orderitems WHERE order_num = 20005;
```

![12.1.5SUM2.png](https://upload-images.jianshu.io/upload_images/16846478-563e40947908d589.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**NULL值** SUM()函数忽略列值为NULL的行。

## 12.2 聚集不同值

5个聚集函数都可以如下使用：

- 对所有的行执行计算，指定ALL参数或不给参数（因为ALL是默认行为）；
- 只包含不同的值，指定DISTINCT参数。

AVG()函数返回特定供应商提供的产品的平均价格，但使用了DISTINCT参数，因此平均值只
考虑各个不同的价格

```sql
SELECT AVG(DISTINCT prod_price) AS avg_price FROM products WHERE vend_id = 1003;
 ```

![12.2AVG.png](https://upload-images.jianshu.io/upload_images/16846478-db59f33a1b496331.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 12.3 组合聚集函数

实际上SELECT语句可根据需要包含多个聚集函数。

```sql
SELECT COUNT(*) AS num_items, MIN(prod_price) AS price_min, MAX(prod_price) AS price_max, AVG(prod_price) AS price_avg FROM products;
```

![12.3COUNT.png](https://upload-images.jianshu.io/upload_images/16846478-ed9b1044fc2f7a49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 第 十三 章 分 组 数 据

如何分组数据，以便能汇总表内容的子集。这涉及两个新SELECT语句子句，分别是GROUP BY子句和HAVING子句。

## 13.1 数据分组

分组允许把数据分为多个逻辑组，以便能对每个组进行聚集计算。

## 13.2 创建分组

分组是在SELECT语句的GROUP BY子句中建立的。

```sql
SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id;
```

![13.2GROUP1.png](https://upload-images.jianshu.io/upload_images/16846478-67095d34391cf7bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

GROUP BY子句指示MySQL按vend_id排序并分组数据。这导致对每个vend_id而不是整个表计算num_prods一次。

GROUP BY子句指示MySQL分组数据，然后对每个组而不是整个结果集进行聚集。

在具体使用GROUP BY子句前，需要知道一些重要的规定。

- GROUP BY子句可以包含任意数目的列。这使得能对分组进行嵌套，为数据分组提供更细致的控制。
- 如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。
- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式（但不能是聚集函数）。如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式。不能使用别名。
- 除聚集计算语句外， SELECT语句中的每个列都必须在GROUP BY子句中给出。
- 如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列中有多行NULL值，它们将分为一组。
- GROUP BY子句必须出现在WHERE子句之后， ORDER BY子句之前。

**使用ROLLUP** 使用WITH ROLLUP关键字，可以得到每个分组以及每个分组汇总级别（针对每个分组）的值

```sql
SELECT vend_id, COUNT(*) AS num_prods FROM products GROUP BY vend_id WITH ROLLUP;
```

![13.2GROUP2.png](https://upload-images.jianshu.io/upload_images/16846478-61802424aa99ebf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 13.3 过滤分组

除了能用GROUP BY分组数据外， MySQL还允许过滤分组，规定包括哪些分组，排除哪些分组。必须基于完整的分组而不是个别的行进行过滤。

HAVING非常类似于WHERE。事实上，目前为止所学过的所有类型的WHERE子句都可以用HAVING来替代。唯一的差别是WHERE过滤行，而HAVING过滤分组。

```sql
SELECT cust_id, COUNT(*) AS orders FROM orders GROUP BY cust_id HAVING COUNT(*) >=2;
```

![13.3HAVING1.png](https://upload-images.jianshu.io/upload_images/16846478-770bb85fc0d7edf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

正如所见，这里WHERE子句不起作用，因为过滤是基于分组聚集值而不是特定行值的。

**HAVING和WHERE的差别** 这里有另一种理解方法，WHERE在数据分组前进行过滤， HAVING在数据分组后进行过滤。这是一个重
要的区别， WHERE排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组。

```sql
SELECT vend_id, COUNT(*) AS num_prods FROM products WHERE prod_price >=10 GROUP BY vend_id HAVING COUNT(*) >=2;
```

![13.3HAVIN2.png](https://upload-images.jianshu.io/upload_images/16846478-d6c43b780a6d1360.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

WHERE子句过滤所有prod_price至少为10的行。然后按vend_id分组数据， HAVING子句过滤计数为2或2以上的分组。

## 13.4 分组和排序

**ORDER BY与GROUP BY 差别**
|ORDER BY|GROUP BY|
|--|--|
|排序产生的输出 |分组行。但输出可能不是分组的顺序|
|任意列都可以使用（甚至非选择的列也可以使用）| 只可能使用选择列或表达式列，而且必须使用每个选择列表达式|
|不一定需要 |如果与聚集函数一起使用列（或表达式），则必须使用|

**不要忘记ORDER BY** 一般在使用GROUP BY子句时，应该也给出ORDER BY子句。这是保证数据正确排序的唯一方法。千万不要仅依赖GROUP BY排序数据。

```sql
SELECT order_num, SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price) >= 50;
```

![13.4GROUP1.png](https://upload-images.jianshu.io/upload_images/16846478-4cc65b300154eb8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
SELECT order_num, SUM(quantity*item_price) AS ordertotal FROM orderitems GROUP BY order_num HAVING SUM(quantity*item_price) >= 50 ORDER BY ordertotal;
```

![13.4GROUP2.png](https://upload-images.jianshu.io/upload_images/16846478-ca16dc065e3c7389.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 13.5 SELECT子句顺序

在SELECT语句中使用时必须遵循的次序，列出迄今为止所学过的子句。

**SELECT子句及其顺序**

|子 句|说 明|是否必须使用|
|--|--|--|
|SELECT| 要返回的列或表达式| 是
|FROM |从中检索数据的表 |仅在从表选择数据时使用
|WHERE |行级过滤 |否
|GROUP BY | 分组说明 |仅在按组计算聚集时使用
|HAVING| 组级过滤 |否
|ORDER BY| 输出排序顺序 |否
|LIMIT |要检索的行数 |否|


# 第 十 四 章 使用子查询

介绍什么是子查询以及如何使用它们。

## 14.1 子查询

SQL还允许创建子查询（ subquery） ，即嵌套在其他查询中的查询。

## 14.2 利用子查询进行过滤

就书中所述，对于包含订单号、客户ID、订单日期的每个订单， orders表存储一行。各订单的物品存储在相关的orderitems表中。 orders表不存储客户信息。它只存储客户的ID。实际的客户信息存储在customers表中。
假如需要列出订购物品TNT2的所有客户，应该怎样检索？下面列出具体的步骤。

(1) 检索包含物品TNT2的所有订单的编号。
(2) 检索具有前一步骤列出的订单编号的所有客户的ID。
(3) 检索前一步骤返回的所有客户ID的客户信息。

上述每个步骤都可以单独作为一个查询来执行。可以把一条SELECT语句返回的结果用于另一条SELECT语句的WHERE子句。
也可以使用子查询来把3个查询组合成一条语句。
第一条SELECT语句的含义很明确，对于prod_id为TNT2的所有订单物品，它检索其order_num列。

```sql
SELECT order_num FROM orderitems WHERE prod_id = 'TNT2';
```

![14.2SELECT 1.png](https://upload-images.jianshu.io/upload_images/16846478-d842eebf82e1c712.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下一步，查询具有订单20005和20007的客户ID。

```sql
SELECT cust_id FROM orders WHERE order_num IN (20005,20007);
```

![14.2SELECT 2.png](https://upload-images.jianshu.io/upload_images/16846478-d48862de6c38496d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在，把第一个查询（返回订单号的那一个）变为子查询组合两个查询。

```sql
SELECT cust_id FROM orders WHERE order_num IN(SELECT order_num FROM orderitems WHERE prod_id = 'TNT2');
```

![14.2SELECT 3.png](https://upload-images.jianshu.io/upload_images/16846478-cd1e3f6ff73a3dd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在SELECT语句中，子查询总是从内向外处理。在处理上面的SELECT语句时， MySQL实际上执行了两个操作。

首先，它执行下面的查询：

```sql
SELECT order_num FROM orderitems WHERE prod_id = 'TNT2'
```

然后，这两个值以IN操作符要求的逗号分隔的格式传递给外部查询的WHERE子句。外部查询变成：

```sql
SELECT cust_id FROM orders WHERE order_num IN (20005,20007)
```

现在得到了订购物品TNT2的所有客户的ID。下一步是检索这些客户ID的客户信息。

```sql
SELECT cust_name, cust_contact FROM customers WHERE cust_id IN (10001,10004);
```

![14.2SELECT 4.png](https://upload-images.jianshu.io/upload_images/16846478-7a6135900c1bbeb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以把其中的WHERE子句转换为子查询而不是硬编码这些客户ID

```sql
SELECT cust_name, cust_contact 
FROM customers 
WHERE cust_id IN (SELECT cust_id FROM orders WHERE order_num IN(SELECT order_num FROM orderitems WHERE prod_id = 'TNT2'));
```

![14.2SELECT 5.png](https://upload-images.jianshu.io/upload_images/16846478-aae75e5a7d83630b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 14.3 作为计算字段使用子查询
使用子查询的另一方法是创建计算字段。假如需要显示customers表中每个客户的订单总数。订单与相应的客户ID存储在orders表中
为了执行这个操作，遵循下面的步骤。
(1) 从customers表中检索客户列表。
(2) 对于检索出的每个客户，统计其在orders表中的订单数目。

```sql
SELECT cust_name, cust_state, ( SELECT COUNT(*) FROM orders WHERE orders.cust_id = customers.cust_id) AS orders FROM customers ORDER BY cust_name;
```

![14.3SELECT .png](https://upload-images.jianshu.io/upload_images/16846478-0585cd0326efd7dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


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