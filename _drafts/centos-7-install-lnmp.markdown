---
layout: post
title: "CentOS 7 安裝 LNMP server 環境"
---

安裝 Nginx web server

	$ yum update
	$ yum install epel-release	\\使用epel repository
	$ yum install nginx

nginx 的預設網站目錄在 /usr/share/nginx/html
啟動 nginx web server

	$ systemctl start nginx

設定開機啟動 nginx server

	$ systemctl enable nginx


安裝 MySQL 資料庫，因 MySQL 已被 Oracle 收購，為避免閉源風險，CentOS 7 已改用 MySQL 的社群維護版本 Mariadb，基本上指令與程式庫與 MySQL 完全相同。

	$ yum install mariadb-server mariadb
	$ systemctl start mariadb.service    \\先啟動 mysql 資料庫伺服器，初始root無密碼
    $ mysql_secure_installation          \\互動式的 mysql 起始安全性設定，只要遵照命令列提示，依序進行相關設定與 mysql root 密碼即可
	$ systemctl enable mariadb.service	\\設定開機執行 mariadb 服務


安裝 PHP , php-mysql 與搭配 Nginx 時必須的的 php-fpm

	$ yum install php php-fpm php-mysql

修正一下 php.ini 裡的設定，提高安全性

	$ vi /etc/php.ini
	找到下列這行 
	#cgi.fix_pathinfo = 1
	修改為
	cgi.fix_pathinfo = 0

接著設定 php-fpm 設定檔
	
	$ vi /etc/php-fpm.d/www.conf
	
可把 FastCGI 請求連接埠修改爲 unix socket

	預設為：
	listen = 127.0.0.1:9000
	欲使用 unix socket 方式可改為如下：
	listen = /var/run/php-fpm/php-fpm.sock

將下列兩行前方的 # 取消

	listen.owner = nobody
	listen.group = nobody
 
再將 user 與 group 皆設定為 CentOS 7 中 Nginx 套件的預設安裝值

	user = nginx
	group = nginx

啟動 php-fpm

	$ systemctl start php-fpm
	$ systemctl enable php-fpm



安裝管理 MySQL 的 phpMyAdmin

	$ yum install phpMyAdmin

phpMyAdmin 的預設目錄在 /usr/share/phpMyAdmin, 要讓 nginx 網站伺服器可以使用 phpMyAdmin，可在 nginx 網站根目錄加入 phpMyAdmin 的路徑連結：

	$cd /usr/share/nginx/html
	$ln -s /usr/share/phpMyAdmin




##各程式的起始設定##

Nginx


---

MySQL

	$systemctl start mariadb.service	\\先啟動 mysql 資料庫伺服器，初始root無密碼
	$mysql_secure_installation			\\互動式的 mysql 起始安全性設定，只要遵照命令列提示，依序進行相關設定與 mysql root 密碼即可


---

PHP 設定


---

phpMyAdmin 設定
設定組態檔位於 /etc/phpMyAdmin/config.inc.php

 
