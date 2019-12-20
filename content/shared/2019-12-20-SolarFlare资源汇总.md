---
title: "SolarFlare资源汇总"
author: William
date: 2019-12-20
lastmod: 2019-12-20
categories: [shared]
tags: [solarflare,网卡,低延时]
description: 
draft: false
ToC: true
---

# 入门指引

1. [使用solarflare网卡降低网络IO延迟](https://zhuanlan.zhihu.com/p/59242346)：介绍 SolarFlare 实现 kernel bypass、降低网络延时的3个基本方式：
   1. openonload：最基础版本，提供了兼容 socker 网络编程的接口，用户不需要自己修改代码(应用程序)，直接调用 `openonload` 实现。
   2. tcpdirect：实现传输层的 tcp 协议栈，提供了类似socket的API：zocket，能让用户读写tcp/udp的payload数据，同时也继承了ef_vi zero-copy的特性。
   3. ef_vi：最底层的网卡操作，处于 OSI layer 2（Data Linker Layer），可以直接抓到 ethernet frame，但是没有提供上层协议的支持。这一层面的网卡延时降低最为显著。

