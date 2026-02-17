---
title: 被浏览器扩展给扒光了
published: 2026-02-15
description: 刚刚创建好的子域名，明明没告诉过别人，为什么过两天就出现在Censys和Archive.org上了？
tags: [安全, 浏览器, 扩展, 隐私, 信息安全]
category: 安全
draft: false
---

## 插件还是“间谍”？从 crxMouse 偷跑数据谈浏览器扩展的安全重灾区

有点恐怖，前几年装的个鼠标手势扩展因为自己域名被 archive.org 爬才发现一直在后台偷偷上传数据

当初发现异常，是因为通过 Chrome 自带的网络抓包工具 `chrome://net-export/` 监测到，在打开普通网页时出现了莫名的流量。

顺藤摸瓜排查下来，元凶正是 **crxMouse**在搞。

最恶心的地方是这东西还会静默推送配置你关掉了数据共享他偷偷直接云控帮你打开谢谢你

也许是巧合，不过确实找到了有这么一个流氓软件，稍微调查了一下，发现这些软件都和一家公司 Big Star Labs 搞的

> Big Star Labs（全称 Big Star Labs LP）是一家因分发含有恶意行为的软件插件而备受争议的技术公司。该公司最知名的产品是浏览器扩展程序 Poper Blocker。
>
> 大规模监听：通过多款插件秘密窃取全球超过 1100 万用户的完整浏览历史及点击记录。
>
> 贼喊捉贼：利用 Poper Blocker 等“隐私保护”工具作为诱饵，实则行监控之实。
>
> 隐匿行踪：将隐私条款制作为图片以逃避引擎索引，并使用多个虚假开发者账号分发软件。
>
> 数据变现：疑似将高度私密的个人上网数据打包出售给类似 Similarweb 的数据情报公司获利。
>
> 去匿名化风险：虽然声称匿名，但其收集的详细路径和 ID 足以对用户进行精确的身份溯源。

脚本用 [CrxViewer](https://robwu.nl/crxviewer) 反编译后，经常会出现出现 yodules (modules 的拼写变体) 和 proconstantinator (拖延，意味着会趁用户不注意悄悄打开数据收集)，仔细一想名字确实听讽刺的。

他们还用 Google Analytics 和 GrowthBook 进行多维度数据收集，这些数据跑到哪里是不知道了，大概估计我刷过的推和看过的 Github Repo 都被扒得一清二楚了（悲）

![分析](https://s.nichijou.moe/thorium_L7GufK8UR7.png)

虽然不喜欢鼠标手势（感觉都是老一辈了再用），但因为 24 年那会 smartUp 被爆出来被植入后门，大概是因为强迫症想要再装个 alternative 结果又掉进了另一个坑里（~~坏习惯别学~~

现在谷歌扩展商店全都是垃圾了，搜索算法甚至没自己的谷歌好，一大堆商业化气息，独立开发者写的基本搜不到，现在尽是是下载量很高但是没啥评论的和下载量很少又没官网的，感觉咕鸽真就没把扩展放心上

现在又有人趁着 AI 发了一堆 malware 上去，盗取用户聊天记录：[^1]

找下来发现 52 破解的站长还开源了一个插件: [FlowMouse](https://github.com/Hmily-LCG/FlowMouse) ，现在还在维护，喜欢鼠标手势的可以去看看（个人当然没啥兴趣现在

即便 ClickFix，FileFix 和乱入的广告你能防住，下载了恶意扩展是 Adblock 都防不住的，所以还是要务必检查自己的浏览器有没有一些神秘小插件。

即便是在 2026 年，这种情况依然在进行，现在的 Chrome Webstore 恶意扩展，NPM 供应链投毒，VSCode 仍然非常狂。 [^2]

![报告](https://s.nichijou.moe/thorium_XQlqxpbG4E.png)

## PS

在查资料的时候还发现了个小项目，~~看标题都知道是啥<https://github.com/classvsoftware/spy-extension/tree/master/src>~~

偷偷 vibe 了个浏览器扩展审查工具，到时候发出来看看，可以先蹲一下 x [^3]

![工具](https://s.nichijou.moe/thorium_rnTyDDSP6I.png)

拓展阅读:

<https://github.com/qcontinuum1/spying-extensions/stargazers>

<https://github.com/HEXXDECIMAL/DISSECT>

<https://www.oakleys.org.uk/blog/2018/02/finding_the_privacy_leak_crxmouse>

（btw 我也正在考虑试试跑在软路由的 DNS + 代理过滤方案）

[^1]: <https://www.ox.security/blog/malicious-chrome-extensions-steal-chatgpt-deepseek-conversations>
[^2]: <https://hackread.com/chrome-extensions-harvest-browsing-data-37m-users>
[^3]: <https://github.com/hexadecimal233/extension-scanner>
