---
title: 好心修改别人的代码却带来新的bug
date: 2019-02-24 21:50:13
tags: bug
categories: bug
---

> 有时候非常严重的问题，就是你常常忽略的小不点。


开发过程中，有时候会遇到要修改别人的代码，那么怎么确保不会带出新的问题出来呢:pray:我们的鹏飞老大给出了几条建议:100:。

- UnitTest，仔细想了想，单元测试真的是太有必要了。
- 代码走查，优秀的技术团队通常都会有，走查和分享的环节。
- 开发纪律上以下上原话 

代码块注释：标明作者、时间、描述
原则上不允许擅自修改他人代码：如需修改需通知作者配合修改，如无法通知作者则必须慎重修改且须有测试保障
防御式编程与契约式编程：默认不信任任何外部调用和方法，默认双方遵守契约，任意一方撕毁契约则抛出异常，建议在UI及应用层倾向于防御式编程保证友好及体验，在逻辑层倾向于契约式编程尽早暴露隐患 