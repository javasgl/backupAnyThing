---
title: Linux强制卸载设备
date: 2017-05-12 11:04:07
tags: [Linux,umount,fuser,losf]
categories: Linux
---

Linux下因为磁盘出问题而导致了`Input/output error`,所以准备先把有问题的磁盘卸载掉再重新挂上试试。

执行`umount`:
```
[root@hostname ~]# umount -n /dev/sdc1
umount: /home/data: device is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1))
```
显示设备正忙，所以执行`umount -nf`:
```
[root@hostname ~]# umount -nf /dev/sdc1
umount2: Device or resource busy
umount: /home/data: device is busy.
        (In some cases useful info about processes that use
         the device is found by lsof(8) or fuser(1))
umount2: Device or resource busy
```
还是无法卸载。

根据给出的提示，使用`losf`或者`fuser`来找出那些进程正在使用该设备:
```
[root@yourdream ~]# fuser -cu /dev/sdc1
/dev/sdc1:            2444c(root)  2458c(root)  3041c(mysql)
[root@yourdream ~]# fuser -cu /dev/sdc1
```
使用`c`指定挂载的文件系统，`u`显示使用者的id。可以看出三个进程正在使用，通过`ps`可以查到分别为`redis`和`mysql`正在使用。
因此，卸载之前停掉`redis`和`mysql`之后再次执行`umount`就能正常卸载了。
