# arch-repair

关于我三天前误删了配置文件一直重装到现在这件事

王敬泽.jpg

>找回是不可能找回的，介辈都不可能找回的，网上的复原软件又不会用
>只能重装系统维持的了生活这样子

我已经预料到了未来的我肯定又会掉坑里的
所以我决定还是把重装的过程记录一下

### 重装的心路历程
#### 准备
1. 制作启动盘
mac下直接dd

准备完毕，就可以开始重装了
#### 安装
1. 分区
查看一下
```
$ fdisk -l
```
开始分区
```
$ fdisk /dev/sda
```
进入fdisk就可以开始新建删除了
```
#d是删除
#n是新建
#p是打印
#q是不保存退出
#w是保存退出

#设置分区大小
#swap 2G
#/boot 200M
#/home 50G
#/ 剩下所有
超过四个分区要提前设置逻辑分区
```
2.  格式化分区
```
$ mkfs.ext4 /dev/sda1  #ext4文件系统
$ mkfs.ext4 /dev/sda3
$ mkfs.ext4 /dev/sda5
$ mkswap /dev/sda2  #swap
$ swapon /dev/sda2  #启用swap
```
3. 挂载目录
```
$ mount /dev/sd5 /mnt
$ mkdir /mnt/boot
$ mkdir /mnt/home
$ mount /dev/sda1 /mnt/boot
$ mount /dev/sda3 /mnt/home
```
4. 配置网络



