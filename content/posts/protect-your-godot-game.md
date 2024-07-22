+++
title = 'Protect Your Godot Game'
date = 2024-07-21T21:30:45+08:00
draft = false
+++

!!!WIP

由于godot的开源，逆向变得易如反掌，本文会提供一些保护的方案

<!--more-->

## 前言

**阅读本文你需要一定的 Godot 开发基础**

使用 **Table Of Contents** 快速跳转到你想看的内容

永远没有绝对安全的保护，唯一能做的就是增加逆向的成本

## 基本知识

### 游戏根目录

```
Example.console.exe  -- Debug独有，启动游戏的同时会启动控制台
Example.exe          -- 类似于缩减版的godot游戏引擎，只有运行游戏的功能
Example.pck          -- 存储着你项目目录的所有文件
```

由此可见，Godot 的逆向十分简单，只要逆向 .pck 文件即可获得完整的项目，使用 [gdsdecomp](https://github.com/bruvzg/gdsdecomp) 可以还原出完整的项目

### C# 和 GDScript

GDScript 是 Godot 开发的编程语言

Godot 可以同时使用 C# 和 GDScript 编程，两者在使用时差别不大，性能差距可以忽略，GDScript 更全能一些，C# 目前还不支持 Android 平台

另外，GDScript 在运行时不会被编译，C# 会被编译。GDScript 在运行时会被 `GDScript VM` 调用并直接执行

## 基础防护

### PCK 密钥

这种是官方的加密方法，逆向相对简单，以下是操作方法

1. 配置环境
   
   - 安装 [Git](https://git-scm.com/download/)
   
   - 安装 [Python](https://www.python.org/downloads/)
   
   - 安装 Scons ``pip install scons``
   
   - 安装 [llvm-mingw](https://github.com/mstorsjo/llvm-mingw/releases) ，下载 msvcrt-x86_64.zip，添加 bin 到环境变量

2. 生成密钥
   
   1. 下载 [openssl](https://www.openssl.org/source/)
   
   2. 使用 `openssl rand -hex 32` 生成密钥 如`f91476533bd554c408e3d03ec634da97ee5e6ccae009dc8062dd39cbd23fa3fc`
   
   3. 保存好这个密钥

3. 获取源码
   
   1. `git clone https://github.com/godotengine/godot.git`
   
   2. 选择对应的版本 `git checkout tags/4.2-stable`

4. 构建导出模板
   
   1. 在源码根目录打开cmd，输入`set SCRIPT_AES256_ENCRYPTION_KEY=你的key`
   
   2. 如果不需要 C# 功能：
      
      ```shell
      scons platform=windows target=template_release
      ```
      
      如果需要 C#：
      
      ```shell
      scons platform=windows target=template_release module_mono_enabled=yes
      ```
      
      在命令后添加 -j 即可多线程编译，如 -j32 就是32线程
   
   3. 等待构建结束，结束后在 /bin 会有以下两个文件
      
      ```
      godot.windows.template_release.x86_64.console.exe
      godot.windows.template_release.x86_64.exe
      ```
      
      如果需要debug模式，则需要另外构建`target=template_debug`

5. 使用构建模板
   
   项目 - 导出 - 添加 - Windows Desktop
   
   选项 - 自定义模板 - 发布 定位到你的 `godot.windows.template_release.x86_64.exe`
   
   加密 - 加密导出的PCK - 加密密钥 填写前面生成的密钥

### 代码混淆

建议使用现成的插件 [gdmaim](https://github.com/cherriesandmochi/gdmaim)

### 使用 C#

因为目前大部分工具没办法还原完整的 C# 代码，所以使用 C# 是相对安全的选项

## 进阶防护

### 改引擎代码

Godot 以 MIT 协议开源，所以我们可以放心修改引擎源码并且商用

#### 自定义PCK加解密

在 `core/io/file_access_encrypted.cpp` 中，`open_and_parse` 负责pck的解密

```cpp
FileAccessEncrypted::open_andparse(Ref<FileAccess> p_base, const Vector<uint8_t> &p_key, Mode p_mode, bool p_with_magic) {
```

其中 `p_key`  是32字节的密钥

你可以在这里对 `p_key`  进行处理，比如变换其中的字节、二次加密之类的，这样即使 gdke 能读取到密钥，也无法用 gdsdecomp 解包，因为游戏在使用密钥解密时有额外的处理

如果想要更强的保护，我建议在编译 Godot Engine 时就进行混淆，这样逆向时就没办法轻易定位到 `open_andparse` 这个函数，建议在每个版本发布时都重新进行一次 Godot Engine 混淆，可以保证跨版本时更难进行修改

### 伪装成 Unity 引擎

别看这个有点搞笑，实践中还真能骗到不少人

这是 il2cpp 打包的 Unity 游戏的结构

```
│  ExampleGame.exe
│  GameAssembly.dll
│  UnityCrashHandler64.exe
│  UnityPlayer.dll
│
└─ExampleGame_Data
    │  app.info
    │  boot.config
    │  resources.assets
    |  ......
    │
    ├─il2cpp_data
    │  ├─etc
    │  │  └─mono
    │  │      │  ......
    │  │
    │  ├─Metadata
    │  │      global-metadata.dat
    │  │
    │  └─Resources
    │          ......
    │
    └─.......
```

把 `ExampleGame.pck`  重命名成 `GameAssembly.dll`

只要使用 `ExampleGame.exe --main-pack GameAssembly.dll`  启动，就可以加载 `GameAssembly.dll` 的内容

## 极端防护

### 加壳

这个没什么好说的，以性能为代价换取安全

建议使用 [VMProtect](https://vmpsoft.com/) 的虚拟化和控制流平坦化

## Credits

https://github.com/bruvzg/gdsdecomp

[Compiling with PCK encryption key ](https://docs.godotengine.org/en/4.2/contributing/development/compiling/compiling_with_script_encryption_key.html)

[Protecting Your Godot Project from Decompilation](https://godot.community/topic/35/protecting-your-godot-project-from-decompilation)

[Protecting Your Godot Project from Decompilation (Web archive)](http://web.archive.org/web/20240619110319/https://godot.community/topic/35/protecting-your-godot-project-from-decompilation)
