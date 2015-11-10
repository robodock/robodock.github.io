---
layout: post
title: "迷你 USB WiFi 網卡與 BeagleBone Black HDMI 的干擾問題"
---

如果你跟我一樣被 BeagleBone Black 與 mini USB 無線網卡搞的灰頭土臉，那你可能要看看這篇 [BeagleBone](http://)。

在 BeagleBone 上 USB 插槽就在 HDMI 插槽旁邊，當使用如 Edimax EW-7811Un 這類的迷你 USB 無線網卡時，因為 USB 迷你網卡的天線過於接近 HDMI 插槽，HDMI 的訊號射頻會嚴重干擾 WiFi 天線。

我這邊的實際狀況是，當距離 AP 在 2 公尺內時還可正常通訊，到 5 公尺左右就已無法建立通訊了。這種 [香草冰淇淋](http://) 的問題有時令人摸不著頭緒，耗費了許多時間在嘗試不同的組態檔設定。

解決方法有兩種：

第一：使用 USB 延長線或延伸出來的 USB 集線器，將 USB 迷你無線網卡遠離 HDMI 插槽，或是改用體積較大，附有外部天線的 USB 無線網卡。

第二：如果 BeagleBone 是當成 Headless 操作，不會連接螢幕使用，那就把 HDMI 輸出關閉，作法如下：

修改 `/boot/uEnv.txt`

找到 `##Disable HDMI` 項目，將 `##cape_disable=....` 前方的 ## 註解拿掉如下：

	##Disable HDMI (v3.8.x)
	cape_disable=capemgr.disable_partno=BB-BONELT-HDMI,BB-BONELT-HDMIN 

>注意 uEnv.txt 設定檔中有很多類似的項目，例如 `##Disable HDMI/eMMC` 等等，不要改錯了，這個設定檔的內容會影響硬體功能的運作。

存檔後重新開機。

OK，現在會影響 BeagleBone 與 AP 通訊的就只有距離了。