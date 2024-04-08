---
title: 12例bash脚本最佳实践
date: 2022-01-18 10:18:17
tags: 
- linux
- translate
comments: true
categories: 
- 学习
---

**声明**

翻译自[12 Best Practices for Writing Bash Scripts](https://www.faqforge.com/linux/12-best-practices-for-writing-bash-scripts/)
翻译完发现这篇文章技术价值相当低。。。

---

Bash shell全称Bourne Again Shell，现在作为大部分的linux发行版本的默认shell。一个Bash脚本是一个包含多行shell命令的文件，用于执行特别的命令。如果你熟知bash脚本，那这篇文章就很适合你，在这篇文章中我写了12种bash脚本的最佳实践，可以增加bash脚本的效率并使其更易读。

## 注解

注解是一段清晰代码最基础的部分，用于定义或解释复杂代码的不同部分。写脚本的时候，你可以毫不费力的看懂很早之前你写过的代码。同样也能帮助你在一个大的项目组提高工作效率，帮助你理解一个函数或方法的真实用途。

## 使用函数

一个函数是一堆命令联合之后实现一个特定的任务，可以帮助工作流模块化并使得脚本可复用，消除重复的代码。这可以让你维护的代码更加清晰和可读。

```bash
#!/bin/bash

function check_root() {

  echo "function has been called";

}
```

## 对引用变量使用双引号

使用双引号可以消除不必要的通配符，同时当引用变量包含分割字符和空格时可以将包含空格的单词进行分割。如下例子中，我们可以看出变量用不用双引号的区别。（译者：没有例子，作者估计漏了）

## 出现错误时终止脚本

有时候执行脚本的时候可能会出现一些错误。然而即使一个命令失败了，剩余的脚本也有可能继续运行，从而影响脚本中的其他命令。所以为了避免任何进一步的逻辑错误，我们需要加入`set -o errexit`或`set -e`终止错误的命令。

```bash
#!/bin/bash

# Terminate the script on command fails

set -o errexit
```
<!-- more -->

## 使用未声明变量时终止脚本

脚本执行时，如果存在未声明的变量，可能导致bash使用变量时出现逻辑错误。当bash中存在一个未声明变量时，我们可以用如下命令中止脚本：

```bash
#!/bin/bash

# Terminate the script on command fails

set -o nounset
```

## 声明变量

我们必须对一个变量的数据类型和用法进行声明。当变量未声明，bash可能会因此执行失败。变量可以被声明为全局或在脚本内的本地变量。

```bash
#!/bin/bash

# variable declaration

declare -r -i x=30

function my_variable(){

  local -r name = ${HOME}

}
```

## 使用花括号

当使用变量关联字符串时，可以使用花括号将变量圈起来，以避免不必要的变量使用。同样也可用于对字符串的简单定义。比如：

```bash
#!/bin/bash

# Terminate the script on command fails

set -o errexit

# Custom variable

data= "${USER}_data is being used"
```

## 命令替换

当分配命令的输出到变量时，bash使用命令替换的特性。我们需要使用推荐的$()而不是反引号去分配输出到变量。

```bash
#!/bin/bash

# Terminate the script on command fails

set -o errexit

#Displaying Date

date_now = ${date}
```

## 变量命名规范

在我们的系统中，所有的环境变量都用大写字母。所以当我们声明一个本地变量时，需要使用小写字母以避免环境变量和本地变量名之间的冲突。

```bash
#!/bin/bash

# Terminate the script on command fails

set -o errexit

user_var = "$HOME is your system's current login user."
```

## 声明静态变量

如果你拥有一个在整个脚本中都不变的静态数据，你可以将值分配到一个不能被修改的静态变量上。你可以使用`readonly`命令来分配静态变量。

```bash
#!/bin/bash

# This is nginx test host config

readonly test_conf_path = "/etc/nginx/conf.d/test.conf"
```

## 比较字符串

在多数场景下字符串用`==`进行比较，但是在bash脚本中，字符串用`=`就可以完成比较。在如下例子中，我用`=`比较两个字符串。

```bash
#!/bin/bash

# comparing two strings

If [ "$HOME" = "User1" ]; then

  echo "This is test purpose".

fi
```

## 脚本调试

调试是鉴定问题最重要的部分。我们需要在运行脚本的时候加上`-n`命令来检查脚本语法错误。

```bash
bash -n script_name
```

同样的，我们可以使用如下命令打开脚本的debug模式。

```bash
bash -x script_name
```

## 总结

这样，就是12个可以提升你脚本能力的bash脚本实践。当使用命令去完成特殊任务时，bash脚本将是更出色的那个。

---

refers to 指的是；全称
demonstration （强烈表达）集会示威;游行示威;示范;示范表演;演示;证明;证实;论证;说明;表达
effortless 不费力的
modularizing 模块化
globbing 通配符
splitting 分割
curly braces 大括号;花括号;大括弧;花括號
concatenation 串联
substitution 置换;取代;代换;代入
assigning 分配
syntax 语法
