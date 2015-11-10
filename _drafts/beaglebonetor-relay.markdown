---
layout: post
title: "使用 BeagleBone 打造 Tor Bridge Relay"
---

## 使用 BeagleBone Black 打造 Tor Bridge Relay ##

1. 安裝標準 Debian 系統 Image，因為要作為網路服務器使用，只要安裝 Console 版本即可。
	我這裡裝的是可安裝至 onboard 2GB eMMC 中的 debian wheezy。 

		BBB-eMMC-flasher-debian-7.8-console-armhf-2015-07-28-2gb.img


2. 安裝 Tor package
	
		apt-get update; apt-get install tor

3. 安裝 python 環境

		apt-get install python2.7 python-pip python-dev build-essential

4. 安裝 obfsproxy

		pip install obfsproxy

5. 編輯 /etc/tor/torrc

		SocksPort 0
		ORPort auto
		BridgeRelay 1
		Exitpolicy reject *:*

		## CHANGEME_1 -> provide a nickname for your bridge, can be anything you like
		#Nickname CHANGEME_1
		## CHANGEME_2 -> provide some email address so we can contact you if there's a problem
		#ContactInfo CHANGEME_2

		ServerTransportPlugin obfs3 exec /usr/local/bin/obfsproxy managed
		
6. 修正系統時間

	若系統中沒有 ntpdate ，請先安裝
		
		apt-get install ntpdate 
		
	校時
	
		ntpdate -b -s -u pool.ntp.org
		hwclock -w
	
	我這邊是將校時的動作，寫至 `rc.local` 中
			

7. 啟動 tor

		service tor restart
		
	檢查 /var/log/tor/log 中是否有相關紀錄
	
8. 開啟防火牆連接埠轉送

	ORPort 與 obfsproxy 兩個連接埠都須開啟允許外部連接轉送至 BeagleBone 上。
	
	obfsproxy 使用埠號可至 /var/log/tor/log 中查詢。
	
9. 可安裝 arm 套件觀看 Tor 系統使用狀況

		apt-get install tor-arm
	
10. 若需 HTTP 代理伺服器服務可安裝 polipo 

		apt-get install polipo	