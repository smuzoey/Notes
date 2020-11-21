# 基础SQL语法



## select 

```
SELECT 列名称 FROM 表名称
```

"*" 代表所有列

```
SELECT * FROM 表名称
```





## distinct

列出某列中不同（distinct）的值。

只能列出目标列不同的，其余信息不能打印。

如 需要列出不同的 City，则出了 City 其余的列不能输出。

```
SELECT DISTINCT 列名称 FROM 表名称
```





## where

如需有条件地从表中选取数据，可将 WHERE 子句添加到 SELECT 语句。

```
SELECT 列名称 FROM 表名称 WHERE 列 运算符 值
```



**运算符**

| 操作符        | 描述         |
| :------------ | :----------- |
| =             | 等于         |
| <>            | 不等于       |
| >             | 大于         |
| <             | 小于         |
| >=            | 大于等于     |
| <=            | 小于等于     |
| BETWEEN...AND | 在某个范围内 |
| LIKE          | 搜索某种模式 |



**示例**

```
SELECT * FROM person WHERE City = "London";
SELECT * FROM person WHERE Id > 1;
SELECT * FROM person WHERE Id  BETWEEN 3 And 5;  -- 包括3和5

```

**提示： 高级用法 中有 between 和 LIKE 的用法。**





## AND 和 OR 运算符

AND 和 OR 可在 WHERE 子语句中把两个或多个条件结合起来。

如果第一个条件和第二个条件都成立，则 AND 运算符显示一条记录。

如果第一个条件和第二个条件中只要有一个成立，则 OR 运算符显示一条记录。



使用 AND 来显示所有姓为 "Carter" 并且名为 "Thomas" 的人：

```
SELECT * FROM Persons WHERE FirstName='Thomas' AND LastName='Carter'
```



使用 OR 来显示所有姓为 "Carter" 或者名为 "Thomas" 的人：

```
SELECT * FROM Persons WHERE firstname='Thomas' OR lastname='Carter'
```



我们也可以把 AND 和 OR 结合起来（使用圆括号来组成复杂的表达式）:

```
SELECT * FROM Persons WHERE (FirstName='Thomas' OR FirstName='William')
AND LastName='Carter'
```





## order by

ORDER BY 语句用于根据指定的列对结果集进行排序。

ORDER BY 语句默认按照升序对记录进行排序。

降序，可以使用 DESC 关键字。



**示例：**

```
SELECT * FROM person ORDER BY City, Address;
SELECT * FROM person ORDER BY City;
SELECT * FROM person ORDER BY City DESC;
```



## insert

INSERT INTO 语句用于向表格中插入新的行。

**语法**

```
INSERT INTO 表名称 VALUES (值1, 值2,....)

指定所要插入数据的列
INSERT INTO table_name (列1, 列2,...) VALUES (值1, 值2,....)
```



**示例：**

```
INSERT INTO Persons VALUES ('Gates', 'Bill', 'Xuanwumen 10', 'Beijing')
INSERT INTO Persons (LastName, Address) VALUES ('Wilson', 'Champs-Elysees')
```





## update

Update 语句用于修改表中的数据。

**语法：**

```
UPDATE 表名称 SET 列名称 = 新值 WHERE 列名称 = 某值;

例如: 修改 id = 5 的人的 FirstName
UPDATE person SET FirstName = "一" where Id = "5";  
```





## delete

DELETE 语句用于删除表中的行。

**语法**

```
DELETE FROM 表名称 WHERE 列名称 = 值;

例如：删除 id = 1 的一行
DELETE FROM person WHERE Id = 1;
```





# 高级用法



## like

LIKE 操作符用于在 WHERE 子句中搜索列中的指定模式。

**SQL LIKE 操作符语法**

```
SELECT column_name(s)
FROM table_name
WHERE column_name LIKE pattern
```

**NOT LIKE : **可以和 **NOT** 连用，不满足 **LIKE** 条件输出。



"%" 可用于定义通配符（模式中缺少的字母）。

**%abc : **以 **abc** 结尾的

**abc% ：**以 **abc** 开头的

**%abc%**：中间部分含有 **abc** 的



**示例：**

我们希望从 "Persons" 表中选取居住在以 "g" 结尾的城市里的人：我们可以使用下面的 SELECT 语句：

```
SELECT * FROM Persons WHERE City LIKE '%g'
```



**MySQL 中 Like 是不区分大小写的，%g 以 g 或 G 结尾均会返回**

```
如果需要区分大小写则需要 列名 前，加上 binary
SELECT * FROM Persons WHERE binary City LIKE '%g'
```





## between

操作符 **BETWEEN ... AND** 会选取介于两个值之间的数据范围。这些值可以是数值、文本或者日期。

不同的数据库 **BETWEEN** 的结果可能不一样，有的包括边界，有的不包括边界值，或者只包括一边。

```
SELECT column_name(s)
FROM table_name
WHERE column_name
BETWEEN value1 AND value2
```

**BETWEEN A AND B** 其实就是 **A <= ans <= B** ;

**NOT BETWEEN ：** 可搭配 **NOT** 使用，就是除了 **BETWEEN** 里面的。



**原表: MySQL**

| Id   | LastName | FirstName | Address        | City     |
| ---- | -------- | --------- | -------------- | -------- |
| 1    | Adams    | John      | Oxford Street  | London   |
| 2    | Bush     | George    | Fifth Avenue   | New York |
| 4    | Carter   | Thomas    | Changan Street | Beijing  |
| 5    | 宇       | 张        | 夷陵区         | 湖北     |
| 6    | 朗普     | 特        | 白宫           | New York |





**示例1**

```
SELECT * FROM person WHERE City BETWEEN "New York" AND "New York";
```

| Id   | LastName | FirstName | Address      | City     |
| :--- | -------- | --------- | ------------ | -------- |
| 2    | Bush     | George    | Fifth Avenue | New York |
| 6    | 朗普     | 特        | 白宫         | New York |



**示例 2**

```
SELECT * FROM person WHERE City BETWEEN "New York" AND "London";
```

| Id   | LastName | FirstName | Address | City |
| ---- | -------- | --------- | ------- | ---- |
|      |          |           |         |      |



**示例 3**

```
SELECT * FROM person WHERE City BETWEEN "London" AND "New York";
```

| Id   | LastName | FirstName | Address       | City     |
| ---- | -------- | --------- | ------------- | -------- |
| 1    | Adams    | John      | Oxford Street | London   |
| 2    | Bush     | George    | Fifth Avenue  | New York |
| 6    | 朗普     | 特        | 白宫          | New York |





**示例 4**

```
SELECT * FROM person WHERE FirstName BETWEEN "John" AND "Thomas"; 
```

| Id   | LastName | FirstName | Address        | City    |
| ---- | -------- | --------- | -------------- | ------- |
| 1    | Adams    | John      | Oxford Street  | London  |
| 4    | Carter   | Thomas    | Changan Street | Beijing |





**参考资料**

- [w3school：SQL 基础教程](https://www.w3school.com.cn/sql/index.asp)





