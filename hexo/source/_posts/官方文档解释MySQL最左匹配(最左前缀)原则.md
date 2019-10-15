---
title: 官方文档解释MySQL最左匹配(最左前缀)原则
date: 2019-10-15 08:48:10
tags: 
- mysql
- translate
comments: true
categories: 
- 学习
- 数据库
---

## 什么是最左匹配原则  
关于最左匹配原则的解释, 网上找了很多, 但是我感觉都不是特别准确, 于是一怒之下
直接找了官网的文档, 一下子就清晰了. 下面贴下官网的解释, 然后我自己翻译了一下.  

### 来自官方文档的解释  
[原文链接](https://dev.mysql.com/doc/refman/8.0/en/multiple-column-indexes.html)  
> MySQL can create composite indexes (that is, indexes on multiple columns). 
 An index may consist of up to 16 columns. For certain data types,
 you can index a prefix of the column (see 
 [Section 8.3.5, “Column Indexes”](https://dev.mysql.com/doc/refman/8.0/en/column-indexes.html)).  

MySQL可以创建联合索引(即, 多列的索引). 一个索引可以包含最多16列. 对于
某些数据类型, 你可以索引列的前缀(这里说的是对于Blob和Text类型, 索引列的前几位就可以, 
如INDEX(blob_col(10)), 详见[索引文档](https://dev.mysql.com/doc/refman/8.0/en/column-indexes.html))  

> MySQL can use multiple-column indexes for queries that test all the columns in the index, 
or queries that test just the first column, the first two columns, the first three columns, and so on. 
If you specify the columns in the right order in the index definition, 
a single composite index can speed up several kinds of queries on the same table.  

MySQL的联合索引可以用于包含索引中所有列的查询条件的语句, 或者包含索引中的第一列的查询条件的语句, 
以及索引中前两列, 索引中的前三列, 以此类推. 如果你在索引定义中以正确的顺序指定列, 
那么联合索引就可以加速同一张表中的多个不同类型的查询.  

> A multiple-column index can be considered a sorted array, the rows of 
which contain values that are created by concatenating the values of the indexed columns.  

一个联合索引可以看作是一个有序队列, 里面有值的列是根据连接索引列的值创建的.(这句可能不准确)  

> Note  
As an alternative to a composite index, you can introduce a column that 
is “hashed” based on information from other columns. If this column is 
short, reasonably unique, and indexed, it might be faster than a “wide” 
index on many columns. In MySQL, it is very easy to use this extra column:  

提示  
作为联合索引的一个替代项, 你可以采用一个Hash值列, 这个列的Hash值来自其他的列. 
如果该列简短, 合理唯一, 且被索引, 那该列就可能比一个很"宽"的由多个列构成的索引
更快. MySQL里可以很容易的使用这种列:
```sql
SELECT * FROM tbl_name
  WHERE hash_col=MD5(CONCAT(val1,val2))
  AND col1=val1 AND col2=val2;
```

> Suppose that a table has the following specification:

假设有如下表定义:
```sql
CREATE TABLE test (
    id         INT NOT NULL,
    last_name  CHAR(30) NOT NULL,
    first_name CHAR(30) NOT NULL,
    PRIMARY KEY (id),
    INDEX name (last_name,first_name)
);
```

> The name index is an index over the last_name and first_name columns. 
The index can be used for lookups in queries that specify values in a known 
range for combinations of last_name and first_name values. It can also be used 
for queries that specify just a last_name value because that column is a 
leftmost prefix of the index (as described later in this section). 
Therefore, the name index is used for lookups in the following queries:  

索引name是一个包含了last_name和first_name列的索引. 该索引可以用于为last_name
和first_name值的组合指定一个已知范围内的查询. 同样也可以用于只指定了last_name列值的查询, 
因为这个列是索引的一个最左前缀(就如下一节所说). 因此, 索引name可以用于下列的查询语句:  
```sql
SELECT * FROM test WHERE last_name='Jones';

SELECT * FROM test
  WHERE last_name='Jones' AND first_name='John';

SELECT * FROM test
  WHERE last_name='Jones'
  AND (first_name='John' OR first_name='Jon');

SELECT * FROM test
  WHERE last_name='Jones'
  AND first_name >='M' AND first_name < 'N';
```

> However, the name index is not used for lookups in the following queries:  

然而, 索引name不能用于下列的查询:  
```sql
SELECT * FROM test WHERE first_name='John';

SELECT * FROM test
  WHERE last_name='Jones' OR first_name='John';
```

> Suppose that you issue the following SELECT statement:  

假设存在以下select语句:  
```sql
SELECT * FROM tbl_name
  WHERE col1=val1 AND col2=val2;
```

> If a multiple-column index exists on col1 and col2, the appropriate 
rows can be fetched directly. If separate single-column indexes exist 
on col1 and col2, the optimizer attempts to use the Index Merge optimization 
(see Section 8.2.1.3, “Index Merge Optimization”), or attempts to find 
the most restrictive index by deciding which index excludes more rows 
and using that index to fetch the rows.  

如果一个联合索引存在于col1和col2, 相应的列会被直接抓取. 如果是分为单独的索引
分别存在于col1和col2, 优化器会尝试利用索引联合优化(详见8.2.1.3, ["索引联合
优化"](https://dev.mysql.com/doc/refman/8.0/en/index-merge-optimization.html)),
或者尝试去寻找包含最多列, 最大限制的索引, 并利用该索引去抓取列.  

> **If the table has a multiple-column index, any leftmost prefix of the 
index can be used by the optimizer to look up rows. For example, 
if you have a three-column index on (col1, col2, col3), you have indexed 
search capabilities on (col1), (col1, col2), and (col1, col2, col3).**  

**如果表拥有一个联合索引, 任何一个索引的最左前缀都会被优化器用于查找列. 比如, 
如果你创建了一个三列的联合索引包含(col1, col2, col3), 你的索引会生效于(col1),
(col1, col2), 以及(col1, col2, col3)**  

> **MySQL cannot use the index to perform lookups if the columns do not 
form a leftmost prefix of the index. Suppose that you have the SELECT 
statements shown here:**  

**如果查询的列不是索引的最左前缀, 那MySQL不会将索引用于执行查询. 假设你有
下列查询语句:** 
```sql
SELECT * FROM tbl_name WHERE col1=val1;
SELECT * FROM tbl_name WHERE col1=val1 AND col2=val2;

SELECT * FROM tbl_name WHERE col2=val2;
SELECT * FROM tbl_name WHERE col2=val2 AND col3=val3;
```

> If an index exists on (col1, col2, col3), only the first two queries 
use the index. The third and fourth queries do involve indexed columns, 
but do not use an index to perform lookups because (col2) and (col2, col3) 
are not leftmost prefixes of (col1, col2, col3).  

如果索引存在于(col1, col2, col3), 那只有头两个查询语句用到了索引. 第三个和
第四个查询包含索引的列, 但是不会用索引去执行查询. 因为(col2)和(col2, col3)
不是(col1, col2, col3)的最左前缀  

## 官方文档小结  
其实官方文档已经解释的非常详细了, 总结关于最左匹配的解释, 那其实只有这么
几句话:  
1.按照文档, 更准确的说法应该是最左前缀原则, 即如果你创建一个联合索引, 那
这个索引的任何前缀都会用于查询, (col1, col2, col3)这个联合索引的所有前缀
就是(col1), (col1, col2), (col1, col2, col3), 包含这些列的查询都会启用索
引查询.   
2.其他所有不在最左前缀里的列都不会启用索引, 即使包含了联合索引里的部分列
也不行. 即上述中的(col2), (col3), (col2, col3) 都不会启用索引去查询.  
**注意, (col1, col3)会启用(col1)的索引查询**  

### 官方文档的其他细节  
读一下官方文档, 还有很多别的发现, 跟最左前缀无关, 关于联合索引的别的细节, 
总结如下:  
- 联合索引最多只能包含16列  
- blob和text也能创建索引, 但是必须指定前面多少位  
- 官方推荐了一种联合索引的替代方案: 可以额外创建一列, 其列值由联合索引包含
的所有列值所生成的hash值来构成(个人认为似乎破坏了第一范式的设计规则) 