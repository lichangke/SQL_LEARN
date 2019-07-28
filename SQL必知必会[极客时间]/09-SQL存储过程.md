目录链接：https://www.jianshu.com/p/2c104aaadb03

## 什么是存储过程

存储过程的英文是 Stored Procedure。 它的思想很简单， 就是 SQL 语句的封装。一旦存储过程被创建出来， 使用它就像使用函数一样简单， 我们直接通过调用存储过程名即可。 

### 存储过程定义
定义一个存储过程：
```sql
CREATE PROCEDURE 存储过程名称 ([参数列表])
BEGIN
    需要执行的语句
END    
```
使用 CREATE PROCEDURE 创建一个存储过程， 后面是存储过程的名称， 以及过程所带的参数， 可以包括输入参数和输出参数。 最后由 BEGIN 和 END 来定义我们所要执行的语句块。

和视图一样， 删除已经创建的存储过程， 使用DROP PROCEDURE 存储过程名称。更新存储过程， 需要使用 ALTER PROCEDURE 存储过程名称。

一个累加运算， 计算 1+2+…+n 等于多少的一个存储过程，其代码如下：

```sql
CREATE PROCEDURE `add_num`(IN n INT)
BEGIN
       DECLARE i INT;
       DECLARE sum INT;
       
       SET i = 1;
       SET sum = 0;
       WHILE i <= n DO
              SET sum = sum + i;
              SET i = i +1;
       END WHILE;
       SELECT sum;
END
```
如果MySql实现需要使用DELIMITER 来临时定义新的结束符，可参见 https://www.jianshu.com/p/33799dc97cd6

```sql
DELIMITER //
CREATE PROCEDURE `add_num`(IN n INT)
BEGIN
       DECLARE i INT;
       DECLARE sum INT;
       
       SET i = 1;
       SET sum = 0;
       WHILE i <= n DO
              SET sum = sum + i;
              SET i = i +1;
       END WHILE;
       SELECT sum;
END //
DELIMITER ;
```
先使用（//） 作为结束符， 在整个存储过程结束后采用了（//） 作为结束符号， 告诉 SQL 可以执行了， 然后再将结束符还原成默认的（;） 。

![存储过程.png](https://upload-images.jianshu.io/upload_images/16846478-dbcfb583b7c14821.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 存储过程的 3 种参数类型

![图片来自SQL必知必会专栏.png](https://upload-images.jianshu.io/upload_images/16846478-efaf52abe0d23f8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

王者荣耀的英雄数据表 heros。 假设创建一个存储类型 get_hero_scores，用来查询某一类型英雄中的最大的最大生命值， 最大的最大魔法值， 以及平均最大攻击值。

```sql
DELIMITER //
CREATE PROCEDURE `get_hero_scores`(
       OUT max_max_hp FLOAT,
       OUT min_max_mp FLOAT,
       OUT avg_max_attack FLOAT,  
       s VARCHAR(255)
       )
BEGIN
       SELECT MAX(hp_max), MIN(mp_max), AVG(attack_max) FROM heros WHERE role_main = s INTO max_max_hp, min_max_mp, avg_max_attack;
END //
DELIMITER ;
```
 4 个参数类型， 其中 3 个为 OUT 类型， 分别为 max_max_hp、 min_max_mp和 avg_max_attack， 另⼀个参数 s 为 IN 类型。

查询命令：

```sql
CALL get_hero_scores(@max_max_hp, @min_max_mp, @avg_max_attack, '战士');
SELECT @max_max_hp, @min_max_mp, @avg_max_attack;
```
![存储过程的 3 种参数类型.png](https://upload-images.jianshu.io/upload_images/16846478-eec3d8b8856d2e8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 流控制语句

流控制语句是用来做流程控制的

上述例子中用到了以下流控制语句

1. BEGIN…END：BEGIN…END 中间包含了多个语句， 每个语句都以（;） 号为结束符。
2. DECLARE：DECLARE 用来声明变量， 使用的位置在于 BEGIN…END 语句中间， 而且需要在其他语句使用之前进行变量的声明。
3. SET：赋值语句， 用于对变量进行赋值。
4. SELECT…INTO：把从数据表中查询的结果存放到变量中， 也就是为变量赋值。

还有一些常用的流控制语句：

1.IF…THEN…ENDIF：条件判断语句， 可以在 IF…THEN…ENDIF 中使用 ELSE 和 ELSEIF 来进行条件判断。

2.CASE：CASE 语句用于多条件的分支判断， 其语法是下面这样的。

```sql
CASE 
	WHEN expression1 THEN ...
	WHEN expression2 THEN ...
	...
    ELSE 
    --ELSE 语句可以加，也可以不加。加的话代表的所有条件都不满足时采用的方式。
END
```

3.LOOP、 LEAVE 和 ITERATE：LOOP 是循环语句， 使用LEAVE 可以跳出循环， 使用 ITERATE 则可以进入下一次循环。 LEAVE 相当于 BREAK， ITERATE 相当于 CONTINUE

4.REPEAT…UNTIL…END REPEAT：一个循环语句， 先会执行一次循环， 然后在 UNTIL 中进行表达式的判断， 如果满足条件就退出， 即 END REPEAT；如果条件不满足， 则会就继续执行循环， 直到满足退出条件。

5.WHILE…DO…END WHILE：这也是循环语句， 和 REPEAT 循环不同的是， 这个语句需要先进行条件判断， 如果满足条件就进入循环， 如果不满足条件就退出循环。

![13 SQL存储过程.png](https://upload-images.jianshu.io/upload_images/16846478-c72f96b79fdad281.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





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
