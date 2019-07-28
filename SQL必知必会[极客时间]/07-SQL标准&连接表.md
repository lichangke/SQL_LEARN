目录链接：https://www.jianshu.com/p/2c104aaadb03

## 常用的 SQL 标准

SQL 有两个主要的标准， 分别是 SQL92 和 SQL99。 92 和 99 代表了标准提出的时间， SQL92 就是 92 年提出的标准规范。 当然除了 SQL92 和 SQL99 以外， 还存在 SQL-86、 SQL-89、SQL:2003、 SQL:2008、 SQL:2011 和 SQL:2016 等其他的标准。

## SQL92 中的连接

### 笛卡尔积

笛卡尔积也称为交叉连接， 英文是 CROSS JOIN， 它的作用就是可以把任意表进行连接， 即使这两张表不相关。 

笛卡尔乘积是一个数学运算。 假设我有两个集合 X 和 Y， 那么 X 和 Y 的笛卡尔积就是 X 和 Y 的所有可能组合， 也就是第一个对象来自于 X， 第二个对象来自于 Y 的所有可能。

使用NBA数据库，假定 player 表的数据是集合 X：

```sql
SELECT * FROM player;
```

假定 team 表的数据为集合 Y:

```sql
SELECT * FROM team;
```

两张表的笛卡尔积的结果:

```sql
SELECT * FROM player,team;
```
运行结果（一共 37*3=111 条记录） ：
![笛卡尔积.png](https://upload-images.jianshu.io/upload_images/16846478-4da9fe7e58e38776.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 等值连接

两张表的等值连接就是用两张表中都存在的列进行连接。也可以对多张表进行等值连接。

```sql
SELECT player_id, player.team_id, player_name, height, team_name 
FROM player, team WHERE player.team_id = team.team_id;
```
![等值连接.png](https://upload-images.jianshu.io/upload_images/16846478-f8cc291f65fd3ff2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以使用表的别名
```sql
SELECT player_id, a.team_id, player_name, height, team_name 
FROM player AS a, team AS b WHERE a.team_id = b.team_id;
```
使用了表的别名， 在查询字段中就只能使用别名进行代替， 不能使用原有的表名。

### 非等值连接

当我们进行多表查询的时候， 如果连接多个表的条件是等号时， 就是等值连接， 其他的运算符连接就是非等值查询。

### 外连接

除了查询满足条件的记录以外， 外连接还可以查询某一方不满足条件的记录。在 SQL92 中采用（+） 代表从表所在的位置，而且在SQL92 中， 只有左外连接和右外连接， 没有全外连接。

分为左外连接，右外连接：

左外连接，左边的表是主表，需要显示左边表的全部行，而右侧的表是从表， （+） 表示哪个是从表。：根据左表的记录，在被连接的右表中找出符合条件的记录与之匹配，找不到匹配的，用null填充。

```sql
SELECT * FROM player, team where player.team_id = team.team_id(+);
```
SQL99为：
```sql
SELECT * FROM player LEFT JOIN team on player.team_id = team.team_id;
```
![左外连接.png](https://upload-images.jianshu.io/upload_images/16846478-9c52c58790b5fd7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

右外连接，指的就是右边的表是主表， 需要显示右边表的全部行， 而左侧的表是从表：根据右表的记录，在被连接的左表中找出符合条件的记录与之匹配，找不到匹配的，用null填充

```sql
SELECT * FROM player, team where player.team_id(+) = team.team_id;
```
SQL99为：
```sql
SELECT * FROM player RIGHT JOIN team on player.team_id = team.team_id;
```
![右外连接](https://upload-images.jianshu.io/upload_images/16846478-5560f14821afb8e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![null填充.png](https://upload-images.jianshu.io/upload_images/16846478-9259088d405d5a22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![team表.png](https://upload-images.jianshu.io/upload_images/16846478-3be8130517b0fea3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 自连接

自连接可以对多个表进行操作， 也可以对同一个表进行操作。 也就是说查询条件使用了当前表的字段。

查看比布雷克·格里芬高的球员都有谁， 以及他们的对应身高：
```sql
SELECT b.player_name, b.height FROM player as a , player as b 
WHERE a.player_name = '布雷克-格里芬' and a.height < b.height;
```

![自连接.png](https://upload-images.jianshu.io/upload_images/16846478-6c2d92a978db8dd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不使用自连接，需要采用两次SQL查询：
```sql
-- ⾸先需要查询布雷克·格里芬的身高⾼
SELECT height FROM player WHERE player_name = '布雷克-格里芬';
-- 运行结果为 2.08
-- 然后再查询比2.08 高的球员都有谁， 以及他们的对应身高：
SELECT player_name, height FROM player WHERE height > 2.08;
```
![不使用自连接01.png](https://upload-images.jianshu.io/upload_images/16846478-f37747d50a063cc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![不使用自连接02.png](https://upload-images.jianshu.io/upload_images/16846478-34a8ca8b8f20a9c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

写成嵌套查询
```sql
SELECT player_name, height FROM player WHERE 
height > (SELECT height FROM player WHERE player_name = '布雷克-格里芬');
```
![嵌套查询.png](https://upload-images.jianshu.io/upload_images/16846478-ff71bc0ba9c477d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## SQL99 中的连接

### 交叉连接

交叉连接实际上就是 SQL92 中的笛卡尔乘积， 采用的是 CROSS JOIN。

得到 player 和 team 这两张表的笛卡尔积的结果：

```sql
SELECT * FROM player CROSS JOIN team;
```
![交叉连接.png](https://upload-images.jianshu.io/upload_images/16846478-3bf4b96c3bb35946.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多张表进行交叉连接：

```sql
SELECT * FROM t1 CROSS JOIN  t2 CROSS JOIN t3;
```

### 自然连接

可以把自然连接理解为 SQL92 中的等值连接，自动查询两张连接表中所有相同的字段， 然后进行等值连接。
```sql
SELECT player_id, team_id, player_name, height, team_name FROM player NATURAL JOIN team;
```
![自然连接.png](https://upload-images.jianshu.io/upload_images/16846478-2618c09afe15270d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

等价于：SQL92标准如下写法

```sql
SELECT player_id, a.team_id, player_name, height, team_name FROM player as a , team as b WHERE a.team_id = b.team_id;
```
在 SQL99 中用 NATURAL JOIN 替代了 WHERE player.team_id = team.team_id 。

### ON 连接

ON 连接用来指定我们想要的连接条件，上面例子中（自然连接中）可以写成：

```sql
SELECT player_id, player.team_id, player_name, height, team_name FROM player  JOIN team ON player.team_id = team.team_id;
```
![ON 连接.png](https://upload-images.jianshu.io/upload_images/16846478-796cf0a896f7682e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

指定了连接条件是 ON player.team_id = team.team_id ， 相当于是用ON 进行了team_id 字段的等值连接。

当然你也可以 ON 连接进行非等值连接。

在 SQL99 中， 需要连接的表会采用 JOIN 进行连接， ON 指定了连接条件， 后面可以是等值连接， 也可以采用非等值连接。

### USING 连接

进行连接的时候， 可以使用 USING 指定数据表中的同名字段进行等值连接。

```sql
SELECT player_id, team_id, player_name, height, team_name FROM player  JOIN team USING(team_id);
```
![USING 连接.png](https://upload-images.jianshu.io/upload_images/16846478-d19e43393c92b637.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

与自然连接 NATURAL JOIN 比较， USING 指定了具体的相同的字段名称。

### 外连接

SQL99 的外连接包括了三种形式：

1. 左外连接：LEFT JOIN 或 LEFT OUTER JOIN
2. 右外连接：RIGHT JOIN 或 RIGHT OUTER JOIN
3. 全外连接：FULL JOIN 或 FULL OUTER JOIN

相比较于SQL92，在 SQL99 中还有全外连接，全外连接实际上就是左外连接和右外连接的结合。 在这三种外连接中， 一般省略 OUTER 不写。

#### 1. 左外连接

SQL92:

```sql
SELECT * FROM player, team where player.team_id = team.team_id(+);
```

SQL99:

```sql
SELECT * FROM player LEFT JOIN team ON player.team_id = team.team_id;
```

![左外连接SQL99.png](https://upload-images.jianshu.io/upload_images/16846478-6dc0b74175820cb7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 2. 右外连接

SQL92:

```sql
SELECT * FROM player, team where player.team_id(+) = team.team_id;
```

SQL99:

```sql
SELECT * FROM player RIGHT JOIN team ON player.team_id = team.team_id;
```

![右外连接SQL99.png](https://upload-images.jianshu.io/upload_images/16846478-c8deefb211f7fe49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/16846478-bf0c732251c455d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参见  SQL92 中的连接 中讲述 外连接 段

#### 3.全外连接

SQL99:

```sql
SELECT * FROM player FULL JOIN team ON player.team_id = team.team_id;
```

### 自连接

自连接的原理在 SQL92 和 SQL99 中都是一样的， 只是表述方式不同。

SQL92:

```sql
SELECT b.player_name, b.height FROM player as a , player as b 
WHERE a.player_name = '布雷克-格里芬' and a.height < b.height;
```
![自连接SQL92.png](https://upload-images.jianshu.io/upload_images/16846478-2a90869f631bf574.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

SQL99:

```sql
SELECT b.player_name, b.height FROM player as a JOIN player as b 
ON a.player_name = '布雷克-格里芬' and a.height < b.height;
```

![自连接SQL99.png](https://upload-images.jianshu.io/upload_images/16846478-58c8c6d4f711e630.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 连接总结

连接操作基本上可以分成三种情况：

1. 内连接：将多个表之间满足连接条件的数据行查询出来。 它包括了等值连接、非等值连接和自连接。

2. 外连接：会返回一个表中的所有记录， 以及另一个表中匹配的行。 它包括了左外连接、 右外连接和全连接。

3. 交叉连接：也称为笛卡尔积， 返回左表中每一行与右表中每一行的组合。 在 SQL99 中使用 CROSS JOIN。

在 SQL92 中进行查询时， 会把所有需要连接的表都放到 FROM 之后， 然后在 WHERE 中写明连接的条件。
在 SQL99 中进行查询时， 它不需要一次性把所有需要连接的表都放到 FROM之后， 而是采用 JOIN 的⽅式， 每次连接一张表， 可以多次使用 JOIN 进行连接。

```sql
SELECT ...
FROM table1
    JOIN table2 ON table1 和 table2 的连接条件
        JOIN table3 ON table2 和 table3 的连接条件
```

![SQL标准&连接表.png](https://upload-images.jianshu.io/upload_images/16846478-dbee897a43935443.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 参考资料：

**注：**
练习数据库使用 SQL必知必会专栏（极客时间）中的作者提供的 王者荣耀数据库以及NBA数据库
练习系统  MySQL Server version: 5.7.26-0ubuntu0.16.04.1 (Ubuntu)

*极客时间 SQL必知必会学习*

SQL必知必会专栏（极客时间）链接： 
http://gk.link/a/103Sm

《MySQL必知必会》学习笔记（15-18）:

[https://www.jianshu.com/p/c575d71e9873](https://www.jianshu.com/p/c575d71e9873)

此篇内容:联结表、创建高级联结、组合查询、全文本搜索

SQL标准简介：

https://blog.csdn.net/lengye7/article/details/80606489

SQL中的连接查询与嵌套查询：

https://blog.csdn.net/CCSUXWZ/article/details/70157911

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
