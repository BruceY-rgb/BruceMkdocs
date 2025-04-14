# Chapter 3 Introduction to SQL
## 1 Data Definition
### 1.1 Domain Types in SQL
- `char(n)`.Fixed length character string, with user-specified length n.

定长字符串，C语言里字符串结尾有`\0`，但是数据库中没有，长度由定义而得

- `varchar(n)`.Variable length character string, with user-specified `maximum` length n.

不定长字符串(可变长字符串)。不同的数据类型比较可能有问题(比如定长和不定长的字符串)

- `int`.Integer (a finite subset of the integers that is machine-dependent).

- `smallint`Small integer (a machine-dependent subset of the integer domain type).

往往是int长度的一半

- `numeric(p,d)`. Fixed point number, with user-specified precision of p digits, with d digits to the right of decimal point.
p 表示有效数字位数, d 表示小数点后多少位。 e.g. number(3,1) allows 44.5 to be store exactly, but neither 444.5 or 0.32
- `real`, double precision. Floating point and double-precision floating point numbers, with machine-dependent precision.
- `float(n)`. Floating point number, with user-specified precision of at least n digits.

### 1.2 Built-in Data Types in SQL

- date: Dates, containing a (4 digit) year, month and date

e.g. date ‘2005-7-27’

- time: Time of day, in hours, minutes and seconds. e.g. time ‘09:00:30’ time ‘09:00:30.75’
- timestamp: date plus time of day e.g. timestamp ‘2005-7-27 09:00:30.75’
- interval: period of time e.g. interval ‘1’ day
    - Subtracting a date/time/timestamp value from another gives an interval value.
    - Interval values can be added to date/time/timestamp values
    - built-in date, time functions: current_date(), current_time(), year(x), month(x), day(x), hour(x), minute(x), second(x)

### 1.3 Create Table Consrtuct

An SQL relation is defined using the create table command:

```sql
create table r (A1 D1, A2 D2, ..., An Dn,           (integrity-constraint1),            
..,         
(integrity-constraintk))

```
- r is the name of the relation
- each $A_i$ is an attribute name in the schema of relation r
- $D_i$ is the data type of values in the domain of attribute $D_i$

```sql
create table instructor(
    ID char(5),
    name varchar(20) not null,
    dept_name varchar(20),
    salary numeric(8,2) 
)
```

- `not null`
- primary key $(A_1,...,A_n)$

不能为空：表内不能有相同的

- foreign key $(A_1,...,A_n)$ references r

隐含：引用对应表的主键，外键是空值就是没有连接到任何值

![alt text](image-47.png)

可以给一个缺省值，比如`default 0`

![alt text](image-48.png)

`sec_id` can not be dropped from primary key above, to ensure a student cannot be registered for two sections of the same course in the same semester

如果引用的表中有条目被删除，可能会破坏完整性约束条件。有下面的方法：
- `restrict`:如果有条目是被引用的，那么不允许被删除
- `cascade`:引用的条目被删了之后，引用者也一并被删除
- `set null`:引用者的指针设为null
- `set default`:引用者的指针设为默认值

如果引用的表中有更新，也有类似上面的四种方法

### 1.4 Drop and Alter Table Constructs
- drop:将表和内容同时删除
- delete：删除表中的所有内容，但是始终保留表结构
- alter table：可以动态修改表的定义
    - `alter table r add A D`
        - A是被添加到表中的属性名
        - D是A的domain
        - 所有关系中的元组的新属性都被置为空值
        - 还可以增加外键的约束条件，也可以删掉
        ```SQL
        alter table Employees add Email varchar(50)
        ```
    - `alter table r drop A`
        - A是r表中的一个属性
        - drop操作在很多数据库系统中是被禁用的
    
## 2 Basic Query Structure

typical SQL query structure:

```SQL
select A1, A2, ..., An
from R1, ..., Rm
where P
```
- SQL查询的结果是一个relation
- SQL查询的结果是多重集，有重复的记录是允许的
### 2.1 The select Clause


The select clause list the attributes desired in the result of a query.

- 为了强制消除掉重复元素，在select的后面加上关键词`distinct`

> eg `select distinct dept_name from instructor`可以加all表示不去重，加不加无所谓

- An asterisk in the select clause denotes “all attributes”
e.g. select * from instructor

- The select clause can contain arithmetic expressions involving the operation,$+, -, \times 
, and \div$, and operating on constants or attributes of tuples.
可以有加减乘除运算 e.g. select ID, name, salary/12 from instructor
> 泛化投影,即可以在投影属性中引入运算

### 2.2 The where clause

The where clause specifies conditions that the result must satisfy.
Corresponds to the selection predicate of the relational algebra.

- SQL includes a between comparison operator e.g. select name from instructor where salary between 90000 and 100000
- tuple comparison 元组相等等价于各个元素相等

### 2.3 The from clause

The from clause lists the relations involved in the query.

Corresponds to the Cartesian product operation of the relational algebra.

### 2.4 Natural Join
- `select name, course_id from instructor, teaches where instructor.ID = teaches.ID;`
`select name, course_id from instructor natural join teaches;`
上面两条语句是等价的。

- 注意：自然连接的问题是如果两个表有相同的属性名但是有不同的含义不能使用自然连接，否则会造成内容的缺省

> Example:course(course_id,title, dept_name,credits）, teaches(ID, course_id,sec_id,semester, year), instructor(ID, name, dept_name,salary） 这里的 department 含义各有不同，不能直接自然连接。
> 教师所在的学院不一定与课程所在的学院相同
> 可以人为规定连接的属性，对应于$\sigma_{\theta}$
> Find students who takes courses across his/her department.

```sql
select distinct student.id
    from (student natural join takes) 
           join course using (course_id)
    where student.dept_name = course.dept_name
  ```

### 2.5 The Rename Operation

The SQL allows renaming relations and attributes using the `as` clause.

`old_name as new_name`

**eg**

```SQL
select distinct T.name
from instructor as T, instructor as S
where T.salary>S.salary and S.dept_name='Comp.Sci'
```
- Keyword as is optional and may be omitted
> eg.`instructor as T`与`instructor T`是完全等价的

- 使用aggregate function和表的自身比较时往往需要使用重命名操作
### 2.6 String Operations

SQL includes a string-matching operator for comparisons on character strings. The operator `like` uses patterns that are described using two special characters.

- 注意单引号表示字符串

- percent(%):The % character matches any substring.
> eg. `select name from instructor where name like '%dar%'`找名字里含有`dar`的字符串
- underscore(_):The _ character matches any character.
> eg. `select name from instructor where name like '_ar%'`找名字里第二个字母是`ar`的字符串

- '---'matches any string of exactly three characters
- '---%'matches any string of at least three characters.

- Match the string 
    - 匹配字符串`100%`但是`%`符号被我们作为了通配符，这里我们需要用到转义符`\`.`\%`即将`%`作为正常字符匹配
    - `\`也可以是一个基本符号，我们需要在后面写出`escape`表示其在这里作为转义符。类似地我们还可以将转义字符定义为`#`

```SQL
like `100 \%'  escape  '\' 
like `100 \%'  
like `100  #%'  escape  `#' 
```

SQL supports a variety of string operations such as

- concatenation(using ||)
```SQL
select '客户名='|| customer.name
```
> 原本name这个属性对应的值显示为客户名=name

- converting from upper to lower case(and vie versa)

- finding string length,extracting substrings

### 2.7 Ordering the Display of Tuples

关系是无序的，但是我们可以规定显示出来的顺序

- 对于某一个属性，我们定义降序为`desc`，升序为`asc`
> eg.`order by name desc`可以排序的类型，如字符串、数字
- 可以对多个属性进行排序
> eg. `order by dept_name,name`先按dept_name排序，如果该属性相同再按照name排序

### 2.8 The `limit` Clause

The `limit` clause can be used to constrain the number of rowa returned by the select statement.

limit clause takes one or two numeric arguments,which must both be nonnegative integer constants:

> eg. `select name from instructor limit 2`限制最多返回两行

### 2.9 Set Operations
- `union`,`intersect`,`except`是严格的集合操作，会对结果去重

- `union all`,`intersect all`,`except all`保持多重集可以存在重复的记录


![alt text](image-49.png)

### 2.10 Null Values

null提供的是一个存在但是未知的值或不存在的值

- The result of any arithmetic expression involving null is null.
e.g. 5 + null returns null

- The predicate is null can be used to check for null values.
e.g. Find all instructors whose salary is null.
select name from instructor where salary is null

- Comparisons with null values return the special truth value: unknown.

![alt text](image-50.png)

- 如果where子句中结果为unknown被当做false处理。理解UNKNOWN被当作FALSE处理有助于编写更精确的查询，避免意外地排除或包含某些数据。

### 2.11 Aggregate Functions

![alt text](image-51.png)

注意在`select`里出现的属性，除了统计函数以外，一定要是分组属性里面出现过的

#### 2.11.1 Having Clause

对分组后的组进行筛选

```SQL
select dept_name, count (*) as cnt
from instructor
where  salary >=100000
group by dept_name
having  count (*) > 10
order by cnt;
```

having clause中的谓词在分组完成之后应用，而where子句中的谓词在组形成前应用

- having子句是对aggregate function的约束
- 如果group语句中存在多个属性，则需要将多个属性按照出现的顺序形成一个组合值，然后进行分组。只有当所有属性的值完全相同时才可以作为同一组
- 执行顺序为:from->where->group by->having->select->order by 
#### 2.11.2 Null Values and Aggregates

`select sum(salary) from instructor`

- 上述语句中计算sum时会忽略掉null值
- 如果没有non-null值那么结果为null，只有count会返回0
- 所有aggregate操作除了`count(*)`都会忽略元组中的null值

![alt text](image-52.png) 

第二个表示重名率小于千分之一

### 2.12 Nested Subqueries

A subquery is a select-from-where expression that is nested within another query.

#### 2.12.1 Set Membership

`in`,`not in`

![alt text](image-53.png)

除了单个元素外，元组也可以使用 in, not in

```SQL
SELECT *
FROM orders
WHERE (customer_id, product_id) IN (select customer_id,product_id from info where price > 100)
```

#### 2.12.2 Set Comparison
- `some` 某些成员
- `all` 所有成员

> eg 工资大于生物系中的某些老师的老师

```SQL
select name
from instructor
where salary > some (select salary
                                    from instructor
                                    where dept_name = 'Biology');
```
### 2.12.3 Scalar Subquery
**Scalar(标量) Subquery** is one which is used where a single value is expected.

```SQL
select name
from instructor
where  salary * 10 > 
    (select budget  from department 
    where department.dept_name = instructor.dept_name)
```
- 这里 dept_name 是这个表的主键，只返回一个元组，这种情况下是可以不用 some, all 的。

#### 2.12.4 Test for Empty Relations

The exists construct returns the value true if the argument subquery is nonempty.

- `exists r`<=> $r \neq \emptyset$
- `not exists r`<=> $r = \emptyset$

```SQL
select course_id
from section as S
where semester = 'Fall' and year= 2009 and                
    exists (select *                            
    from section as T                      
    where semester = 'Spring' and year= 2010 and S.course_id= T.course_id);
```

> Find all students who have taken all courses offered in the Biology department.
SQL 语句往往需要逆向考虑，即找到这样的学生，不存在他没选过的生物系的课。

```SQL
select distinct S.ID, S.name
from student as S
where not exists ( (select course_id
                        from course
                        where dept_name = ’Biology’)
                except
                    (select T.course_id
                        from takes as T
                        where S.ID = T.ID));
```

#### 2.12.5 Test for Absence of Duplicate Tuples

The unique construct tests whether a subquery has any duplicate tuples in its result.
验证一个集合是否是集合，而非多重集

- Evaluates to “true” on an empty set.可以将 unique 理解为 at most once.

![alt text](image-54.png)

- not unique一般题目中会描述为`at least two`

### 2.13 With Clause

The `with` clause提供了一个定义relation definition只对with子句发生的查询开放的临时表

- 只是一个临时表，随着查询的结束自动消除
> Find all departments with the maximum budget

- 一般可以在选择一个具有特定性质的值的时候使用


