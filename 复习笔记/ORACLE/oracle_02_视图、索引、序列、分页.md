# 1 视图（View）

## 1.1 前言

数据是存储在表中，对数据的操作主要是通过表进行的。但是仅仅通过对表操作数据会带来一系列的性能、安全、效率等问题。 

解决上述问题的一个有效手段就是视图。视图可以把表中分散的数据集成起来，可以让操作人员通过视图而不是通过表来访问数据。

## 1.2 视图

视图是由一个或若干个基表产生的数据的集合，它不占用存储空间。

视图犹如数据表的窗户，管理员定义这些“窗户”的位置后，用户就只能查看他可以看到的数据。视图不是数据表，它仅是一些SQL查询语句的集合，作用是按照不同的要求从数据表中提取不同的数据。

视图与表不同，视图是一个虚表，即视图所对应的数据不进行实际存储，数据库中只存储视图的定义，对视图的数据进行操作时，系统根据视图的定义去操作与视图相关联的表。 

## 1.3 视图与表的区别

相同之处：

1. 视图和表一样由列组成，其查询方式与表完全相同。
2. 和表一样，用户也可以在视图中插入、更新或删除数据，在视图中做这些操作时与在表中是一样的。

区别：

1. 与表不同，视图中没有数据，而仅仅是一条SQL查询语句。按此查询语句检索出的数据以表的形式表示。视图中的列可以在一个或多个基本表中找到。视图不使用物理存储位置来存储数据。
2. 视图的定义（列的安排、授予的权限等等）存储在数据字典中。对一个视图进行查询时，视图将查询其基于的表，并且以视图定义所规定的格式和顺序返回值。
3. 由于视图没有直接相关的物理数据，所以不能像表那样被索引。

## 1.4 创建视图

语法：

```SQL
CREATE [OR REPLACE] [FORCE|NOFORCE] VIEW view_name [(alias[, alias]...)] 
AS subquery 
[WITH CHECK OPTION [CONSTRAINT constraint]] 
[WITH READ ONLY]
```

语法解析：

- OR REPLACE：若所创建的试图已经存在，则替换旧视图；
- FORCE：不管基表是否存在ORACLE都会自动创建该视图(即使基表不存在，也可以创建该视图，但是该视图不能正常使用，当基表创建成功后，视图才能正常使用)；
- NOFORCE  ：如果基表不存在，无法创建视图，该项是默认选项(只有基表都存在ORACLE才会创建该视图)。
- alias：为视图产生的列定义的别名；
- subquery：一条完整的SELECT语句，可以在该语句中定义别名；
- WITH CHECK OPTION ：插入或修改的数据行必须满足视图定义的约束；
- WITH READ ONLY：默认可以通过视图对基表执行增删改操作，但是有很多在基表上的限制(比如：基表中某列不能为空，但是该列没有出现在视图中，则不能通过视图执行insert操作)，WITH READ ONLY说明视图是只读视图，不能通过该视图进行增删改操作。现实开发中，基本上不通过视图对表中的数据进行增删改操作。

```SQL
-- DDL
CREATE OR REPLACE VIEW MYVIEW
AS
SELECT EMPNO 员工编号, ENAME 姓名,JOB 职位,HIREDATE 入职日期 FROM SCOTT.EMP;

CREATE OR REPLACE VIEW VIEWEMP
AS
SELECT E.*,D.DNAME,D.LOC FROM EMP E 
LEFT JOIN DEPT D ON E.DEPTNO = D.DEPTNO;
```

在视图中被查询的表称为基表。

## 1.5 移去视图

移去一视图并不会丢失数据，因为一视图是基于数据库中隐含的一张表的，视图没有实际数据。

```SQL
DROP VIEW MYVIEW;
```

## 1.6 视图对基表的影响

通过更新视图数据可以修改基表数据。但不是所有的视图都可以更新，只有对满足可更新条件的视图，才能进行更新。

1. 没有使用连接函数、集合运算函数和组函数。
2. 创建视图的SELECT语句中没有集合函数且没有GROUP BY,DISTICT关键字。
3. 创建视图的SELECT语句中不包含从基表列通过计算所得的列。
4. 创建视图没有包含只读属性。

插入数据

​	使用INSERT语句通过视图向基本表中插入数据。

​	注意：当视图所依赖的基本表有多个时，不能向该视图中插数据。

修改、删除数据

​	使用UPDATE语句可以通过视图修改基本表的数据。

​	使用DELETE语句可以通过视图删除基本表的数据。

​	注意：对于依赖多个基本表的视图，不能使用DELETE语句。

# 2 索引（Index）

数据库索引好比是一本书前面的目录,能加快数据库的查询速度。

- 自动创建索引:当你为一张表定义主关键或唯一性约束条件时一个惟一的索引就已经被创建了。 
- 手动创建索引: 用户可以自己创建索引。

## 2.1 创建索引

对一列或多列创建索引

 ```sql
CREATE INDEX index ON table (column[, column]...);
 ```

```sql
-- DDL
CREATE INDEX INDEX_EMP_ENAME ON EMP(ENAME);
```

何时需要创建一个索引：

- 在WHERE语句中该列被经常用到。
- 该列所包含的值有很大取值范围。
- 在WHERE语句中有两个或两个以上的列被经常在一起使用的时候。
- 表很大。

何时不需要创建一个索引：

- 表很小时。
- 这列在查询中不经常用到。
- 表不断地被更新。

## 2.2 查看索引

USER_INDEXES 数据字典视图包含索引名和它的uniqueness。

USER_IND_COLUMNS 视图包含索引名、表名、列名。

```SQL
-- 查所有索引
SELECT * FROM USER_INDEXES;

-- 查看EMP表索引
SELECT	ic.index_name, ic.column_name, ic.column_position col_pos,ix.uniqueness
FROM	user_indexes ix, user_ind_columns ic
WHERE	ic.index_name = ix.index_name AND	ic.table_name = 'EMP';
```

## 2.3 删除索引

要删除一个索引你必须是这个索引的拥有者或拥有删除索引的权限。 

从数据字典中删除索引

```sql
DROP INDEX index;
```

从数据字典中删除EMP_ENAME_IDX索引。

```SQL
DROP INDEX emp_ename_idx;
```

# 3 序列（Sequence）

序列是一种可被多个用户使用的用于自动产生一系列惟一数字的数据库对象。序列是一个共享的对象。通常被用来产生主键值。

创建序列的语句 CREATE SEQUENCE

## 3.1 创建序列

定义一个序列来自动地产生有序的数字。

```SQL
CREATE SEQUENCE sequence
[INCREMENT BY n]  -- 每次增加几
[START WITH n]  -- 从几开始
[{MAXVALUE n | NOMAXVALUE}] -- 最大值
[{MINVALUE n | NOMINVALUE}] -- 最小值
[{CYCLE | NOCYCLE}] --确保在序列达到最大值（增序列）或最小值（减序列）之后不能产生更多的值，用以防止序列回转
[{CACHE n | NOCACHE}]; --用以确定在内存高速缓冲区预先装入的一组序列数目
```

## 3.2 查看序列

可以在 USER_SEQUENCES 数据字典表中查看你的序列 。

```SQL
-- 全部信息
SELECT * FROM USER_SEQUENCES;

-- 部分信息
SELECT	sequence_name, min_value, max_value, increment_by, last_number
FROM	user_sequences;
```

## 3.3 NEXTVAL 和 CURRVAL

NEXTVAL ：返回下一个序列的值。

CURRVAL ：当前的序列值 。

​	每引用一次序列的伪列NEXTVAL，就会按照序列的定义产生一个新的序列码；而通过序列伪列CURRVAL可以反复利用当前的序列码。一次返回一个值。

在以下场合可以使用两个伪列 

- INSERT语句的VALUES子句中
- SELECT语句中的前面选择的表列名中
- UPDATE语句中的SET子句

在以下场合不可以使用两个伪列 

1. 子查询中。
2. 视图定义的查询中。
3. 带有DISTINCT操作符的SELECT语句
4. 带有GROUP BY 或ORDER BY子句的SELECT语句。
5. SELECT语句的WHERE子句中
6. 检查约束条件中

## 3.4 修改序列

```SQL
ALTER SEQUENCE dept_deptno
INCREMENT BY 1
MAXVALUE 999999
NOCACHE
NOCYCLE;
```

## 3.5 删除序列

从数据字典中删除一个序列可以使用DROP SEQUENCE 语句，一旦删除序列就不再能使用了。

```SQL
DROP SEQUENCE dept_deptno;
```

# 4 分页（Rownum）

分页：是一种将所有数据分段展示给用户的技术。

用户每次看到的不是全部数据，而是其中的一部分。

如果在其中没有找到自习自己想要的内容，用户可以通过制定页码或是翻页的方式转换可见内容，直到找到自己想要的内容为止。其实这和我们阅读书籍很类似。

分页的优缺点：

- 优点：提升了性能，有效使用资源
- 缺点：加大了查询的复杂度

分页的类型：

1）物理分页：

- 每次只查询一定的数据，但是每次都需要重新查询
- 优点是速度快

2）逻辑分页：

- 一次性查询出所有的数据，存放到程序的集合中
- 优点是只需要查询一次
- 缺点是数据更新不及时，如果1000条数据只看前10条，那么会造成资源浪费

## 4.1 常见的数据库分页

SQL Server分页：top

```SQL
select top 10* from (select top 3000010* from tbl_FlightsDetail order by FlightsDetailID) as t order by t.FlightsDetailID desc
```

MySQL分页：limit m,n

​	m：表示从哪一行开始查

​	n：表示查询多少条

```sql
SELECT*FROM 表 limit  0,10   查询前10条数据从0开始
SELECT*FROM 表 limit  10   查询前10条
SELECT*FROM 表 limit  5,5  从第6条开始查询5条数据
```

Oracle分页：rownum

```sql
SELECT * FROM (
  SELECT E2.*,ROWNUM R FROM 
  ( -- 先查SAL在 1500 ~ 3000 的数据
    SELECT * FROM EMP E1 WHERE E1.SAL BETWEEN 1500 AND 3000
  ) E2 WHERE ROWNUM <= 10 -- 嵌套优化
) E3 WHERE E3.R >= 5 -- 将ROWNUM变成真实列
```

## 4.2 物理分页

- 物理分页是在数据库端，使用数据库提供的关键字编写的分页SQL语句，直接得到分页结果。
- 物理分页每次查询数据都需要访问数据库，数据库的负担大。
- 物理分页每次需要数据时都访问数据库，能够获取数据库的最新状态，实时性强。
- 物理分页主要用于数据量较大、更新频繁的场合。

## 4.2 rownum分页

oracle 数据库没有分页关键字，想要做分页就需要知道查询的结果集的数据量。 

rownum是oracle系统顺序分配为从查询返回的行的编号，返回的第一行分配的是1，第2行分配的是2，依次类推，这个伪字段可以用于限制查询返回的总行数。用来实现分页。

```sql
-- ROWNUM
SELECT E.*,ROWNUM FROM EMP E WHERE ROWNUM <= 5;

-- 将rownum变成真实的列
SELECT * FROM (SELECT E.*,ROWNUM RW FROM EMP E ORDER BY SAL) MYEMP
WHERE MYEMP.RW >= 10  AND MYEMP.RW <= 15; 

-- 优化后的SQL语句
SELECT * FROM (SELECT E.*,ROWNUM RW FROM EMP E WHERE ROWNUM <= 10) MYEMP
WHERE  MYEMP.RW >= 5;

-- 嵌套优化
--  sal  在  1500 ~ 3000
SELECT * FROM (
  SELECT E2.*,ROWNUM R FROM 
  (
    SELECT * FROM EMP E WHERE E.SAL BETWEEN 1500 AND 3000
  ) E2 WHERE   ROWNUM <= 10
) E3 WHERE E3.R >= 5;
```

## 4.3 rownum的陷阱

**1）对ROWNUM进行 >、>=、= 操作**

不能对ROWNUM使用 >（大于1的数值）、>=（大于或等于1的数值）、=（大于或等于1的数值），否则无结果。

ROWNUM是伪列，必须要要有返回结果后，每条返回记录就会对应产生一个ROWNUM数值。返回结果记录的ROWNUM是从1开始排序的，因此第一条始终是1。

**2）Rownum和Order by**

在使用ROWNUM时，只有当Order By的字段是主键（或唯一约束）时，查询结果才会先排序再计算ROWNUM。

oracle先按物理存储位置（rowid）顺序取出满足rownum条件的记录，即物理位置上的前5条数据，然后在对这些数据按照Order By的字段进行排序，而不是我们所期望的先排序、再取特定记录数。所以建议在排序好的数据上根据 rownum 进行数据检索。

**3）rownum不能以任何基表的名称作为前缀** 

## 4.4 Oracle中最常见的分页

表查询的列增加rownum

```sql
select rownum r , e.* from emp e;
```

在第一步的结果集基础上用 rownum的别名 r 做范围限制 

```sql
select * from ( select rownum r , e.* from emp e ) e where r <=10 and r >=1;
```

Between 的版本 

```sql
select * from ( select rownum r , e.* from emp e ) e where r between 1 and 10;
```

