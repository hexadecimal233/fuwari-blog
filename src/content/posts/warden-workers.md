---
title: 在 Cloudflare Workers 上部署 Bitwarden
published: 2026-01-11
description: to be added
tags:
  - Cloudflare
  - Bitwarden
  - 部署指南
  - Workers
category: 系统运维
---

## 前言

之前我的 Vaultwarden 实例是运行在自己的 NAS 上的，因为公网 IP 变得越来越不好搞了，用的 FRP 内网穿透国外节点的延迟大，运气差一点根本用不了，国内的又需要备案（就是那种不肯备案的人），我便想到了在云服务器上跑服务器 + 用带善人 Cloudflare 的数据库作同步的方案。但别急！随手查了一下，发现已经有佬写了 Workers 的版本

Serverless 的时代，来临力！（喜

原文：<https://www.nodeseek.com/post-575140-2>

## 部署教程

:::tip
关于数据备份的内容可以去看原文，下面只包含了关键步骤（部分 AI 生成）
:::

:::warning

1. 本项目不是 Vaultwarden 的兼容层，不保证安全性跟 Vaultwarden 持平
2. 本项目不提供设备管理功能
3. 如果发现安全性问题，请在 Github 中提交安全报告
4. 数据安全建议：使用不同的 Cloudflare 账户进行备份，避免单点故障

:::

## 1. 设置 Github 项目

1. Fork 项目仓库：https://github.com/qaz741wsd856/warden-worker
2. 进入 Action 页面，启用 Action
3. 点击 settings → Secrets and variables → Actions，准备添加 Repository secrets
4. 需要添加三个 secrets：

### 获取并添加 secrets

1. **CLOUDFLARE_ACCOUNT_ID**：

   - 进入 Cloudflare 控制台：https://dash.cloudflare.com/
   - 在浏览器地址栏中复制你的 account id
   - 在 Github 中添加为 `CLOUDFLARE_ACCOUNT_ID`

2. **CLOUDFLARE_API_TOKEN**：

   - 访问 https://dash.cloudflare.com/profile/api-tokens
   - 选择编辑 Worker 的模板
   - 添加 D1 的编辑权限
   - 创建 API 令牌
   - 在 Github 中添加为 `CLOUDFLARE_API_TOKEN`

3. **D1_DATABASE_ID**：

   - 进入 Cloudflare 控制台的存储与数据库 → D1 sql 数据库
   - 创建一个数据库
   - 复制它的 id
   - 在 Github 中添加为 `D1_DATABASE_ID`

## 2. 部署到 Cloudflare

此时点击上方的 Actions 选项卡，选择 Build 工作流点运行

Github Actions 会自动在数据库为空时建表，无需手动建表

### 配置环境变量与自定义域名

1. 当 Github Action 执行完毕后，在 CF 的 Workers 中找到 `warden-worker`
2. 进入 Worker 的设置页面，在变量和机密栏添加以下三个密钥，**在设置完变量后会自动重新部署，Github 无需额外再运行一遍**：

| 名称                 | 说明                                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| `ALLOWED_EMAILS`     | 允许注册的完整邮箱（支持通配符），用逗号分隔（例：your-name@example.com,\*@xxx.com），建议只填你自己的邮箱 |
| `JWT_SECRET`         | 随机长字符串（32 字节以上）                                                                                |
| `JWT_REFRESH_SECRET` | 随机长字符串（32 字节以上）                                                                                |

### 可选环境变量

- `DISABLE_USER_REGISTRATION`：控制是否显示注册按钮，默认为 true
- `IMPORT_BATCH_SIZE`：导入密码库时每次批操作的数据条数，默认为 30
- `PASSWORD_ITERATIONS`：服务端 PBKDF2 迭代次数，默认 600000，最小 600000
- `TRASH_AUTO_DELETE_DAYS`：回收站中的项目清理天数，默认为 30，0 代表永不清理
- `AUTHENTICATOR_DISABLE_TIME_DRIFT`：控制是否允许 TOTP 的时间 ±1 偏移，默认为 true
- `ATTACHMENT_MAX_BYTES`：单个附件的大小限制，单位字节，默认不限制
- `ATTACHMENT_TOTAL_LIMIT_KB`：单个用户的总附件大小限制，单位 KB，默认不限制
- `ATTACHMENT_TTL_SECS`：附件的上传下载链接的有效时间，默认五分钟

3. 在域和路由中添加自己的域名，测试网页是否能打开
4. 建议去域名配置页的安全性 → 安全规则里给 `/identity/*` 和 `/api/accounts/*` 添加速率限制规则（到时候请求被刷爆就没了）

## 4. 创建账户并导入数据

1. 如果已有 bitwarden 库，在电脑上把它导出为 JSON
2. 前端默认隐藏了注册按钮，可以：
   - 参考上一步的环境变量开启然后清空缓存
   - 直接访问 `https://你的域名/#/register` 注册

**注意**：创建账户时用的密码必须记住，没有任何方式能找回
