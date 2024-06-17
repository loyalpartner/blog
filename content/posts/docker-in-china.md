+++
title = 'Docker in China'
date = 2024-06-17T15:32:36+08:00
draft = true
+++

Docker 在国内已经完全无法使用了，因为它的镜像仓库在国内被封锁了。
你可以使用阿里云的镜像仓库，或者使用其他的替代方案。不过这些替代方案都不是很好用，因为它们都不是官方的。

如果你有一个稳定的代理，可以通过代理来访问 Docker 的官方镜像仓库。

```bash
sudo mkdir /etc/systemd/system/docker.service.d/ -p

cat <<EOF | sudo tee /etc/systemd/system/docker.service.d/proxy.conf
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:7890"
Environment="HTTPS_PROXY=http://127.0.0.1:7890"

sudo systemctl daemon-reload
sudo systemctl restart docker
```


