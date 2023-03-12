# mysql 速查

连接 ：

```
mysql [-h 127.0.0.1] [-P 3306] -u root -p 
```

> 参数：
>
> -h : 	MySQL服务所在的主机IP 
>
> -P : 	MySQL服务端口号， 默认3306 
>
> -u : 	MySQL数据库用户名 
>
> -p ： MySQL数据库用户名对应的密码 

## SQL

### DDL

#### 数据库操作

查询所有数据库： `show databases;`

查询当前数据库：`select database();`

创建数据库：

```
create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];
```

删除数据库：

```
drop database [if exist] 数据库名;
```

切换数据库：

```
use 数据库名;
```

#### 表操作

查询当前数据库所有表：

```
show tables;
```

查看指定表结构：

```
desc 表名;
```

查询指定表的建表语句：

```
show create table 表名 ;
```

通过这条指令，主要是用来查看建表语句的，而有部分参数我们在创建表的时候，并未指定也会查询到，因为这部分是数据库的默认值，如：存储引擎、字符集等

创建表：

```
CREATE TABLE 表名
(
    字段1 字段1类型 [ COMMENT 字段1注释 ],
    字段2 字段2类型 [COMMENT 字段2注释 ],
    ) [ COMMENT 表注释 ];
```

##### 表操作 修改

添加字段：

```
ALTER TABLE 表名 ADD 字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];
```

修改数据类型：

```
ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);
```

修改字段名和字段类型：

```
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];
```

删除字段：

```
ALTER TABLE 表名 DROP 字段名;
```

修改表名：

```
ALTER TABLE 表名 RENAME TO 新表名;
```

##### 表操作 删除

1. 删除表：

   ```
   DROP TABLE [IF EXISTS] 表名;	
   ```

2. 删除指定表，并重新创建表

   ```
   TRUNCATE TABLE 表名;
   ```

   

### DML

#### 添加数据

1. 给指定字段添加数据

   ```
   INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);
   ```

2. 给全部字段添加数据

   ```
   INSERT INTO 表名 VALUES (值1, 值2, ...);
   ```

3. 批量添加数据

   ```
   INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值 1, 值2, ...) ;
   ```

#### 修改数据

1. 修改数据

   ```
   UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ;
   ```

   > 注意事项:
   >
   > 修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据。

#### 删除数据

```
DELETE FROM 表名 [ WHERE 条件 ] ;
```

> 注意事项:
>
> • DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据。
>
> • DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即 可)

### DQL

#### 基本语法

```
SELECT
	字段列表 
FROM
	表名列表 
WHERE
	条件列表 
GROUP BY 
	分组字段列表 
HAVING
	分组后条件列表 
ORDER BY 
	排序字段列表 
LIMIT
	分页参数 
```

我们在讲解这部分内容的时候，会将上面的完整语法进行拆分，分为以下几个部分：

- 基本查询（不带任何条件）
- 条件查询（WHERE）
- 聚合函数（count、max、min、avg、sum）
- 分组查询（group by）
- 排序查询（order by）
- 分页查询（limit） 

#### 基础查询

1. 查询多个字段

   ```
   SELECT 字段1, 字段2, 字段3 ... FROM 表名 ;
   
   SELECT * FROM 表名 ;
   ```

2. 字段设置别名

   ```
   SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名;
   
   SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名;
   ```

3. 去除重复记录

   ```
   SELECT DISTINCT 字段列表 FROM 表名;
   ```

#### 条件查询

```
SELECT 字段列表 FROM 表名 WHERE 条件列表 ;
```

##### 条件

常用的比较运算符如下：

![image-20230204001149544](https://img.herrluk.icu/typoraPicture/2023-02-04-00:11:49-Fh0anZ.png)

**非空查询**：

```
select * from emp where idcard is not null;
```

**模糊查询**

查询姓名为两个字的员工信息 

```
select * from emp where name like '_ _';
```

 查询身份证号最后一位是X的员工信息

```
select * from emp where idcard like '%X'; 
select * from emp where idcard like '_________________X';
```

常用的逻辑运算符如下：

![image-20230204001225826](https://img.herrluk.icu/typoraPicture/2023-02-04-00:12:25-EHt7oE.png)

**不等于的两种写法：**

```sql
select * from emp where age != 88; 
select * from emp where age <> 88;
```

 查询年龄在15岁(包含) 到 20岁(包含)之间的员工信息:

```
select * from emp where age >= 15 && age <= 20; 
select * from emp where age >= 15 and age <= 20; 
select * from emp where age between 15 and 20;
```

**Between…and和 and 是一致的**

#### 聚合函数

##### 介绍

将一列数据作为一个整体，进行纵向计算 。

##### 常见的聚合函数

![image-20230204001313991](https://img.herrluk.icu/typoraPicture/2023-02-04-00:13:14-HdDMLM.png)

##### 语法

```
SELECT 聚合函数(字段列表) FROM 表名 ; 
```

> **注意 : NULL值是不参与所有聚合函数运算的。**

##### 案例：

A. 统计该企业员工数量

```
select count(*) from emp; 	// 统计的是总记录数 
select count(idcard) from emp; 	// 统计的是idcard字段不为null的记录数
```

**null 值不参与计数！**

对于count聚合函数，统计符合条件的总记录数，还可以通过 count(数字/字符串)的形式进行统计查询，比如：

```
select count(1) from emp;
// 对于count(*) 、count(字段)、 count(1) 的具体原理，我们在进阶篇中SQL优化部分会详
细讲解，此处大家只需要知道如何使用即可。
```

#### 分组查询

##### 语法

```
SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组 后过滤条件 ];
```

##### where 与 having 的区别

- **执行时机不同**：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。
- **判断条件不同**：where不能对聚合函数进行判断，而having可以

> 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。
>
> 执行顺序: where > 聚合函数 > having 。
>
> 支持多字段分组, 具体语法为 : group by columnA,columnB

##### 案例

A. 根据性别分组 , 统计男性员工 和 女性员工的数量

```
select gender, count(*) from emp group by gender ; 
```

![image-20230308185135805](https://img.herrluk.icu/typoraPicture/2023-03-08-18:51:36-mErT4f.png)

```
select count(*) from emp where age<=60 group by gender;
```

![image-20230308185206486](https://img.herrluk.icu/typoraPicture/2023-03-08-18:52:06-tUNah8.png)

B. 根据性别分组 , 统计男性员工 和 女性员工的平均年龄

```
select gender, avg(age) from emp group by gender ; 
```

C. 查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址

```
select workaddress, count(*) address_count from emp where age < 45 group by workaddress having address_count >= 3;
```

D. 统计各个工作地址上班的男性及女性员工的数量

```
select workaddress, gender, count(*) '数量' from emp group by gender , workaddress;
```

#### 排序查询

排序在日常开发中是非常常见的一个操作，有升序排序，也有降序排序

##### 语法

```
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;
```

##### 排序方式

1. ASC : 升序(默认值)
2. DESC: 降序

> 注意事项：
>
> - 如果是**升序, 可以不指定排序方式ASC ;**
> - 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序 ;

##### 案例

A. 根据年龄对公司的员工进行升序排序

```
select * from emp order by age asc;
```

B. 根据入职时间, 对员工进行降序排序

```
select * from emp order by entrydate desc;
```

C. 根据年龄对公司的员工进行升序排序 , 年龄相同 , 再按照入职时间进行降序排序

```
select * from emp order by age asc , entrydate desc;
```

#### 分页查询

分页操作在业务系统开发时，也是非常常见的一个功能，我们在网站中看到的各种各样的分页条，后台都需要借助于数据库的分页操作。

##### 语法

```
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ;
```

> 注意事项:
>
> - 起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
> - 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
> - 如果查询的是第一页数据，起始索引可以省略，直接简写为 limit 10。

##### 案例

A. 查询第1页员工数据, 每页展示10条记录

```
select * from emp limit 0,10; 
select * from emp limit 10;
```

B. 查询第2页员工数据, 每页展示10条记录 --------> **(页码-1)*页展示记录数**

```
select * from emp limit 10,10;
```

#### 执行顺序

在讲解DQL语句的具体语法之前，我们已经讲解了DQL语句的完整语法，及编写顺序，接下来，我们要来说明的是DQL语句在执行时的执行顺序，也就是先执行那一部分，后执行那一部分。

![image-20230204154205792](https://img.herrluk.icu/typoraPicture/2023-02-04-15:42:05-YoW1xu.png)

> 综上所述，我们可以看到DQL语句的执行顺序为： 
>
> from ... where ... group by ...having ... select ... order by ... limit ...

### DCL

#### 管理用户

##### 查询用户

```
select * from mysql.user;
```

查询结果：

![image-20230204165111930](https://img.herrluk.icu/typoraPicture/2023-02-04-16:51:12-T5rwLZ.png)

其中 Host代表当前用户访问的主机, 如果为localhost, 仅代表只能够在当前本机访问，是不可以远程访问的。 User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一个用户。

##### 创建用户

```
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
```

##### 修改密码

```
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;
```

##### 删除用户

```
DROP USER '用户名'@'主机名' ;
```

> 注意事项:
>
> - 在MySQL中需要通过用户名@主机名的方式，来唯一标识一个用户。
> - 主机名可以使用 % 通配。
> - 这类SQL开发人员操作的比较少，主要是DBA（ Database Administrator 数据库管理员）使用。

##### 案例

A. 创建用户itcast, 只能够在当前主机localhost访问, 密码123456;

```
create user 'itcast'@'localhost' identified by '123456'; 
```

B. 创建用户heima, 可以在任意主机访问该数据库, 密码123456;

```
create user 'heima'@'%' identified by '123456';
```

C. 修改用户heima的访问密码为1234;

```
alter user 'heima'@'%' identified with mysql_native_password by '1234';
```

D. 删除 itcast@localhost 用户

```
drop user 'itcast'@'localhost';
```

#### 权限控制

MySQL中定义了很多种权限，但是常用的就以下几种：

![image-20230204165942952](https://img.herrluk.icu/typoraPicture/2023-02-04-16:59:43-tWtDpm.png)

##### 查询权限

```
SHOW GRANTS FOR '用户名'@'主机名' ;
```

##### 授予权限

```
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
```

##### 撤销权限

```
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```

> 注意事项：
>
> - 多个权限之间，使用逗号分隔
> - **授权时， 数据库名和表名可以使用 * 进行通配，代表所有。**



## 函数

### 字符串函数

MySQL中内置了很多字符串函数，常用的几个如下：

![image-20230206205452854](https://img.herrluk.icu/typoraPicture/2023-02-07-16:51:10-u040R6.png)



### 数值函数

常见的数值函数如下：

![image-20230206211111137](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:33-KWry6n.png)



### 日期函数

常见的日期函数如下：

![image-20230206211355033](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:33-t6VQWM.png)



### 流程函数

流程函数也是很常用的一类函数，可以在SQL语句中实现条件筛选，从而提高语句的效率。

![image-20230206213322389](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:34-P99vbR.png)

## 约束

### 概述

概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

目的：保证数据库中数据的正确、有效性和完整性。

分类:

![image-20230206214227247](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:35-QBXTL4.png)

> 注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。

### 外键约束

在关系型数据库中，外键（Foreign Key）是指一个表中的字段（或字段组合），它的值必须在另一个表的主键中存在。外键用于建立不同表之间的关联关系，保证数据的完整性和一致性。

#### 语法

##### 添加外键

```
CREATE TABLE 表名(
字段名 数据类型,
...
[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);
```

```
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名)
REFERENCES 主表 (主表列名) ;
```

案例: 

为emp表的dept_id字段添加外键约束,关联dept表的主键id。

```
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
```

##### 删除外键

```
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称; 
```

#### 删除/更新行为

添加了外键之后，再删除父表数据时产生的约束行为，我们就称为删除/更新行为。具体的删除/更新行为有以下几种:

![image-20230206221641823](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:39-HeNZrD.png)

具体语法为:

```
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES
主表名 (主表字段名) ON UPDATE CASCADE ON DELETE CASCADE;
```

演示如下：

1. cascade

   ```
   alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade ;
   ```

2. set null

   ```
   alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update set null on delete set null ;
   ```

   

# SQL 通用语法

在学习具体的SQL语句之前，先来了解一下SQL语言的同于语法。

> 1. SQL语句可以单行或多行书写，以分号结尾。
>
> 2. SQL语句可以使用空格/缩进来增强语句的可读性。
>
> 3. MySQL数据库的SQL语句不区分大小写，关键字建议使用大写。
>
> 4. 注释：
>
>    单行注释：-- 注释内容 或 # 注释内容
>
>    多行注释：/* 注释内容 */

SQL语句，根据其功能，主要分为四类：DDL、DML、DQL、DCL。 

**DDL(Data Definition Language)**:	 数据定义语言，用来定义数据库对象(数据库，表，字段)

**DML Data Manipulation Language**:	数据操作语言，用来对数据库表中的数据进行增删改

**DQL Data Query Language**:	 数据查询语言，用来查询数据库中表的记录

**DCL Data Control Language**:	数据控制语言，用来创建数据库用户、控制数据库的访问权限

## DDL

### 数据库操作

#### 查询

##### 查询所有数据库

```
show databases ; 
```

##### 查询当前数据库

```
select database() ; 
```

#### 创建数据库

```
create database [ if not exists ] 数据库名 [ default charset 字符集 ] [ collate 排序 规则 ] ;
```

#### 删除

```
drop database [ if exists ] 数据库名 ;
```

#### 使用/切换数据库

```
use 数据库名 ;
```

我们要操作某一个数据库下的表时，就需要通过该指令，切换到对应的数据库下，否则是不能操作的。

### 表操作

#### 查询创建数据库

##### 查询当前数据库所有表

```
show tables;
```

比如,我们可以切换到sys这个系统数据库,并查看系统数据库中的所有表结构。

```
use sys; 
show tables;
```

##### 查看指定表结构

```
desc 表名 ;
```

通过这条指令，我们可以查看到指定表的字段，字段的类型、是否可以为NULL，是否存在默认值等信息。

**是`DESCRIBE`的缩写**

##### 查询指定表的建表语句

```
show create table 表名 ;
```

通过这条指令，主要是用来查看建表语句的，而有部分参数我们在创建表的时候，并未指定也会查询到，因为这部分是数据库的默认值，如：存储引擎、字符集等。

##### 创建表结构

```
CREATE TABLE 表名( 
字段1 字段1类型 [ COMMENT 字段1注释 ], 
字段2 字段2类型 [COMMENT 字段2注释 ], 
字段3 字段3类型 [COMMENT 字段3注释 ], 
...... 
字段n 字段n类型 [COMMENT 字段n注释 ] 
) [ COMMENT 表注释 ] ; 


```

```
create table tb_user( 

id int comment '编号', 

name varchar(50) comment '姓名', 

age int comment '年龄', 

gender varchar(1) comment '性别' 

) comment '用户表'; 
```

> ​	**字段注释用单引号！** **不是双引号**

### 表操作-数据类型

MySQL中的数据类型有很多，主要分为三类：数值类型、字符串类型、日期时间类型。

####  数值类型

**MySQL 支持所有标准 SQL 数值数据类型。**

这些类型包括严格数值数据类型(INTEGER、SMALLINT、DECIMAL 和 NUMERIC)，以及近似数值数据类型(FLOAT、REAL 和 DOUBLE PRECISION)。

**关键字INT是INTEGER的同义词，关键字DEC是DECIMAL的同义词。**

BIT数据类型保存位字段值，并且支持 MyISAM、MEMORY、InnoDB 和 BDB表。

作为 SQL 标准的扩展，MySQL 也支持整数类型 TINYINT、MEDIUMINT 和 BIGINT。下面的表显示了需要的每个整数类型的存储和范围。

| 类型         | 大小                                     | 范围（有符号）                                               | 范围（无符号）                                               | 用途            |
| :----------- | :--------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :-------------- |
| TINYINT      | 1 Bytes                                  | (-128，127)                                                  | (0，255)                                                     | 小整数值        |
| SMALLINT     | 2 Bytes                                  | (-32 768，32 767)                                            | (0，65 535)                                                  | 大整数值        |
| MEDIUMINT    | 3 Bytes                                  | (-8 388 608，8 388 607)                                      | (0，16 777 215)                                              | 大整数值        |
| INT或INTEGER | 4 Bytes                                  | (-2 147 483 648，2 147 483 647)                              | (0，4 294 967 295)                                           | 大整数值        |
| BIGINT       | 8 Bytes                                  | (-9,223,372,036,854,775,808，9 223 372 036 854 775 807)      | (0，18 446 744 073 709 551 615)                              | 极大整数值      |
| FLOAT        | 4 Bytes                                  | (-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38) | 0，(1.175 494 351 E-38，3.402 823 466 E+38)                  | 单精度 浮点数值 |
| DOUBLE       | 8 Bytes                                  | (-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308) | 双精度 浮点数值 |
| DECIMAL      | 对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2 | 依赖于M和D的值**M(精度，所有位数)D(标度，小数的位数)**       | 依赖于M和D的值                                               | 小数值          |

例子：

1). 年龄字段 -- 不会出现负数, 而且人的年龄不会太大 

```
age tinyint unsigned 
```

2). 分数 -- 总分100分, 最多出现一位小数 

```
score double(4,1) 
```



#### 日期和时间类型

表示时间值的日期和时间类型为DATETIME、DATE、TIMESTAMP、TIME和YEAR。

每个时间类型有一个有效值范围和一个"零"值，当指定不合法的MySQL不能表示的值时使用"零"值。

TIMESTAMP类型有专有的自动更新特性，将在后面描述。

| 类型      | 大小 ( bytes) | 范围                                                         | 格式                | 用途                     |
| :-------- | :------------ | :----------------------------------------------------------- | :------------------ | :----------------------- |
| DATE      | 3             | 1000-01-01/9999-12-31                                        | YYYY-MM-DD          | 日期值                   |
| TIME      | 3             | '-838:59:59'/'838:59:59'                                     | HH:MM:SS            | 时间值或持续时间         |
| YEAR      | 1             | 1901/2155                                                    | YYYY                | 年份值                   |
| DATETIME  | 8             | '1000-01-01 00:00:00' 到 '9999-12-31 23:59:59'               | YYYY-MM-DD hh:mm:ss | 混合日期和时间值         |
| TIMESTAMP | 4             | `'1970-01-01 00:00:01'` UTC 到 `'2038-01-19 03:14:07' UTC`结束时间是第 **2147483647** 秒，北京时间 **2038-1-19 11:14:07**，格林尼治时间 2038年1月19日 凌晨 03:14:07 | YYYY-MM-DD hh:mm:ss | 混合日期和时间值，时间戳 |

------

#### 字符串类型

字符串类型指CHAR、VARCHAR、BINARY、VARBINARY、BLOB、TEXT、ENUM和SET。该节描述了这些类型如何工作以及如何在查询中使用这些类型。

| 类型       | 大小                  | 用途                            |
| :--------- | :-------------------- | :------------------------------ |
| CHAR       | 0-255 bytes           | 定长字符串                      |
| VARCHAR    | 0-65535 bytes         | 变长字符串                      |
| TINYBLOB   | 0-255 bytes           | 不超过 255 个字符的二进制字符串 |
| TINYTEXT   | 0-255 bytes           | 短文本字符串                    |
| BLOB       | 0-65 535 bytes        | 二进制形式的长文本数据          |
| TEXT       | 0-65 535 bytes        | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据                    |

**注意**：char(n) 和 varchar(n) 中括号中 n 代表字符的个数，并不代表字节个数，比如 CHAR(30) 就可以存储 30 个字符。

CHAR 和 VARCHAR 类型类似，但它们保存和检索的方式不同。它们的最大长度和是否尾部空格被保留等方面也不同。在存储或检索过程中不进行大小写转换。

char 与 varchar 都可以描述字符串，char是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关 。而varchar是变长字符串，指定的长度为最大占用长度 。相对来说，char的性能会更高些。

BINARY 和 VARBINARY 类似于 CHAR 和 VARCHAR，不同的是它们包含二进制字符串而不要非二进制字符串。也就是说，它们包含字节字符串而不是字符字符串。这说明它们没有字符集，并且排序和比较基于列值字节的数值值。

BLOB 是一个二进制大对象，可以容纳可变数量的数据。有 4 种 BLOB 类型：TINYBLOB、BLOB、MEDIUMBLOB 和 LONGBLOB。它们区别在于可容纳存储范围不同。

有 4 种 TEXT 类型：TINYTEXT、TEXT、MEDIUMTEXT 和 LONGTEXT。对应的这 4 种 BLOB 类型，可存储的最大长度不同，可根据实际情况选择。

**例子**

如： 

1). 用户名 username ------> 长度不定, 最长不会超过50 

```
username varchar(50) 
```

2). 性别 gender ---------> 存储值, 不是男,就是女 

```
gender char(1) 
```

3). 手机号 phone --------> 固定长度为11 

```
phone char(11)
```



### 表操作——案例

设计一张员工信息表，要求如下：

1. 编号（纯数字）
2. 员工工号 (字符串类型，长度不超过10位) 
3. 员工姓名（字符串类型，长度不超过10位）
4. 性别（男/女，存储一个汉字）
5. 年龄（正常人年龄，不可能存储负数）
6. 身份证号（二代身份证号均为18位，身份证中有X这样的字符）
7. 入职时间（取值年月日即可）

对应的建表语句如下：

```
create table emp( 

id int comment '编号', 

workno varchar(10) comment '工号', 

name varchar(10) comment '姓名', 

gender char(1) comment '性别', 

age tinyint unsigned comment '年龄', 

idcard char(18) comment '身份证号', 

entrydate date comment '入职时间' 

) comment '员工表'; 
```

### 表操作——修改

#### 添加字段

```
ALTER TABLE 表名 ADD 字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];
```

案例: 

为emp表增加一个新的字段”昵称”为nickname，类型为varchar(20)

```
ALTER TABLE emp ADD nickname varchar(20) COMMENT '昵称';
```

#### 修改数据类型

```
ALTER TABLE 表名 MODIFY 字段名 新数据类型 (长度);
```

#### 修改字段名和字段类型

```
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型 (长度) [ COMMENT 注释 ] [ 约束 ];
```

案例: 

将emp表的nickname字段修改为username，类型为varchar(30)

```
ALTER TABLE emp CHANGE nickname username varchar(30) COMMENT '昵称';
```

#### 删除字段

```
ALTER TABLE 表名 DROP 字段名;

//例子
ALTER TABLE emp DROP username;
```

#### 修改表名

```
ALTER TABLE 表名 RENAME TO 新表名;

//例子
ALTER TABLE emp RENAME TO employee;
```

### 表操作——删除

#### 删除表

```
DROP TABLE [ IF EXISTS ] 表名;

// 例子
DROP TABLE IF EXISTS tb_user;
```

#### 删除指定表，并重新创建表

```
TRUNCATE TABLE 表名;
```

## DML

DML英文全称是Data Manipulation Language(数据操作语言)，用来对数据库中表的数据记录进行增、删、改操作。

- 添加数据（INSERT）
- 修改数据（UPDATE）
- 删除数据（DELETE） 

### 添加数据

#### 给指定字段添加数据

```
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);

//
insert into employee(id,workno,name,gender,age,idcard,entrydate) values(1,'1','Itcast','男',10,'123456789012345678','2000-01-01');
```

#### 给全部字段添加数据

```
INSERT INTO 表名 VALUES (值1, 值2, ...);

//
insert into employee values(2,'2','张无忌','男',18,'123456789012345670','2005-01- 01');
```

#### 批量添加数据

```
INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值 1, 值2, ...) ;

INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ; 1
```

注意事项:

- 插入数据时，指定的字段顺序需要与值的顺序是一一对应的

- 字符串和日期型数据应该包含在引号中。
- 插入的数据大小，应该在字段的规定范围内。

### 修改数据

```
UPDATE 表名 SET 字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE 条件 ] ; 

//
update employee set name = '小昭' , gender = '女' where id = 1;
update employee set entrydate = '2008-01-01';
```

注意事项:

修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据。

### 删除数据

```
DELETE FROM 表名 [ WHERE 条件 ] ;

//
delete from employee where gender = '女';
delete from employee;	//删除所有员工
```

注意事项:

- DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数据。
- DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即 可)。

## DQL

DQL英文全称是Data Query Language(数据查询语言)，数据查询语言，用来查询数据库中表的记录。

查询关键字: SELECT

在一个正常的业务系统中，查询操作的频次是要远高于增删改的，当我们去访问企业官网、电商网站，在这些网站中我们所看到的数据，实际都是需要从数据库中查询并展示的。而且在查询的过程中，可能还会涉及到条件、排序、分页等操作。

### 基本语法

```
SELECT
	字段列表 
FROM
	表名列表 
WHERE
	条件列表 
GROUP BY 
	分组字段列表 
HAVING
	分组后条件列表 
ORDER BY 
	排序字段列表 
LIMIT
	分页参数 
```

我们在讲解这部分内容的时候，会将上面的完整语法进行拆分，分为以下几个部分：

- 基本查询（不带任何条件）
- 条件查询（WHERE）
- 聚合函数（count、max、min、avg、sum）
- 分组查询（group by）
- 排序查询（order by）
- 分页查询（limit） 

### 基础查询

在基本查询的DQL语句中，不带任何的查询条件，查询的语法如下：

#### 查询多个字段

```
SELECT 字段1, 字段2, 字段3 ... FROM 表名 ;
SELECT * FROM 表名 ; 
```

> 注意 :  `*` **号代表查询所有字段，在实际开发中尽量少用（不直观、影响效率）。**

#### 字段设置别名

```
SELECT 字段1 [ AS 别名1 ] , 字段2 [ AS 别名2 ] ... FROM 表名;
SELECT 字段1 [ 别名1 ] , 字段2 [ 别名2 ] ... FROM 表名;
```

#### 去除重复记录

```
SELECT DISTINCT 字段列表 FROM 表名;
```

`distinct` 关键字去重

### 条件查询

#### 语法

```
SELECT 字段列表 FROM 表名 WHERE 条件列表 ;
```

#### 条件

常用的比较运算符如下：

![image-20230204001149544](https://img.herrluk.icu/typoraPicture/2023-02-04-00:11:49-Fh0anZ.png)

**非空查询**：

```
select * from emp where idcard is not null;
```

**模糊查询**

查询姓名为两个字的员工信息 

```
select * from emp where name like '_ _';
```

 查询身份证号最后一位是X的员工信息

```
select * from emp where idcard like '%X'; 
select * from emp where idcard like '_________________X';
```



常用的逻辑运算符如下：

![image-20230204001225826](https://img.herrluk.icu/typoraPicture/2023-02-04-00:12:25-EHt7oE.png)



**不等于的两种写法：**

```sql
select * from emp where age != 88; 
select * from emp where age <> 88;
```

 查询年龄在15岁(包含) 到 20岁(包含)之间的员工信息:

```
select * from emp where age >= 15 && age <= 20; 
select * from emp where age >= 15 and age <= 20; 
select * from emp where age between 15 and 20;
```

**Between…and是和 and 一致的**

### 聚合函数

#### 介绍

将一列数据作为一个整体，进行纵向计算 。

#### 常见的聚合函数

![image-20230204001313991](https://img.herrluk.icu/typoraPicture/2023-02-04-00:13:14-HdDMLM.png)

#### 语法

```
SELECT 聚合函数(字段列表) FROM 表名 ; 
```

> **注意 : NULL值是不参与所有聚合函数运算的。**

##### 案例：

A. 统计该企业员工数量

```
select count(*) from emp; 	// 统计的是总记录数 
select count(idcard) from emp; 	// 统计的是idcard字段不为null的记录数
```

**null 值不参与计数！**

对于count聚合函数，统计符合条件的总记录数，还可以通过 count(数字/字符串)的形式进行统计查询，比如：

```
select count(1) from emp;
// 对于count(*) 、count(字段)、 count(1) 的具体原理，我们在进阶篇中SQL优化部分会详
细讲解，此处大家只需要知道如何使用即可。
```

B. 统计该企业员工的平均年龄

```
select avg(age) from emp;
```

C. 统计该企业员工的最大年龄

```
select max(age) from emp;
```

### 分组查询

#### 语法

```
SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组 后过滤条件 ];
```

#### where 与 having 的区别

- **执行时机不同**：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。
- **判断条件不同**：where不能对聚合函数进行判断，而having可以

> 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。
>
> 执行顺序: where > 聚合函数 > having 。
>
> 支持多字段分组, 具体语法为 : group by columnA,columnB

#### 案例

A. 根据性别分组 , 统计男性员工 和 女性员工的数量

```
select gender, count(*) from emp group by gender ; 
```

B. 根据性别分组 , 统计男性员工 和 女性员工的平均年龄

```
select gender, avg(age) from emp group by gender ; 
```

C. 查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址

```
select workaddress, count(*) address_count from emp where age < 45 group by workaddress having address_count >= 3;
```

D. 统计各个工作地址上班的男性及女性员工的数量

```
select workaddress, gender, count(*) '数量' from emp group by gender , workaddress;
```

### 排序查询

排序在日常开发中是非常常见的一个操作，有升序排序，也有降序排序

#### 语法

```
SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1 , 字段2 排序方式2 ;
```

#### 排序方式

1. ASC : 升序(默认值)
2. DESC: 降序

> 注意事项：
>
> - 如果是升序, 可以不指定排序方式ASC ;
> - 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序 ;

#### 案例

A. 根据年龄对公司的员工进行升序排序

```
select * from emp order by age asc;
```

B. 根据入职时间, 对员工进行降序排序

```
select * from emp order by entrydate desc;
```

C. 根据年龄对公司的员工进行升序排序 , 年龄相同 , 再按照入职时间进行降序排序

```
select * from emp order by age asc , entrydate desc;
```

### 分页查询

分页操作在业务系统开发时，也是非常常见的一个功能，我们在网站中看到的各种各样的分页条，后台都需要借助于数据库的分页操作。

#### 语法

```
SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数 ;
```

> 注意事项:
>
> - 起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
> - 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
> - 如果查询的是第一页数据，起始索引可以省略，直接简写为 limit 10。

#### 案例

A. 查询第1页员工数据, 每页展示10条记录

```
select * from emp limit 0,10; 
select * from emp limit 10;
```

B. 查询第2页员工数据, 每页展示10条记录 --------> **(页码-1)*页展示记录数**

```
select * from emp limit 10,10;
```

### 执行顺序

在讲解DQL语句的具体语法之前，我们已经讲解了DQL语句的完整语法，及编写顺序，接下来，我们要来说明的是DQL语句在执行时的执行顺序，也就是先执行那一部分，后执行那一部分。

![image-20230204154205792](https://img.herrluk.icu/typoraPicture/2023-02-04-15:42:05-YoW1xu.png)

> 综上所述，我们可以看到DQL语句的执行顺序为： 
>
> from ... where ... group by ...having ... select ... order by ... limit ...

## DCL

DCL英文全称是**Data Control Language**(数据控制语言)，用来管理数据库用户、控制数据库的访问权限。

### 管理用户

#### 查询用户

```
select * from mysql.user;
```

查询结果：

![image-20230204165111930](https://img.herrluk.icu/typoraPicture/2023-02-04-16:51:12-T5rwLZ.png)

其中 Host代表当前用户访问的主机, 如果为localhost, 仅代表只能够在当前本机访问，是不可以远程访问的。 User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一个用户。

#### 创建用户

```
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
```

#### 修改密码

```
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;
```

#### 删除用户

```
DROP USER '用户名'@'主机名' ;
```

> 注意事项:
>
> - 在MySQL中需要通过用户名@主机名的方式，来唯一标识一个用户。
> -  主机名可以使用 % 通配。
> - 这类SQL开发人员操作的比较少，主要是DBA（ Database Administrator 数据库管理员）使用。

#### 案例

A. 创建用户itcast, 只能够在当前主机localhost访问, 密码123456;

```
create user 'itcast'@'localhost' identified by '123456'; 
```

B. 创建用户heima, 可以在任意主机访问该数据库, 密码123456;

```
create user 'heima'@'%' identified by '123456';
```

C. 修改用户heima的访问密码为1234;

```
alter user 'heima'@'%' identified with mysql_native_password by '1234';
```

D. 删除 itcast@localhost 用户

```
drop user 'itcast'@'localhost';
```

### 权限控制

MySQL中定义了很多种权限，但是常用的就以下几种：

![image-20230204165942952](https://img.herrluk.icu/typoraPicture/2023-02-04-16:59:43-tWtDpm.png)

##### 查询权限

```
SHOW GRANTS FOR '用户名'@'主机名' ;
```

##### 授予权限

```
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';
```

##### 撤销权限

```
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```

> 注意事项：
>
> - 多个权限之间，使用逗号分隔
> - 授权时， 数据库名和表名可以使用 * 进行通配，代表所有。

# 函数

我们先来看两个场景：

1). 在企业的OA或其他的人力系统中，经常会提供的有这样一个功能，每一个员工登录上来之后都能够看到当前员工入职的天数。 而在数据库中，存储的都是入职日期，如 2000-11-12，那如果快速计算出天数呢？

2). 在做报表这类的业务需求中,我们要展示出学员的分数等级分布。而在数据库中，存储的是学生的分数值，如98/75，如何快速判定分数的等级呢？

其实，上述的这一类的需求呢，我们通过MySQL中的函数都可以很方便的实现 。MySQL中的函数主要分为以下四类： 字符串函数、数值函数、日期函数、流程函数

## 字符串函数

MySQL中内置了很多字符串函数，常用的几个如下：

![image-20230206205452854](https://img.herrluk.icu/typoraPicture/2023-02-07-16:51:10-u040R6.png)

演示如下：

A. concat : 字符串拼接

```
select concat('Hello' , ' MySQL');
```

B. lower : 全部转小写

```
select lower('Hello');
```

C. upper : 全部转大写

```
select upper('Hello');
```

D. lpad : 左填充

```
select lpad('01', 5, '-');
```

E. rpad : 右填充

```
select rpad('01', 5, '-');
```

F. trim : 去除空格

```
select trim(' Hello MySQL ');
```

G. substring : 截取子字符串

```
select substring('Hello MySQL',1,5);
```

#### 案例：

企业员工的工号，统一为5位数，目前不足5位数的全部在前面补0。比如： 1号员工的工号应该为00001。

```
update emp set workno = lpad(workno, 5, '0');
```

## 数值函数

常见的数值函数如下：

![image-20230206211111137](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:33-KWry6n.png)

演示如下：

A. ceil：向上取整

```
select ceil(1.1);
```

B. floor：向下取整

```
select floor(1.9);
```

C. mod：取模

```
select mod(7,4);
```

D. rand：获取随机数

```
select rand();
```

E. round：四舍五入

```
select round(2.344,2);
```

案例：

通过数据库的函数，生成一个六位数的随机验证码。

思路： 获取随机数可以通过rand()函数，但是获取出来的随机数是在0-1之间的，所以可以在其基础上乘以1000000，然后舍弃小数部分，如果长度不足6位，补0

```
select lpad(round(rand()*1000000 , 0), 6, '0');
```

## 日期函数

常见的日期函数如下：

![image-20230206211355033](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:33-t6VQWM.png)

演示如下

A. curdate：当前日期

```
select curdate();
```

B. curtime：当前时间

```
select curtime();
```

C. now：当前日期和时间

```
select now();
```

D. YEAR , MONTH , DAY：当前年、月、日

```
select YEAR(now());
select MONTH(now());
select DAY(now());
```

E. date_add：增加指定的时间间隔

```
select date_add(now(), INTERVAL 70 YEAR );
```

F. datediff：获取两个日期相差的天数

```
select datediff('2021-10-01', '2021-12-01');
```

案例：

查询所有员工的入职天数，并根据入职天数倒序排序。

思路： 入职天数，就是通过当前日期 - 入职日期，所以需要使用datediff函数来完成。

```
select name, datediff(curdate(), entrydate) as 'entrydays' from emp order by
entrydays desc;
```

## 流程函数

流程函数也是很常用的一类函数，可以在SQL语句中实现条件筛选，从而提高语句的效率。

![image-20230206213322389](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:34-P99vbR.png)

演示如下：

A. if

```
select if(false, 'Ok', 'Error');
```

B. ifnull

```
select ifnull('Ok','Default');
select ifnull('','Default');
select ifnull(null,'Default');
```

C. case when then else end

需求: 查询emp表的员工姓名和工作地址 (北京/上海 ----> 一线城市 , 其他 ----> 二线城市)

```
select
name,
( case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else
'二线城市' end ) as '工作地址'
from emp;
```

案例

```
create table score(
id int comment 'ID',
name varchar(20) comment '姓名',
math int comment '数学',
english int comment '英语',
chinese int comment '语文'
) comment '学员成绩表';
insert into score(id, name, math, english, chinese) VALUES (1, 'Tom', 67, 88, 95
), (2, 'Rose' , 23, 66, 90),(3, 'Jack', 56, 98, 76);
```

具体的SQL语句如下:

```
select
id,
name,
(case when math >= 85 then '优秀' when math >=60 then '及格' else '不及格' end )
'数学',
(case when english >= 85 then '优秀' when english >=60 then '及格' else '不及格'
end ) '英语',
(case when chinese >= 85 then '优秀' when chinese >=60 then '及格' else '不及格'
end ) '语文'
from score;
```

MySQL的常见函数我们学习完了，那接下来，我们就来分析一下，在前面讲到的两个函数的案例场景，

思考一下需要用到什么样的函数来实现?

1). 数据库中，存储的是入职日期，如 2000-01-01，如何快速计算出入职天数呢？ -------->

答案: datediff

2). 数据库中，存储的是学生的分数值，如98、75，如何快速判定分数的等级呢？ ---------->

答案: case ... when ...

# 约束

## 概述

概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

目的：保证数据库中数据的正确、有效性和完整性。

分类:

![image-20230206214227247](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:35-QBXTL4.png)

> 注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束。

## 约束演示

上面我们介绍了数据库中常见的约束，以及约束涉及到的关键字，那这些约束我们到底如何在创建表、修改表的时候来指定呢，接下来我们就通过一个案例，来演示一下。

案例需求： 根据需求，完成表结构的创建。需求如下：

![image-20230206214647542](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:37-aYDp85.png)

对应的建表语句为：

```
CREATE TABLE tb_user(
id int AUTO_INCREMENT PRIMARY KEY COMMENT 'ID唯一标识',
name varchar(10) NOT NULL UNIQUE COMMENT '姓名' ,
age int check (age > 0 && age <= 120) COMMENT '年龄' ,
status char(1) default '1' COMMENT '状态',
gender char(1) COMMENT '性别'
);
```

在为字段添加约束时，我们只需要在字段之后加上约束的关键字即可，需要关注其语法。我们执行上面的SQL把表结构创建完成，然后接下来，就可以通过一组数据进行测试，从而验证一下，约束是否可以生效。

```
insert into tb_user(name,age,status,gender) values ('Tom1',19,'1','男'),
('Tom2',25,'0','男');
insert into tb_user(name,age,status,gender) values ('Tom3',19,'1','男');
insert into tb_user(name,age,status,gender) values (null,19,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom3',19,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom4',80,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom5',-1,'1','男');
insert into tb_user(name,age,status,gender) values ('Tom5',121,'1','男');
insert into tb_user(name,age,gender) values ('Tom5',120,'男');
```

上面，我们是通过编写SQL语句的形式来完成约束的指定，那加入我们是通过图形化界面来创建表结构时，又该如何来指定约束呢？ 只需要在创建表的时候，根据我们的需要选择对应的约束即可。

## 外键约束

### 介绍

外键：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

我们来看一个例子：

![image-20230206215221134](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:37-96R9G0.png)

左侧的emp表是员工表，里面存储员工的基本信息，包含员工的ID、姓名、年龄、职位、薪资、入职日期、上级主管ID、部门ID，在员工的信息中存储的是部门的ID dept_id，而这个部门的ID是关联的部门表dept的主键id，那emp表的dept_id就是外键,关联的是另一张表的主键。

```
注意：目前上述两张表，只是在逻辑上存在这样一层关系；在数据库层面，并未建立外键关联，
所以是无法保证数据的一致性和完整性的。
```

没有数据库外键关联的情况下，能够保证一致性和完整性呢，我们来测试一下。

##### 准备数据

```
create table dept(
id int auto_increment comment 'ID' primary key,
name varchar(50) not null comment '部门名称'
)comment '部门表';
INSERT INTO dept (id, name) VALUES (1, '研发部'), (2, '市场部'),(3, '财务部'), (4,
'销售部'), (5, '总经办');
create table emp(
id int auto_increment comment 'ID' primary key,
name varchar(50) not null comment '姓名',
age int comment '年龄',
job varchar(20) comment '职位',
salary int comment '薪资',
entrydate date comment '入职时间',
managerid int comment '直属领导ID',
dept_id int comment '部门ID'
)comment '员工表';
INSERT INTO emp (id, name, age, job,salary, entrydate, managerid, dept_id)
VALUES
(1, '金庸', 66, '总裁',20000, '2000-01-01', null,5),(2, '张无忌', 20,
'项目经理',12500, '2005-12-05', 1,1),
(3, '杨逍', 33, '开发', 8400,'2000-11-03', 2,1),(4, '韦一笑', 48, '开
发',11000, '2002-02-05', 2,1),
(5, '常遇春', 43, '开发',10500, '2004-09-07', 3,1),(6, '小昭', 19, '程
序员鼓励师',6600, '2004-10-12', 2,1);
```

![image-20230206220903526](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:38-apcV2a.png)

接下来，我们可以做一个测试，删除id为1的部门信息。

![image-20230206220919088](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:38-CxBbFi.png)

结果，我们看到删除成功，而删除成功之后，部门表不存在id为1的部门，而在emp表中还有很多的员工，关联的为id为1的部门，此时就出现了数据的不完整性。 而要想解决这个问题就得通过数据库的外键约束。

### 语法

####  添加外键

```
CREATE TABLE 表名(
字段名 数据类型,
...
[CONSTRAINT] [外键名称] FOREIGN KEY (外键字段名) REFERENCES 主表 (主表列名)
);
```

```
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名)
REFERENCES 主表 (主表列名) ;
```

案例:

为emp表的dept_id字段添加外键约束,关联dept表的主键id。

```
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references
dept(id);
```

![image-20230206221036339](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:38-HJlOpr.png)

添加了外键约束之后，我们再到dept表(父表)删除id为1的记录，然后看一下会发生什么现象。 此时将会报错，不能删除或更新父表记录，因为存在外键约束。

#### 删除外键

```
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
```

#### 案例：

删除emp表的外键fk_emp_dept_id。

```
alter table emp drop foreign key fk_emp_dept_id;
```

### 删除/更新行为

添加了外键之后，再删除父表数据时产生的约束行为，我们就称为删除/更新行为。具体的删除/更新行为有以下几种:

![image-20230206221641823](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:39-HeNZrD.png)

具体语法为:

```
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段) REFERENCES
主表名 (主表字段名) ON UPDATE CASCADE ON DELETE CASCADE;
```

演示如下：

由于NO ACTION 是默认行为，我们前面语法演示的时候，已经测试过了，就不再演示了，这里我们再

演示其他的两种行为：CASCADE、SET NULL。

####  CASCADE

```
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references
dept(id) on update cascade on delete cascade ;
```

A. 修改父表id为1的记录，将id修改为6

![image-20230206222329305](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:41-b38L0O.png)

我们发现，原来在子表中dept_id值为1的记录，现在也变为6了，这就是cascade级联的效果。

> 在一般的业务系统中，不会修改一张表的主键值。

B. 删除父表id为6的记录

![image-20230206222402210](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:41-GaGmnd.png)

我们发现，父表的数据删除成功了，但是子表中关联的记录也被级联删除了。

####  SET NULL

在进行测试之前，我们先需要删除上面建立的外键 fk_emp_dept_id。然后再通过数据脚本，将emp、dept表的数据恢复了。

```
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references
dept(id) on update set null on delete set null ;
```

接下来，我们删除id为1的数据，看看会发生什么样的现象。

![image-20230206222517871](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:41-w5kxMV.png)

我们发现父表的记录是可以正常的删除的，父表的数据删除之后，再打开子表 emp，我们发现子表emp的dept_id字段，原来dept_id为1的数据，现在都被置为NULL了。

![image-20230206222737502](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:42-l4k5kd.png)

这就是SET NULL这种删除/更新行为的效果。

# 多表查询

我们之前在讲解SQL语句的时候，讲解了DQL语句，也就是数据查询语句，但是之前讲解的查询都是单表查询，而本章节我们要学习的则是多表查询操作，主要从以下几个方面进行讲解。

## 多表关系

项目开发中，在进行数据库表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：

- 一对多(多对一)
- 多对多
- 一对一

### 一对多

- 案例: 部门 与 员工的关系
- 关系: 一个部门对应多个员工，一个员工对应一个部门
- 实现: 在多的一方建立外键，指向一的一方的主键

![image-20230206222942117](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:43-izYfPc.png)

### 多对多

- 案例: 学生 与 课程的关系
- 关系: 一个学生可以选修多门课程，一门课程也可以供多个学生选择
- 实现: 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

![image-20230206223220499](https://img.herrluk.icu/typoraPicture/2023-02-07-16:53:43-arRQRD.png)

对应的SQL脚本

```
create table student(
id int auto_increment primary key comment '主键ID',
name varchar(10) comment '姓名',
no varchar(10) comment '学号'
) comment '学生表';
insert into student values (null, '黛绮丝', '2000100101'),(null, '谢逊',
'2000100102'),(null, '殷天正', '2000100103'),(null, '韦一笑', '2000100104');
create table course(
id int auto_increment primary key comment '主键ID',
name varchar(10) comment '课程名称'
) comment '课程表';
insert into course values (null, 'Java'), (null, 'PHP'), (null , 'MySQL') ,
(null, 'Hadoop');
create table student_course(
id int auto_increment comment '主键' primary key,
studentid int not null comment '学生ID',
courseid int not null comment '课程ID',
constraint fk_courseid foreign key (courseid) references course (id),
constraint fk_studentid foreign key (studentid) references student (id)
)comment '学生课程中间表';
insert into student_course values (null,1,1),(null,1,2),(null,1,3),(null,2,2),
(null,2,3),(null,3,4);
```

### 一对一

案例: 用户 与 用户详情的关系

关系: 一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率

实现: 在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)

![image-20230208160123173](https://img.herrluk.icu/typoraPicture/2023-02-08-16:01:23-osVSGl.png)

对应的 SQL 脚本：

```
create table tb_user
(
    id     int auto_increment primary key comment '主键ID',
    name   varchar(10) comment '姓名',
    age    int comment '年龄',
    gender char(1) comment '1: 男 , 2: 女',
    phone  char(11) comment '手机号'
) comment '用户基本信息表';

create table tb_user_edu
(
    id            int auto_increment primary key comment '主键ID',
    degree        varchar(20) comment '学历',
    major         varchar(50) comment '专业',
    primaryschool varchar(50) comment '小学',
    middleschool  varchar(50) comment '中学',
    university    varchar(50) comment '大学',
    userid        int unique comment '用户ID',
    constraint fk_userid foreign key (userid) references tb_user (id)
) comment '用户教育信息表';

insert into tb_user(id, name, age, gender, phone)
values (null, '黄渤', 45, '1', '18800001111'),
       (null, '冰冰', 35, '2', '18800002222'),
       (null, '码云', 55, '1', '18800008888'),
       (null, '李彦宏', 50, '1', '18800009999');

insert into tb_user_edu(id, degree, major, primaryschool, middleschool, university, userid)
values (null, '本科', '舞蹈', '静安区第一小学', '静安区第一中学', '北京舞蹈学院', 1),
       (null, '硕士', '表演', '朝阳区第一小学', '朝阳区第一中学', '北京电影学院', 2),
       (null, '本科', '英语', '杭州市第一小学', '杭州市第一中学', '杭州师范大学', 3),
       (null, '本科', '应用数学', '阳泉第一小学', '阳泉区第一中学', '清华大学', 4);
```

## 多表查询概述

### 数据准备

1. 删除之前 emp, dept表的测试数据

2. 执行如下脚本，创建emp表与dept表并插入测试数据

```
-- 创建dept表，并插入数据
create table dept
(
    id   int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '部门名称'
) comment '部门表';
INSERT INTO dept (id, name)
VALUES (1, '研发部'),
       (2, '市场部'),
       (3, '财务部'),
       (4, '销售部'),
       (5, '总经办'),
       (6, '人事部');
-- 创建emp表，并插入数据
create table emp
(
    id        int auto_increment comment 'ID' primary key,
    name      varchar(50) not null comment '姓名',
    age       int comment '年龄',
    job       varchar(20) comment '职位',
    salary    int comment '薪资',
    entrydate date comment '入职时间',
    managerid int comment '直属领导ID',
    dept_id   int comment '部门ID'
) comment '员工表';

-- 添加外键
alter table emp
    add constraint fk_emp_dept_id foreign key (dept_id) references dept (id);

INSERT INTO emp (id, name, age, job, salary, entrydate, managerid, dept_id)
VALUES (1, '金庸', 66, '总裁', 20000, '2000-01-01', null, 5),
       (2, '张无忌', 20, '项目经理', 12500, '2005-12-05', 1, 1),
       (3, '杨逍', 33, '开发', 8400, '2000-11-03', 2, 1),
       (4, '韦一笑', 48, '开发', 11000, '2002-02-05', 2, 1),
       (5, '常遇春', 43, '开发', 10500, '2004-09-07', 3, 1),
       (6, '小昭', 19, '程序员鼓励师', 6600, '2004-10-12', 2, 1),
       (7, '灭绝', 60, '财务总监', 8500, '2002-09-12', 1, 3),
       (8, '周芷若', 19, '会计', 48000, '2006-06-02', 7, 3),
       (9, '丁敏君', 23, '出纳', 5250, '2009-05-13', 7, 3),
       (10, '赵敏', 20, '市场部总监', 12500, '2004-10-12', 1, 2),
       (11, '鹿杖客', 56, '职员', 3750, '2006-10-03', 10, 2),
       (12, '鹤笔翁', 19, '职员', 3750, '2007-05-09', 10, 2),
       (13, '方东白', 19, '职员', 5500, '2009-02-12', 10, 2),
       (14, '张三丰', 88, '销售总监', 14000, '2004-10-12', 1, 4),
       (15, '俞莲舟', 38, '销售', 4600, '2004-10-12', 14, 4),
       (16, '宋远桥', 40, '销售', 4600, '2004-10-12', 14, 4),
       (17, '陈友谅', 42, null, 2000, '2011-10-12', 1, null);
```

dept表共6条记录，emp表共17条记录。

### 概述

多表查询就是指从多张表中查询数据。

原来查询单表数据，执行的SQL形式为：`select * from emp;`

那么我们要执行多表查询，就只需要使用逗号分隔多张表即可，如： `select * from emp , dept;` 

具体的执行结果如下:

![image-20230208161156754](https://img.herrluk.icu/typoraPicture/2023-02-08-16:11:56-on1qXb.png)

此时,我们看到查询结果中包含了大量的结果集，总共102条记录，而这其实就是员工表emp所有的记录(17) 与 部门表dept所有记录(6) 的所有组合情况，这种现象称之为**笛卡尔积**。接下来，就来简单介绍下笛卡尔积。

> 笛卡尔积: 笛卡尔乘积是指在数学中，两个集合A集合 和 B集合的所有组合情况。
>
> 而在多表查询中，我们是需要消除无效的笛卡尔积的，只保留两张表关联部分的数据。

**在SQL语句中，如何来去除无效的笛卡尔积呢？ 我们可以给多表查询加上连接查询的条件即可。**

```
select * from emp , dept where emp.dept_id = dept.id;
```

![image-20230208161444446](https://img.herrluk.icu/typoraPicture/2023-02-08-16:14:44-AS9zNd.png)

而由于id为17的员工，没有dept_id字段值，所以在多表查询时，根据连接查询的条件并没有查询到。

### 分类

**连接查询**

- 内连接：相当于查询A、B交集部分数据
- 外连接：
  - 左外连接：查询左表所有数据，以及两张表交集部分数据
  - 右外连接：查询右表所有数据，以及两张表交集部分数据
  - 自连接：当前表与自身的连接查询，自连接必须使用表别名

**子查询**

![image-20230208161719119](https://img.herrluk.icu/typoraPicture/2023-02-08-16:17:19-6Q1Zi5.png)

## 内连接

内连接查询的是两张表交集部分的数据。(也就是绿色部分的数据)

内连接的语法分为两种: 隐式内连接、显式内连接。先来学习一下具体的语法结构。

##### 隐式内连接

```
SELECT 字段列表 FROM 表1 , 表2 WHERE 条件 ... ; 1
```

##### 显式内连接

```
SELECT 字段列表 FROM 表1 [ INNER ] JOIN 表2 ON 连接条件 ... ;
```

##### 案例:

A. 查询每一个员工的姓名 , 及关联的部门的名称 (隐式内连接实现)

-  表结构: emp , dept
-  连接条件: emp.dept_id = dept.id

```
select emp.name, dept.name
from emp,
     dept
where emp.dept_id = dept.id;

-- 为每一张表起别名,简化SQL编写 
select e.name, d.name
from emp e,
     dept d
where e.dept_id = d.id;
```



> 表的别名: 
>
> - tablea as 别名1 , tableb as 别名2 ; 
> - tablea 别名1 , tableb 别名2 ;
>
> 注意事项: 
>
> 一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段.

## 外连接

![image-20230208161719119](https://img.herrluk.icu/typoraPicture/2023-02-08-16:24:58-qUZlUb.png)

外连接分为两种，分别是：左外连接 和 右外连接。具体的语法结构为：

##### 1. 左外连接

```
SELECT 字段列表 FROM 表1 LEFT [ OUTER ] JOIN 表2 ON 条件 ... ;
```

左外连接相当于查询表1(左表)的所有数据，当然也包含表1和表2交集部分的数据。

##### 2. 右外连接

```
SELECT 字段列表 FROM 表1 RIGHT [ OUTER ] JOIN 表2 ON 条件 ... ;
```

右外连接相当于查询表2(右表)的所有数据，当然也包含表1和表2交集部分的数据。

##### 案例:

A. 查询emp表的所有数据, 和对应的部门信息

 由于需求中提到，要查询emp的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。

-  表结构: emp, dept
-  连接条件: emp.dept_id = dept.id

```
select e.*, d.name
from emp e
         left outer join dept d on e.dept_id = d.id;

select e.*, d.name
from emp e
         left join dept d on e.dept_id = d.id;
```

B. 查询dept表的所有数据, 和对应的员工信息(右外连接) 

由于需求中提到，要查询dept表的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。

-  表结构: emp, dept
-  连接条件: emp.dept_id = dept.id

```
select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id; 

select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id;
```

> 注意事项：
>
> 左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺序就可以了。
>
> **而我们在日常开发使用时，更偏向于左外连接。**

## 自连接

### 自连接查询

自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。我们先来学习一下自连接的查询语法：

```
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件 ... ; 1
```

而对于自连接查询，可以是内连接查询，也可以是外连接查询。

##### 案例：

A. 查询员工 及其 所属领导的名字

 表结构: emp

```
select a.name , b.name from emp a , emp b where a.managerid = b.id;
```

B. 查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来

 表结构: emp a , emp b 

```
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = b.id;
```

> 注意事项:
>
> **在自连接查询中，必须要为表起别名**，要不然我们不清楚所指定的条件、返回的字段，到底是哪一张表的字段。

### 联合查询

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

```
SELECT 字段列表 FROM 表A ... 
UNION [ ALL ] 
SELECT 字段列表 FROM 表B ....;
```

- 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致。
- union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重

##### 案例:

A. 将薪资低于 5000 的员工 , 和 年龄大于 50 岁的员工全部查询出来.

当前对于这个需求，我们可以直接使用多条件查询，使用逻辑运算符 or 连接即可。 那这里呢，我们也可以通过union/union all来联合查询. 

```
select * from emp where salary < 5000 
union all 
select * from emp where age > 50;
```

**union all查询出来的结果，仅仅进行简单的合并，并未去重。**



```
select * from emp where salary < 5000 
union 
select * from emp where age > 50;
```

**union 联合查询，会对查询出来的结果进行去重处理。**

> **注意：**
>
> 如果多条查询语句查询出来的结果，字段数量不一致，在进行union/union all联合查询时，将会报错。

## 子查询

### 概述

#####  概念

SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询。

```
SELECT * FROM t1 WHERE column1 = ( SELECT column1 FROM t2 );
```

子查询外部的语句可以是INSERT / UPDATE / DELETE / SELECT 的任何一个

##### 分类

> 根据子查询结果不同，分为：
>
> A. 标量子查询（子查询结果为单个值）
>
> B. 列子查询(子查询结果为一列)
>
> C. 行子查询(子查询结果为一行)
>
> D. 表子查询(子查询结果为多行多列)



> 根据子查询位置，分为：
>
> A. WHERE之后
>
> B. FROM之后
>
> C. SELECT之后
