+++
title = '我的 Api'
date = 2024-05-23T15:32:20+08:00
draft = false

+++

## Randomfurry api (随机兽人图片)

<details>
  <summary>查看演示(NSFW警告)</summary>
  <div style="display: flex; gap: 10px;">
    <img src="https://api.melaton.top/randomfurry/?format=image" style="object-fit: contain; width: 30%;">
    <p>SFW</p>
    <img src="https://api.melaton.top/randomfurry/?format=image&r18=1" style="object-fit: contain; width: 30%;">
    <p>NSFW</p>
  </div>
</details>

请求地址 https://api.melaton.top/randomfurry

演示站点 https://api.melaton.top/randomfurry/index

演示站点nsfw https://api.melaton.top/randomfurry/index18

Parameters:

- format 
  - json (默认) 返回 json 格式
  - image 302到图片地址
- r18
  - 0 (默认) 返回 SFW 的数据
  - 1 返回 NSFW 的数据

元数据每3天爬取一次 链接: https://static.melaton.top/randomfurry/metadata.json

开源地址 https://github.com/HundSimon/randomfurry-api

默认图片代理: https://pixiv.re
