##  UEFI 模式双系统

1. 在win下腾出空闲的磁盘空间，使用easyUEFI修改引导
2. 在BIOS中关闭系统的快速启动和安全启动
3. linux 分区的时候不要分boot区，需要分EFI系统分区，500M左右（/,/home,/tmp/,swap,efi）
4. 装的过程需要断网，不下载第三方软件

