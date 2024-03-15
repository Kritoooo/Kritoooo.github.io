---
title: Linux基础入门（四）
tags: Linux


---

# Makefile

## 规则

Makefile的框架由规则构成。make命令执行时先在Makefile文件中查找各种规则，对各种规则进行解析后运行规则。

```makefile
#每条规则的语法格式：
target1,target2...: depend1, depend2, ...
			command
			......
			......
```

每条规则由三个部分组成，分别是目标(target)，依赖(depend)和命令(command)。

- 命令(command)：当前这条规则的动作，一般情况下是一个shell命令。
  - 例如：通过某个命令编译文件，生成库文件，进入目录等。
  - 动作可以是多个，每个命令前必须有一个tab并且独占一行
- 依赖(depend)：规则所必需的依赖条件，在规则的命令中可以使用这些依赖。
  - 可以为空，可以是其他规则中的某个目标，可以指定多个。
- 目标(target)：规则中的目标，这个目标和规则中的命令是对应的
  - 通过执行命令，可以生成一个和目标同名的文件。
  - 规则中可以有多个命令，因此可以有多个目标。

```makefile
#单个目标，单个命令
app:a.c b.c c.c
	gcc a.c b.c c.c -o app
	
#多个目标，多个命令
app,app1:a.c b.c c.c d.c
	gcc a.c b.c -o app
	gcc c.c d.c -o app1

#规则之间的嵌套
app:a.o b.o c.o
	gcc a.o b.o c.o -o app
a.o:a.c
	gcc -c a.c
b.o:b.c
	gcc -c b.c
c.o:c.c
	gcc -c c.c
```

## 工作原理

### 文件时间戳

make命令执行时会根据文件的时间戳判定是否执行makefile文件中相关规则的命令。

- 目标是通过依赖生成，所以正常情况下：目标时间戳>所有依赖的时间戳，如果执行make命令的时候检测到规则中的目标和依赖满足这个条件，那么规则中的命令就不被执行。
- 当依赖文件被更新时，目标时间戳<某些依赖的时间戳，在这种情况下目标文件会被重新生成。
- 如果目标文件不存在，规则中的命令看到会被执行。

### 自动推导

make有自动推导的能力，不会完全依赖makefile。

makefile内容：

![image-20240309205941666](/pic/image-20240309205941666.png)

![image-20240309210136746](/pic/image-20240309210136746.png)

![image-20240309210002712](/pic/image-20240309210002712.png)

前五条命令为make自动推导的结果。

# GBD

## 调试准备

生成可用于调试的可执行文件

```bash
gcc test.c -g -Wall -O0 -o app
```

 使用gdb 进行调试

```bash
gdb app
```

设置命令行参数

![image-20240310124549878](/pic/image-20240310124549878.png)

```bash
# 执行到第一个断点，如果没有设置断点，程序直接执行完
run
# 启动程序，最终会阻塞在main函数第一行
start
# 退出gdb
quit/q
```

run命令，没有断点则直接执行完程序。

![image-20240310124737571](/pic/image-20240310124737571.png)

## 查看代码

当前文件

```bash
# 从第一行开始显示
(gdb) list 

# 列值这行号对应的上下文代码, 默认情况下只显示10行内容
(gdb) list 行号

# 显示这个函数的上下文内容, 默认显示10行
(gdb) list 函数名
```

切换文件

```bash
l 文件名:行号

l 文件名:函数名
```

设置显示的行数

```bash
# 以下两个命令中的 listsize 都可以写成 list
set listsize 行数

# 查看当前list一次显示的行数
show listsize
```

## 断点操作

### 设置断点

断点的设置有两种方式，一种是常规断点，程序只要运行到这个位置就会被阻塞；还有一种叫条件断点，只有指定的条件被满足了程序才会在断点处阻塞。

设置普通断点到当前文件

```bash
# 在当前文件的某一行上设置断点
# break = b
b 行号
b 函数名
```

设置普通断点到某个非当前文件上

```bash
# 在非当前文件的某一行上设置断点
b 文件名:行号
b 文件名:函数名
```

设置条件断点

```bash
b  行数 if 变量名==某个值
```

### 查看断点

```bash
info breakpoint
i b
```

![image-20240310141015418](/pic/image-20240310141015418.png)

### 删除断点

```bash
# delete == del == d
d 断点的编号1 [断点编号2 ...]
d 1
d 2 4 6
d 1-5
```

![image-20240310141215534](/pic/image-20240310141215534.png)

### 设置断点状态

```
#设置断点无效
# disable == dis
#设置某一个或者某几个断点无效
dis 断点1的编号
dis 1-5
#生效enable = ena
```

![image-20240310141418525](/pic/image-20240310141418525.png)

编号6的断点Ebh变为n

![image-20240310141454631](/pic/image-20240310141454631.png)

## 调试命令

继续运行gdb

```bash
# continue = c
continue
```

 打印变量值

```
# print = p
p 变量名
```

![image-20240310144055829](/pic/image-20240310144055829.png)

打印变量类型

```bash
ptype 变量名
```

![image-20240310144313726](/pic/image-20240310144313726.png)

自动打印信息

```bash
display 变量名
```

![image-20240310144547924](/pic/image-20240310144547924.png)



```bash
#删除自动显示
delete display num
#停用
disable display num
#启用
enable display num
```

![image-20240310144716007](/pic/image-20240310144716007.png)

## 单步调试

执行下一行，如果是函数调用，则会进入到函数体内部。

```bash
# step == s
step
```

![image-20240310154958050](/pic/image-20240310154958050.png)

跳出函数体

```bash
finish
```

![image-20240310155040279](/pic/image-20240310155040279.png)

执行下一行，不会进入函数体内部

```bash
#next == n
next
```

![image-20240310155100436](/pic/image-20240310155100436.png)

跳出循环体。循环体内无有效断点。

```bash
until
```

![image-20240310155922854](/pic/image-20240310155922854.png)

