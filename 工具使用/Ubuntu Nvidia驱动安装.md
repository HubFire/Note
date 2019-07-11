## Ubuntu 16系统Nvidia显卡驱动安装
* 从Nvidia官网选择对应显卡的驱动，下载run文件
* 禁用默认的nouveau驱动：
```shell
在/etc/modprobe.d/blacklist.conf末尾添加：
blacklist nouveau
options nouveau modeset=0
更新内核：
sudo update-initramfs -u
sudo reboot （一定要重启后生效）
```
* 验证nouveau是否已经禁用 lsmod | grep nouveau
* 显卡安装前关闭图形界面 sudo service  lightdm  stop 
* 卸载原有驱动sudo apt-get remove nvidia-* （如果有的话）
* run 文件增加执行权限后运行， -h 可以查看可以加哪些参数，-A显示高级参数帮助
```shell
查看高级选项：
sudo  ./NVIDIA-Linux-x86_64-430.34.run -A
驱动安装：
sudo  ./NVIDIA-Linux-x86_64-430.34.run --no-x-check --no-nouveau-check 
--no-x-check 表示关闭X服务
--no-nouveau-check  不检查nouveau
挂载驱动：
modprobe nvidia
测试驱动：
nvidi-smi
sudo  reboot 
```

