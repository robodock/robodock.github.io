---
layout: post
title: "BeagleBone Black 使用 Connman 管理設定網路"
date: 2016-03-27T20:44:48+08:00
---

新的 Debian(armhf) for BeagleBone Black (簡稱BBB)改用 ConnMan 來作為網路管理程式，感覺比設定 wpa_supplicant.conf 組態檔更為簡易，用法如下：

**connmanctl**

connmanctl 為 ConnMan 的標準命令列用戶端程式，有兩種使用方式：

	- connmanctl [arguments] 的方式啟動。
	- 或直接執行 connmanctl，進入互動模式，在 connmanctl> 提示符號後輸入指令。

---
在 BBB 上通常搭配 USB 無線網卡，接上網卡後須先啟用：

	$sudo connmanctl enable wifi

接下來掃描可用無線網路存取點：

	$sudo connmanctl scan wifi
	
掃瞄完成後列出可用 AP:

	$connmanctl services
	Network1	wifi_dc85de828967_68756773616d_managed_none
	Network2	wifi_dc85de828967_38303944616e69656c73_managed_psk 
	Network3	wifi_dc85de828967_3257495245363836_managed_wep	
接下有兩種情況：
	
- 如果連接的是開放式網路(無密碼保護):

		$connmanctl connect wifi_dc85de828967_68756773616d_managed_none
	
- 如果連接的是有密碼保護的 AP，必須使用互動模式:

		$connmanctl
		connmanctl> scan wifi
		
		connmanctl> services
		Network1	wifi_dc85de828967_68756773616d_managed_none
		Network2	wifi_dc85de828967_38303944616e69656c73_managed_psk 
					wifi_74da38557d81_hidden_managed_psk
		
		connmanctl> connect wifi_dc85de828967_38303944616e69656c73_managed_psk
		connmanctl> agent on
		Agent registered
		
		connmanctl> connect wifi_dc85de828967_38303944616e69656c73_managed_psk
		Agent RequestInput wifi_74da38557d81_hidden_managed_psk
		Name = [ Type=string, Requirement=mandatory, Alternates=[ SSID ] ]
		SSID = [ Type=ssid, Requirement=alternate ]
		Passphrase = [ Type=psk, Requirement=mandatory ]
		Hidden SSID name?
		Passphrase?
		
		connmanctl> quit
	
最後可使用 `ifconfig`/`iwconfig` 檢查無線網路連線狀況。
		

	 
