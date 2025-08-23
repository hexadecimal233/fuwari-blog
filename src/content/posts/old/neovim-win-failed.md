---
title: 【水】Neovim装不上竟然是......
published: 2024-04-01
description: ""
image: https://s.nichijou.moe/old-images/202407121530914.png
tags: []
category: 旧作
draft: false
---

哈喽又是我，最近看到 Neovim 的一个视频[男人减速带：Neovim 代码编辑器](https://www.bilibili.com/video/BV1Mx4y1H7qd/)

是的，我装完 neovide 之后开心地打开，然后开心地寄了 wwwwww

...

具体表现就是打开 Neovim 瞬间卡死（关 checker 更新检查器的了想打命令也卡住），CPU 占满一个核

我一开始以为是 Neovim 的问题，升了 Nightly，后来又想是 GCC 的问题，升级了 GCC（我这时候就该把 GCC 给卸了的）尝试无果后又在 Github 上开了 Issue，开发者也一头雾水，我 tm 就？？？？了

整着整着想打开 x64Dbg 出来看看有没有报错，结果里边弹出来 Cygwin...

```plaintext
跳过了不支持的调试类型 IMAGE_DEBUG_TYPE_CODEVIEW （在模块vim.so中）……
在模块 vim.so 中没有找到任何支持的调试类型！
DLL已载入： 00000004D15D0000 C:\Users\User\AppData\Local\nvim-data\lazy\nvim-treesitter\parser\vim.so
跳过了不支持的调试类型 IMAGE_DEBUG_TYPE_CODEVIEW （在模块cygwin1.dll中）……
在模块 cygwin1.dll 中没有找到任何支持的调试类型！
DLL已载入： 00007FFFDD8D0000 D:\cygwin64\bin\cygwin1.dll
调试字符串： "cYgFFFFFFFF 7FFFDDAFB0C0 0"
EXCEPTION_DEBUG_INFO:
           dwFirstChance: 1
           ExceptionCode: E24C4A02
          ExceptionFlags: 00000081
        ExceptionAddress: kernelbase.00007FF8C33DEE5A
        NumberParameters: 0
第一次异常于 00007FF8C33DEE5A (E24C4A02)！
EXCEPTION_DEBUG_INFO:
           dwFirstChance: 1
           ExceptionCode: C0000005 (EXCEPTION_ACCESS_VIOLATION)
          ExceptionFlags: 00000000
        ExceptionAddress: cygwin1.00007FFFDDA1A3A7
        NumberParameters: 2
ExceptionInformation[00]: 0000000000000001 Write
ExceptionInformation[01]: 0000000000000000 Inaccessible Address
第一次异常于 00007FFFDDA1A3A7 (C0000005, EXCEPTION_ACCESS_VIOLATION)！
EXCEPTION_DEBUG_INFO:
           dwFirstChance: 0
           ExceptionCode: C0000005 (EXCEPTION_ACCESS_VIOLATION)
          ExceptionFlags: 00000000
        ExceptionAddress: cygwin1.00007FFFDDA1A3A7
        NumberParameters: 2
ExceptionInformation[00]: 0000000000000001 Write
ExceptionInformation[01]: 0000000000000000 Inaccessible Address
第二次异常于 00007FFFDDA1A3A7 (C0000005, EXCEPTION_ACCESS_VIOLATION)！
```

...真的，之前用过 MingGW，Msys2，现在又换回 Cygwin，之前编译明着给你报错现在暗戳戳把我整得脑溢血...其实也有 nvim 的锅，一直在那报 0xc0000005 还让程序继续跑下去...这样的结果就是卡死，崩溃不好吗？😡😡kiss-translator

之后用卸了 GCC，Cygwin 还在，再清`nvim-data`的时候（就是把编译好的内容给删了，）已经不报错了,好耶~

虽然但是，我搞 Neovim 这三天里，几乎花了好多精力在这上面了，so wtf am i doing!？解决问题方法多，摸索的时间成本也多，哎。。。最近几天学 react 做聊天系统的计划也没时间搞了...

所以说，我为什么要碰 Neovim！！？？？？ヽ（≧□≦）ノ

...

...

...

PS: 最后还是成了玩具，再也没碰过（

...

....
