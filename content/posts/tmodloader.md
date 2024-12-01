+++
title = 'Tmodloader模组开发指南'
date = 2024-04-25T20:29:14+08:00
tags = [
    "Tmodloader",
]
draft = true
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

**命名规则:** 因人而异，但我更推荐Pascal Case，如 `CatSwordClass`，命名时优先可读性(毕竟IDE都会自动补全)，因为Tmodloader对于翻译文件的设计，Pascal Case是最优先的选择

## 1.开发环境配置

**SDK:** 仅能使用 [.NET 6.0](https://dotnet.microsoft.com/en-us/download/dotnet/6.0)，注意不要下载成 Runtime

**像素画:** 推荐使用 [Krita](https://krita.org/en/download/)(开源免费) 以及 [Aseprite](https://github.com/aseprite/aseprite)(开源、轻量) (Aseprite需要手动编译或购买编译好的软件)

**IDE:** Windows 环境下推荐使用 [Visual Studio](https://visualstudio.microsoft.com/downloads/)； Mac/Linux 环境下推荐使用 [Rider](https://www.jetbrains.com/rider/download)； (下位替代：[VSCode](https://code.visualstudio.com/Download))

由于本人更倾向在Linux环境下开发，所以本文的演示都会基于 Rider + Aseprite

## 2.Tmodloader 注意事项

在开始前，本文还想简单提及一些对模组开发有帮助的内容、Tmodloader特有的内容以及一些对一些内容的介绍

**强烈建议阅读完本部分后再阅读其余教学**

### Hook

在模组开发中，Tmodloader Hook 并非实际意义上的Hook函数，但与Hook函数有类似的作用，即可以更改(Override)其他函数的内容(IL修改是更高阶的实现方法，文章中同样会提到)

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
    // 修改的代码
}
```

注意：由于这个函数处于ModItem类里，于是不需要使用`Terraria.ModLoader.ModItem.UpdateInventory`，使用`UpdateInventory`即可

### 数据类型

Index 和 Type 是新手会经常混淆的概念，错误的使用下模组虽可以运行，但会出现很多问题，所以弄清这两个概念对之后的模组开发十分重要

#### Type(类型)/ID

Type是每个内容的标识符(ID)，以整数表示，用于区分物品类型，识别各种内容

物品ID可以在[Wiki](https://terraria.wiki.gg/wiki/Terraria_Wiki)中查到

比如手里剑的Type(id)是3

#### Index索引

Index是每个实例的标识符，每个被创建的实例都被存在数组中，实例们对应按顺序排列的索引

比如世界中的投掷物储存于`Main.projectile`数组中，用`Main.projectile[0]`可以访问到第一个索引的投掷物的值，如Type ID 3(手里剑)

### Class 类

Tmodloader 提供了很多类，如上文提到的`ModItem`类，通过类派生和继承，我们可以很轻松的创建模组内容(类似于Java中的`extends ModItem`)

例如 `CatSwordClass : ModItem`中，我们创建了自定义物品`CatSword`，其中的`: ModItem` 类说明其源于`ModItem`类，这意味着我们可以基于`ModItem`来进行自定义修改，创建自己的物品

同样的，自己创建的类只能继承自一个类

## 3.初始化模组

## 4.添加模组内容

## 5.性能优化

## 6.发布模组
