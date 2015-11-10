---
layout: post
title: "在 Raspberry Pi 與 BeagleBone Black 上設定使用 Edimax EW-7811Un 迷你無線網卡"
date: 2015-08-22T19:53:23+08:00
---

**Edimax EW-7811Un** 迷你無線網卡使用 Realtek RTL8188CUS 晶片，在 Linux Kernel 中以相容的 8192cu 驅動程式模組來驅動，體積小，堪稱 Raspberry Pi 與 BeagleBone 的絕配。
>註：這類迷你 USB 無線網卡在 BeagleBone 上使用時會受到 HDMI 插槽的干擾，請參考[這篇](http://)


但各家 Linux Distribution 中搭配的無線網路管理工具不一，有些使用 **connman**，有些使用 **wpasupplicant**，有些則使用 **wicd**，設定方法也不一。

在 Raspberry Pi 上使用 Edimax 的 EW-7811Un 迷你無線網卡相當簡單，只要在 /etc/wpa_supplicant/wpa_supplicant.conf 中設定如下：

    update_config=1
    ctrl_interface=/var/run/wpa_supplicant

    network={
        scan_ssid=1
        ssid="your SSID"
        psk="your WPA-key"
      }

其中 `scan_ssid=1` 用於 “隱藏SSID” 的 WiFi 環境。

另外如果有安全性的考量的話，記得把 `wpa_supplicant.conf` 檔案設定為其他人無法讀取。

然後編輯 /etc/network/interface，找到有關 WiFi 設定部分，加入 wpa-conf 設定如下:

    auto wlan0
    iface wlan0 inet dhcp
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf


重新啟動應可順利連線至無線網路。

本以為同樣的方法應該適用於 **BeagleBone Black**，沒想到折騰了好久才搞定：

目前 BeagleBone Black 採用由 Robert Nelson 維護的 [Debian Image](http://rcn-ee.com/rootfs/bb.org/)，主要分為二類型，一種為適用於安裝於 SD卡的 Image，另一種為用來寫入內建記憶體(eMMC)的 Flasher Image，但因為新版的 BBB Rev.C 加大為內建 4GB eMMC，所以 Flasher Imager 又分為 4GB 與 2GB 兩種版本。 

另外 Image 檔又可區分為“圖形視窗介面”與“Console介面"，可視自己專案需求選擇適合的 Image。這裏我要做自動控制用的，所以用的是使用 on board eMMC 的精簡 Console 版本，系統裝好不到200MB ，還有很多空間可利用：

`BBB-eMMC-flasher-debian-7.8-console-armhf-2015-03-01-2gb.img.xz`

本來以為只要修改網路相關設定組態檔中的一些參數，就可輕鬆讓無線網卡運作，但我錯了。

這個精簡 Console Image 真的是很精簡，並未包含任何無線網路管理工具， 須先安裝wpasupplicant 工具

	# apt-get update
	# apt-get install wpasupplicant 
	
然後呢？只要套用上述與 Raspberry Pi 相同的設定就可以了。 