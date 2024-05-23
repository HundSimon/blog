+++
title = '我的 Api'
date = 2024-05-23T15:32:20+08:00
draft = false

+++

## Randomfurry api (随机兽人图片)

请求地址 https://api.melaton.top/randomfurry

演示站点 https://static.melaton.top/randomfurry/

Parameters:

- format 
  - json (默认) 返回 json 格式
  - image 302到图片地址
- r18
  - 0 (默认) 返回 SFW 的数据
  - 1 返回 NSFW 的数据

元数据在每周一爬取，默认保存每周 top 200 的数据

开源地址 https://github.com/HundSimon/randomfurry-api
