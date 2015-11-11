---
layout: post
title: "快速建立網頁伺服器"
date: 2015-11-10T15:01:37+08:00
---

# 快速建立網頁伺服器 #

在許多的網頁程式開發過程中，經常需要快速建立臨時性的 WebServer，又不想動用到 Apache 或 Ngix，底下有兩種分別利用 **Python** 與 **Node.js** 的快速建立 WebServer 方法：

---

使用 **Python**
在網頁伺服器的目錄下，執行 `python -m SimpleHTTPServer 8000 &` ，可建立一個 script 檔如下來啟動 WebServer

	#!/bin/bash
	cd /home/webserver
	/usr/bin/python -m SimpleHTTPServer 8000 &


---
使用 **Node.js**

可透過 node.js 的 **http-server** 套件輕鬆達成，

若系統中已有 npm 管理程式:
	
	npm install http-server -g

若系統中無 npm :

	curl https://npmjs.org/install.sh | sh

建立一個 script 來啟動 **http-server**


	#!/bin/bash
	http-server /home/webserver -p 8000 -i False -s &


