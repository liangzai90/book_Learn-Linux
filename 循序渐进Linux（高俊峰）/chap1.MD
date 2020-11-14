

 - > /boot: 存储系统的引导信息和内核等信息。
 - > /usr:存储系统应用软件安装信息。
 - > /var:存储系统日志信息。


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

```


```bash

```

```bash

```
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 









