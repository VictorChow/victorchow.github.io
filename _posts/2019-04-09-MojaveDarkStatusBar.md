---
layout: post
title: macOS Mojave仅状态栏使用深色模式
date: 2019-04-09
categories: macOS
tags: macOS
---

> 像macOS High Sierra及以前版本一样使用暗色状态栏

1. 在「系统偏好设置 - 通用」中将外观切换成「浅色」

2. 打开终端，输入指令：

   `defaults write -g NSRequiresAquaSystemAppearance -bool Yes`

3. 注销并重新登录

4. 在「系统偏好设置 - 通用」中将外观切换成「深色」

5. 若恢复成系统的深色模式，那么在终端输入命令后，再执行第 3 步即可：

   `defaults delete -g NSRequiresAquaSystemAppearance`