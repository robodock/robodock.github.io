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

虛擬主機(單一主機多站台)設定：

	/etc/nginx/conf.d/site1

	server {
        listen 80;
 
        root /usr/share/nginx/html/site1;
        index index.html index.htm;
 
        server_name site1.test.com;
 
        location / {
                try_files $uri $uri/ =404;
        }
	}


	/etc/nginx/conf.d/site2

	server {
        listen 80;
 
        root /usr/share/nginx/html/site2;
        index index.html index.htm;
 
        server_name site2.test.com;
 
        location / {
                try_files $uri $uri/ =404;
        }
	}

記得 DNS server 記錄要設定

---

Nginx 要使用 PHP 需安裝 php5-fpm 套件

---

反向代理設定

    upstream fl_web {
    server test.com:80; # 網站名稱自行設定
    }
    server {
        listen 80;
        server_name fl;
        access_log /var/log/nginx/fl_access_log;
        location / {
            proxy_pass_header Server;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Scheme $scheme;
            proxy_pass http://192.168.1.11; #這裡輸入後端其他網頁伺服器的ip
        }
    }

---

## 網頁基本帳號密碼認證(http basic authentication) ##

    location / {
      auth_basic           "closed site";
      auth_basic_user_file conf/htpasswd;
    }

`auth_basic_user_file` 檔案內容

    # 這是註解
    name1:password1
    name2:password2:這是註解
    name3:password3

密碼要先經過編碼，可使用 htpasswd 或 openssl

    # openssl passwd

整個網站受登入保護，但特定目錄 `public` 除外：

    server {
      auth_basic "closed website";
      auth_basic_user_file conf/htpasswd;
    
      location /public/ {
          auth_basic off;
      }
    }

---

## 允許與禁止連線 IP 位址 ##

    location / {
      deny  192.168.1.2;
      allow 192.168.1.1/24;
      allow 127.0.0.1;
      deny  all;
    }

---

## 連線數量控制 ##

設定一個大小 10MB，名為 `addr` 的 zone    

    limit_conn_zone $binary_remote_addr zone=addr:10m;

指定一個 IP 同時間允許最大連線數：

    location /download/ {
      limit_conn addr 1;
    }

整台伺服器連線數上限

    http {
      limit_conn_zone $server_name zone=server:10m;
    
      server {
        limit_conn server 1000;
      }
    }

## 連線頻率控制 ##

定義 zone 的名稱與大小，r/s 每秒連線次數或 r/m 每分鐘連線次數。

    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

需要限制連線頻率的地方，加上 `limit_req` 設定：

    location /search/ {
      limit_req zone=one burst=5;
    }

## 下載速度控制 ##

限制每個連線的頻寬

    location /download/ {
      limit_rate 50k;
    }

配合連線數量限制

    location /download/ {
      limit_conn addr 1;
      limit_rate 50k;
    }

先下載一部分資料後，再進行頻寬限制：

    limit_rate_after 500k;
    limit_rate 20k;

組合範例

    http {
      limit_conn_zone $binary_remote_address zone=addr:10m
    
      server {
        root /www/data;
        limit_conn addr 5;
    
        location / {
        }
    
        location /download/ {
          limit_conn addr 1;
          limit_rate_after 1m;
          limit_rate 50k;
        }
      }
    }