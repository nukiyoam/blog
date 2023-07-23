---
title: "配置Chrome触摸板双指手势导航"
date: 2023-07-23T14:22:47+08:00
categories:
  - Manjaro
tags:
  - Chrome
  - 触摸板手势
slug: "配置Chrome触摸板双指手势导航"
---

> 由于之前使用的是MacOS,Chrome自带了有触摸板手势导航,现在切换到Manjaro之后有点不习惯,因此在网上找到开启Chrome触摸板双指手势导航的方法,特在此记录一下

*此方法应该也适用与其他的Linux发行版本*

开启方法其实特别简单,只需要开启Chrome的一个标记特性就行

1. 在Chrome的地址栏输入 `chrome://flags/` 回车
2. 找到名称为 `Overscroll history navigation` 的flag,将其设置为 `Enabled`
3. 重启浏览器

**wayland需要注意一下**

如果使用的是 `wayland` 而非 `X11` ,并且上述配置不生效的话,还需要在Chrome启动参数中加入 `--enable-features=TouchpadOverscrollHistoryNavigation` 

不出意外的话,现在在Chrome中已经可以使用双指手势导航了