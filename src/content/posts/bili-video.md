---
title: 如何通过CF反代API绕过B站视频CORS限制并在首页展示最新视频
published: 2025-08-20
description: ""
image: ""
tags: [反代, 视频, B站, Cloudflare]
category: "技术"
draft: false
---

最近继续写前端练手

又在翻别人主页的时候看到了一些活，懒得搞灵感记录（这是坏的！不要学我把一整天全窝在电脑前）

准备学学某人（ta 是 youtube）的博客在主页上展示自己的 B 站最新视频，同时让自己的主页更加有高级感（疯狂引流 x

## B 站 API

现在对 B 站 API 的研究也算比较成熟，易姐的 [Bilibili API Collect](https://github.com/SocialSisterYi/bilibili-API-collect/blob/master/docs/user/space.md) 够用，不多说

众所周知，B 站是有做反爬的——

...

## 处理 CORS

由于浏览器安全限制，直接请求 B 站 API 会被 CORS 拦截（而且所有默认不带`Access-Control-Allow-Origin`的你服务器返回个 200 浏览器最后也会拒绝处理

[这篇文章](https://www.bilibili.com/opus/924356874277486612)具体写了用怎么用 CF Workers 绕过跨域请求

:::tip
不会用 CF 文章里有详细部署教程，如果你已经知道怎么搭建，这一步可以跳过
:::

:::note
Workers 免费版每个月有**100000**次请求限制，超过就会停用，而且搞反向代理有概率号会炸连带绑定的域名（墙倒是不怎么担心
:::

## Workers 的限制

现在已经成功代理，其他网站可以正常访问，但是发现访问 API 时 B 站直接返回 412

通过查看 [Request Catcher](https://requestcatcher.com/) 和 [httpbin](https://httpbin.org/get) 发现，CF 的 `fetch` 会在请求头里下毒，同时找到了[这篇文章](https://meta.appinn.net/t/topic/73851/1)

> 具体来说，Cloudflare 的 fetch API 会自动且强制地在所有出站请求中加入一些无法被过滤掉的 CF-\* 请求头，例如：
>
> cf-connecting-ip：暴露发起请求用户的真实 IP 地址。
>
> cf-ipcountry：暴露用户所在的国家/地区。
>
> cf-worker：明确标识该请求经过了 Cloudflare Workers

应该是是动了这些手脚被防火墙检测后拦截了，所以需要找到办法去掉这些头，而恰巧这里用 CF 的`socket` api 就能自由的去发送 HTTP 请求，同时绕过系统的一些拦截限制，差不多就是个越狱版

因为原本的脚本不具备绕过 CORS 的能力，故需要手动修改请求头

```js title="worker.js" {6-36}
class ShadowProxy {
  static async handleRequest(req, env, ctx) {
    try {
      // ... 原有代码

      // 复刻请求头
      const req2 = new Request(req.url, {
        headers: req.headers,
        method: req.method,
        body: req.body,
        redirect: "follow",
      });

      // 处理特殊头部规则
      const specialCases = {
        "*": {
          Origin: "DELETE",
          Referer: "DELETE",
        },
      };

      const rules = specialCases[url.hostname] || specialCases["*"];
      for (const [key, value] of Object.entries(rules)) {
        if (value === "DELETE") {
          req2.headers.delete(key);
        } else if (value !== "KEEP") {
          req2.headers.set(key, value);
        }
      }

      const response = await proxy.connect(req2, dstUrl);

      // 强制允许跨域
      const modifiedResponse = new Response(response.body, response);
      modifiedResponse.headers.set("Access-Control-Allow-Origin", "*");
      return modifiedResponse;
    } catch (error) {
      console.error("ShadowProxy error:", error);
      return new Response(`Error: ${error.message}`, { status: 500 });
    }
  }
}
```

:::warn
听说 CF 最近打反代，评论区有人被封了，想账号不被封的话可以先等等
:::

## 实装

编辑好脚本部署后，接下来就是调用 API 了

调用十分的简单，域名+link（）

```js
// mid是你的用户id
// keywords这东西本来是给你找视频用的你却用来查最新视频x
// ps是获取个数
const URL =
  "https://<你的域名>/your-auth-token/https://api.bilibili.com/x/series/recArchivesByKeywords?mid=0174927495&keywords=&ps=6";
const resp = await fetch(URL);
const data = await resp.json();
```

:::note
返回 B 站图片`http://i***.hdslb.com/`也有跨域问题，所以客户端请求图片的时候也要处理链接
:::

:::tip
对于不想看的可以直接抄（）

首页的实现能在[BiliVideo.vue](https://github.com/hexadecimal233/homepage-new/blob/master/app/components/home/BiliVideo.vue)找到啦
:::

本人在 API 这边还是作过限制了，限制了其他协议的访问和允许链接，所以过来偷跑主播流量的就别想了（

btw 今天也给自己主页写了自动获取 github readme skillicon 的功能

说实话没用的小玩意又增加了（主页这东西摆在那没人看还做甚

---

**什么？绿联 NAS 调教教程呢？**

先别急，在写（）
