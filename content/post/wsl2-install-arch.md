+++
title = "wsl2 install arch"
author = ["lee"]
date = 2020-06-04
tags = ["arch", "linux", "wsl2", "blog"]
categories = ["Windows"]
draft = false
+++

启用 wsl2  

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

默认使用 wsl2  

```powershell
wsl --set-default-version 2
```

下载安装 ArchWSL [yuk7/ArchWSL](https://github.com/yuk7/ArchWSL/releases)  

换源  

```shell
echo 'Server = https://repo.huaweicloud.com/manjaro/stable/$repo/$arch' > /etc/pacman.d/mirrorlist
```

更新系统  

```bash
pacman-key --init # 初始化key
pacman -Syy manjaro-keyring
# 这里的 /var/cache/pacman... 就是刚才让询问你是否删除的文件
pacman -U /var/cache/pacman/pkg/manjaro-keyring-20190608-1-any.pkg.tar.xz # 强制安装，导入Manjaro的key
pacman-key --init # 再次初始化key
pacman-key --populate archlinux manjaro # 下载Arch和Manjaro的key
pacman -Syyu # 更新软件源索引列表，并更新系统软件包
```

创建用户  

```bash
sudo useradd -r -m -s /bin/bash your_name
sudo chmod +w /etc/sudoers
sudo vim /etc/sudoers
# 添加 your_name ALL=(ALL) ALL
sudo chmod -w /etc/sudoers
# 设置密码
passwd your_name
```

安装 yay  

```bash
sudo pacman -S --needed yay base-devel
# yay 换源
sudo yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```

安装桌面环境  

```bash
sudo pacman -S xfce4 xfce4-goodies # 直接回车全部选择
# 安装中文字体
sudo pacman -S wqy-microhei
```

启用中文环境  

sudo vim /etc/locale.gen。取消下面两行的注释：  

```bash
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
```

执行下面的代码初始化语言环境  

```bash
sudo locale-gen 
```

在~/.zshrc中添加以下几行  

```bash
export LC_CTYPE=zh_CN.UTF-8
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```

声音支持  
[Enabling sound in WSL / Ubuntu - Let It Sing! - X410.dev](https://x410.dev/cookbook/wsl/enabling-sound-in-wsl-ubuntu-let-it-sing/)  

其中 61 行的 ip 设置改成这样  

```bash
load-module module-native-protocol-tcp auth-ip-acl=127.0.0.1;192.168.0.0/24 auth-anonymous=1
```

[Manjaro WSL2 配置记录 - osc\_t1bxxmjp的个人空间 - OSCHINA](https://my.oschina.net/u/4362704/blog/3308054)
