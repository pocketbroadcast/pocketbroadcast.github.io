---
layout: post
title: "SSH the LG HomBot (VR64703)"
date: 2016-09-04
---

# Introduction:

Enabling WLAN would offer us dozens of possibilities to extend the functionality and better configure the HomBot, so let\'s get this done!

Searching for a way to enable WLAN on the LG HomBot lead me to 
[roboter-forum.com][roboterforumthread]. Since, the described method is to update the HomBot with
a cracked firmware image (compiled by someone on the Internet), this way was not satisfying at all.

The following has been tested with HomBot VR64703 and firmware version 13865 and 16552 (get the current firmware [at][hombotfirmware]).



# TL;DR

Buy a compatible WLAN Dongle (e.g. [Edimax EW-7811UN 150Mbps Wireless Nano USB Adapter][edimax]) plug it in the USB connector on the bot and
configure your wlan router as access point using the SSID `RK_HIT` and no encryption. (Use this method only as a starting point, unencrypted
networks bare a tremendous security risk)

Afterwards restart the HomBot and check for devices connected to your router. Download [putty.exe][puttydownload] and connect to the IP of your HomBot using
the username `root` and password `most9981`.


![SSH Connection to HomBot]({{ site.url }}/assets/hombot-wlan/sshtobot.png)

# namespace detail {

This part is thought to be a complement of **TL;DR** where I elaborate the achieved knowledge in more detail. 

#### The firmware update

describe what can be found on the image and what axf is

#### Disassembling and decompiling the binary

describe the process of disassembling and decompiling the axf in ida

{% highlight cpp linenos %}

      if ( stReadSubHeader.unDataSize == -1 )
      {
        if ( !(stReadSubHeader.unFlag & v2) && stReadSubHeader.unFlag )
        {
          printf("=> Skip %s\n", pName);
        }
        else
        {
          printf("=> Make directory %s\n");
          if ( mkdir((const char *)pName, 0x1FDu) && *_errno_location() != 17 )
          {
            printf("UnpackagingFile - can't make directory (%s)\n", pName);
            return -2;
          }
        }
      }
      else if ( stReadSubHeader.unFlag & v2 || !stReadSubHeader.unFlag )
      {
        printf("=> Make file %s\n", pName);
        unlink((const char *)pName);
        nWriteFile = open((const char *)pName, 577);
        if ( nWriteFile == -1 )
        {
          printf("UnpackagingFile - File open error (%s)\n", pName);
          return -3;
        }
{% endhighlight %}

#### DatExtractor was born!

By this, it was not hard to write a tool extracting the `.dat` file contained in the firmware update.
The source code of the **DatExtractor** can be found on [github][datextractorrepo].

TODO: get DatExtractor a command line interface and add a screenshot of it\'s usage.

#### Browsing the firmware image

describe `.rclocal`
describe how we achieved the utilization of the update script
describe how we found out `SSID: RK_HIT`

#### Bring WLAN to the Bot

describe which wlan chips are supported by the firmware
refer to TL;DR


[roboterforumthread]: http://www.roboter-forum.com/showthread.php?10009-LG-Hombot-3-0-WLAN-Steuerung-per-Weboberfl%E4che
[edimax]: https://www.amazon.de/gp/product/B003MTTJOY/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1
[hombotfirmware]: http://www.lg.com/at/service/software-firmware?keyword=&superCateId=CT20086025&categoryId=CT20086032&modelNum=VR64703LVMB
[puttydownload]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[datextractorrepo]: https://github.com/pocketbroadcast/hombot-tools
