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
机房电脑要配置静态网络地址所以要用netcl来配置

netctl的配置文件保存在` /etc/netctl/`。

一些配置文件的示例位于 `/etc/netctl/examples/`

可以把`/etc/netctl/examples/ethernet-static` 复制到`/etc/netctl`下

然后根据情况进行修改

一般配置好这些就可以了
```
Interface=your interface   #网络接口
Address=your ip      #ip地址
Gateway= your gateway    #网关地址
DNS= your dns       #dns
```
配置完成后 把网卡关闭一下 然后启动netcl
```
$ ip link set your interface down
$ netcl start your profile  #你的配置文件名
```
5. 配置mirror
打开`/etc/pacman.d/mirrorlist`

把前面几个源都加井号注释掉，让中国的源在第一的位置
```
$  vim /etc/pacman.d/mirrorlist
```
6. 安装系统
```
$ pacman -Syy        
$ pacstrap -i /mnt base base-devel        
```
7. 配置系统
用以下命令生成 fstab 文件 (用 -U 或 -L 选项设置UUID 或卷标)：
```
$ genfstab -U /mnt >> /mnt/etc/fstab
```

Change root 到新安装的系统：
```
arch-chroot /mnt
# 配置时区
$ ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime        #有时要删除/etc/localtime
$ hwclock --systohc --utc                           # 建议使用UTC
#
#
# 设置Locale
# locale主要的两个文件locale.conf和locale.gen
# 
$ nano /etc/locale.gen
# 我留下了以下
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
#
$ locale-gen
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
$ echo myhostname > /etc/hostname                   # 设置主机名
$ vim /etc/hosts                                    # 将hostname部分修改成你上面设置的主机名
$ mkinitcpio -p linux                               # 如果修改了mkinitcpio.conf
$ pacman -Syy
$ pacman -S grub
$ grub-install --target=i386-pc /dev/sda             # --target=i386-pc只是为BIOS引导的用户安装的
$ grub-mkconfig -o /boot/grub/grub.cfg               # 创建grub.cfg配置文件
$ passwd                                             # 设置root密码
#
# 以上配置完毕
$ exit                                               # Ctrl+D或者exit
$ umount -R /mnt
$ reboot                                             # 安装完成重启系统,修改BIOS启动顺序为硬盘
```
到这里就安装好了基本的系统了

以上这些参考了[简书](https://www.jianshu.com/p/5ab11ed63c1c)上的配置

