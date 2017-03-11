---
layout: "post"
title: "CentOS 7 Core 安裝"
date: "2017-03-10 17:22"
---

## CentOS 7 Core ##

1. install CentOS 7 Core
2. Centos 7 Core 的網路工具程式改用 `ip` ，若仍想使用 `ifconfig` 等網路公用程式，必須安裝 `net-toos` 套件。

		# yum install net-tools (提供 ifconfig 功能)
    使用 `ip` 指令查看網卡設定：
		
		# ip addr show      //檢查網卡介面名稱，例如 enp5s0
	
	修改網卡設定檔：
    	
		# vi /etc/sysconfig/network-scripts/ifcfg-enp5s0
    	
	必要參數為 static ip address, gateway, dns

    	IPADDR=
    	NETMASK=
    	GATEWAY=
    	DNS1=
    	DNS2=
    
	重新啟動網路

    	# service network restart   //重新啟動網路
    
    (optional) 修改主機名稱
    
		# vi /etc/hostname
    	or 
   	 	# hostname "主機名稱"


3.  更新套件庫目錄與程式更新

		# yum update && yum upgrade

    	# yum install wget  //方便命令列下載

