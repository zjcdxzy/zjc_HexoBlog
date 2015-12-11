title: shell 中-bash、、0、 之间的区别
date: 2015-12-01 18:48:34
categories: Shell
tags:
---
##shell 中$0、$*、$#、$@ 之间的区别


*脚本示例*


``` bash

#!/bin/bash
MIN_PARAMS=10
echo
echo "The name of this script is \"`basename $0`\"."

echo
if [ -n "$1" ]; then
echo "param #1 is $1"
fi
if [ -n "$2" ]; then
echo "param #2 is $2"
fi
if [ -n "$3" ]; then
echo "param #3 is $3"
fi

echo "______________________________"
echo "All command-line params are : $*"
echo '$0 =' "$0"
echo '$# =' "$#"
echo '$@ =' "$@"
echo '$* =' "$*"

if [ $# -lt $MIN_PARAMS ]
then
echo
echo "this script need at least $MIN_PARAMS"
fi
exit 0
```

1. 终端直接执行 sh build-Test.sh 


	```bash
	The name of this script is "build-Test.sh".
	
	______________________________
	All command-line params are : 
	$0 = build-Test.sh
	$# = 0
	$@ =
	$* = 
	
	this script need at least 1
	```
	
	- $0直接，打印出脚本的名字`build-Test.sh`
	- 判断没有进入
	- $#的结果为0
	- $@、$*的结果为空
	
2. 终端直接执行 sh build-Test.sh 1
	
	```bash
	The name of this script is "build-Test.sh".

	param #1 is 1
	______________________________
	All command-line params are : 1
	$0 = build-Test.sh
	$# = 1
	$@ = 1
	$* = 1
	
	this script need at least 10
	```
	- 判断已经执行
	- $# = 1，$@ = 1，$* = 1
	
3. 终端直接执行 sh build-Test.sh 1 2

	```bash
	The name of this script is "build-Test.sh".
	
	param #1 is 1
	param #2 is 2
	______________________________
	All command-line params are : 1 2
	$0 = build-Test.sh
	$# = 2
	$@ = 1 2
	$* = 1 2
	
	this script need at least 10
	```
	- 判断已经执行
	- $# = 2，$@ = 1 2，$* = 1 2
	
	
4. 终端直接执行 sh build-Test.sh 1 2 3 4 5 6 7 8 9 10 

	```bash
	The name of this script is "build-Test.sh".

	param #1 is 1
	param #2 is 2
	param #3 is 3
	______________________________
	All command-line params are : 1 2 3 4 5 6 7 8 9 10
	$0 = build-Test.sh
	$# = 10
	$@ = 1 2 3 4 5 6 7 8 9 10
	$* = 1 2 3 4 5 6 7 8 9 10
	```
	- $0 可用来取出脚本的名字。
	- $# 取出脚本的最后的一个参数
	- $@和@* 可用来枚举出脚本的参数值 
	- `if [ -n "%1" ]` 如果%1的长度非零则返回为真，即非空是真
	- `[ INT1 -lt INT2 ]` INT1小于INT2返回为真 ,<






