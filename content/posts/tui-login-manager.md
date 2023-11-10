+++
title = '酷酷的 TUI 登陆管理器'
date = 2023-11-10T10:33:23+08:00
draft = false
+++


## 安装

```bash
yay -S greetd{,-tuigreet}
```

## 修改配置文件

greetd 的配置文件位于 /etc/greetd/config.toml
```conf
[terminal]
vt = "next"
[default_session]
command = "tuigreet --user-menu --user-menu-min-uid 1000 --remember --remember-session --time --issue --asterisks"
user = "greeter"
```

## 设置开机启动

```bash
systemctl --enabale --now greetd
```

到这里， 酷酷的TUI登录界面就配置好了
