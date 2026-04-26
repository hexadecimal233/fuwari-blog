---
title: Windows 11 25H2 安装WSL与Docker的教程
published: 2077-02-33
description: 在 Alpine WSL 中徒手搭建容器环境，避免使用 Docker Desktop 的问题。
tags: [容器, Alpine, WSL2]
category: 技术
draft: false
---

## 引言

由于最近开始搞起了 Docker 开发，介于手头上没有台装了 Linux 的电脑，所以我决定在 Windows 上搭建一个容器环境，不过路上有一些坑。

## 卸载后的余烬

之前因为 c 盘空间吃紧所以把 wsl 给卸了，现在突然想装却突然装不回来了。

之前想装 Docker Desktop，但是看见 600MB 的安装包之后就畏惧了（~~Electron 坏事做尽~~）

于是转而选择自己装 wsl distro。我运行 `wsl --update`，结果却抛出一个奇怪的错误：

```powershell
$ wsl --set-default-version 2
wsl: WSL 安装似乎已损坏 (错误代码： Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG)。
按任意键修复 WSL，或 CTRL-C 取消。
此提示将在 60 秒后超时。
没有注册类
错误代码: Wsl/CallMsi/Install/REGDB_E_CLASSNOTREG
```

尝试卸载后安装更新则发现不行：

```powershell
安装成功或错误状态: 1603。
```

## 解决方案

在 Github 上找到一篇文章，解释了相关错误， <https://github.com/microsoft/WSL/issues/10882#issuecomment-3688889886>，核心原因就是：

**Windows 11 24H2/25H2 上，WSL 必须通过 Microsoft Store / winget 安装，仅运行 `wsl --update` 是不够的。**

### 🎯 核心问题

在 **Windows 11 (24H2/25H2)** 上安装 Docker Desktop 后，WSL 更新失败，报错：

```
REGDB_E_CLASSNOTREG
```

### 🔍 根本原因

- 新版 Windows 11 **不再完整捆绑 WSL**
- 旧方法依赖 MSI 安装，但新系统中 WSL 在注册表层面**未正确注册**
- Docker 依赖 WSL 2，因此无法启动

### ✅ 唯一有效的解决方案

**关键步骤（缺一不可）：**

1. **启用功能**：`Windows Subsystem for Linux（适用于Linux的Windows子系统）` + `Virtual Machine Platform（虚拟机平台）`（重启）
2. **通过 Microsoft Store（或以下命令）安装 WSL**（关键）：

   ```powershell
   winget install -e --id Microsoft.WSL
   ```

3. **重启系统**（必须）
4. **设置 WSL 2 为默认**

   ```powershell
   wsl --set-default-version 2
   ```

5. **检查状态**

   ```powershell
   wsl --status
   ```

   应该显示 `Default Distribution: Alpine（你的 WSL 发行版）` 和 `Version: 2`

## 个人遇到的第二个错误

之前 MSI 安装时报错 1603，查阅日志后是发现注册表权限出现了问题（出现了！TrustedInstaller！）

```log
MSI (s) (54:2C) [15:36:46:247]: Product: Windows Subsystem for Linux -- Error 1406. Could not write value  to key \SOFTWARE\Classes\Directory\Background\shell\WSL.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.

Error 1406. Could not write value  to key \SOFTWARE\Classes\Directory\Background\shell\WSL.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.
Action ended 15:36:46: InstallFinalize. Return value 3.
Info 1403.Could not delete value  from key \SOFTWARE\Classes\Directory\Background\shell\WSL.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.
Info 1406.Could not write value  to key \SOFTWARE\Classes\Directory\Background\shell\WSL.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.
Info 1401.Could not create key: \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\IdListAliasTranslations\WSLLegacy.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.
Info 1401.Could not create key: \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\IdListAliasTranslations\WSLLegacy.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.
Info 1401.Could not create key: \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\IdListAliasTranslations\WSL.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.
Info 1401.Could not create key: \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\IdListAliasTranslations\WSL.  System error .  Verify that you have sufficient access to that key, or contact your support personnel.
Action ended 15:36:48: INSTALL. Return value 3.
```

本人通过强制提权改权限把有问题的下面三个注册表条目给删了：

- `\SOFTWARE\Classes\Directory\Background\shell\WSL`
- `\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\IdListAliasTranslations\WSLLegacy`
- `\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\IdListAliasTranslations\WSL`

删除后，以管理员权限重新安装 WSL 即可。

## 一条更硬核的路

修复 WSL 后，我本可以安安稳稳地使用 Docker Desktop。但我做了一个决定：

**不要 Docker Desktop，不要 GUI，纯命令行。**

于是，我开始在 WSL 中折腾 Alpine Linux。

### 为什么是 Alpin？

- 极致轻量，镜像只有 5MB 左右
- 资源占用极低
- 适合有洁癖的开发者

### 为什么不用 Docker Desktop？

- Docker Desktop 闭源，商业许可付费，安装包臃肿
- 桌面端行为是一个黑箱，可能出现无法预测的 Bug
- ...

### 但代价是什么？

- 使用 `musl libc` 而非主流的 `glibc`，部分预编译二进制可能不兼容
- 包管理器是 `apk` 而非 `apt`
- WSL 默认不运行 `systemd`，而 Alpine 使用 OpenRC，需要手动伺候

## 合：踩着坑走到终点

### 安装 Alpine

从 Microsoft Store 安装 Alpine Linux，然后点击打开，就是默认创建一个 Alpine Linux 的 WSL 实例了。

### 更新 Alpine

因为巨硬商店里的 Alpine Linux 镜像已经是 EOL 版本（3.17）了，而且 Docker.10..10.24 版本，所以需要更新到最新版本。

```bash
# 备份当前镜像源
sudo cp /etc/apk/repositories /etc/apk/repositories.bak
# 替换镜像源为最新版本
sudo sed -i 's/v3.17/v3.23/g' /etc/apk/repositories
# 更新目录
sudo apk update
# 升级所有可用的包
sudo apk upgrade --available

# 此时切到powershell重启系统
wsl --shutdown

# 检查 Alpine 版本
apk info -vv | grep alpine-base
# alpine-base-3.23.4-r0 - Meta package for minimal alpine base
# alpine-baselayout-3.7.2-r0 - Alpine base dir structure and init scripts
# alpine-baselayout-data-3.7.2-r0 - Alpine base dir structure and init scripts
```

### 配置 tuna 镜像源（加速）

```bash
sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories
apk update
```

### 安装 Docker

```bash
apk add docker docker-compose openrc
```

### 启动 Docker

```bash
# 手动启动（调试用）
sudo dockerd

# 创建 /etc/network/interfaces 文件（Alpine Linux 不默认有）
echo "auto lo
iface lo inet loopback" | sudo tee /etc/network/interfaces

# 配置网卡
sudo setup-interfaces

# 或配置 OpenRC 自启
rc-update add docker default
service docker start

# 如果运行 sudo service docker restart 报 Docker is starting 则可能需要重启系统
# WSL中， reboot / poweroff 可能无法正常重启，需要使用 wsl --shutdown 重启

# 普通用户没有权限操作 Docker，需要添加到 docker 组中（把 hexzii 替换为你的用户名）
addgroup hexzii docker

# 重启 WSL（在powershell 中执行）
wsl --shutdown

# 重启系统后，检查 Docker 版本
docker --version
# Docker version 29.1.3, build f52814d454173982e6692dd7e290a41b828d9cbc
```

:::tip
在安装过程中也遇到了一些问题，记录在这里。

- `sudo su` 密码错误，但 `su -` 正常

  - 解决方法：将用户添加到 `wheel` 组中 （不过我没成功）

    ```bash
    su -
    addgroup 你的用户名 wheel
    visudo # 取消注释 %wheel ALL=(ALL:ALL) ALL
    ```

:::

## 瞎编的结语 x

1. **WSL 不是虚拟机，也不是纯 Linux**。它的初始化系统、进程管理都有特殊性，不能套用物理机的经验。

2. **Alpine 很适合 WSL**，但需要接受它的“小众代价”。如果你追求稳定、不想折腾，Ubuntu 仍然是更省心的选择。

3. **`wsl --shutdown` 是最强的调试工具**。当你觉得环境已经乱成一团时，这行命令能刷新系统，让你重新开始。

4. **本机开发的话，用不着一直 `sudo` 跑 Docker**。把用户加进 `docker` 组，一劳永逸。

没有 Docker Desktop，没有后台 GUI 进程，一个纯净的 Alpine + Docker 命令行环境，运行在 Windows 11 上。
