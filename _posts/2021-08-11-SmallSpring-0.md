---
layout: post
title: 手撸 Spring 第 0 篇：序章
date: 2021-08-11
categories: code
tags: Java Spring SpringBoot
typora-root-url: ../../victorchow.github.io
---

> 小傅哥《 Spring 手撸专栏》系列学习笔记

## 初衷

想想转到后端写 Java 这几年，面试题看了不少，各种源码也看了一些，目前的现状是有的技术懂一点，有的技术懂一些，最多最多只敢说自己『熟悉』（使用并能解决问题）某项技术，从未『精通』（自己从 0 手写一个）某项技术。

最近部门发起了『百日蜕变』的活动，鼓励大家报名不同的技术小组进行深入学习，我被分在 Spring / SpringBoot 组当小组长。我初步制定的计划分为两部分：初阶以各种面试题为切入点，深入了解 Spring Framework 的设计和使用；高阶为从 0 开始手写一个迷你版的 Spring。

正巧，看到了大神[小傅哥](https://bugstack.cn/about.html)朋友圈发的[《 Spring 手撸专栏》](https://github.com/fuzhengwei/small-spring)系列，因为之前也经常看[小傅哥](https://bugstack.cn/about.html)的文章，所以就决定以这个系列为主，记录下来每章自己学习的笔记和代码。

说起 Spring，给人的第一印象还是 IoC 和 AOP，关于如何实现这两个功能，还是能想出大体的解决方案的，但里面的一些细节，就比如『如何解决 Bean 的循环依赖』，这个在不了解 Spring 源码的时候就会让人很费解，但在了解了 Spring 的处理方式后就会不禁赞叹道『还能这么搞』，赞叹过后还会感慨自己知道的太少了，因为循环依赖只是 Spring 里面的一个小功能，不知道的东西太多了，难怪自己只能停留在『熟悉』的阶段。

给自己定个小目标吧，学习完《 Spring 手撸专栏》以后，可以说自己『精通』Spring。

![](/assets/img/SmallSpring-0-1.png)

开整开整！