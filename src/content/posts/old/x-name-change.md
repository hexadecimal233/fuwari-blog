---
title: 记一下黑叉改名失败解决方案
published: 2024-04-01
description: ""
image: https://s.nichijou.moe/old-images/202407121539053.webp
tags: []
category: 旧作
draft: false
---

# 一、 万恶之源

自蓝鸟黑化之后，整个推都被上了些逆天的东西，我~~大概是因为选择困难犯了~~想重新改下平台上的 ID（这次！应该！不会！再改了！）（~~确信~~）

改着改着改到推了，

换梯子？换了几个没用

换用 app？还是没用

账号风控？没被限制

马斯克我测你 🐎

# 二、原因

大部分原因给排除了，用 Ctrl+Shift+I（别问我为什么不用 F12，问就是用惯了）打开控制台

网络那块发现每次点击保存的时候会发送一个`update_profile.json`的请求（没有截图）

又在外网上随便找了篇教程，在(清了 Cookie 后重新登陆)[https://twittercommunity.com/t/display-name-change-limit/202585/5]后，发现毫无卵用

又注意到请求旁边还带了个 Service Worker 来搞什么勾八？

# 三、问题解决

于是我就把 Service Worker 从控制台狠狠地删除了

![](https://i0.hdslb.com/bfs/article/7c046e122cab2daa1b32e0d03807f9e2174927495.png)

· · ·

我又试了一次改名，发现可以改名而且成功！（虽然不知道是什么原理但是大佬们可以来研究一下）

这下终于找到原因了，才知道不用 Service Worker 都能成功改名，你推搞个 Worker 来监控请求什么意思都懂（目前只知道是用来推送的）

然而触发了验证，重定向弹出来一个 captcha，在经过了一题的折磨（bushi，雀食简单）后，重定向会主页了，同时发现我的用户名已经改好了，没有跳回去

![](https://i0.hdslb.com/bfs/article/3431efcd6ab39456596ffa55f8ab6fea174927495.png)

> \*顺便再宣传一下我的推~~其实也没啥好看~~ [澪-LLKawi](https://twitter.com/llkawi_)
