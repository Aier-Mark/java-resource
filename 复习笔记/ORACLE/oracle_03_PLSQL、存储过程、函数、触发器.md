# 1 PL/SQL程序

​	对于标准化的SQL语言对数据库进行各种操作，每次只能执行一条语句，语句以英文的分号“；”为结束标识。这是因为Oracle数据库系统不像VB，VC这样的程序设计语言，侧重在于后台数据库的管理，因此提供的编程能力较弱，而结构化编程语言对数据库的支持能力又较弱。
	在这种要求的驱使下，Oracle公司在标准SQL语言的基础上发展了自己的PL/SQL语言，将变量、控制结构、过程和函数等结构化程序设计的要素引入了SQL语言中，这样就能够编制比较复杂的SQL程序了，利用PL/SQL语言编写的程序也称为PL/SQL程序块。

注意：PI/SQL是专用于Oracle服务器，在SQL基础之上，添加了一些过程化控制语句，叫PL/SQL。

PL/SQL组成：

- 数据类型
- 常量、表达式
- 条件、循环控制语句
- 游标、异常处理

## 1.1 PL/SQL程序总体结构

```SQL
declare      
    < 定义语句段 >
begin
    < 执行语句段 >
exception
    < 异常处理语句段 >
end;
```

定义语句段：以declare为标识，在该部分定义程序中要使用的常量、变量、游标等。

执行语句段：以begin为开始标识。该部分是每个pl/sql程序所必备的，包含了对数据库的操作语句和各种流程控制语句。

异常处理语句段：该部分包含在这行部分里面，以exception为标识，对程序执行中产生的异常情况进行处理。有的程序比较简单，往往省略异常处理部分。 

## 1.2 数据类型

基本数据类型

| 类型标识符     | 说明                        |
| -------------- | --------------------------- |
| number         | 数字型                      |
| int            | 整数型                      |
| Pls_integer    | 整数型，产生溢出时出现错误  |
| Binary_integer | 整数型，表示带符号的整数    |
| Char           | 定长字符型，最大255个字符   |
| varchar        | 变长字符型，最大2000个字符  |
| Long           | 变长字符型，最大2GB         |
| Date           | 日期型                      |
| Boolean        | 布尔型（true, false, null） |

符号数据类型

| 类型标识符   | 说明                                                     |
| ------------ | -------------------------------------------------------- |
| %type        | 用于匹配其字段类型和数据表中字段的的数据类型             |
| 记录类型变量 | 多个基本数据类型捆绑在一起的记录类型                     |
| %rowtype     | 使用%rowtype可以使变量获得整个记录的数据类型             |
| 游标         | 从数据表中提取出来的数据结果集使用游标类型接收，便于操作 |

## 1.3 常量

语法格式：常量名  constant  类型标识符  [not null]:=值； 

要求：常量名与后面的变量名都必须以字母开头，不能有空格，不能超过30个字符长度，同时不能和保留字同名，常（变）量名称不分大小写，在字母后面可以带数字或特殊字符。括号内的not null为可选参数，若选用，表明该常（变）量不能为空值。 

```sql
-- 定义名为pi的数字型常量，长度为9。
declare
	pi  constant number(9) := 3.1415926;
begin
	commit;
end;
```

## 1.4 表达式

变量、常量经常需要组成各种表达式来进行运算，下面介绍PL/SQL中常见表达式的运算规则。

**1、数值表达式**

PL/SQL程序中的数值表达式是由数值型常数、变量、函数和算术运算符组成的，可以使用的算术运算符包括+（加法）、-（减法）、*（乘法）、/（除法）和**（乘方）等。

**2、字符表达式**

字符表达式由字符型、变量、函数和字符运算符组成，惟一可以使用的字符运算符就是连接运算符“||”。

**3、关系表达式**

关系表达式由字符表达式或数值表达式与关系运算符组成，关系型表达式运算符两边的表达式的数据类型必须一致。可以使用的关系运算符包括以下9种。 

- = 等于（不是赋值运算符:=)
- like 类似于
- in 在．．．之中
- <= 小于等于
- `>=` 大于等于
- ！= 不等于
- Between 在．．．之间

**4、逻辑表达式**

​      逻辑表达式由逻辑常数、变量、函数和逻辑运算符组成，常见的逻辑运算符包括以下3种：

​      NOT：逻辑非

​      OR：逻辑或

​      AND：逻辑与

**5、重要的几个函数**

​      PL/SQL程序 中提供了很多函数供扩展功能，除了标准SQL语言的函数可以使用外，最常见的数据类型转换函数有以下3个。

​      To_char：将其他类型数据转换为字符型。

​      To_date：将其他类型的数据转换为日期型。

​      To_number：将其他类型数据转换为数值型。

# 2 PL/SQL语法基础

```sql
--  没有定义变量  注意不能在PLSQL语句做查询
SELECT * FROM BONUS;
DECLARE
BEGIN
  INSERT INTO BONUS(ENAME,JOB,SAL,COMM) VALUES('WANGYANG','TEACHER',1000,500);
  INSERT INTO BONUS(ENAME,JOB,SAL,COMM) VALUES('WANGKANG','TEACHER',800,200);
  INSERT INTO BONUS(ENAME,JOB,SAL,COMM) VALUES('LIUZHEN','TEACHER',600,100);
  COMMIT;
END;

--输出语句
DECLARE
BEGIN
  DBMS_OUTPUT.put_line('HELLO,PLSQL');
END;

-- 声明变量
DECLARE  --声明
  NAME VARCHAR(20) := '任晓丹';
BEGIN
  DBMS_OUTPUT.put_line('HELLO,'||NAME);
END;

--  查看工资800的员工姓名  
DECLARE
  NAME VARCHAR(20); 
BEGIN
  SELECT ENAME INTO NAME FROM EMP WHERE SAL = 800;
  dbms_output.put_line(NAME);
END;

--输入参数
DECLARE
  ENAME VARCHAR(20);
BEGIN
  SELECT ENAME INTO ENAME FROM EMP WHERE SAL = &sal;   --取缔值：用来输入数据
  dbms_output.put_line(ename);
END;

--多变量
DECLARE
  ename EMP.ENAME%TYPE;
  job EMP.JOB%TYPE;
  comm EMP.COMM%TYPE;
BEGIN
  SELECT ENAME  ,JOB,COMM INTO ename, job, comm FROM EMP WHERE SAL = &SAL;
  dbms_output.put_line(ename||','||job||','||comm);
END;

-- rowtype
SELECT * FROM EMP;
DECLARE
  emprow EMP%rowtype;
BEGIN
  SELECT * INTO emprow FROM EMP WHERE SAL=&SAL;
  dbms_output.put_line(emprow.ename||','||emprow.job);
END;
```

# 3 运算符

```sql
DECLARE
  SAL EMP.SAL%TYPE;
  RESULTS number(9) := 0;
  FLAG  BOOLEAN;
  MSG   VARCHAR(225);
BEGIN
  SELECT SAL INTO SAL FROM EMP WHERE EMPNO=&EMPNO;
  dbms_output.put_line('原始数据：'||to_char(sal));
  dbms_output.put_line(sal+100);
  RESULTS := RESULTS + SAL;
  dbms_output.put_line('加法：'||RESULTS);
  results := results - 1000;
  dbms_output.put_line('减法：'||results);
  results := results*2;
  dbms_output.put_line('乘法：'||results);
  results := results/2;
  dbms_output.put_line('除法：'||results);
  results := 2**2;
  dbms_output.put_line('平方：'||results);
  dbms_output.put_line('-----------------关系运算符-----------');
  FLAG := results = 4.0;
  --MSG := TO_CHAR(FLAG);  不能直接输出布尔值
  --dbms_output.put_line( MSG);  
END;
```

# 4 IF语句

1） IF

```sql
IF 条件 THEN 
	语句段;
END IF;
```

```sql
-- IF
DECLARE
BEGIN
  IF not(1 < 2) THEN
    dbms_output.put_line('true');
  END IF;
END;
```

2） IF ELSE

```sql
IF 条件 THEN
	语句段1;
ELSE
	语句段2;
END IF;
```

```sql
-- IF ELSE
DECLARE
  number int := &A;
BEGIN
  IF number in (2,3,4,5) THEN
     dbms_output.put_line('true');
  ELSE
     dbms_output.put_line('false');
    
  END IF;
END;
```

3） BETWEEN AND

```sql
IF 变量 BETWEEN 数字1 AND 数字2 THEN
     语句段1;
ELSE
     语句段2;
END IF;
```

```sql
-- BETWEEN  AND
DECLARE
   NUMBER INT := &U;
BEGIN
  IF NUMBER BETWEEN 1 AND 15 THEN
    dbms_output.put_line('true');
  ELSE
    dbms_output.put_line('false');
  END IF;
END;
```

4） IF ELSE IF

```
IF 条件1 THEN
	语句段1;
ELSIF 条件2 THEN
	语句段2;
ELSE
	语句段3;
END IF;
```

```sql
--IF ELSE IF
DECLARE
     NUMBER INT := &Z;
BEGIN
  IF NUMBER > 1 AND NUMBER <= 2 THEN
    dbms_output.put_line('NUMBER 1');
  ELSIF NUMBER > 2 AND NUMBER <=3 THEN
    dbms_output.put_line('NUMBER 2');
  ELSE
     dbms_output.put_line('ELSE');
  END IF;
END;
```

5） IF嵌套

```
IF 条件1 THEN
	IF 条件2 THEN
		语句段1;
	ELSE
		语句段2;
	END IF;
ELSE
	语句段3;
END IF;
```

```sql
--IF嵌套
DECLARE
  NUMBER INT := &H;
BEGIN
  IF NUMBER> 1 THEN
     IF NUMBER <= 2 THEN
         dbms_output.put_line('NUMBER 1');
     END IF;  
  END IF;
END;
```

# 5 LOOP循环

循环结构是按照一定逻辑条件执行一组命令，PL/SQL中可以有4种基本循环结构，在它们基础上又可以演变出许嵌套循环控制。

1） LOOP ... EXIT

```SQL
LOOP
	循环语句;
	IF 条件 THEN
		EXIT; -- 退出循环
	END IF;
END LOOP;
```

```SQL
DECLARE
	I INT := 1;
BEGIN
	LOOP
		IF I = 10 THEN
			EXIT;
		END IF;
		dbms_output.put_line('LOOP'||I);
		I := I+1;
	END LOOP;
END;
```

2） LOOP EXIT WHEN

```
LOOP
	循环语句;
	EXIT WHEN 条件;
END LOOP;
```

```SQL
-- LOOP EXIT WHEN
DECLARE
I INT := 1;
BEGIN
  LOOP
      EXIT WHEN I = 10;
      dbms_output.put_line('LOOP'||I);
      I := I+1;
  END LOOP;
END;
```

3） WHILE LOOP

```
WHILE 条件 LOOP
	循环语句;
END LOOP;
```

```SQL
-- while 循环
DECLARE
I INT := 0;
BEGIN
  WHILE I < 10 LOOP
     dbms_output.put_line('LOOP'||I);
     I := I + 1;  --自增
  END LOOP;
END;
```

4） FOR IN LOOP

```SQL
FOR 循环变量 IN [REVERSE] 循环下界..循环上界 LOOP
	循环语句;
END LOOP;
```

```SQL
-- for 循环
DECLARE 
I INT := 1;
N INT := 1;
BEGIN
  FOR I IN N..10 LOOP
     dbms_output.put_line('LOOP'||I);
  END LOOP;
END;
```

# 6 游标（Cursor）

游标是从数据表中提取出来的数据，以临时表的形式存放在内存中，在游标中有一个数据指针，在初始状态下指向的是首记录，利用fetch语句可以移动该指针，从而对游标中的数据进行各种操作，然后将操作结果写回数据表中。 

## 6.1 游标的定义

游标作为一种数据类型，首先必须进行定义，其语法如下。

​      cursor 游标名  is select 语句;

​      cursor是定义游标的关键字，select是建立游标的数据表查询命令。

```SQL
DECLARE
  cursor mycursor is select * from emp; -- 定义游标
  cursor_row mycursor%rowtype; -- 定义变量存储数据
BEGIN
  dbms_output.put_line('游标');
  open mycursor; -- 打开游标
  fetch mycursor into cursor_row; --提取第一行数据
  close mycursor; -- 关闭游标
  dbms_output.put_line(cursor_row.empno||','||cursor_row.ename);
END;
```

## 6.2 游标的打开

要使用创建好的游标就要打开游标，语法形式如下。

​     open  游标名;

打开游标的过程有以下两个步骤。

1. 将符合条件的记录送入内存。
2. 将指针指向第一条记录。

## 6.3 游标数据的提取

要提取游标中的数据，需要使用fetch命令，语法形式如下。

- ​    fetch 游标名 into 变量名1，变量名2，……;
- ​    fetch 游标名 into 记录型变量名;

## 6.4 游标的关闭

使用完游标后，需要关闭游标，使用close命令，语法形式如下。

​      close  游标名;

## 6.5 游标的重要属性

游标提供的一些属性可以帮助编写PL/SQL程序，游标属性的使用方法为：

​	游标名[属性]。

1） %isopen属性

​      属性功能：测试游标是否打开，如果没有打开游标就是用fetch语句并提示错误。

2） ％FOUND

​      逻辑值，游标是否找到一条记录。如游标找到记录其值为True，反之为False。

3） %NOTFOUND

​      逻辑值，游标没有找到记录，是%FOUND属性的逻辑非。

4） %ROWCOUNT

​    返回提取游标记录的行数。

## 6.6 游标案例

```sql
DECLARE
  -- 定义游标
  CURSOR EMP_CUR IS SELECT * FROM EMP WHERE SAL > 800; 
  CUR EMP_CUR%ROWTYPE; -- 定义变量
BEGIN
  OPEN EMP_CUR; -- 打开游标
  IF EMP_CUR%ISOPEN THEN -- 判断游标是否打开
    LOOP
      IF EMP_CUR%NOTFOUND THEN -- 如果没有找到记录返回true
        DBMS_OUTPUT.PUT_LINE('数据提取完毕...');
        EXIT; -- 退出循环
      END IF;
      
      FETCH EMP_CUR INTO CUR; -- 提取数据
      DBMS_OUTPUT.PUT_LINE(CUR.EMPNO||','||CUR.ENAME||','||CUR.SAL);
      
    END LOOP;
    -- 查看记录多少条数据
    DBMS_OUTPUT.PUT_LINE('总计：'||EMP_CUR%ROWCOUNT||'条数据'); 
  END IF;
  CLOSE EMP_CUR; -- 关闭游标
END;
```

# 7 异常（Exception）

在设计PL/SQL程序时，经常会发生这样或那样的错误，异常处理就是针对错误进行处理的程序段，Oracle中的异常处理分为系统定义异常处理和自定义异常处理两部分。 

## 7.1 用户自定义异常处理

异常不一定必须是Oracle返回的系统错误，用户可以在自己的应用程序中创建可触发及可处理的异常。

定义异常的语法：

​	declare

​		异常名 exception;

触发异常处理的语法是：

​	raise  异常名;

触发异常处理后，可以定义异常处理部分，语法如下：

​	Exception

​		when异常名1  then

​			异常处理语句段1；

​		when 异常名2 then

​			异常处理语句段2；

## 7.2 异常案例

```SQL
DECLARE
  sal_exception exception; -- 定义异常
  sal emp.sal%type;
  emp_ename emp.ename%type;
BEGIN
  select sal,ename into sal,emp_ename from emp where empno = &empno;
  if sal < 3500 then
    raise sal_exception; -- 触发异常
  end if;
  
  exception
    when sal_exception then -- 处理异常
      dbms_output.put_line(emp_ename||','||sal);
      dbms_output.put_line('异常：工资太低！');
      UPDATE EMP SET SAL = 3500 WHERE ENAME = emp_ename;
END;
```

# 8 存储过程(Procedure)

- 完成一个或多个行为；
- 创建后被编译存放进Oracle数据字典中；
- 无返回值。

## 8.1 存储过程语法

```SQL
create [or replace] procedure ProcedureName [(param1 [in] [out] type [, param2 [in] [out] type]...)] is|as
     << 定义变量 >>
begin
    << 执行过程代码语句 >>  
exception
     << 异常处理代码语句 >>
end [ProcedureName];
```

解释：

- create  : 创建
- replace  修改
- procedure 存储过程关键字
- param..  过程参数
- in / out   参数模式，默认是 in  代表输入参数，  out 代表输出参数
- type  参数类型（详见PL.SQL的数据类型）

```SQL
CREATE OR REPLACE PROCEDURE HELLO_PRO(NAME IN VARCHAR,AGE IN INT)
AS
BEGIN
   dbms_output.put_line(NAME||','||AGE);
END;
-- 执行
DECLARE
   NAME VARCHAR(20) := 'ZHANGLINJIA';
   AGE INT := 18;
BEGIN
   HELLO_PRO(NAME,AGE);
END;
```

## 8.2 转账案例

```SQL
--转账记录
CREATE  TABLE BONUS_INFO
(
       MY VARCHAR(255),  --自己账号
       YOU VARCHAR(255), -- 对方账号
       MONEY NUMBER(10), --转账金额
       MYDATE DATE -- 时间
);

--转账存储过程
CREATE OR REPLACE PROCEDURE ZHUANZHANG(MY VARCHAR,YOU VARCHAR ,MONEY BONUS.SAL%TYPE)
AS
MYSAL BONUS.SAL%TYPE;  --临时变量 ，查询自己账户余额是否充足
MYCOUNT INT;           --临时变量 ， 查询对方账号是否存在
SAL_EXCEPTION EXCEPTION; -- 定义异常
BEGIN
   SELECT SAL INTO MYSAL FROM BONUS WHERE ENAME=MY;  -- 查询自己账号余额
   DBMS_OUTPUT.PUT_LINE(MY||'账户余额：'||MYSAL);   
   IF MYSAL >= MONEY THEN    --判断余额跟 转账金额
     DBMS_OUTPUT.PUT_LINE('转账开始...');
     UPDATE BONUS SET SAL = SAL - MONEY WHERE ENAME = MY;  --修改自己的账号金额  
     SELECT COUNT(*) INTO MYCOUNT FROM BONUS WHERE ENAME= YOU; --判断对方站好是否存在
     IF MYCOUNT >0 THEN  -- 对方账号存在
       UPDATE BONUS SET SAL = SAL + MONEY WHERE ENAME = YOU;  --修改对方账号金额    
       --备份转账记录
       INSERT INTO BONUS_INFO(MY,YOU,MONEY,MYDATE) VALUES(MY,YOU,MONEY,SYSDATE); -- SYSDATE 当前时间
       COMMIT; --万无一失 ，提交事务，2个修改，1个添加
     ELSE --对方账号不存在
       DBMS_OUTPUT.PUT_LINE(YOU||'账户不存在');
       ROLLBACK; -- 回归MY账户     
     END IF; 
   ELSE
     RAISE SAL_EXCEPTION; --抛出自定义异常
   END IF;   
EXCEPTION --异常处理开始
   WHEN SAL_EXCEPTION THEN
     DBMS_OUTPUT.PUT_LINE('余额不足无法开始转账！');
   WHEN OTHERS THEN  --其他异常
     DBMS_OUTPUT.PUT_LINE(MY||'账户不存在');
END;

-- 测试
DECLARE
BEGIN
  ZHUANZHANG('WANGKANG','WANGYANG',200);
END;
```

# 9 函数(Function)

语法：

```SQL
create [or replace] Function FunctionName [(param1 [in] [out] type [, param2 [in] [out] type]...)] return 类型 is|as
     << 定义变量 >>
begin
    << 执行过程代码语句 >>  
    return 值;
exception
     << 异常处理代码语句 >>
end [FunctionName];
```

解释：

- create  : 创建
- replace：修改
- function：函数关键字
- param.. ：过程参数
-  in / out：参数模式，默认是 in  代表输入参数， out 代表输出参数
- type：参数类型（详见PL.SQL的数据类型）   
- return：返回的数据类型

```SQL
-- 函数
CREATE OR REPLACE FUNCTION MYFUNCTION(NAME IN VARCHAR, AGE OUT INT)
RETURN VARCHAR
AS
BEGIN
  DBMS_OUTPUT.put_line(NAME);
  AGE := 10;
  RETURN 'HELLO FUNCTION';
END;
-- PLSQL语句块
DECLARE
AGE INT;
MSG VARCHAR(20);
BEGIN
  MSG := MYFUNCTION('WANGYANG',AGE);
  DBMS_OUTPUT.put_line(MSG||','||AGE);
END;
```

# 10 触发器（Trigger）

语法：

```sql
create [or replace] trigger TriggerName [before|after] [insert] [update] [delete] on 表名 [for each row ] [when 条件]   
declare
	<< 定义变量 >>
begin
	<< 执行过程代码语句 >>
	-- 禁止提交或回滚事务
exception
	<< 异常处理代码语句 >>
end [TriggerName];
```

解释：

- create  : 创建
- replace： 修改
- trigger ：触发器关键字
- before|after : 前置触发|后置触发，二者选择其一
- `[insert][update][delete]` ：触发的动作
- on  : 针对某一张表/视图 的DML语句进行触发
- for  each  row : 行级触发
- when  : 触发条件

```SQL
-- 给表EMP添加触发器，在更新EMP表数据时会将旧数据添加到BACK_EMP表保存
CREATE OR REPLACE TRIGGER MYTRIGGER BEFORE UPDATE 
ON EMP FOR EACH ROW
BEGIN
  DBMS_OUTPUT.put_line('TRIGGER INVOKE.');
  DBMS_OUTPUT.put_line(:old.ename);
  DBMS_OUTPUT.put_line(:new.ename);
  INSERT INTO BACK_EMP(EMPNO,ENAME,JOB,MGR,HIREDATE,SAL,COMM,DEPTNO)
  VALUES(:OLD.EMPNO,:OLD.ENAME,:OLD.JOB,:OLD.MGR,:OLD.HIREDATE,:OLD.SAL,:OLD.COMM,:OLD.DEPTNO);
END;
```

说明：

触发器中的谓词 

- Old 谓词：在执行前的字段的值，例如在 更新用户密码的时候，可以通过 ：old.password获取原密码
- New谓词：在执行后字段的值，例如在 更新用户密码的时候，可以通过：new.password 获取新密码
- INSERTING谓词：代表执行的动作是插入动作，可以作为判断条件
- UPDATING谓词：代表执行的动作是更新动作，可以作为判断条件
- DELETING谓词：代表执行的动作是删除动作，可以作为判断条件

