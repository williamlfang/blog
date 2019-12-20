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

## [使用solarflare网卡降低网络IO延迟](https://zhuanlan.zhihu.com/p/59242346)

介绍 SolarFlare 实现 kernel bypass、降低网络延时的3个基本方式：-

- openonload：最基础版本，提供了兼容 socker 网络编程的接口，用户不需要自己修改代码(应用程序)，直接调用 `openonload` 实现。

- tcpdirect：实现传输层的 tcp 协议栈，提供了类似socket的API：zocket，能让用户读写tcp/udp的payload数据，同时也继承了ef_vi zero-copy的特性。

- ef_vi：最底层的网卡操作，处于 OSI layer 2（Data Linker Layer），可以直接抓到 ethernet frame，但是没有提供上层协议的支持。这一层面的网卡延时降低最为显著。

## [支持Solarflare API的基于轮询的网络库pollnet发布了](https://zhuanlan.zhihu.com/p/66485526)

作者开发了一个基于轮询的网络库，既不会阻塞也不能通过事件驱动，而是需要用户线程频繁的（注意，不是忙等）调用api以接收网络数据。

> 在使用Solarflare底层API Tcpdirect和Efvi的时候我踩了一些坑，也感受到了和Socket网络编程的许多细节差异。而在实际工程中，由于服务器网卡差异往往需要混合使用Socket和Solarflare的API，这就对上层应用形成了挑战。我花了一些时间抽取两者的共同点，结合实际应用对网络库的需求，设计了一套统一的API，使得用户可以轻松的切换底层实现。在API设计和实现上，我主要关注功能性，易用性和性能（低延迟），目前这套新的网络库几乎可以满足我所有的工程需求。

可以查看项目网站：链接为[pollnet](https://link.zhihu.com/?target=https%3A//github.com/MengRao/pollnet)。

## [CloudFlare: Kernel bypass](https://blog.cloudflare.com/kernel-bypass/)

一位 CloudFlare 的工程师介绍了如何实现 kernel bypass 的技术

- `PACKET_MMAP`/`PF_RING`
    `Packet_mmap` 是  Linux API for fast packet sniffing，可以直接在 Linux 进行调用。严格来说，这并非一个 Kernel bypass 技术。正如本文作者所评论的：

    > Since the goal of kernel bypass is to spare the kernel from processing packets, we can rule out `packet_mmap`. It doesn't take over the packets - it's just a fast interface for packet sniffing. Similarly, plain `PF_RING` without ZC modules is unattractive since its main goal is to speed up libpcap.

- Snabbswitch

- DPDK
    Intel® DPDK全称Intel Data Plane Development Kit，是intel提供的数据平面开发工具集，为Intel architecture（IA）处理器架构下用户空间高效的数据包处理提供库函数和驱动的支持，它不同于Linux系统以通用性设计为目的，而是专注于网络应用中数据包的高性能处理。具体体现在DPDK应用程序是运行在用户空间上利用自身提供的数据平面库来收发数据包，绕过了Linux内核协议栈对数据包处理过程。

- Netmap

对于 `Snabbswitch`、`DPDK`、`Netmap`，作者也不是十分看好：

> Let me show why. In order to achieve a kernel bypass all of the remaining techniques: Snabbswitch, DPDK and netmap take over the whole network card(需要控制这个网络接口), not allowing any traffic on that NIC to reach the kernel. At CloudFlare, we simply can't afford to dedicate the whole NIC to a single offloaded application.

基于以上几点，作者认为现在比较能够符合 `kernel bypass` 的主要技术有：

- `Solarflare's EF_VI`
    
    ![](/images/2019-12-20-SolarFlare资源汇总/efvi-model.png)

    `EF_VI`, being a proprietary library, can be only used on Solarflare NIC's, but you may wonder how it actually works behind the scenes. It turns out EF_VI reuses the usual NIC features in a very smart way.

    Under the hood each `EF_VI` program is granted access to a dedicated RX queue, hidden from the kernel. By default the queue receives no packets, until you create an `EF_VI` "filter". This filter is nothing more than a hidden flow steering rule. You won't see it in ethtool -n, but the rule does in fact exist on the network card. Having allocated an RX queue and managed flow steering rules, the only remaining task for `EF_VI` is to provide a userspace API for accessing the queue.

    作者认为， `EF_VI` 是当前最可行的一个 `kernel bypass` 技术。

- Virtualization approach
    
    ![](/images/2019-12-20-SolarFlare资源汇总/virt-model.png)

    While it sounds good on the paper, it's not all that simple. First, only DPDK supports "ixgbevf" interfaces, netmap, snabbswitch and PF_RING don't. Secondly, by default the VF interface won't receive any packets. To send some flows from PF to VF you need this obscure patch to ixgbe. With it you can address the VF by encoding it in the high bits of "action" queue number in ethtool.                                                                                                                                                                                          
## [SDN: 软件定义网络](https://tonydeng.github.io/sdn-handbook/)

> SDN （Software Defined Networking）作为当前最重要的热门技术之一，目前已经普遍得到大家的共识。有关SDN的资料和书籍非常丰富，但入门和学习SDN依然是非常困难。本书整理了SDN实践中的一些基本理论和实践案例心得。


# 友商大战

## Mellanox

在低延时网卡业务领域，SolarFalre 虽然做的比较早，具有一定的先发优势，但是也一直收到新市场力量的挑战。今天我看到评论有人说，现在 [Mellanox](https://cn.mellanox.com/solutions/fsi/) 也逐渐在低延时网卡方向开始崭露头角，可以关注一下这一方面的进展情况。
