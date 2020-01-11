<!-- GFM-TOC -->

- [PROCEDURE语法](#PROCEDURE语法)
  - [1. DELIMITER](#1-DELIMITER)
- [程序设计环境](#程序设计环境)
  - [1. helloWorld](#1-helloWorld)
  - [2. 存储过程变量](#2-存储过程变量)
  - [3. Mysql变量](#3-Mysql变量)
  - [4. 设备管理](#4-设备管理)
- [系统调用](#系统调用)
- [大内核和微内核](#大内核和微内核)
  - [1. 大内核](#1-大内核)
  - [2. 微内核](#2-微内核)
- [中断分类](#中断分类)
  \_ [1. 外中断](#1-外中断)
- [i/o设备](#i/o设备)
  \_ [1. 缓冲区](#1-缓冲区)
  <!-- GFM-TOC -->

## Advantages

- Reduce network traffic

```
Instead of sending multiple lengthy SQL statements, applications have to send only the name and parameters of stored procedures.
```

- Centralize business logic in the database

- Make database more secure

```
DB administrator can grant appropriate privileges to applications that only access specific stored procedures without giving any privileges on the underlying tables.
```

## Dissdvantages

- Resource usages

```
1. the memory usage of every connection will increase substantially
2. the CPU usage
    - overusing a large number of logical operations.
    - MySQL is not well-designed for logical operations.
```

- Troubleshooting：difficult to debug

# PROCEDURE语法

<div align="center">  <img src="MySQL-procedure" width="75%" height="75%" /></div><br>

## 1、DELIMITER

默认情况下，delimiter 是分号 `;` 。如果在终端有一条命令以 `;` 结束，那么回车后， mysql 会执行该命令。

但我们在执行 PROCEDURE 时不希望 mysql 这么做，需要事先将 delimiter 换为其他符号

```sql
-- 替换定界符为 $$
DELIMITER $$
BEGIN
...
END$$

DELIMITER ;
```

# 程序设计环境

## 1、helloWorld

```sql
-- craete a store procedure
DELIMITER $$
CREATE PROCEDURE helloworld()
BEGIN
    SELECT 'hello world' from DUAL;
END$$
DELIMITER ;
```

```sql
-- delete a store procedure
DROP PROCEDURE IF EXISTS helloworld;
```

## 2、存储过程变量

基本语法：`[IN | OUT | INOUT] parameter_name datatype[(length)]`

**IN**

> the stored procedure only works on the copy of the IN parameter.

调用程序必须将参数传递给存储过程.另外，IN 参数按值传递。

**OUT**

The value of an OUT parameter can be changed inside the stored procedure and its new value is passed back to the calling program. 

**INOUT**

a combination of IN  and OUT  parameters. It means that the calling program may pass the argument, and the stored procedure can modify the INOUT parameter, and pass the new value back to the calling program.

## 3、Mysql变量

### 局部变量

> 仅作用于 sql 语句块中，比如存储过程的`begin/end`。语句执行完毕后，局部变量就消失了。

基本语法：`DECLARE var_name [, var_name]... data_type [ DEFAULT value ];`

### 用户变量

> 用户变量作用于当前 session 连接。

基本语法：`SET | SELECT @var_name:=expr [, @var_name:=expr]...;`

## 4、流程控制

### 条件分支

```sql
IF condition THEN
   statements;
ELSEIF condition THEN
   statements;
...
ELSE
   statements;
END IF;
```

```sql
CASE case_value
   WHEN when_value1 THEN statements
   WHEN when_value2 THEN statements
   ...
   [ELSE else-statements]
END CASE;
```

### 循环

WHILE Loop、REPEAT Loop

- LEAVE：`break`

- ITERATE：`continue`

```sql
-- The loop_label  before the LOOPstatement for using with the ITERATE and LEAVE statements.
[begin_label:] LOOP
    statement_list
END LOOP [end_label]
```

# Cursor

可以在存储过程、存储函数和触发器中使用 Mysql 游标。

用于临时存储一个查询返回的多行数据（结果集，类似于java的jdbc链接返回额ResultSet集合），游标允许您迭代查询返回的一组行并分别处理每一行。

游标的使用方式：声明-->打开-->读取-->关闭

- Read-only：cannot update data in the underlying table through the cursor.

- Non-Scrollable：only fetch rows in the order determined by the SELECT statement. 

<div align="center">  <img src="MySQL-cursor" width="75%" height="75%" /></div><br>


设备文件（逻辑） | 物理设备 | 驱动
------------ | ------------- | -------------
/dev/printer1 | hp | 1024
/dev/printer2 | dell | 2046
.. | .. | ..



## 参考

[基于Oracle数据库存储过程的创建及调用](https://www.cnblogs.com/jianshuai520/p/11772766.html#%E7%A8%8B%E5%BA%8F%E7%BB%93%E6%9E%84)

[Introduction to MySQL Stored Procedures](http://www.mysqltutorial.org/introduction-to-sql-stored-procedures.aspx#)

[MySql中的变量定义](https://www.cnblogs.com/zhuawang/p/4090916.html)