# 1 数据库简介

## 1.1 数据库

```
数据库：存放数据仓库； 
数据： 
	文字数据、图片数据、音频数据、视频数据 
	
1KB = 1024B 
1MB = 1024KB 
1GB = 1024MB 
1TB = 1024GB 
1PB = 1024TB    
```

## 1.2 数据库分类

```
关系型数据库 
	Oracle、MySQL、SQL Server、Postgre、Assess、DB2等 	
	Oracle版本：9i > 10G > 11G > 12C > 18C > 19C 
	MySQL版本：5.x > 8.x 
	
非关系型数据库 
	Redis、MongoDB、MapReduce等  
```

## 1.3 数据库安装

1、数据库安装

```
服务端：数据的服务器
客户端：链接数据的工具，可以为可视化工具提供桥梁
可视化工具：方便操作数据库
```

2、数据库的配置

```
链接服务器时需要的信息：
	账号：自定义
	密码：自定义
	地址：服务器的IP地址，
		如果服务器在本地，可以填localhost或者127.0.0.1
		如果服务器不在本地，必须要填写IP地址
	端口号:1521
	服务器名称：orcl
	
orcl =
	(DESCRIPTION =
		(ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521))
		(CONNECT_DATA =
			(SERVER = DEDICATED)
			(SERVICE_NAME = orcl)
		)
	)
```

# 2 常用方法

## 2.1 用户、角色与权限

创建账号密码、赋权限

```
1、打开cmd命令提示符，输入【sqlplus / as sysdba;】
2、输入【create user 用户名 identified by 密码;】创建用户
3、输入【grant connect,resource,dba to 用户名;】给用户赋权限
```

```sql
--创建用户
CREATE USER WANGYANG IDENTIFIED BY 123456;

-- 分配角色
-- 超级管理员 DBA
GRANT DBA TO WANGYANG;
-- 普通管理员 RESOURCE
GRANT RESOURCE TO WANGYANG;
-- 普通用户 CONNECT
GRANT CONNECT TO WANGYANG;
-- 赋值多角色
GRANT DBA,CONNECT TO WANGYANG;

-- 回收角色
REVOKE CONNECT FROM WANGYANG;
-- 回收多角色
REVOKE DBA,CONNECT FROM WANGYANG;

-- 删除用户并回收角色
-- 加上cascade则将用户连同其创建的东西全部删除
DROP USER WANGYANG CASCADE;
```

## 2.2 约束条件

可以为一个表列创建约束条件，此时，表中的每一行都必须满足约束条件定义所规定的条件。

```
主键约束（Primary Key）
	主键是表中的一列或多个列。为表定义主键有几个作用，主键包含的列不能输入重复的值，以保证一个表的所有的行的唯一性；主键包含的列不能输入重复值，以保证一个表的所有行的唯一性；主键也不允许定义此约束的列为NUL值；主键在定义此约束的列中创建了唯一性的索引，利用这个索引可更快地检索表中的行。

外键约束条件（foreign key）
	该约束条件规定表间的关系性质。一个外键使一个表的一列或多列与已定义为主键的表中的一批相同的列相关联。

默认约束条件（Defauult）
	在表中插入一行数据但没有为列指定值时生成一个在定义表时预先指定的值。
	
检查约束条件（Check）
	该约束条件确保指定列中的值符合一定的条件。

惟一性约束条件（unique）
	用于保证不是主键那些列的惟一性。

非空约束（not null）
	该约束规定插入的数据不能为空。
```

```sql
CREATE TABLE MYEMP
(
       ID INT PRIMARY KEY, --主键约束
       NAME VARCHAR(20) NOT NULL, --非空约束
       ADDRESS VARCHAR(2000) UNIQUE NOT NULL, --唯一性约束和非空约束
       SEX VARCHAR(5) DEFAULT '女' CHECK( SEX='男' OR SEX='女'), --默认约束和检查约束
       STATEID INT,
       FOREIGN KEY(STATEID) REFERENCES STATE(STATEID) --外键约束
);
```

## 2.3 关联关系

```
一对一：一张表中的一条数据在另一张表只有一条对应；例如：一个学生只能对应一个班级；
一对多：一张表中的一条数据在另一张表只有多条对应；例如：一个学生有多个成绩；
多对一：一张表中的多条数据在另一张表只有一条对应；例如：多个成绩可以对应一个学生；
多对多：一张表中的多条数据在另一张表中有多条对应；例如：多个学科可以对应多个成绩；
```

## 2.4 DDL、DML、DCL、DQL

```
数据库语言分为：DDL、DML、DCL、DQL

DDL（Data Definition Language 数据定义语言）用于操作对象和对象的属性，对象包括数据库本身，以及数据库对象，如：表、视图等，DDL对这些对象和属性的管理和定义具体表现在 Create（创建）、Drop（删除）和 Alter（修改）上。

DML（Data Manipulation Language 数据操控语言）用于操作数据库对象中包含的数据，也就是说操作的单位是记录。

DCL（Data Control Language 数据控制语句）用于数据库对象的权限，这些操作的确定使数据更加的安全。

DQL（Data Query Language 数据查询语句）用于数据库的数据查询操作。
```

DDL语句：针对与数据库操作来说的，比如说权限操作，表操作，约束操作。
DML语句：针对与数据来说的，比如说增删改查。

## 2.5 注释

```SQL
单行注释：
	-- 单行注释
多行注释：
	/*
		多行注释
	*/
```

## 2.6 表操作

1、创建表

```SQL
-- 语法结构
create table 表名(
	字段名称 字段类型,
	字段名称 字段类型,
	字段名称 字段类型,
	......
);
```

```SQL
CREATE TABLE ROOM
(
       ROOMID INT PRIMARY KEY,
       DESCRIPTION VARCHAR(2000),
       GUESTNUM INT,
       ROOMSTATEID INT ,
       ROOMTYPEID INT,
       FOREIGN KEY(ROOMSTATEID) REFERENCES ROOMSTATE(ROOMSTATEID),
       FOREIGN KEY(ROOMTYPEID)  REFERENCES ROOMTYPE(TYPEID)    
);
```

```SQL
-- 备份表
CREATE TABLE COPY_EMP AS SELECT * FROM EMP;
```

2、修改表

```sql
-- 添加列
alter table 表名 add 字段名称 字段类型；
-- 添加外键约束
alter table 表名 add constraint 外键名称 foreign key(本表列名) references 其他表表名(列名)；
-- 删除列
alter table 表名 drop (列名);
```

```SQL
-- 在表 ROOM 中添加列 GOODSID
ALTER TABLE ROOM ADD GOODSID INT;
-- 为表 ROOM 的 GOODSID 列添加外键
ALTER TABLE ROOM CONSTRAINT FK_G FOREIGN KEY(GOODSID) REFERENCES GOODS(GOODSID);
-- 删除表 ROOM 中的 GOODSID 列
ALTER TABLE ROOM DROP (GOODSID);
```

3、删除表

```sql
drop table 表名;
```

```SQL
DROP TABLE ROOM;
```

# 3 数据类型

## 3.1 字符类型

```SQL
char(n)：字符长度是n，如果数据的长度不足n，会以空格补全；最大长度是2000。
	char(10)
		'123' -> '123 '
	特点：
		1、定长
		2、会补空格
		3、最大长度2000

varchar(n)：字符长度是n，数据的值是多少，就是多少，不会补空格。最大长度是2000。
	varchar(10)
		'123' -> '123'
	特点:
		1、不定长
		2、不会补空格
		3、最大长度是2000

varchar2(n)：Oracle数据库特有的数据类型，字符长度是n，数据的值是多少，就是多少，不会补空格。最大长度是4000；
	varchar2(10)
		'123' -> '123'

clob：大文本类型，最大4G数据，用于存储小说、文本、新闻等；
blob：大字节类型，最大4G数据，用于存储图片、视频、音频等；
```

varchar和varchar2的区别 

1. varchar是标准的SQL数据类型，varchar2是Oracle提供的特有的数据类型； 
2. varchar对于汉字占2个字节，对于字母和数字占1个字节；varchar2都是2个字节； 
3. varchar对空字符串不处理，varchar2会把空字符串当作null处理； 
4. varchar存放固定长度的字符串，最大长度是2000，varchar2是存放可变长度的字符串，最大长度 是4000。    

## 3.2 数值类型

```SQL
number(p,s)：
	可以存放数据范围为10^130-10^126（不包含此值)，需要1~22字节(BYTE)不等的存储空间；
	p是Precison的英文缩写，即精度缩写，表示有效数字的位数，最多不能超过38个有效数字；从左边第一个不为0的数算起；
	s是Scale的英文缩写，可以使用的范围为-84~127。Scale为正数时，表示从小数点到最低有效数字的位数，它为负数时，表示从最大有效数字到小数点的位数；
	
	p表示包含小数在内的最大总位数，s表示小数的最大位数；
		s>0，精确到小数点右边s位，并四舍五入。然后检验有效位是否<=p。
		s<0，精确到小数点左边s位，并四舍五入。然后检验有效位是否<=p+|s|。
		s=0，此时number表示整数。
		
	number(5, 2)
		123.01
		
	integer：integer是number的子类型，它等同于number(38,0)，用来存储整数。若插入、更新的数值有小数，则会被四舍五入。
```

## 3.3 日期类型

```SQL
date：DATE是最常用的数据类型，日期数据类型存储日期和时间信息。虽然可以用字符或数字类型表示日期和时间信息，但是日期数据类型具有特殊关联的属性。为每个日期值，Oracle 存储以下信息： 世纪、 年、 月、 日期、 小时、 分钟和秒。一般占用7个字节的存储空间。

timestamp：这是一个7字节或12字节的定宽日期/时间数据类型。它与DATE数据类型不同，因为TIMESTAMP可以包含小数秒，带小数秒的TIMESTAMP在小数点右边最多可以保留9位
```

#  4 Oracle权限管理

## 4.1 Oracle 权限 

权限允许用户访问属于其它用户的对象或执行程序。

ORACLE系统提供三种权限：

​	Object 对象级、System 系统级、Role 角色级。

这些权限可以授予给用户、特殊用户public或角色，如果授予一个权限给特殊用户"Public"（用户public是oracle预定义的，每个用户享有这个用户享有的权限），那么就意味作将该权限授予了该数据库的所有用户。
对管理权限而言，角色是一个工具，权限能够被授予给一个角色，角色也能被授予给另一个角色或用户。用户可以通过角色继承权限，除了管理权限外角色服务没有其它目的。权限可以被授予，也可以用同样的方式撤销。

## 4.2 权限分类

1. 系统权限：系统规定用户使用数据库的权限。（系统权限是对用户而言)。
2. 实体权限：某种权限用户对其它用户的表或视图的存取权限。（是针对表或视图而言的）。

## 4.2 系统权限

系统权限分类：  

1. DBA: 拥有全部特权，是系统最高权限，只有DBA才可以创建数据库结构。  
2. RESOURCE:拥有Resource权限的用户只可以创建实体，不可以创建数据库结构。  
3. CONNECT:拥有Connect权限的用户只可以登录Oracle，不可以创建实体，不可以创建数据库结构。  

对于普通用户：授予connect, resource权限。  

对于DBA管理用户：授予connect，resource, dba权限。  

系统权限授权命令：  

系统权限只能由DBA用户授出：sys, system(最开始只能是这两个用户)  

授权命令：

```SQL
grant connect, resource, dba to 用户名1 [,用户名2]...; 
```

注：普通用户通过授权可以具有与system相同的用户权限，但永远不能达到与sys用户相同的权限，system用户的权限也可以被回收。 

删除用户

```SQL
drop user 用户名 cascade; --加上cascade则将用户连同其创建的东西全部删除 
```

系统权限回收：系统权限只能由DBA用户回收  

```sql
revoke connect, resource from user50; 
```

## 4.3 实体权限

实体权限分类  

```SQL
-- all包括所有权限  
select, update, insert, alter, index, delete, all  
-- 执行存储过程权限
execute 
```

# 5 Oracle增删改查

## 5.1 insert添加

```sql
-- 给表中添加一条数据
insert into 表名(列名1,列名2..) values(值1,值2..);
```

```SQL
-- DML
-- 添加
INSERT INTO EMP(EMPNO,ENAME) VALUES(8000,'GREI');
COMMIT;  -- 提交
ROLLBACK;  -- 回滚
```

## 5.2 select查询

```sql
-- 根据列名查询表中对应列的值
select 列名1，列名2.. from 表名；
```

```SQL
-- 查询
SELECT * FROM EMP;  -- 查询所有
SELECT EMPNO,ENAME,SAL FROM EMP;
SELECT EMPNO AS 编号, ENAME 姓名, E.SAL 工资 FROM EMP E;
```

## 5.3 update修改

```sql
-- 根据某一列修改其他列的值
update 表名 set 列名 = 值, 列名 = 值.. where 列名 = 值;
```

```SQL
-- 修改
UPDATE EMP SET ENAME = 'KOSS', SAL = 1000 WHERE EMPNO = 7599; 
```

## 5.4 delete删除

```sql
-- 删除所有数据
delete from 表名；
-- 删除某些数据
delete from 表名 where 列名 = 值;
```

```SQL
DELETE FROM EMP; -- 删除所有数据
DELETE FROM EMP WHERE EMPNO = 7599;
COMMIT;

-- DDL
-- 清除表中所有数据
TRUNCATE TABLE EMP;
```

# 6 SELECT基础查询

## 6.1 SELECT 基础语法

```SQL
SELECT *|{[DISTINCT] column|expression [alias],...}
FROM table
WHERE Query conditions
ORDER BY column|alias;
```

说明：

- SELECT     标识 选择哪些列，*表示查询表中所有的列。
- DISTINCT  去除重复的行数据
- FROM       标识从哪个表中选择。
- WHERE      查询条件
- ORDER BY    排序，ASC升序，DESC降序

```SQL
SELECT DISTINCT ENAME,SAL FROM EMP
WHERE SAL > 600
ORDER BY SAL ASC;
```

注意：distinct 要后面列都一样才认为重复。

## 6.2 列查询

```SQL
-- 基础查询语句模板
SELECT * FROM EMP;
-- 查看特定的列
SELECT EMPNO , ENAME, JOB FROM EMP;
-- 给表起别名，给列起别名
SELECT E.EMPNO AS 员工编号,
       E.ENAME AS 员工姓名,
       E.JOB 职位,
       E.MGR 上级领导,
       E.HIREDATE 入职时间,
       E.SAL 工资,
       E.COMM 奖金,
       E.DEPTNO 部门编号 FROM EMP E;
```

## 6.3 条件查询

```SQL
-- 工资为5000的员工信息
SELECT * FROM EMP WHERE E.SAL = 5000;
-- 工资在800 ~ 1500之间的员工信息
SELECT * FROM EMP WHERE SAL BETWEEN 800 AND 1500;
SELECT * FROM EMP WHERE SAL >= 800 AND SAL <= 1500;
-- 找到员工名字叫JAMES的所有员工信息
SELECT * FROM EMP WHERE ENAME = 'JAMES';
```

## 6.4 模糊查询

```SQL
-- 模糊查询 找到J开头的员工信息
SELECT * FROM EMP WHERE ENAME LIKE 'J%';
-- 模糊查询 找到员工姓名S结尾的员工信息
SELECT * FROM EMP WHERE ENAME LIKE '%S';
-- 模糊查询 找到员工姓名中包含W的员工信息 
SELECT * FROM EMP WHERE ENAME LIKE '%W%'; 
-- 模糊查询 找到员工姓名中第二字为A的员工信息
SELECT * FROM EMP WHERE ENAME LIKE '_A%'; 
-- 哪些员工没有奖金
SELECT * FROM EMP 
WHERE COMM IS NULL OR COMM = 0;  
-- 哪些员工有奖金
SELECT * FROM EMP
WHERE COMM IS NOT NULL AND COMM <> 0;
```

## 6.5 排序

```SQL
-- 按SAL升序
SELECT * FROM EMP ORDER BY SAL ASC;
-- 按SAL降序
SELECT * FROM EMP ORDER BY SAL DESC ;
-- 先按SAL升序，SAL一样再按EMPNO降序
SELECT * FROM EMP ORDER BY SAL ASC , EMPNO DESC;
```

# 7 运算符

## 7.1 算术运算符

数字和日期使用的算术运算符。

| 操作符 | 描述 |
| ------ | ---- |
| +      | 加   |
| -      | 减   |
| *      | 乘   |
| /      | 除   |

注意：

- 乘除的优先级高于加减。
- 同一优先级运算符从左向右执行。
- 括号内的运算先执行。
- 两个日期相减返回日期之间相差的天数。
- 列值与空值做运算结果都是NULL，需要使用NVL函数处理 。

```SQL
-- 查询EMP表的COMM列，如果为NULL就用0代替
SELECT NVL(COMM,0) FROM EMP;
```

## 7.2 比较运算符

(1) =等于、<>或!=(不等于)、<(小于)、>(大于)、>=(大于或等于)、<=(小于或等于)

(2) [NOT] BETWEEN…AND…

(3) [NOT] IN

(4) IS NULL和IS NOT NULL

(5) [NOT] LIKE

   %：通配零个或多个任意字符

   _：通配一个任意字符

## 7.3 逻辑运算符

| 操作符 | 含义   |
| ------ | ------ |
| AND    | 逻辑与 |
| OR     | 逻辑或 |
| NOT    | 逻辑非 |

## 7.4 字符拼接符

连接符:

- 把列与列，列与字符连接在一起。
- 用 ‘||’表示。
- 可以用来 ‘合成’ 列。

注意：Oracle中字符要包含在单引号中 。

# 8 常见函数

Oracle中函数分为两种：单行函数和组函数。

单行函数有：通用函数、字符函数。数值函数、日期函数、转换函数。

## 8.1 字符函数

| 函数               | 描述                           |
| ------------------ | ------------------------------ |
| CONCAT             | 字符串拼接                     |
| SUBSTR             | 字符串截取                     |
| LENGTH             | 返回字符串的长度               |
| INSTR              | 返回字符串中的索引字符（坐起） |
| LPAD\|RPAD         | 填充字符串的左边/右边          |
| TRIM\|LTRIM\|RTRIM | 去除字符串的空白（左右/左/右） |
| REPLACE            | 字符串的替换                   |
| LOWER\|UPPER       | 字符串转换为小写/大写          |
| INITCAP            | 字符串每个单词首字母转为大写   |
| ASCII              | 把字符串转换为ASCII            |

注意：DUAL 是一个‘伪表’，可以用来测试函数和表达式。

```SQL
-- 虚拟表，仅仅是为了满足ORACLE的语法要求
-- Systm.out.println();
SELECT 'wangyang' FROM DUAL;

--拼接字符
SELECT CONCAT('WANG','YANG') 拼接字符 FROM DUAL;
SELECT CONCAT(ENAME,JOB) 拼接 FROM EMP;
SELECT * FROM EMP;

-- 字符长度
SELECT LENGTH('WANGYANG') FROM DUAL;
SELECT ENAME,LENGTH(ENAME) FROM EMP;

-- REPLACE 替换
SELECT REPLACE('WANGYANG','A','Z') FROM DUAL;
SELECT ENAME, REPLACE(ENAME,'A','*') FROM EMP;

-- 大小写
SELECT UPPER('wangyang') FROM DUAL;
SELECT LOWER('WANGYANG') FROM DUAL;

-- 首字母大写
SELECT INITCAP('wangyang,renxiaodan,zhanglinjia') FROM DUAL;
SELECT INITCAP('wangyang renxiaodan;zhanglinjia') FROM DUAL;
SELECT INITCAP('1,一') FROM DUAL;

--ASCII码
SELECT ASCII('A') FROM DUAL;
SELECT ASCII(ENAME) FROM EMP;

-- 左右拼接
SELECT LPAD('EFG',6,'ABCe') FROM DUAL;
SELECT RPAD('ABCD',9,'EFGHI') FROM DUAL;
-- 注意一个中国字在oracle中固定占有2个字节
SELECT RPAD('你好',10,'，世界') FROM DUAL;

-- 去除空格
SELECT TRIM('     HELLO WORLD    HELLO,863     ') FROM DUAL;
SELECT LTRIM('     HELLO WORLD    HELLO,863     ') FROM DUAL;
SELECT RTRIM('     HELLO WORLD    HELLO,863     ') FROM DUAL;

--截取字符串
SELECT SUBSTR('WANGYANG',5) FROM DUAL;
--从第几位开始，往后截取几个
SELECT SUBSTR('WANGYANG123',5,2) FROM DUAL;

--查询子字符串在字符串中出现的位置
SELECT INSTR('WANGYANG','A') FROM DUAL;
```

## 8.2 数字函数

ROUND：四舍五入

​	ROUND(45.926,2)     ->     45.93

TRUNC：截断

​	RTRUNC(45.926,2)     ->     45.92

MOD：求余

​	MOD(1600,300)         ->     100

  ```SQL
SELECT ROUND(3.1415926) FROM DUAL;
SELECT ROUND(3.1415926,3) FROM DUAL;

SELECT TRUNC(3.1415926,3) FROM DUAL;

SELECT MOD(1,2) FROM DUAL;
  ```

## 8.3 日期函数

| 函数           | 描述                             |
| -------------- | -------------------------------- |
| MONTHS_BETWEEN | 两个日期相差的月数               |
| ADD_MONTHS     | 向指定日期中加上若干月数         |
| NEXT_DAY       | 指定日期的下一个星期* 对应的日期 |
| LAST_DAY       | 本月的最后一天                   |
| ROUND          | 日期四舍五入                     |
| TRUNC          | 日期截断                         |

```SQL
SELECT MONTHS_BETWEEN(TO_DATE('1999-9-9','YYYY-MM-DD'), TO_DATE('2000-9-9','YYYY-MM-DD')) FROM DUAL;

SELECT ADD_MONTHS(TO_DATE('1999-12-01','YYYY-MM-DD'),1) FROM DUAL;

SELECT NEXT_DAY(TO_DATE('2022-7-27','YYYY-MM-DD'),1) FROM DUAL;

SELECT LAST_DAY(TO_DATE('2022-7-27','YYYY-MM-DD')) FROM DUAL;

SELECT ROUND(TO_DATE('2022-9-6','YYYY-MM-DD'),'YYYY') FROM DUAL;

SELECT TRUNC(TO_DATE('2022-9-6','YYYY-MM-DD'),'DD') FROM DUAL;
```

NEXT_DAY (date,char) 

date参数为日期型， 

char：为1~7或Monday/Mon~Sunday

## 8.4 转换函数

| 函数      | 描述                     |
| --------- | ------------------------ |
| TO_CHAR   | 日期或者数值转化为字符串 |
| TO_DATE   | 字符串转化为日期         |
| TO_NUMBER | 字符串转化为数值         |

```SQL
-- 其他类型转成varchar
SELECT CONCAT(TO_CHAR(9),9) FROM DUAL;
-- 其他类型转成为数字
SELECT TO_NUMBER('9')+9 FROM DUAL;
-- 字符串转成为日期
SELECT TO_DATE('1999-9-9 14:12:30' , 'YYYY-MM-DD HH:MI:SS') FROM DUAL;
```

## 8.5 通用函数

这些函数适用于任何数据类型，同时也适用于空值：

- NVL (expr1, expr2)：expr1值为NULL，返回expr2的值，否则返回expr1的值。
- NULLIF (expr1, expr2)：相等返回NULL，不等返回expr1 。

```SQL
SELECT NVL('WANGYANG',1) FROM DUAL;
SELECT ENAME,NVL(COMM,0) FROM EMP;

SELECT NULLIF(1,1) FROM DUAL;
SELECT NULLIF(1,2) FROM DUAL;
```

## 8.6 聚合函数

| 函数  | 描述         |
| ----- | ------------ |
| AVG   | 求平均       |
| MIN   | 最小值       |
| MAX   | 最大值       |
| SUM   | 求和         |
| COUNT | 返回数据条数 |

```SQL
SELECT MAX(SAL) FROM EMP;
SELECT MIN(SAL) FROM EMP;
SELECT MAX(COMM) FROM EMP;
SELECT AVG(SAL) 平均工资 FROM EMP;
SELECT SUM(SAL) 总工资 FROM EMP;
SELECT COUNT(EMPNO) FROM EMP;
SELECT COUNT(COMM) FROM EMP;
SELECT COUNT(*) FROM EMP;
```

# 9 复杂查询

## 9.1 DISTINCT消除重复行

```SQL
SELECT DISTINCT JOB FROM EMP;
SELECT DISTINCT * FROM EMP;  --没有效果
--  ORACLE会将JOB, SAL, EMPNO组合成一条数据，如果这3列一样，就消除
SELECT DISTINCT JOB, SAL, EMPNO FROM EMP;

-- 拷贝 ，备份
CREATE TABLE BACK_EMP AS SELECT * FROM EMP;
SELECT * FROM BACK_EMP;
```

## 9.2 ORDER BY排序

升序 ASC，降序 DESC，默认是升序。

```SQL
-- 按SAL升序
SELECT * FROM EMP ORDER BY SAL ASC;
-- 按SAL降序
SELECT * FROM EMP ORDER BY SAL DESC ;
-- 先按SAL升序，SAL一样再按EMPNO降序
SELECT * FROM EMP ORDER BY SAL ASC , EMPNO DESC;
```

## 9.3 GROUP BY分组

1. GROUP BY 不是必须跟着聚合函数一起使用。
2. GROUP BY后面写的的列的名字必须和查询真实列的名字，一模一样。
3. 除了查询的列，还可以写其他真实列。
4. 会将GROUP BY后面写的列的内容组合在一起，进行判断，如果内容一样则判定为同组，如果不一样分组。
5. GROUP BY 写在WHERE后面。

```SQL
-- 工资最高  当聚合函数与真实列同时存在时，必须对真实列进行分组
SELECT ENAME, MAX(SAL) FROM EMP
GROUP BY ENAME;  --分组

-- 每种职位的最高薪资是多少
SELECT DISTINCT JOB,MAX(SAL) 最高薪资 FROM EMP
WHERE JOB IS NOT NULL
GROUP BY JOB;

--  每个部门员工人数 
SELECT DEPTNO,COUNT(EMPNO) FROM EMP
GROUP BY DEPTNO; 
```

## 9.4 HAVING筛选

对分组之后的数据做条件筛选 。

```SQL
-- having
--  每个部门员工人数  超过5  HAVING 即可对真实列进行条件筛选，也可以对伪列进行筛选
SELECT DEPTNO, COUNT(*) C FROM EMP
GROUP BY DEPTNO
HAVING COUNT(*) > 5;
```

## 9.5 子查询

子查询是指在WHERE子句中也包括查询语句。子查询主要是将自己的结果用于建立主查询的查找条件。

```SQL
-- 有员工的部门信息
SELECT * FROM DEPT WHERE DEPTNO IN (
SELECT DEPTNO FROM EMP
GROUP BY DEPTNO
HAVING COUNT(*) > 0);

--  工资最高的员工信息 
SELECT * FROM EMP WHERE SAL = 
(SELECT MAX(SAL) FROM EMP);
```

使用子查询的规则：

1. 子查询必须“自身就是一个完整的查询”。即，它必须至少包括一个SELECT子句和FROM子句。
2. 子查询SELECT语句不能有ORDER BY子句。因为ORDER BY字句只能对最终查询结果排序，如果显示的输出需要按照特定顺序显示，那么ORDER BY子句应该作为外部查询的最后一个子句列出。
3. 子查询“必须包括在一组括号中”，以便将它与外部查询分开。
4. 如果将子查询放在外部查询的WHERE或HAVING子句中，那么该子查询只能位于比较运算符的“右边”。

## 9.6 多表查询

多表查询指从多个有关联的表中查询数据，其基本语法跟单表查询类似。 

### 9.6.1 内连接

在每个表中找出符合条件的共有记录。

inner join 内连接

```SQL
-- 内链接：满足条件的结果出来，不满不出来 子表.外键 = 主表.主键
SELECT * FROM EMP INNER JOIN DEPT ON EMP.DEPTNO = DEPT.DEPTNO
```

### 9.6.2 外链接

**1 左外连接**

以左表为主，左表所有数据都出来，右表和左表匹配上的数据出来,匹配不上显示为空 outer可省略。

LEFT [OUTER] JOIN  左连接

语法： SELECT ... FROM 表1 LEFT  [OUTER]  JOIN 表2 ON 连接条件

```SQL
-- 左【外】链接： JOIN左边表中的数据全部展示，JOIN右遍表中的数据匹配的出来，不匹配不出来
SELECT * FROM EMP LEFT OUTER  JOIN  DEPT  ON EMP.DEPTNO = DEPT.DEPTNO;
```

**2 右外连接**

以右表为主，右表所有数据都出来，左表和右表匹配上的数据出来 outer可省略。

RIGHT [OUTER] JOIN  右连接

语法： SELECT ... FROM 表1 RIGHT OUTER JOIN 表2 ON 连接条件

```SQL
-- 右【外】链接： JOIN右边表中的数据全部展示，JOIN右遍表中的数据匹配的出来，不匹配不出来
SELECT * FROM EMP RIGHT OUTER JOIN DEPT  ON DEPT.DEPTNO = EMP.DEPTNO;
```

**3 全外连接**

左右表所有数据都出来。

FULL [OUTER] JOIN  全连接

语法： SELECT ... FROM 表1 FULL OUTER JOIN 表2 ON 连接条件

```SQL
-- 全【外】链接： 左右表中的数据全部展示
SELECT * FROM EMP FULL JOIN DEPT ON EMP.DEPTNO = DEPT.DEPTNO;
```

外连接：笛卡尔乘积  表A结果 * 表B结果

```SQL
-- 外连接：左右表数据全部展示，但是不展示连接条件为NULL的结果
SELECT * FROM EMP,DEPT WHERE EMP.DEPTNO = DEPT.DEPTNO;
```

### 9.6.3 自连接 

自表关联。

```SQL
SELECT * FROM EMP E1 LEFT JOIN EMP E2 ON E1.MGR = E2.EMPNO;
```

### 9.6.4 多表关联

例如：A表  左连接 B表 和 C表。

```SQL
SELECT E1.EMPNO,E2.EMPNO,D.DNAME FROM EMP E1 
LEFT JOIN EMP E2 ON E1.MGR = E2.EMPNO
LEFT JOIN DEPT D ON E1.DEPTNO = D.DEPTNO;
```

## 9.7 嵌套查询

指一个SELECT查询中包含一个以上的子查询，所谓子查询指嵌套在另一个SELECT、INSERT、UPDATE或DELETE语句中的SELECT查询语句。

子查询的语法与SELECT语法类似，但有所限制，如子查询不能含有ORDER BY、INTO关键字等。

```SQL
-- 查询高于自己部门平均工资的员工的信息
SELECT * FROM EMP E1 INNER JOIN (
SELECT DEPTNO,AVG(SAL) AVGSAL FROM EMP GROUP BY DEPTNO) E2
ON E1.DEPTNO = E2.DEPTNO AND E1.SAL > E2.AVGSAL;
```



