---
title: Adobe2024全家桶(转载) + AE报错打不开解决方案
published: 2024-04-01
description: ""
image: https://s.nichijou.moe/old-images/202407121548083.png
tags: []
category: 旧作
draft: false
---

## 下载地址

Adobe 软件大全（包含 Win 系统和 Mac 系统各版本各软件，2015-2024 版本）

[阿里云盘](https://www.aliyundrive.com/s/q4svdbBj7Bx)

[百度网盘 提取码：2024](https://pan.baidu.com/s/1BvQv8c_m4uIJAJMuHPSODQ?pwd=2024)

选择需要的软件，根据自己的电脑配置选择版本。

## 避坑

不知道为什么下载下来的一键安装包解压会报错（用 alist 下载的），就装了 CC 版的，装完到了装 AE 的时候，用破解工具破解好，结果打开的时候弹错：

```
After Effects can't continue: Licensing Error.
Your subscription entitles you to Chinese Simplified product versions.
Please verify the language settings in your OS browser, and software download are set to Chinese Simplified.
If you continue to have installation issues, please contact support.
```

查一下，发现网上有好多和我一样报错的的，不知道为什么 PS 破解完就没报错，接下来说一下自己的解决方案（暂且没有更好的）

先打开区域与时间，非 Unicode 程序的语言把 UTF8 文打了勾的话会导致你开屏弹错，把勾取消掉，重启就行了

分析下来，你只要你 codepage 不是 936，而且装的还是中文的全家桶，不出意外你就打不开

> **个人评价：**
> Adobe 你这防盗版防的有点太那啥了吧？

> 本文转自：[https://mp.weixin.qq.com/s/sH_lM3h7teYVYK_pJ9yJRA](https://mp.weixin.qq.com/s/sH_lM3h7teYVYK_pJ9yJRA)

金秋十月，Adobe 2024 如约而至！

想必有很多网友都已经安装体验了，在前面已经分享了通过创意云工具在线下载安装方法和离线安装包激活安装方法，而**这一期再带来一键安装版**。

影视后期系统教学

自@vposy 宣布暂停之后（注：**微博才是@vposy 本人，其余平台打着 vposy 名号，均是冒牌货**），很多网友还是很怀念@vposy 版本，@vposy 版本区别于其它版就是在于一键安装。

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiag5U3t5zLSbwVNQgprPnPXcyBxyENcib3CeYp7AA2ZIRfV1RbibsEzhR7A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

一键安装：即内置了安装激活补丁，安装即激活的，不需要再手动去替换补丁。

此次更新一键安装版是来自俄罗斯老毛子 monkrus 大神，更新包括了：

- Adobe After Effects 2024 v24.0.0.55
- Adobe Premiere Pr 2024 v24.0.0.58
- Adobe Animate 2024 v24.0.0.305
- Adobe Audition 2024 v24.0.0.46
- Adobe Media Encoder 2024 v24.0.0.54
- Adobe Illustrator 2024 v28.0.0.88
- Adobe InCopy 2024 v19.0
- Adobe InDesign 2024 v19.0
- Adobe Photoshop LightroomV Classic 2024 v13.0.0.15
- Adobe Bridge 2024 v14.0.0.12
- Adobe Photoshop 2024 v25.0.0.37

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagicgwBDgnOYibbXqfUyricKhCEZpRjjl0ewuaYhArHSTx9LwE2eGiaK7gAg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## 通用安装方法

Adobe 2024 是大版本更新，故是不能直接覆盖其它 Adobe 2023 、2022 等版本，如果有安装旧版本，是两个版本可共存。

1. 下载好压缩包，解压，直接运行 Set-up.exe 即可安装
2. 如果初次安装，可以自行设置安装路径，安装目录不要有中文

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagicqxBWMiaNWdT9CfzWWOqQybpU8B3EHCgveHnB1icfGysUsOBUqXbUJZQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

安装完成了，不会自动在桌面创建快捷启动，可以在开始菜单里边看到应用，直接拖拽桌面即可创建快捷启动。

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagPYibOibVuhcekQs1zXMp2RRm5rulcib6mya9uSPzdbibDETALv5wVdTjww/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### PR 语音转字幕制作工具 Adobe Speech to Text v12.0

此次更新的 PR 是不带语音转字幕的语音包的，这里单独提供工具 Adobe Speech to Text v12.0，可以自定义选择语言类型安装。

#### 语音转文本支持的语言：

- 简体中文（普通话）Chinese Simplified (Mandarin)
- 繁体中文（普通话）Traditional Chinese (Mandarin)
- 英语 English
- 英语（英国）English (United Kingdom)
- 西班牙语 Spanish
- 德语 German
- 法语 French
- 日语 Japanese
- 葡萄牙语 Portuguese
- 朝鲜语 Korean
- 意大利语 Italian
- 俄语 Russian
- 印度 Indian

一样是直接运行 Set-up.exe 安装，可选择安装语言类似，默认路径安装。

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagAzztcu2IOOicLnwhhNMjNmomcRRa4IDhZ5dduyicCiaPGdHnr9Jcde85Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

默认语音包路径：

- C:\\Program Files\\Common Files\\Adobe\\Premiere Pro\\24.0\\SpeechESL

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiag1CW2FahB9reQbEfwrHAnmBAu0ls2v2BwaxwX14AzanfYIbX2uklThQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)
只安装简体中文

此外如果是通过 Creative Cloud 在线安装更新，也会自动安装语音包。

## Adobe 2024 Mac 版本

Adobe 2024 Mac 版本也有两种安装更新方法，一是创意云工具在线下载安装方法，二是使用离线安装包。

Mac 系统安装 2020-2024 全套 adobe 之前，一定要先安装好 Adobe Creative Cloud，因为从 Adobe 2020 版本开始重写了 Creative Cloud 模块，否则软件打开会报错，并且必须联网才能使用。

#### 安装 Adobe Creative Cloud

1. 有安装了，可以跳过，打开 AdobeCreative Cloud 文件，双击 Install 进入安装

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagrmOicaq9LGRRHpqIwP06VAK9JOYZX8ic10oD2MRL5qgiccKQwqbbSlsKQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

2. 点击继续，输入自己点击开机密码，点击好

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagNibuXHIic59B2KIz3GrwsPc81m07ybiaJ1Ciby5MLRmiaal2lTibqUq3vnibg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

3. 登录账号，点击继续（没有注册的可以自己注册一个，**注册的时候地区选择国外，可以选择美国**）

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagCGg81n6C9sx2PIHZuAKtgsZTKCficazPtsP8Xos41KUsUTZjXbsbbhg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

4. 等待安装完成，点击跳过问题！

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagIcdNEghyvv6frKpYPA5aavT6ib6vUM6vgM95B8DSqEmS8taicg1dGE4w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 创意云工具在线下载安装激活方法

在线下载安装方法，即通过创意云工具简称 ACC，通过 ACC 下载最新版本，需要适配激活工具对应的版本，如果 ACC 里边应用更新，就需要等激活工具更新。

此激活程序目前是 1.4 版本，可以一键激活多款 Adobe 软件，但注意需要对应以下版本，目前对应最新版本程序。

- After effects v24.0
- Premiere Pro v24.0
- Media Encoder v24.0
- Photoshop v25.0
- Illustrator v28.0.0
- Lightroom Classic v13.0
- Bridge v14.0
- InDesign v19.0.0.151
- Adobe Distiller v23.006.20320
- Adobe Acrobat v23.006.20320

具体使用方法是安装登录 Adobe Creative Cloud，在 Adobe Creative Cloud 下载应用是最新的，下载安装试用，建议都打开都试用一遍，然后再去运行一键激活补丁 Patcher。

此外，在线安装试用是比较慢，建议还是使用离线安装包安装激活。

#### 离线安装包

离线安装包即固定的安装包版本，激活补丁也是固定的，没有特殊情况，安装包是可以一直使用。

安装方法都是通用的，注：ARM 版本即 M1 和 M2 系列芯片电脑使用，Intel 版本是 Intel 芯片电脑使用，U2B 就是芯片都通用。

1. 安装 2023 Adobe 软件之前，一定要先安装好 Adobe Creative Cloud 创意应用软件（安装包在提供链接里边可单独下载），如果有安装好了，可以跳过此步。
2. 安装 AntiCC，有安装过可跳过此步（不安装这个可能出现-2700 报错）

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagKN0xUWtP1yd6lsmQHcXa0UusVQTgmL9n52Nr52NN2Uc6DCyWTDMtrg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

3. 点击 Install 即可默认安装，安装过程需要输入密码，输入自己电脑开机密码。（如果安装有遇到问题，可以看对应问题大全进行解决）

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPv0oEBUSQS5G90Q9P6yq7Svun5oMeRziaAAVZFBszCgjbgjUicZb7afNzwgGydRKK9YjAAbVmicr47Sg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

4. 运行激活工具，点击自定义勾选安装对应的激活补丁工具

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPv0oEBUSQS5G90Q9P6yq7SvUg9ev5pEhicMR54VBw2vvDHwDxuCJZgI4uZPCcx7u9hkNWj2frt5zug/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

5. 在启动台打开即可使用

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPv0oEBUSQS5G90Q9P6yq7SvpMvuQiacLEafygP3xwDu3COQiboYfqEeYyJsyPNibGQ8kmoibRXBWeImsg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

此外，PR 自带语音转文本功能，语音包体积很多占用不少空间，安装好软件之后，可以去对应路径删除。

Mac 系统语音包文件目录， /Library/Application Support/Adobe/Premiere Pro/24.0/SpeechESL

![图片](https://m.wbiao.cn/mallapi/wechat/picReverseUrl?url=https://mmbiz.qpic.cn/sz_mmbiz_png/VbpP8Aa9jPsQ2PSgQLlNiaicDBOxz1ALiagr83qHj3PeX1bYicJ8lt4L5Bz8Uqen5lqIvqLgLGIrLYa8AX7DzTZumQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

建议只保留 CMN 简体中文和繁体中文，对应所在文件夹 12.1.0.5，其余均可删除。

## Adobe 2023 Mac 版本

此外针对 2023 最终版本下载可能出现损坏问题，对安装包进行了优化，重新打包上传，如果工作所需建议安装 2022 或者 2023 版本。

## 总结

以上，就是 Adobe 2024 Mac 版本安装详细介绍，希望大家都能够免费安装上，后续软件也会继续更新，做到免费没有套路分享，可以转发给更多有需要的小伙伴！
