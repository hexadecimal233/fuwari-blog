---
title: PDM和Pylint补全的一些问题解决
description: ""
published: 2024-12-09
tags: [技术]
category: 技术
draft: false
---

## 前言

今天玩 PDM 安装 Fluent Widgets 时遇到了一个问题，发现所有装的 import 全部不识别，连自己这个项目里的源文件也不认，但 ctrl+左键却又能看见模块的原文件...

## 正文

我先是尝试在`.vscode/settings.json`里改 pylint 的 cwd，结果项目里的源文件认到了，但 import 还是不认...

```json
{
  "pylint.cwd": "${workspaceFolder}/src"
}
```

> 其实我还问了群 u 们，发现他们也没能幸免（

自己摸索后继续改 pylint 的设置，发现改解释器的路径后就不再报错了：

```json
{
  "pylint.interpreter": "${workspaceFolder}/.venv/Scripts/python.exe"
}
```

但这对用别的 IDE 的人很不友好

所以我用万能的搜索引擎发现了通用的解决方案：<https://vi.stackexchange.com/questions/45737/pylint-unable-to-find-imports-from-currently-active-virtual-environment>

因为没时间去判断这是 bug 还是 intended behavior，所以不敢去官方 repo 那提 issue（
