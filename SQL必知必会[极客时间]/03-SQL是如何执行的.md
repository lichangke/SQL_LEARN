
目录链接：https://www.jianshu.com/p/2c104aaadb03

## Oracle 中的 SQL 是如何执行的

更多可以参看 oracle 官方文档 关于 About SQL Processing [https://docs.oracle.com/database/121/TGSQL/tgsql_sqlproc.htm#TGSQL175](https://docs.oracle.com/database/121/TGSQL/tgsql_sqlproc.htm#TGSQL175)

看下 SQL 在 Oracle 中的执行过程：

![image.png](https://upload-images.jianshu.io/upload_images/16846478-2c77b99411aebfa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注：图片来自于 极客时间 SQL必知必会 专栏

这个和官方文档中流程图基本一样
![image.png](https://upload-images.jianshu.io/upload_images/16846478-e7729f73acb7f6d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面的图中可以看出， SQL 语句在 Oracle 中经历了以下的几个步骤
*例子来自官方文档*
1、语法检查：检查 SQL 拼写是否正确， 如果不正确， Oracle 会报语法错误。
```sql
SQL> SELECT * FORM employees;
SELECT * FORM employees
         *
ERROR at line 1:
ORA-00923: FROM keyword not found where expected
```
2. 语义检查：检查 SQL 中的访问对象是否存在语句是否有意义。 比如我们在写 SELECT 语句的时候， 列名写错了， 系统就会提示错误。 语法检查和语义检查的作用是保证 SQL 语句没有错误。
```sql
SQL> SELECT * FROM nonexistent_table;
SELECT * FROM nonexistent_table
              *
ERROR at line 1:
ORA-00942: table or view does not exist
```
3. 权限检查：看用户是否具备访问该数据的权限。

4. 共享池检查：共享池（Shared Pool） 是一块内存池， 最主要的作用是缓存 SQL 语句和该语句的执行计划。Oracle 通过检查共享池是否存在 SQL 语句的执行计划， 来判断进行软解析， 还是硬解析。 
**那软解析和硬解析应该怎么理解呢？**
在共享池中， Oracle 首先对 SQL 语句进行 Hash 运算， 然后根据 Hash 值在库缓存（Library Cache） 中查找， 如果存在 SQL 语句的执行计划， 就直接拿来执行， 直接进行“执行器”的环节， 这就是软解析。如果没有找到 SQL 语句和执行计划， Oracle 就需要创建解析树进行解析， 生成执行计划，进行“优化器”这个步骤， 这就是硬解析。

![image.png](https://upload-images.jianshu.io/upload_images/16846478-3752cee205a87ebd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
图片来自 官方文档

5. 优化器：优化器中就是要进行硬解析， 也就是决定怎么做， 比如创建解析树， 生成执行计划。
在优化阶段，Oracle数据库必须至少对每个唯一的DML语句执行一次硬解析，并在此解析期间执行优化。 数据库永远不会优化DDL，除非它包含DML组件，例如需要优化的子查询。
关于DDL DML可参见 [https://www.jianshu.com/p/8ead960d2079](https://www.jianshu.com/p/8ead960d2079)


6. 执行器：当有了解析树和执行计划之后， 就知道了 SQL 该怎么被执行， 这样就可以在执行器中执行语句了。

**共享池** 是 Oracle 中的术语， 包括了库缓存， 数据字典缓冲区等。 库缓存区，它主要缓存 SQL 语句和执行计划。而数据字典缓冲区存储的是 Oracle 中的对象定义， 比如表、视图、 索引等对象。 当对 SQL 语句进行解析的时候， 如果需要相关的数据， 会从数据字典缓冲区中提取。

为了提升 SQL 的执行效率， 我们应该尽量避免硬解析， 因为在 SQL 的执行过程中， 创建解析树， 生成执行计划是很消耗资源的。如何避免硬解析， 尽量使行软解析呢？在 Oracle 中， 绑定变量是它的一大特色。 绑定变量的实质就是用于替代sql语句中的常量的替代变量。绑定变量能够使得每次提交的sql语句都完全一样。

绑定变量相关可以参看： 
[https://blog.csdn.net/wanghai__/article/details/4778343](https://blog.csdn.net/wanghai__/article/details/4778343)

## MySQL 中的 SQL 是如何执行的

首先 MySQL 是典型的 C/S 架构， 即 Client/Server 架构， 服务器端程序使用的 mysqld。 整体的
MySQL 流程如下图所示：
![image.png](https://upload-images.jianshu.io/upload_images/16846478-4107814304082b2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自于 极客时间 SQL必知必会 专栏
或者 
![image.png](https://upload-images.jianshu.io/upload_images/16846478-1887d7a4284541cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自 《当我们输入一条 SQL 查询语句时，发生了什么？》，文章链接见参考资料


能看到 MySQL 由三层组成：

1. 连接层：客户端和服务器端建立连接， 客户端发送 SQL 至服务器端；
2. SQL 层：对 SQL 语句进行查询处理；
3. 存储引擎层：与数据库文件打交道， 负责数据的存储和读取。

**SQL 层的结构**
![image.png](https://upload-images.jianshu.io/upload_images/16846478-9bc4489ea1e87d19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自于 极客时间 SQL必知必会 专栏

1. 查询缓存：
Server 如果在查询缓存中发现了这条 SQL 语句， 就会直接将结果返回给客户端；如果没有， 就进行到解析器阶段。 需要说明的是， 因为查询缓存往往效率不高， 所以在MySQL8.0 之后就抛弃了这个功能。查询缓存的失效非常频繁，只要有对一个表的更新，这个表上所有的查询缓存都会被清空。因此很可能你费劲地把结果存起来，还没使用呢，就被一个更新全清空了。对于更新压力大的数据库来说，查询缓存的命中率会非常低。
2. 解析器：
在解析器中对 SQL 语句进行语法分析、 语义分析。
3. 优化器：
在优化器中会确定 SQL 语句的执行路径， 比如是根据全表检索， 还是根据索引来检索等。优化器是在表里面有多个索引的时候，决定使用哪个索引；或者在一个语句有多表关联（join）的时候，决定各个表的连接顺序。
4. 执行器：在执行之前需要判断该用户是否具备权限， 如果具备权限就执行 SQL 查询并返回
结果。 在 MySQL8.0 以下的版本， 如果设置了查询缓存， 这时会将查询结果进行缓存。

与 Oracle 不同的是， MySQL 的存储引擎采用了插件的形式， 每个存储引擎都面向一种特定的数据库应用环境。

**存储引擎**
|存储引擎|特点|
|--------|----------|
|InnoDB 存储引擎|它是 MySQL 5.5.8 版本之后默认的存储引擎， 最大的特点是支持事务、级锁定、 外键约束等。|
|MyISAM 存储引擎|在 MySQL 5.5.8 版本之前是默认的存储引擎， 不支持事务， 也不支持外键， 最大的特点是速度快， 占用资源少。|
|Memory 存储引擎|使用系统内存作为存储介质， 以便得到更快的响应速度。 |
|NDB 存储引擎|也叫做 NDB Cluster 存储引擎，主要用于 MySQL Cluster 分布式集群环境， 类似于 Oracle 的 RAC 集群。|
|Archive 存储引擎|它有很好的压缩机制， 用于文件归档， 在请求写入时会进行压缩， 所以也经常用来做仓库。|

不同的存储引擎都有各自的特点，以适应不同的需求，如下表所示：
|功  能|	MYISAM|Memory	|InnoDB	|Archive|
|--|--|--|--|--|
|存储限制|	256TB|	RAM	|64TB|	None|
|支持事物|	No|	No	|Yes|	No|
|支持全文索引|	Yes	|No|	No	|No|
|支持数索引|	Yes|	Yes|	Yes|	No|
|支持哈希索引|	No|	Yes	|No	|No|
|支持数据缓存|	No	|N/A	|Yes	|No|
|支持外键|	No	|No	|Yes	|No|

实际上完整的 Oracle 和 MySQL结构图要复杂得多：

![image.png](https://upload-images.jianshu.io/upload_images/16846478-a37c1080acb10d36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自于 极客时间 SQL必知必会 专栏

可参看：
[Oracle--数据库体系结构（物理结构、逻辑结构、内存结构及后台进程）](https://blog.csdn.net/oChangWen/article/details/51171992)

![image.png](https://upload-images.jianshu.io/upload_images/16846478-143b501542e9c071.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自于 极客时间 SQL必知必会 专栏

可参看：
 [MySQL体系结构](https://www.cnblogs.com/yjf512/archive/2012/02/06/2339496.html)



![image.png](https://upload-images.jianshu.io/upload_images/16846478-0d5c0493d7c037fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
注：图片来自于 极客时间 SQL必知必会 专栏

## 参考资料：

*极客时间 SQL必知必会学习*
    
SQL必知必会专栏（极客时间）链接： 
http://gk.link/a/103Sm

[Oracle ](https://docs.oracle.com/database/121/index.htm) 官方文档 Database SQL Tuning Guide中:
[https://docs.oracle.com/database/121/TGSQL/tgsql_sqlproc.htm#TGSQL176](https://docs.oracle.com/database/121/TGSQL/tgsql_sqlproc.htm#TGSQL176)

ORACLE 绑定变量用法总结：
[https://blog.csdn.net/wanghai__/article/details/4778343](https://blog.csdn.net/wanghai__/article/details/4778343)

当我们输入一条 SQL 查询语句时，发生了什么？：
[https://www.infoq.cn/article/PKzT75BPcryCYJ_VuWrR](https://www.infoq.cn/article/PKzT75BPcryCYJ_VuWrR)


Mysql 存储引擎的区别和比较:
[https://blog.csdn.net/zgrgfr/article/details/74455547](https://blog.csdn.net/zgrgfr/article/details/74455547)

四种mysql存储引擎:
[https://blog.csdn.net/zhangyuan19880606/article/details/51217952](https://blog.csdn.net/zhangyuan19880606/article/details/51217952)

Oracle--数据库体系结构（物理结构、逻辑结构、内存结构及后台进程）：
[https://blog.csdn.net/oChangWen/article/details/51171992](https://blog.csdn.net/oChangWen/article/details/51171992)

MySQL体系结构：
[https://www.cnblogs.com/yjf512/archive/2012/02/06/2339496.html](https://www.cnblogs.com/yjf512/archive/2012/02/06/2339496.html)


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
