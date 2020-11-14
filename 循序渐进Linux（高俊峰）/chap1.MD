
/boot: 存储系统的引导信息和内核等信息。
/usr:存储系统应用软件安装信息。
/var:存储系统日志信息。


### Linux查看系统硬件信息

 * 1.查看系统PCI设备
```bash
   lspci
```	
  host bridge：集成主板设备
  VGA compatible controller：VGA显卡设备
  Class 0403：Intel 声卡设备
  USB Controller：USB接口设备
  SATA controller：SATA控制器
  Ethernet controller：千兆网卡设备
  
此外，通过命令  lspic -v  能查看到更详细的PCI设备信息  


 * 2.











