---
title: 树莓派centos7-mimimal连接WiFi
date: 2018-04-17 10:12:33
tags: 瞎搞
---
###### #nmcli d命令查看网络情况

```
[root@centos-rpi3 ~]# nmcli d
DEVICE  TYPE      STATE         CONNECTION
eth0    ethernet  connected     eth0
wlan0   wifi      disconnected  --
lo      loopback  unmanaged     --
```
    
###### #nmcli d wifi查看附近WiFi

```
[root@centos-rpi3 ~]# nmcli d wifi
*  SSID               MODE   CHAN  RATE       SIGNAL  BARS  SECURITY
   501                Infra  5     54 Mbit/s  60      ▂▄▆_   WPA2
   --                 Infra  1     54 Mbit/s  20      ▂___   WPA2 
```
       
###### #nmcli d wifi connect your-wifi-ssid password wifi-passwd 连接WiFi

```
[root@centos-rpi3 ~]# nmcli d wifi connect <your-wifi-ssid> password <wifi-passwd>
[root@centos-rpi3 ~]# nmcli d
DEVICE  TYPE      STATE      CONNECTION 
eth0    ethernet  connected  eth0       
wlan0   wifi      connected  <your-wifi-ssid>    
lo      loopback  unmanaged  --       
```

树莓派3b+，CentOS-Userland-7-armv7hl-Minimal-1708-RaspberryPi3.img.xz测试
img下载：http://mirror.centos.org/altarch/7/isos/armhfp/      



 

