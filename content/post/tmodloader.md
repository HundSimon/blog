+++
title = 'Tmodloader模组开发指南'
date = 2024-04-25T20:29:14+08:00
draft = false

+++

**!!! 文章尚未完成**
TODO:

- [x] 开发环境配置
- [ ] 初始化模组
- [ ] 制作武器...

这是一篇写给Tmodloader开发新手的指南，适用于.Net开发新手

本文会从一个示例入手，由浅入深介绍模组的开发、优化以及发布，欢迎指出文档内的错误

## 0.基础概念

**编程语言:** .NET/C#

**编译:** 并非由编译器编译，而是 Tmodloader 自动打包运行，使用 Tmodloader 生成 .tmod 格式模组，所以理论上任何文本编辑器都适用与模组开发

## 1.开发环境配置

**SDK:** 仅能使用 [.NET 6.0](https://dotnet.microsoft.com/en-us/download/dotnet/6.0)，注意不要下载成 Runtime

**像素画:** 推荐使用 [Krita](https://krita.org/en/download/)(开源免费) 以及 [Aseprite](https://github.com/aseprite/aseprite)(开源、轻量) (Aseprite需要手动编译或购买编译好的软件)

**IDE:** Windows 环境下推荐使用 [Visual Studio](https://visualstudio.microsoft.com/downloads/)； Mac/Linux 环境下推荐使用 [Rider](https://www.jetbrains.com/rider/download)； (下位替代：[VSCode](https://code.visualstudio.com/Download))

由于本人更倾向在Linux环境下开发，所以本文的演示都会基于 Rider + Aseprite

## 2.初始化模组
