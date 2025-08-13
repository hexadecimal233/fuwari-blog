---
published: 2025-01-31
title: 某云神秘的追踪参数
description: ""
tags: [逆向]
category: 逆向
draft: false
---

逆向复健一下

之前发现网易云的分享参数里有名为`userid=xxxxxxx`的参数，里面是分享者的用户 ID，但在某一次更新后取而代之的是`uct2=xxxxx`，像是加密的样子：

PC 端：<https://music.163.com/playlist?id=8416733444&uct2=U2FsdGVkX18I0qiX738+CD1FdOh+w/CByAqGXtw/rt8=>

手机端： <http://163cn.tv/ByX1EOw>

虽然网上已经有现成的工具：[找到 TA](https://findsomebody.nclgclub.com/start)

但因为其并未公布算法，也可能存在数据安全的问题，就想自己动手造一个平替（）

两者都是链接，但其加密算法不同，因此可以通过 uct2 的不同特征来确定对方的分享平台和用户 ID，下面将分别解析一下两端的 uct2 算法：

## 手机端

手机端有时复制出来的是短链（<http://163cn.tv/>，会跳转，包含其他跟踪参数），而电脑端访问会跳转后地址栏闪过 uct2 后继续跳到没有 uct2 的网站，这里通过 curl 获取跳转目标：

```shell
$ curl -v http://163cn.tv/ByX1EOw
* Host 163cn.tv:80 was resolved.
* IPv6: (none)
* IPv4: 198.18.1.58
* Trying 198.18.1.58:80...
* Connected to 163cn.tv (198.18.1.58) port 80
* using HTTP/1.x
> GET /ByX1EOw HTTP/1.1
> Host: 163cn.tv
> User-Agent: curl/8.10.1
> Accept: */*
>
* Request completely sent off
< HTTP/1.1 302
< Server: nginx
< Date: Fri, 31 Jan 2025 07:58:59 GMT
< Content-Length: 0
< Connection: keep-alive
< Cache-Control: no-store
< Pragrma: no-cache
< Expires: Thu, 01 Jan 1970 00:00:00 GMT
< Cache-Control: no-cache
< X-Application-Context: application:8888
< Location: https://y.music.163.com/m/song?id=2668595321&uct2=%2BhNMiP%2FRMJxgbE5ka%2FgRkw%3D%3D&fx-wechatnew=t1&fx-wxqd=c&fx-wordtest=t3&fx-listentest=t3&H5_DownloadVIPGift=&playerUIModeId=5623502&PlayerStyles_SynchronousSharing=t3&dlt=0846&app_version=9.2.45&sc=wm&tn=
< X-From-Src: 114.93.71.49
<
* Connection #0 to host 163cn.tv left intact
```

**Location**里的 uct2 就是我们要找的东西。

首先通过 Jadx（类似工具有 ReCaf 等）反编译网易云音乐客户端通过搜索 uct2 得到相关代码（部分代码已删除）：

```java
   public static final void o(Context context, Program program, String platform, Function1<? super com.netease.cloudmusic.share.framework.e, Unit> callback) {
        String string;
        com.netease.cloudmusic.share.framework.e eVar = new com.netease.cloudmusic.share.framework.e();
        eVar.f120920a = String.valueOf(program.getId());
        eVar.f120922d = context.getString(ir1.j.f228957m3, program.getName());
        eVar.f120924f = (!program.needShowFeeTag() || TextUtils.isEmpty(program.getReason())) ? program.getBrand() : program.getReason();
        eVar.f120928j = program.getCoverUrl();
        eVar.f120921b = 1;
        eVar.f120937t = 3;
        eVar.f120934q = program;
        long u15 = ap.a.m().u(); // 返回用户ID
        String b15 = com.netease.cloudmusic.utils.d.INSTANCE.b(j() /* 生成加密密钥 */, String.valueOf(u15)); // 生成加密的用户ID b15
        if (b15 == null) {
        // 生成出错的话就 fallback 到原本的 userid ，机制不清
            string = Intrinsics.areEqual(platform, "sina") ? ApplicationWrapper.getInstance().getString(ir1.j.f228886a4, dp.f124165c, 17, Long.valueOf(program.getId()), Long.valueOf(u15), 0) : ((ILinkConfig) ServiceFacade.get(ILinkConfig.class)).urlStringForKey("program_share", "id", String.valueOf(program.getId()), "userid", String.valueOf(u15), "djId", String.valueOf(program.getDj().getUserId()));
        } else {
            string = Intrinsics.areEqual(platform, "sina") ? ApplicationWrapper.getInstance().getString(ir1.j.f228930i0, dp.f124165c, 17, Long.valueOf(program.getId()), URLEncoder.encode(b15, Charsets.UTF_8.name()), 0) : ((ILinkConfig) ServiceFacade.get(ILinkConfig.class)).urlStringForKey("program_share", "id", String.valueOf(program.getId()), "uct2", b15, "djId", String.valueOf(program.getDj().getUserId()));
            //将uct2作为分享链接的参数使用 (uct2=xxxxxx)
        }
        if (Intrinsics.areEqual(platform, "sina")) {
            eVar.f120924f = ApplicationWrapper.getInstance().getString(ir1.j.W3) + " " + string + " " + ApplicationWrapper.getInstance().getString(ir1.j.f228917g);
        }
        eVar.f120933p = string;
        eVar.f120929l = string;
        eVar.f120930m = com.netease.cloudmusic.module.spread.e.f(program.getMainSong().getId());
        if (!Intrinsics.areEqual(platform, "sina")) {
            String b16 = kp.b(NeteaseMusicApplication.getInstance());
            eVar.f120933p = eVar.f120933p + "&app_version=" + b16;
            eVar.f120929l = eVar.f120929l + "&app_version=" + b16;
        } else if (!com.netease.cloudmusic.share.framework.j.f120943a.c()) {
            eVar.f120926h = "";
            eVar.f120928j = "";
        }
        eVar.f120926h = "";
        callback.invoke(eVar);
    }
```

> 占掉我 30 多 G 内存，加上原本就已经红了的 D 盘里还要塞个分页文件

看到是 b15 赋值给了 uct2 ，u15 是咱的用户 id，所以就应该有办法从 uct2 转成原本的 id，先看加密部分，反编译得到 b 函数内部（代码经过处理）：

```java
private final String encode(byte[] data) {
    return Base64.encodeToString(data, 2);
}

// 加密函数，AES/ECB算法
public final String b(String secretKey, String data) {
    try {
        byte[] bytes = secretKey.getBytes(Charsets.UTF_8);
        SecretKeySpec secretKeySpec = new SecretKeySpec(bytes, "AES");
        Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
        cipher.init(1, secretKeySpec);
        byte[] bytes2 = data.getBytes(Charsets.UTF_8);
        byte[] doFinal = cipher.doFinal(bytes2);
        return encode(doFinal);
    } catch (Exception e) {
        e.printStackTrace();
        return null;
    }
}
```

关于加密密钥的是 j 函数，其被调用时返回一个静态单例 Lazy 对象，内部存储的是加密的字符串：

```java
private static final Lazy f298165f = LazyKt__LazyJVMKt.lazy(a.f298166a);

// KT生成的嵌套类
static final class a extends Lambda implements Function0<String> {
    public static final a f298166a = new a();
    a() {
        super(0);
    }

    @Override
    public final String invoke() {
        return (String) ((ICustomConfig) ServiceFacade.get(ICustomConfig.class)).getAppCustomConfig("IuRPVVmc3WWul9fT", "JwDUI7QfKebyIhZwcWAJu1172eV2CgCD", "share#id_encrypt_key"); // 返回加密的字符串，默认值为JwDUI7QfKebyIhZwcWAJu1172eV2CgCD
    }
}
```

> 一开始以为又是什么逆天加密算法，最后发现他们的配置名就是一串 base64 了的随机数

通过`/assets/default_custom_config_IuRPVVmc3WWul9fT.json`可以找到这个配置文件里的加密密钥也是`JwDUI7QfKebyIhZwcWAJu1172eV2CgCD`

那么理论存在，实验开始——

![image.png](https://raw.githubusercontent.com/onlyra1n/image-bed/master/202501311608730.png)

这里看到原本的 uct2 已经被成功解出来了~

## 电脑端

通过观察电脑端的 uct2，b64 解出来均以`Salted__`开头，难不成手机电脑算法不一样？？

> PS：Salted\_\_ 是 crypto-js AES 加密加盐的特征
> 本来想着还要碰反汇编，现在一点也不用（本人反汇编水平不咋）

下面通过 BetterNCM 打开控制台并搜索`share#id_encrypt_key`、`uct2`等字段，发现他们工程师直接把代码放在`orpheus://orpheus/pub/hybrid/app~subApp.chunk.601020.js`里边了，这边放上核心加密代码：

```javascript
function share(e, t) {
  let { payload: n } = e,
    { select: i, call: o } = t;
  return (function* () {
    const {
        platform: e,
        type: t,
        title: a = "分享",
        id: s = 0,
        data: u,
        threadId: p,
      } = n,
      f = yield i((e) => e.host),
      v = y.a.configFromRequest$.getValue(),
      m =
        (null === v || void 0 === v ? void 0 : v["share#id_encrypt_key"]) || "", // 也是从配置文件里获取加密密钥，获取到的也是JwDUI7QfKebyIhZwcWAJu1172eV2CgCD
      b = f.uid || "", // 用户ID
      g = m && b ? d.a.encrypt(b, m, { mode: h.a }).toString() : b, // 加密用户ID，否则fallback
      Q = {
        [k.b.playlist]: "playlist",
        [k.b.audio]: "djradio",
        [k.b.album]: "album",
        [k.b.mv]: "mv",
        [k.b.user]: "user",
        [k.b.artist]: "artist",
        [k.b.article]: "topic",
        [k.b.video]: "video",
        [k.b.voice]: "dj",
        [k.b.track]: "song",
        [k.b.topic]: "activity",
      }; // 分享类型 // 生成分享链接
    let O = "";
    (O =
      g !== b
        ? "https://music.163.com/"
            .concat(Q[t], "?id=")
            .concat(s, "&uct2=")
            .concat(g)
        : "https://music.163.com/"
            .concat(Q[t], "?id=")
            .concat(s, "&userid=")
            .concat(g)), //fallback
      e;
    return !0;
  })();
}
```

可以看到流程与安卓端差不多，but 加密算法有点不一样！

![image.png](https://raw.githubusercontent.com/onlyra1n/image-bed/master/202501311932536.png)

通过动态分析在 encrypt 处下断点发现加密函数这里的 d 和 h 就是 CryptoJS 的实例，整理后加密算法是这样：

```javascript
CryptoJS.AES.encrypt(userID, key, { mode: CryptoJS.mode.ECB }).toString();
```

相应的就能推出解密算法：

> 其实正常走流程的话要走 webpack 的老路，这边直接拿 CryptoJS 上了

```javascript
CryptoJS.AES.decrypt(encryptedID, key, { mode: CryptoJS.mode.ECB }).toString(
  CryptoJS.enc.Utf8
);
```

某云两端加密算法不一样有点想笑，但这可能是检测的某种手段（？）
而且发现了有关 config 云控的一些字段，会不会有那种情况的可能？

## 总结

这种跟踪参数搞对称加密确实比较容易破，双端用的都是解释型语言，对技术的门槛确实比较低，希望大家新的一年天天被视奸手撕加密算法疯狂 deobf 万事如意！！！！
