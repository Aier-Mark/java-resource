# SQL性能优化

## 1、SELECT 子句

​	SELECT 子句中避免使用 ‘ * ‘ ;

## 2、使用表的别名 (Alias)  

​	当在 SQL 语句中连接多个表时 , 请使用表的别名并把别名前缀于每个 Column 上 。这样一来 , 就可以减少解析的时间并减少那些由Column 歧义引起的语法错误 。

## 3、用 >= 替代 > 

高效 : 

​	SELECT * FROM  EMP E  WHERE  DEPTNO >=4 
低效 : 
	SELECT * FROM EMP WHERE DEPTNO >3  

两者的区别在于 , 前者 DBMS 将直接跳到第一个 DEPT 等于 4 的记录，而后者将首先定位到 DEPTNO=3 的记录并且向前扫描到第一个 DEPT 大于 3 的记录。

注意：项目中需根据实际情况而定。 

## 4、GROUP BY

​	提高 GROUP BY 语句的效率 , 可以通过将不需要的记录在 GROUP BY 之前过滤掉。下面两个查询返回相同结果但第二个明显就快了许多。

低效 ：

```SQL
SELECT JOB, AVG(SAL) FROM EMP 
GROUP BY JOB 
HAVING JOB = 'PRESIDENT' OR JOB = 'MANAGER';
```

高效：

```SQL
SELECT JOB, AVG(SAL) FROM EMP 
WHERE JOB = 'PRESIDENT' OR JOB = 'MANAGER' 
GROUP BY JOB;
```

备注：避免使用HAVING子句，HAVING 只会在检索出所有记录之后才对结果集进行过滤.。这个处理需要排序，总计等操作。如果能通过WHERE子句限制记录的数目，那就能减少这方面的开销，提高效率。

## 5、SQL文大写

​	SQL文在编译执行的时候先把SQL文转换为大写，然后再执行。所以开发过程中SQL大写，能提高执行效率。

## 6、IN 操作符（和exists）

 解释：用IN写出来的SQL的优点是比较容易写以及 清晰易懂，这比较适合现代软件开发的风格。但是用IN的SQL性能总是比较低的。

推荐方案：EXISTS代替。子查询时,但要结合实际情况。

1. 如果查询的两个表大小相当，那么用in和exists差别不大；
2. 如果两个表中一个较小，一个是大表，则子查询表大的用exists，子查询表小的用in。

例如：表A（小表），表B（大表）

- 效率低：select * from A where cc in (select cc from B)  用到了A表上cc列的索引；
- 效率高：select * from A where exists(select cc from B where cc=A.cc)  用到了B表上cc列的索引。

相反的：表A（大表），表B（小表）：

- 效率高：select * from B where cc in (select cc from A)  用到了B表上cc列的索引；
- 效率低：select * from B where exists(select cc from A where cc=B.cc)  用到了A表上cc列的索引。

综上：如果两个表中一个较小，一个是大表，则子查询表大的用exists，子查询表小的用in。

## 7、UNION操作符

​	UNION操作符用于连接两个以上的SELECT 语句的结果组合到一个结果集合中。多个SELECT 语句会删除重复语句。

解释：UNION在进行表链接后会筛选掉重复的记录，所以在表链接后会对所产生的结果集进行排序运算，删除重复的记录再返回结果。

推荐方案：采用UNION ALL操作符替代UNION，因为UNION ALL操作只是简单的将两个结果合并后就返回。

注意：项目中根据实际情况而定，看是否需要去重。