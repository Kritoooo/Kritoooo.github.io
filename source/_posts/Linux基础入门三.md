---
title: Linux基础入门（三）
tags: Linux


---

# Linux基础入门（三）

## GCC

GCC是Linux下的编译工具集，是GNU Compiler Collection的缩写，包含gcc、g++等编译器，这个工具集不仅包含编译器，还包含其他工具集，例如ar、nm等。

### 安装GCC

```bash
ubuntu
sudo apt update
sudo apt install gcc g++

centos
sudo yum update
sudo yum install gcc g ++

查看gcc版本
gcc -v

查看g++版本
g++ -v
```

![image-20240307124527126](/pic/image-20240307124527126.png)

### gcc工作流程

GCC编译器对程序的编译分为四个阶段：预处理（预编译）、编译和优化、汇编和链接。GCC编译器可以将这四个步骤合并成一个。

1.预处理：GCC调用预处理器来完成；展开头文件、宏替换、去掉注释行。

2.编译：GCC调用编译器对文件进行编译，最终得到一个汇编文件。

3.汇编：GCC调用汇编器对文件进行汇编，最终得到一个二进制文件。

4.链接：GCC调用链接器对程序需要调用的库进行链接，最终得到一个可执行的二进制文件。

| 文件名后缀 | 说明                           | gcc参数 |
| ---------- | ------------------------------ | ------- |
| .c         | 源文件                         | 无      |
| .i         | 预处理后的C文件                | -E      |
| .s         | 编译之后得到的汇编语言的源文件 | -S      |
| .o         | 汇编后得到的二进制文件         | -c      |

![image-20240307125904722](/pic/image-20240307125904722.png)

test.c

![image-20240307130209517](/pic/image-20240307130209517.png)

test.i

![image-20240307130150335](/pic/image-20240307130150335.png)

test.s

![image-20240307130412927](/pic/image-20240307130412927.png)

test.o

![image-20240307130516326](/pic/image-20240307130516326.png)

最后运行

![image-20240307130552101](/pic/image-20240307130552101.png)

我们也可以直接gcc test.c -o test。

### gcc常用参数

#### 指定生成的文件名(-o)

```bash
gcc test.c -o 生成文件名
```

#### 搜索头文件(-l)

指定要引用的头文件路径。

```bash
gcc *.c -I include
```

#### 指定一个宏(-D)

```bash
gcc 源文件 -D 宏定义
```

![image-20240307161527012](/pic/image-20240307161527012.png)

gcc test.c

![image-20240307161611531](/pic/image-20240307161611531.png)

![image-20240307161715163](/pic/image-20240307161715163.png)

### gcc和g++

gcc无法自动链接到c++标准库，需要指定参数。

```bash
gcc a.cpp -l stdc++
```

## 静态库和动态库

### 静态库

在Linux中静态库由程序ar生成。

- 在Linux中静态库以lib作为前缀，以.a作为后缀，中间是库的名字，即：libxxx.a
- 在Windows中静态库一般以lib作为前缀，以lib作为后缀，中间是库的名字，即：libxxx.lib

#### 生成静态库

生成静态库，需要先对源文件进行汇编操作得到二进制格式的目标文件，然后再通过ar工具将目标文件打包就可以得到静态库文件了。

- 参数c：创建一个库。
- 参数s：创建目标文件索引，在创建较大的库时能加快时间。
- 参数r：在库中插入模块（替换）。

生成静态链接库的具体步骤如下：

1.需要将源文件进行汇编，得到.o文件。

```bash
# 执行如下操作，默认生成二进制的.o文件
gcc 源文件(*.c) -c 
```

 2.将得到的.o进行打包，得到静态库

```bash
ar rcs 静态库的名字 原材料(*.o)
```

sub.c

```c
#include <stdio.h>
#include "head.h"

int subtract(int a, int b)
{
        return a-b;
}
```

head.h

```c
#ifndef _HEAD_H
#define _HEAD_H

int add(int a, int b);
int subtract(int a, int b);
int multiply(int a, int b);
double divide(int a, int b);
#endif
```

main.c

```c
#include <stdio.h>
#include "head.h"
int main()
{
        int a = 20;
        int b = 12;
        printf("a = %d, b = %d\n", a, b);
        printf("a + b = %d\n", add(a, b));
        printf("a - b = %d\n", subtract(a, b));
        printf("a * b = %d\n", multiply(a, b));
        printf("a / b = %f\n", divide(a, b));
        return 0;
}
```



![image-20240309150724507](/pic/image-20240309150724507.png)

![image-20240309151809802](/pic/image-20240309151809802.png)

![image-20240309151904844](/pic/image-20240309151904844.png)

![image-20240309152344807](/pic/image-20240309152344807.png)

### 动态库

动态链接库是程序运行时加载的库，当动态链接库正确部署之后，运行的多个程序可以使用同一个加载到内存中的动态库，因此在Linux中动态链接库也可以称之为共享库。

- 在Linux中动态库以lib作为前缀，以.so作为后缀，中间是库的名字，例如：libxxx.so
- 在Windows中动态库一般以lib作为前缀，以dll作为后缀，中间是库的名字，例如：libxxx.dll	 

#### 生成动态库

直接使用gcc命令并且需要添加-fPIC（-fpic）以及-shared参数。

- -fPIC或-fpic参数的作用是使得gcc生成的代码是与位置无关的，也就是使用相对位置。
- -shared参数是告诉编译器生成一个动态链接库

生成动态链接库的具体步骤如下：

1.将源文件进行汇编操作、需要使用参数-c，还需要添加额外此参数 -fpic/-fPIC

```bash
得到若干个 .o文件
gcc 源文件(*.c) -c -fpic
```

2.将得到的.o文件打包成动态库，还是使用gcc，使用参数 -shared 指定生成动态库

```bash
gcc -shared 与位置无关的目标文件(*.o) -o 动态库(libxxx.so)
```

3.发布动态库和头文件

```
1.提供头文件：xxx.h
2.提供动态库：libxxx.so
```

![image-20240309152830627](/pic/image-20240309152830627.png)

![image-20240309152810286](/pic/image-20240309152810286.png)

![image-20240309153126857](/pic/image-20240309153126857.png)

![image-20240309153111078](/pic/image-20240309153111078.png)

#### 解决动态库无法加载问题

##### 库的工作原理

- 静态库加载

  静态库会被打包到可执行程序中，当可执行程序被执行，静态库中的代码也会一并被加载到内存中，因此不会出现静态库找不到无法被加载的问题。

- 动态库加载

  gcc命令中虽然指定了库路径（-L），但是这个路径并没有记录到可执行程序中，只是检查了这个路径下的库文件是否存在。

  动态库文件没有被打包到可执行程序中，只是在可执行程序中记录了库的名字。

可执行程序被执行后，会先检测需要的动态库是否可以被加载，加载不到就会提示错误信息。

当动态库中的函数在程序中被调用了，这时候动态库才加载到内存，如果不被调用就不加载。

动态库的检测和内存加载操作都是由动态连接器来完成的。

##### 动态链接器

动态链接器是一个独立的进程，当用户的程序需要加载动态库时，动态链接器开始工作。

动态链接器的搜索顺序：

1.可执行文件内部的DT_RPATH段

2.系统的环境变量LD_LIBRARY_PATH

3.系统动态库的缓存文件/etc/ld.so.cache

4.存储动态库 / 静态库的系统目录 /lib/，/usr/lib等

如果按照上面四个顺序搜索，最后还是没找到，动态链接器就会提示错误信息。

```bash
ldd 可执行文件 #检查可执行文件所需的动态库能否被正确加载
```

##### 解决方案

方案1：将库路径添加到环境变量LD_LIBRARY_PATH中

1.找到配置文件

```bash
~/.bashrc 对当前用户有效
/etc/profile 对所有用户有效
```

2.使用vim打开配置文件，在文件最后添加

``` bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:动态库的绝对路径
```

3.配置文件生效

```bash
source ~/.bashrc          (. ~/.bashrc)
source /etc/profile       (. /etc/profile)
```

![image-20240309154359422](/pic/image-20240309154359422.png)

![image-20240309154412551](/pic/image-20240309154412551.png)

方案2: 更新 /etc/ld.so.cache 文件

1. 找到动态库所在的绝对路径 比如：`/home/krito/Library/`

2. 修改 `/etc/ld.so.conf`，将路径添加到该文件末尾。

   ```bash
   sudo vim /etc/ld.so.conf
   ```

3. 更新

   ```bash
   sudo ldconfig
   ```

方案3: 拷贝动态库文件到系统库目录 `/lib/` 或者 `/usr/lib` 中

```bash
# 库拷贝
sudo cp /xxx/xxx/libxxx.so /usr/lib

# 创建软连接
sudo ln -s /xxx/xxx/libxxx.so /usr/lib/libxxx.so
```

![image-20240309154814930](/pic/image-20240309154814930.png)

### 比较

静态库优点：

- 静态库被打包到应用程序中，加载速度更快
- 发布程序无需提供静态库，移植方便

静态库缺点：

- 相同的库文件可能会在内存中加载多份，消耗系统资源，浪费内存。
- 库文件更新需要重新编译项目文件，生成新的可执行程序，浪费时间。

动态库优点：

- 可实现不同进程间的资源共享
- 动态库升级简单，只需要替换库文件，无需重新编译应用程序
- 可控制何时加载

动态库缺点：

- 加载速度比静态库慢
- 发布程序需要提供依赖的动态库
