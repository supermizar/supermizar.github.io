---
title: ubuntu 18.04 'Wired Unmanaged'问题解决
date: 2018-09-01
updated: 2018-09-01
categories: ubuntu
tags:
- ubuntu
- 有线网络配置
---

## 问题描述

办公PC安装18.04后，出现了奇怪的bug：右上角没有有线网络连接的图标，显示'Wired Unmanaged'，在网络配置界面也只有VPN和无线网络的配置，实际上此时电脑已经连接网络，通过DHCP获得IP地址可以正常访问网络，可就是看不到在GUI上配置有线网络的地方。办公环境下不能配置固定IP的麻烦很多，譬如Synergy等需要配置IP的应用，所以尝试解决。

## 解决方法

最直接搜到的办法是：[方法1](https://askubuntu.com/questions/2901/unmanaged-network-icon-network-manangement-disabled)

修改**/etc/NetworkManager/nm-system-settings.conf**，将managed字段改为true，再重启network-manager服务：

```bash
sudo service network-manager restart
```

几乎解决了所有人的问题，但不包括我

解决了我的问题的方法：[方法2](https://forum.linuxconfig.org/t/wired-unmanaged-ubuntu-desktop-issue/1574)

原链接说得非常明白了，18.04有两套网络管理软件：server版对应netplan，desktop版对应NetworkManager。由于大部分人是升级安装或直接安装desktop版本，所以方法1一般都有效。但由于本人的洁癖强迫症，安装系统时用了mini.iso。因此netplan的默认renderer仍然没有改成NetworkManager。

所以，解决方法就是，修改**/etc/netplan/01-netcfg.yaml**，将renderer字段由networkd改为NetworkManager（大小写敏感），再应用修改：

```bash
sudo netplan apply
```

问题解决。







