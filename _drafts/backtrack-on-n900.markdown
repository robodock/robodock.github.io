---
layout: post
title: "BackTrack on N900"
---

需要安裝

	Rootsh v1.8
	Easy Chroot v0.3.5-1 Fremantle

取得 root access

	$sudo gainroot
	
取得 BackTrack 5 for ARM img，像是 BT5-GNOME-ARM.7z，解開為 .img 檔

	$sudo gainroot
	$mkdir /mnt/bt5
	$qchroot ~/bt5.img /mnt/bt5
	$export USER=root
	$vncpass 'xxxx'	//自行設定密碼
	$vncserver -geometry 800x470
	startvnc
	
結束離開

	$stopvnc
	$exit
	$qumount /mnt/bt5
	$exit
	
