---
title: Shell特殊变量：Shell $0, $#, $*, $@, $?, $$和命令行参数
date: 2019-09-14 00:08:55
tags:
---
# Shell特殊变量：Shell $0, $#, $*, $@, $?, $$和命令行参数
变量名只能包含数字、字母和下划线，因为某些包含其他字符的变量有特殊含义，这样的变量被称为特殊变量。

## 特殊变量列表
|变量	|含义|
|---|---|
|$0|	当前脚本的文件名|
|$n	|传递给脚本或函数的参数。n 是一个数字，表示第几个参数。例如，第一个参数是$1，第二个参数是$2。|
|$#	|传递给脚本或函数的参数个数。|
|$*|	传递给脚本或函数的所有参数。|
|$@	|传递给脚本或函数的所有参数。被双引号(" ")包含时，与 $* 稍有不同，下面将会讲到。|
|$?|	上个命令的退出状态，或函数的返回值。|
|$$	|当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。|

## 命令行参数
运行脚本时传递给脚本的参数称为命令行参数。命令行参数用 $n 表示，例如，$1 表示第一个参数，$2 表示第二个参数，依次类推。

## $* 和 $@ 的区别
`$*` 和 `$@` 都表示传递给函数或脚本的所有参数，不被双引号(" ")包含时，都以`"$1"`` "$2"` … `"$n"` 的形式输出所有参数。

但是当它们被双引号`(" ")`包含时，`"$*"` 会将所有的参数作为一个整体，以`"$1 $2 … $n"`的形式输出所有参数；`"$@"` 会将各个参数分开，以`"$1" "$2" … "$n" `的形式输出所有参数

## 退出状态
`$?` 可以获取上一个命令的退出状态。所谓退出状态，就是上一个命令执行后的返回结果。

退出状态是一个数字，一般情况下，大部分命令执行成功会返回 0，失败返回 1。
`$?` 也可以表示函数的返回值