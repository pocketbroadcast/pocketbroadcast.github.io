---
layout: post
title: "SSH the LG HomBot (VR64703)"
date: 2016-09-04
---

# Introduction:

Enabling WLAN would offer us dozens of possibilities to extend the functionality and better configure the abilities of the on the HomBot, so let\'s get this done!

Searching for a way to enable WLAN on the LG HomBot lead me to 
[roboter-forum.com][roboterforumthread]. Since, the described method is to update the HomBot with
a cracked firmware image (compiled by someone on the Internet), this way was not satisfying at all.

The following has been tested with HomBot VR64703 and firmware version 13865 and 16552 (get the current firmware [at][hombotfirmware]).



# TL;DR

Buy a compatible WLAN Dongle (e.g. [Edimax EW-7811UN 150Mbps Wireless Nano USB Adapter][edimax]) plug it in the USB connector on the bot and
configure your wlan router as access point using the SSID \"RK_HIT\" and no encryption. (Only use this method for testing, as it is an tremendous security leak)

Afterwards restart the HomBot and check for devices connected to your router. Download [putty.exe][puttydownload] and connect to the IP of your HomBot using
the username \'root\' and password \'most9981\'.


![SSH Connection to HomBot]({{ site.url }}/assets/sshtobot.png)

# namespace detail {





# The firmware update

# Disassembling and decompiling the binary

# Browsing the firmware image

# Bring WLAN to the Bot

# Connect via SSH

## Cracking the password

explain how to get the password


[roboterforumthread]: http://www.roboter-forum.com/showthread.php?10009-LG-Hombot-3-0-WLAN-Steuerung-per-Weboberfl%E4che
[edimax]: https://www.amazon.de/gp/product/B003MTTJOY/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1
[hombotfirmware]: http://www.lg.com/at/service/software-firmware?keyword=&superCateId=CT20086025&categoryId=CT20086032&modelNum=VR64703LVMB
[puttydownload]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
