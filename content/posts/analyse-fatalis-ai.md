+++
title = 'MHW黑龙AI分析'
date = 2024-07-28T15:18:18+08:00
draft = true
+++

闲来无事 分析一下黑龙的AI

<!--more-->

## 前言

通过[WorldChunkTool](https://github.com/mhvuze/WorldChunkTool)，我们可以提取出黑龙的AI (位于em/em013/00/data)，再使用[MHWI-Monster-THK-Editor](https://github.com/NackDN/MHWI-Monster-THK-Editor)，即可查看黑龙的AI逻辑

本文分阶段讲解黑龙的AI，以下是文件对应表

```
Combat_Main = em013_00.nack @ 97793049
Combat_Enter = em013_01.nack @ 53ACD122
Search = em013_02.nack @ 0A3FC6CC
Discover = em013_03.nack @ B45B36F5
THK_04 = em013_00.nack @ 97793049
THK_05 = em013_01.nack @ 53ACD122
THK_06 = em013_03.nack @ B45B36F5
Rage_Enter = em013_07.nack @ 0A3FC6CC
THK_08 = em013_08.nack @ 0A3FC6CC
Mount = em013_11.nack @ 536F468B
Concealment = em013_13.nack @ D5C2636C
THK_14 = em013_13.nack @ D5C2636C
Non_Combat_Main = em013_15.nack @ 0A3FC6CC
Non_Combat_Hit = em013_18.nack @ CDE4D8D3
THK_19 = em013_19.nack @ 0A3FC6CC
Target_Lost = em013_29.nack @ E68EC1AC
Global = em013_55.nack @ 299E388A
THK_60 = em013_60.nack @ 0A3FC6CC
```

## P1

首先进入战斗，触发 `Combat_Enter` ,

## P2

## P3

## Credits

[WorldChunkTool](https://github.com/mhvuze/WorldChunkTool)

[MHWI-Monster-THK-Editor](https://github.com/NackDN/MHWI-Monster-THK-Editor)

[MonsterHunterWorldModding](https://github.com/Ezekial711/MonsterHunterWorldModding/)

[Leviathon](https://github.com/AsteriskAmpersand/Leviathon)

怪物猎人世界数据全表合一 by冰块&Jodo
