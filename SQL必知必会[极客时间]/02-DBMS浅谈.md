
目录链接：https://www.jianshu.com/p/2c104aaadb03

## 一些概念

数据库管理系统(Database Management System)是一种操纵和管理数据库的大型软件，用于建立、使用和维护数据库，简称DBMS。

**DB：**
DB 的英文是 DataBase， 也就是数据库。 数据库是存储数据的集合， 你可以把它理解为多个数据表。

**DBS:**
DBS 的英文是 DataBase System， 数据库系统。 它是更大的概念， 包括了数据库、 数据库管理系统以及数据库管理人员 DBA。

**DBMS:**
DBMS 的英文全称是 DataBase Management System， 数据库管理系统， 实际上它可以对多个数据库进行管理， 所以你可以理解为 DBMS = 多个数据库（DB） + 管理程序。

**区别**：数据库就是存放数据的仓库，数据库管理系统就是用来管理数据库的，其中数据库系统是一个通称，包括数据库、数据库管理系统、数据库管理人员等的统称，是最大的范畴。

我们有时候把 Oracle、 MySQL 等称之为数据库， 但确切讲， 它们应该是数据库管理系统， 即 DBMS。


## 常见DBMS 
排名前 20 的 DBMS 都是哪些

下面这张表是 2019 年 5 月DB-Engines 公布的 DBMS 的排名（每年的排名会有更新， 主要依据这些 DBMS 在搜索引擎上的热度） ：

![image.png](https://upload-images.jianshu.io/upload_images/16846478-096a8330b0800299.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注：图片来自于 极客时间 SQL必知必会 专栏

从排名中我们能看出来， 关系型数据库绝对是 DBMS 的主流， 其中使用最多的 DBMS 分别是 Oracle、 MySQL 和 SQL Server。

《MySQL必知必会》学习笔记可参见：
[https://www.jianshu.com/p/d6e5c152d89f](https://www.jianshu.com/p/d6e5c152d89f)

**关系型数据库（RDBMS）**

所谓的关系型数据库，是建立在关系模型基础上的数据库，借助于集合代数等数学概念和方法来处理数据库中的数据。

**NoSQL 泛指非关系型数据库**

NoSQL 泛指非关系型数据库，包括了榜单上的键值型数据库、 ⽂档型数据库、 搜索
引擎和列存储等， 除此以外还包括图形数据库。

*关系型数据库与非关系型数据库区别*

可参见[常见的几个非关系型数据库(NoSQL)、非关系型和关系型的区别](https://blog.csdn.net/weixin_39805338/article/details/80893838)

![image.png](https://upload-images.jianshu.io/upload_images/16846478-af6c85b1c312d17c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



**键值型数据库**

键值型数据库通过 Key-Value 键值的方式来存储数据， 其中 Key 和 Value可以是简单的对象， 也可以是复杂的对象。 Key 作为唯一的标识符，优点是查找速度快， 在这方面明显优于关系型数据库， 同时缺点也很明显， 它无法像关系型数据库一样自由使用条件过滤（比如 WHERE） ， 如果你不知道去哪里找数据， 就要遍历所有的键， 这就会消耗大量的计算。

临时性键值存储：Memcached，Redis

永久性键值存储：ROMA，Redis

应用场景：内容缓存，主要用于处理大量数据的高访问负载，也用于一些日志系统等等

数据模型：Key指向Value的键值对，通常用HashTable来实现

优点：查找速度快

缺点：数据无结构化，通常只被当做字符串或者是二进制数据


**文档型数据库**

文档型数据库用来管理文档， 在数据库中文档作为处理信息的基本单位，一个文档就相当于一条记录， MongoDB 是最流行的文档型数据库。

应用场景：WEB应用（与key-value类似，value是结构化的，不同的是数据库能够了解到value的内容）

数据模型：Key-Value对应的键值对，Value是结构化的数据

优点：数据结构要求不严格，表结构可变，不需要像关系型数据库一样需要预先定义表结构

缺点：查询性能不高，而且缺乏统一的查询语法



**列式数据库**

列式数据库是相对于行式存储的数据库， Oracle、 MySQL、 SQL Server 等数据库都是采用的行式存储（Row-based） ， 而列式数据库是将数据按照列存储到数据库中， 这样做的好处是可以大量降低系统的 I/O， 适合于分布式文件系统， 不足在于功能相对有限。

应用场景：分布式的文件系统

数据模型：以列簇式存储，将一列数据存储在一起

优点：查找速度快，可扩展性强，更容易进行分布式扩展

缺点：功能相对局限



**图形数据库**

图形数据库， 利用了图这种数据结构存储了实体（对象） 之间的关系。 最典型的例子就是社交网络中人与人的关系， 数据模型主要是以节点和边（关系） 来实现， 特点在于能高效地解决复杂的关系问题。

应用场景：社交网络，推荐系统等，专注于构建关系图谱

数据模型：图结构

优点：利用图结构相关算法。比如最短路径寻址，N度关系查找等等。

缺点：很多时候要对整个图做计算才能得出需要的信息，而且这种结构不太好做分布式的集群方案。


## SQL 阵营中的 DBMS

三个主流 DBMS 的发展

**Oracle**

1979 年， Oracle 2 诞⽣， 它是第一个商用的 RDBMS（关系型数据库管理系统） ， 随后被卖给了军方客户。 随着 Oracle 软件的名气越来越大， 公司也改叫 Oracle 公司。 扩展阅读[https://blog.csdn.net/suwu150/article/details/52529942](https://blog.csdn.net/suwu150/article/details/52529942)

**MySQL**

MySQL 是 1995 年诞生的开源数据库管理系统， 因为免费开源的特性，得到了开发者的喜爱， 用户量迅速增长， 成为开源数据库的 No.1。但在发展过程中， MySQL 先后两次被易手， 先是在2008 年被 SUN 收购， 然后在 2010 年 SUN 被 Oracle 收购，
扩展阅读[https://blog.csdn.net/zyj66666/article/details/74003041](https://blog.csdn.net/zyj66666/article/details/74003041)


**SQL Server**

SQL Server 是微软开发的商业数据库， 诞生于 1989 年。 SQL Server 是大型数据库， 用于后台的存储和查询， 不具备界面开发的功能。 
扩展阅读[https://blog.csdn.net/Hehuyi_In/article/details/90412426](https://blog.csdn.net/Hehuyi_In/article/details/90412426)


![image.png](https://upload-images.jianshu.io/upload_images/16846478-b6bf8c1d4c0a9cfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注：图片来自于 极客时间 SQL必知必会 专栏

## 参考资料：

*极客时间 SQL必知必会学习*
    
SQL必知必会专栏（极客时间）链接：
 http://gk.link/a/103Sm

[常见的几个非关系型数据库(NoSQL)、非关系型和关系型的区别](https://blog.csdn.net/weixin_39805338/article/details/80893838)

[关于NoSql的理解，键值数据库，文档数据库，列式存储数据库，图形数据库](https://blog.csdn.net/dyt443733328/article/details/80090383)

[Oracle数据库发展历史](https://blog.csdn.net/suwu150/article/details/52529942)

[MySQL的发展历程](https://blog.csdn.net/zyj66666/article/details/74003041)

[SQL Server 发展历史](https://blog.csdn.net/Hehuyi_In/article/details/90412426)

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



