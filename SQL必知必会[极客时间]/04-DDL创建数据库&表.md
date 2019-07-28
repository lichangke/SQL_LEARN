
目录链接：https://www.jianshu.com/p/2c104aaadb03

## DDL 的基础语法

DDL， 英文叫做 Data Definition Language， 也就是数据定义语言， 它用来定义我们的数据库对象， 包括数据库、 数据表和列。 通过使用 DDL， 我们可以创建， 删除和修改数据库和表结构。

常用的功能是增删改， 分别对应的命令是 CREATE、 DROP 和 ALTER，不需要 COMMIT， 就可以完成执行任务。

### 数据库进行定义
```sql
CREATE DATABASE nba; -- 创建一个名为 nba 的数据库
DROP DATABASE nba; -- 删除一个名为 nba 的数据库
```
以mysql为例，注意 以 ； 作为结束符

![CREATEandDROP.png](https://upload-images.jianshu.io/upload_images/16846478-99811c2abc3eba77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 对数据表进行定义

```sql
CREATE TABLE table_name --先要 use 数据库名; 指定某一数据库
```
![use .png](https://upload-images.jianshu.io/upload_images/16846478-cc18b007d56eb665.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 创建表结构

在nba数据库中创建一个球员表，表名为 player，有两个字段，一个是 player_id， 它是 int 类
型可以用作主键， 另一个 player_name 字段是 varchar(255)类型。两个字段都不为空， 且 player_id 是递增的。

**主键（primary key）**
一列（或一组列），其值能够唯一区分表中每个行。
唯一标识表中每行的这个列（或这组列）称为主键。主键用来表示一个特定的行。没有主键，更新或删除表中特定行很困难，因为没有安全的方法保证只涉及相关的行。

```sql
CREATE TABLE player(
    player_id int(11) NOT NULL AUTO_INCREMENT,
    player_name varchar(255)  NOT NULL    -- 最后一个字段的定义结束后没有逗号
);
```
|参数|说明|
|--|--|
| int(11)|代表整数类型， 显示长度为 11 位,11代表最大有效显示长度 |
| varchar(255)| 代表的是最大长度为 255 的可变字符串类型|
|NOT NULL | 表明整个字段不能是空值， 是一种数据约束。|
| AUTO_INCREMENT |代表自动增长。 |

可以使用一些可视化工具来创建和操作数据库和数据表。Navicat， 它是一个数据库管理和设计工具， 跨平台， 支持很多种数据库管理软件， 如 MySQL、 Oracle、 MariaDB 等。 

**Navicat安装**

1.先去官网下载
[https://www.navicat.com.cn/download/navicat-premium](https://www.navicat.com.cn/download/navicat-premium)

2.下载后解压tar文件 
tar -zxvf navicat121_premium_cs_x64.tar.gz 

3.解压后 进入解压后的目录运行命令： 
./start_navicat

4.打开start_navicat文件，会看到 export LANG=”en_US.UTF-8” 将这句话改为 export LANG=”zh_CN.UTF-8”
![image.png](https://upload-images.jianshu.io/upload_images/16846478-25a33e19b68d9450.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5.查看系统支持的字符集: locale -a
6.修改字符集: export LANG=zh_CN.utf8 
![image.png](https://upload-images.jianshu.io/upload_images/16846478-84eac0f03769e317.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
7.还需要修改数据库字符集。
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
在 /etc/mysql/mysql.conf.d路径下的mysqld.cnf文件中
在[mysqld]段落中添加如下两行： 
character-set-server=utf8 
collation-server=utf8_general_ci
8.重启MySQL：sudo /etc/init.d/mysql restart 
然后查看数据库字符集设置： 
mysql -u root -p 
mysql> show variables like 'character_set_%'; 
![image.png](https://upload-images.jianshu.io/upload_images/16846478-18384c6243b22e81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 使用 Navicat 软件进行设计

假如还是针对 player 这张表， 我们想设计以下的字段：
![player .png](https://upload-images.jianshu.io/upload_images/16846478-e6e6ccd2b1c1abb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自于 极客时间 SQL必知必会 专栏

![Navicat .png](https://upload-images.jianshu.io/upload_images/16846478-47679810a8067c3d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们还可以对 player_name 字段进行索引， 索引类型为 Unique

![image.png](https://upload-images.jianshu.io/upload_images/16846478-fa2139242cb8d8da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过 在 Navicat 左侧右键选中 player 这张表， 然后选择“转储 SQL 文件”→“仅结构”， 这样就可以看到导出的 SQL 文件了
![image.png](https://upload-images.jianshu.io/upload_images/16846478-13c6d847911879c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

转存文件如下
![image.png](https://upload-images.jianshu.io/upload_images/16846478-d9f27b9a356fd4d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```sql
DROP TABLE IF EXISTS `player`;

CREATE TABLE `player`  (

  `player_id` int(11) NOT NULL,

  `team_id` int(11) NOT NULL,

  `player_name` varchar(255) CHARACTER SET latin1 COLLATE latin1_swedish_ci NOT NULL,

  `height` float(3, 2) NULL DEFAULT NULL,

  PRIMARY KEY (`player_id`) USING BTREE,

  UNIQUE INDEX `player_name`(`player_name`) USING BTREE

) ENGINE = InnoDB CHARACTER SET = latin1 COLLATE = latin1_swedish_ci ROW_FORMAT = Dynamic;
```
### 修改表结构
![image.png](https://upload-images.jianshu.io/upload_images/16846478-f8741689bc069df0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 添加字段， 如我在数据表中添加个 age 字段， 类型为 int(11)
```sql
ALTER TABLE player ADD (age int(11));
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-b5e98542ff0f2485.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 修改字段名， 将 age 字段改成 player_age
```sql
ALTER TABLE player CHANGE COLUMN age player_age int(11);
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-1dd2dab907937477.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3. 修改字段的数据类型， 将 player_age的数据类型设置为 float(3,1)
```sql
ALTER TABLE player MODIFY (player_age float(3,1))
```
4. 删除字段, 删除刚才添加的 player_age
```sql
ALTER TABLE player MODIFY player_age float(3,1);
```
![image.png](https://upload-images.jianshu.io/upload_images/16846478-bbf60843d711f85c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 数据表的常见约束

常见的约束有哪些。

首先是主键约束，其次还有外键约束。外键确保了表与表之间引用的完整性。 一个表中的外键对应另一张表的主键。 外键可以是重复的， 也可以为空。 除了对键进行约束外， 还有字段约束。
唯一性约束。
唯一性约束表明了字段在表中的数值是唯一的， 即使我们已经有了主键， 还可以对其他字段进行
唯一性约束。 

NOT NULL 约束。 对字段定义了 NOT NULL， 即表明该字段不应为空， 必须有取值。

DEFAULT， 表明了字段的默认值。 如果在插⼊数据的时候， 这个字段没有取值， 就设置为默认
值。 

CHECK 约束，用来检查特定字段取值范围的有效性， CHECK 约束的结果不能为 FALSE

![image.png](https://upload-images.jianshu.io/upload_images/16846478-5d155d0f1df815f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自于 极客时间 SQL必知必会 专栏


## 参考资料：

*极客时间 SQL必知必会学习*
    
SQL必知必会专栏（极客时间）链接： 
http://gk.link/a/103Sm

《MySQL必知必会》学习笔记 目录：
[https://www.jianshu.com/p/97bab08c6755](https://www.jianshu.com/p/97bab08c6755)

ubuntu下安装navicat的方法 :
[https://blog.csdn.net/ouzhuangzhuang/article/details/81739933](https://blog.csdn.net/ouzhuangzhuang/article/details/81739933)

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
