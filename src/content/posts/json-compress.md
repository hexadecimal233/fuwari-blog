---
title: 一次有趣的尝试：列式 JSON 压缩
published: 2026-03-30
description: 一次有趣的尝试，尝试使用列式 JSON 压缩技术，减少 JSON 字符串的大小。
tags: [JSON, 压缩]
category: 技术
draft: false
---

> 本文由 AI 辅助撰写，请仔细检查内容真伪。

## 前言

JSON 是现代 Web 开发中最常用的数据交换格式，但它并非完美。当我们需要传输大量数据时，JSON 的冗余性会成为一个显著问题。本文将探讨几种 JSON 压缩和替代方案，并分享我的一次有趣尝试。

## JSON 的痛点

JSON 有几个明显的效率问题：

1. **数值编码低效**：`3.141592653589793` 在内存中只占 8 字节，但 `JSON.stringify()` 后变成 17 字节的字符串
2. **引号泛滥**：每个字符串都要加引号，增加额外开销
3. **键名重复**：当传输多个相同结构的对象时，键名被反复传输

```json
[
  { "x": 100, "y": 100 },
  { "x": 100, "y": 100, "width": 200, "height": 150 },
  { "x": 50, "y": 50 }
]
```

上面的例子中，`"x"` 和 `"y"` 被重复了多次，这是明显的空间浪费。

## CJSON：自动类型提取

[CJSON](https://stevehanov.ca/blog/compress-your-json-with-automatic-type-extraction) 是一种巧妙的压缩方案，核心思想是**自动推导数据结构模板**，避免重复传输键名。

### 工作原理

CJSON 会分析输入数据，自动识别出不同的对象类型，并生成模板：

```json
{
  "f": "cjson",
  "t": [
    [0, "x", "y"],
    [1, "width", "height"]
  ],
  "v": [
    { "": [1, 100, 100] },
    { "": [2, 100, 100, 200, 150] },
    { "": [1, 50, 50] }
  ]
}
```

- `t` (templates)：模板数组，每个模板的第一个数字表示继承自哪个模板
- `v` (values)：值数组，第一个数字是模板索引

模板 `[0, "x", "y"]` 表示从空模板开始，添加 `x` 和 `y` 字段。模板 `[1, "width", "height"]` 表示继承第 1 个模板，再添加 `width` 和 `height`。

### 类型提取算法

CJSON 使用树结构来识别共享键集的对象类型：

1. 遍历所有对象，按遇到键的顺序构建树
2. 树的节点代表键，路径代表键的组合
3. 叶子节点和分支节点都成为模板候选

这种方法的压缩效果取决于数据结构的规律性。对于大量相似对象，压缩率非常可观。

## HPack：另一种思路

[HPack](https://blog.longwin.com.tw/2011/07/json-compress-hpack-cjson-algorithm-2011/) 采用了更简单的策略：将键名提取到头部数组，然后用索引替换。

```json
// 原始
[{"name": "Alice", "age": 30}, {"name": "Bob", "age": 25}]

// HPack 压缩后
[["name", "age"], ["Alice", 30], ["Bob", 25]]
```

HPack 的优点是实现简单，缺点是要求所有对象结构一致。

## MessagePack：二进制序列化

[MessagePack](https://msgpack.org/) 是一种高效的二进制序列化格式，官方口号是："It's like JSON, but fast and small."

### 核心优势

- 小整数编码为单字节
- 短字符串只需额外 1 字节存储长度
- 无需引号和分隔符

```javascript
import { pack, unpack } from "msgpackr";

const data = { name: "hello", count: 42 };
const encoded = pack(data); // 二进制 Buffer
const decoded = unpack(encoded);
```

### msgpackr 的 useRecordId 优化

`msgpackr` 库提供了 `useRecordId` 选项，类似于 CJSON 的模板机制。启用后，重复的结构会被记录并复用：

```javascript
import { Packr } from "msgpackr";

const packr = new Packr({ useRecords: true });

const data = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Charlie" },
];

const encoded = packr.pack(data);
```

第一次遇到某个结构时，会记录其键序列；后续相同结构的对象只需引用记录 ID，大幅减少重复。

## Protobuf：强类型的极致

[Protocol Buffers](https://protobuf.dev/) 是 Google 开发的二进制序列化格式，需要预先定义 `.proto` 文件：

```protobuf
message Person {
  int32 id = 1;
  string name = 2;
  bool active = 3;
}
```

### Varint 编码

Protobuf 使用 Varint 编码整数，小整数只需 1 字节：

```
// int32 值 1 的常规表示（4 字节）
00000000 00000000 00000000 00000001

// Varint 编码（1 字节）
00000001
```

### Wire Type

每个字段编码为 `<tag><type>[<length>]<data>`：

| Wire Type | 含义             | 适用类型                 |
| --------- | ---------------- | ------------------------ |
| 0         | Varint           | int32, int64, bool, enum |
| 1         | 64-bit           | fixed64, double          |
| 2         | Length-delimited | string, bytes, 嵌套消息  |
| 5         | 32-bit           | fixed32, float           |

### Protobuf vs JSON

| 特性     | Protobuf | JSON   |
| -------- | -------- | ------ |
| 格式     | 二进制   | 文本   |
| 体积     | 小       | 大     |
| 可读性   | 差       | 好     |
| 类型安全 | 强       | 弱     |
| Schema   | 必需     | 不需要 |
| 跨语言   | 优秀     | 优秀   |

**Protobuf 适合**：内部服务通信、高性能场景、数据存储

**JSON 适合**：公开 API、配置文件、调试场景

## HTTP 协议层压缩

除了应用层的数据格式优化，HTTP 协议本身也提供了压缩机制。

### Content-Encoding

服务器可以通过 `Content-Encoding` 响应头告知客户端数据已被压缩：

```
Content-Encoding: gzip
Content-Encoding: br
Content-Encoding: deflate
```

### 协商流程

1. 客户端发送 `Accept-Encoding: gzip, deflate, br`
2. 服务器选择支持的压缩算法
3. 服务器压缩响应体，设置 `Content-Encoding` 头
4. 客户端自动解压

### 压缩算法对比

| 算法             | 压缩率 | 速度 | 浏览器支持 |
| ---------------- | ------ | ---- | ---------- |
| gzip             | 中     | 快   | 全部       |
| deflate          | 中     | 快   | 全部       |
| Brotli (br)      | 高     | 中   | 现代浏览器 |
| Zstandard (zstd) | 高     | 快   | 较新浏览器 |

**注意**：HTTP 压缩是传输层优化，浏览器无法预知服务器是否支持压缩，所以**浏览器发送请求时无法使用 HTTP 压缩**。这就是为什么客户端到服务器的 JSON 压缩特别重要。

## 其他序列化方案

### CBOR

CBOR (Concise Binary Object Representation) 是 JSON 的二进制替代品，设计目标是：

- 无需 Schema
- 自描述
- 支持所有 JSON 类型

### BSON

MongoDB 使用的二进制格式，增加了日期、二进制数据等类型支持。

### Avro

Hadoop 生态的序列化格式，Schema 在数据中内嵌，适合大数据场景。

### FlatBuffers

Google 出品，零拷贝反序列化，适合游戏和实时应用。

## Colson：我的玩具库

在研究这些方案后，我写了一个名为 **Colson** 的玩具库（[jsr:@hexzii/colson](https://jsr.io/@hexzii/colson)），目标是：

- **类型安全**：完整的 TypeScript 类型推断
- **零依赖**：轻量级实现

### 设计思路

Colson 采用类似 CJSON 的模板机制，但增加了 TypeScript 的类型推导信息：

```typescript
import { decodeColsonRow, encodeColsonRow } from "@hexzii/colson";

interface User {
  id: number;
  name: string;
  email: string;
}

const users: User[] = [
  { id: 1, name: "Alice", email: "alice@example.com" },
  { id: 2, name: "Bob", email: "bob@example.com" },
];

const encoded = encodeColsonRow(users);
const decoded = decodeColsonRow(encoded);
```

## 总结

没有银弹，只有最合适的选择：

| 场景             | 推荐方案               |
| ---------------- | ---------------------- |
| 公开 API         | JSON + HTTP 压缩       |
| 内部服务通信     | Protobuf / MessagePack |
| 浏览器上传大数据 | CJSON / Colson         |
| 高性能实时应用   | FlatBuffers            |
| 大数据存储       | Avro                   |

JSON 的可读性和通用性使其仍然是 Web 开发的首选，但在性能敏感场景，了解这些替代方案能帮助我们做出更好的架构决策。
