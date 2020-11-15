# Linux基础知识
## Linux 系统基础结构

 - > /boot: 存储系统的引导信息和内核等信息。
 - > /usr:存储系统应用软件安装信息。
 - > /var:存储系统日志信息。

  一个完整的Linux内核一般由5个部分组成，它们分别是
  * 内存管理
  * 进程管理
  * 进程间通信
  * 虚拟文件系统
  * 网络接口
```txt
 1.内存管理：内存管理主要完成的是如何合理有效地管理整个系统的物理内存，
同时快速响应内核各个子系统对内存分配的请求。

 2.进程管理：进程管理主要控制系统进程对CPU的访问，当需要某个进程运行时，
由进程调度器根据基于优先级的调度算法启动新的进程，Linux支持多任务运行。

 3.进程间通信：进程间通信主要用于控制不同进程之间在用户空间的同步、数据共享和交换。
 
 4.虚拟文件系统：Linux内核中的虚拟文件系统用一个通用的文件模型表示各种不同的文件系统，
这个文件模型屏蔽了很多具体文件系统的差异，使Linux内核支持很多不同的文件系统。
 
 5.网络接口：网络接口提供了对各种网络标准的实现和各种网络硬件的支持。
```  


### Linux查看系统硬件信息

#### 1.查看系统PCI设备
```bash
   lspci
```	
 - > host bridge：集成主板设备
 - > VGA compatible controller：VGA显卡设备
 - > Class 0403：Intel 声卡设备
 - > USB Controller：USB接口设备
 - > SATA controller：SATA控制器
 - > Ethernet controller：千兆网卡设备
  
此外，通过命令  lspic -v  能查看到更详细的PCI设备信息  


#### 2.查看CPU信息
```bash
	more /proc/cpuinfo 
	
	或者
	
	cat /proc/cpuinfo
```

 * 查看系统物理CPU的个数
```bash
	cat /proc/cpuinfo | grep "physical id" | sort | uniq | wc -l 
```

 * 查看每个物理CPU中内核的个数
```bash
	cat /proc/cpuinfo | grep "cpu cores" 
``` 

 * 查看系统所有逻辑CPU个数（物理CPU中内核个数+超线程个数）
```bash
	cat /proc/cpuinfo | grep "processor" | wc -l 
``` 


#### 3.查看系统内存

```bash
	more /proc/meminfo
``` 
根据输出，可以清楚知道系统的内存占用情况。
比如，此系统的物理内存多少、可用物理内存多少、缓冲区多少，缓存多少等 

#### 4.查看磁盘分区信息

```bash
	fdisk -l 
```
这个需要管理员权限才能查看。
通过这个命令可以知道划分了多少个区，每个区大小。


 * 常见文件系统类型
```txt

msdos : DOS文件系统类型
vfat : 支持长文件名的DOS分区文件系统类型，也可理解为Windows文件系统类型
iso9660 : 光盘格式文件系统类型
ext2/ext3/ext4 : Linux下的主流文件系统类型
xfs : Linux下一种高性能的日志文件系统，在Centos7.x版本中称为默认文件系统。

```


```bash
  查看系统的默认 target
[henry@localhost system]$ ll  /etc/systemd/system/default.target
lrwxrwxrwx. 1 root root 37 Jun 14 14:04 /etc/systemd/system/default.target -> /lib/systemd/system/multi-user.target



  查看运行级别与 target 的对应关系
[henry@localhost system]$ ll  /lib/systemd/system/runlevel*.target
lrwxrwxrwx. 1 root root 15 Jun 14 19:58 /lib/systemd/system/runlevel0.target -> poweroff.target
lrwxrwxrwx. 1 root root 13 Jun 14 19:58 /lib/systemd/system/runlevel1.target -> rescue.target
lrwxrwxrwx. 1 root root 17 Jun 14 19:58 /lib/systemd/system/runlevel2.target -> multi-user.target
lrwxrwxrwx. 1 root root 17 Jun 14 19:58 /lib/systemd/system/runlevel3.target -> multi-user.target
lrwxrwxrwx. 1 root root 17 Jun 14 19:58 /lib/systemd/system/runlevel4.target -> multi-user.target
lrwxrwxrwx. 1 root root 16 Jun 14 19:58 /lib/systemd/system/runlevel5.target -> graphical.target
lrwxrwxrwx. 1 root root 13 Jun 14 19:58 /lib/systemd/system/runlevel6.target -> reboot.target

5种target模式
poweroff.target 对应 runlevel0，表示关机模式；
rescue.target 对应 runlevel1，表示单用户模式或救援模式；
multi-user.target 对应 runlevel2、runlevel3、runlevel4，表示多用户模式；
graphical.target 对应 runlevel5，表示图形界面多用户模式；
reboot.target 对应 runlevel6，表示重启系统模式。
```


### 系统服务管理工具systemd


 * 启动、停止、重启服务
```bash
  通过 systemctl 命令启动一个服务：
  systemctl start httpd.service

  停止服务
  systemctl  stop  httpd.service
  
  重启服务
  systemctl  restart http.service
  systemctl  try-restart http.service
  systemctl  reload httpd.service
```


 * 查看、禁止、启用服务
 
  通过 systemctl 命令可以启用、禁用服务，以此来控制开机服务启动，
  可以使用 enable/disable 选项来控制一个服务示范开机启动，命令如下。
```bash
	打开 httpd 服务 开机自启动功能。
	systemctl enable httpd.service 

	关闭 httpd 服务 开机自启动
	systemctl disable httpd.service 
	
	查看一个服务的运行状态，可以使用 systemctl 命令的 "status"选项
	systemctl status httpd.service
```
 
  * rz,sz进行文件的上传和下载
```bash
  查询是否安装了 lrzsz 软件包
  rpm -q lrzsz
  
  安装 lrzsz
  yum install lrzsz 
  
  从Linux下载文件到本地 
  sz   需要下载的文件的路径
  例如：
  sz test.txt 

  从本地上传文件到Linux 
  rz  
  然后选择要上传的文件

```
 