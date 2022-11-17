+++
title = "Bash 入门教程"
description = "初学者的 Bash 脚本入门教程"
date = 2021-11-17T14:08:08+00:00
[taxonomies]
tags = ["Bash","Tool","Tutorial"]
categories = ["Tool"]
[extra]
author = "hi2code"
+++
Bash 是大部分 Linux 发行版上的默认命令行环境（Shell）。它的语法很简单，可以很方便的操作类 Linux 系统上的原生命令，适合备份、快速部署等自动化工作。这是一篇 Bash 快速入门教程。

<!-- more -->
# Bash 快速入门 

Bash 是大部分 Linux 发行版上的默认命令行环境（Shell），通常所说的命令行命令就是在 Bash 中执行的。

# 准备工作

安装 Bash 软件：

- macOS

  无需安装，自带 Bash。「终端」程序即可执行 Bash 脚本。

- Window

  [下载 Git 客户端](https://git-scm.com/download/win)，安装 Git 客户端的同时会安装一个 Bash 程序，安装后在开始菜单中找到 Bash 程序。

- Linux

  无需安装，自带 Bash。如果是图像界面，「终端」程序执行 Bash 脚本；命令行界面直接进入的就是 Bash 程序。

# 注释
在 Bash 中`#`表示注释，`#`号之后的字符串都会被忽略
# 第一个脚本

我们先打开 Bash，在命令行中执行一个脚本展示根路径下的文件

```bash
$ ls /  #展示根路径下的文件。bash 脚本使用#号表示注释，#号后的字符会被忽略
bin   data  etc   opt
```

> 说明：示例代码中，每一行最开头的`$`开头表示当前执行脚本的用户为非 root 账户。 `#`开头表示执行脚本的用户为 root 用户。
>
> **复制到自己的命令行中执行时，只需要复制`$`符号之后的部分。**

`ls`是 Linux 中的一个命令（即一个程序），用来列出（list）路径下的文件

# 执行过程

理解 Bash 脚本最重要的一点就是，**每行脚本都是先拓展，再执行**。

拓展可以简单的理解为替换。我们把上面命令改为展示用户 home 路径下的文件：

```bash
$ ls ~
```

执行过程为：

1. 先拓展：假如用户名为 bob，`~` 会被替换为`/home/bob`。
2. 再执行：`ls /home/bob`

这种叫波浪号拓展，还有很多其他类型的拓展后续我们将陆续讲解。

# 变量拓展

Bash 中变量有两种，环境变量和自定义变量。

> 我们可以用 echo 命令打印变量值，类似于其他语言中的 print 函数。

**环境变量：**

  环境变量是启动 Bash 时就已经定义好的变量。可以直接使用。

  在变量名前加 `$` 符号，表示引用变量。

  ```bash
  $ echo $HOME #HOME 变量表示用户的家路径
  /home/bob
  ```

  执行过程是通过变量拓展（Parameter Expansion）把`$HOME`拓展为`/home/bob`，再执行。

**自定义变量：**

  下例定义一个变量 a，并将 a 的值打印出来。

  ```bash
  $ a=1 #定义一个变量 a。注意等号两侧不能有空格
  $ echo $a #打印 a 变量的值
  1
  ```

# 子命令拓展

我们可以在一行命令中，先执行一个子命令，再执行这个命令。格式为`$(sub command)`。

如下命令，先执行 `ls ~` ，然后再执行 echo 打印子命令返回结果。

```bash
$ echo $(ls ~)
```

# 循环语句

主要用到两种循环，while 循环和 for 循环。while 循环涉及到条件的判断，我们后续学习了条件再讲。我们先讲 for 循环迭代，for 循环是从「元素列表」中依次取「元素」。

> 从这里开始就开始涉及多行的命令脚本。对于多行脚本，在交互式命令行中，回车后提示 `>` 可以继续编辑，而不是直接执行。

```bash
files=$(ls ~)      # 子命令拓展，并赋值给 files 变量
for file in $files # 从变量 files 中轮流取出每个文件，赋值给变量 file。
do                 # do 表示循环体开始
  echo $file        # 每次循环需要执行的语句
done               # done 表示循环体结束
```

# 条件语句

Bash 中的条件语句，和其他编程语言差异比较大。它的条件语句需要用 test 命令进行判断。

条件语句的格式如下：

```bash
if test-commands; then
  consequent-commands;
[elif more-test-commands; then #elif 和 else 分支都是可选的
  more-consequents;]
[else alternate-consequents;]
fi
```

if 后跟着条件语句（test-commands），根据条件语句的真假执行不同的条件分支。如下例所示，test 有很多种用法，详细用法可以查看 [这里](https://www.runoob.com/linux/linux-shell-test.html)。

```bash
if test 1 -ne 2;then #判断 1 不等于（not equal） 2，这里判断为真，执行 if 分支
  echo 条件为真
else
  echo 条件为假
fi
```

还有一个常用的写法是，判断命令是否执行成功，如下下例所示。

```bash
echo 执行某行命令 #我们执行某行命令后，需要在脚本中判断是否执行成功
if test $? -eq 0; then 
# $? 为上一条命令的返回值，-eq 0 判断上条命令是否返回值等于 （equal）0。命令返回 0 表示成功，返回 1 表示失败
	echo 命令执行失败
fi
```

test 命令还有其他两种写法，但是本质上都是调用 test 命令进行了判断，`[`是对 test 的简化，所以`[`需要紧接着一个空格，否则会报错。
```bash
# 单方括号写法
if [ 1 -ne 2 ];then
fi

# 双方括号写法
if [[ 1 -ne 2 ]];then
fi
```
# 参考资料
- [Bash Reference Manual](https://www.gnu.org/software/bash/manual/bash.html)
- [Bash Shortcuts Cheat Sheet](https://www.30secondsofcode.org/articles/s/terminal-shortcuts-cheatsheet)
- [Bash 脚本教程](https://wangdoc.com/bash)