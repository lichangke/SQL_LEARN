目录链接：https://www.jianshu.com/p/2c104aaadb03

## 什么是视图

视图， 也就可以讲的虚拟表， 本身是不具有数据的， 它是 SQL 中的一个重要概念。
虚拟表的创建连接了一个或多个数据表， 不同的查询应用都可以建立在虚拟表之上。

![什么是视图.png](https://upload-images.jianshu.io/upload_images/16846478-3425157f8a3b0d64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

视图一方面可以帮我们使用表的一部分而不是所有的表， 另一方面也可以针对不同的用户制定不同的查询视图。 ⽐

## 如何创建， 更新和删除视图

视图作为一张虚拟表， 帮我们封装了底层与数据表的接口。 它相当于是一张表或多张表的数据结果集。 视图的这一特点， 可以帮我们简化复杂的 SQL 查询。

### 创建视图：CREATE VIEW
```sql
CREATE VIEW view_name AS
SELECT column1, column2
FROM table
WHERE condition
```

view_name 为视图名称， column1、 column2 代表列名， condition 代表查询过滤条件。


以 NBA 球员数据表为例。 我们想要查询比NBA 球员平均身高高的球员都有哪些， 显示他们的球员 ID 和身高。 

```sql
CREATE VIEW player_above_avg_height AS
SELECT player_id, height
FROM player
WHERE height > (SELECT AVG(height) from player);
```
![创建视图.png](https://upload-images.jianshu.io/upload_images/16846478-387cd46a7d62e886.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 嵌套视图

当我们创建好一张视图之后， 还可以在它的基础上继续创建视图， 比如可以在虚拟表player_above_avg_height 的基础上， 找到比这个表中的球员平均身高高的球员， 作为新的视图player_above_above_avg_height， 那么可以写成：
```sql
CREATE VIEW player_above_above_avg_height AS
SELECT player_id, height
FROM player
WHERE height > (SELECT AVG(height) from player_above_avg_height);
```
![嵌套视图.png](https://upload-images.jianshu.io/upload_images/16846478-67aeb1d8c41a881d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 修改视图：ALTER VIEW

```python
ALTER VIEW view_name AS
SELECT column1, column2
FROM table
WHERE condition
```
更新视图player_above_avg_height， 增加一个 player_name 字段， 可以写成：
```
ALTER VIEW player_above_avg_height AS
SELECT player_id, player_name, height
FROM player
WHERE height > (SELECT AVG(height) from player);
```
![修改视图.png](https://upload-images.jianshu.io/upload_images/16846478-66217116b80f7e2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 删除视图：DROP VIEW

```sql
DROP VIEW view_name
```
SQLite 不支持视图的修改， 仅支持只读视图， 只能使用 CREATE VIEW和 DROP VIEW， 如果想要修改视图， 就需要先 DROP 然后再 CREATE。

## 使用视图简化 SQL 操作

### 利用视图完成复杂的连接

NBA数据库中有两张表，分别为 player 和 height_grades。 其中 height_grades 记录了不同身高对应的身高等级。 这里可以通过创建视图， 来完成球员以及对应身高等级的查询，从而简化复杂的连接查询。

```sql
CREATE VIEW player_height_grades AS
SELECT p.player_name, p.height, h.height_level
FROM player as p JOIN height_grades as h
ON p.height BETWEEN h.height_lowest AND h.height_highest;
```
![利用视图完成复杂的连接.png](https://upload-images.jianshu.io/upload_images/16846478-ef69c6e66bd0ab7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 利用视图对数据进行格式化

想输出球员姓名和对应的球队， 对应格式为player_name(team_name)， 就可以使用视图来完成数据格式化的操作：

```sql
CREATE VIEW player_team AS 
SELECT CONCAT(player_name, '(' , team.team_name , ')') AS player_team 
FROM player JOIN team WHERE player.team_id = team.team_id;
```

![利用视图对数据进行格式化.png](https://upload-images.jianshu.io/upload_images/16846478-b15591bc8704bfa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 使用视图与计算字段

在数据查询中， 有很多统计的需求可以通过视图来完成。 正确地使用视图可以帮我们简化复杂的数据处理。

NBA数据库player_score 表中共有 19 个字段， 它们代表的含义如下：

![图片来自极客时间 SQL必知必会学习.png](https://upload-images.jianshu.io/upload_images/16846478-a8441f9c9c82a061.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

想要统计每位球员在每场比赛中的二分球、 三分球和罚球的得分， 可以通过创建视图完成：
```sql
CREATE VIEW game_player_score AS
SELECT game_id, player_id, (shoot_hits-shoot_3_hits)*2 AS 
shoot_2_points,shoot_3_hits*3 AS shoot_3_points, shoot_p_hits AS shoot_p_points, score 
FROM player_score;
```
![使用视图与计算字段.png](https://upload-images.jianshu.io/upload_images/16846478-446814036a617ab5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**视图VS临时表**
视图是虚拟表,临时表是实体表
临时表只在当前连接存在,关闭连接后,临时表就会自动释放。


![SQL中的视图.png](https://upload-images.jianshu.io/upload_images/16846478-da89737879c12b2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 参考资料：
**注：**
练习数据库使用 SQL必知必会专栏（极客时间）中的作者提供的 王者荣耀数据库以及NBA数据库
练习系统  MySQL Server version: 5.7.26-0ubuntu0.16.04.1 (Ubuntu)

*极客时间 SQL必知必会学习*

SQL必知必会专栏（极客时间）链接： 
http://gk.link/a/103Sm

《MySQL必知必会》学习笔记（19-23）:

[https://www.jianshu.com/p/33799dc97cd6](https://www.jianshu.com/p/33799dc97cd6)

此篇内容:插入数据、更新和删除数据、创建和操纵表、使用视图、使用存储过程




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
