目录链接：https://www.jianshu.com/p/2c104aaadb03

## 为什么要使用ORM 框架

![图片来自极客时间SQL必知必会专栏.png](https://upload-images.jianshu.io/upload_images/16846478-89b8bc331ee402f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 ORM提供了一种持久化模式， 可以高效地对数据库进行访问。 ORM 的英文是 Object Relation Mapping，对象关系映射，是 RDBMS 和业务实体对象之间的一个映射，把底层的 RDBMS 封装成业务实体对象，提供给业务逻辑层使用。 好处:简单可复用,不用关注底层的数据库访问细节,注意力关注到业务逻辑层。不足:对于复杂的数据查询,采用ORM会力不从心,同时在效率上也会有损耗。

## Python 中的 ORM 框架都有哪些

三种主流的 ORM 框架： Django、SQLALchemy、peewee

**Django**
Django， 它是 Python 的 WEB 应用开发框架， 本身是大而全的方式。 Django 采用了 MTV 的框架模式， 包括了 Model（模型） ， View（视图）和 Template（模版）Model 模型只是 Django 的一部分功能， 我们可以通过它来实现数据库的增删改查操作。Model 映射到一个数据表，如下图：

![图片来自极客时间SQL必知必会专栏.png](https://upload-images.jianshu.io/upload_images/16846478-c00cd7c686027bc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**SQLALchemy**

SQLALchemy，它提供了 SQL 工具包及 ORM工具，支持 ORM 和支持原生SQL 

**peewee**
一个轻量级的 ORM 框架。peewee 采用了 Model 类、 Field 实例和 Model 实例来与数据库建立映射关系， 从而完成面向对象的管理方式。

## 使用 SQLAlchemy 来操作 MySQL

### 安装相应的工具包：
```
pip install sqlalchemy
初始化数据库连接
from sqlalchemy import create_engine
# 初始化数据库连接，修改为你的数据库用户名和密码
engine = create_engine('mysql+mysqlconnector://户名: 密码@IP地址:端口号/数据库名称')
```
### 创建模型
```python
## 引用数据类型
from sqlalchemy import Column, String, Integer, Float
from sqlalchemy.ext.declarative import declarative_base

Base = declarative_base()
# 定义 Player 对象:
class Player(Base):
    # 表的名字:
    __tablename__ = 'player'

    # 表的结构:
    player_id = Column(Integer, primary_key=True, autoincrement=True)
    team_id = Column(Integer)
    player_name = Column(String(255))
    height = Column(Float(3, 2))
```
__tablename__ 指明了模型对应的数据表名称，在 Player 模型中对采用的变量名进行定义， 变量名需要和数据表中的字段名称保持一致，采用 Column 对字段进行定义

![常用的数据类型.png](https://upload-images.jianshu.io/upload_images/16846478-7aa2b9bde7473daa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

除了指定 Column 的数据类型以外， 我们也可以指定 Column 的参数， 这些参数可以帮我们对对象创建列约束：

![对象创建列约束.png](https://upload-images.jianshu.io/upload_images/16846478-d3b65d2860171844.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

引用数据类型

```python
from sqlalchemy import Column, String, Integer, Float
```

### 对数据表进行增删改查

**增**

```python
from sqlalchemy.orm import sessionmaker
# 创建 DBSession 类型:
DBSession = sessionmaker(bind=engine)
# 创建 session 对象:
session = DBSession()

# 创建 Player 对象:
new_player = Player(team_id = 1003, player_name = " 约翰 - 科林斯 ", height = 2.08)
# 添加到 session:
session.add(new_player)
# 提交即保存到数据库:
session.commit()
# 关闭 session:
session.close()
```
首先需要初始化 DBSession， 相当于创建一个数据库的会话实例 session。 通过 session 来完成新球员的添加。 

通过 Player 类来完成数据创建， 在参数中指定相应的 team_id, player_name, height 即可。

然后把创建好的对象 new_player 添加到 session 中， 提交到数据库即可完成添加数据的操作。
![增.png](https://upload-images.jianshu.io/upload_images/16846478-03d11cfbbcf95483.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*查*
```python

# 增加 to_dict() 方法到 Base 类中
def to_dict(self):
    return {c.name: getattr(self, c.name, None)
            for c in self.__table__.columns}
# 创建 DBSession 类型:
DBSession = sessionmaker(bind=engine)
# 创建 session 对象:
session = DBSession()
# 将对象可以转化为 dict 类型
Base.to_dict = to_dict
# 查询身高 >=2.08 的球员 同时 ≤ 2.10 的球员有哪些， AND 的关系
# rows = session.query(Player).filter(Player.height >=2.08, Player.height <=2.10).all()
# 查询身高 >=2.08 的球员 或 ≤ 2.10 的球员有哪些 OR 的关系
# from sqlalchemy import or_ 
# rows = session.query(Player).filter(or_(Player.height >=2.08, Player.height <=2.10)).all()
rows = session.query(Player).filter(Player.height >= 2.08).all()
print([row.to_dict() for row in rows])
session.close()
```
![查.png](https://upload-images.jianshu.io/upload_images/16846478-b671b94ae815d9ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对整个数据行进行查询， 采用 session.query(Player) ， 相当于使用的是 SELECT *。
使用 filter 方法， 对应的是 SQL 中的 WHERE 条件查询。filter 也支持多条件查询。

**删**

我们需要进行查询， 然后从 session 对象中进行删除， 最后进行 commit 提交
```python
# 创建 DBSession 类型:
DBSession = sessionmaker(bind=engine)
# 创建 session 对象:
session = DBSession()
row = session.query(Player).filter(Player.player_name == '约翰 - 科林斯').first()
session.delete(row)
session.commit()
session.close()
```
![删.png](https://upload-images.jianshu.io/upload_images/16846478-8b3870bd508058af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**改**
先进行查询， 然后再进行修改
```python
# 创建 DBSession 类型:
DBSession = sessionmaker(bind=engine)
# 创建 session 对象:
session = DBSession()
row = session.query(Player).filter(Player.player_name=="约翰 - 科林斯 ").first()
row.height = 2.17
session.commit()
session.close()
```

![改.png](https://upload-images.jianshu.io/upload_images/16846478-4321c1cf908ac028.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



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
