---
title: 如何用 PingTunnel 绕过校园网限制
description: 在许多校园网环境下，虽然你可能还没登录认证系统，或者 TCP/UDP 流量被拦截，但系统往往会允许 ICMP (Ping) 报文通过，以便进行基础的网络诊断。PingTunnel (Pingtunnel) 正是利用这一点，将你的网络流量封装在 Ping 包中进行传输，从而“骗”过网关的审查。
published: 2026-01-19T19:20:43.779Z
tags:
  - 代理
  - 隧道
  - DNS
  - ICMP
  - 网络协议
category: 系统运维
---

## 前言

因为我们学校之前因为一些原因导致校园网被加上了网关认证，虽然有时候能直连上外边但极其不稳定。（而且也没发现蹦不蹦出来认证的规律）

**那么问题来了，如何在不认证的情况下去访问外网呢？**

有一次我测试自己是否能 ping 通服务器时，发现正常获取到了服务器的 IP 地址。也就是说，校园网的防火墙并没有封掉 ICMP 和 DNS 协议。

在许多校园网环境下，虽然你可能还没登录认证系统，或者 TCP/UDP 流量被拦截，但系统往往会允许 **ICMP (Ping)** 和 **DNS** 的报文通过，以便进行基础的网络诊断。**[PingTunnel](https://github.com/esrrhs/pingtunnel)** 正是利用这一点，将你的网络流量封装在 Ping 包中进行传输，从而“骗”过网关的审查。

> 之前也试过 [iodine](https://github.com/yarrick/iodine)，作为一款开源 DNS 隧道工具，本人在尝试的时候发现可以连上，服务器端也能正常握手，但是 TAP 驱动死活是连不上 (应该是这个 issue: https://github.com/yarrick/iodine/issues/102)
> iodine 虽然支持 raw 隧道，但是假如 raw 的包被拦截，速度差不多就会降到几十 KB/s，所以体验会很差

1. **一台服务器：** 需拥有公网 IP，且该 IP 能够被校园网 Ping 通。
2. **PingTunnel：** 在服务器和本地电脑上分别下载对应系统的二进制文件。
3. **代理工具：** 本文以 `Tinyproxy`（服务端）和浏览器插件（客户端）为例。

:::note
注意：本文并不是翻墙教程，本方案的性能也可能因为 ICMP 性能限制导致开网页慢得像蜗牛

本文仅作研究性分享，请勿用作不当用途，遵守校园网管理规定。
:::

---

## 1. 服务端配置 (VPS)

首先，我们需要在服务器上运行服务端，并设置访问 key 保证隧道的基本安全。

1. **启动 PingTunnel 服务端：**
   使用 `-key` 参数设置验证密钥：

```bash
sudo ./pingtunnel -type server -key 114514
```

2. **部署 HTTP 代理服务：**
   安装 Tinyproxy 并设置其监听在 `8888` 端口：

```bash
# 安装 (以 Ubuntu 为例)
sudo apt install tinyproxy -y
# 修改配置允许本地连接
vi /etc/tinyproxy/tinyproxy.conf # 确保 Allow 127.0.0.1 未被注释
sudo service tinyproxy restart
```

---

## 2. 本地客户端配置

由于校园内经常装的是希沃白板系统，故接下来用 Windows 来演示。

:::note
注意：在 Windows 上运行需要以“管理员身份”打开 CMD/PowerShell。
:::

```bash
# -type client: 客户端模式
# -l :1080: 本地监听 1080 端口
# -s: 你的 VPS 公网 IP / 域名
# -t: 目标指向 VPS 本地的代理端口 (8888) (是VPS的而不是电脑的！)
# -key: 必须与服务端一致
# -tcp 1: 开启 TCP 转发
pingtunnel.exe -type client -l :1080 -s [你的VPS_IP] -t 127.0.0.1:8888 -tcp 1 -key 114514
```

---

## 3. 浏览器设置与测试

现在，你的本地 `1080` 端口就是一个通往校外世界的“入口”了。

下面有两种方法设置系统代理：

1. **设置浏览器代理：**
   使用 **ProxySwitchyOmega** 插件或系统自带代理设置，添加一个 HTTP 代理：

- **代理服务器：** `127.0.0.1`
- **端口：** `1080`

2. **设置系统代理**
   通过 Windows 设置中的“代理”选项，启用手动代理设置，填写同样的信息。

   此时，即使你没有在校园网弹窗中登录账号，只要能 Ping 通服务器，浏览器应该就能正常访问网页了。

---

## 可能需要注意的点 💡

- **禁用系统 Ping 响应：** 为了让隧道更稳定，建议在服务器上运行：
  `echo 1 > /proc/sys/net/ipv4/icmp_echo_ignore_all`
  这能防止内核干扰 PingTunnel 处理 ICMP 包。**（AI 生成的建议，但本人目前不需要操作）**
- **关于 -key 参数：** 这是一个非常简易的鉴权，主要作用是防止扫描器乱入，务必保证两端一致。如果你把服务器设在海外，估计 GFW 直接一条龙（
- **速度与稳定性：** ICMP 并不是为大数据量设计的，因此延迟（Ping 值）会比直连高很多。
- **防火墙设置：** 确保服务器的防火墙允许 ICMP 流量通过。
