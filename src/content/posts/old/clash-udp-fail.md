---
title: 【经验分享】在Clash内直连UDP（玩联机必会）
published: 2024-04-01
description: ""
image: https://s.nichijou.moe/old-images/202407121558375.png
tags: []
category: 旧作
draft: false
---

## 1. 起因

昨日晚某人在打开 VRC 的时候之前发现无法连上 VRC 的服务器（之前其实没准备做其实因为没被墙），随即打开浏览器一顿操作后学会了写 Clash 的配置（虽然只是接触了一点皮毛

## 2. 解决

因为之前用 Clash 的时候一直都没注意过 Clash 配置是咋回事，就当加了个全局代理的 V2RayN 用 =w=

首先要会导入订阅，这就不多说了吧（

再确保你用的是全局代理模式（TUN），仅 HTTP 的话 Clash 碰都碰不到 UDP 的流量

好的，简单来说，咱们只需要把 UDP 给禁用掉就行了（我机场 UDP 连不通）
先找一个订阅转换工具（这里用的是[ACL4SSR](https://acl4ssr-sub.github.io/)，不了解有没有风险啥的）

接着打开进阶模式，在更多选项里把启用 UDP 给他勾去掉，这样 UDP 流量就会走直连啦~

[![thorium-D65-Ba-S2-HEr.png](https://i.postimg.cc/kXbQwTGq/thorium-D65-Ba-S2-HEr.png)](https://postimg.cc/N5Q2j7Rz)

## 3. 结语

这没什么技术含量，希望这篇文章能帮到大家，不在配置代理上浪费过多时间 ヾ(≧▽≦\*)o

## 更新：24/07/12

现在已经用上 Biubiu 加速器了 <https://www.biubiu001.com/>

纯粹看广告刷时长

用那个 24 亿身份证可以注册

> 其实**大多数**游戏的实名都是虚晃一枪，输一个就给过了，根本不查
>
> 有人说网易要扫脸，还是我朋友告诉我不用扫脸的，那些人都天天在那乱扯
>
> 如果还是觉得有风险的就别用吧
