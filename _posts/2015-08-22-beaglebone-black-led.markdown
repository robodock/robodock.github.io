---
layout: post
title: "關閉 BeagleBone Black LED 閃燈"
date: 2015-08-22T20:11:08+08:00
---

早期的 **BeagleBone Black** 的 **LED** 指示燈 真的是太閃亮了，特別是在晚上，閃的整個房間像舞廳一樣。
四個 LED 指示燈的控制項位於 

	/sys/class/leds
	
目錄下，usr0~3 分別代表四個指示燈，想要在開機後自動關閉的話，只要在 `/etc/rc.local` 中 `exit 0` 敘述前加入底下幾行：


	echo none > /sys/class/leds/beaglebone\:green\:usr0/trigger
	echo none > /sys/class/leds/beaglebone\:green\:usr1/trigger
	echo none > /sys/class/leds/beaglebone\:green\:usr2/trigger
	echo none > /sys/class/leds/beaglebone\:green\:usr3/trigger

存檔後重新啟動即可。