---
layout: "post"
title: "CentOS 安裝 Nginx Server"
date: "2016-10-15 19:59"
---

## CentOS 7 ##

Nginx 已包含於 CentOS 7 EPEL 套件庫中，安裝 EPEL 套件庫後即可安裝。

	$ sudo yum install epel-release
	$ sudo yum install nginx

啟動 nginx

	$ systemctl start nginx

相關防火牆設定

	$ sudo firewall-cmd --permanent --zone=public --add-service=http
	$ sudo firewall-cmd --permanent --zone=public --add-service=https
	$ sudo firewall-cmd --reload

開機自動啟動 nginx

	$ sudo systemctl enable nginx

預設網站根目錄

	/usr/share/nginx/html

預設組態設定檔位置

	/etc/nginx/conf.d/default.conf

全域組態檔

	/etc/nginx/nginx.conf
	可設定 daemon 執行 user，worker processes 數量等等

Server Block (Virtual Hosts) 組態檔位置

	/etc/nginx/conf.d
	此目錄下附檔名為 .conf 的組態檔皆會載入

---

## CentOS 6 ##

Nginx 已包含於 CentOS 6 EPEL 套件庫中，安裝 EPEL 套件庫後即可安裝。

	$ sudo yum install epel-release
	$ sudo yum install nginx

啟動 nginx

	$ /etc/init.d/nginx start
	or
	$ service nginx start
