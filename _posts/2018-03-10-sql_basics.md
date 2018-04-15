---
title:  "SQL Baiscs"
categories: [Database ℗]
tags: [SQL, Database]
---


### 1 主要数据库类型

- Relational database - [关系型数据库](https://zh.wikipedia.org/wiki/%E5%85%B3%E7%B3%BB%E6%95%B0%E6%8D%AE%E5%BA%93)。
  - SQLite
  - MySQL
  - PostgreSQL
  - Oracle
  - Microsoft SQL
  - ...
- [NoSQL 类型数据库](https://zh.wikipedia.org/wiki/NoSQL)。
  - MongoDB
  - CouchBase
  - Redis
  - ...

区别：
  - 关系型数据需要使用 SQL Structured Query Language 进行查询。
  - NoSQL 可以不使用 SQL 进行查询

  *[后者用不同的分类标准又分为很多种类型](https://en.wikipedia.org/wiki/NoSQL#Types_and_examples_of_NoSQL_databases)*

From wikipedia:
> NoSQL systems are also sometimes called "Not only SQL" to emphasize that they may support SQL-like query languages

---

**基本操作类型**

Four Main Data Operations
- Create
- Read
- Update
- Delete

---
### 2 Structured Query Language

> A database can be considered a structure in realization
of the database language.
                                        - Rybinski,H.

**基本结构 - table**

table == column * row

|column1|column2|column3|...|
|:-:|:-:|:-:|
|||||row1|
|||||row2|
|||||row3|
|||||...|

SQL的复杂变化都是基于table这个基本结构构建起来的。

含有信息的table:

![](https://ws2.sinaimg.cn/large/006tNc79gy1fpejsettb4j30na0a9my5.jpg)


**Types of Data in SQL - SQL中的数据类型**

- Text Type Examples 文本型
  - TEXT
  - VARCHAR
- Numeric Type Examples 数字型
  - INT
  - INTEGER
- Date Type Examples 日期型
  - DATETIME
  - DATE
  - TIMESTAMP

### 3 Basic CRUD

### 3.1 READ - Query类句法

**Basic Syntax**

keywords(can be lowercase):

- `;` 分号是一个statement/query的结束标志
- AS 可以给sub query的结果命名（类似存入变数名称）
- SELECT
- FROM
- `*` 代表 All
- WHERE 用来引出条件限制，通常会是一个条件式
  - operator 如 `=` `!=` `>` `<` `<=` `>=`
  - AND
  - OR
  - IN (通常与WHERE)配合使用
  - NOT IN (通常与WHERE)配合使用
  - BETWEEN ... AND

**Retrieving Specific Columns of Information**

- 选择单个column时， column名称是bare word不用引号包裹, 直接跟在 `SELECT` 后。
- 选择多个column时，各个column名称之间用逗号`,`隔开。

```sql
SELECT email FROM patrons;
SELECT email, phone FROM patrons;
SELECT email, phone, zip_code FROM patrons;
```

**Categorizing Your Output with 'AS'**

选择单个 column 时使用 `AS`，后面的new_name用引号包裹。

```sql
SELECT <column_name> AS <new_name> FROM <table_name>;
```

选择多个 column 时使用 `AS`；

```sql
SELECT <column_1> AS <new_name_1>, <column_2> AS <new_name_2> FROM <table_name>;
```

Tricks:

```sql
SELECT 1+1;
```

|1+1|
|:-:|
|2|

```sql
SELECT 1+1, 2+2;
```
|1+1|2+2|
|:-:|:-:|
|2|4|

```sql
SELECT 1+1 AS 'small', 2+2 AS 'big';
```
|small|big|
|:-:|:-:|
|2|4|

**Searching Tables with 'WHERE'**

基本用法
```sql
SELECT <columns> FROM <table> WHERE <condition>;
```

- 使用 conditon 时要注意 data type 的对应关系(数字类型不加引号)。
- condition 可以使用组合条件

exmaples:

从contact中找出first_name是 'Andrew' 的所有数据(返回结果包含所有column)
```sql
SELECT * FROM contacts WHERE first_name = "Andrew";
```

从people中找出所有 age column 大于 19 的数据(包含所有column)。
```sql
SELECT * FROM people WHERE age > 19;
```

从people中找出所有 age column 大于 19 的项目的 name 和 age。
```sql
SELECT name, age FROM people WHERE age > 19;
```

**条件的组合**

基本用法

```sql
SELECT <columns> FROM <table> WHERE <condition 1> AND <condition 2> ...;
SELECT <columns> FROM <table> WHERE <condition 1> OR <condition 2> ...;
```

example:

从people表中找出所有gender为'female', age小于22的条目的，name, phone, address
```sql
SELECT name, phone, address FROM people WHERE gender = 'female' AND age < 22 ...;
```

**用日期作为过滤条件**

- 注意日期用引号包裹
- `>` date 代表这个日期之后的时间段
- 日期格式 "YYYY-MM-DD"

```sql
SELECT * FROM loans WHERE loaned_on <  "2015-12-13";
```

**Searching in a Set of Values 用一个集合的值作为过滤条件**

- IN
- NOT IN

基本用法

```sql
SELECT <columns> FROM <table> WHERE <column> IN (<value 1>, <value 2>, ...);
```

找出id为 1,2,3,20 的数据
```sql
SELECT * FROM books WHERE id IN (1,2,3,20);
```
给出集合中的条件并不一定必须存在于数据库中，最后拿到的是交集。上例子中如果books中并没有一个book条目的id等于20，那么就只返回前面三个匹配到的项目。

**Searching Within a Range of Values 使用range作为过滤条件**

- BETWEEN ... AND ...

基本用法

```sql
SELECT <columns> FROM <table> WHERE <column> BETWEEN <lesser value> AND <greater value>;
```

找出books中id在 8 到 13 之间（包含端点值）的数据。
```sql
select * from books where id between 8 and 13;
```

找出一个时间区间内出版的书
```sql
SELECT name, author FROM books WHERE published_at BETWEEN "2015-01-01" AND "2015-01-07";
```

**使用 `LIKE` + `%` 进行模糊匹配**

基本用法

```sql
SELECT <columns> FROM <table> WHERE <column> LIKE <pattern>;
```

从books中找出tile以'a story about'开头的书de title
```sql
SELECT title FROM books WHERE title LIKE "A story about%";
```

LIKE算作condition的一种，所以可以和其他条件组合
```sql
SELECT title FROM books WHERE title LIKE "A story about%" AND published_at > '2017-09-18';
```

**NULL 作为查找条件**

- IS NULL
- IS NOT NULL

基本用法

```sql
SELECT <columns> FROM <table> WHERE <column> IS NULL;
```

从books中找出co_author合著者为NULL的条目

```sql
SELECT title FROM books WHERE co_author IS NULL;
```

IS NOT NULL：
```sql
SELECT name FROM people WHERE forte IS NOT NULL;
```

**跨table查找**

基本用法：

```sql
SELECT <columns> FROM <table1>, <table2> WHERE table1.column_x = table2.column_y;
```

这个操作类似接表的操作，把books和movies中给出column中有交集的rows接起来
```sql
SELECT * FROM books, movies WHERE books.genre = movies.type;
```

### 3.2 Create类句法

**Keywords**

- INSERT INTO
- VALUES



注入单独一行

```sql
INSERT INTO <table> VALUES (<value 1>, <value 2>, ...);
```

假设有books table, 有5个column

|id|title|author|genre|first_published|
|:-:|:-:|:-:|:-:|:-:|
|||||||

**不给出column名称，只给value，按顺序注入**

- 限制是，给出的 value 数量以及顺和 column 在表中排布要一致，显然这不是一个省力的方法。
- 直接给出一个具体的primary key的值可能引起冲突，由于id是自动增量的，所以id可以给出 `NULL`

**给出column名称和对应的值进行注入**

比如
```sql
INSERT INTO books (id, title, first_published, author) VALUES (NULL, "Blessing", "2018-01-10", "David")
```

- 顺序要对应但可以不和表中一样
- 允许值为 NULL 的column可以不写，会被写入NULL
- 要注意有些column限制了值不能为 NULL

**Inserting multiple rows**

基本用法：
VALUES后跟多个括号，一个括号一行，用逗号分隔
```sql
INSERT INTO <table> (<column 1>, <column 2>, ...) VALUES
  (<value 1>, <value 2>, ...),
  (<value 1>, <value 2>, ...),
  (<value 1>, <value 2>, ...);
```


### 3.3 Update 类句法

**修改整个一列column的值**

keywords

- UPDATE
- SET

基本句法
```sql
UPDATE <table> SET <column> = <value>;
```

如果要更新多个 column:
```sql
UPDATE <table> SET <column 1> = <value 1>, <column 2> = <value 2>;
```

比如
```sql
UPDATE books SET author = 'Dave', genre = 'tragedy';
```

会把 books 表里所有数据的 author 改为 'Dave'， genre 会全部变成 'tragedy'。


**修改指定row的数据**

keywords:
- UPDATE
- SET
- WHERE

基本句法：

思路是在后面加上 WHERE condition 匹配指定的rows

```sql
UPDATE <table> SET <column> = <value> WHERE <condition>;
```

当然也可以修改指定row的多个column， 条件式也可以加上逻辑组合

```sql
UPDATE <table> SET <column 1> = <value 1>, <column 2> = <value 2> WHERE <condition1> AND <condition2>;
```

### 3.4 Delete 类句法

**keywords**

- DELETE
- WHERE

**删除所有rows**

```sql
DELETE * FROM <table>;
```

**删除指定rows**

```sql
DELETE FROM <table> WHERE <condition>;
```

### 4 使用 transaction 处理可能出现的错误

数据库在进行数据的 CRUD 操作时，默认是autocommit。也就是写的statement会默认直接写到存储介质上。但实际情况是出错不可避免会出错，或者在进行多条数据注入时突然断电或死机等情况。

为了解决这种情况数据库应用了transaction机制，一次transaction中可以包含多个statements，其中任何一个地方出错都会导致整个transaction回撤，让数据库回到transaction之前的状态。

基本句法是用 `BEGIN;` 和 `COMMIT;` 包裹statements：
```sql
BEGIN;
INSERT INTO ...
UPDATE ...
.
.
.
COMMIT;
```

![](https://ws2.sinaimg.cn/large/006tNc79gy1fpf0wayc9zj30kw09f0tc.jpg)

#### 数据的回滚 - Rolling Back from Transactions

让数据库回到transaction之前的状态

```sql
BEGIN;
INSERT INTO ...
UPDATE ...
.
.
.
ROLLBACK;
```

### 5 Databases with Frameworks

实际开发中不常见直接在程序中使用 SQL 原生语句的情况，多数情况下都使用编程语言专有的针对数据库的library，将其转译为SQL，这类库叫 ORM - Object-Relation Mapping，Rails用到的 [ActiveRecord](http://api.rubyonrails.org/classes/ActiveRecord/Base.html) 就是其中一种。

[ORM 的维基页面](https://en.wikipedia.org/wiki/Object-relational_mapping)

> Object-relational mapping (ORM, O/RM, and O/R mapping tool) in computer science is a programming technique for converting data between incompatible type systems using **object-oriented programming languages**. This creates, in effect, a "virtual object database" that can be used from within the programming language.

### Datebase Normalization

### Datebase keys

### Table Relationships

### Joining Table Data with SQL

### Set Operations

### Subqueries
