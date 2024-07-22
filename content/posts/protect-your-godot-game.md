+++
title = 'Protect Your Godot Game'
date = 2024-07-21T21:30:45+08:00
draft = false
+++

!!!WIP

由于godot的开源，逆向变得易如反掌，本文会提供一些保护的方案，以及逆向时绕过这些保护的方法

<!--more-->

## 前言

**阅读本文你需要一定的 Godot 开发基础**

使用 **Table Of Contents** 快速跳转到你想看的内容

永远没有绝对安全的保护，我们唯一能做的就是增加逆向的成本。

买断制游戏建议推出试玩版，好玩的游戏自然会有人愿意掏钱

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

## 基础防护

### PCK 密钥

#### 加密

这种是官方的加密方法，逆向相对简单，以下是操作方法

1. 配置环境
   
   - 安装 [Git](https://git-scm.com/download/)
   
   - 安装 [Python](https://www.python.org/downloads/)
   
   - 安装 Scons ``pip install scons``
   
   - 安装 [llvm-mingw](https://github.com/mstorsjo/llvm-mingw/releases) ，下载 msvcrt-x86_64.zip，添加 bin 到环境变量

2. 生成密钥
   
   1. 访问这个网站 [Keygen](https://asecuritysite.com/encryption/keygen)
   
   2. passphase 瞎填，模式选 aes-256-cbc，然后 Generate Key
   
   3. 获得像这样的结果：
   
   ```
   salt=B456CFE46DB5BACE
   key=FF34295A677DB38784EAAD79E9C41AC1816C232DBF85C8D1
   iv =2BDD0D132A3EB385E932112E5B353B82
   ```
   
   4. 保存好这个结果

3. 获取源码
   
   1. `git clone https://github.com/godotengine/godot.git`
   
   2. 选择对应的版本 `git checkout tags/4.2-stable`

4. 构建导出模板
   
   1. 在源码根目录打开cmd，输入`set SCRIPT_AES256_ENCRYPTION_KEY=你的key`
   
   2. 如果不需要 C# 功能：
      
      ```shell
      scons platform=windows use_mingw=yes arch=x86_64 target=template_release use_llvm=true mingw64_prefix=x86_64-w64-mingw32-```
      ```
      
      如果需要 C#：
      
      ```shell
      scons platform=windows use_mingw=yes arch=x86_64 target=template_release use_llvm=true mingw64_prefix=x86_64-w64-mingw32- module_mono_enabled=yes
      ```
   
   3. 等待构建结束，结束后在 /bin 会有以下两个文件
      
      ```
      godot.windows.template_release.x86_64.llvm.mono.console.exe
      godot.windows.template_release.x86_64.llvm.mono.exe
      ```

5. 使用构建模板
   
   项目 - 导出 - 添加 - Windows Desktop
   
   选项 - 自定义模板 - 调试/发布 定位到你的 `godot.windows.template_release.x86_64.llvm.mono.exe`
   
   加密 - 加密导出的PCK - 加密密钥 填写前面生成的密钥

#### 解密

不幸的是，加密密钥可以被程序猜到

[gdke](https://github.com/char-ptr/gdke) 可以猜测密码

### 代码混淆

#### 加密

建议使用现成的插件 [gdmaim](https://github.com/cherriesandmochi/gdmaim)

#### 解密

解包后的代码仍然可以运行，现代IDE都有字符串函数名重命名的能力，只要花时间在上面，就一定可以还原出大部分逻辑

### 使用 C#

#### 加密

因为目前大部分工具没办法还原完整的 C# 代码，所以使用 C# 是相对安全的选项

#### 解密

用 C# 反编译软件即可，如[dnSpy](https://github.com/dnSpyEx/dnSpy)

## 进阶防护

### 改引擎代码

Godot 以 MIT 协议开源，所以我们可以放心修改引擎源码

#### 自定义PCK加密

## 极端防护

### 加壳

#### 加密

这个没什么好说的，以性能为代价换取安全

建议使用 [VMProtect](https://vmpsoft.com/) 的虚拟化和控制流混淆

#### 逆向

这个真没什么好办法，建议去看其他人写的脱壳文章

## Credits

https://github.com/bruvzg/gdsdecomp

[Compiling with PCK encryption key ](https://docs.godotengine.org/en/4.2/contributing/development/compiling/compiling_with_script_encryption_key.html)

[Protecting Your Godot Project from Decompilation](https://godot.community/topic/35/protecting-your-godot-project-from-decompilation)

[Protecting Your Godot Project from Decompilation (Web archive)](http://web.archive.org/web/20240619110319/https://godot.community/topic/35/protecting-your-godot-project-from-decompilation)
