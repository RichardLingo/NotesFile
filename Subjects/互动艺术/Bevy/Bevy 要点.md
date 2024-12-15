---
title: Bevy 要点
authors: Ethan Lin
year: 2024-11-28
tags:
  - 类型/笔记
  - 日期/2024-11-28
aliases:
  - Bevy 要点
---

# Bevy 要点



## Asset 路径


官方样例 `/examples/asset/asset_settings.rs` 可以改资产的默认根路径。但是这个改动只适用于 Bevy 的 Asset。意味着写在系统里的路径是从Bevy的默认Asset路径或者修改过的路径开始的，写在非Bevy系统的函数的路径，还是按照默认的Crete根路径开始的。


