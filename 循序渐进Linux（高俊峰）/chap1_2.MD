# Linux基础知识
## Linux常用命令及技巧


### shell命令的语法分析

#### 1.shell的命令格式

command  [options]  [arguments]

command：表示命令的名称

options：表示命令的选项

arguments：表示命令的参数

例如：
```bash
ls -la /etc 
```

#### 2.shell的通配符

bash中常用的通配符有"*","?","[]"

 * "*"，匹配任意一个或多个字符
```bash
ls *.txt
```
这条命令列出当前目录中所有以".txt"结尾的文件（除去以"."开头的文件）

```bash
ls -al  /etc/*/*.conf 
```
这条命令列出/etc目录的子目录下所有以".conf"结尾的文件。在/etc目录下以".conf"结尾的文件将不会列出。

 * "?"，匹配任意单一字符
```bash
ls  ab?.txt
```
这条命令列出当前目录下以ab开头，随后一个字母是任意字符，接着以".txt"结尾的文件。

```bash
ls  ab??.txt
```
这条命令列出当前目录下以 ab 开头，随后的两个字母是任意字符，接着以".txt"结尾的文件。0


 * "[]"匹配任何包含在方括号内的单字符
```bash
[henry@localhost LinuxDemo]$ ls  /dev/sda[12345]
/dev/sda1  /dev/sda2
```
上面的命令列出了在 /dev 目录下以 sda 开头，第4个字符是1、2、3、4或5的所有文件。

```bash
ls -l /etc/sda[1-5]
```
在方括号中"1-5"给出了匹配的范围，与上面一条命令完全等效。

 * 通配符的组合使用
```bash
ls [0-9]?.conf
```
这条命令列出当前目录下以数字开头，随后一个是任意字符，接着以".conf"结尾的所有文件。

```bash
ls  [xyz]*.txt
```
这条命令列出当前目录下以x、y或z开头，最后以".txt"结尾的文件。


#### 3.shell的重定向
 
  Linux下系统打开3个文件，即标准输入、标准输出和标准错误输出。
用户的shell将键盘设为默认的标准输入，默认的标准输出和标准错误输出为屏幕。
也就是，用户从键盘输入命令，然后将结果和错误消息输出到屏幕。

  所谓重定向，就是不适用系统默认的标准输入/输出，而是重新指定，
因此重定向分为输入重定向、输出重定向和错误输出重定向。

  * 输出重定向 "<", "<<"
```bash
wc </etc/shadow
```
这里用wc命令统计输入给他的文件 /etc/inittab 的行数、单词数和字符数


```bash
[henry@localhost LinuxDemo]$ wc  << aa 
> abc
> def 
> 123
> aa
 3  3 13

```
这种重定向告诉shell，当前命令的标准输入为来自命令行中一对分隔号之间的内容。
例如，这里就是 将一对分隔号 "aa" 之间的内容作为wc命令的输入。
分隔号可以是任意字符。
shell将在第一个分隔号后开始读取内容，直到出现另一个分隔号读取结束，
然后将内容送给wc命令处理。


 * 输出重定向 ">",">>"
输出重定向不是将命令的输出结果在屏幕输出，而是输出到一个指定的文件中。
```bash
ps -ef > ps.txt
```
这条命令将 ps -ef 输出的系统运行进程信息全部输入到了 ps.txt 文件，而不输出到屏幕。

```bash
more  file1 file2 file3 > file
```
more命令用于查看文件内容，上面的命令是将file1,file2,file3的内容全部输出到file文件中，类似于文件的合并。

如果在">"后面指定的文件不存在，shell就会自动创建一个；如果文件存在，那么这个文件原有的内容将被覆盖；
如果不想被覆盖存在的文件，可以使用">>"操作符。

```bash
ls -la /etc/*  >> /root/install.log
```
这条命令将 /etc 目录及其子目录下的所有文件信息追加到  /etc/install.log 文件的后面。
/root/install.log 文件原来的内容仍然存在。

 * 错误重定向
错误重定向和标准输出重定向一样，可以使用操作符"2>","2>>"实现对错误输出的重定向
```bash
tar -zxvf text.tar.gz  2> error.txt
```
tar是打包命令，可以在屏幕看到tar的解压过程。
如果"text.tar.gz"是个损坏的压缩包，就会把错误消息输出到 error.txt 文件。


#### 4.shell管道
通过管道符"|"可以建立管道连接，例如：
这条命令表示将 /etc 目录以及子目录下的所有文件分屏显示。
```bash
ls -la /etc/* | more 
```

这个命令用于查看系统中正在运行的httpd进程，并统计httpd进程数
```bash
ps -ef |grep httpd | wc -l
```

#### 5.shell中的引用
 * 转义字符"\" 
 将"\"放到特殊字符前面，shell 就忽略这些特殊字符的原有含义，把它们当做普通字符对待。

 \\    \*  \?

 * 单引号“'”
 如果将字符串放到一对单引号之间，那么字符串所有字符的特殊含义将被忽略。
 
 * 双引号 “""”
  双引号的引用与单引号基本相同，包含在双引号内的大部分特殊字符可以当作普通字符处理，
但是仍有一些特殊字符即使使用双引号括起来，也仍然保留自己的特殊含义，比如“$”、“\”和“`”

$,\，在双引号内仍然保留了特殊含义。
注意，str之后不能有空格
```bash
[henry@localhost ~]$ str="The \$SHELL Current shell is $SHELL" 
[henry@localhost ~]$ 
[henry@localhost ~]$ 
[henry@localhost ~]$ str1="\$$SHELL" 
[henry@localhost ~]$ 
[henry@localhost ~]$ echo str
str
[henry@localhost ~]$ echo $str 
The $SHELL Current shell is /bin/bash
[henry@localhost ~]$ 
[henry@localhost ~]$ echo $str1
$/bin/bash
[henry@localhost ~]$ 
[henry@localhost ~]$ 



[henry@localhost ~]$ str2="this hostname is `hostname`" 
[henry@localhost ~]$ echo $str2
this hostname is localhost.localdomain

```


#### 6.shell的自动补全命令行
输入某个命令的一部分之后，按Tab键，shell就会根据系统环境变量信息提示出于用户输入命令
相似的所有命令和文件。



### 系统管理和维护

#### ls命令

```bash

```


```bash

```


```bash

```








