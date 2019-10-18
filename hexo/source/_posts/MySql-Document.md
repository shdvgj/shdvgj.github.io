---
title: MySql 8.0参考手册中文版（一）
date: 2019-10-17 09:03:03
tags: 
- mysql
- translate
comments: true
categories: 
- 学习
- 数据库
---
[原文文档](https://dev.mysql.com/doc/refman/8.0/en/)  

**声明**

该文档纯粹自己手动翻译， 也许网上有更好的版本， 但是我这个属于自己学习的版本， 为了提高英语水平和对MySQL的理解， 后续不保证会持续更新，内容如有理解错误， 还请指正。

## MySQL 8.0 参考手册  

### 包含MySQL NDB集群8.0  

#### 摘要  

这是一份MySQL参考手册。 该文档分别记录了MySQL8.0到8.0.20，以及基于NDB8.0版本到NDB8.0.19版本的NDB集群发布版本。这份文档可能包括一些目前还没发布的MySQL版本的特性。想要了解哪些版本已经发布，请查看[MySQL8.0发布日志](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/)。  

**MySQL8.0特性**。这份手册描述的特性并没有包含在所有版本的MySQL8.0中；这些特性可能不包含在你所购买的MySQL8.0版本中。如果你有任何问题是关于你的MySQL8.0版本中所包含的特性的，请参阅你的MySQL8.0许可协议或者联系你的oracle销售代表。  

有关每个发布版本的变化，请查看[MySQL8.0发布日志](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/)。  

有关法律信息和许可信息，请查看[前言和法律声明](https://dev.mysql.com/doc/refman/8.0/en/preface.html)。  

在使用MySQL中需要帮助，请访问[MySQL论坛](http://forums.mysql.com/)，你可以在那里向别的MySQL用户描述你的问题。  

文档创建于：2019-10-16  

## 1.  一般信息  

MySQL提供了非常快速的，多线程，多用户，和健壮的SQL数据库服务。MySQL服务用于关键服务，高负荷生产的系统以及嵌入大量部署的软件。Oracle是Oracle公司及其分支机构的注册商标。MySQL是Oracle公司及其分支机构的商标，未经Oracle正式的书面许可，客户不得使用。其他的名字可能是其各自所有者的商标。  

MySQL软件具有双重许可。用户可以在GNU通用公共许可 (http://www.fsf.org/licenses/)  条款下将MySQL软件作为开源软件产品使用，或者从Oracle购买一份标准的商业许可。访问 http://www.mysql.com/company/legal/licensing/  以获取更多关于授权政策的信息。  

下面的列表描述了一些在本手册中需要特别关注的部分：  

 - 访问[1.3.2章节，“MySql的主要特性”](https://dev.mysql.com/doc/refman/8.0/en/features.html)，讨论关于MySQL数据库服务的功能。

 - 访问[1.4章节，“MySQL8.0更新了什么”](https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html)，获取关于MySQL新功能的概况。访问[发布日志](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/)，获取关于每个版本的更新信息。

 - 访问[2章节，安装和升级MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)，获取安装介绍。访问[章节2.11](https://dev.mysql.com/doc/refman/8.0/en/upgrading.html)，获取MySQL升级说明。  

 - 访问[第3章节，教程](https://dev.mysql.com/doc/refman/8.0/en/tutorial.html)，获取MySQL数据库服务的教程介绍。

 - 访问 [第5章节，MySQL服务管理](https://dev.mysql.com/doc/refman/8.0/en/server-administration.html)，获取关于MySQL服务的配置和管理的信息。

 - 访问 [第6章节，安全](https://dev.mysql.com/doc/refman/8.0/en/security.html)，获取关于MySQL的安全信息。

 - 访问 [第17章节，复制](https://dev.mysql.com/doc/refman/8.0/en/replication.html)，获取关于设置复制服务器的信息。

 - 访问[第30章节，MySQL企业版](https://dev.mysql.com/doc/refman/8.0/en/mysql-enterprise.html)，获取关于MySQL企业版的信息，商用版的MySQL会发布一些进阶的功能和管理工具。

 - 访问 [附录A，MySQL8.0常见问题](https://dev.mysql.com/doc/refman/8.0/en/faqs.html)，获取关于MySQL服务及其功能的一些常见问题的答案。

 - 访问[发布日志](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/)，获取关于新特性和bug修复的发布信息。

     

   **重要**  

   如果要报告问题或者bug，请按照[章节17，“如何报告bug和问题”](https://dev.mysql.com/doc/refman/8.0/en/bug-reports.html)的指示。如果你发现在MySQL中的一个敏感的安全性的bug，请立即发送邮件信息到< [secalert_us@oracle.com](mailto:secalert_us@oracle.com) >让我们知晓。例外：支持客户应该报告所有的问题，包括安全bug，发送给Oracle支持部门。  

   

### 1.1 关于本手册  

这份参考手册适用于MySQL数据库系统8.0版本到8.0.20版。在本文中关于和MySQL8.0镜像版本的区别参照了（8.0.x）的发行版本。查看[法律声明](https://dev.mysql.com/doc/refman/8.0/en/preface.html#legalnotice)，获取许可的信息。

本手册不适用于MySQL软件的更早版本，因为在MySQL8.0和更早版本之间存在着很多功能及别的方面的不同。如果你正在使用MySQL软件的早期发布版本，请参考对应的手册。比如：[MySQL5.7操作手册](https://dev.mysql.com/doc/refman/5.7/en/)可以覆盖MySQL软件的5.7发布版本系列。

由于这个手册是作为参考来使用，因此不会提供关于SQL或关联的数据库概念的一般介绍。同样也不会教你如何使用你的操作系统或命令行解释器。

MySQL数据库软件在经历不断的发展，其手册同样也会不断的更新。最新版本的手册可以在 https://dev.mysql.com/doc/ 在线搜索到。其他格式也可以在这里获取，包括HTML，PDF和EPUB版本。

参考手册的源文件由DocBook XML格式写成。其HTML版本和其他版本都自动生成，主要使用DocBook XSL样式表。关于DocBook的信息，详见 http://docbook.org/ 。

MySQL的源代码包括内部文档，由Doxygen编写。已完成的Doxygen内容可以访问 https://dev.mysql.com/doc/index-other.html 。同样也可以按照 [章节2.9.10，“创建MySQL Doxygen文档内容”](https://dev.mysql.com/doc/refman/8.0/en/source-installation-doxygen.html)的介绍从MySQL源分发创建本地的内容。

如果你有使用MySQL的的问题，加入 [MySQL开发者社区](https://mysqlcommunity.slack.com/)，或者在我们的论坛提问；详见 [章节1.6.2，“MySQL论坛的MySQL社区支持”](https://dev.mysql.com/doc/refman/8.0/en/forums.html)。如果你对本手册有补充或者更正的建议，请发送到 http://www.mysql.com/company/contact/。

本手册最初由 David Axmark 和 Michael “Monty” Widenius 编写。主要由MySQL文档团队编写，成员包括 Chris Cole， Paul DuBois， Margaret Fisher， Edward Gilmore， Stefan Hinz， David Moss， Philip Olson， Daniel Price， Daniel So， 以及 Jon Stephens 。



### 1.2  排版和语法约定

本手册使用下列的排版约定：

// TODO

### 1.3 MySQL数据库管理系统总览  

#### 1.3.1 什么是MySQL？ 

MySQL是最受欢迎的开源的SQL数据库管理系统，由Oracle公司开发，发布和支持。

[MySQL官网](http://www.mysql.com/)提供了MySQL软件的最新版本的信息。

- MySQL是一个数据库管理系统  

  数据库是数据的结构化集合。它可以是任何东西，从简单的购物清单，到一个图片集，或者公司网络中的海量信息。在一个计算机数据库上添加，访问和执行数据存储，你需要一个像MySQL服务器一样的数据库管理系统。由于计算机非常善于处理大量的数据，数据库管理系统就在计算机中扮演了一个中心角色，无论是作为一个独立的程序，还是别的应用的一部分。

- MySQL数据库是关系型的

  一个关系型数据库将数据存储在不同的表中，而不是把所有的数据放在一个大的存储库中。数据库结构组织成物理文件以优化速度。具有对象（诸如数据库，表，视图，行和列）的逻辑模型提供了灵活的程序设计环境。你可以建立规则去管理不同字段之间的关系，比如一对一，一对多，唯一，必填或者选填，以及不同表之间的指向。数据库强制执行这些规则，所以一个良好设计的数据库，可以使你的程序避免看到不一致，重复，孤立，过期，或者缺失的数据。

  MySQL中的SQL代表“结构化查询语言”。SQL是用于访问数据库的最通用的标准化语言。基于你的程序设计环境，又可以直接输入SQL（比如，用于创建报表），用另一种语言把SQL注入到代码中，或者使用隐含SQL语法的特定语言的API。

  SQL由 ANSI/ISO 的SQL标准来定义。SQL的标准从1986年以来不断发展，存在多个不同的版本。在本手册中，“SQL-92”是指1992年发布的标准，“SQL:1999”是指在1999年发布的版本，以及“SQL:2003”，指现在的版本。我们在任何时用到“SQL标准”这个词时，都是指当前版本的SQL标准。

- MySQL软件是开源的

  开源意味着对于任何人来说都可以使用和修改该软件。任何人都可以从网络上下载MySQL软件并且免费使用。你也可以按照你的意愿去学习和更改源代码以适应你的需求。MySQL软件使用[GPL]( http://www.fsf.org/licenses/)（通用公共许可），来定义你在不同情景下对软件能做的和不能做的事。如果你对GPL感到不适，或者想要将MySQL的代码注入到一个商用软件中，你可以从我们这里购买一份商用许可。访问[MySQL许可概览](http://www.mysql.com/company/legal/licensing/)以获取更多信息。

- MySQL数据库服务快速，可靠，可拓展且易用

  如果你寻求的就是这个，你应该去尝试一下。MySQL服务可以在笔记本或者台式机上与其他应用，网络服务一起舒适的运行，只需要一点甚至无需额外的关注。如果你将整台机器都用于MySQL，你可以调整设置利用所有的内存，CPU功率，以及IO性能。MySQL同样可以扩大网络和机器的集群。

  MySQL服务最初开发用来比现有方式更快的处理数据，而且成功的应用在高要求的生产环境很多年。尽管经历了不断的发展，MySQL如今提供了一组丰富而有用的功能。其可连接性，速度以及安全性使得MySQL服务高度适合在网络上访问数据库。

- MySQL服务运行于客户端/服务端或者嵌入式系统

  MySQL数据库软件是一个客户端/服务端系统，其包含了一个多线程的SQL服务器用于支持不同的后端，不同的客户端程序以及库，管理工具，以及大范围的应用程序接口。

  我们也提供MySQL服务作为一个嵌入的多线程库，你可以将其链接到你的应用以实现一个更小，更快，易于管理的独立产品。

- 大量捐赠的MySQL软件已经可用

  MySQL服务有一些使用的功能是由与我们密切合作的用户开发的。很有可能你最喜欢的应用或者语言由MySQL数据库服务支持。

“MySQL”的官方读法是“ My Ess Que Ell ”（而不是 “my sequel” ），但是我们不介意你把它读作“my sequel”或者别的当地的读法。

#### 1.3.2 MySQL的主要功能

本节描述了一些MySQL数据库软件的重要特征。在大部分情况下，该路线图适用于所有版本的MySQL。想要知道在特定版本的MySQL中引入的功能，请在“In a Nutshell”章节查看对应的手册：

- MySQL8.0：[章节1.4，“MySQL8.0更新了什么”](https://dev.mysql.com/doc/refman/8.0/en/mysql-nutshell.html)
- MySQL5.7：[MySQL5.7更新了什么](https://dev.mysql.com/doc/refman/5.7/en/mysql-nutshell.html)
- MySQL5.6：[MySQL5.6更新了什么](https://dev.mysql.com/doc/refman/5.6/en/mysql-nutshell.html)
- MySQL5.5：[MySQL5.5更新了什么](https://dev.mysql.com/doc/refman/5.5/en/mysql-nutshell.html)

**内部实现和可移植性**

- 由C和C++实现
- 通过了大规模的不同编译器的测试
- 在多个不同的平台上运行。详见https://www.mysql.com/support/supportedplatforms/database.html
- 关于移植，MySQL5.5及以上的版本使用CMake命令。更早的版本使用GNU Automake，Autoconf和Libtool。
- 经过了Purify（一个商用的内存泄漏探测器）以及Valgrind（一个GPL工具）的测试。（http://developer.kde.org/~sewardj/）
- 使用具有独立模块的多层服务的设计。
- 设计为使用核心线程的多线程方式，如果多核CPU可用，则可以很容易的被使用。
- 提供了基于事务和非事务的存储引擎。
- 使用索引压缩的非常快速的B-tree磁盘（MyISAM）
- 设计为可以相对简单的增加另一个存储引擎。如果你想为一个内部的数据库提供一个SQL接口，这会非常有用。
- 使用了非常快速的基于线程的内存分配系统。
- 使用了一种优化了的内部循环的join来非常快速的执行join。
- 实现了在内存的hash table，其作为临时表来使用。
- 使用了一种尽可能快的高度优化的类库来实现SQL功能。在所有的查询初始化后一般就不会再有内存分配。
- 为使用客户端/服务端环境提供了作为单独程序的服务，作为一个库可以嵌入到独立的应用中。这种应用可以单独使用或者在网络无法访问的环境中使用。

**数据类型**

- 很多的数据类型：带符号/无符号的整数类型，1、2、3、4以及8比特的长度， [`FLOAT`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html), [`DOUBLE`](https://dev.mysql.com/doc/refman/8.0/en/floating-point-types.html), [`CHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html), [`VARCHAR`](https://dev.mysql.com/doc/refman/8.0/en/char.html), [`BINARY`](https://dev.mysql.com/doc/refman/8.0/en/binary-varbinary.html), [`VARBINARY`](https://dev.mysql.com/doc/refman/8.0/en/binary-varbinary.html), [`TEXT`](https://dev.mysql.com/doc/refman/8.0/en/blob.html), [`BLOB`](https://dev.mysql.com/doc/refman/8.0/en/blob.html), [`DATE`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html), [`TIME`](https://dev.mysql.com/doc/refman/8.0/en/time.html), [`DATETIME`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html), [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html), [`YEAR`](https://dev.mysql.com/doc/refman/8.0/en/year.html), [`SET`](https://dev.mysql.com/doc/refman/8.0/en/set.html), [`ENUM`](https://dev.mysql.com/doc/refman/8.0/en/enum.html) ，以及OpenGIS的空间数据类型。详见[章节11，数据类型](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)。
- 固定长度和可变长度的字符串类型。

**语句和功能**

- 所有的操作符和功能支持都在Select列表和Where条件语句查询中。比如：

  ```sql
  mysql> SELECT CONCAT(first_name, ' ', last_name)
      -> FROM citizen
      -> WHERE income/dependents > 10000 AND age > 30;
  ```

- 完全支持SQL中的`Group By`和`Order By`条件。提供Group的功能的支持（ [`COUNT()`](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html#function_count), [`AVG()`](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html#function_avg), [`STD()`](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html#function_std), [`SUM()`](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html#function_sum), [`MAX()`](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html#function_max), [`MIN()`](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html#function_min), 和 [`GROUP_CONCAT()`](https://dev.mysql.com/doc/refman/8.0/en/group-by-functions.html#function_group-concat)) ）

- 完全支持拥有SQL和ODBC语法的 `LEFT OUTER JOIN` 和 `RIGHT OUTER JOIN`  

- 支持标准SQL要求的表和列的别名。

- 支持`Delete`，`Insert`，`Replace`，以及在`update`时返回修改的数量，或者返回匹配的行数，而不是在连接服务器时设置一个标识。

- 支持MySQL特定的`show`语句，用于返回数据库，存储引擎，表，以及索引的信息。支持 `INFORMATION_SCHEMA`数据库，实现自标准SQL。

-  [`EXPLAIN`](https://dev.mysql.com/doc/refman/8.0/en/explain.html) 语句会展现优化器如何处理查询语句的。

- 函数名称独立于表明和列名。比如，`ABS`是一个合法的列明。唯一的限制是在调用函数时，在函数名和(之间不能存在空格。详见[章节9.3，关键字和保留字](https://dev.mysql.com/doc/refman/8.0/en/keywords.html)。

- 你可以在一个语句内指定不同数据库的表。

**安全性**

- 权限和密码系统非常灵活和安全，并支持基于主机的认证。
- 连接服务器时，通过对所有密码通信进行加密来实现密码安全。

**可拓展性和限制**

- 支持大型数据库。我们使用的MySQL服务拥有可以包含5000万条数据的数据库。我们也知道拥有20万张表和50亿条数据的MySQL服务器的用户。
- 支持每张表最多64个索引。每个索引可以包含1-16列或者部分列。InnoDB表的索引最大宽度为767字节或者3072字节。详见 [章节15.6.1.16，“InnoDB表的限制”](https://dev.mysql.com/doc/refman/8.0/en/innodb-restrictions.html)。MyISAM表的最大索引宽度为1000字节。详见 [章节16.2，“MyISAM存储引擎”](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html)。可以使用Char，Varchar，Blob或者Text字段类型的前缀作为一个索引。

**可连接性**

- 客户端可以通过不同的协议连接MySQL服务：
  - 客户端可以在任何平台上使用TCP/IP端口进行连接。
  - 在windows系统，如果服务端启用了 [`named_pipe`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_named_pipe) 系统参数进行启动，那客户端就可以使用name pipe进行连接。如果服务端启用了 [`shared_memory`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_shared_memory)  系统参数进行启动，那客户端也可以使用  [`shared_memory`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_shared_memory)  方式进行连接。客户端可以通过使用  [`--protocol=memory`](https://dev.mysql.com/doc/refman/8.0/en/connection-options.html#option_general_protocol) 选项来进行shared memory的连接。
  - 在unix系统，客户端可以使用Unix域的套接字文件来进行连接。
- MySQL的客户端程序可以用不同的语言编写。一个C语言编写的客户端库可以用于C语言和C++编写的客户端，或者任何别的提供C语言绑定的语言。
-  C, C++, Eiffel, Java, Perl, PHP, Python, Ruby, 和 Tcl 的API都是可用的，使得MySQL客户端可以被多种语言编写。详见[章节28，连接器和API](https://dev.mysql.com/doc/refman/8.0/en/connectors-apis.html).
- 连接器/ODBC（MyODBC）接口为那些使用ODBC（Open Datebase Connectivity）连接的客户端程序提供了MySQL支持。比如说，你可以使用MS Access去连接你的MySQL服务器。客户端可以运行在Windows或者Unix。连接器/ODBC源是可用的。和很多其他的功能一样，所有的ODBC2.5的功能都是被支持的。详见[MySQL 连接器/ODBC开发者指南](https://dev.mysql.com/doc/connector-odbc/en/)。
- 连接器/J 接口为那些使用了JDBC连接器的java客户端程序提供了MySQL支持。客户端可以运行于Windows或者Unix。连接器/J 源是可用的。详见[MySQL连接器/J 5.1开发者指南](https://dev.mysql.com/doc/connector-j/5.1/en/)。
- MySQL连接器/NET 允许开发者创建一个要求安全和高性能数据连接MySQL的.NET应用程序。它实现了必须的ADO.NET的接口并融入进了ADO.NET的感知工具。开发者可以选择他们的.NET语言去构建应用。MySQL连接器/NET是一个完全托管的ADO.NET驱动，100%由纯粹的C#编写。详见[MySQL连接器/NET开发者指南](https://dev.mysql.com/doc/connector-net/en/)。

**本地化**

- 服务器可以提供各种语言的报错信息。详见[章节10.12，设置错误提示的语言](https://dev.mysql.com/doc/refman/8.0/en/error-message-language.html)。
- 完全支持不同的字符集，包括 `latin1` (cp1252)，`german`，`big5`，`ujis` ，几种Unicode编码，以及别的。比如， Scandinavian 的字符 “`å`”, “`ä`” 和“`ö`” 可以用在表和列的名字中。
- 所有的数据以选定的字符集存储。
- 根据默认的字符集和校对规则进行排序和比较。当MySQL服务运行的时候也可以对该规则进行更改(详见[章节10.3.2，“服务器字符集和校验”](https://dev.mysql.com/doc/refman/8.0/en/charset-server.html))。一个非常高级的排序例子，可以看下捷克的排序编码。MySQL支持多种可以在编译和运行阶段指定的字符集。
- 服务器时区可以动态的改变，而且单独的客户端可以指定各自的时区。详见[章节5.1.13，MySQL服务器时区支持](https://dev.mysql.com/doc/refman/8.0/en/time-zone-support.html)。

**客户端和工具**

- MySQL包含多个客户端和实用程序。包括命令行程序，比如 [**mysqldump**](https://dev.mysql.com/doc/refman/8.0/en/mysqldump.html) 和 [**mysqladmin**](https://dev.mysql.com/doc/refman/8.0/en/mysqladmin.html) ，以及图形化程序 [MySQL Workbench](https://dev.mysql.com/doc/refman/8.0/en/workbench.html) 。
- MySQL服务器内置了对于SQL语句的检查，优化和表修复的支持。这些语句可以通过[mysqlcheck](https://dev.mysql.com/doc/refman/8.0/en/mysqlcheck.html)客户端从命令行使用。MySQL也包含了[myisamchk](https://dev.mysql.com/doc/refman/8.0/en/myisamchk.html)工具，一个非常快速的命令行工具，用于在MyISAM表提交这些操作。详见[章节4，MySQL程序](https://dev.mysql.com/doc/refman/8.0/en/programs.html)。
- 可以调用 --help 和 -?来呼叫MySQL程序用以获取在线协助。