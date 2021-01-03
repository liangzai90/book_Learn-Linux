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

#### ls 命令

列出 /home 目录下文件及其子目录
```bash
ls  -l  /home 
```

### pwd 命令
显示当前的工作目录
```bash
pwd
```

### cd 命令
切换到 /usr/bin 目录下 
```bash
cd /usr/bin
```

返回当前用户的默认工作目录
```bash
cd 或 cd ~ 
```


### date 命令
显示或者修改系统时间

指定的格式显示时间和日期
```bash
[heliang@VM_0_10_centos ~]$  date '+this date now is:%x,time is now:%X,thanks!'
this date now is:11/29/2020,time is now:11:05:29 AM,thanks!
```

修改系统时间，可以单独设置，也可以合并一起设置
```bash
[root@VM_0_10_centos heliang]# date -s 20101111
Thu Nov 11 00:00:00 CST 2010
[root@VM_0_10_centos heliang]# date -s 13:14
Thu Nov 11 13:14:00 CST 2010
[root@VM_0_10_centos heliang]# 
[root@VM_0_10_centos heliang]# 
[root@VM_0_10_centos heliang]# date -s "20201129 11:12"
Sun Nov 29 11:12:00 CST 2020
```


显示两天前的时间，这里的单词不能错 2 days ago ，不能有多余字母~~~~！！！！
```bash
[root@VM_0_10_centos heliang]#  date "+%Y-%m-%d"
2020-11-29
[root@VM_0_10_centos heliang]# date -d "2 days agao" "+%Y-%m-%d" 
date: invalid date ‘2 days agao’
[root@VM_0_10_centos heliang]# 
[root@VM_0_10_centos heliang]# date -d "2 days ago" +%Y-%m-%d
2020-11-27
[root@VM_0_10_centos heliang]# date -d "2 years ago" +%Y-%m-%d
2018-11-29
[root@VM_0_10_centos heliang]# 
```

### passwd 命令
用于设置用户密码，语法格式如下：
passwd [用户名]

普通用户要修改自己的密码，使用如下命令
```bash
passwd
```
然后根据提示，连续两次输入密码即可。

超级用户root修改某个用户的密码时，使用以下命令。
```bash
passwd [用户名]
```
然后根据提示，连续两次输入密码即可。


### su 命令
su命令主要用于改变用户身份，其格式如下：
su  [选项]  [用户名]

普通用户要称为超级用户，可执行如下命令：
```bash
su -
```

### clear 命令
clear 命令用来清除屏幕信息,
格式如下:
```bash
clear 
```

### man 命令
man命令用来显示指定命令的帮助信息，格式如下：
man  [命令名称]

需要得到 ls 命令的帮助信息，可以使用以下命令：
```bash
man  ls 
```

### who 命令
who 命令显示目前登录到系统的用户，格式如下：
who  [选项]  [file]

查询系统处于什么运行级别
```bash
who  -r 
```

显示系统最近启动日期，以及当前每个用户的登录详情、终端状态
```bash
who -buT
```

### w命令
用于显示登录到系统的用户信息，格式如下；
w  [选项]  [用户]

显示当前用户登录信息
```bash
w
```

### uname 命令
uname 命令用来显示操作系统相关信息。其格式如下：
uname  [选项]

显示操作系统全部信息
```bash
uname  -a 
```

### uptime 命令
uptime 命令用来输出系统任务队列信息，其格式如下：
uptime

uptime输出当前系统时间、系统开机到现在的运行时间、
目前有多少个用户在线和系统平均负载
```bash
[henry@localhost cppDemo]$ uptime
 15:06:46 up  2:07,  2 users,  load average: 0.00, 0.01, 0.05
```

### last 命令
列出目前与过去登入系统的用户相关信息。
last  [选项]  [-n 显示行数]
列出最近的5条数据
```bash
last -n 5
```

### dmesg 命令
显示开机信息。
dmesg  [选项]

也可以在 /var/log 目录下查看 dmesg文件。
```bash
dmesg 
```

### free 命令
free 命令用来显示系统内存状态，具体包括系统物理内存、
虚拟内存、共享内存和系统缓存。
free  [选项]  [-s  （间隔秒数）]

列出当前内存的使用状况，以 MB 的形式显示：
```bash
free -m
```

### ps 命令
ps命令显示系统进程在瞬间的运行动态，其格式如下：
ps  [选项]

 PID 是进程的标识号
 TTY 是进程所属的终端控制台
 TIME 列是进程所使用的总的CPU时间
 CMD列列出正在执行的命令行
```bash
[henry@localhost cppDemo]$ ps -a
   PID TTY          TIME CMD
  2645 pts/0    00:00:00 ps
```

要查看系统所有进程，使用以下命令：
```bash
ps -ef
```

```txt
ps 命令的选项及其说明

a    显示所有用户的进程，包含每个程序的完整路径
-x   显示所有系统程序，包括那些没有终端的程序
-u   显示使用者的名称和起始时间
-f   详细显示程序执行的路径群
-c   只显示进程的名称，不显示进程的完整路径
-e   将除内核进程以外所有进程的信息写到标准输出

常用组合： 
ps -ef
ps aux 
```

### top 命令
top命令提供了对系统处理器状态的实时监控，
它能够实时显示系统中各个进程的资源占用情况。
```bash
top
```
```txt
top 的交互式命令。
交互式命令就是在top命令执行过程中使用的一些命令

m     切换显示内存信息
t     切换显示进程和CPU状态信息
l     切换显示平均负载和启动时间信息
q     退出top显示
c     切换显示完整命令行和命令名称信息

M     根据驻留内存大小进行排序输出
P     根据CPU使用百分比大小进行排序输出
T     根据时间/累计时间进行排序输出
S     切换到累计模式

```


```bash

```


```bash

```


```bash

```


```bash

```


```bash

```


```bash

```






































