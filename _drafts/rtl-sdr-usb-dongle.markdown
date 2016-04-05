---
layout: post
title: "RTL-SDR USB Dongle"
---

編譯參數

	cd rtl-sdr/build
	cmake ../ -DINSTALL_UDEV_RULES=ON -DDETACH_KERNEL_DRIVER=ON
	make
	sudo make install
	ldconfig

