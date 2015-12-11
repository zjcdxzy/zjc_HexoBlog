title: Shell单引号和双引号的区别
date: 2015-12-01 18:46:36
categories: Shell
tags: 
---


###一、 单引号和双引号的区别

#### 定义一个变量
	
	
``` bash
localhost:~ zjc$ name = zjc
-bash: name: command not found
localhost:~ zjc$ name =zjc
-bash: name: command not found
localhost:~ zjc$ name= zjc
-bash: zjc: command not found
localhost:~ zjc$ name=zjc
localhost:~ zjc$ echo $name
zjc
localhost:~ zjc$ 
```

- shell 基本语法，等号两侧不能有空格.

- 使用$符号可以访问变量。

#### 引号的引用

```bash
localhost:~ zjc$ name=zjc
localhost:~ zjc$ echo $name
zjc
localhost:~ zjc$ sayhello="Hello $name"
localhost:~ zjc$ echo $sayhello
Hello zjc
localhost:~ zjc$ sayhello='Hello $name'
localhost:~ zjc$ echo $sayhello
Hello $name
localhost:~ zjc$ 
```

- 获取变量值的时候使用双引号""。
- 单引号和双引号的最大的不同在于双引号仍然可以保有变量的内容，但是单引号时能是一般的字符，不能是特殊的符号。




  