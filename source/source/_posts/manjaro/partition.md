---
title: Manjaro 硬盘分区方案
date: 2019-09-10 18:03:32
categories: Manjaro
tags: [Manjaro, 分区]
---

> Linux 分区这个事每次重装都要现查，记录一下下次直接复用这个方案。

硬件条件 : 120G SSD

`/boot/efi` : 1024M~2048M **ESP** *fat32*

`/` : 60G *ext4*

`/home` : 40G *ext4*

`swap` : 8192M *linuxswap*


