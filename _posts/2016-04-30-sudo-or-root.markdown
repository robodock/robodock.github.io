---
layout: post
title: "sudo or root?"
date: 2016-04-30T18:16:07+08:00
---

取得 Root 特權的方法：

* 直接登入 root 帳號

* 遠端使用 ssh 登入 root 帳號

		$ssh root@IP_address
	
	遠端主機需執行 sshd ，同時允許 root 可透過 ssh 登入， 確定遠端主機中的 `/etc/ssh/sshd.config` 設定檔中有下列一行設定：
	
		PermitRootLogin Yes
		
* 使用 su 取得 root 特權(成為 root)，系統會要求輸入 **root** 密碼
	
		$su
		
* 使用 sudo 以 root 特權執行程式，不一樣的是系統要求輸入的是 **使用者** 的密碼

		$sudo command
	
	為何一般使用者透過 sudo 指令與自己的密碼，就可取得 root 特權執行程式，其實是透過 `/etc/sudoers` 檔案來進行控管。
		
	`/etc/sudoers`檔案內的設定可決定誰具有取得 root 特權的能力，因此檔案屬性預設為無法編輯，且只有 root 才能檢視，`/etc/sudoers`檔案屬性如下：
	
		-r--r----- 1 root root 800 Aug 19  2014 sudoers	
	要修改 `/etc/sudoers` 檔內容時，必須使用 **`visudo`** 指令來編輯 `/etc/sudoers` 檔，`visudo` 會啟動系統預設的文字編輯器如 `vi` 或 `nano` ，存檔時會自動檢查輸入內容是否符合 `/etc/sudoers` 語法，確保內容無誤。
	
	在 ubuntu 中可看到如下的 `/etc/sudoers` 檔內容：
	
		Defaults        env_reset
		Defaults        mail_badpass
		Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
		
		# User privilege specification
		root    ALL=(ALL:ALL) ALL

		# Members of the admin group may gain root privileges
		%admin ALL=(ALL) ALL

		# Allow members of group sudo to execute any command
		%sudo   ALL=(ALL:ALL) ALL
	
	
	
	
	
	再來看看設定檔內容怎麼寫：
		
		Defaults env_reset
		// 這行用來清除使用者環境變數，避免有害的變數帶入 sudo 環境。
		Defaults secure_path=
		// 這行則定義 sudo 環境下的執行檔搜尋路徑。
		
		root ALL=(ALL:ALL) ALL
		//root 為要套用規則的使用者名稱
		//第一個欄位 ALL 表示要套用到所有“主機”
		//第二個欄位 ALL 表示此使用者可像所有的 users 般執行 commands
		//第三個欄位 ALL 表示此使用者可像所有的 groups 般執行 commands
		//第四個欄位 ALL 表示此使用者可執行所有的 commands
		
		%admin ALL=(ALL) ALL
		使用者名稱前加上 % 則表示群組，在此群組內的使用者將可套用規則。
		依此方法，可對個別使用者進行對不同程式執行權限的詳細控管。
	
	查詢自己的 sudo 權限內容：
	
		$sudo -l
		

	