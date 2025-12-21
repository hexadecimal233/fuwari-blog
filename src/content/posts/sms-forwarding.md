---
title: DIY 短信转发模块
published: 2025-12-20
description: "低成本用esp32实现短信转发"
tags: ["DIY"]
category: DIY
image: https://s.nichijou.moe/photo_2025-12-21_10-24-15.jpg
---

之前刷到个 DIY 转发 ESP32 转发短信的项目，功耗也特别低，搞的 giffgaff 卡也因为主播就一台机子~~被插满了~~，现在就整台玩一玩

::github{repo="chenxuuu/sms_forwarding"}

教程视频：https://www.bilibili.com/video/BV1cSmABYEiX

## 刷前准备

准备原材料：

- 硬件设备：**ESP32C3 Super Mini** + **ML307R-DC 开发板** + **4G FPC 天线**（结果发现还没包邮
- 焊接工具 + 面包板
- 杜邦线（或者是跳线）

![](https://s.nichijou.moe/%E6%97%A0%E6%A0%87%E9%A2%98-2025-12-21-0111.svg)

**焊接开发板的时候千万不要直接把 ESP 直接叠上去！！！！！**
在 ESP 的 GND 右边还有一个 3.3V 的引脚，没有想到更好的办法，只好用杜邦线连接起来（或者插在面包板上），这么丑就是没办法（摊手）

视频直接把焊在模块上的直接掰弯引脚给接开发板上去了（然后疯狂囤板子 x）

这次买的是 ESP32-C3 的 Supermini，因为没有写入固件，会不断重启，导致刷入永远失败，所以需要让 ESP 进入串口 Bootloader 模式.

根据 Reddit ~~国际版贴吧 x~~，可通过将 GPIO9 拉低，来进入 Bootloader 模式，或者不短接 GPIO9，同时按下 BOOT 和 RST 按钮，随后松开 RST 按钮，即可进入 Bootloader 模式，这时可直接松开 BOOT 按钮。[^1]

[^1]: https://www.reddit.com/r/esp32/comments/ivtdzf/esp32_keeps_disconnecting_and_reconnecting_from/

然后用 Arduino IDE 打开项目工程文件，下载所需的库与工具链[^2]。

> 最后 ESP32 的工具链装在用户目录下又吃了我 C 盘 3G 多空间 😭

[^2]: https://github.com/chenxuuu/sms_forwarding?tab=readme-ov-file#%E8%BD%AF%E4%BB%B6%E7%BB%84%E6%88%90

## 启动！

准备完成后，打开`wifi_config.h`文件，配置 WiFi 信息（需选用 2.4G 网络），编译并上传到开发板。

上传成功后打开串口监视器，里面就是我们控制面板的地址

```bash
ESP-ROM:esp32c3-api1-20210207
配置已加载
连接wifi
<WiFi名称>
wifi已连接
IP地址: 192.168.X.X
正在同步NTP时间...
NTP时间同步失败，将使用设备时间
HTTP服务器已启动
模组AT响应正常
CNMI参数设置完成
PDU模式设置完成
CGATT已附着
⚠️ 请访问 http://192.168.2.145/ 配置系统参数
Debug> OK
```

## 其他配置

若要配置邮箱通知，可以加上 SMTP 服务器的相关配置
