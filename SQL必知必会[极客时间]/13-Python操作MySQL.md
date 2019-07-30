目录链接：https://www.jianshu.com/p/2c104aaadb03

## Python DB API 规范

Python 可以支持非常多的数据库管理系统， 比如 MySQL、 Oracle、 SQL Server 和PostgreSQL 等。 为了实现对这些 DBMS 的统一访问， Python 需要遵守一个规范， 这就是 DB API 规范。 这个规范给我们提供了数据库对象连接、对象交互和异常处理的方式， 为各种 DBMS 提供了统一的访问接口。 这样做的好处就是如果项目需要切换数据库， Python 层的代码移植会比较简单。

![图片来自极客时间SQL必知必会专栏.png](https://upload-images.jianshu.io/upload_images/16846478-67cb6a3f272b1be3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用 Python 对 DBMS 进行操作需要经过下面的几个步骤：
1. 引入 API 模块；
2. 与数据库建立连接；
3. 执行SQL 语句；
4. 关闭数据库连接。

## 如何使用 mysql-connector
使用Python 对数据库进行访问需要基于 DB API 规范，可以使用MySQLdb、 mysqlclient、 PyMySQL、 peewee 和 SQLAIchemy 等这些库。mysql-connector是 MySQL 官方提供的驱动器， 用来给后端语言， ⽐如 Python 提供连接。
安装 mysql-connector
```
pip install mysql-connector
```

```python
# -*- coding: UTF-8 -*-
import mysql.connector
# 打开数据库连接
db = mysql.connector.connect(
    host="127.0.0.1",
    port=3306,
    user="root",
    passwd="XXXXXX",  # 写上你的数据库密码
    database="NBA",
    auth_plugin="mysql_native_password"
)
# 获取操作游标
cursor = db.cursor()
# 执行 SQL 语句
cursor.execute("SELECT VERSION()")
# 获取一条数据
data = cursor.fetchone()
print("MySQL 版本: %s " % data)
# 关闭游标 & 数据库连接
cursor.close()
db.close()

"""
如果报auth_plugin="mysql_native_password" 错误，使用下面命令
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'XXXXXXX';
```
![使用 mysql-connector.png](https://upload-images.jianshu.io/upload_images/16846478-29300ff9f2064194.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Connection 就是对数据库的当前连接进行管理：

1. 通过指定 host、 user、 passwd 和 port 等参数来创建数据库连接， 这些参数分别对应着数据库 IP 地址、 用户名、 密码和端口号；
2. 使用 db.close() 关闭数据库连接；
3. 使用 db.cursor() 创建游标， 操作数据库中的数据；
4. 使用 db.begin() 开启事务；
5. 使用 db.commit() 和 db.rollback()， 对事务进行提交以及回滚。

通过 cursor = db.cursor() 创建游标后， 通过如下命令对数据库中的数据进行操作：

1. 使用 cursor.execute(query_sql) ， 执行数据库查询；
2. 使用 cursor.fetchone() ， 读取数据集中的一条数据；
3. 使用 cursor.fetchall() ， 取出数据集中的所有行， 返回一个元组 tuples 类型；
4. 使用 cursor.fetchmany(n) ， 取出数据集中的多条数据， 同样返回一个元组 tuples；
5. 使用 cursor.rowcount ， 返回查询结果集中的行数。 如果没有查询到数据或者还没有查询， 则结果为 -1， 否则会返回查询得到的数据行数；
6. 使用 cursor.close() ， 关闭游标。

## 对数据表进行增删改查

### 增加数据

```python
# 插入新球员
sql = "INSERT INTO player (team_id, player_name, height) VALUES (%s, %s, %s)"  # 用（%s） 进行占位
val = (1003, " 约翰 - 科林斯 ", 2.08)
cursor.execute(sql, val)
db.commit()   # 使用 db.commit() 进行提交
print(cursor.rowcount, " 记录插入成功。")
```
![增加数据.png](https://upload-images.jianshu.io/upload_images/16846478-7420b0bd7300e220.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用 cursor.execute 来执行相应的 SQL 语句， val 为 SQL 语句中的参数， SQL 执行后使用db.commit() 进行提交。在使用 SQL 语句的时候， 可以向 SQL 语句传递参数， 这时 SQL 语句里要统一用（%s） 进行占位， 否则就会报错。 不论插入的数值为整数类型， 还是浮点类型， 都需要统统一用（%s） 占位。

游标进行SQL 操作之后， 还需要使用 db.commit() 进行提交， 否则数据不会被插。

### 修改数据

```python
# 修改球员约翰 - 科林斯
sql = 'UPDATE player SET height = %s WHERE player_name = %s'
val = (2.09, " 约翰 - 科林斯 ")
cursor.execute(sql, val)
db.commit()
print(cursor.rowcount, " 记录被修改。")
```
![修改数据.png](https://upload-images.jianshu.io/upload_images/16846478-fd9d9539073f6b92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 删除数据

```python
sql = 'DELETE FROM player WHERE player_name = %s'
val = (" 约翰 - 科林斯 ",)
cursor.execute(sql, val)
db.commit()
print(cursor.rowcount, " 记录删除成功。")
```

![删除数据.png](https://upload-images.jianshu.io/upload_images/16846478-05435f7d60d044d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在对数据进行增加、 删除和修改的时候， 可能会出现异常， 这时就需要用 try...except 捕获异常信息。 （上面代码未处理）

![Python操作MySQL.png](https://upload-images.jianshu.io/upload_images/16846478-6ff60719a6667f4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 参考资料：
练习数据库使用 SQL必知必会专栏（极客时间）中的作者提供的 王者荣耀数据库以及NBA数据库
练习系统  MySQL Server version: 5.7.26-0ubuntu0.16.04.1 (Ubuntu)

SQL必知必会专栏（极客时间）链接： 
http://gk.link/a/103Sm

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