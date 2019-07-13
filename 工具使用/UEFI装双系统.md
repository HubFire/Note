##  UEFI 模式双系统
1. 在win下腾出空闲的磁盘空间
2. 在BIOS中关闭系统的快速启动和安全启动
3. linux 分区的时候不要分boot区，需要分EFI系统分区，1G左右（/,/home,/tmp/,swap,efi）
4. 装的过程需要断网，不下载第三方软件


## 双系统安装后找不到ubuntu引导问题
1. 先备份win的启动EFI文件夹
2. 从BIOS启动ubuntu，用ubuntu的EFI覆盖win的EFI(在/boot下)
3. 在将备份的win的EFI下的Microsoft放到win的C盘EFI下
4. ubuntu安装grub2引导修复工具boot-repair修复引导
5. 手动修改/boot/grub/grub.cfg,将多余的或者无效的引导删除
完成后重启，此时grub2已经接管了win的启动项，所有启动项由grub管理

## 装好后的收尾工作
1. 将BIOS的安全启动打开
2. 禁止USB启动

## 心得
1. 分区前备份分区表
2. 备份win启动项文件（C盘EFI文件夹）

##  一些好用的工具
1. win自带命令行磁盘分区工具diskpart
2. 分区表恢复/数据恢复工具testdisk(开源) Diskgenius(部分功能免费)
3. USB启动制作工具Universal-USB-Installer,uiso9cn(不仅仅支持U盘，还支持移动硬盘) 
4. 快速文件复制工具FastCopy,用于备份大量磁盘数据





