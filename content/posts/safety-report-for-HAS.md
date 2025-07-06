+++
title = 'HAS 分析报告'
date = 2025-07-06T12:16:53+08:00
draft = false

+++

**分析日期:** 2025.7.6
**分析对象:** 安卓应用 `zpm.rpgmv.gnhas` (版本 1.01) 
**VirusTotal:** https://www.virustotal.com/gui/file/c0296bd2d333522b809fbe2330477e951ee512f030b4009bec9e1c5de1b900d6/details

```
MD5 ea3e9351a9d56ec4f87e08798b3264de
SHA-1 b95f67905dad8bd78e8d46516f9fa96e769ac6be
SHA-256 c0296bd2d333522b809fbe2330477e951ee512f030b4009bec9e1c5de1b900d6
```

### Summary

分析确认，该应用包含一个高风险的恶意后门。尽管VirusTotal将该威胁归类为银行木马（Trojan-Spy.AndroidOS.Banker），但深入分析表明，这是一个分类误报。实际是一个通用的、休眠状态的后门。

此后门为攻击者提供了执行任意系统命令和读写本地文件的能力。配合应用程序清单（Manifest）中发现的严重安全配置错误（如全局可调试标志）和过度的文件系统权限。

### Analysis Scope

* **入口脚本:** `main.js`

* **插件配置文件:** `js/plugins.js`

* **Android Manifest:** `AndroidManifest.xml`

* **核心引擎脚本:** `rpg_managers.js`

*   **高风险插件代码:**
    *   `BaiSZ_tongyong.js`
    *   `BaiSZ_X_quest.js`
    *   `ZPM_Telephone.js`
    *   `OrangeMapshot.js` (潜在后门)
    
* **事件数据:** `CommonEvents.json` 及所有 `MapXXX.json` 文件（基于搜索结果）

### Key Findings

#### 潜在的风险

*   **文件:** `js/plugins/OrangeMapshot.js`
*   **问题:**
    1.  **任意命令执行:** 插件通过 `require('child_process').exec` 获得了在用户操作系统上执行任何命令行指令的能力。相当于为攻击者打开了一个远程控制的后门。
    2.  **文件系统完全访问:** 插件通过 `require('fs')` 获得了读写用户本地文件的权限，可用于窃取数据或部署其他恶意软件。
*   **当前状态:** 分析表明，该恶意函数 `OrangeMapshot.saveMapshot()` 暂时并未在任何地图事件或公共事件中被调用。其唯一的内置触发器是键盘按键 F7。因此，该后门目前处于休眠状态，但只要文件存在，威胁就依然存在。

#### 不安全的应用程序配置

*   **文件:** `AndroidManifest.xml`
*   **问题:** 
    1. 应用程序被设置为可调试 (`android:debuggable="true"`)。
    2. 不必要的 `android.permission.WRITE_EXTERNAL_STORAGE` 和 `READ_EXTERNAL_STORAGE`。当此权限与OrangeMapshot.js 相结合时，攻击者可以利用后门扫描并上传用户的个人文件（文档、照片等），或将勒索软件等其他恶意载荷写入设备。

#### 默认签名

- **问题:** 程序使用了默认的 Android Debug 签名，可能导致误报