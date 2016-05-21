---
layout: post
title: "CentOS 7 安裝 LNMP server 環境"
date: 2016-05-21T23:28:45+08:00
---

**有別於常見的 LAMP (Linux-Apache-MySQL-PHP) 網站伺服器環境，今天要來改用 Nginx 取代 Apache 做為網頁伺服器，這樣的組合成為 LNMP (Linux-Nginx-MySQL-PHP)**

雖然標題寫著 CentOS 7，但在 Debian/Ubuntu 環境下改用對應的 apt-get 套件管理程式，應該也可順利安裝。

## Nginx ##

安裝 Nginx web server，需要用到 epel repository

	$ yum update
	$ yum install epel-release	\\使用epel repository
	$ yum install nginx

nginx 的預設網站目錄在 /usr/share/nginx/html，
啟動 nginx web server

	$ systemctl start nginx

設定開機啟動 nginx server 服務

	$ systemctl enable nginx

## MySQL (MariaDB) ##

安裝 MySQL 資料庫，因 MySQL 已被 Oracle 收購，為避免閉源風險，CentOS 7 已改用 MySQL 的社群維護版本 Mariadb，基本上指令與程式庫與 MySQL 完全相同。

	$ yum install mariadb-server mariadb
	$ systemctl start mariadb.service    \\先啟動 mysql 資料庫伺服器，注意初始root帳號無密碼
    $ mysql_secure_installation          \\互動式的 mysql 起始安全性設定，只要遵照命令列提示，依序進行相關設定與 mysql root 密碼即可
	$ systemctl enable mariadb.service	\\設定開機執行 mariadb 服務


## PHP (php-fpm)

安裝 PHP , php-mysql 與搭配 Nginx 時必須的的 php-fpm

	$ yum install php php-fpm php-mysql

修正一下 php.ini 裡的設定，提高安全性

	$ vi /etc/php.ini
	
	找到下列這行 
	#cgi.fix_pathinfo = 1
	註解拿掉，修改為
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
 
再將 user 與 group 皆設定為 CentOS 7 中 Nginx 套件安裝後的預設值

	user = nginx
	group = nginx

啟動 php-fpm

	$ systemctl start php-fpm
	$ systemctl enable php-fpm

## phpMyAdmin ##

安裝管理 MySQL 的 phpMyAdmin

	$ yum install phpMyAdmin

phpMyAdmin 的預設目錄在 /usr/share/phpMyAdmin, 要讓 nginx 網站伺服器可以使用 phpMyAdmin，可在 nginx 網站根目錄加入 phpMyAdmin 的路徑連結：

	$ cd /usr/share/nginx/html
	$ ln -s /usr/share/phpMyAdmin

另外 /var/lib/php 與 /var/lib/phpMyAdmin 目錄的預設使用者與群組為 apache.apache ，使用 Nginx 須將其使用者與群組設定為 nginx.nginx

---

##*Example*##

/etc/nginx/nginx.conf

```
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  localhost;
        root    /usr/share/nginx/html;
        index   index.html index.htm;

        location / {
                try_files $uri $uri/ =404;
        }

        error_page 404 /404.html;

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
                root /usr/share/nginx/html;
        }
    }
}
```

要加入虛擬主機，只要在 /etc/nginx/conf.d 目錄中加入主機設定的即可，例如

/etc/nginx/conf.d/robodock.conf

```
server {
    server_name www.robodock.net;		\\網站名稱
    root /home/robodock/wordpress;	\\網頁根目錄
    index index.html index.php index.htm;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # set expiration of assets to MAX for caching
    location ~* \.(ico|css|js|gif|jpe?g|png|ogg|ogv|svg|svgz|eot|otf|woff)(\?.+)?$ {
        expires max;
        log_not_found off;
    }

    server_tokens off;

    # framework rewrite
    location / {
        try_files $uri $uri/ /index.php;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_split_path_info ^(.+\.php)(.*)$;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```