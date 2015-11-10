---
layout: post
title: "安裝 Phant Data Log Service"
date: 2015-10-05T14:13:31+08:00
---

## 安裝 Phant 服務程式

**Phant** 是一個數據資料儲存管理服務的開源軟體，由開源硬體廠商 **SparkFun** 提供，簡潔易用，相當適合業餘個人玩家建置自己的數據記錄服務。程式以 **node.js** 開發，所以必須先在安裝主機上確認是否已有 **node.js** 開發環境。

**node.js** 是以 javascript 為基礎的網路服務架構，在許多的 Linux 預設發行版本中已內建支援，如果是 CentOS 7，則須先啟用 **EPEL** repository 方可用 yum 進行安裝：

	[root@centos7 ~]# yum install epel-release
	[root@centos7 ~]# yum install nodejs

確認 nodejs 已成功安裝，並把 node.js 套件管理程式 **npm** 也一併安裝起來：

	[root@centos7 ~]# node --version
	v0.10.36
	[root@centos7 ~]# yum install npm

**node.js** 環境建置完成後，便可使用 **npm** 套件管理程式來安裝 **Phant**

	root@beaglebone:~# npm install -g phant


##啟動 Phant
啟動 Phant 相當簡單，直接執行 `phant` 就可以了:

	root@beaglebone:~# phant
	phant http server running on port 8080
	phant telnet server running on port 8081

預設的網頁管理頁面連接埠為 `8080`，文字界面 Telnet server 連接埠則為 `8081` 。
只要透過瀏覽器連至 `http://"電腦IP":8080/` 即可輕鬆管理。

另外，如果你用的是 CentOS 系列，或許您會想將 **SELinux** 設定為 **disable**，以方便存取網路服務。另外 CentOS 7 採用了新的 **firewalld** 防火牆管理服務，也必須設定開啟對應的服務。

新增 **firewalld** 開放項目，在 `/etc/firewalld/services` 目錄中建立一名為 `Phant.xml` 的防火牆服務設定檔

	/etc/firewalld/services/Phant.xml

檔案內容如下：

	<?xml version="1.0" encoding="utf-8"?>
	<service>
	  <short>Phant</short>
	  <description>Phant Log service</description>
	  <port protocol="tcp" port="8080"/>
	  <port protocol="tcp" port="8081"/>
	</service>

修改 `/etc/firewalld/zones` 中相對應的 zone file，我這邊的是 `public.xml`

	/etc/firewalld/zones/public.xml

內容加入剛新增的 service 項目

	<?xml version="1.0" encoding="utf-8"?>
	<zone>
	  <short>Public</short>
	  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
	  <service name="dhcpv6-client"/>
	  <service name="ssh"/>
	  <service name="Phant"/>    \\<-- 新增這行
	</zone>

重新載入防火牆規則

	firewall-cmd --reload

查看防火牆開放服務清單

	firewall-cmd --list-services


現在的 Linux 發行版本都改用 **systemd** 來作為系統服務管理，要將 Phant 作為系統服務在 Server 開機時自動執行，先在 `/etc/systemd/system` 中建立一個名為 `phant.service` 的檔案：

	/etc/systemd/system/phant.service

檔案內容如下：

	[Unit]
	Description=phant service
	After=network.target

	[Service]
	Type=simple
	WorkingDirectory=/home/phant
	ExecStart=/usr/bin/phant &

	[Install]
	WantedBy=multi-user.target

啟動 phant 服務

	systemctl start phant

結束 phant 服務

	systemctl stop phant

將 phant 服務加入開機啟動項目

	systemctl enable phant

取消 phant 服務開機啟動

	systemctl disable phant




## 在 Phant 上建立資料流 Stream
每個記錄資料稱為資料流(Stream)，在 Phant 上可對不同的記錄項目建立不同的 Stream。

透過網頁管理頁面建立 Stream 相當簡單，選取 `Create Stream` , 依照畫面說明進行即可。

底下僅說明使用指令列模式

	$ telnet beaglebone.local 8081
 	Trying 10.0.1.10...
 	Connected to beaglebone.local.
 	Escape character is '^]'.
 	           .-.._
 	     __  /`     '.
 	  .-'  `/   (   a \
 	 /      (    \,_   \
 	/|       '---` |\ =|
 	` \    /__.-/  /  | |
 	  |  / / \ \  \   \_\  jgs
 	  |__|_|  |_|__\
 	 welcome to phant.
 	
 	Type 'help' for a list of available commands
 	
 	phant>

出現 `phant>` 提示符號表示成功進入管理介面, 輸入 `create` 建立 Stream 資料流。

	phant> create
	Enter a title> Test
	Enter a description> Testing BeagleBone Black.
	Enter fields (comma separated)> test
	Enter tags (comma separated)> test
	
	Stream created!
	PUBLIC KEY: aAYVpdNaOeu6rQ80Ogeau2vxDKq
	PRIVATE KEY:  PW4OPY5B6Ztjd5wD6zOXuY4BD2L
	DELETE KEY:  lAEwmPboWZuBqa10LQ9wcyz9qn8
 	
	If you need help getting started, visit http://phant.io/docs.
	phant> quit
	Connection closed by foreign host.

Stream 資料流建立成功後，會產生三個 **KEY**，必須將這三個 KEY 妥善保存，往後存取 Stream 資料流時必須用到。

若是網頁介面，則會提供一個包含這三個 KEY 的 json 檔案供下載保存。

## 將記錄資料送至 Phant server

要將記錄資料送至 Phant Server 上，則是以網頁請求的方式：

	http://beaglebone.local:8080/input/PUBLIC_KEY?private_key=PRIVATE_KEY&test=testvalue

記得在每筆上傳的 http 請求中都必須包含 `PUBLIC_KEY` 與 `PRIVATE_KEY`。
上傳成功後會看到 `1 success` 的回應訊息。

## 從 Phant 取得資料

**Example** CSV output

	http://beaglebone.local:8080/output/PUBLIC_KEY.csv

**Example** JSON output

	http://beaglebone.local:8080/output/PUBLIC_KEY.json 

