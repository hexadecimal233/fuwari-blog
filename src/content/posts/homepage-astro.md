---
title: 从 Nuxt 迁移到 Astro
published: 2025-11-22
description: "关于我为什么要把 Nuxt 迁移到 Astro"
tags: [博客, Astro, Nuxt, 迁移]
category: 开发
---

今天一下午把自己的主页用 Astro+Svelte 重构了一下，就想稍微阐释一下自己迁移的原因。

## Astro, Yes!

下面是摘自官网[astro.build](https://astro.build/)的介绍

> **什么是 Astro？**
> Astro 是一个专为构建快速、内容驱动型网站而优化的 JavaScript Web 框架。
>
> **服务端优先**
> Astro 通过在服务器端渲染组件来提升网站性能，向浏览器发送轻量级的 HTML，无需任何不必要的 JavaScript 开销。
>
> **内容驱动**
> Astro 旨在与您的内容无缝协作，无论内容存储在哪里。可以从文件系统、外部 API 或您喜欢的 CMS 加载数据。
>
> **高度可定制**
> 使用您喜爱的工具扩展 Astro。可以引入自己的 JavaScript UI 组件、CSS 库、主题、集成等更多功能。

对于友链的网站图标加载 Astro 也有做优化，因为目前的友链图片全是第三方加载，朋友的网站的图片加载速度都没做过优化（被墙的甚至直接加载不出来），可能导致加载开屏卡很久

现在用通过`<Image>`组件在编译时就会将图标预加载并且缓存到本地，削除了前端从不同网站加载图标而且分辨率不统一导致的加载卡慢的问题

> 途中因为我直接把新的分支 push 到[老项目](https://github.com/hexadecimal233/homepage-new)的 branch 里了，造成了多分支保护规则 (见<https://github.com/withastro/docs/issues/1376>)，直接导致 workflow failure，要我们将 `Environments`里的分支保护规则加上咱新的分支或者设置为全部即可

## Astro + Svelte

Astro + Svelte 对于规模不大的像我们这种个人首页就挺合适感觉，功能上基本的东西都有

对于客户端渲染的响应式组件，Astro 的孤岛架构保证了只会在需要的时候才会加载组件，也是像我从别的框架或者开发 SPA 的人比较容易犯错的地方

:::important
对于要在客户端渲染的组件须经历水合 (hydration，也叫做水化，注水) 过程，将干巴巴的 HTML 转换为交互式的组件，才能在浏览器中渲染出交互效果
各大主流 SSR 框架都支持水合过程，包括 Nuxt、Next.js、Vue SSR 等，这样对一些静态站点来说，也能获得不错的性能提升，同时还能提升 SEO 效果
:::

+1, Svelte 在客户端 JS 体积上也比 vue 小很多，即使你的站点部署在 Gtihub Pages 上或者套了 Cloudflare 减速器(x) 也能获得极快的加载速度

:::tip
PS：本站的博客主题也是基于 Astro + Svelte 写的，看看[Fuwari](https://github.com/saicaca/fuwari)！
:::

## 为什么 Nuxt 拉了？

因为我现代前端接触的第一个框架是 vue，再加上对于 Nuxt 和 Vue 与渲染机制的不理解，那时候还迷迷糊糊地就选用 Nuxt 了

Nuxt 本身作为一个全栈开发框架，主要注重服务端与客户端的一个 monolith 架构，对于一个静态为主的个人站说，可以说是大炮打蚊子

每次用 vue 都要打包一个运行时进去，导致最终的包体积很大，而 Astro 则是在编译时就会将组件编译为静态 HTML，不会有运行时的依赖，最终的包体积也会小很多

## 未来的计划

准备在之后将继续写个人主页，随便换点设计也不是不行（）

~~到时候条件允许直接迁移上云~~
