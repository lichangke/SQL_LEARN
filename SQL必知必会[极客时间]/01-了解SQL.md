
目录链接：https://www.jianshu.com/p/2c104aaadb03

SQL，指结构化查询语言，全称是 Structured Query Language。。工作中使用的类似 MySQL、 Oracle 这种的数据库管理系统， 实际上这些数据库管理系统都遵循 SQL 语言， 这就意味着， 我们在使用这些数据库的时候， 都是通过 SQL 语言与它们打交道。 

## SQL半衰期很长

45 年前， 也就是 1974 年， IBM 研究员发布了一篇揭开数据库技术的论文《SEQUEL：一门结构化的英语查询语言》 ， 直到今天这门结构化的查询语言并没有太大的变化， 相比于其他语言，SQL 的半衰期可以说是非常长了。

SQL有两个重要标准，分别是SQL92和SQL99，它们分别代表了92年和99年颁布的 SQL 标准， 我们今天使用的 SQL 语言依然遵循这些标准。 
有兴趣的可以了解 [https://blog.csdn.net/lengye7/article/details/80606489](https://blog.csdn.net/lengye7/article/details/80606489)

## SQL 入门并不难

SQL 不需要像其他语言那样， 学习起来需要大量的程序语言基础， SQL 更像是一门英语， 有一些简单的英语单词， 当你使用它的时候， 就好像在用英语与数据库进行对话。

SQL 语言按照功能可分为以下4个部分：

1、DDL， 英文叫做 Data Definition Language， 也就是数据定义语言， 它用来定义我们的数据库对象， 包括数据库、 数据表和列。 通过使用 DDL， 我们可以创建， 删除和修改数据库和表结构。

>数据定义语言DDL用来创建数据库中的各种对象-----表、视图、索引、同义词、聚簇等如:CREATE TABLE/ VIEW/ INDEX/ SYN/ CLUSTER  表 视图 索引 同义词 簇
>DDL操作是隐性提交的！不能rollback 

2、DML， 英文叫做 Data Manipulation Language， 数据操作语言， 我们用它操作和数据库相关的记录， 比如增加、 删除、 修改数据表中的记录。

>数据操纵语言DML主要有三种形式：
>1) 插入：INSERT
>2) 更新：UPDATE
>3) 删除：DELETE


3、DCL， 英文叫做 Data Control Language， 数据控制语言， 我们用它来定义访问权限和安全级别。

>数据控制语言DCL用来授予或回收访问数据库的某种特权，并控制数据库操纵事务发生的时间及效果，对数据库实行监视等。如：
>1) GRANT：授权。
>2) ROLLBACK [WORK] TO [SAVEPOINT]：回退到某一点。回滚---ROLLBACK回滚命令使数据库状态回到上次最后提交的状态。其格式为：SQL>ROLLBACK;
>3) COMMIT [WORK]：提交。*



4、DQL， 英文叫做 Data Query Language， 数据查询语言， 我们用它查询想要的记录， 它是SQL 语言的重中之重。 在实际的业务中， 我们绝大多数情况下都是在和查询打交道， 因此学会编写正确且高效的查询语句， 是学习的重点。

>数据查询语言DQL基本结构是由SELECT子句，FROM子句，WHERE
>子句组成的查询块：
>SELECT <字段名表>
>FROM <表或视图名>
>WHERE <查询条件>


SQL 是我们与 DBMS 交流的语言， 我们在创建 DBMS 之前， 还需要对它进行设计， 对于RDBMS来说采用的是 ER 图（Entity Relationship Diagram） ， 即实体 - 关系图的方式进行设计。

**E-R图即实体-联系图(Entity Relationship Diagram)**，是指提供了表示实体型、属性和联系的方法，用来描述现实世界的概念模型。

**实体型(Entity)**：具有相同属性的实体具有相同的特征和性质，用实体名及其属性名集合来抽象和刻画同类实体
**属性(Attribute)**：实体所具有的某一特性，一个实体可由若干个属性来刻画。
**联系(Relationship)**： 数据对象彼此之间相互连接的方式称为联系，也称为关系。
联系可分为以下 3 种类型： 
(1) 一对一联系 (1 ∶ 1) 
(2) 一对多联系 (1 ∶ N)
(3) 多对多联系 (M ∶ N) 



## 参考资料：

*极客时间 SQL必知必会学习*    
    
SQL必知必会专栏（极客时间）链接： 
http://gk.link/a/103Sm

DQL、DML、DDL、DCL的概念与区别:https://www.cnblogs.com/fan-yuan/p/7879353.html

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
