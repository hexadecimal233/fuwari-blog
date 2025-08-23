---
title: Cloudflare DNS 托管+Github Pages HTTPS加密避坑
published: 2024-04-01
description: ""
image: https://s.nichijou.moe/old-images/202407121555061.png
tags: []
category: 旧作
draft: false
---

## 起因

最近在 NameSilo 上买了个自己的域名给托管到了 Cloudflare 上，准备当博客，开个自己的个人网站

博客部署完后，准备给自己的简介网站弄个域名，pages 弄上去

就在 Cloudflare 上添加 CNAME 记录指向到我的[Github Pages 域名](https://fiz-victor.github.io)了

特别注意：我这时把 CF 的代理勾选了

## 事发过程

网站啥的都设置好了，发现没有 https 协议（）

奇怪了，gh pages 上的 dns check 也过了，那个 https 勾选框灰了（恼）

`Unavailable for your site because your domain is not properly configured to support HTTPS.`

同时 Cloudflare 访问 HTTPS 的加密页面也会因为重定向次数过多而寄掉

解决方案：把 SSL 证书 `灵活`换成 `完全`

之后，我尝试将 CNAME 记录改成 A 记录指向 Github 的 IP，也一样报错

```
@    A    185.199.108.153
@    A    185.199.109.153
@    A    185.199.110.153
@    A    185.199.111.153
```

后来查到 cloudflare 会自己把根域名下的 cname 转成 a 记录，就这样折腾了大半个小时就结果一无所获

## 解决方案

这时我想起来之前配置博客弄 https 证书的时候成功在关闭代理的时候把 gh 的证书加上去了，我就开始怀疑是不是 cloudflare 代理的证书影响了 gh 证书检查的功能

我关闭我个人简介站点的加速之后发现 gh pages 已经开始获取证书了：

`Unavailable for your site because a certificate has not yet been issued for your domain.`

等了大概半分钟之后，原本变灰的按钮已经可以被选中了

开启之后简介网站已经可以强制 https 访问了

总结一下，Github 和 Cloudflare 的加速就过不去，cf 加速开了 gh https 的证书就死活套不上去，要把那个域名的 cf 加速给关闭才加得上去

## 7.30 更新

为了证书可以顺利续期，需要在 cf 面板里加一些规则：

- 缓存规则：URI 路径等于/.well-known/\*，选择绕过缓存
- 页面规则：同上，把能关的都关了

配置好以来都未出现过掉证书的现象，有相同问题的小伙伴都可以参考一下
