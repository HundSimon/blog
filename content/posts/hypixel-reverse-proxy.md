+++
title = 'Protect Your Godot Game'
date = 2024-11-23T19:30:45+08:00
draft = false
+++

hypixel 在国内的延迟很高，本文会提供搭建 加速ip/加速节点 的教程，以及一些进阶功能

<!--more-->

## 安装

本文推荐使用 [zbproxy](https://github.com/layou233/ZBProxy) 进行代理

1. 下载最新发行版

```shell
wget -O /usr/local/bin/zbproxy https://github.com/layou233/ZBProxy/releases/latest/download/ZBProxy-linux-amd64-v1
```

2. 给予文件执行权限

```shell
sudo chmod +x /usr/local/bin/zbproxy
```

3. 配置 systemd

```shell
sudo mkdir -p /usr/local/etc/zbproxy
```

写入 `zbproxy.service`

```shell
sudo cat << 'EOF' > /etc/systemd/system/zbproxy.service
[Unit]
Description=ZBProxy Service
Documentation=https://github.com/layou233/ZBProxy
After=network-online.target

[Service]
Type=simple
WorkingDirectory=/usr/local/etc/zbproxy
ExecStart=/usr/local/bin/zbproxy
ExecReload=/bin/kill -s HUP $MAINPID
KillSignal=SIGTERM
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
```

载入配置

```shell
systemctl daemon-reload
```

启动服务

```shell
systemctl start zbproxy
```

## 配置

所有配置都在 `/usr/local/etc/zbproxy/ZBProxy.json`

### 代理对象

```json
{
   "Outbounds":[
      {
         "Name":"Hypixel-out",
         "TargetAddress":"mc.hypixel.net",
         "TargetPort":25565,
         "Minecraft":{
            }
      }
   ]
}
```

- `TargetAddress` 是要代理的mc服务器
- `TargetPort` 是要代理的服务器的端口

### MOTD

```json
"Minecraft":{
   "EnableHostnameRewrite":true,
   "OnlineCount":{
      "Max":114514,
      "Online":-1,
      "EnableMaxLimit":false
   },
   "IgnoreFMLSuffix":true,
   "MotdFavicon":"{DEFAULT_MOTD}",
   "MotdDescription":"§dProxy of hypixel"
}
```
- `Max` 是代理服务器显示的最大人数
- `MotdFavicon` 是代理服务器的图标，可以使用 base64 格式的图片([图片转换](https://launium.com/app/file-base64))
- `MotdDescription` 是代理服务器的描述，可以使用 § 进行颜色修改

### 白名单

```json
"Minecraft":{
    "NameAccess": {
        "Mode": "allow",
        "ListTags": ["list1", "list2"]
                },
}
```

`"Mode": "allow"` 表示允许 `ListTags` 内的玩家进入

```json
"Lists": {
    "list1": ["AAA", "BBB"],
    "list2": ["CCC"],
}
```

### 示例配置

这个配置中，将25565作为出口端口，仅允许`["HundSimon","Splithoff","HIMlaoS_Misa"]` 使用代理服务器

```json
{
    "Log": {
        "Level": "debug"
    },
    "Services": [
        {
            "Name": "Hypixel-in",
            "Listen": 25565,
            "IPAccess": {
                "Mode": ""
            },
            "Outbound": {
                "Type": ""
            }
        }
    ],
    "Router": {
        "DefaultOutbound": "Hypixel-out",
        "Rules": [
            {
                "Type": "always",
                "Sniff":"minecraft"
            }
        ]
    },
    "Outbounds": [
        {
            "Name": "Hypixel-out",
            "TargetAddress": "mc.hypixel.net",
            "TargetPort": 25565,
            "Minecraft": {
                "EnableHostnameRewrite": true,
                "OnlineCount": {
                    "Max": 114514,
                    "Online": -1,
                    "EnableMaxLimit": false
                },
                "HostnameAccess": {
                    "Mode": ""
                },
                "NameAccess": {
                    "Mode": "allow",
            "ListTags":["whitelist"]
                },
                "PingMode": "",
                "MotdFavicon": "{DEFAULT_MOTD}",
                "MotdDescription": "§dMelaton's Hypixel Proxy"
            },
            "ProxyOptions": {
                "Type": ""
            }
        }
    ],
    "Lists": {
    "whitelist":["HundSimon","Splithoff","HIMlaoS_Misa"]
    }
}
```

## 进阶功能

### Badlion 使用 hypixel modules

Badlion 使用正则匹配 hypixel 的服务器以启用对应的功能，代理服务器的域名无法匹配正则

官方正则表达式如下：

```regexp
^(?:.*\.)?hypixel\.(?:net|io)\.?
```

通过在配置文件添加自定义正则，即可实现在代理服务器上使用 hypixel modules

配置文件在此路径

```
%appdata%\.minecraft\badlion_settings.json
```

通过添加以下条目，即可实现对 `*.example.com` 的通配

```json
"customHypixelIpRegex": "^(?:.*.)?(?:hypixel.(?:net|io)|example.com).?",
```
### Lunar 使用 hypixel modules

将代理域名的 SRV 名改成 `hypixel.net.exaple.com` 类似的域名，即可绕过正则选择

## Credits

[zbproxy](https://github.com/layou233/ZBProxy)