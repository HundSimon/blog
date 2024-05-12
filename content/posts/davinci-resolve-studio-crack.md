+++
title = '在 Linux 下安装 Davinci Resolve Studio'
date = 2024-05-12T15:52:23+08:00
draft = false
+++

```shell
yay -S davinci-resolve-studio

sudo perl -pi -e 's/\x00\x85\xc0\x74\x7b\xe8/\x00\x85\xc0\xEB\x7b\xe8/g' /opt/resolve/bin/resolve

sudo rm -f /opt/resolve/libs/libglib-2.0.so*
sudo rm -f /opt/resolve/libs/libgio-2.0.so*
sudo rm -f /opt/resolve/libs/libgmodule-2.0.so*

```

在 Arch Linux 下完美运行

