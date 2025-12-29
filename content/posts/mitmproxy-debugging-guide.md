---
title: "Claude Code 抓包教程（mitmproxy）"
date: 2025-12-29
draft: false
tags: ["debugging", "mitmproxy", "claude-code"]
---

## 前提条件

- Arch Linux（其他发行版命令略有不同）
- 已安装 mitmproxy：`sudo pacman -S mitmproxy`

## 步骤一：安装 CA 证书

```bash
# 首次运行 mitmproxy 会自动生成证书
mitmproxy
# 按 q 退出

# 安装证书到系统信任库
sudo cp ~/.mitmproxy/mitmproxy-ca-cert.pem /etc/ca-certificates/trust-source/anchors/mitmproxy.crt
sudo update-ca-trust

# 验证证书已信任
trust list | grep -i mitmproxy
```

## 步骤二：确保 Python 库版本正确

```bash
# 检查是否有版本冲突（用户目录覆盖系统版本）
pip show pyopenssl cryptography | grep -E "^(Name|Version|Location)"
pacman -Q python-pyopenssl python-cryptography

# 如果用户目录有旧版本，删除它们
pip uninstall pyopenssl cryptography -y --break-system-packages
```

## 步骤三：启动 mitmproxy

```bash
# 确保没有代理环境变量干扰
unset http_proxy https_proxy HTTP_PROXY HTTPS_PROXY

# 启动（默认监听 8080 端口）
mitmproxy

# 或使用 Web 界面
mitmweb
```

如果使用 Clash 等代理软件，需要配置上游代理：

```bash
mitmproxy --mode upstream:http://127.0.0.1:7890
```

## 步骤四：启动 Claude Code

```bash
# 指定代理和 Node.js CA 证书
NODE_EXTRA_CA_CERTS=~/.mitmproxy/mitmproxy-ca-cert.pem \
https_proxy=http://127.0.0.1:8080 \
http_proxy=http://127.0.0.1:8080 \
claude
```

**推荐**：添加 alias 到 `~/.bashrc` 或 `~/.zshrc`：

```bash
alias claude-debug='NODE_EXTRA_CA_CERTS=~/.mitmproxy/mitmproxy-ca-cert.pem https_proxy=http://127.0.0.1:8080 http_proxy=http://127.0.0.1:8080 claude'
```

然后执行 `source ~/.bashrc`，之后用 `claude-debug` 启动即可抓包。

## 常见问题

| 问题 | 原因 | 解决方案 |
|------|------|--------|
| TLS handshake failed | CA 证书未安装 | 执行步骤一 |
| cert must be an X509 instance | pyOpenSSL/cryptography 版本冲突 | 执行步骤二 |
| 502 Bad Gateway | mitmproxy 继承了代理变量形成回环 | 用 unset 清除后重启 mitmproxy |
| unexpected eof while reading | TLS 握手中断，通常是上述原因之一 | 检查步骤一和二 |
| UNABLE_TO_VERIFY_LEAF_SIGNATURE | Node.js 不信任证书 | 添加 NODE_EXTRA_CA_CERTS 环境变量 |

## mitmproxy 常用快捷键

| 快捷键 | 功能 |
|--------|------|
| ? | 帮助 |
| ↑/↓ | 选择请求 |
| Enter | 查看详情 |
| e | 查看 eventlog（排错用） |
| q | 返回/退出 |
| f | 过滤请求 |

## 过滤只看 Claude API

在 mitmproxy 中按 `f`，输入：

```
~d api.anthropic.com
```

## 核心要点

- `NODE_EXTRA_CA_CERTS` 是 Node.js 追加自定义 CA 证书的标准方式，不会覆盖系统证书
- Claude Code 基于 Node.js，系统信任证书后 curl/Python 能工作，但 Node.js 需要单独配置
- 使用 alias 可以按需启用抓包，避免影响正常使用时的性能
