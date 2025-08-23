---
title: Windows配置GPG签名，Github展示Verified绿标
published: 2024-04-01
description: ""
image: https://s.nichijou.moe/old-images/images/24/11/e02b15961013a17078375e35a42b6dad.png
tags: []
category: 旧作
draft: false
---

## 前言

你是否羡慕别人的 Commit 旁有一个小绿标而自己旁边什么都没有？？（

嚎啊，今天和大家讲一下怎么样给 github 的提交签名。

Github 提交签名有三种方式，Github 自签（网页端操作自带），SSH 签名（要配置 SSH 密钥，没了解很深）和 GPG 签名（就是我们今天讲的）

所以说，这有什么用？

- 给你的 Commit 加上小绿标（Verified）
- 验证身份，让别人知道这不是别人冒名顶替提交上来的（比如说了 Linus 假删库那件事

  > 这里想到了之前看的一个视频：你公开了的 repo 再设私有，通过一些操作就能随意访问 branch 的内容[https://www.youtube.com/watch?v=EH3tenVGk60](https://www.youtube.com/watch?v=EH3tenVGk60%E2%80%B8)

不止 Git——他还能:

- 签名文件
- 文件的加解密
- SSH 身份验证
- ...

关于用途这方面今天不展开来讨论，今天就谈谈 Git 签名这件事，作为一个 Windows 用户，这些功能日常都不太会用到

> 没法把 Linux 装上实体机！家里就一台游戏本和一个台式，显示器还接到游戏本上了

## 正文

首先，我们要在系统上有 GPG，社区提供了 GPG4Win，一个在 Windows 上的 GPG 实现。

[https://www.gpg4win.org/get-gpg4win.html](https://www.gpg4win.org/get-gpg4win.html)

下载安装，没啥好说的。

安装完，这里默认会给你安装上 Kleopatra（一个 GPG 的 GUI）并且默认打开

第一次打开没任何证书的时候，会提示你是否创建新的证书，这里就选择新建 OpenPGP 密钥对，填入名称和邮箱（算法细节可以自行更改），会来到到下面这个页面：
![https://s.nichijou.moe/old-images/images/24/11/43d5c6ac7d98735ef3aff81636f5a23d.png](https://s.nichijou.moe/old-images/images/24/11/43d5c6ac7d98735ef3aff81636f5a23d.png)

我们需要获取 GPG 的绝对路径：

```bash
$ where.exe gpg
D:\Program Files (x86)\GnuPG\bin\gpg.exe
```

然后为 Git 配置 GPG 路径：

```bash
$ git config --global gpg.program "D:\Program Files (x86)\GnuPG\bin\gpg.exe"
```

如果你想为一个项目的 commit 签名的话，就用 local：

```bash
$ git config --local commit.gpgsign true
```

反之，我给全局的 commit 都开启了签名：

```bash
$ git config --global commit.gpgsign true
```

接下来，我们要获取到 key 的 ID，然后让 Git 知道我们是那个身份去签名，注意一下，提交的邮箱和用户名必须要和 Git 配置（也就是`.gitconfig`）的名称一样！不匹配会导致 Unverified！

```bash
$ gpg --list-secret-keys --keyid-format LONG
```

![https://s.nichijou.moe/old-images/images/24/11/ace918349cc3332d650c7554719d1eac.png](https://s.nichijou.moe/old-images/images/24/11/ace918349cc3332d650c7554719d1eac.png)

这个 sec 后面是`算法/ID`，0530 就是我们想要的 ID 了

我们本地端的最后一步，配置 key id——`[GPG_KEY]`就是上面的 ID，配置单个项目把 global 换成 local 就可以。

```bash
$ git config --global user.signingkey "[GPG_KEY]"
```

之后我们的每个 commit 都是签了名的，push 上去，GH 说：

> 你谁啊？我不认识你！

喜提 Unverified~

![https://s.nichijou.moe/old-images/images/24/11/c49836354b7d5f4e604120853cf9c354.png](https://s.nichijou.moe/old-images/images/24/11/c49836354b7d5f4e604120853cf9c354.png)

要想绿标，你还得在 Github 上配置自己的 GPG 公钥，可以通过[https://github.com/hexadecimal233.gpg](https://github.com/hexadecimal233.gpg)查看。（想看别人用户名自己改啦）

导出公钥有两种方法，第一种，也是我当时的方法（纯命令行）：

```bash
$ gpg --export --armor soda114514@proton.me
```

![https://s.nichijou.moe/old-images/images/24/11/5af1d3a425527e6c48556d7919f4f5c8.png](https://s.nichijou.moe/old-images/images/24/11/5af1d3a425527e6c48556d7919f4f5c8.png)

然后第二种就是 Kleopatra，选择导出证书就是公钥，复制到 Github 里完全可用，十分甚至九分的方便啊!（

最后的最后！我们把公钥传到 Github 上~

[https://github.com/settings/keys](https://github.com/settings/keys)

这样自己 Push 的 commit 也就有绿色的 Verified 了！

## 结语 & 闲聊

其实写这篇文章是希望那些想要小绿标的大家能降低查文章的时间成本，想说的就这么点了（

我每次找 powershell 之前敲的代码都很费劲，用 everything 找 PSReadline 所在文件夹，再点开历史文件`(C:\Users\User\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt)`

> 笔记本上的 win11 还要被塞爆了，安装包还分开好多文件夹保存没删，编曲的音源上百 G，这不塞满才怪，还有我浏览器的收藏夹好乱（
>
> 不知道如何把子文件夹放到最开头显示，还堆着之前没分类时候塞的巨大收藏夹（YES，scj3，名字够随意）

![https://s.nichijou.moe/old-images/images/24/11/9250fb68119b09815f3a334b2dd05071.png](https://s.nichijou.moe/old-images/images/24/11/9250fb68119b09815f3a334b2dd05071.png)

> 参考：[https://www.git-tower.com/blog/setting-up-gpg-windows/](https://www.git-tower.com/blog/setting-up-gpg-windows)

## 12.3 Update

Push 的时候卡住了...

```bash
gpg: keydb_search failed: No agent running

gpg: skipped "CF8F4C11552208D5": No agent running

gpg: signing failed: No agent running

error: gpg failed to sign the data

fatal: failed to write commit object
```

看到<https://superuser.com/questions/1075404/how-can-i-restart-gpg-agent/1150399#1150399>说，重启 GPG agent 可以修复，试了最后也有效果

```bash
$ gpgconf --kill gpg-agent
```

## 1.30 Update

为了防止 keyserver 不知道什么时候突然失联建议在配置里边加上 `keyserver hkps://keyserver.ubuntu.com` 手动设定？
配置目录：
Win：`%APPDATA%\gnupg\gpg.conf`
Linux: `~/.gnupg/gpg.conf`
