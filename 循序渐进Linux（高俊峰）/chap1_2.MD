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

## 文件管理与编辑

### midir 命令
mkdir命令用于创建一个目录。
格式如下：
mkdir  [选项]  目录名

在当期目录下创建2个目录abc和def
```bash
mkdir  -p  ./abc/def 
```

### more 命令
如果一个文本文件比较长，一屏无法显示完毕，就需要使用more命令。

 按Enter键，more命令就会接着再显示文本的一行
 按空格键，more命令就会继续显示文本文件的另一屏信息。

格式如下：
more  [选项]  文件名

显示某个文件的内容，每10行显示一屏，同时清除屏幕：
```bash
more  -c  -10  abcdefgFile
```

### cat 命令
cat命令用来将文件的内容输出到标准输出，类似于DOS下面的type命令，
同时cat还可以用于连接合并文件。
其格式如下：

cat  [选项]  文件名

或者：

cat  文件1  文件2  >  文件3

将file1.txt, file2.txt文件内容合并后输出到file3.txt
合并输出之后，原来的文件file1.txt,file2.txt里面的内容不变。

```bash
[henry@localhost LinuxDemo]$ ls
file1.txt  file2.txt  ps.txt  websockt
[henry@localhost LinuxDemo]$ cat file1.txt 
this is file1.txt
abcdeft 
file End
[henry@localhost LinuxDemo]$ cat file2.txt 
this is file2.txt
aabbccddeeff
file2 END...
[henry@localhost LinuxDemo]$ cat file1.txt file2.txt > file3.txt 
[henry@localhost LinuxDemo]$ cat file3.txt 
this is file1.txt
abcdeft 
file End
this is file2.txt
aabbccddeeff
file2 END...
[henry@localhost LinuxDemo]$ cat file1.txt
this is file1.txt
abcdeft 
file End
```

### diff 命令
diff 命令用来比较文件的差异。

以 "<" 打头的行属于文件1，
以 ">" 打头的行属于文件2。

a表示添加;
d表示删除；
c表示修改；

```bash
[henry@localhost LinuxDemo]$ diff file1.txt  file2.txt 
1,3c1,3
< this is file1.txt
< abcdeft 
< file End
---
> this is file2.txt
> aabbccddeeff
> file2 END...
[henry@localhost LinuxDemo]$ diff file1.txt  file3.txt 
3a4,6
> this is file2.txt
> aabbccddeeff
> file2 END...

```

### grep 命令
grep命令是Linux下的文本过滤工具，grep根据指定的字符串，
对文件的每一行进行搜索，如果找到了这个字符串，就输出该行的内容。

其格式如下：
grep  [选项]  需要查找的字符串  文件名

-i 搜索时忽略大小写；
-n 在搜索结果中显示行号
```bash
grep -ni  aa file2.txt 
```

### rm 命令
rm 命令用来删除某个目录及其下的所有文件及子目录。
对于链接文件，只是断开了链接，原文件保持不变。
其格式如下：
rm  [选项]  文件或者目录  
```bash
rm 命令的选项及其说明

-r    告诉rm将选项中列出的全部目录以及子目录还有文件均递归地删除。
      如果不指定 "-r"选项，"rm"命令将不能删除目录。
-f    忽略不存在的问题，也不给出提示
-i    交互式删除，即在删除前进行确认	   
```


### touch 命令
touch命令用来改变指定文件的访问时间和修改时间，若指定文件不存在则创建此文件。
如果没有指定时间，则使用当前时间。
其格式如下：
touch  [选项]  设定的时间  文件

```txt
touch 命令的选项及其说明

-a    改变文件的访问时间为系统当前时间，无须设置时间选项
-m    改变文件的修改时间为系统当前时间，无须设置时间选项
-c    如果文件不存在，不创建也不提示
-d或-t  使用指定的日期或时间
-r     把指定文件或目录的日期、时间都设置成和【参考文件】或目录的日期、时间相同
```

```bash
touch  file1.txt   # 创建一个文件 file1.txt 
touch -a  file1.txt  # 改变文件的访问时间为系统当前时间
touch -m  file1.txt  # 改变文件的修改时间为系统的当前时间
touch -a -r file2.txt  file1.txt # 将file1.txt文件的访问时间修改为file2.txt文件的访问时间
touch -d "20210103 22:45" file2.txt # 将file2.txt文件的修改时间设定为2021年1月3日 22点45分
```

### ln 命令
ln命令用来在文件或目录之间创建链接。
Linux下的链接有两种，一种是硬链接(Hard Link)，另一种是符号链接(Symbolic Link)，
默认情况下 ln 命令产生的是硬链接。

 - > 硬链接：是指通过文件的 inode 来进行链接。在Linux的文件系统中，保存在磁盘的所有类型的文件
都会分配一个编号，这个编号称为 inode 号（Inode Index）。多个文件指向同一个 inode 在
Linux 系统中是允许的，这就是所谓的硬链接。硬链接的作用是允许一个文件拥有多个有效的路径名，
这样用户就可以对一些重要文件建立硬链接，以防止误删除操作。
因为对应该文件的 inode 有一个以上的链接，所以只删除一个链接并不影响 inode 本身和其他链接，
只有当最后一个链接被删除后，文件的数据块及目录的链接才会释放，也就是说，此时文件才会被真正删除。

 - > 符号链接：也叫软链接，软链接类似于windows中的快捷方式，因此软链接是一个
指向真正的文件或目录位置的符号连接。

ln 命令的格式如下：
ln  [选项]  源文件  目标链接名

给file3.txt 创建一个硬链接 abc 
```bash
[henry@localhost LinuxDemo]$ ln file3.txt  abc
[henry@localhost LinuxDemo]$ ls 
abc  file1.txt  file2.txt  file3.txt  ps.txt  websockt
[henry@localhost LinuxDemo]$ ll
total 28
-rw-rw-r--. 2 henry henry    80 Jan  3 18:41 abc
-rw-rw-r--. 1 henry henry    36 Jan  3 18:40 file1.txt
-rw-rw-r--. 1 henry henry    44 Jan  3 18:41 file2.txt
-rw-rw-r--. 2 henry henry    80 Jan  3 18:41 file3.txt
-rw-rw-r--. 1 henry henry 10569 Nov 15 21:28 ps.txt
drwxrwxr-x. 3 henry henry    70 Nov 16 22:43 websockt
```

给 file1.txt 文件创建一个软链接 def 
```bash
[henry@localhost LinuxDemo]$ ln -s file1.txt  def 
[henry@localhost LinuxDemo]$ ll
total 28
-rw-rw-r--. 2 henry henry    80 Jan  3 18:41 abc
lrwxrwxrwx. 1 henry henry     9 Jan  3 23:14 def -> file1.txt
-rw-rw-r--. 1 henry henry    36 Jan  3 18:40 file1.txt
-rw-rw-r--. 1 henry henry    44 Jan  3 18:41 file2.txt
-rw-rw-r--. 2 henry henry    80 Jan  3 18:41 file3.txt
-rw-rw-r--. 1 henry henry 10569 Nov 15 21:28 ps.txt
drwxrwxr-x. 3 henry henry    70 Nov 16 22:43 websockt
```

### file 命令
file命令用来显示文件的类型。
对于长度为0的文件，将识别为空文件；
对于符号连接文件，默认情况下将显示符号链接引用的真实文件路径。
其格式如下：
file  [选项]  文件名 
```bash
[henry@localhost LinuxDemo]$ file file1.txt
file1.txt: ASCII text
[henry@localhost LinuxDemo]$ file websockt
websockt: directory
[henry@localhost LinuxDemo]$ file def
def: symbolic link to `file1.txt'
[henry@localhost LinuxDemo]$ file /etc/init.d/halt
/etc/init.d/halt: cannot open (No such file or directory)
[henry@localhost LinuxDemo]$ file /home/henry/project/cppDemo/a.out
/home/henry/project/cppDemo/a.out: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=e7d51edb28527e92a9e88ffc148d944b664e51d6, not stripped
```

### cp 命令
cp 命令用来将给出的文件或者目录复制到另一个文件或者目录中。
其格式如下；
cp  [选项]  源文件或目录   目标文件或目录

将当前目录下的所有文件复制到 /tmp 目录下，
"./*"表示当前目录下所有文件和目录。
```bash
cp -r  ./*  /tmp
```
将 /home/oracle 目录及其子目录下的全部内容复制到 /home/ixdba 下
```bash
cp  -r  /home/oracle  /home/ixdba
```

### find 命令
find 命令用来在指定的路径下查找指定的文件。
其格式如下：
find  path-name  [-options]  [-print  -exec  -ok 命令{} \;]

-print：将搜索结果输出到标准输出。

-exec：对搜索出符合条件的文件执行所给出的Linux命令，而不询问用户是否需要执行该命令。
{}表示shell命令的选项即为所查找到的文件。
命令的末尾必须以";"结束。
在"}" 和 "\"之间一定要有空格才行。

在系统根目录下，查找文件类型为普通文件，属于ixdba用户，两天以前，
并且查找时不包含 /usr/bin 目录的文件名为 main.c的文件，
并将结果输出到屏幕。
```bash
find / -path  "/usr/bin"  -prune -o  -name  "main.c"  -user  ixdba  -type  f -mtime  +2  -print
```

在系统根目录下查找 不在 /var/log 和 /usr/bin 目录下的所有普通文件
```bash
find / \( -path /var/log -o -path /usr/bin \) -prune  -o  type f -print
```

查找系统中所有大小为0的普通文件，并列出他们的完整路径
```bash
find / -type f -size 0 -exec ls -al {}  \;
```

在当前目录及子目录下查找所有 ".txt" 文件
```bash
find  .  -name  "*.txt"  -print
```

在 /etc 目录下查找文件属主为 henry 用户的文件
```bash
find /etc  -user henry -print
```

在用户自己的根目录下查找文件名以一个大写字母开头，
紧接着是一个小写字母和两个数字，最后以 ".txt" 结尾的文件。
```bash
find ~ -name "[A-Z][a-z][0--9][0--9]*.txt" -print
```

### split 命令
split命令用来分割文档，将一个文件分成数个。
其格式如下：
split  [选项]  [input_file]  [output_file]

将 access_log 文件进行分割，设定每个分割文件的大小为10MB
```bash
split  -b 10M  access_log  access_log_bak
```

```txt
split 命令的选项及其说明

-b  size   指定分割出来的文件大小，size可加入单位，b代码512字节，k代表1kb,m代表1mb
-n         这里 "n" 指定分割的每个文件的长度，默认为1000行
-d         将split命令生成的文件序列以数字形式命名
-a         指定split命令生成的文件序列的长度，默认长度为2
```

### mv 命令
mv命令用来将文件或目录改名或将文件由一个目录移入另一个目录中。
其格式如下：
```bash
mv  [选项]  源文件或目录  目标文件或目录 
```

## 压缩与解压

### zip/unzip 命令
将一般的文件或者目录进行压缩或解压，默认生成以".zip"为后缀的压缩包。
其格式如下：
```bash
zip  [选项]  压缩文件名  需要压缩的文档列表
unzip  [选项]  压缩文件名  


       zip 命令的选项及其说明
-r    递归压缩，将指定目录下的所有文件以及子目录全部压缩
-d    从压缩文件内删除指定的文件
-i "文件列表"  只压缩文件列表中的文件
-x "文件列表"  压缩时排除文件列表中指定的文件
-u    更新文件到压缩文件中
-m    将文件加入压缩文件压缩后，删除原始文件，即把文件移到压缩文件中
-F    尝试修复损坏的压缩文件
-T    检查压缩文件内的每个文件是否正确无误
- 压缩级别   压缩级别是1~9之间的一个数字


       unzip  命令的选项及其说明
-x "文件列表"  解压文件，但不包含文件列表中指定的文件
-t       测试压缩文件有无损坏，并不解压
-v       查看压缩文件的详细信息，具体包括压缩文件中包含的文件大小、
   文件名和压缩比等，并不解压。	   
-n       解压时不覆盖已经存在的文件
-o       解压时覆盖已经存在的文件，并且不要求用户确认
-d 目录名  把压缩文件解压到指定目录下   
```

将  /etc 目录下的所有文件以及目录进行压缩，
设置压缩级别为最高，保存到文件 /opt/etc.zip 
```bash
zip  -9r  /opt/etc.zip   /etc 
```

将 /var 目录中所有文件以及子目录压缩，除了 "*.log"文件之外，
将压缩文件保存到  /opt 目录下
```bash
zip  -r  /opt/var.zip  /var  -x  "*.log"
```

将上面 /opt/etc.zip 中的 etc/passwd 文件从压缩包中删除
```bash
zip  /opt/etc.zip  -d  etc/passwd
```

修改了 /etc/inittab 文件，希望把更改信息更新到 /opt/etc.zip包中
```bash
zip  -u  /opt/etc.zip  /etc/inittab 
```

如果 /etc 目录下很多文件进行了修改或者增加了很多文件，
就需要把 /etc 目录下的全部信息更新到 /opt/etc.zip 文件中。
可以使用如下命令：
```bash
zip  -u  /opt/etc.zip  /etc/*

或者使用
zip  -ru  /opt/etc.zip  /etc
```

将 /opt/etc.zip 压缩文件全部解压到 /etc 目录下，
除了 etc/inittab 文件之外，并且在解压过程中如果出现相同的文件就直接进行覆盖，
不要求用户确认。可以使用如下命令：
```bash
unzip  -o  /opt/etc.zip  -x  etc/inittab  -d  /etc 
```


### gzip/gunzip 命令
将一般的文件进行压缩或者解压。压缩文件预设的扩展名为".gz"，其实gunzip就是gzip的硬链接，
因此无论是压缩或解压都可以通过gzip来实现。

gzip 只能对文件进行压缩，不能压缩目录，即使指定压缩的目录，也只能压缩目录内的所有文件。
其格式如下：
```bash
gzip  [选项]  压缩   （解压缩）的文档名


          gzip  命令的选项及其说明
-d    对压缩的文件进行解压
-r    递归式压缩指定目录以及子目录下的所有文件
-t    检查压缩文档的完整性
-v    对于每个压缩和解压缩的文档，显示相应的文件名和压缩比
-l    显示每个压缩文件的压缩信息，显示字段为压缩文档大小、未压缩文档大小、压缩比和未压缩文档名称
-num  用指定的数字 num 配置压缩比， "-1"或"-fast"表示最低压缩比，"-9"或"best"表示最高压缩比。
   系统默认压缩比为6.	  
```

首先将  /etc 目录下的所有文件以及子目录进行压缩，备份压缩包 etc.zip 到/opt目录，
然后对 etc.zip 文件进行 gzip 压缩，设置gzip的压缩级别为 9.
```bash
zip  -r  /opt/etc.zip  /etc
gzip  -9v  /opt/etc.zip 
```

查看上述 etc.zip.gz 文件的压缩信息
```bash
gzip  -l  /opt/etc.zip.gz  
```

解压上述 etc.zip.gz 文件到当前目录
```bash
gzip  -d  /opt/etc.zip.gz

gunzip   /opt/etc.zip.gz
```
gzip  -d 等价于  gunzip 



### bzip2/bunzip2 命令
对文件进行压缩和解压缩。
此命令类似于 "gzip/gunzip" 命令，只能对文件进行压缩。
对于目录只能压缩目录下的所有文件，压缩完成后，在目录下生成以".bz2"为后缀的压缩包。
bunzip2其实是bzip2的符号链接，即4软链接，因此压缩、解压都可以通过bzip2实现。

其格式如下：
bzip2  [选项]  要压缩或解压的文件
```bash
           bzip2  命令的选项及其说明
-d      执行解压缩，此时选项后面跟要解压缩的文件
-v      压缩或解压文件时，显示详细信息
-k      bzip2在压缩或解压缩后，会删除原始文件，若要保留原始文件，可使用此选项。
-f      bzip2在此压缩或解压缩时，若输出文件与现有文件同名，预设不会覆盖现有文件。
    若要覆盖，就要使用此选项。
-t      测试 ".bz2" 压缩文件的完整性
- 压缩级别    压缩级别可以从1~9中选取，数字越大，压缩率越高
```

将 /opt 目录下的 etc.zip, var.zip 和 backup.zip 进行压缩，
设置压缩率为最高，同时在压缩完毕后不删除原始文件，显示压缩过程的详细信息。
```bash
bzip2  -9vk  /opt/etc.zip   /opt/var.zip   /opt/backup.zip
```


### tar 命令 
tar是linux下经常使用的归档工具，对文件或者目录进行打包归档，归档成一个文件，但并不进行压缩。
其格式如下：
tar  [主选项 + 辅助选项]  文件或目录
```bash
      tar  命令的主选项及其说明
-c    新建文件
-r    把要归档的文件追加到档案文件的末尾
-t    列出档案文件中已经归档的文件列表
-x    从打包的档案文件中还原出文件
-u    更新档案文件，用新建文件替换档案中的原始文件	  
	  
	  
	  tar   命令的辅助选项及其说明
-z    调用gzip命令在文件打包的过程中压缩/解压文件	 
-w    在还原文件时，把所有文件的修改时间设定为现在时间
-j    调用bzip2命令在文件打包的过程中压缩/解压文件
-Z    调用 compress 命令过滤档案
-f    "-f"选项后面紧跟档案文件的存储设备，默认是磁盘，需要制定档案文件名；
     在"-f"选项之后不能再跟任何其他选项，也就是说，"-f"必须是tar命令的最后一个选项。
-v    指定在创建归档文件过程中，显示各个归档文件的名称。
-p    在文件归档的过程中，保持文件的属性不发生变化
-N  "yyyy/mm/dd"  在指定日期之后的文件才打包到档案文件中
--exclude  file    在打包过程中，不将指定file文件打包 
```

将 /etc 目录下的所有文件打包，并显示打包的详细文件，设置打包文件名为 etc.tar，
同时保存文件到 /opt 目录下
```bash
tar  -cvf  /opt/etc.tar   /etc
```

将 /etc 目录下的所有文件打包并压缩，然后显示打包的详细文件，
设置打包文件名为 etc.tar.gz，同时保存文件到 /opt目录下：
```bash
tar  -zcvf  /opt/etc.tar.gz   /etc


tar  -jcvf  /opt/etc.tar.bz2   /etc
```

查阅上面 /opt/etc.tar.gz 压缩包的内容
```bash
tar  -ztvf  /opt/etc.tar.gz


etc.tar.gz 可能包含很多个文件，无法一个屏幕显示完毕，这时可以使用 more 命令，
比如：  tar  -zxvf  /opt/etc.tar.gz | more 
```

将 /opt/etc.tar.gz 解压到 /usr/local/src 下
```bash
cd    /usr/local/src  

tar  -zxvf    /opt/etc.tar.gz
```

将 /etc 目录下的所有文件打包备份到 /opt目录下，并保存每个文件的权限
```bash
tar  -zcvpf   /opt/etc.tar.gz  /etc 
```

在 /opt 目录下，仅解压 /opt/etc.tar.gz 压缩文件中的  /etc/inittab文件
```bash
cd    /opt
tar  -zxvf  /opt/etc.tar.gz  etc/inittab 
```

打包备份 /var/log 目录中 2008/7/21 以后的文件
```bash
tar   -N   "2008/7/21"  -zcvf  log.tar.gz  /var/log  
```


打包备份 /home 目录和 /etc 目录，但是不备份 /home 下的 ixdba目录
```bash
tar   -exclude  /home/ixdba  -zcvf  myfile.tar.gz  /home/*  /etc
```

将 /etc 目录打包压缩后直接解压到 /opt 目录下，而不生成打包的档案文件
```bash
cd    /opt
tar  -zcvf  -  /etc  |  tar  -zxvf  -

紧跟在 "f" 后面的 "-"将创建的档案文件输出到标准输出，
"|"在 linux下表示管道符，
命令最后的 "-"表示将tar命令通过管道传入的档案文件作为需要解压的数据来源。
```


### dd 命令
dd 命令用来转换或复制文件，同时可以对设备进行备份。其格式如下：
dd  if="input_file"  of="output_file"  bs="block_size"  count="number"

dd命令的选项及其说明如下：
 - > if: 就是输入文件，可以是设备，例如，磁盘某分区或者整个磁盘
 - > of: 就是输出文件，也可以是输出设备，例如，磁盘、磁带等
 - > bs: 指定一个block的大小，如果没有指定，默认为 512字节
 - > count: 就是 "bs"的数量
 
将 /etc/inittab 文件备份到 /opt/inittab.bak 中 
```bash
dd   if=/etc/inittab   of=/opt/inittab.bak 
```

将 /dev/sda2 整个分区进行备份，保存备份文件到 /opt 目录
```bash
dd  if=/dev/sda2  of=/opt/sda2.bak
```

将上面例子中备份包 /opt/sda2.bak 恢复到相应的分区，使用以下命令：
```bash
dd  if=/opt/sda2.back  of=/dev/sda2

在恢复目标分区时，一定要先卸载目标分区，待恢复完成，再进行挂载
```

### cpio 命令
cpio命令是通过重定向的方式将文件进行打包、备份、还原、恢复的工具，
它可以解压以 ".cpio" 或者 ".tar" 结尾的文件。
其格式如下：

cpio  [选项]  >  文件名或者设备名

cpio  [选项]  >  文件名或者设备名

```bash
        cpio 命令的选项及其说明
-o      将文件复制、打包成文件或者将文件输出到设备上
-i      将打包文件解压或者将设备上的备份还原到系统中
-t      查看 cpio 打包的文件内容或者输出到设备上的文件内容
-v      显示打包过程中的文件名称
-d      在cpio还原文件的过程中，自动建立相应的目录
-c      一种较新的存储方式
-B      让默认块可以增大到5120字节，默认块为512字节，这样做的好处是可以加快存取速度		
```

将 /etc 下的所有普通文件都备份到 /opt/etc.cpio 中
```bash
find   /etc  -type  f | cpio  -ocvB  >  /opt/etc.cpio
```


##  磁盘管理与维护

### df 命令
df命令用来检查 Linux 系统的磁盘空间占用情况。
其格式如下：
df  [选项]
```bash
       df   命令的选项及其说明
-h     以容易理解的格式输出文件系统分区占用情况，如 32B、120MB、60GB
-k     以KB大小为单位输出文件系统分区占用情况
-m     以MB大小为单位输出文件系统的分区占用情况
-a     列出所有的文件系统分区，包含0大小的文件系统分区
-i     列出文件系统分区的 inode 信息
-T     显示磁盘分区的文件系统类型
```

查看当前系统磁盘分区情况，并显示文件系统类型
```bash
[heliang@VM_0_10_centos linuxCmd]$ df -hT
Filesystem     Type      Size  Used Avail Use% Mounted on
devtmpfs       devtmpfs  1.9G     0  1.9G   0% /dev
tmpfs          tmpfs     1.9G   24K  1.9G   1% /dev/shm
tmpfs          tmpfs     1.9G  484K  1.9G   1% /run
tmpfs          tmpfs     1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/vda1      ext3       50G   20G   28G  42% /
tmpfs          tmpfs     379M     0  379M   0% /run/user/0
tmpfs          tmpfs     379M     0  379M   0% /run/user/1000

```

输出文件系统的 inode 信息
```bash
[heliang@VM_0_10_centos linuxCmd]$ df  -ihT
Filesystem     Type     Inodes IUsed IFree IUse% Mounted on
devtmpfs       devtmpfs   472K   308  471K    1% /dev
tmpfs          tmpfs      474K     7  474K    1% /dev/shm
tmpfs          tmpfs      474K   404  474K    1% /run
tmpfs          tmpfs      474K    16  474K    1% /sys/fs/cgroup
/dev/vda1      ext3       3.2M  288K  2.9M    9% /
tmpfs          tmpfs      474K     1  474K    1% /run/user/0
tmpfs          tmpfs      474K     1  474K    1% /run/user/1000

```

### du 命令
du 命令用来显示文件或目录所占用的磁盘空间。
其格式如下：
du  [选线]  文件或目录
```bash
        du 命令的选项及其说明
-s     显示文件或者整个目录的大小，单位为KB
-b     以字节为单位显示文件大小或者显示目录下所有文件的大小
-sh    以人性化的格式显示文件或目录大小，如300MB，1.2GB 等
-sm    以MB为单位显示文件或者目录大小		
```

显示当前目录下所有文件的大小
```bash
du  -sh  ./*
```

显示 /home/heliang 目录的大小
```bash
[heliang@VM_0_10_centos ~]$ du -sh  /home/heliang/ 
7.0G	/home/heliang/

```

### fsck命令
fsck命令用来检查文件系统并尝试修复错误。
格式如下：
fsck  [选项]  [-t < 文件系统类型]  [设备名]

在执行fsck命令修复某个文件系统时，
这个文件系统对应的磁盘分区一定要处于卸载状态，
磁盘分区在挂载状态下进行修复是极为不安全的，
数据可能遭到破坏，也有可能损坏磁盘。

### sync命令
sync命令用来强制把内存中的数据写回硬盘，以免数据的丢失。
其格式如下：
```bash
sync  
```

### eject命令
eject命令用来退出抽取式设备，如光驱或磁带。
如果该设备已挂载，则eject先将设备卸载，
然后再退出设备。
其格式如下：
eject  [选项]  设备名

要卸载 linux 系统的 /tmp分区，使用以下命令：
```bash
eject   /tmp
```

### mount/umount 命令
挂载以及卸载指定的文件系统。

```bash
mount  [选项]  [-L<标签>]  [-o<选项>]  [-t<文件系统类型>]  [设备名]  [挂载点]

unmount  [挂载点]
```

## 网络设置与维护
ifconfig 命令
ifconfig命令用来配置网络或显示当前网络接口状态。

要在 eth0上配置两个IP地址，分别为 192.168.60.136、192.168.66.138，
子网掩码为 255.255.255.0，使用以下命令：
```bash
ifconfig  eth0  192.168.60.136  netmask  255.255.255.0
ifconfig  eth0:0  192.168.66.138  netmask  255.255.255.0

此时用 ifconfig 命令就可以看到2个网卡信息了，分别是 "eth0" 和 "eth0:0".
```

修改网卡的MAC地址为新的MAC地址，使用以下命令：
```bash
ifconfig  eth0 ether  XX:XX:XX:XX:XX:XX
```

将 eth0 禁用后再启用
```bash
ifconfig  eth0  down
ifconfig  eth0  up
```
用ifconfig 命令配置的网卡信息，在网卡重启或者机器重启后，
所有的配置都失效了，如果要让网卡配置永久生效，就需要修改网卡的配置文件。


### scp命令
scp就是secure copy，用于将文件或目录从一个Linux系统复制到另一个Linux系统下。
scp传输数据用的是SSH协议，保证了数据传输的安全。其格式如下：

### netstat 命令
netstat 命令用来显示本机网络连接、运行端口和路由表等信息。
其格式如下：
netstat  [选项]
```bash
        netstat  命令的选项及其说明
-a     显示本机所有连接的监听端口
-n     以网络IP地址的形式显示当前建立的有效连接和端口
-r     显示路由表信息
-s     显示按协议的统计信息。默认情况下，
   将显示IP、IPv6、ICMP、ICMPv6、TCP、TCPv6、UDP和UDPv6的统计信息。
-v     显示当前的有效连接，与"-n"选项类似
-t     显示所有的TCP协议连接情况
-u     显示所有的UDP协议连接情况
-c<秒数>  后面跟的秒数，表示每隔几秒就刷新显示一次
-i      显示自动配置接口的状态
-l      仅显示连接状态为 "LISTEN" 的服务的网络状态
-p      显示连接对应的 PID 与程序名
```

显示当前系统的路由信息
```bash
[heliang@VM_0_10_centos ~]$ netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
0.0.0.0         172.21.0.1      0.0.0.0         UG        0 0          0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 eth0
172.21.0.0      0.0.0.0         255.255.240.0   U         0 0          0 eth0
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


























