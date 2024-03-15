---
title: Linux基础入门（二）
tags:
- Linux

---

# Linux基础入门（二）

## 压缩命令

### tar

​	tar是打包工具，不能压缩文件。gzip和bzip2，不能打包压缩文件，每个文件都会生成一个单独的压缩包，并且压缩之后不会保留原文件。

​	我们可以将两者结合，先使用tar进行打包，然后使用gzip和bzip2进行压缩。

#### 压缩(.tar.gz .tar.bz2 .tgz)

```bash
tar 参数 生成压缩包的名字 要压缩的文件或目录（中间用空格隔开）
# 使用gzip，标准后缀格式为：.tar.gz
# 使用bzip2，标准后缀格式为：.tar.bz2
```

- c：创建压缩文件
- z：使用gzip的方式进行文件压缩
- j：使用bzip2的方式进行文件压缩
- v：压缩过程中显示压缩信息
- f：指定压缩包的名字

> 一般认为.tgz文件就等同于.tar.gz 文件。

将config文件夹与b.txt文件以gzip的方式进行压缩，得到a.tar.gz这个压缩包。

![image-20240302203635419](/pic/image-20240302203635419.png)

#### 解压缩(.tar.gz .tar.bz2 .tgz)

```
#解压到当前目前
tar 参数 压缩包名

#解压到指定目录中
tar 参数 压缩包名 -C 解压目录
```

- x：释放压缩文件内容
- z：使用gzip的方式进行文件压缩，压缩包后缀为.tar.gz
- j：使用bzip2的方式进行文件压缩，压缩包后缀为.tar.bz2
- v：解压缩过程中显示信息
- f：指定压缩包的名字

### zip

```bash
#ubuntu
sudo apt install zip
sudo apt install unzip
#CentOS
sudo yum install zip
sudo yum install unzip
```

#### 压缩(.zip)

```bash
zip [-r] 压缩包名 要压缩的文件
```

#### 解压缩(.zip)

```bash
unzip 压缩包名
# 压缩到指定目录
unzip 压缩包名 -d 解压目录
```

### rar

```bash
#ubuntu
sudo apt install rar
```

## 查找命令

### find

根据文件的属性，查找对应的磁盘文件，比如文件名，文件类型，文件大小，文件的目录深度等。

```bash
find 搜索的路径 参数 搜索的内容
# -name 文件名，支持通配符?（单个）和*（多个），如find ./ -name "*.cpp"
# -type 文件类型
# -size 文件大小

find / -maxdepth 5 -name "*.txt"
# 搜索最多5层目录。同理还有 mindepth

find 路径 参数 参数值 -exec shell命令2 {} \;
#同时执行多个操作，将-exec替换为-ok，可以进行交互式操作。

find 路径 参数 参数值 | xargs shell命令2
```

![image-20240303210053582](/pic/image-20240303210053582.png)

### grep

grep命令用于查找文件里符合条件的字符串。

```bash
grep "搜索的内容" 搜索的路径/文件 参数
```

- -r：如果需要搜索目录。
- -i：忽略大小写。
- -n：显示行号。

![image-20240303210754953](/pic/image-20240303210754953.png)

### locate

功能与find类似，需要额外安装mlocate（ubuntu），不推荐。
