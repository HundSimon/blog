+++
title = '用C#对进程进行内存编辑'
date = 2024-08-20T18:55:03+08:00
draft = true
+++

写给自己看的C#内存编辑教程

<!--more-->

## 前言

本文不会讲具体C#内存编辑的底层代码 而是基于 [memory.dll](https://github.com/erfg12/memory.dll)

由于静态逆向的局限性，大多情况下无法进行跨版本兼容，而由于程序的代码跨版本不会有很大的变化，于是可以利用内存编辑实现跨版本功能。

文章所有代码基于以下前提

```cs
using Memory;
public Mem MemLib = new Mem();
MemLib.OpenProcess("GAME_NAME");
```

## 静态地址

适用于不再更新的程序，可以直接通过静态地址编辑实现功能。

### 读取

```csharp
float current_hp = MemLib.readFloat("gamedll_x64_rwdi.dll+0x007F94E8,0x18,0x18,0xF0,0xB0,0x6BC");
```

可以使用 基址+指针+偏移 的方法读取

要根据类型读取， 如 `float` 则使用 `readFloat` 方法， `int` 则使用 `readInt` 方法， `bool` 则使用 `readBool` 方法。

### 写入

```csharp
MemLib.writeMemory("gamedll_x64_rwdi.dll+0x007F94E8,0x18,0x18,0xF0,0xB0,0x6BC","float", "100.0");
```

同样可以使用 基址+指针+偏移 的方法写入

### 冻结值

memory.dll 提供了更方便的冻结值功能，其作用和 `new Thread` 一样

如

```csharp
MemLib.FreezeValue("gamedll_x64_rwdi.dll+0x007F94E8,0x18,0x18,0xF0,0xB0,0x6BC","float", "100.0");
```

取消冻结可以使用

```csharp
MemLib.UnfreezeValue("gamedll_x64_rwdi.dll+0x007F94E8,0x18,0x18,0xF0,0xB0,0x6BC");
```

## 动态地址

适用于程序更新后，需要通过动态地址编辑实现功能。

主要的方法是 AOB (Address of Byte) 扫描，即通过字节序列定位地址。AOB 可以适用于任意代码未变化的程序。

```csharp
IEnumerable<long> AoBScanResults = await MemLib.AoBScan(0x01000000, 0x04000000, "?? ?? ?? ?5 ?? ?? 5? 00 ?? 00 00 00 ?? 00 50 00", false, true);
```

其中 `AoBScan(long start_address, long end_address, string pattern, bool writable, bool executable);`

writable 和 executable 用于过滤可读写可执行的内存区域。

当程序内存过大时，AOB 扫描就会很大程度拖慢程序，这时可以通过人为划分内存范围，就像上文一样

注意，如果 pattern 没找对，就会搜索到多余的结果，需要进一步过滤。