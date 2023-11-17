---
title: MySQL不定时笔记
date: 2023-11-12 13:07:51
tags:
---

### WHERE解析顺序

- MySQL从左向右解析
- Oracle从右往左解析
- 根据解析顺序把最优筛选用于最先解析
- [最左原则](https://blog.cindyforever.com/2019/10/15/tech/%E5%AE%98%E6%96%B9%E6%96%87%E6%A1%A3%E8%A7%A3%E9%87%8AMySQL%E6%9C%80%E5%B7%A6%E5%8C%B9%E9%85%8D(%E6%9C%80%E5%B7%A6%E5%89%8D%E7%BC%80)%E5%8E%9F%E5%88%99/?highlight=mysql)

<!-- more -->
### SQL执行顺序

- 缓存（MySQL8.0移除）
  - MySQL的缓存机制主要包括查询缓存和InnoDB缓冲池（Buffer Pool）。
  - 查询缓存是MySQL中的一个全局缓存，用于缓存查询语句的结果集。当一个查询语句被执行时，MySQL首先检查查询缓存，如果相同的查询已经被缓存过并且缓存有效，则直接返回缓存的结果，而不需要执行实际的查询操作。（需要查询语句完全相同才能命中缓存）
  - InnoDB缓冲池是InnoDB存储引擎中的一个重要组件，用于缓存数据和索引页。它将磁盘上的数据和索引页加载到内存中，以提高数据的访问速度。
- 分析器
  - 词法分析（Lexical Analysis）：将SQL语句分解为一个个的标记（tokens），如关键字、标识符、运算符等。
  - 语法分析（Syntax Analysis）：根据SQL语句的语法规则，将标记组合成语法树（parse tree）或语法分析树（syntax tree）。
  - 语义分析（Semantic Analysis）：对语法树进行语义检查，包括检查表名、列名的存在性和合法性，检查语句的权限等。
- 优化器
  - 查询优化（Query Optimization）：对查询语句进行优化，选择最优的执行计划。这涉及到索引选择、连接顺序、连接类型等优化策略。
- 执行器
  - 执行计划生成（Execution Plan Generation）：根据优化后的查询计划，生成实际的执行计划，包括确定表的访问顺序、使用哪些索引等。
  - 执行计划执行（Execution）：按照生成的执行计划执行查询，包括从磁盘读取数据、进行排序、聚合等操作。
- 结果返回（Result Retrieval）：将执行结果返回给客户端
- PreparedStatement：节省分析和优化的消耗

### SQL关键字解析顺序（从上往下顺序解析）

- FROM
- ON
- JOIN
- WHRER
- GROUP BY
- HAVING
- SELECT columns
- DISTINCT
- ORDER BY
- LIMIT

### MySQL全文索引

MySQL中的全文索引是一种用于支持全文搜索的索引类型。它能够提供更高效的文本搜索功能，使得在大量文本数据中进行关键字搜索变得更加快速和准确。

- 特点
  - 支持全文搜索：全文索引可以对文本数据进行索引，使得可以通过关键字搜索来查找包含指定关键字的文本。
  - 分词处理：在创建全文索引时，MySQL会对文本进行分词处理，将文本拆分成一个个词语（或称为词项），并将这些词语存储在全文索引中。
  - 忽略常见词和停用词：全文索引会忽略一些常见词和停用词（如"and"、"the"、"is"等），这些词通常对搜索结果没有实质性的帮助。
  - 支持关键字匹配：全文索引支持多种关键字匹配模式，如全文匹配、布尔搜索、短语搜索等。
  - 支持排序和评分：全文索引可以根据搜索结果的相关性进行排序，并为每个搜索结果分配一个评分，以便更好地反映搜索结果的相关性。
- 要使用全文索引，需要满足以下条件：
  - 数据表的存储引擎必须是支持全文索引的引擎，如InnoDB、MyISAM等。注意，InnoDB引擎在MySQL 5.6版本之前不支持全文索引。
  - 要创建全文索引的列必须是字符类型的列，如VARCHAR、TEXT等。
- 使用示例：
  - 创建全文索引：
  ```sql
  ALTER TABLE 表名 ADD FULLTEXT 索引名 (列名1, 列名2, ...);
  ```
  - 执行全文搜索：
  ```sql
  SELECT * FROM 表名 WHERE MATCH(列名) AGAINST('关键字');
  ```

### 存储引擎

- 查看存储引擎
```sql
show engines;
```
- 指定存储引擎
```sql
CREATE TABLE table_name (
  column1 datatype,
  column2 datatype,
  ...
) ENGINE = engine_name;
```
- 存储引擎分类
  - InnoDB：InnoDB是MySQL的默认存储引擎，它支持事务和行级锁定。它提供了高度的并发性和可靠性，并具有较好的崩溃恢复能力。InnoDB还支持外键约束和支持ACID（原子性、一致性、隔离性和持久性）事务。
  - MyISAM：MyISAM是MySQL的另一个常用存储引擎。它不支持事务和行级锁定，但具有较高的性能和较小的存储开销。MyISAM适用于读密集型的应用场景，如日志记录、数据仓库等。
  - Memory（或者称为Heap）：Memory存储引擎将数据存储在内存中，提供了非常快速的读写操作。但是，由于数据存储在内存中，重启MySQL服务或服务器故障会导致数据丢失。Memory存储引擎适用于需要快速读写操作和临时数据存储的场景，如缓存表、临时表等。
  - NDB Cluster：NDB Cluster存储引擎是MySQL集群（MySQL Cluster）的一部分，它提供了高可用性和可扩展性。NDB Cluster存储引擎将数据分布在多个节点上，并提供了分布式事务和高度并发的访问。它适用于大规模的分布式应用，如互联网应用、实时数据处理等。
  - Archive引擎：Archive存储引擎是一种用于存储归档数据的引擎。它的设计目标是提供高度的数据压缩和快速的插入操作，适用于需要长期存储大量历史数据的场景。Archive引擎使用了行级别的压缩算法，可以将数据压缩到很小的存储空间。但是，它不支持索引和更新操作，只支持追加插入和查询操作。
  - CSV引擎：CSV存储引擎将数据以逗号分隔值（CSV）的格式存储在文本文件中。它适用于需要与其他应用程序或工具进行数据交换的场景，如导入和导出数据。CSV引擎的数据存储在文本文件中，不支持索引和事务，因此在查询和更新操作方面性能较低。它更适合于只需要简单读取数据的场景。
  - Blackhole引擎：Blackhole存储引擎是一个特殊的引擎，它不会实际存储数据，而是将所有写入操作都丢弃，只保留了表结构。它适用于数据复制和数据同步的场景，可以用作数据的中转站。通过将数据写入Blackhole引擎，可以快速将数据复制到其他MySQL实例或进行其他处理，而无需实际存储数据。Blackhole引擎对读取操作返回空结果。

###  [InnoDB中MySQL中一条记录是怎么存储的？](https://www.xiaolincoding.com/mysql/base/row_format.html)

在MySQL中，一行记录是按照表的定义和存储引擎的规则进行存储的。具体的存储方式会根据使用的存储引擎而有所不同。下面是一般情况下的存储方式：

- 行格式（Row Format）：MySQL支持不同的行格式，如Compact、Redundant和Dynamic等。每种行格式都有不同的存储方式和特性。下面是一般情况下的行记录存储方式：
  - 行头信息（Header）：包含了一些元数据，如是否为NULL、是否为变长字段等信息。
  - 列数据（Column Data）：按照表的定义，将每个列的数据存储在相应的位置上。对于变长字段，可能会有额外的指针来指示实际存储位置。
  - 记录头信息（Record Header）：包含了一些记录级别的元数据，如版本号、删除标记等。
  - 记录尾信息（Record Footer）：包含了一些校验和和其他控制信息。
- 存储引擎的差异：不同的存储引擎可能会有不同的存储方式和优化策略。例如，InnoDB存储引擎使用了聚簇索引的概念，将数据按照主键的顺序进行存储，这样可以提高查询性能。而MyISAM存储引擎则将数据存储在不同的文件中，分别存储数据和索引，以提供更高的并发性能。

### 日志分类

#### 二进制日志（Binary Log）
二进制日志是MySQL中最重要的日志类型之一。它记录了所有对数据库的修改操作，包括插入、更新和删除等，以二进制的形式保存在磁盘上。二进制日志可以用于数据恢复、主从复制和数据库的增量备份等。
- 启用：要启用二进制日志，需要在MySQL的配置文件中进行相应的设置。通常，可以在配置文件（如my.cnf或my.ini）中添加以下配置项
```conf
log_bin = /path/to/binlog  # 会生成binlog.000001这样的文件
```
- 查看和管理二进制日志
```sql
SHOW BINARY LOGS; #查看当前正在使用的二进制日志文件列表。
SHOW BINLOG EVENTS IN 'binlog.000001'; # 查看binlog内容
SHOW MASTER STATUS; #查看当前正在写入的二进制日志文件和位置。
PURGE BINARY LOGS; #清理旧的二进制日志文件，释放磁盘空间。
```
- 二进制日志格式：MySQL提供了多种二进制日志格式，包括Statement、Row和Mixed。不同的格式有不同的特点和适用场景。主从备份的情况下，一般可以主机用Statement模式，从机用Row模式。
  - Statement格式记录SQL语句本身。（缺少每条数据发生变化的细节）
  - Row格式记录每一行数据的修改。
  - Mixed格式根据具体情况选择使用Statement或Row格式。
- 使用Binlog恢复数据的步骤
  - 确认备份和binlog文件的完整性
  - 停止mysql服务器`sudo systemctl stop mysql`
  - 恢复备份文件
  - 回放binlog，从备份文件最后的时间节点开始回放`mysqlbinlog --base64-output=DECODE-ROWS --verbose --start-position=<start_position> --stop-position=<stop_position> <binlog_file>` 或者 `mysqlbinlog --no-defaults --database=mydb --base64-output=decode-rows -v --start-datetime='2019-12-11 16:30:00' --stop-datetime='2019-12-11 16:31:00' mysql-bin.000003`
    - 其中start_position和stop-position取自`SHOW BINLOG EVENTS IN 'binlog.000001'`
    - 如何确定start-position
      - 使用SHOW MASTER STATUS命令
      - 使用mysqlbinlog binlog.000001
      - 查看备份工具生成的备份日志
    - docker中使用
      - `docker exec -it <container_name_or_id> bash` 进入容器内命令行
      - 找到mysql的binlog目录，默认`/var/lib/mysql`
      - 执行`mysqlbinlog --base64-output=DECODE-ROWS --verbose --start-position=<start_position> --stop-position=<stop_position> <binlog_file>`
  - 使用如下命令将回放记录放回到MySQL服务器中：`mysqlbinlog --base64-output=DECODE-ROWS --verbose --start-position=<start_position> --stop-position=<stop_position> <binlog_file> | mysql -u <username> -p <database_name>`
- binglog的event_type类型
  - Query：表示SQL语句执行事件，记录了对数据库执行的SQL查询语句。
  - Table_map：表示表映射事件，记录了每个表的结构信息，包括表名、列名、列类型等。
  - Write_rows：表示写入行事件，记录了向表中插入新行的操作。
  - Update_rows：表示更新行事件，记录了对表中行进行更新的操作。
  - Delete_rows：表示删除行事件，记录了从表中删除行的操作。
  - Xid：表示事务提交事件，记录了事务的提交操作。
  - Rotate：表示binlog文件切换事件，记录了binlog文件的切换信息。
  - Anonymous_Gtid：当MySQL服务器配置为使用[GTID](https://dev.mysql.com/doc/refman/8.0/en/replication-gtids-concepts.html)复制模式时，如果在binlog中记录的事务没有具体的用户事务标识符（即没有指定GTID），则会使用Anonymous_Gtid作为事件类型来表示这些事务。
- 在`mysqldump`命令中，`--single-transaction`确保事务一致性。
  - 只适用于使用事务引擎的表，如InnoDB。

#### 事务日志（Transaction Log）
事务日志也称为重做日志（Redo Log），用于记录已提交的事务对数据库的修改操作。事务日志是InnoDB存储引擎特有的日志类型，用于保证数据库的事务持久性和崩溃恢复。它记录了事务对数据的修改操作，以便在数据库崩溃时进行数据恢复。

#### 错误日志（Error Log）
错误日志记录了MySQL服务器在运行过程中发生的错误和异常信息。它包含了警告、错误、连接问题等相关的日志信息。错误日志对于排查和解决数据库运行中的问题非常有用。

#### 查询日志（Query Log）
查询日志记录了MySQL服务器接收到的所有查询语句，包括SELECT、INSERT、UPDATE和DELETE等操作。查询日志可以用于分析和优化查询性能，但是在高负载环境下开启查询日志会对性能产生一定的影响。

#### 慢查询日志（Slow Query Log）
慢查询日志记录了执行时间超过指定阈值的查询语句。它可以帮助开发人员和数据库管理员发现慢查询语句，并进行性能优化。慢查询日志对于定位和解决数据库性能问题非常有用。
- 启用慢查询
```conf
slow_query_log = 1
slow_query_log_file = /path/to/slow-query.log
long_query_time = 1
```
- 慢查询分析工具
```sql
mysqldumpslow -s t slow-query.log
```
- [percona-toolkit：开源分析数据库工具](https://percona.com/software/database-tools/percona-toolkit)


#### 撤销日志（Undo Log）：
撤销日志也是InnoDB存储引擎特有的日志类型，用于支持事务的回滚操作。当事务执行过程中需要回滚时，撤销日志记录了对数据的逆操作，以实现事务的原子性


### 查看参数命令

- 查看binlog配置 `show variables like '%binlog%';`

### 备份

- 备份策略
  - 全量备份：全量备份将整个数据集完整备份，适用于首次备份和周期性完整备份。
  - 差异备份：差异备份备份自上次全量备份以来的变化部分，相对于全量备份来说备份时间和存储空间较少。
  - 增量备份：增量备份备份自上次备份以来的增量变化，备份时间最短，存储空间最小。
- 备份方式
  - 时间点恢复
  - 热备：不影响数据库读写（MyISAM不能进行热备，InnoDB可以）
    - MyISAM不能进行热备的原因
      - 缺乏事务支持：MyISAM是一种非事务性的存储引擎，它不支持ACID（原子性、一致性、隔离性和持久性）特性。在进行备份时，如果数据库正在处理写操作，备份可能会导致数据不一致或损坏。
      - 表级锁定：MyISAM使用表级锁定来控制并发访问。当备份工具尝试锁定表时，其他查询和写操作将被阻塞，可能导致数据库性能下降。这种锁定机制使得在备份期间无法进行正常的读写操作，因此无法实现热备份。
      - 数据文件和索引文件分离：MyISAM引擎将数据存储在一个文件中，索引存储在另一个文件中。在备份过程中，如果同时备份数据文件和索引文件，可能会导致备份的不一致性。因此，需要在备份期间停止对数据库的写操作，以确保数据文件和索引文件的一致性。
  - 温备：数据库只能进行读操作，不能进行写操作
  - 冷备：数据库需要停机
  - 物理备份：直接复制数据库文件
  - 逻辑备份：数据导出方式
- 备份工具
  - msyqldump
    - `mysqldump`是一个用于备份和恢复MySQL数据库的命令行工具。它可以生成一个包含数据库结构和数据的SQL脚本文件，以便在需要时进行恢复或迁移数据库。
    - 备份数据库，使用以下命令来备份整个数据库：
    ```
    mysqldump -u <username> -p <database_name> > backup.sql
    ```
    将`<username>`替换为具有备份权限的MySQL用户名，`<database_name>`替换为要备份的数据库名称，`backup.sql`为备份文件的名称和路径。
    如果只想备份数据库结构而不包括数据，可以添加`--no-data`选项：
    ```shell
    mysqldump -u <username> -p --no-data <database_name> > backup.sql
    ```
    - 恢复数据库：
    使用以下命令将备份文件中的数据恢复到MySQL数据库中：
    ```shell
    mysql -u <username> -p <database_name> < backup.sql
    ```
    将`<username>`替换为具有恢复权限的MySQL用户名，`<database_name>`替换为要恢复的数据库名称，`backup.sql`为备份文件的名称和路径。
    如果备份文件中只包含数据库结构而不包括数据，可以添加`--no-data`选项：
    ```
    mysql -u <username> -p --no-data <database_name> < backup.sql
    ```
  - xtrabackup
    - percona提供
    - 物理备份工具
    - 支持全量，差异，增量备份
  - select语句备份
  - cp命令：物理备份
- 备份策略实施
  - 备份流程
    - 备份工具：mysqldump，xtrabackup等
    - 备份方式：热备，温备，冷备；物理备份，逻辑备份
  - 考虑数据量
    - 数据量较小的情况下：逻辑备份，如mysqldump
    - 数据量较大的情况下：物理备份，如xtrabackup
  - 考虑时间点补偿
    - 从上一个全量备份到当前时间的数据
    - 这部分数据使用binlog进行恢复
    - 读多写少用建议使用row格式，写多读少使用statement格式
      - statement不能恢复的情况举例：如上次全量备份后误执行了一条update全表的语句，那么从全量备份的时间点到当前时间点，statement日志中只有这一条update语句，也就无法恢复到全量备份时的状态。
      - 另一种情况：执行了两条sql，第一条+50，第二条-100，但是实际情况中第二条执行失败。而MySQL无论哪种日志格式，都会记录成功和失败的记录，因此在statement格式日志中，就是两条记录，在恢复时就会同时执行两条，导致数据与实际不符。
  - 备份方案
    - 全量备份，使用cron语法执行mysqldump全量备份脚本
    - 增量备份，使用binlog
[mysql备份.pdf](mysql.pdf)

### 数据库查询优化

- SQL及索引
  - 适合建索引的字段
    - 值差异较多的字段（如手机号，不合适：性别字段）
    - 经常查询的字段
    - 常用的关联字段
    - 统计和分组的字段
    - 非必要不建立索引，会影响插入修改删除性能
  - explain
    - 用法`EXPLAIN SELECT * FROM table_name WHERE condition;`
    - 返回结果
      - id：查询的唯一标识符，用于标识查询中的每个操作步骤。
      - select_type：表示查询的类型，例如SIMPLE表示简单查询，SUBQUERY表示子查询，JOIN表示连接查询等。
      - table：表示查询涉及的表名。
      - type：表示表的访问类型，常见的类型包括ALL（全表扫描）、index（索引扫描）、range（范围扫描）、ref（基于索引的等值查找）等。
      - possible_keys：表示可能使用的索引。
      - key：表示实际使用的索引。
      - rows：表示估计的扫描行数。
      - Extra：包含其他附加信息，如排序、临时表等。
  - 索引命中规则
    - 等值匹配：如果查询条件中使用了等值比较（如=），并且该列上存在索引，那么索引就会被命中。例如，`WHERE column_name = value`。
    - 范围查询：如果查询条件中使用了范围查询（如>、<、BETWEEN、IN等），并且该列上存在索引，那么索引的部分会被命中。范围查询只能利用索引的有序性，但在范围的两端之间的值需要进行全表扫描。例如，`WHERE column_name > value`。
    - 前缀匹配：如果查询条件中使用了字符串的前缀匹配（如LIKE 'prefix%'），并且该列上存在索引，那么索引就会被命中。前缀匹配可以利用索引的前缀部分进行查询，加快查询速度。例如，`WHERE column_name LIKE 'prefix%'`。
    - 最左前缀匹配：如果查询条件中使用了多列索引，并且查询条件从索引的最左边开始匹配，那么索引就会被命中。如果查询条件不满足最左前缀匹配，那么索引将无法被完全利用。例如，如果有一个联合索引 (column1, column2)，查询条件为 `WHERE column1 = value AND column2 = value`。
    - 精确匹配：如果查询条件中使用了精确匹配（如主键查询），那么索引就会被命中。主键是一种特殊的索引，它的值是唯一的，因此精确匹配主键时索引命中的几率非常高。
    - 覆盖索引：当查询结果中有且仅有索引的列时，就会命中索引。如组合索引时（column1，column2），此时查询sql为`select column1, column2 from table1`，这样就会少了一步[回表操作](https://zhuanlan.zhihu.com/p/401198674)，无论where条件如何都会命中索引。
  - 索引不命中的情况
    - 函数应用于索引字段：如果查询中的函数应用于索引字段，例如 WHERE UPPER(column_name) = 'VALUE'，索引可能无法被使用。这是因为函数会对索引字段的值进行转换，导致无法直接匹配索引。
    - 列值表达式：如果查询中使用了列值表达式，例如 `WHERE column_name + 1 = 10`，索引可能无法被使用。列值表达式会对索引字段的值进行计算，使得无法直接匹配索引。
    - 隐式类型转换：如果查询中使用了隐式类型转换，例如 `WHERE column_name = '10'`，而实际上 column_name 的数据类型是整数，索引可能无法被使用。隐式类型转换会导致索引无法直接匹配查询条件。

精确匹配：如果查询条件中使用了精确匹配（如主键查询），那么索引就会被命中。主键是一种特殊的索引，它的值是唯一的，因此精确匹配主键时索引命中的几率非常高。
- 数据库表结构：三范式
- MySQL系统配置，Linux配置
- 硬件优化

### kill掉锁表进程

- 使用 `SHOW PROCESSLIST;` 命令来查看当前的 MySQL 进程列表，包括正在执行的查询和锁定的表。这个命令将显示每个连接的详细信息，包括连接ID、用户、执行状态、执行时间等。你可以在执行状态 (State) 列中查找包含 "Waiting for table lock" 或类似信息的进程。然后执行`kill pid;`

[mysql备份优化和数据库架构设计.pdf](mysql2.pdf)

### 数据库字段设计规范

- 优先选择符合存储的最小数据类型
  - int来存储时间戳
  - varchar(255) 无论存入的数据是多长，都会占用255个字符
- 避免使用text和blob类型，必要使用的话最好另外建立关联表
- 避免使用枚举：修改枚举要用ddl语句
- 字段尽量定义为非空字段，否则数据表会在字段表头额外开辟一个空间保存该字段是否为空
- 日期尽量不要用字符串存储
  - 无法用日期函数比较
  - （某些格式）无法直接排序
  - 占用空间更大（相比数字类型）
- 财务数据用decimal存储

### 分区表

- 正常表的存放方式
  - `table.frm` 表结构文件，ddl数据
  - `table.ibd` innodb的数据和索引文件
    - myisam 会生成两个文件 `table.MYD` 和 `table.MYI` 分别是数据文件和索引文件。
- mysql5.1之后就支持分区表
  - 查看命令：`show plugins;`
  - `partion` 参数
- 为什么使用分区表
  - 数据量过大
  - 历史数据无需过度关心
  - 归档
    - 常规方式
      - 以时间戳方式where条件来获取需要删除的数据
        - mysqldump命令方式也可以
      - 获取到要删除的数据放到备份表或者归档表
        - delete命令删除后不会立刻释放空间，mysql通过B+树方式存储数据，节点上的空间需要留着用于检索
        - 可通过 `optimize table my_table_name`命令来立刻释放空间
    - 分区表方式
      - 可以通过时间范围，地点范围等条件对数据进行单独管理和维护
      - 可以通过删除整个分区来快速删除数据，而不需要逐行删除
      - 通过将数据分散到多个分区，可以减少查询的数据量，提高查询效率
- 分区类型
  - HASH分区：根据指定的哈希算法对数据进行分区。哈希分区可以均匀地将数据分散到不同的分区中，适用于负载均衡和分布式查询。例如，可以根据用户ID的哈希值将数据分为不同的分区。
  ```sql
  CREATE TABLE partitioned_table (
        id INT,
        name VARCHAR(50),
        user_id INT
    )
    PARTITION BY HASH (user_id) 
    PARTITIONS 4;
  ```
  - LIST分区：根据指定的列值列表对数据进行分区。可以根据列的离散取值进行分区。例如，可以根据地区将数据分为不同的分区。
  ```sql
    CREATE TABLE partitioned_table (
        id INT,
        name VARCHAR(50),
        region VARCHAR(50)
    )
    PARTITION BY LIST (region) (
        PARTITION p_east VALUES IN ('New York', 'Boston'),
        PARTITION p_west VALUES IN ('Los Angeles', 'San Francisco'),
        PARTITION p_other VALUES IN (DEFAULT)
    );
  ```
  - RANGE分区：根据指定的范围对数据进行分区。可以根据数值、日期或字符串等列的范围进行分区。例如，可以按照日期范围将数据分为不同的分区。
  ```sql
    CREATE TABLE partitioned_table (
        id INT,
        name VARCHAR(50),
        date_column DATE
    )
    PARTITION BY RANGE (YEAR(date_column)) (
        PARTITION p0 VALUES LESS THAN (2010),
        PARTITION p1 VALUES LESS THAN (2020),
        PARTITION p2 VALUES LESS THAN (2030),
        PARTITION p3 VALUES LESS THAN MAXVALUE
    );
  ```
  - KEY分区：类似于哈希分区，但是键分区使用用户指定的表达式来计算分区键的哈希值。可以根据自定义的分区规则对数据进行分区。
  ```sql
    CREATE TABLE partitioned_table (
        id INT,
        name VARCHAR(50),
        user_id INT
    )
    PARTITION BY KEY (user_id)
    PARTITIONS 8;
  ```
- 分区完成后会生成相应数量的分区文件(ibd文件)，insert的数据会根据分区规则进入到不同的文件中
- 查看分区表的详细信息
```sql
select t.TABLE_NAME, t.PARTITION_NAME, t.PARTITION_DESCRIPTION, t.TABLE_ROWS
from information_schema.PARTITIONS t where t.TABLE_NAME = 'year_partition';
```
- 修改分区表结构
  - 删除分区：`ALTER TABLE partitioned_table DROP PARTITION partition_name;`
  - 增加分区：`ALTER TABLE year_partition ADD PARTITION (PARTITION p1 VALUES LESS THAN ((2020)));`
    - 注意：如果最后一个分区已经是MAXVALUE了，则不能通过该语句增加分区
- 查询某一分区的数据
```sql
select * from year_partition partition ($partition_name);
```
- 分区交换进行快速归档（mysql v5.7之后）
  - 两个表结构相同
  - 归档表不能是分区表，不能有外键约束
  - 交换
  ```sql
  alter table orig_table exchange partition partition_name with archive_table;
  ```
  - 更改归档表引擎为archive（归档引擎，只读），压缩空间
  ```sql
  alter table archive_table engine=archive;
  ```
  - 删除原表分区`ALTER TABLE partitioned_table DROP PARTITION partition_name;`
- 注意事项
  - 如果列表和范围分区没指定所有的值，超出分区范围的值插入就会报错
  - 已经存在数据的业务表可以增加分区（已存在数据会重排）
    - 数据量大的情况下，系统资源会消耗很大
    - 一般通过创建新的相同结构的分区表，进行重新复制插入来分区
  - mysql v5.x版本支持最大分区1024个，8.0后根据系统资源决定
    - 分区消耗资源，不用建立太多
  - 如果表里有主键，必须包含分区表的分区键
  - 分区表不支持外键
  - 分区规则需要提前确定好，有数据后中途修改非常不妥
  - Range分区特别适合用于日志

### 高可用HA实现

- mysql主从复制基本逻辑
  - 配置主服务器：首先，在主服务器上需要启用二进制日志（binary log）。二进制日志记录了主服务器上执行的所有数据修改操作，包括插入、更新和删除等操作。
  - 配置从服务器：在从服务器上需要配置主服务器的连接信息，并启动从服务器的复制进程。从服务器连接到主服务器，并请求从主服务器获取二进制日志的内容。
  - 复制进程：主服务器将二进制日志中的数据更改事件发送给从服务器。从服务器接收到这些事件后，将它们应用到自己的数据库中，以保持与主服务器上的数据一致。
  - 日志位置：主服务器会为每个复制事件分配一个唯一的日志位置（log position）。从服务器会跟踪自己复制到的日志位置，以便在下次连接主服务器时，可以继续从上次的位置继续复制。
  - 异步复制：主从复制是异步的，这意味着主服务器上的数据修改操作可以在稍后的时间应用到从服务器上。主从复制的延迟取决于网络延迟、主服务器的负载和从服务器的性能等因素。
  - 故障恢复：如果主服务器发生故障，可以将一个从服务器提升为新的主服务器，以继续提供服务。一旦主服务器恢复，它可以重新加入到主从复制的拓扑中，作为一个新的从服务器。
- 复制方式
  - binlog+position
    - 执行导出：`mysqlbinlog --base64-output=DECODE-ROWS --verbose --start-position=<start_position> --stop-position=<stop_position> <binlog_file>`
    - 执行写入：`mysqlbinlog --base64-output=DECODE-ROWS --verbose --start-position=<start_position> --stop-position=<stop_position> <binlog_file> | mysql -u <username> -p <database_name>`
  - binglog+GTID
- **主从配置步骤**
  - 确保两个MySQL服务器上已经正确安装并且正常运行。主服务器和从服务器可以是不同的物理服务器或虚拟机
  - 在主服务器上编辑主配置文件（my.cnf）。找到以下行并进行相应修改：
    ```makefile
    server-id = 1  
    log_bin = /var/log/mysql/mysql-bin.log  
    binlog_do_db = your_database_name
    ```
    `server-id` 设置为唯一的ID（例如1），`log_bin` 启用二进制日志并指定日志文件的路径，`binlog_do_db` 指定要复制的数据库名称。根据实际情况进行修改。
  - 重启主服务器以使配置更改生效。
  - 在从服务器上编辑从配置文件（my.cnf）。找到以下行并进行相应修改：
    ```makefile
    server-id = 2  
    relay_log = /var/log/mysql/mysql-relay-bin.log  
    log_bin = /var/log/mysql/mysql-bin.log  
    read_only = 1
    ```
    `server-id` 设置为唯一的ID（例如2），`relay_log` 启用中继日志并指定日志文件的路径，`log_bin` 启用二进制日志并指定日志文件的路径，`read_only` 设置为1以使从服务器只接收来自主服务器的复制。根据实际情况进行修改。
  - 重启从服务器以使配置更改生效。
  - 在主服务器上创建一个用于复制的用户，并授予该用户复制权限。打开MySQL命令行客户端，执行以下命令：
    ```sql
    CREATE USER 'replication_user'@'%' IDENTIFIED BY 'password';  
    GRANT REPLICATION SLAVE ON *.* TO 'replication_user'@'%';  
    FLUSH PRIVILEGES;
    ```
    将 `'replication_user'@'%'` 替换为适当的用户名和密码。该用户将用于从服务器连接到主服务器并进行复制。
  - 在从服务器上执行以下命令，设置从服务器的复制密码：
    ```sql
    CHANGE MASTER TO MASTER_HOST='master_ip_address', MASTER_USER='replication_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='mysql-bin.XXXXXX', MASTER_LOG_POS=XXXX;
    ```
    将 `master_ip_address` 替换为主服务器的IP地址，`replication_user` 和 `password` 替换为第6步中创建的复制用户的用户名和密码，`mysql-bin.XXXXXX` 替换为主服务器当前的二进制日志文件名（注意，这里指定的文件路径和下一个参数MASTER_LOG_POS是为了指定同步开始的位置，并不是说只同步该binlog文件），`XXXX` 替换为对应文件中的位置（可以通过 `SHOW MASTER STATUS;` 命令在主服务器上获得）。
  - 在从服务器上启动复制进程：
    ```sql
    START SLAVE;
    ```
  - 检查复制状态：在从服务器上执行以下命令：
    ```sql
    SHOW SLAVE STATUS\G;
    ```
    检查结果中的 `Slave_IO_Running` 和 `Slave_SQL_Running` 是否都为 `Yes`，这表示复制已经成功启动。如果其中一个或两个都为 `No`，请检查配置和网络连接并重新启动复制进程。
[数据库高可用实现](mysql3.pdf)


### 双主同步方式

[MySQL高可用集群&产品设计流程分析](mysql4.pdf)
