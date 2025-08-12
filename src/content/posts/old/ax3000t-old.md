---
title: 小米AX3000T刷OpenWRT中遇到的问题和解决方案
published: 2024-04-01
description: ""
image: https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/7479ad5b1765f906bc5237316d60d362.png
tags: []
category: 旧作
draft: false
---

> 去年入的路由器，上周试着折腾一下，搞好了公网 ipv6，但现在一到五根本没空写博文 💦

# 1. 刷入 OpenWRT

## 简介（转）

CPU：联发科 FiLogic 820 系列的 MT7981，12nm 双核 A53 架构，12nm 工艺制程，1.3GHz 主频

内存：256mb+128MB 的存储组合

网络：4 个自适应千兆口（好评），天线数量 4 根（5G 频段 2+1 配置）支持 160MHz 超大频宽，支持近磁场 NFC 连接 wifi（一次也没有用过。。。）

优点：活动价格保持 15X 性价比很高，轻松跑满千兆，mesh 组网很方便，白色外观设计小巧精致，很简洁
缺点：部分人反应断流严重，我不玩游戏没有感触，断流

## 刷入

网上关于小米刷 OpenWRT 的教程多的去了，自己去百度一下就行。

这里也提供两篇自己参考的文章：

[小米 AX3000T 保姆级免拆刷 openWrt 教程以及排坑指南](https://post.smzdm.com/p/admxql3d/)

[OpenWrt：刷机小米 WR30U（AX3000T）](https://blog.csdn.net/jgw2008/article/details/135602964)

用到的计算 SSH 密码的工具：[https://miwifi.dev/ssh](https://miwifi.dev/ssh%E2%80%B8)

固件地址

> PS:
>
> - 第二篇文章里的解锁 SSH 方法我没实测过，我的机子是用上面那篇文章的方法解的
> - 到时候刷 uboot 时一定要用网线连+手动配置 IP！
> - 刷 uboot 的时候可能不止五分钟，千万不要断电！刷完灯会灭，然后重启！
> - 管理后台的默认用户名是`root`，密码是`password`！

![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/8dc1f22979ca7e896ad404d82bb03e0e.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/8dc1f22979ca7e896ad404d82bb03e0e.png)

# 2. 配置 OpenWRT

刷完 OpenWRT 并不代表配置完了，要想用上公网 IPv6 和无线还有几个步骤！

## 配置无线

在**网络->无线**下面把两个网络设置为开启，配置个密码就可以正常使用了。（支持双频合一）

![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/e6f91433a25aad037cc2214a65d39d5f.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/e6f91433a25aad037cc2214a65d39d5f.png)

## 杂七杂八的设置

设置网络前我是先更新了下软件包（**系统->软件包**），**服务**里的**DDNS-GO、UPNP**，

## 配置 WAN 口和 LAN 口

> 我家里是电信的光猫做主路由，小米做旁路由的方案，所以和直接 PPPOE 拨号的方案有些许不同！

由于 OpenWRT 默认不支持自动识别 Wan 口，所以我们得手动将网线插入离 Reset 口最进的默认 Wan 口！！！插错口会像我一样无法上网！！！！

（听网上说如果之前改过默认 Wan 口了的话需要插在改过的网口上，没刷机前就是智能识别的可以不管）

接下来需要更改的设置只会在**网络->接口**里面！！

### LAN 口

OK，为了不让局域网使用内部使用自己的 IPv6 地址，首先我们需要清除**全局网络选项**里的 ULA 前缀，不分配私网 ipv6 地址。
![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/16c24d5f622d90e2f7c8bb706e80dd38.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/16c24d5f622d90e2f7c8bb706e80dd38.png)

接下来因为我电信路由的网关地址已经是`192.168.1.1`了，为了不产生冲突，这里需要在**LAN**里设置一个新的 IP 地址。（只需要设置静态地址就行了，网关和广播正常会自动识别）

![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/901f35540fc8ba65d450d1a7f914ced9.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/901f35540fc8ba65d450d1a7f914ced9.png)

DCHP 这里在高级模式里打开强制（不记得原来是开着的还关着的了就提一下），在 IPv6 设置里全部选择中继模式，如图。

![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/22bec7c9ea8f4594082435c9ce5325da.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/22bec7c9ea8f4594082435c9ce5325da.png)

![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/0f703a877b8e5a09bf49a1e5701d8e34.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/0f703a877b8e5a09bf49a1e5701d8e34.png)

高级设置里打开使用内置的 IPv6 管理，为什么写上去，原因同上。
![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/aa5d3e16e0fc352d15afb60b0d9b0084.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/aa5d3e16e0fc352d15afb60b0d9b0084.png)

### WAN 口

配置 IPv4 的 WAN 就不用我多说了吧，咱们今天稍微讲一下 WAN6 的配置。

因为我这个版本的固件缺了点设置，那些别的教程里的的设置在我的 GUI 上没有（）所以我们就要用到配置文件来改 DHCPv6 的设置！

先确保一下 WAN6 的配置是和图里的一样的：
![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/573f4944d875851f7f529bc7dea5ff56.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/573f4944d875851f7f529bc7dea5ff56.png)

现在也是最后一步！最后找到`/etc/config/dhcp`，连上 SSH 用 vi 编辑文件，找到 DHCP 关于 wan 和 wan6 的两项，把原来的删掉，用下面两个替换：

```
config dhcp 'wan'
        option interface 'wan'
        option ignore '1'
        option dhcpv6 'disabled'
        option ndp 'relay'
        option ra 'relay'
        option master '1'

config dhcp 'wan6'
        option dhcpv6 'relay'
        option ra 'relay'
        option ndp 'relay'
        option master '1'
```

修改后，重启 network 服务，保险点可以重启一下系统，但有时候我们要手动刷新一下 IPv6，有些时候系统不会马上获取到。（[教程](https://hksanduo.github.io/2020/03/13/2020-03-13-openwrt-set-ipv6/)）

想要测试 ipv6 连通性话可以用[www.test-ipv6.com](https://www.test-ipv6.com/index.html.zh_CN)（梯子需要关！！有些机场自带 IPv6 的线路！）图里应该是 V4 的测试地址被墙了，然后我成了大半个互联网都上不了的人（

![https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/a90aa4eec865d985c4d9122c0d1eb4b5.png](https://mirror.ghproxy.com/https://github.com/onlyra1n/image-bed/raw/master/images/24/9/a90aa4eec865d985c4d9122c0d1eb4b5.png)

参考：

[OPENWRT 里面设置 IPV6 方法](https://kzpu.com/archives/4617.html)，[OpenWRT 配置 ipv6](https://blog.csdn.net/qq_38073913/article/details/136069711)，[旁路由 IPV6 设置（校园网 NAT6）](https://blog.csdn.net/m0_46247741/article/details/140648308)，[openwrt 配置 ipv6](https://hksanduo.github.io/2020/03/13/2020-03-13-openwrt-set-ipv6/)

# 3. 结语

现在这样就算配置完了一个能获取到公网 IPv6 的 OpenWRT 了，但因为这个版本的固件自带的插件并不多，而且我这里也装不上任何软件包，所以最后如果要加软件包的话是不是得自己编译了（？

体验算好，DDNS 还在研究，离家里云又近一步！
