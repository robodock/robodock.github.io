---
layout: post
title: "在 BeagleBone Black 或 Raspberry Pi 上使用 RTL-SDR usb dongle"
date: 2016-04-09T15:51:52+08:00
---

## 在 BeagleBone Black 或 Raspberry Pi 上使用 RTL-SDR usb dongle

編譯參數如下：

	cd rtl-sdr/build
	cmake ../ -DINSTALL_UDEV_RULES=ON -DDETACH_KERNEL_DRIVER=ON
	make
	sudo make install
	ldconfig

重新開機後應可成功驅動，可使用 `rtl_test` 命令測試。

啟動 rtl_tcp server 供其他用戶端程式(如 `SDR#` 或 `GQRX` 連接使用：

	rtl_tcp -a xxx.xxx.xxx.xxx
	
xxx 為 ip 位址。