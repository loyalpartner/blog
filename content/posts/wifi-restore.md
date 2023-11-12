+++
title = '休眠后 Wifi 列表找不到热点'
date = 2023-11-12T22:53:02+08:00
draft = false
+++


  Macbook 安装了 Arch Linux 已经有一段时间了, 基本上能满足我的日常需求.
唯一不足的地方就是在设备进入休眠唤醒后无线网会找不到热点.

下面记录下重启网卡模块的方法:

1. 找到网卡模块
```
hwnet | grep 'net driver'
```

如果没有 hwnet 命令, 只要你记得网卡的设备名称, 比如我的是 `wlp3s0`,
那么也可以通过查看 `/sys/class/net/wlp3s0/device/uevent` 文件的 `DRIVER` 项获取.

2. 重新加载网卡模块
```
sudo modprobe -r brcmfmac
sudo modprobe -i brcmfmac
```
