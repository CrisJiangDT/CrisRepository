<link href="http://cdn.bootcss.com/highlight.js/8.0/styles/monokai_sublime.min.css" rel="stylesheet">  
<script src="http://cdn.bootcss.com/highlight.js/8.0/highlight.min.js"></script>  
<script>hljs.initHighlightingOnLoad();</script>

# 范式

- 范式指的是设计数据库时需要遵循的规范
- 目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式（4NF）、第五范式（5NF，又称完美范式）
- 一般的数据库满足第三范式就足够了

##### 第一范式（1NF）

- 要求：**每一列都是不可分割的原子项**（在数据库中这一点总是满足，因为不可能有复合列）
- **1NF存在的问题**：例如在`(学号, 姓名, 系, 系主任, 选修课, 选修课分数)`这张表中：
 1. **数据冗余**：`(系主任)`出现冗余
 2. **插入存在问题**：想要插入一个系，但没有学生时，不合法
 3. **删除存在问题**：某一个系中的学生删除时，系的信息也会丢失

##### 第二范式（2NF）


- 要求：在1NF的基础上，非码属性必须完全函数依赖于候选码（即**在1NF的基础上消除非主属性对主码的部分函数依赖**）
- 例如在`(学号, 姓名, 系, 系主任, 选修课, 选修课分数)`这张表中：
 - 属性组`(学号, 选修课)`可以唯一确定其他属性，称为**码**，码中的属性`(学号)`和`(选修课)`称为**主属性**
 - 非主属性`(选修课分数)`完全依赖于码`(学号, 选修课)`
 - 其他非主属性部分依赖于码`(学号, 选修课)`，如`(姓名)`只依赖于`(学号)`而不依赖于`(选修课)`
 - 2NF就是要消除这种部份依赖
- **分表**可以满足2NF，即**将完全函数依赖于码的属性与码放在一起，将部分函数依赖与码的属性与码中的主属性单独放在一起**
 - 将`(学号, 选修课, 选修课分数)`作为选课成绩表
 - 将`(学号, 姓名, 系, 系主任)`作为学生表
- **2NF解决了1NF存在的数据冗余问题，但没有解决插入问题和删除问题**

##### 第三范式（3NF）

- 要求：在2NF的基础上，任何非主属性都不依赖于其他非主属性（即**在2NF的基础上消除传递依赖**）
- 例如在`(学号, 姓名, 系, 系主任)`这张表中：
 - 码为`(学号)`
 - `(系)`依赖于`(学号)`，`(系主任)`又依赖于`(系)`，因此`(系主任)`传递依赖于`(学号)`
 - 3NF就是要消除这种传递依赖
- **继续分表**可以满足3NF，即**将传递依赖的非主属性和其所依赖的非主属性单独放在一张表中**
 - 将`(系, 系主任)`作为系表
 - 将`(学号, 姓名, 系)`作为学生表
- **3NF解决了2NF存在的插入问题和删除问题**
---

# 数据库

- 常见的数据库：Oracle、DB2、MySQL、SQL Server、Sybase...
- **RDBMS（关系型数据库管理系统）**
 - RDBMS = Manager + database1 + database2 + ...
 - 上述常见的数据库其实是RDBMS
 - database = table1 + table2 +...
 - 表结构：定义表的列名和列类型
 - 表记录：表中的数据

## SQL

- SQL，即Structured Query Language，是操作数据库的语言
- SQL语句分类：
 - DDL：Data Definition Language，对数据库或表的结构操作
 - DML：Data Manipulation Language，对表的记录进行更新操作（增、删、改）
 - DQL：Data Query Language，对表的记录进行查询操作
 - DCL：Data Control Language，对用户的创建、授权等

### MySQL

```
连接数据库(一般需要管理员模式打开cmd)：	mysql -uroot -r
查看数据库：	SHOW DATABASES;
创建数据库：	CREATE DATABASE xxx;
切换数据库：	USE DATABASE xxx;
删除数据库：	DROP DATABASE xxx;
修改数据库编码：	ALERT DATABASE xxx CHARACTER SET utf8(不是utf-8)
```

#### 常用数据类型

```
int 整型
double 浮点型，如double(5, 2)表示最多5位，其中2位是小数，最大值为999.99
decimal 浮点型，在表单钱方面使用该类型，不会出现精度缺失问题
char(x) 固定长度字符串，如char(x)表示长度为x的字符串，长度不足时补足，但x最多只能为255
varchar(x) 可变长度字符串，没有补足操作，但需要浪费1个字节空间来存储长度
text(clob) 字符串类型
blob 字节类型
date 日期类型，只有年月日，格式为：yyyy-MM-dd
time 时间类型，只有时分秒，格式为：hh:mm:ss
timestamp 时间戳类型，既有年月日，又有时分秒
```

#### DDL（操作表结构）

- 创建表：`CREATE TABLE [IF NOT EXISTS] 表名(列名 列类型, 列名 列类型, ...);`
- 查看当前数据库中所有表名称：`SHOW TABLES;`
- 查看表结构：`DESC 表名;`
- 删除表：`DROP TABLE 表名;`
- 修改表：
 - 修改表名：`ALTER TABLE 表名 RENAME TO 新表名;`
 - 添加列：`ALTER TABLE 表名 ADD(列名 列类型, 列名 列类型, ...);`
 - 删除列：`ALTER TABLE 表名 DROP 列名;`
 - 修改列名：`ALTER TABLE 表名 CHANGE 原列名 新列名 列类型;`
 - 修改列的数据类型：`ALTER TABLE 表名 MODIFY 列名 新的列数据类型;`

#### DML（表记录的增删改）

- 插入表记录：
 - `INSERT INTO 表名(列名1, 列名2, ...) VALUES(列数据1, 列数据2, ...);` 可以给出所有列，也可以只给出部分列，未给出的列数据值为NULL，数据值的顺序与给出列名的顺序相对应
 - `INSERT INTO 表名 VALUES(列数据1, 列数据2, ...);` 没有给出插入的列名时，默认插入值的顺序按照表中列的顺序
- 修改表记录：`UPDATE 表名 SET 列名=列值 [WHERE 条件];`
 - 条件运算符：`=`、`!=`、`<>`(相当于!=)、`<`、`>`、`<=`、`>=`、`BETWEEN...AND...`、`IN(..., ...)` **`IS NULL`(注意，不是=NULL)**、`IS NOT NULL`、`NOT`、`AND`、`OR`
- 删除表记录:`DELETE FROM 表名 [WHERE 条件];`

#### DCL（操作用户）

- 创建用户：
 - `CREATE USER 用户名@IP地址 IDENTIFIED BY '密码';` 用户只能在指定IP地址上登录
 - `CREATE USER 用户名@'%' IDENTIFIED BY '密码';` 用户可以在任意IP地址上登录
- 用户授权：`GRANT 权限1,权限2,... ON 数据库名.* TO 用户名@IP地址;`
 - 权限包括`SELECT`、`GRANT`、`CREATE`、`UPDATE`等，`ALL`则表示所有权限
- 撤销用户权限：`REVOKE 权限1,权限2,... ON 数据库名.* FROM 用户名@IP地址;`
- 查看用户权限：`SHOW GRANTS FOR 用户名@IP地址;`
- 删除用户：`DROP USER 用户名@IP地址;`

#### DQL（查询表记录）

###### 基础查询
- 查询所有列：`SELECT * FROM 表名;`
- 查询指定列：`SELECT 列1 [, 列2, ...] FROM 表名;`
- 去除相同项：`SELECT DISTINCT 列1 [, 列2, ...] FROM 表名;`
- 列运算
 - IFNULL()函数，如`SELECT *,sal+IFNULL(comm, 0) FROM emp;`，表示如果comm为NULL则用0代替，否则所有数和NULL运算结果都是NULL（若将不能进行运算的列数据类型进行运算，如字符串，会将其当作0）
 - CONCAT()函数，如`SELECT CONCAT('$', sal) FROM emp;`，表示连接字符串"$"和列sal的值
 - 给列起别名，如`SELECT name [as] 名字, job [as] 工作 FROM emp;`

###### 条件查询
- 条件查询：`SELECT ... FROM 表名 WHERE 条件;`

###### 模糊查询

- 模糊查询的关键字：`LIKE`
- 模糊查询的符号：`_`表示匹配一个字符，`%`表示匹配任意个字符（包括0个）
 - 例如`SELECT * FROM emp WHERE ename like '_刚';` 表示查询所有ename为两个字符，且第二个字符为“刚”的表记录
 - 例如`SELECT * FROM emp WHERE ename like %小%';` 表示查询所有ename包含“小”的表记录

##### 查询结果排序

- 排序关键字：`ORDER BY`
- 升序关键字：`ASC` （可省略）
- 降序关键字：`DESC`
 - 例如`SELECT * FROM emp ORDER BY sal;` 按sal升序排列
 - 例如`SELECT * FROM emp ORDER BY sal DESC;` 按sal降序排列
 - 例如`SELECT * FROM emp ORDER BY sal, comm, empno DESC;` 先按sal升序排列，相同的再按comm升序排列，还相同的再按empno降序排列

###### 聚合函数

- 聚合函数：
 - `count()` 非NULL记录数
 - `sum()` 求和
 - `max()` 最大值
 - `min()` 最小值
 - `avg()` 平均值
 - 例如：`SELECT COUNT(*) FROM emp;` 表示查询emp表中的表记录数
 - 例如：`SELECT COUNT(comm) FROM emp;` 表示查询emp表中comm不为NULL的表记录数

###### 分组查询

- 分组查询关键字：`GROUP BY`
- **分组查询只能查询组信息**，具体来说能查询的只有两种：
 - **分组列**，如`SELECT job FROM emp GROUP BY job;`
 - **聚合函数**，如`SELECT job, COUNT(*) FROM emp GROUP BY job;`
- 待条件的分组查询：
 - **条件是分组前的条件（先满足条件，再分组），用关键字`WHERE`**，例如查询每个部门中工资在1000以上的员工个数，`SELECT deptno, COUNT(*) FROM emp WHERE sal>=1000 GROUP BY deptno;`
 - **条件是分组后的条件（先分组，再满足条件），用关键字`HAVING`**，例如查询起码有4个员工的部门，`SELECT deptno, COUNT(*) FROM emp GROUP BY deotno HAVING COUNT(*)>=4;`
 - 例如查询起码有4个员工的工资在1000以上的部门，`SELECT deptno, COUNT(*) FROM emp WHERE sal>=1000 GROUP BY deptno HAVING COUNT(*)>=4;`

###### LIMIT方言（MySQL独有）

- `LIMIT`关键字用于限定查询结果的起始行和总行数
 - 例如`SELECT * FROM emp LIMIT 0, 5;` 表示从第1行开始，查询5行表记录（若后面不足5行，有几行就查几行）
 - `LIMIT`可用于分页查询


#### MySQL备份与恢复

- 备份是指将数据库的内容转换成sql脚本，例如在登录MySQL前`mysqldump -uroot -pxxx mydb>D:/temp.sql`，就将数据库mydb中的内容备份到了D盘下的temp.sql中
- 恢复是指从sql脚本中恢复数据库的内容（注意不是恢复数据库，**要先创建数据库**），例如在登录MySQL前`mysql -uroot -pxxx mydb<D:/temp.sql`，就将temp.sql中的内容恢复到了数据库mydb中
 - 另一种恢复的方法：登录MySQL后，创建数据库，然后`source D:/temp.sql;`

#### 约束

###### 主键约束

- 主键约束的关键字为`PRIMARY KEY`
- **主键**是**唯一标识**，特点是**非空、唯一、被（外键）引用**
- 指定主键的方法：
 - 创建表时在主键列后面加上关键字`PRIMARY KEY`，如`CREATE TABLE emp(empno INT PRIMARY KEY, ename VARCHAR(50));`
 - 创建表时在最后加上`PRIMARY KEY(xxx)`来指定，如`CREATE TABLE emp(empno INT, ename VARCHAR(50), PRIMARY KEY(empno));`
 - 表已经存在，则`ALTER TABLE emp ADD PRIMARY KEY(empno);`
- 删除主键的方法：`ALTER TABLE emp DROP PEIMARY KEY;`
- 主键自增长（不适用于多服务器集群的情况）：可以用一个自增的整型变量来作为代理主键，如`CREATE TABLE emp(id INT PRIMARY KEY AUTO_INCREMENT, empno INT, ename VARCHAR(50));`，如果第一个插入的记录id为1，则插入第二个记录时不指定id，自动设置为2

###### 非空约束

- 非空约束的关键字为`NOT NULL`，例如`CREATE TABLE emp(empno INT PRIMARY KEY, ename VARCHAR(50) NOT NULL);`，对ename设置了非空约束

###### 唯一约束

- 唯一约束的关键字为`UNIQUE`，例如`CREATE TABLE emp(empno INT PRIMARY KEY, ename VARCHAR(50) NOT NULL UNIQUE);`，对ename设置了非空约束和唯一约束，但ename不是主键，主键还要能被外键引用

###### 外键约束

- 外键的特点：
 - 外键必须是另一张表（也可以是自身表）的主键（**外键引用主键**）
 - **外键可以重复**
 - **外键可以为空**
 - 一张表中**可以有多个外键**
- 外键约束的方法：
 - 创建表时指定外键，例如emp表中的外键deptno引用dept表中的主键no，则

```
CREATE TABLE emp(
    eno INT PRIMARY KEY,
	ename VARCHAR(50) NOT NULL,
	deptno INT,
	CONSTRAINT fk_emp_dept FOREIGN KEY(deptno) REFERENCES dept(no)
);
（其中fk_emp_dept是外键名称，可以自己取）
```
 - 表已存在，则`ALTER TABLE emp ADD CONSTRAINT fk_emp_dept FOREIGN KEY(deptno) REFERENCES dept(no);`


###### 关系模型

- 关系模型和对象模型的区别：
 - **对象模型可以双向引用，且引用的是整个对象，而不是主键**，例如Java中Employee类可以有一个Department属性，Department类中也可以有一个List<Employee>属性
 - **关系模型只能单向引用（多方引用一方），且引用的是主键，而不是整行记录**，例如在emp表中可以有外键deptno（即引用dept表的主键），但dept表中不可能有empno


- **一对一关系**，在数据库中的表现：**从表的主键即外键**，例如：

```
CREATE TABLE husband(
	hid INT PRIMARY KEY AUTO_INCREMENT,
	hname VARCHAR(20) NOT NULL
);

CREATE TABLE wife(
	wid INT PRIMARY KEY,
	wname VARCHAR(20) NOT NULL AUTO_INCREMENT,
	CONSTRAINT fk_husband_wife FOREIGN KEY(wid) REFERENCES husband(hid)
);
```
则在插入wife数据时，其wid唯一且必须和某个hid相同，从而husband和wife形成一对一关系

- **多对多关系**，在数据库中的表现：**两张表本身没有关系，新增一张关系表（关系表中有两个外键分别引用两张表的主键）**，例如：

```
CREATE TABLE student(
	sid INT PRIMARY KEY AUTO_INCREMENT,
	sname VARCHAR(20)
);

CREATE TABLE teacher(
	tid INT PRIMARY KEY AUTO_INCREMENT,
	tname VARCHAR(20)
);

CREATE TABLE student_teacher(
	sid INT,
	tid INT,
	CONSTRAINT fk_student FOREGIN KEY(sid) REFERENCES student(sid),
	CONSTRAINT fk_teacher FOREGIN KEY(tid) REFERENCES teacher(tid)
);
```
student表和teacher表本身没有关系，但是通过关系表student_teacher表来建立关系，关系表student_teacher表中的两个外键分别引用student表和teacher表的主键

#### 多表查询

- 多表查询可以分为：
 - 合并结果集
 - 连接查询
 - 子查询

###### 合并结果集

- 合并结果集要求两个查询结果集的结构相同（列数、列类型完全相同）
- 合并结果集的关键字为`UNION ALL`（不去除两个结果集的重复项）或`UNION`（去除两个结果集的重复项）
 - 例如
```
SELECT deptno FROM emp
UNION ALL
SELECT deptno FROM dept;
```
和
```
SELECT deptno FROM emp
UNION
SELECT deptno FROM dept;
```

###### 连接查询

- 连接查询分为：
 - 内连接
 - 外连接（左外连接、右外连接、全外连接）
 - 自然连接（一种简化方式）

####### 内连接

- 方言形式，`SELECT * FROM 表1 表1别名, 表2 表2别名 WHERE 表1别名.xx=表2别名.xx;`，例如：
```
SELECT ename, dname
FROM emp e, dept d
WHERE e.deptno = d.deptno;
```
- 标准形式：`SELECT * FROM 表1 表1别名 INNER JOIN 表2 表2别名 ON 表1别名.xx=表2别名.xx;`，例如：
```
SELECT e.ename, d.dname
FROM emp e INNER JOIN dept d
ON e.deptno = d.deptno;
```

####### 自然连接

- 自然连接是内连接的一种特殊的简化形式，当自然连接的条件是两张表中名称相同的列时可以使用
- 形式：`SELECT * FROM 表1 NATURAL JOIN 表2;`，例如表emp和表dept中都有相同的deptno列，即表emp的外键deptno是表dept的主键：
```
SELECT e.ename, dname
FROM emp e NATURAL JOIN dept d;
```

####### 外连接

- 外连接有主表和次表（如左外连接时左表是主表），主表中的所有表记录无论是否满足条件都会显示，不满足条件的表记录的次表用NULL补，例如：
```
SELECT ename, dname
FROM emp LEFT OUTER JOIN dept
ON emp.deptno = dept.deptno;
```
则表emp中dept值为NULL的表数据也会显示在结果中，该表数据的dname等数据用NULL补
- MySQL不支持全外连接，但可以用左外连接+合并结果集+右外连接进行模拟

###### 子查询

- 子查询即`SELECT`的嵌套，子查询可以放在FROM后面，也可以放在WHERE后面，主要形式：
 - **单行单列**，一般放在`WHERE`后，例如查询工资大于平均工资的员工的信息：
```
SELECT *
FROM emp
WHERE sal > (SELECT MAX(sal) FROM emp);
```
注意`WHERE`后不能直接用聚合函数
 - **多行单列**，一般放在`WHERE`后，通常搭配`IN`、`ANY`、`ALL`使用，例如查询工资大于所有部门为30的员工的工资的员工的信息：
```
SELECT *
FROM emp
WHERE sal > ALL(SELECT sal FROM emp WHERE dept=30);
```
 - **单行多列**，一般放在`WHERE`后，此时类似于对象，通常搭配`IN`使用，例如查询工资和部门都与MARTIN相同的员工的信息：
```
SELECT *
FROM emp
WHERE (sal, deptno) IN (SELECT sal, deptno FROM emp WHERE ename='MARTIN');
```
 - **多行多列**，一般放在`FROM`后，例如：
```
SELECT temp.ename, temp.job
FROM (SELECT * FROM emp WHERE deptno='30') temp;
```

##### 多表查询练习

1. 查出至少有1个员工的部门，显示部门编号、部门名称、部门位置、部门人数
```
SELECT t2.deptno 部门编号, t2.dname 部门名称, t2.loc 部门位置, t1.cnt 部门人数
FROM (SELECT deptno, COUNT(*) cnt FROM emp GROUP BY deptno) t1 INNER JOIN dept t2
ON t1.deptno = t2.deptno;
```
2. 列出所有员工的姓名及其直接上级的姓名
```
SELECT e.ename 员工姓名, m.ename 直接上级姓名
FROM emp e LEFT OUTER JOIN emp m
ON e.mgr = m.empno;
```
3. 列出所有入职早于其直接上级的员工的员工编号、员工姓名及其部门名称
```
SELECT e.empno 员工编号, e.ename 员工姓名, d.dname 部门名称
FROM emp e, emp m, dept d
WHERE (e.mgr = m.empno) AND (e.hiredate < m.hiredate) AND (e.deptno = d.deptno);
```
4. 列出所有部门的员工信息，同时列出没有员工的部门
```
SELECT d.dname, e.*
FROM dept d LEFT OUTER JOIN emp e
ON d.deptno = e.deptno;
```
5. 列出最低工资大于1500的工作及其员工人数
```
SELECT job, COUNT(*)
FROM emp
GROUP BY job HAVING MIN(sal) > 1500;
```
6. 列出在销售部工作的员工的姓名，假定不知道销售部的部门编号
```
SELECT e.ename
FROM emp e, dept d
WHERE (e.deptno = d.deptno) AND (d.dname = 'SALES');
```
或
```
SELECT ename
FROM emp
WHERE deptno = (SELECT deptno FROM dept WHERE dname = 'SALES');
```
7. **列出工资高于公司平均工资的所有员工信息、所在部门名称、上级领导、工资等级**
```
SELECT e.*, d.dname, m.ename 上级领导, s.grade
FROM emp e LEFT OUTER JOIN dept d ON e.deptno = d.deptno
		   LEFT OUTER JOIN emp m ON e.mgr = e.ename
		   LEFT OUTER JOIN salgrade s ON e.sal BETWEEN s.losal AND s.hisal
WHERE e.sal > (SELECT AVG(sal) FROM emp);
```
注意，这题用到了四张表和一次子查询，需要多张表外连接，否则领导为NULL或部门为NULL的员工会被遗漏
多表外连接时，每连接一个表就写一次条件`ON`
8. 列出与SCOTT从事相同工作的所有员工及部门名称
```
SELECT e.*, d.dname
FROM emp e LEFT OUTER JOIN dept d
ON e.deptno = d.deptno
WHERE e.job = (SELECT job FROM emp WHERE ename = 'SCOTT');
```
9. 列出工资高于部门30中所有员工工资的员工的姓名、工资和部门名称
```
SELECT e.ename, e.sal, d.dname
FROM emp e LEFT OUTER JOIN dept d
ON e.deptno = d.deptno
WHERE e.sal > ALL(SELECT sal FROM emp WHERE deptno = 30);
```
10. 列出年份、利润、年度增长比（其中表tb_year中，year表示年份，zz表示年利润），其中第一年的年度增长比为0%
```
SELECT y1.*, CONCAT(IFNULL((y1.zz-y0.zz)/y0.zz * 100, 0), '%') 年度增长比
FROM tb_year y1 LEFT OUTER JOIN tb_year y0
ON y1.year = y0.year + 1
ORDER BY y1.year;
```

# JDBC连接MySQL

- JDBC，即Java Database Connectivity，就是用Java连接数据库，就是用Java来操作数据库
- 步骤：
 1. 导jar包：驱动！（mysql-connector-java的jar包）
 2. **加载驱动类（注册驱动）**：`Class.forName("com.mysql.jdbc.Driver")`
 3. 给出url、username、password，其中url背下来！
 4. 使用DriverManager类的`Connection getConnection(url, username, password)`方法得到Connection类
- JDBC四大参数：
 - **driverClassName：`com.mysql.jdbc.Driver`**
 - **url：`jdbc:mysql//localhost:3306/数据库名`**（还可加上参数`?useSSL=false`以避免SSLException，原因是MySQL在高版本需要指明是否进行SSL连接）
 - username
 - password

```
    @Test
    public void demo1() throws ClassNotFoundException, SQLException {
        /*
        JDBC四大参数：
            driverClassName: com.mysql.jdbc.Driver
            url: jdbc:mysql://localhost:3306/数据库名?useSSL=false
            username
            password
         */
        String driverClassName = "com.mysql.jdbc.Driver";
        String url = "jdbc:mysql://localhost:3306/mydb?useSSL=false";
        String username = "Cris";
        String password = "123";

        Class.forName(driverClassName);    // 加载驱动类（注册驱动）
        Connection conn = DriverManager.getConnection(url, username, password); // 通过url、username、password，得到Connection对象

        System.out.println(conn);
    }
```

##### JDBC原理

- Java提供了**数据库驱动接口的规范，即java.sql.Driver接口，各个数据库的驱动Driver类去实现该接口**，比如MySQL的驱动类com.mysql.jdbc.Driver就是java.sql.Driver接口的实现类
![](.\MyPic\jdbc.jpg)
- `Class.forName(driverClassName);`和`Connection conn = DriverManager.getConnection(url, username, password);`看似没有联系，为什么DriverManager可以识别驱动？
 - 这是因为**`Class.forName()`在加载类时，会执行类中的静态代码块，而驱动类的静态代码块中将该驱动类注册到了DriverManager中**，相当于`java.sql.DriverManager.registerDriver(new Driver());`
 - JDBC4.0后，在每个驱动jar包的META-INF/services中新增了java.sql.Driver文件，其中记录了driverClassName信息，会自动扫描，因此可以不写`Class.forName()`，但为了扩展性，仍建议写

### JDBC操作数据库

步骤：
1. 连接数据库，获得Connection对象
1. 通过Connection对象的`Statement createStatement()`方法**获取Statement对象**，Statement对象是**SQL语句的发送器**，作用是向数据库发送SQL语句
2. 通过Statement对象向数据库发送SQL语句**（注意：发送的SQL语句不用加`;`，且每次只能发送一条SQL语句）**
 - 调用Statement对象的**`int executeUpdate(String sql)`方法，向数据库发送DDL、DML语句**（增、删、改），**返回值是受影响的行数**
 - 调用Statement对象的**`ResultSet executeQuery(String querySql)`方法，向数据库发送DQL语句（查），返回值是ResultSet，其实是一张表，需要解析**，具体方法：ResultSet的`boolean next()`方法可以向下移动行光标并判断当前行是否存在，而`xxx getXxx(int col)`方法和`xxx getXxx(String colName)`方法可以通过列号或列名获取数据，其中xxx是数据类型如int、String等
 - （了解）调用Statement对象的`boolean execute(String sql)`方法既可以发送DDL、DML语句，又可以发送DQL语句，但是其返回值是boolean类型，要想得到SQL语句执行的结果还需要使用`int getUpdateCount()`或`ReseltSet getResultSet()`来获取
1. 关闭资源（如Connection、Statement、RestuleSet等都需要关闭）

```
    /**
     * JDBC实现增、删、改
     */
    @Test
    public void demo2() throws ClassNotFoundException, SQLException {
        // JDBC连接MySQL
        String driverClassName = "com.mysql.jdbc.Driver";
        String url = "jdbc:mysql://localhost:3306/mydb?useSSL=false";
        String username = "Cris";
        String password = "123";
        Class.forName(driverClassName);
        Connection conn = DriverManager.getConnection(url, username, password);

        // 对数据库进行增、删、改操作
        /* 步骤：
          1. 通过Connection对象获得Statement对象，它是SQL语句的发送器，功能是向数据库发送SQL语句，调用createStatement()方法
          2. 使用Statement对象发送SQL语句，调用int executeUpdate(String sql)方法，可以发送DDL、DML，返回值是影响的行数
          3. 关闭资源
         */
        Statement statement = conn.createStatement();

//        String sql = "INSERT INTO tab_stu VALUES('0005', 'zhangSan', 'female', 10, NULL)";  // sql语句，最后不用加";"
//        String sql = "UPDATE tab_stu SET gender=NULL WHERE no='0005'";
        String sql = "DELETE FROM tab_stu WHERE name='zhangSan'";

        int r = statement.executeUpdate(sql);
        System.out.println("r = " + r);

        statement.close();
        conn.close();
    }
```

```
    /**
     * JDBC实现查
     */
    @Test
    public void demo3() throws ClassNotFoundException, SQLException {
        // 连接数据库，获得Connection对象
        String driverClassName = "com.mysql.jdbc.Driver";
        String url = "jdbc:mysql://localhost:3306/exam?useSSL=false";
        String username = "Cris";
        String password = "123";
        Class.forName(driverClassName);
        Connection conn = DriverManager.getConnection(url, username, password);

        // 对数据库进行查询操作
        /* 步骤：
            1. 获取Statement对象，即SQL语句的发送器
            2. 调用Statement对象的ResultSet executeQuery(String querySql)方法发送SQL查询语句，返回值ResultSet其实是一张表
            3. 解析得到的ResultSet：
                    boolean next()方法可以将行光标往下移动，同时判断当前行是否存在
                    xxx getXxx(int col)方法和xxx getXxx(String colName)方法可以通过列号或列名获取数据，其中xxx是数据类型如int、String
            4. 关闭资源
        */
        Statement statement = conn.createStatement();
        String querySql = "SELECT empno, ename, sal FROM emp";
        ResultSet resultSet = statement.executeQuery(querySql);

        System.out.println("empno\tename\tsal");
        while (resultSet.next()) {
            int empno = resultSet.getInt(1); // 通过列号获取数据（注意：列号下标从1开始）
            String ename = resultSet.getString("ename");    // 通过列名获取数据
            double sal = resultSet.getDouble("sal");
            System.out.println(empno + "\t" + ename + "\t" + sal);
        }
        
        resultSet.close();
        statement.close();
        conn.close();
    }
```

JDBC代码规范化：
 - Connection、Statement、ResultSet等资源需要关闭，且是倒关，即先获取的资源后关闭
 - 关闭资源的操作需要放在finally语句块中
 - 关闭资源前需要先判断资源是否为null
 - 关闭资源时还要处理close()时可能抛出的异常

```
    /**
     * 代码规范化：关闭资源
     */
    @Test
    public void demo4() {
        String driverClassName = "com.mysql.jdbc.Driver";
        String url = "jdbc:mysql://localhost:3306/exam?useSSL=false";
        String username = "Cris";
        String password = "123";

        Connection conn = null;
        Statement state = null;
        ResultSet rs = null;

        try {
            Class.forName(driverClassName);
            conn = DriverManager.getConnection(url, username, password);
            state = conn.createStatement();
            rs = state.executeQuery("SELECT * FROM dept");

            System.out.println("deptno\tdname\tloc");
            while (rs.next()) {
                int deptno = rs.getInt("deptno");
                String dname = rs.getString("dname");
                String loc = rs.getString("loc");
                System.out.println(deptno + "\t" + dname + "\t" + loc);
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            // 关闭资源时倒序关闭，且要先判断资源是否为null，还要处理close()时可能抛出的异常
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (state != null) {
                try {
                    state.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

---

##### 结果集ResultSet

- 结果集ResultSet其实是一张二维表，行号和列号都是从1开始排序
- **结果集ResultSet的三大特性：是否可滚动、是否敏感、是否可更新**
 - **ResultSet的特性在**利用Connection的createStatement()方法**生成Statement对象时就已经确定**
 - 是否可滚动：不可滚动指的是读取结果集时行光标只能往下移动，而不能往上移动或偏移移动
 - 是否敏感：不敏感指的是在获取结果集之后读取结果集之前，数据库的更新不会影响结果集中的数据（几乎没有数据库实现结果集的敏感，因为太浪费资源）
 - 是否可更新：不可更新指的是改变结果集中的数据，不会改变数据库中的数据
 - `connection.createStatement()`得到的Statement对象执行查询操作得到的ResultSet**默认是不可滚动、不敏感、不可更新的，但MySQL默认是可以滚动的**
 - `connection.createStatement(int, int)`中的两个参数可以指定得到的Statement对象执行查询操作得到的ResultSet是否可滚动、是否可更新等
- 结果集ResultSet方法：
 - `xxx getXxx(int colIndex)`和`xxx getXxx(String colName)`，**根据列号或列名获取数据**，其中xxx是基本数据类型如int、String等，其中**最常用的是getString()和getObject()**
 - `void beforeFirst()` 将行光标移动到**第一行之前**，也是**行光标的默认位置**
 - `void afterLast()` 将行光标移动到最后一行之后
 - `boolean first()` 将行光标移动到第一行，若返回false表示移动失败（可能1行都没有）
 - `boolean last()` 将行光标移动到最后一行，若返回false表示移动失败（可能1行都没有）
 - `boolean isBeforeFirst()` 行光标是否在第一行之前
 - `boolean isAfterLast()` 行光标是否在最后一行之后
 - `boolean isFirst()` 行光标是否在第一行
 - `boolean isLast()` 行光标是否在最后一行
 - `boolean previous()` 将行光标向上移动一行，若返回false表示当前行无数据
 - **`boolean next()` 将行光标向下移动一行，若返回false表示当前行无数据**
 - `boolean relative(int row)` 将行光标相对位移row行，若row为负值，则向上移动，若返回false表示当前行无数据
 - `boolean absolute(int row)` 将行光标移动到指定行处，若返回false表示当前行无数据
 - `int getRow()` 返回当前行光标所在行
 - **`ResultSetMetaData getMetaData()` 获取**结果集**元数据（可以获取列信息）**，而ResultSetMetaData的`int getColumnCount()`方法可以获取列数，`String getColumnName(int column)`方法可以获取列名

```
    /**
     * 用ResultSet操作行光标、用元数据ResultSetMetaData获取列信息
     */
    @Test
    public void demo1() {
        String driverClassName = "com.mysql.jdbc.Driver";
        String url = "jdbc:mysql://localhost:3306/exam?useSSL=false";

        Connection conn = null;
        Statement state = null;
        ResultSet rs = null;

        try {
            Class.forName(driverClassName);
            conn = DriverManager.getConnection(url, "Cris", "123");
            state = conn.createStatement();
            rs = state.executeQuery("SELECT * FROM emp");

            // 获取总行数
            rs.last();  // 将行光标移动到最后一行
            int row = rs.getRow();   // 得到总行数
            rs.beforeFirst();   // 将行光标移动回到beforeFirst位置，便于后续遍历

            // 获取总列数
            int col = rs.getMetaData().getColumnCount();

            System.out.println("总行数：" + row + "\t总列数：" + col);

            for (int i = 1; i <= col; ++i) {
                System.out.print(rs.getMetaData().getColumnName(i));
                if (i != col)
                    System.out.print(",");
            }
            System.out.println();

            while (rs.next()) {
                for (int i = 1; i <= col; ++i) {
                    System.out.print(rs.getObject(i));
                    if (i != col)
                        System.out.print(",");
                }
                System.out.println();
            }
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (rs != null) {
                try {
                    rs.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (state != null) {
                try {
                    state.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
```

##### PreparedStatement

- PreparedStatement的好处：
 - 防止SQL攻击
 - 提高代码可读性和可维护性
 - 提高效率

SQL攻击是指SQL语句的参数部分恶意填入SQL语句片段来欺骗数据库，例如：
```
    /**
     * 使用Statement时，模拟SQL攻击
     * 表tab_login中有：
     * ---------------------
     *  |username | password
     *  |"Cris"   | "123"
     *  |"vvy"    | "456"
     *  --------------------
     * 只有当用户名和密码正确时才能登录，但SQL攻击可以使密码验证失效
     *
     */
    @Test
    public void demo1() throws Exception {
        Class.forName("com.mysql.jdbc.Driver");
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb?useSSL=false",
                "Cris", "123");
        Statement stat = conn.createStatement();

//        String loginUsername = "Cris";
//        String loginPassword = "123";

		// SQL攻击：在SQL语句参数部分恶意填入部分SQL语句片段
        String loginUsername = "a' OR 'a'='a";
        String loginPassword = "a' OR 'a'='a";

        String sql = "SELECT * FROM tab_login WHERE username='" + loginUsername + "' AND password='" + loginPassword +"'";
        ResultSet rs = stat.executeQuery(sql);

        boolean flag = rs.next();   // 若结果集中有内容则说明在数据库中查找到对应的用户名和密码，即登录成功

        System.out.println("sql = " + sql); // sql = SELECT * FROM tab_login WHERE username='a' OR 'a'='a' AND password='a' OR 'a'='a'
        System.out.println("是否登录成功：" + flag);   // 是否登录成功：true

        rs.close();
        stat.close();
        conn.close();
    }
```

- **获取PreparedStatement对象的方法：Connection对象的`PreparedStatement prepareStatement(String sql)`方法，参数是SQL模板，参数模板是指SQL语句中的参数用`?`代替**
- **PreparedStatement对象填入参数的方法：`void setXxx(int parameterIndex, xxx parameter)`方法**，其中xxx是数据类型，表示将第parameterIndex个参数设置为parameter
- **PreparedStatement对象发送SQL语句的方法：`int executeUpdate()`方法和`ResultSet executeQuery()`方法，注意它们都没有参数，因为PreparedStatement对象已经和SQL模板绑定了，且填入了参数**

```
    /**
     * 使用PreparedStatement之后，既可以防止SQL攻击，又提高了代码的可读性和可维护性
     */
    @Test
    public void demo2() throws Exception {
        Class.forName("com.mysql.jdbc.Driver");
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb?useSSL=false",
                "Cris", "123");

        String sql = "SELECT * FROM tab_login WHERE username=? && password=?";  // SQL模板，参数用?代替
        PreparedStatement pstate = conn.prepareStatement(sql);  // Connection获取PreparedStatement时绑定SQL模板

//        String loginUsername = "vvy";
//        String loginPassword = "456";
        // SQL攻击
        String loginUsername = "a' OR 'a=";
        String loginPassword = "a' OR 'a=";

		// 将参数填入PreparedStatement绑定的SQL模板中
        pstate.setString(1, loginUsername); // 将第1个参数设置为loginUsername
        pstate.setString(2, loginPassword); // 将第2个参数设置为loginPassword

        ResultSet rs = pstate.executeQuery();   // PreparedStatement执行SQL语句不用参数，因为已经绑定过SQL模板并填入过参数

        boolean flag = rs.next();
        System.out.println("是否登录成功：" + flag);   // SQL攻击时无法登录成功

        rs.close();
        pstate.close();
        conn.close();
    }
```

- **PreparedStatement的原理：**
 - 数据库服务器收到SQL语句时，先要校验SQL语法，再编译，然后才能执行
 - 而PreparedStatement在绑定SQL模板时，服务器就会先进行校验和编译动作（称为**预编译/预处理**），而在调用executeUpdate()或executeQuery()方法时只是把参数带过去执行
 - MySQL的预编译功能默认是关闭的！

###### 自定义JDBCUtils工具类 v1.0

- 每次都要获取Connection对象，可以将这个操作封装到一个工具类JDBCUtils中
- 连接的数据库、用户名等可能改变，可以将JDBC四大参数存储在一个配置文件`src>jdbc.properties`中，需要修改时到配置文件中修改
- 在工具类JDBCUtils中，可以进一步将读取配置文件、加载驱动类等只需要执行一次的操作放到静态代码块中

src>jdbc.properties中的内容：
```
driverClassName = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/mydb?useSSL=false
username = Cris
password = 123
```

JDBCUtils的内容：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/7 21:44
 * @Description: JDBC的工具类 v1.0
 * 用于获取Connection对象，JDBC四大参数放在src>jdbc.properties配置文件中，若要修改数据库、用户名等，到配置文件中修改
 */
public class JDBCUtils {
    private static Properties prop;

    /* 将只需要执行一次的代码放到静态代码块中，如加载配置文件、加载驱动类 */
    static {
        // 获取配置文件内容（src下的文件会被复制到classpath中，可以用类加载器ClassLoader获        InputStream in = null;
        try {
            in = JDBCUtils.class.getClassLoader().getResourceAsStream("jdbc.properties");
            prop = new Properties();
            prop.load(in);	// 加载配置文件
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            if (in != null) {
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }

        try {
            Class.forName(prop.getProperty("driverClassName")); // 加载驱动类
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
    }

    /**
     * 根据 src>jdbc.properties 配置文件中的JDBC四大参数，获取Connection对象
     * @return Connection对象
     */
    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(prop.getProperty("url"),
                    prop.getProperty("username"),
                    prop.getProperty("password"));  // 获取并返回Connection对象
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```
---

##### 面向接口编程

- 在做过的登录功能中，DAO层是一个具体的类UserDao，但实际我们**不希望过度依赖某一个类，而是希望将该类中的方法抽象到接口中，只要是该接口的实现类就可**，因此**将UserDao变成一个接口**，其中有addUser()方法和findUserByName()方法
- 在做过的登录功能中，Service层依赖DAO层，因此在UserService类中有如下代码：`private UserDao userDao = new UserDao();`，将UserDao变成接口后，变成`private UserDao userDao = new UserDaoImpl();`，其中**类UserDaoImpl是UserDao接口的实现类**，但**仍过度依赖于UserDaoImpl类！**因此**引入工厂类UserDaoFactory实现解耦**，即`private UserDao userDao = UserDaoFactory.getUserDaoImpl();`
- 在**工厂类UserDaoFactory**的`public static UserDao getUserDaoImpl()`方法中如果直接写`return new UserDaoImpl();`，仍然过度依赖UserDaoImpl类！因此**可以将类名写到配置文件中，从配置文件中读取类名，运用反射创建该类实例来返回，这样做了之后只要修改配置文件，而不需要修改源代码**（读取配置文件的操作只需要做一次，因此可以放在静态代码块中）

配置文件realDatabaseConfig.properties中的内容：
```
driverClassName = com.mysql.jdbc.Driver
url = jdbc:mysql://localhost:3306/mydb?useSSL=false
username = Cris
password = 123
```

配置文件userDaoConfig的内容：
```
program.dao.UserDao = program.dao.JDBCUserDaoImpl
```

UserDao接口的实现类JDBCUserDaoImpl：
```
/**
 * @BelongProject: Program
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/8 11:28
 * @Description: UserDao的实现类，连接数据库并查询
 */
public class JDBCUserDaoImpl implements UserDao {
    private static Connection conn; // 与数据库连接得到的Connection对象

    static {
        Properties prop = new Properties();
        InputStream in = null;
        try {
            in = JDBCUserDaoImpl.class.getClassLoader().getResourceAsStream("realDatabaseConfig.properties");
            prop.load(in);
            Class.forName(prop.getProperty("driverClassName"));
            conn = DriverManager.getConnection(prop.getProperty("url"), prop.getProperty("username"), prop.getProperty("password"));
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            if (in != null) {
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /**
     * 将用户User添加到数据库中
     * @param user 待添加的User对象
     */
    public void addUser(User user) {
        String sql = "INSERT INTO tab_login VALUES(?, ?)";
        PreparedStatement pstate = null;
        try {
            pstate = conn.prepareStatement(sql);

            pstate.setString(1, user.getUsername());
            pstate.setString(2, user.getPassword());

            pstate.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            if (pstate != null) {
                try {
                    pstate.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /**
     * 根据给定的用户名在数据库中查询用户，若未查找到则返回null
     * @param username 用户名
     * @return 查找到的用户，若未查找到则返回null
     */
    @Override
    public User findUserByName(String username) {
        String sql = "SELECT * FROM tab_login WHERE username = ?";
        ResultSet rs = null;
        PreparedStatement pstate = null;
        try {
            pstate = conn.prepareStatement(sql);
            pstate.setString(1, username);

            rs = pstate.executeQuery();
            if (rs.next()) {
                User user = new User();
                user.setUsername(rs.getString("username"));
                user.setPassword(rs.getString("password"));
                return user;
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (rs != null)
                    rs.close();
                if (pstate != null)
                    pstate.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        return null;
    }
}
```

UserDao的工厂类UserDaoFactory：
```
/**
 * @BelongProject: Program
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/8 10:44
 * @Description: UserDaoImpl的工厂类，可以通过它获取UserDaoImpl的实例
 */
public class UserDaoFactory {
    private static Properties prop;

    static {
        InputStream in = null;
        try {
            in = UserDaoFactory.class.getClassLoader().getResourceAsStream("userDaoConfig.properties");
            prop = new Properties();
            prop.load(in);
        } catch (IOException e) {
            throw new RuntimeException(e);
        } finally {
            if (in != null) {
                try {
                    in.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    /**
     * 根据配置文件 src>userDaoConfig.properties 中给出的UserDao接口实现类的类名，获取UserDaoImpl的实例
     * @return UserDao接口的相应实现类UserDaoImpl的实例
     */
    public static UserDao getUserDaoImpl() {
        String className = prop.getProperty("program.dao.UserDao");
        try {
            return  (UserDao) Class.forName(className).getConstructor().newInstance();
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

UserService依赖UserDao：
```
private UserDao userDaoImpl = UserDaoFactory.getUserDaoImpl();
```

---

##### java.util包下的日期对象和java.sql包下的日期对象之间的转换

- **除DAO层外，包括Domain层在内的其他层都不能出现java.sql包下的东西**，否则会造成污染！
- 数据库中的DATE、TIME、TIMESTAMP数据类型分别对应java.sql.Date（只包含日期）、java.sql.Time（只包含时间）、java.sql.Timestamp，但Domain层却不能使用这些类型，因此需要和java.util.Date（包含日期和时间）进行转换
 - 将数据库中的日期对象取出后赋值给Domain对象时需要将java.sql包下的日期对象转换成java.util.Date，而**java.util.Date是java.sql.Date、java.sql.Time、java.sql.Timestamp的父类**，因此**java.sql包下的日期对象转换成java.util.Date不需要转换**，如`java.util.Date utilDate = new java.sql.Date();`
 - 将Domain类中的**java.util.Date对象转换成java.sql包下的日期对象**时，可以**用毫秒值做中转**，如
```
java.util.Date utilDate = new java.util.Date();
long m = utilDate.getTime();
java.sql.Date sqlDate = new java.sql.Date(m);
```

---

#### 数据库存取大数据文件

- 数据库也可以存储MP3等文件，在MySQL中可以用MEDIUMBLOB等数据类型

将文件存到数据库中：
```
    /**
     * 将MP3文件存到数据库的tab_mp3表中
     * 表的结构为{id INT PRIMARY KEY AUTO_INCREMENT, name VARCHAR(50), date MEDIUMBLOB}
     */
    @Test
    public void putMp3() throws SQLException, IOException {
        // 获取Connection对象
        Connection conn = JDBCUtils.getConnection();

        // 获取SQL语句发送器PreparedStatement对象
        String sql = "INSERT INTO tab_mp3 VALUES(?, ?, ?)";
        PreparedStatement pstsm = conn.prepareStatement(sql);

        // 设置SQL模板参数
        pstsm.setInt(1, 1);
        pstsm.setString(2, "vv.m4a");
        InputStream in = new FileInputStream("D:\\IDEA\\IdeaProject\\MyProject2\\myTest\\myCookie.m4a");
        pstsm.setBlob(3, in);

        // 执行SQL语句
        pstsm.executeUpdate();

        in.close();
        pstsm.close();
        conn.close();
    }
```

从数据库中读取文件：
```
    /**
     * 从数据库的tab_mp3表中将MP3文件读取出来，并写到硬盘上
     */
    @Test
    public void getMp3() throws SQLException, IOException {
        // 获取Connection对象
        Connection conn = JDBCUtils.getConnection();

        // 获取SQL语句发送器PreparedStatement对象
        String sql = "SELECT * FROM tab_mp3";
        PreparedStatement pstmt = conn.prepareStatement(sql);

        // 执行SQL语句，得到结果集ResultSet
        ResultSet rs = pstmt.executeQuery();

        // 从ResultSet中获取MP3数据并写到硬盘上
        if (rs.next()) {
            String name = rs.getString("name");
            Blob blob = rs.getBlob("data");
            InputStream in = blob.getBinaryStream();
            OutputStream out = new FileOutputStream("D:\\" + name);

            // 利用commons-io.jar中的IOUtils工具类将输入流中的数据复制到输出流中
            IOUtils.copy(in, out);
            in.close();
            out.close();

            /* 自己实现输入流中的数据复制到输出流中
            BufferedInputStream bis = new BufferedInputStream(in);
            BufferedOutputStream bos = new BufferedOutputStream(out);

            int ch;
            while ((ch = bis.read()) != -1)
                bos.write(ch);

            bos.close();
            bis.close();
             */
        }
        rs.close();
        pstmt.close();
        conn.close();
    }
```

---

#### 批处理

- 批处理：SQL语句的发送器(Statement、PreparedStatement)同时执行多条SQL语句，可以提高效率，减轻网络负担
- 只考虑PreparedStatement
 - PreparedStatement对象内部有一个集合，可以不断向集合中添加参数
 - 每一组参数会和SQL模板组成一条SQL语句（**每添加完一组参数都要执行一次addBatch()方法**，相当于分隔）
 - **添加完所有参数后再执行executeBatch()方法**，则将所有SQL语句一起发送给数据库执行
 - **（注意：MySQL的批处理功能默认是关闭的，要设置参数rewriteBatchedStatements=true）**

```
    /**
     * PreparedStatement的批处理
     *  设置参数rewriteBatchedStatements=true，否则MySQL批处理功能默认是关闭的
     *  不断向PreparedStatement中添加参数，每添加一组参数执行一次addBatch()方法
     *  所有参数添加完成后，执行executeBatch()方法
     */
    @Test
    public void batchDemo() throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection conn = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/mydb?useSSL=false&rewriteBatchedStatements=true",
                "Cris", "123"); // 设置rewriteBatchedStatements=true！！
        String sql = "INSERT INTO tab_batch VALUES(?, ?, ?, ?)";
        PreparedStatement pstmt = conn.prepareStatement(sql);

        // 循环不断向PreparedStatement对象的集合中添加参数
        for (int i = 0; i < 10000; ++i) {
            pstmt.setInt(1, i + 1);
            pstmt.setString(2, "Cris" + (i+1));
            pstmt.setInt(3, new Random().nextInt(100));
            pstmt.setString(4, (new Random().nextInt(2) == 0 ? "Male" : "Female"));

            // 添加完一组参数后要执行addBatch()方法
            pstmt.addBatch();
        }

        long start = System.currentTimeMillis();

        // 执行executeBatch()方法，将装载的所有参数发送给数据库进行批处理
        pstmt.executeBatch();

        long end = System.currentTimeMillis();

        System.out.println("耗时" + (end - start) + "ms..."); // 设置 rewriteBatchedStatements=true，耗时87ms
    }
```

---

## 事务

- **事务是数据库操作的最小工作单元**，是一些操作的集合，这些操作要么都执行，要么都不执行

##### 事务的四大特性（ACID）

 - **原子性**（**A**tomicity）：事务中的所有操作都是不可分割的原子单位，事务中的所有操作**要么都执行，要么都不执行**
 - **一致性**（**C**onsistency）：事务执行前后，数据库状态与其业务规则保持一致。例如转账前后，两个账户的余额之和相等**（事务的四大特性中，一致性最重要，其他三个特性都是为了一致性）**
 - **隔离性**（**I**solation）：隔离性是指在并发操作中，不同事务应隔离开来，使**每个并发事务不会相互干扰**
 - **持久性**（**D**urability）：**一旦事务提交成功，事务中的所有操作必须都被持久化到数据库中，即使事务提交后，数据库马上崩溃**，在数据库重启后，**也必须保证能**通过某种机制（如日志）**恢复数据**

##### MySQL中开启和关闭事务

- 开启事务：`START TRANSACTION;`
- 结束事务：`COMMIT`提交事务，`ROLLBACK`回滚事务（数据回滚到开启事务时的状态）

##### JDBC中完成事务处理

- **JDBC中对事务的操作都是由Connection对象完成的**，涉及到的方法：
 - 事务开始：`setAutoCommit(boolean)`方法，参数设置为false，表示关闭自动提交，即事务开始
 - 提交事务：`commit()`方法
 - 回滚事务：`rollback()`方法
- **同一事务的所有操作必须使用同一个Connection对象！**

JDBC中操作事务的一般格式：
```
try {
	connection.setAutoCommit(false);	// 开启事务

	...	// 事务的具体操作

	connection.commit();	// 提交事务
} catch (Exception e) {
	connection.rollback();	// 若有异常发生，则回滚事务
}
```
---

转账示例：
 - 注意，此时在Service中出现了java.sql包下的Connection对象，是不规范的，后期会改善

TransferDao：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/9 21:24
 * @Description: 转账的DAO层
 */
public class TransferDao {
    /**
     * 将表 tab_account 中的余额 balance更新为 balance + money
     * 注意：由于事务中的所有操作都要使用同一个Connection对象，因此不能在DAO层中创建独有的Connection对象
     * @param conn Connection对象
     * @param name 更新账户人的名字
     * @param money 要增加的金额
     */
    public void updateBalance(Connection conn, String name, int money) {
        String sql = "UPDATE tab_account SET balance = balance + ? WHERE name = ?";
        PreparedStatement pstmt = null;
        try {
            pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, money);
            pstmt.setString(2, name);
            pstmt.executeUpdate();
        } catch (SQLException e) {
            throw new RuntimeException(e);
        } finally {
            if (pstmt != null) {
                try {
                    pstmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    @Test
    public void testUpdateBalance()  {
        Connection conn = JDBCUtils.getConnection();
        updateBalance(conn, "zhangSan", 100);
    }
}
```

TransferService：
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/9 21:40
 * @Description: 转账的Service层
 */
public class TransferService {
    private TransferDao dao = new TransferDao();

    /**
     * 转账业务
     * @param from  转出方姓名
     * @param to    转入方姓名
     * @param money 转账金额
     */
    public void transfer(String from, String to, int money) {
        Connection conn = JDBCUtils.getConnection();	// 注意，此时在Service中出现了java.sql包下的Connection对象，是不规范的，后期会改善
        try {
            // 开启事务
            conn.setAutoCommit(false);

            // 事务操作
            dao.updateBalance(conn, from, -100);
            dao.updateBalance(conn, to, 100);

            /* 若抛出一个异常，则事务会回滚
            if (true)
                throw new RuntimeException();
             */

            // 提交事务
            conn.commit();
        } catch (Exception e) {
            try {
                // 回滚事务
                conn.rollback();
            } catch (SQLException se) {
                throw new RuntimeException(se);
            }
        } finally {
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    @Test
    public void testTransfer() {
        transfer("zhangSan", "liSi", 100);
    }
}
```

---

##### 并发事务问题

- 数据库中的数据可能同时被多个事务访问，若没有采取必要的隔离措施，就会导致各种并发问题，主要包括**3类数据读问题和2类数据写问题**：
 - **脏读**（Dirty Read）：指**读取到另一事务还未提交的数据**，即A事务读取B事务尚未提交的数据，而B事务又回滚，那么A事务读取到的数据就是脏数据
 - **不可重复读**（Unrepeatable Read）：指**由于另一事务对记录做了更新（对应UPDATE操作），导致两次读取结果不一致**，即A事务第一次查询后，B事务对该记录进行了修改，A事务再查询的时候发现和第一次查询结果不一致
 - **幻读**（Phantom Read）：指**由于另一事务插入（对应INSERT操作）了一条记录，导致两次读取结果不一致**，即A事务第一次查询后，B事务新增了记录，A事务第二次查询发现记录变多了
 - **第一类丢失更新**：**A事务撤销（对应ROOLBACK操作）时，将B事务已经提交的更新数据覆盖**，如：A事务查询余额（假定余额为1000） -> B事务查询余额并减去100 -> B事务提交（余额900）-> A事务回滚（余额1000），则B事务的更新被覆盖了
 - **第二类丢失更新**：**A事务的更新（对应COMMIT操作）覆盖B事务已经提交的更新**，如：A事务查询余额（余额1000） -> B事务查询余额并减去100 -> B事务提交（余额900） -> A事务增加余额100（余额在A之前查询到的1000上增加，变成1100） -> A事务提交，则B事务已经提交的更新数据被A事务的更新覆盖了


- **事务的四种隔离级别：**
 - **Read Uncommitted**（读未提交数据）：不做任何处理，性能最好，但可能出现任何并发事务问题
 - **Read Committed**（读已提交数据）：**可以防止脏读**，但无法处理不可重复读和幻读（Oracle默认隔离级别）
 - **Repetable Read**（可重复读）：**可以防止脏读和不可重复读**，但无法处理幻读，性能不如Read Committed（MySQL默认隔离级别）
 - **Serializable**（串行化）：不会出现任何并发事务问题，因为只能串行访问而不能并发访问，性能最差

---

## 数据库连接池

- Connection的创建和关闭很浪费资源，**数据库连接池的作用就是重用Connection对象**。相当于外包：需要Connection对象时，向连接池要；用完后，归还给连接池
- 数据库连接池需要的参数：
 - **池参数**，如初始连接数、最大空闲连接数、最大连接数、最长等待时间等，池参数都有默认值，一般不用配置
 - **JDBC四大参数**，需要配置
- **所有数据库连接池都实现了javax.sql.DataSource接口，都有`Connection getConnection()`方法用于获取Connection对象**
- **连接池使用装饰模式对Connection对象进行了增强，将其close()方法的功能变成了归还连接，而非关闭连接**

##### DBCP连接池

- DBCP连接池底层使用的是装饰模式对Connection进行增强
- 使用DBCP连接池需要导入commons-dbcp、commons-pool、commons-logging的jar包，同时还需要导入JDBC驱动类的jar包如mysql-connector-java的jar包
- DBCP连接池中的连接池对象是BasicDataSource

```
    /**
     * 使用DBCP连接池的步骤：
     *      1. 导入commons-dbcp、commons-pool、commons-logging的jar包（还要导入JDBC驱动类jar包）
     *      2. 获取连接池对象BasicDataSource
     *      3. 设置JDBC四大参数（还可以设置池参数）
     *      4. 获取连接对象Connection
     *      最后用close()方法将连接归还给连接池（连接池用装饰模式对Connection进行了增强，将close()方法变为归还连接而非关闭连接）
     */
    @Test
    public void dbcpDemo() throws SQLException {
        // 获取连接池对象
        BasicDataSource basicDataSource = new BasicDataSource();

        // 设置JDBC四大参数
        basicDataSource.setDriverClassName("com.mysql.jdbc.Driver");
        basicDataSource.setUrl("jdbc:mysql://localhost:3306/mydb?useSSL=false");
        basicDataSource.setUsername("Cris");
        basicDataSource.setPassword("123");

        // 获取Connection对象
        Connection conn = basicDataSource.getConnection();

        System.out.println(conn);

        // 将连接归还给连接池
        conn.close();
    }
```

##### C3P0连接池

- C3P0连接池底层使用的是动态代理对Connection进行增强，性能比DBCP连接池更好
- 使用C3P0连接池需要导入c3p0和mchange-commons-java的jar包，还需要JDBC驱动类jar包如mysql-connector-java的jar包
- C3P0连接池中的连接池对象是ComboPooledDataSource

```
    /**
     * 使用C3P0连接池的步骤：
     *      1. 导入c3p0和mchange-commons-java的jar包（还需要JDBC驱动类的jar包）
     *      2. 获取连接池对象ComboPooledDataSource
     *      3. 设置JDBC四大参数和池参数
     *      4. 获取连接，即Connection对象
     *      最后用close()方法将连接归还给连接池
     */
    @Test
    public void c3p0Demo1() throws SQLException, PropertyVetoException {
        // 获取连接池参数
        ComboPooledDataSource comboPooledDataSource = new ComboPooledDataSource();

        // 设置JDBC四大参数
        comboPooledDataSource.setDriverClass("com.mysql.jdbc.Driver");
        comboPooledDataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydb?useSSL=false");
        comboPooledDataSource.setUser("Cris");
        comboPooledDataSource.setPassword("123");

        // 获取Connection对象
        Connection conn = comboPooledDataSource.getConnection();

        System.out.println(conn);

        // 将连接归还给连接池
        conn.close();
    }
```

- c3p0连接池除了可以使用代码对JDBC四大参数和池参数进行配置之外，还可以使用配置文件进行配置
 - 要求配置文件的名称必须为c3p0-config.xml
 - 要求配置文件的位置必须在src下

src>c3p0-config.xml中的内容：
```
<?xml version="1.0" encoding="utf-8"?>
<c3p0-config>
    <!-- 默认配置 -->
    <default-config>
        <!-- JDBC四大参数配置 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/mydb?useSSL=false</property>
        <property name="user">Cris</property>
        <property name="password">123</property>
        <!-- 池参数配置 -->
        <property name="acquireIncrement">5</property>
        <property name="initialPoolSize">10</property>
    </default-config>

    <!-- 命名配置 -->
    <named-config name="examDB">
        <!-- JDBC四大参数配置 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/exam?useSSL=false</property>
        <property name="user">Cris</property>
        <property name="password">123</property>
    </named-config>
</c3p0-config>
```

```
    /**
     * C3P0连接池使用配置文件进行配置 之 默认配置（使用的是<default-config>下的参数）
     *  配置文件名必须为c3p0-config.xml，且位置必须在src下
     *  池参数和JDBC四大参数已经在配置文件中配置过了，无需代码配置
     */
    @Test
    public void c3p0Demo2() throws SQLException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        Connection conn = dataSource.getConnection();
        System.out.println(conn);
        conn.close();
    }
```

```
    /**
     * C3P0连接池使用配置文件进行配置 之 命名配置
     *      创建DataSource时给出配置名称，如"examDB"，则按照配置文件中<named-config name="examDB">下的参数进行配置
     */
    @Test
    public void c3p0Demo3() throws SQLException {
        ComboPooledDataSource dataSource = new ComboPooledDataSource("examDB");
        Connection conn = dataSource.getConnection();
        System.out.println(conn);
        conn.close();
    }
```

####### 自定义工具类JDBCUtils v2.0

更新自定义的JDBCUtils工具类：（使用连接池，实现连接的重用）
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/11 11:45
 * @Description: 用C3P0连接池更新自定义的JDBCUtils工具类
 */
public class JDBCUtils2 {
    private static ComboPooledDataSource dataSource = new ComboPooledDataSource();

    /**
     * 通过C3P0连接池获取连接，需要配置文件
     * @return 连接，即Connection对象
     */
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }

    /**
     * 获取C3P0连接池对象ComboPooledDataSource，通过连接池对象可以代码配置参数
     * @return 连接池对象
     */
    public static DataSource getDataSource() {
        return dataSource;
    }
}

```

##### JNDI配置数据库连接池

- JNDI（Java Naming and Directory Interface），即Java命名和目录接口，是类似于目录或注册中心的东西，**将Java对象以某个名称的形式绑定到一个容器环境（Context）中，以后调用容器环境（Context）的查找（lookup）方法又可以查找出某个名称所绑定的Java对象**
- 要想得到Context树中的一个Java对象，**首先要得到其所在的Context对象**，只要得到了一个Context对象，就可以**调用它的查询（lookup）方法来获得其中绑定的Java对象**
 - 另外，调用某个**Context对象的lookup方法也可以获得Context树中的任意一个Context对象，这只需要在lookup方法中指定相应的Context路径即可**
 - **在JNDI中不存在“根Context”的概念**，执行JNDI操作可以从Context树中的任意一个Context开始。无论如何，程序必须获得一个作为操作入口的Context对象后才能执行各种JNDI命名操作，为此，JNDI API中提供了一个**InitialContext类来用作JNDI命名操作的入口Context对象**
- 配置JNDI
 - 可以在`Tomcat安装目录>conf>context.xml`中配置，会影响所有Web项目，不推荐
 - 可以在`Tomcat安装目录>conf>Catalina>localhost`中创建一个`项目名.xml`，在里面进行配置，只影响相对应名称的Web项目

在Tomcat安装目录>conf>Catalina>localhost>项目名.xml中配置内容：
```
<?xml version="1.0" encoding="UTF-8"?>

<Context>
	<!-- 
	name是指定资源的名称，自定义
	factory是固定的
	type是资源的类名
	剩下的是资源所需的参数
	-->
	<Resource name="jdbc/DataSource"
			  factory="org.apache.naming.factory.BeanFactory"
			  type="com.mchange.v2.c3p0.ComboPooledDataSource"
			  
			  driverClass="com.mysql.jdbc.Driver"
			  jdbcUrl="jdbc:mysql://localhost:3306/mydb?useSSL=false"
			  user="Cris"
			  password="123"
			  />
</Context>
```

通过配置JNDI来获取连接：（要开启数据库！！）
```
@WebServlet(name = "JNDIServlet", urlPatterns = "/JNDIServlet")
public class JNDIServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        try {
            Context initialContext = new InitialContext();   // 获取查询入口
            Context envContext =  (Context) initialContext.lookup("java:comp/env"); // 这一步也是固定的
            DataSource dataSource = (DataSource) envContext.lookup("jdbc/DataSource");  // 这一步查询的名称和配置文件中Resource的name是对应的
            Connection conn = dataSource.getConnection();
            System.out.println("conn = " + conn);
            conn.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

##### Druid连接池

- Druid连接池是阿里提供的，性能好，需要导入druid.jar
- 使用Druid连接池的步骤：
 1. 加载配置文件（配置文件是properties形式的，可以是任意名称，可以放在任意位置）
 2. 获取连接池对象：DruidDataSourceFactory工厂类的`DataSource createDataSource(Properties prop)`方法
 3. 获取连接：DataSource的getConnection()方法

```
    /**
     * 使用Druid连接池的步骤：（注意要打开数据库的服务器！）
     *          1. 加载配置文件中的配置信息
     *          2. 使用DruidDataSourceFactory工厂类的createDataSource()方法获取连接池对象
     *          3. 通过连接池对象获取连接对象
     */
    @Test
    public void druidDemo() throws Exception {
        // 加载配置文件
        Properties prop = new Properties();
        InputStream in = this.getClass().getClassLoader().getResourceAsStream("druid.properties");
        prop.load(in);

        // 获取连接池对象
        DataSource dataSource = DruidDataSourceFactory.createDataSource(prop);

        // 获取连接对象
        Connection conn = dataSource.getConnection();
        System.out.println("conn = " + conn);
    }
```

---

##### 对象增强的方式

- **继承：被增强的对象固定，增强的内容固定**
- **装饰模式：被增强的对象可以切换，增强的内容固定**
- **动态代理：被增强的对象可以切换，增强的内容可以切换**

装饰模式的原理：**“是你还有你，一切拜托你”**
例如连接池的Connection对象就是由普通的Connection对象增强而来的，增强的是close()方法
```
class Connection implements java.sql.Connection {	// 是你（被增强类和增强类是同一类型）
	// 还有你
	private Connection con = new Connection();	// 底层对象，即被增强对象，是可以切换的，构造时指定
	public Connection(Connection con) {
		this.con = con;
	}

	// 一切拜托你
	public PreparedStatement prepareStatement(String sql) {
		return con.prepareStatement(sql);
	}

	// 增强点
	public void close() {
		// 将连接归还给连接池，而不是关闭连接
	}
}
```

---

#### ThreadLocal&lt;T&gt;

- **ThreadLocal通常用在类成员上，多个线程访问它时，每个线程都有一份副本，互不干扰**，不会产生并发访问的问题
- **ThreadLocal内部维护的其实是一个Map，key是当前线程**，value是值
- ThreadLocal&lt;T&gt;的方法：
 - `void set(T value)` 设置值
 - `T get()` 获取值
 - `void remove()` 删除值

```
    /**
     * ThreadLocal内部维护一个Map<Thread, value>，不同线程之间设置的值互不干扰
     */
    @Test
    public void threadLocalDemo() {
        String name1 = "Cris";
        String name2 = "vvy";

        ThreadLocal<String> tl = new ThreadLocal<>();

        System.out.println(Thread.currentThread().getName() + " set..." + name1);   // main set...Cris
        tl.set(name1);

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + " set..." + name2);   // Thread-0 set...vvy
            tl.set(name2);
            System.out.println(Thread.currentThread().getName() + " get..." + tl.get());    // Thread-0 get...vvy
        }).start();

        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println(Thread.currentThread().getName() + " get..." + tl.get());    // main get...Cris
    }
```
即Thread-0线程在ThreadLocal中设置的值，在main线程中无法获取到，因为ThreadLocal内部维护的是一个`Map<Thread, value>`

---

#### DbUtils

- 问题提出：使用JDBC进行增删改查操作时，大部分代码是类似的，如获取Connection对象，得到PreparedStatement对象，设置SQL参数，执行SQL语句等，因此考虑代码复用
- 使用**commons-dbUtils**的jar包可以**简化JDBC操作**，其中重要两个重要的类是**QueryRunner类和ResultSetHandler&lt;T&gt;接口**

###### 模拟DbUtils，洞悉其原理

- DML操作（增删改）中，获得连接池对象后，有变化的是SQL语句和SQL参数
- DQL操作（查询）中，获得连接池对象后，有变化的是SQL语句、SQL参数和JavaBean类型

因此可以将SQL语句、SQL参数作为参数（由于参数类型和个数不确定，因此用Object类型的可变参数，即`Object... pramas`），而DQL操作中将ResultSet的数据装载到JavaBean对象中的操作可以用一个泛型接口的实现类来完成

RsHandler&lt;T&gt;接口：用于将ResultSet中的数据装载到JavaBean对象中
```
/**
 * RsHandler接口的实现类负责将查询到的ResultSet中的内容封装到JavaBean对象中
 */
public interface RsHandler<T> {
    T handle(ResultSet rs) throws SQLException;
}
```

QR类：实现JDBC的简化操作，创建实例时需要给定数据库连接池
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/14 15:13
 * @Description: 模拟DbUtils中的QueryRunner类
 */
public class QR {
    private DataSource dataSource;  // 数据库连接池，需要在创建本类对象时指定

    public QR() { }

    public QR(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    /**
     * DML操作，即增、删、改
     * @param sql   SQL语句
     * @param params    SQL语句中的参数数组，需要按顺序给出
     * @return  受影响的行数
     */
    public int update(String sql, Object... params) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        try {
            conn = dataSource.getConnection();
            pstmt = conn.prepareStatement(sql);
            fillParams(pstmt, params);  // 使用本类的fillParams()方法，将SQL参数装载到PreparedStatement中
            return pstmt.executeUpdate();   // 执行SQL语句
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (pstmt != null)
                    pstmt.close();
                if (conn != null)
                    conn.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

    /**
     * 将SQL参数装载到PreparedStatement中
     * @param pstmt     PreparedStatement对象
     * @param params    SQL参数数组，需要按顺序给出
     */
    private void fillParams(PreparedStatement pstmt, Object... params) throws SQLException {
        for (int i = 0; i < params.length; ++i)
            pstmt.setObject(i+1, params[i]);
    }

    /**
     * DQL操作，即查询
     * @param sql   SQL语句
     * @param handler   RsHandler的实现类，用于将ResultSet中的数据装载到JavaBean中
     * @param params    SQL语句的参数数组，需要按顺序给出
     * @return  查询到的对象，若未查询到则返回null
     */
    public <T> T query(String sql, RsHandler<T> handler, Object... params) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = dataSource.getConnection();
            pstmt = conn.prepareCall(sql);
            fillParams(pstmt, params);  // 使用本类的fillParams()方法，将SQL参数装载到PreparedStatement中
            rs = pstmt.executeQuery();  // 执行SQL语句，得到结果集
            return handler.handle(rs);  // RsHandler接口的实现类将结果集中数据封装到JavaBean中，实现类由调用者提供，提供时给定JavaBean类型
        } catch (Exception e) {
            throw new RuntimeException(e);
        } finally {
            try {
                if (rs != null)
                    rs.close();
                if (pstmt != null)
                    pstmt.close();
                if (conn != null)
                    conn.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

测试QR类：查询操作时，需要实现RsHandler接口，也给出了JavaBean类型
```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/14 15:35
 * @Description: 测试自己实现的QR类，模拟DbUtils中的QueryRunner类
 */
public class TestQR {
    @Test
    public void testUpdate() {
        QR qr = new QR(JDBCUtils2.getDataSource());	// JDBCUtils2是自定义工具类，使用c3p0连接池连接数据库，配置文件中给出池参数和JDBC四大参数

        // INSERT操作
        Stu stu = new Stu("0000", "0号特工", "female", 0);
        String sql = "INSERT INTO tab_stu VALUES(?, ?, ?, ?)";
        Object[] params = {stu.getNo(), stu.getName(), stu.getGender(), stu.getAge()};
        System.out.println("影响行数：" + qr.update(sql, params));


        // UPDATE操作
//        String sql = "UPDATE tab_stu SET age = ? WHERE no = ?";
//        Object[] params = {1, "0000"};
//        System.out.println("影响行数：" + qr.update(sql, params));

        // DELETE操作
//        String sql = "DELETE FROM tab_stu WHERE no = ?";
//        Object[] params = {"0000"};
//        System.out.println("影响行数：" + qr.update(sql, params));
    }

    @Test
    public void testQuery() {
        QR qr = new QR(JDBCUtils2.getDataSource());	// JDBCUtils2是自定义工具类，使用c3p0连接池连接数据库，配置文件中给出池参数和JDBC四大参数

        // 创建RsHandler接口的实现类，指定JavaBean的类型(Stu)，将ResultSet中的数据装载到JavaBean中并返回
        RsHandler<Stu> handler = rs -> {
            if (!rs.next())
                return null;
            return new Stu(rs.getString("no"), rs.getString("name"),
                    rs.getString("gender"), rs.getInt("age"));
        };

        // 通过name查询Stu
//        String sql = "SELECT * FROM tab_stu WHERE name = ?";
//        Object[] params = {"JDT"};
//        Stu stu = qr.query(sql, handler, params);
//        System.out.println(stu);

        // 通过no查询Stu
        String sql = "SELECT * FROM tab_stu WHERE no = ?";
        Object[] params = {"0001"};
        Stu stu = qr.query(sql, handler, params);
        System.out.println(stu);
    }
}
```

###### DbUtils操作

- commons-dbutils.jar中的QueryRunner类就对应上面自定义的QR类，ResultSetHandler&lt;T&gt;接口就对应上面自定义的RsHandler&lt;T&gt;接口
- QueryRunner的方法：
 - `int update(String sql, Object... params)`，执行增删改操作
 - `int update(Connection conn, String sql, Obejct... params)`，给定Connection对象，执行增删改操作（**支持事务**，如**多个事务需要用同一个Connection对象时**，需要用到）
 - `T query(String sql, ResultSetHandler<T> rsh, Object... params)`，执行查询操作，**原理是得到结果集ResultSet后会调用ResultSetHandler接口实现类的handler()方法，将数据装载到对象中**
 - `T query(Connection conn, String sql, ResultSetHandler<T> rsh, Object... params)`，给定Connection对象，执行查询操作（**支持事务**）
- **DbUtils本身提供了一些ResultSetHandler&lt;T&gt;接口的实现类**，如：
 - **BeanHandler&lt;T&gt;（单行），构造时给出一个Class类型的参数（Bean的类型），并要求数据库中的列名和JavaBean的属性名相对应**
 - **BeanListHandler&lt;T&gt;（多行），构造时也要给出一个Class类型的参数（Bean的类型）**，将得到的多行记录转换成多个Bean对象，并返回一个List
 - **MapHandler（单行），将一行结果转换成Map&lt;String, Object&gt;并返回**，key是列名，value是值
 - **MapListHandler（多行），将多行记录转换成List&lt;Map&lt;String, Object&gt;&gt;并返回**，Map中key是列名，value是值
 - **ScalarHandler&lt;T&gt;（单行单列时最合适），返回的是Object类型**，如`SELECT COUNT(*) FROM tab_stu`时使用**（注意：不同数据库或不同数据库版本的`COUNT(*)`返回值类型不同，如Integer、Long、BigInteger等，但都是Number的子类，因此可以先得到Number类型的数据，再转换成想要的类型）**

```
    /**
     * ResultSetHandler接口实现类 之 BeanHandler（单行，返回Bean）
     */
    @Test
    public void demo1() throws SQLException {
        QueryRunner qr = new QueryRunner(JDBCUtils2.getDataSource());

        // UPDATE操作
//        String sql = "UPDATE tab_stu SET age = ? WHERE name = ?";
//        Object[] params = {21, "JDT"};
//        System.out.println("受影响行数：" + qr.update(sql, params));

        // SELECT操作
        String sql = "SELECT * FROM tab_stu WHERE name = ?";
        Object[] params = {"JDT"};
        Stu stu = qr.query(sql, new BeanHandler<>(Stu.class), params);  // 使用ResultSetHandler接口的实现类BeanHandler
                                                                        // 给出Bean的类型，并要求数据库中的列名和Bean中的属性名相同
        System.out.println(stu);
    }
```

```
    /**
     * ResultSetHandler接口实现类 之 BeanListHandler（多行，返回List<Bean>）
     */
    @Test
    public void demo2() throws SQLException {
        QueryRunner qr = new QueryRunner(JDBCUtils2.getDataSource());
        String sql = "SELECT * FROM tab_stu";
        List<Stu> list = qr.query(sql, new BeanListHandler<>(Stu.class));   // 没有参数就不用给定
        System.out.println(list);
    }
```

```
    /**
     * ResultSetHandler接口实现类 之 MapHandler（单行，返回Map<String, Object>）
     */
    @Test
    public void demo3() throws SQLException {
        QueryRunner qr = new QueryRunner(JDBCUtils2.getDataSource());
        String sql = "SELECT * FROM tab_stu WHERE name = ?";
        Object[] params = {"vvy"};
        Map<String, Object> map = qr.query(sql, new MapHandler(), params);
        System.out.println(map);    // {no=0002, name=vvy, gender=female, age=3}
    }
```

```
    /**
     * ResultSetHandler接口实现类 之 MapListHandler（多行，返回List<Map<String, Object>>）
     */
    @Test
    public void demo4() throws SQLException {
        QueryRunner qr = new QueryRunner(JDBCUtils2.getDataSource());
        String sql = "SELECT * FROM tab_stu";
        List<Map<String, Object>> list = qr.query(sql, new MapListHandler());
        System.out.println(list);
    }
```

```
    /**
     * ResultSetHandler接口实现类 之 ScalarHandler<T>（单行单列时最合适，返回Object）
     * 注意：不同数据库或数据库版本的COUNT(*)返回值类型不同，可能是Integer、Long、BigInteger等，但都是Number的子类
     *       因此可以先得到Number类型的数据，再用Number的xxxValue()方法转换成想要的类型
     */
    @Test
    public void demo5() throws SQLException {
        QueryRunner qr = new QueryRunner(JDBCUtils2.getDataSource());
        String sql = "SELECT COUNT(*) FROM tab_stu";

        Number number = qr.query(sql, new ScalarHandler<Number>()); // 先得到Number类型的数据
        int count = number.intValue();  // 再用Number的xxxValue()方法转换成想要的数据类型

        System.out.println(count);
    }
```

```
    /**
     * update()方法，执行增删改操作，不需要ResultSetHandler
     */
    @Test
    public void demo6() throws SQLException {
        QueryRunner qr = new QueryRunner(JDBCUtils2.getDataSource());
        String sql = "UPDATE tab_stu SET age = ? WHERE name = ?";
        Object[] params = {1, "vvy"};
        int row = qr.update(sql, params);
        System.out.println("受影响行数：" + row);
    }
```

---

####### 自定义工具类JDBCUtils v3.0

- 在事务中，多次操作需要保证使用同一个Connection对象
 - 事务不应该放在dao层中，因为dao层应该只是对数据的增删改查操作，不应涉及到功能性代码，而事务是多个操作的集合，一定是功能性代码
 - 事务应放在service层中，但要保证事务的多个操作使用同一个Connection对象，需要给dao层的操作传递Connection参数，这样会导致service层出现java.sql包下的Connection对象，污染了service层
 - 因此，我们希望将service层中的Connection对象隐藏起来，包装在自定义JDBCUtils工具类的方法中，代码应该如下：

```
	try {
		JDBCUtils3.startTransaction();
		dao.daoMethod1(...);
		dao.daoMethod2(...);
		JDBCUtils3.commitTransaction();
	} catch (SQLException e) {
		JDBCUtils3.rollbackTransaction();
	}
```

- 在JDBCUtils3中，遇到的问题：
 - startTransaction()、commitTransaction()、roolbackTransaction()三个方法应该对同一个Connection对象进行操作
 - 在事务开启时，dao层的多个方法应使用和上述三个方法所用的相同的Connection对象
 - 在事务未开启时或事务提交/回滚后，dao层应从数据库连接池获取一个Connection对象
 - 在dao层中，执行完操作后，会关闭获取到的连接Connection，此时应判断是否是事务专用的连接，若是则不要关闭，因为事务在提交或回滚时会关闭连接，若在此时就关闭，事务的后续操作会获取到已关闭的连接
- 解决方案：
 - 在JDBCUtils3中设置一个**事务专用的Connection类型的成员con**，初始值为null，在startTransaction()方法中赋值，在commitTransaction()和rollbackTransaction()方法中，若con不为null，则**关闭con并将con置为null（否则不开启事务时也会获取到之前事务的con，这时该连接已关闭，是无效的Connection）**
 - 在JDBCUtils3的**getConnection()**方法中，**当con不为null时，说明事务已开启，返回con；当con为null时，说明事务未开启，从数据库中获取Connection并返回**
 - **在dao层中**，用JDBCUtils中新增的releaseConnection(Connection connection)方法**替代connection.close()**，在里面**判断要释放的连接connection是否是事务专用连接，若是则不关闭，若不是则关闭**

```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/16 21:59
 * @Description: 自定义工具类 JDBCUtils v3.0
 *               可以处理事务
 */
public class JDBCUtils3 {
    private static DataSource dataSource = new ComboPooledDataSource(); // 数据库连接池，参数配置在配置文件中

    private static Connection conn = null;  // 事务专用的Connection对象

    /**
     * 通过C3P0连接池获取连接，需要配置文件
     * @return 连接，即Connection对象
     */
    public static Connection getConnection() throws SQLException {
        if (conn != null)   // 若conn不为null，说明事务已开启，返回事务专用的Connection对象conn
            return conn;

        return dataSource.getConnection();  // 事务未开启，从连接池中获取一个Connection的对象返回
    }

    /**
     * 获取C3P0连接池对象ComboPooledDataSource，通过连接池对象可以代码配置参数
     * @return 连接池对象
     */
    public static DataSource getDataSource() {
        return dataSource;
    }

    /**
     * 开启事务
     */
    public static void startTransaction() throws SQLException {
        if (conn != null)   // conn不为null时，表示事务已开启
            throw new RuntimeException("事务已开启，请勿重复开启");

        conn = dataSource.getConnection();
        conn.setAutoCommit(false);
    }

    /**
     * 提交事务
     */
    public static void commitTransaction() throws SQLException {
        if (conn == null)   // conn为null时，表示事务未开启
            throw new RuntimeException("未开启事务，无法提交");

        conn.commit();
        conn.close();
        conn = null;    // 要将conn置为null，表示事务已结束
    }

    /**
     * 回滚事务
     */
    public static void rollbackTransaction() throws SQLException {
        if (conn == null)
            throw new RuntimeException("未开启事务，无法回滚");

        conn.rollback();
        conn.close();
        conn = null;    // 要将conn置为null，表示事务已结束
    }

    /**
     * 释放连接connection
     *  若connection是事务专用的连接，则不用关闭，因为事务提交/回滚时会关闭，若在此处关闭，则事务的后续操作会获取到已关闭的连接
     *  若connection不是事务专用的连接，则关闭
     */
    public static void releaseConnection(Connection connection) throws SQLException {
        if (conn != null && conn == connection) // connection是事务专用的连接，不关闭
            return;
        
        connection.close(); // connection不是事务专用的连接，关闭
    }
}
```

测试JDBCUtils3
```
    @Test
    public void testJDBCUtils3() {
        try {
            JDBCUtils3.startTransaction();
            daoUpdate("zhangSan", 100);
            daoUpdate("liSi", -100);

            /* 测试回滚
            if (true)
                throw new RuntimeException();
             */

            JDBCUtils3.commitTransaction();
        } catch (Exception e) {
            try {
                JDBCUtils3.rollbackTransaction();
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
        }
    }

    private void daoUpdate(String name, int money) throws SQLException {
        Connection conn = JDBCUtils3.getConnection();   // 若事务开启，则返回的是事务专用的连接对象，否则从连接池中获取连接对象
        QueryRunner qr = new QueryRunner();
        String sql = "UPDATE tab_account SET balance = balance + ? WHERE name = ?";
        Object[] params = {money, name};
        qr.update(conn, sql, params);   // 若事务开启，可以保证事务所有操作使用的是同一个连接对象
		JDBCUtils3.releaseConnection(conn); // dao层中关闭连接用releaseConnection()方法代替，它会判断该连接是否是事务专用连接，若是则不关闭
    }
```

####### 自定义工具类JDBCUtils v4.0

- JDBCUtils3中遇到的问题：
 - 若有A线程和B线程同时开启事务，用JDBCUtils3会获取到同一个事务专用的连接对象，A事务提交或回滚后，该连接对象会被关闭，B事务的连接也被关闭
- 解决方法：
 - 在JDBCUtils中，将事务专用连接对象包装在ThreadLocal中，可以处理多线程并发访问问题

```
/**
 * @BelongProject: IdeaProject
 * @Author: Cris_JiangDT
 * @CreatTime: 2020/3/17 20:11
 * @Description: 自定义工具类 JDBCUtils v4.0
 *              可以处理多线程并发访问问题
 */
public class JDBCUtils4 {
    private static DataSource dataSource = new ComboPooledDataSource(); // 数据库连接池，参数配置在配置文件中

    private static ThreadLocal<Connection> tl = new ThreadLocal<>();    // 用ThreadLocal封装事务专用的Connection对象

    /**
     * 通过C3P0连接池获取连接，需要配置文件
     * @return 连接，即Connection对象
     */
    public static Connection getConnection() throws SQLException {
        Connection conn = tl.get(); // 从ThreadLocal中获取该线程的事务专用的连接对象conn

        if (conn != null)   // 若conn不为null，说明事务已开启，返回事务专用的Connection对象conn
            return conn;

        return dataSource.getConnection();  // 事务未开启，从连接池中获取一个Connection的对象返回
    }

    /**
     * 获取C3P0连接池对象ComboPooledDataSource，通过连接池对象可以代码配置参数
     * @return 连接池对象
     */
    public static DataSource getDataSource() {
        return dataSource;
    }

    /**
     * 开启事务
     */
    public static void startTransaction() throws SQLException {
        Connection conn = tl.get(); // 从ThreadLocal中获取该线程的事务专用的连接对象conn，若conn不为null，则说明该线程的事务已开启

        if (conn != null)   // conn不为null时，表示事务已开启
            throw new RuntimeException("事务已开启，请勿重复开启");

        conn = dataSource.getConnection();
        conn.setAutoCommit(false);

        tl.set(conn);   // 将该线程事务专用的连接对象封装到ThreadLocal中
    }

    /**
     * 提交事务
     */
    public static void commitTransaction() throws SQLException {
        Connection conn = tl.get(); // 从ThreadLocal中获取该线程的事务专用的连接对象conn，若conn为null，则说明该线程的事务未开启

        if (conn == null)   // conn为null时，表示事务未开启
            throw new RuntimeException("未开启事务，无法提交");

        conn.commit();
        conn.close();

        tl.remove();    // 从ThreadLocal中移除该线程专用的连接对象
    }

    /**
     * 回滚事务
     */
    public static void rollbackTransaction() throws SQLException {
        Connection conn = tl.get(); // 从ThreadLocal中获取该线程的事务专用的连接对象conn，若conn为null，则说明该线程的事务未开启

        if (conn == null)
            throw new RuntimeException("未开启事务，无法回滚");

        conn.rollback();
        conn.close();

        tl.remove();    // 从ThreadLocal中移除该线程专用的连接对象
    }

    /**
     * 释放连接connection
     *  若connection是事务专用的连接，则不用关闭，因为事务提交/回滚时会关闭，若在此处关闭，则事务的后续操作会获取到已关闭的连接
     *  若connection不是事务专用的连接，则关闭
     */
    public static void releaseConnection(Connection connection) throws SQLException {
        Connection conn = tl.get(); // 从ThreadLocal中获取该线程的事务专用的连接对象conn

        if (conn != null && conn == connection) // connection是事务专用的连接，不关闭
            return;

        connection.close(); // connection不是事务专用的连接，关闭
    }
}

```

### Spring JdbcTemplate

- **JdbcTemplate类是Spring框架提供的，和dbUtils.jar提供的QueryRunner类的作用相同**
- JdbcTemplate的方法：
 - `update(sql, params...)` 执行DML语句，增删改
 - `queryForMap(sql, params...)` **将结果封装到`Map<String, Object>`中**，key为列名，value为数据值**（注意这个方法的结果集长度只能是1）**
 - `queryForList(sql, params...)` **返回的是`List<Map<String, Object>>`**，将每一条结果分别封装到Map中，再添加到List中
 - `query(sql, RowMapper, params...)` 将结果封装到JavaBean对象中，RowMapper一般使用实现好的**BeanPropertyRowMapper，如：`template.query(sql, new BeanPropertyRowMapper<>(Person.class), ...)`**
 - `queryForObject(sql, clazz)` 将结果封装为clazz类对象，**一般用于聚合函数，如：`template.queryForObject("SELECT COUNT(*) FROM db", Long.class)`**

---

##### 多条件组合查询

- 问题：用姓名、性别、电话、邮箱等条件查询数据库中的Customer对象，**给出的条件个数不确定**（可能只给出性别，可能四个条件都给出，可能一个条件都不给出）
 - SQL模板如何给？
 - SQL模板中的`WHERE`如何处理？加不加？什么时候加？
 - 参数个数如何确定？
- 解决方案：
 - **SQL模板：用StringBuilder代替String**，每给出一个条件就添加一个条件
 - **`WHERE`问题**：用一个**废的条件`WHERE 1 = 1`占住`WHERE`的位置**，则其他条件就不用再考虑`WHERE`的问题了
 - **参数个数：用List&lt;Object&gt;代替Object[]**，每给出一个条件就添加一个条件

```
// Customer类对象criteria中装载着查询条件，若为null说明该条件未给出
public List<Customer> query(Customer criteria) {
	Connection conn = null;
	try {
		conn = JDBCUtils4.getConnection();
		QueryRunner qr = new QueryRunner();

		String cname = criteria.get(cname);
		String gender = criteria.get(gender);
		String cellphone = criteria.get(cellphone);
		String email = criteria.get(email);

		StringBuilder sqlSb = new StringBuilder("SELECT * FROM tab_customer WHERE 1 = 1");	// SQL模板用StringBuilder代替String，并且用废条件"1=1"占住"WHERE"的位置
		List<Object> paramsList = new LinkedList<>();	// 参数用List<Object>代替Object[]数组

		if (cname != null) {
			sqlSb.append(" AND cname LIKE ?");
			paramsList.add("%" + cname + "%");	// 模糊查询记得加通配符
		}

		if (gender != null) {
			sqlSb.append(" AND gender = ?");
			paramsList.add(gender);
		}

		if (cellphone != null) {
			sqlSb.append(" AND cellphone LIKE ?");
			paramsList.add("%" + cellphone + "%");	// 模糊查询记得加通配符
		}

		if (email != null) {
			sqlSb.append(" AND email LIKE ?");
			paramsList.add("%" + email + "%");	// 模糊查询记得加通配符
		}

		return qr.query(conn, sqlSb.toString(), 
					new BeanListHandler<>(Customer.class), 
					paramsList.toArray());	// 记得将StringBuilder和List<Object>转换回String和Object[]
	} catch (SQLException e) {
		throw new RuntimeException(e);
	} finally {
		if (conn != null)
			JDBCUtils4.releaseConnection(conn);
	}
}
```

---

##### 分页查询

- 分页查询的好处：只需要查询一部分数据，不用查询所有数据
- Servlet需要的参数：
 - 当前页码 pc（pageCode）：如果页面没有传递当前页码，则Servlet默认是第一页，否则按照页面传递的页码为准
 - 总页数 tp（totalPage）：总记录数/每页记录数
 - 总记录数 tr（totalRecord）：DAO层来获取，即`SELECT COUNT(*) FROM ...`
 - 每页记录数 ps（page size）：是业务数据（系统数据），由调用者给定
 - 当前页数据 beanList：DAO层，用MySQL的方言`SELECT * FROM ... LIMIT fromIndex, count`

- 多条件组合查询 + 分页查询
 - 问题：多条件组合查询后，点击底部的页面列表的超链接，条件会丢失
 - 解决方案：在Servlet中用`request.getQueryString()`将URL中的参数截取下来，剔除pc参数，再传回页面中，拼接新的pc参数，作为超链接的URL