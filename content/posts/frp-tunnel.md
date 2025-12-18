---
title: Frp 内网穿透
date: 2025-12-18T10:57:00+08:00
lastmod: 2025-12-18T10:57:00+08:00
author: 云弋
# avatar: /img/author.jpg
# authorlink: https://author.site
#cover: /img/cover.jpg
# covercaption: a description of the cover image
# images:
#   - /img/cover.jpg
categories:
  - frp
tags:
  - frp
  - 内网穿透
# nolastmod: true
# math: true
draft: false
---

<!--more-->

## Server 配置

### 配置文件

```toml
[common]
bind_port = 7000
token = c5c3e122c4d6a8f7e9c0b1d3a5e7f9c2d1b3a5e7f9c0b1d3a5e7f9c2d1b3a5e7

[[proxies]]
name = "web-service"
type = "tcp"
localIP = "127.0.0.1" # 控制只有本机可以访问
localPort = 5001      # FRP 映射的本地端口
remotePort = 5001     # 在 frpc 端的端口

[[proxies]]
name = "web-service-2"
type = "tcp"
localIP = "127.0.0.1"
localPort = 8000 # FRP 映射的本地端口
remotePort = 8000 # 在 frpc 端的端口
```

### Systemd

```bash
Description = frp server
After = network.target

[Service]
Type = simple
User = nobody
Restart = on-failure
RestartSec = 5s
ExecStart = /opt/frp/frps -c /opt/frp/frps.toml

[Install]
WantedBy = multi-user.target
```

## Client

### 配置文件

```toml
[common]
server_addr = 110.110.110.110
server_port = 7000
token = c5c3e122c4d6a8f7e9c0b1d3a5e7f9c2d1b3a5e7f9c0b1d3a5e7f9c2d1b3a5e7

[web_service_1]
type = tcp
local_ip = 127.0.0.1
local_port = 5000    # 本地服务自己监听的端口
remote_port = 5001   # 跟 server 中的remotePort 保持一致
```
