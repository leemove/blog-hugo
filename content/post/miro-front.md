---
title: 讨厌微前端的几个理由
date:  2023-12-04T11:04:31+08:00
draft: false
tags: ["微前端"]
categories: ["前端"]
---

## 叠甲

大部分微前端的文章主要围绕以下几个微前端的优点：

1. 独立开发/部署
2. 横向拓展简单(增删子应用)
3. 相对mpa微前端给用户spa的体验


不否认微前端架构带来的优势，但是实际体验下来大多数场景,以上优势并不能弥补微前端带来的各种不足

## 揭开微前端的伤疤

如果普通mpa的使用成本是1，那微前端姑且算作1.1。调试普通应用的成本是1，那调试微前端项目的调试成本大概在5。复杂度来自以下几个方面。

1. 多了一层主应用。微前端主应用往往比较简单，子应用承担具体业务逻辑，我们开发也是更多围绕子应用，但是调试子应用就必须包含主应用，整个链路多了数个环节，首当其冲的就是项目加载运行时间会变长，其次就是主应用和子应用之间的通信问题，需要处理的问题也会增多。

2. 沙箱不是完美的。微前端技术最核心的就是隔离各个子应用的js和css代码，使各个子应用之间不会互相影响，虽然各个子应用是独立部署，但是实际上是在一个页面上下文中执行。但是目前非常可悲的是沙箱并不是完美的，有多种情况下会导致逃逸。在子应用较多，或者依赖较多的情况下这种问题几乎无法排查。

3. 糟糕的性能。微前端跑不开的魔咒，因为是聚合了多个应用，多个应用之前的通信成本肯定是大于单个应用内通信的，大多数情况下依赖也很难做到公用，而且每次子应用切换也会有很大的性能开销，多个子应用的加载，优化也是一直在隔靴搔痒，如果是高性能要求场景，微前端目前基本无法使用。

4. 生态尚不完善。微前端的技术从起初的迸发，到现在逐渐有止步不前的趋势，笔者分析很大一部分原因就是生态导致。微前端的相关工具链，框架层出不穷，但是真正能大规模商用的并不多，而且大多都各自为战，没有形成合力。国外使用微前端架构的项目估计也是屈指可数。大多数人在开发类库的时候不会考虑微前端场景。


5. 微前端的使用需要完整的运维部署工具等等，有这些能力的公司可能不会使用微前端，因为mpa可能是一个更稳定的选择。没有这部分能力的公司可能没有使用微前端的必要，因为他们的项目可能并没有想象的那么复杂。