---
layout: post
title: "使用 BeagleBone Black 或 Raspberry Pi 打造簡易的網路 VoIP/Voice Chat 設備 (1/2)"
date: 2016-05-19T22:20:03+08:00
keywords: beaglebone black, seren, voip, python, raspberry pi
---

##使用 BeagleBone Black/Raspberry Pi 打造簡易的網路 VoIP/Voice Chat 設備 (1/2)

###Seren on BeagleBone Black

在 Linux 上有隻名為 **Seren** 的簡易 VoIP 程式，可在終端機模式下進行語音通訊與文字交談，不必使用外部廠商提供的通訊軟體或註冊任何服務帳號，很適合小型輕量化系統使用，搭配 BeagleBone 或 Raspberry Pi 的 GPIO 做通訊開關控制，可衍生出不少應用。

Seren 的前身稱為 Parole，使用 ***C*** 語言開發，利用 ***alsa*** 程式庫與音效卡溝通，並用 ***opus*** codec 處理音訊壓縮，進化至 Seren 後，增加了使用 ***gmp*** 演算法程式庫優化程式，可允許多達 10 人加入同一音訊會議，並加入利用 ***ncurse*** 做成的文字使用者介面，方便操作。

Seren 目前可在 Fedora/RHEL/CentOS Linux 版本中，直接從官方程式庫(Repositories)下載：

	# yum install seren
	
Debian/Ubuntu 系列則還未收入官方程式庫，因此要在 BeagleBone Black/Raspberry Pi 上使用 Seren，須自行[下載](http://holdenc.altervista.org/seren/downloads/seren-0.0.21.tar.gz)原始碼編譯，編譯時需要的相依性套件如下：

	$ sudo apt-get install build-essential libasound2-dev libopus-dev libogg-dev libgmp-dev libncursesw5-dev
	
將原始碼下載解壓至一工作目錄後，在工作目錄下：
	
	$ ./configure	
	//確認滿足編譯所需相依性，並產生 make 組態檔
	$ make	
	//編譯程式...，在 BeagleBone Black 上需要幾分鐘時間
	
	//如果想將執行檔安裝至系統目錄下，可再執行這行
	$ sudo make install
	
###使用 Seren

操作方法相當簡單，使用 `seren -h` 查看可用命令列參數，或直接執行 `seren` 進入互動操作模式，`seren` 執行後便會進入接聽待命模式，要撥打電話的一方只要鍵入 **`/c 對方IP位址`** 即可進行連線。

若系統未連接音效卡或預設的音效卡設備無法提供 seren 連接使用時，可能會出現如下訊息：

	[alsa] Cannot open audio device 'hw:0,0'
	
可利用下列方式排除問題:
	
列出可用的音效卡設備編號 **`hw:x,x`**

	$ cat /proc/asound/pcm
	
亦可使用 `$ cat /proc/asound/cards` 或是 `$ cat /proc/asound/devices` 查看

若系統中也裝有 `alsa-utils` 音效卡工具程式庫，可用 `aplay -l` 指令列出音效卡設備。

確認音效卡編號後，可在啟動 seren 時使用 `-d hw:x,x` 參數，指定實際之音效卡編號載入使用。