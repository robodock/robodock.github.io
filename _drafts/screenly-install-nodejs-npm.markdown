---
layout: post
title: "Screenly Install Nodejs+npm"
---

目前的 Screenly 使用 Raspbian (基於Debian 7 Wheezy)版本，系統預設並未安裝 node.js，若直接 apt-get 從預設 repository 安裝 node.js，則版本過於老舊。


###手動安裝新的 node.js 版本###
直接下載最新的官方的 arm 架構 binary 檔案，
注意 Raspberry Pi Model A, B, B+ 為 **armv6** 架構，而 Raspberry Pi 2 則為 **armv7** 架構，須選用相對應的 binary 檔案。

	wget https://nodejs.org/dist/v4.2.2/node-v4.2.2-linux-armv6l.tar.gz
	tar -xvf node-v4.2.2-linux-armv6l.tar.gz
	cd node-v4.2.2-linux-armv6l

	sudo cp -R * /usr/local/

新版的 nodejs 已包含 npm 套件管理程式。



###安裝 http-server 套件###

	sudo npm install http-server -g





  