+++
title = 'Tmodloader模组开发指南'
date = 2024-04-25T20:29:14+08:00
tags = [
    "Tmodloader",
]
+++

这是一篇写给Tmodloader开发新手的指南，适用于.Net开发新手

本文会从一个示例入手，由浅入深介绍模组的开发、优化以及发布，欢迎指出文档内的错误

<!--more-->
**!!! 文章尚未完成**

TODO:

- [x] 开发环境配置
- [ ] 初始化模组
- [ ] 制作武器...


## 0.基础概念

**编程语言:** .NET/C#

**编译:** 并非由编译器编译，而是 Tmodloader 自动打包运行，使用 Tmodloader 生成 .tmod 格式模组，所以理论上任何文本编辑器都适用与模组开发

## 1.开发环境配置

**SDK:** 仅能使用 [.NET 6.0](https://dotnet.microsoft.com/en-us/download/dotnet/6.0)，注意不要下载成 Runtime

**像素画:** 推荐使用 [Krita](https://krita.org/en/download/)(开源免费) 以及 [Aseprite](https://github.com/aseprite/aseprite)(开源、轻量) (Aseprite需要手动编译或购买编译好的软件)

**IDE:** Windows 环境下推荐使用 [Visual Studio](https://visualstudio.microsoft.com/downloads/)； Mac/Linux 环境下推荐使用 [Rider](https://www.jetbrains.com/rider/download)； (下位替代：[VSCode](https://code.visualstudio.com/Download))

由于本人更倾向在Linux环境下开发，所以本文的演示都会基于 Rider + Aseprite

## 2.Tmodloader 特有内容

在开始前，本文还想简单提及一些对模组开发有帮助的内容、Tmodloader特有的内容以及一些对一些内容的介绍

**强烈建议阅读完本部分后再阅读其余教学**

### Hook

在模组开发中，Tmodloader Hook 并非实际意义上的Hook函数，但与Hook函数有类似的作用，即可以更改(Override)其他函数的内容

Tmodloader Hook 允许模组可以调用游戏/其他模组内的函数，更方便编写模组

例如：

我想更改原版这个函数的行为：

```c#
virtual void Terraria.ModLoader.ModItem.UpdateInventory	(Player player)	
```

于是我们可以在ModItem类里编写以下函数：

```c#
override void UpdateInventory (Player player)
{
    // We write code here.
}
```

注意：由于这个函数处于ModItem类里，于是不需要使用`Terraria.ModLoader.ModItem.UpdateInventory`，使用UpdateInventory即可
