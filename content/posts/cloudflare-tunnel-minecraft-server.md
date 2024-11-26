+++
title = 'Protect Your Godot Game'
date = 2024-11-26T19:30:45+08:00
draft = false
+++

仅使用 CloudFlare Tunnel 的免费功能来搭建外网可访问的 MC 服务器 以及优选ip

<!--more-->

前排提醒：客户端也需安装 CloudFlare Tunnel！延迟 150-200ms 左右(大陆)，并不适合延迟敏感的游戏，可以用于只有 ipv6 的服务器

实测非大陆地区效果非常好

## 配置服务端和域名

1. 部署 CloudFlare Tunnel，绑定 tcp 协议的 MC 服务器 (能看到这篇文章的人想必都会吧)

2. 绑定两个 **一级域名不同** 的域名 一个主域名，用于 MC 访问，一个回源域名，用于应用优选 IP。假设主域名为 `mc.example1.com`，回源域名为 `ori.example2.com`

3. 打开 `ori.example2.com`，进入 `SSL/TLS` `自定义主机名`，设置回退源为 `ori.example2.com`，添加自定义主机名 `mc.example1.com`

4. 设置 `mc.example1.com` 的 `CNAME` 为 `ori.example2.com`

5. 设置 `ori.example2.com` 的 `CNAME` 为 `ori.example2.com`

至此，域名配置结束

## 配置客户端

若要域名直接访问，则需要付费服务 Cloudflare Spectrum，本文介绍无需付费但更麻烦的方法

客户端本地安装 [CloudFlare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/)

打开命令行，输入

```bash
cloudflared access tcp --hostname mc.example1.com --url localhost:25565
```

打开 MC，即可通过 localhost 访问到服务器

## 优选 CloudFlare IP

CloudFlare IP 有很多优选的方式，本文只介绍不常见或者更有效的几种

如何应用优选 IP / 域名？

若是 IP，则需要先配置 A 记录到一个域名，如 `ip.example2.com`，然后再设置 CNAME `mc.example1.com` 的 `CNAME` 为 `ip.example2.com`

域名则直接可以 CNAME `mc.example1.com` 的 `CNAME` 为 对应的域名

### 1.1.1.1

1.1.1.1 / 1.0.0.1 是 CloudFlare 的 DNS 服务器，可以用来优选 IP，据说有奇效

由于 1.1.1.1 在国内部分地区阻断严重，可以使用 1.1.1.1/24 范围内的所有 IP，如 1.1.1.34

### 现有网站优选

有很多套 CloudFlare 的网站可以直接用于 CNAME，因为这些网站的 IP 已经经过优选或其他设置，推荐的域名如下：

```
time.cloudflare.com
shopify.com
time.is
icook.hk
icook.tw
ip.sb
japan.com
malaysia.com
russia.com
singapore.com
skk.moe
www.visa.com
www.visa.com.sg
www.visa.com.hk
www.visa.com.tw
www.visa.co.jp
www.visakorea.com
www.gco.gov.qa
www.gov.se
www.gov.ua
www.digitalocean.com
www.csgo.com
www.shopify.com
www.whoer.net
www.whatismyip.com
www.ipget.net
www.hugedomains.com
www.udacity.com
www.4chan.org
www.okcupid.com
www.glassdoor.com
www.udemy.com
www.baipiao.eu.org
cdn.anycast.eu.org
cdn-all.xn--b6gac.eu.org
cdn-b100.xn--b6gac.eu.org
xn--b6gac.eu.org
edgetunnel.anycast.eu.org
alejandracaiccedo.com
nc.gocada.co
log.bpminecraft.com
www.boba88slot.com
gur.gov.ua
www.zsu.gov.ua
www.iakeys.com
edtunnel-dgp.pages.dev
www.d-555.com
fbi.gov
```

### 优选域名

有很多用其他CDN厂商进行负载均衡的域名，可以自动根据地址选择最好的 IP，推荐的域名如下

```
speed.marisalnc.com
freeyx.cloudflare88.eu.org
*.cloudflare.182682.xyz
115155.xyz
cdn.2020111.xyz
cfip.cfcdn.vip
cf.0sm.com
```

### Credits

- [freedidi](https://www.freedidi.com/10143.html)

- [wetest.vip](https://www.wetest.vip/)