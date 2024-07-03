+++
title = 'Lxc and Remoting Network'
date = 2024-07-03T17:34:24+08:00
draft = false
+++

# Lxc and Remoting Network


## 远程管理 Lxd 容器

使用 Lxd 容器时, 有时候我们需要远程管理容器, 例如在本地管理远程服务器上的容器. 这时候我们可以使用 Lxc 命令行工具来管理远程容器.


### 远程服务器配置

首先, 我们需要在远程服务器上配置 Lxd 服务, 使其支持远程管理. 首先, 我们需要在远程服务器上安装 Lxd 服务:

```bash
lxc config set core.https_address :8443
lxc config set core.trust_password xxxxxxxx
```

其中, `core.https_address` 用于配置 Lxd 服务的监听地址, `core.trust_password` 用于配置 Lxd 服务的访问密码.


### 远程管理容器

在本地机器上, 我们可以使用 Lxc 命令行工具来管理远程服务器上的容器. 首先, 我们需要配置 Lxc 客户端, 使其支持远程管理:

```bash
lxc remote add remote-server https://remote-server:8443
lxc remote switch remote-server
```

其中, `remote-server` 是远程服务器的名称, `https://remote-server:8443` 是远程服务器的 Lxd 服务地址.


## 异地组网


n2n 是一种基于用户空间的 VPN 技术, 可以用于构建异地组网. n2n 通过在用户空间中运行的进程, 将多个节点连接在一起, 形成一个虚拟的网络. n2n 可以用于构建多种网络拓扑, 例如点对点, 网状, 树状等.

接下来, 我们将介绍如何使用 n2n 构建一个异地组网.

### 配置 supernode 节点

我们需要准备一台拥有公网 IP 的服务器, 用于作为 n2n 的 supernode 节点.

#### 安装 n2n

n2n 版本不同，很容易出现问题，这里我们使用 n2n 的最新版本.

首先, 我们需要从 n2n 的官方仓库中下载源码,并进行编译安装:

```bash
git clone https://github.com/ntop/n2n.git
cd n2n
./autogen.sh
./configure --prefix=/usr
make
sudo make install
```

#### 启动 supernode

```bash
echo "-p=7777 -f" | sudo tee /etc/n2n/supernode.conf
sudo supernode /etc/n2n/supernode.conf 
```

### 安装配置 edge 节点

我使用的系统是 Arch Linux, 可以通过 yay 直接安装 n2n.

编辑配置文件: /etc/n2n/edge.conf 然后启动 edge 服务.

```bash
yay -S n2n
sudo systemctl enable --now edge
```





