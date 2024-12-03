+++
title = '用链式代理加速你的节点'
date = 2024-12-03T06:55:06+08:00
draft = false
+++

本文会讲述如何使用链式代理加速你的垃圾节点

<!--more-->

## 什么时候要用到？

在网上有很多公开的免费低延迟节点，但危险性非常高并且 IP 不纯净。自己购买的 VPS 可能对国内优化不好，但对国际出口的优化不错，并且 IP 纯净，这时候既可以使用链式代理加速你对自建节点的访问。并且，这样的代理方式不会使你的 VPS IP 被墙

## 什么是链式代理?

链式代理会首先与你的节点通信，确定好加密协议和方式，然后使用中继节点交换加密的请求信息在这个过程中，中继节点无法获取你的加密(Https)请求，只知道你和目标节点的 IP 以及访问的域名，这样的代理方式在最小化风险的情况下可以优化你的访问速度

注意，http 或未加密请求可以被中继节点获取

## 配置

由于公益节点很大概率不稳定，所以建议使用多个公益节点，使用链式代理进行加速，负载均衡。由于你的落地 IP 不变，节点负载均衡并不会对访问产生影响

推荐使用 Clash Verge Rev 进行负载均衡和链式代理

### 单个节点

```yaml
proxies:
  - name: 免费节点
    server: 1.1.1.1
    port: '1111'
    type: vmess

  - name: 落地节点
    server: 2.2.2.2
    port: '1111'
    type: vmess

proxy-groups:
  - name: "链式代理"
    type: relay
    proxies:
      - 免费节点
      - 落地节点

  - name: 节点选择
    type: select
    proxies:
      - DIRECT
      - 链式代理
```

在这个配置中，链式代理的顺序如下：免费节点->落地节点

#### 多个节点或订阅链接

```yaml
proxies:
  - name: 落地节点
    server: 2.2.2.2
    port: '1111'
    type: vmess

proxy-providers:
  sub1:
    type: http
    url: https://订阅url
    interval: 86400
    health-check:
      enable: false
      interval: 600
      url: https://www.gstatic.com/generate_204

proxy-groups:
  - name: "负载均衡"
    type: load-balance
    use:
      - sub1
    url: 'https://www.gstatic.com/generate_204'
    interval: 300
    strategy: round-robin

  - name: "链式代理"
    type: relay
    use:
      - 负载均衡
      - 落地节点

  - name: 节点选择
    type: select
    proxies:
      - DIRECT
      - 链式代理
```

~~虽然没什么用吧但是挺有意思的~~