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

2. [支持Solarflare API的基于轮询的网络库pollnet发布了](https://zhuanlan.zhihu.com/p/66485526)：作者开发了一个基于轮询的网络库，既不会阻塞也不能通过事件驱动，而是需要用户线程频繁的（注意，不是忙等）调用api以接收网络数据。

    > 在使用Solarflare底层API Tcpdirect和Efvi的时候我踩了一些坑，也感受到了和Socket网络编程的许多细节差异。而在实际工程中，由于服务器网卡差异往往需要混合使用Socket和Solarflare的API，这就对上层应用形成了挑战。我花了一些时间抽取两者的共同点，结合实际应用对网络库的需求，设计了一套统一的API，使得用户可以轻松的切换底层实现。在API设计和实现上，我主要关注功能性，易用性和性能（低延迟），目前这套新的网络库几乎可以满足我所有的工程需求。

    可以查看项目网站：链接为[pollnet](https://link.zhihu.com/?target=https%3A//github.com/MengRao/pollnet)。

