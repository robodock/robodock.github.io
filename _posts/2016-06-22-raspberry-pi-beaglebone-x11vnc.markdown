---
layout: post
title: "Raspberry Pi (Beaglebone) 使用 VNC 遠端桌面"
date: 2016-06-22T08:26:27+08:00
---

## Raspberry Pi / BeagleBone 安裝 VNC

在 Windows 系統上安裝 VNC server，可讓遠端使用者連線到目前電腦操作者的桌面，進行遠端操作，然而在 Linux 世界中，圖形桌面系統是多人多工的，如果你在套件庫中尋找 `vncserver` 套件來安裝，結果可能與您預期的不太一樣。建立遠端連線後，得到卻是一個全新的可登入桌面環境，而不是操作中的螢幕畫面。

那有沒有辦法連線至操作中的 Linux 桌面環境螢幕畫面呢? 這就要靠 **`x11VNC`** 套件來達成了。

## x11vnc

安裝方式(以 Raspbian/jessie 系統為例):

	$ sudo apt-get install x11vnc

如果需要建立 vnc 連線密碼，可使用 `storepasswd` 參數，依照指示輸入密碼:

	$ x11vnc -storepasswd

會產生一個預設路徑存放於 `.vnc/passwd` 的加密文字檔。

簡單執行 `x11vnc` 即可啟動，常用設定如下：

	$ x11vnc -usepw  	//使用 .vnc/passwd 作為連線密碼
	
	$ x11vnc -nopw       //不要使用密碼
	
	$ x11vnc -bg         //在背景執行，但當遠端連線結束後亦會結束服務。
	$ x11vnc -bg -forever //在背景執行，但會持續執行等候連線。
	
	$ x11vnc -display :0    //指定要連線至 :0 螢幕(如果你只有一個 x-windows 顯示器，:0 就是你正在用的螢幕畫面)

可執行 `x11vnc --help` 可查看所有可用設定參數。

通常我們會想要系統啟動後便自動執行  vnc  服務，首先設定好開機自動登入圖形介面，然後編輯登入使用者家目錄下的  `.config/lxession/LXDE-pi/autostart`，新增一行：

	@x11vnc -bg -nevershared -forever -ultrafilexfer -nopw -display :0

其中的 `-ultrafilexfer` 參數用來支援 ultraVNC viewer 的檔案傳輸功能，如果你用的是 tightVNC，則改為 `-tightfilexfer`。

這樣子在啟動使用者桌面環境(LXDE)時，便會自動執行 x11vnc。
