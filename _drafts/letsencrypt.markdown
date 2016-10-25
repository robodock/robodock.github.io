---
layout: "post"
title: "Let's Encrypt 免費網站憑證"
date: "2016-10-24 14:08"
---

# 使用 Certbot ACME client 自動化流程 #

Let's Encrypt 建議使用 EFF 的 Certbot ACME client 進行自動化流程，
Certbot 網頁 [(https://certbot.eff.org)](https://certbot.eff.org) 提供各種不同網頁伺服器與作業系統的安裝說明。

## CentOS/RHEL 7 ##

Certbot 已經成為 EPEL 的套件之一，安裝 EPEL 套件庫後即可安裝。

	$ sudo yum install epel-release

### 使用 Nginx 伺服器 ###

- 直接安裝 certbot

		$ sudo yum install certbot

- 使用 certbot 來取得所需憑證

		$ certbot certonly

- 如果已有執行中的 Web Server，可利用 "webroot" plugin，在網頁目錄中做設定

		$ certbot certonly --webroot -w /var/www/example -d example.com -d www.example.com -w /var/www/thing -d thing.is -d m.thing.is

- 如果要取得 "standalone" webserver 的憑證，須先停止 server daemon

		$ certbot certonly --standalone -d example.com -d www.example.com


### 使用 Apache 伺服器 ###

- 安裝 python-certbot-apache

		$ yum install python-certbot-apache

- 使用 Apache Plugin 進行自動設定

		$ certbot --apache

- 或僅要取得憑證

		$ certbot --apache certonly


### 自動更新 ###

- Let's Encrypt 憑證有效期僅為 90 天，建議設定自動更新

		$ certbot renew --dry-run
		測試成功後，可利用 cron 或 systemd 排定定期工作(官網建議一天檢查兩次)
		$ certbot renew --quiet

---

## CentOS/RHEL 6 ##

首先安裝 EPEL 套件庫

	$ sudo yum install epel-release

CentOS 6 尚未有 Certbot 套件，須手動安裝

	$ wget https://dl.eff.org/certbot-auto
	$ chmod a+x certbot-auto


使用 certbot 來取得所需憑證

	$ ./certbot-auto

### 使用 Nginx 伺服器 ###

- 僅取得憑證

		$ ./path/to/certbot-auto certonly

- 使用 "webroot" plugin, 自動對網站目錄進行設定

		$ $ ./path/to/certbot-auto certonly --webroot -w /var/www/example -d example.com -d www.example.com -w /var/www/thing -d thing.is -d m.thing.is


### 使用 Apache 伺服器 ###

- 使用 Apache Plugin 進行自動設定

		$ ./path/to/certbot-auto --apache

- 或僅要取得憑證

		$ ./path/to/certbot-auto --apache certonly

### 自動更新 ###

- Let's Encrypt 憑證有效期僅為 90 天，建議設定自動更新

		$ ./path/to/certbot-auto renew --dry-run
		測試成功後，可利用 cron 或 systemd 排定定期工作(官網建議一天檢查兩次)
		$ ./path/to/certbot-auto renew --quiet

---

## 運作原理 ##

在網站伺服器上執行一個憑證管理代理程式，使用 ACME protocol 來向 Let's Encrypt CA 自動取得瀏覽器信任憑證。

分為兩個階段：

1. 代理程式向 CA 證明網頁伺服器具有 domain 控制權。
2. 代理程式可請求、更新、撤銷管理 domain 的憑證。


- 階段 1 : **網域驗證**

	假設網站域名為 `example.com`
	當代理程式第一次聯繫 Let's Encrypt CA 時，會產生一組新的金鑰對供後續使用。
	流程開始，代理程式詢問 CA，需要做些什麼來證明網站對 `example.com` 網域有控制權。
	CA 將會尋找域名並要求一或數個質問挑戰，可能為：

	- 提供 `example.com` 下的 DNS record
	- 提供 Well-known URI `https://example.com/` 下的 HTTP resource

	伴隨著質問挑戰，CA 也會提供一次性隨機數(nonce)要求代理程式以私鑰簽署，以證明代理程式對金鑰對有控制權。

	代理程式會回應挑戰，並在 `https://example.com` 網站特定路徑下建立一個檔案，同時對 CA 提供的一次性隨機數(nonce)以私鑰簽署。完成這些步驟後，便通知 CA 已準備好檢驗。

	接下來便是 CA 的工作，CA 會檢驗一次性隨機數(nonce)的簽署，並嘗試從網站下載代理程式準備的檔案，確定內容正確。
	一切檢查成功後，此 key pair 便會做為 `example.com` 的 `已驗證金鑰對`。

- 階段 2 : **發證與撤銷**

	有了 `已驗證金鑰對` 後，接下來的請求、更新、撤銷憑證動作就簡單了，只要送出使用金鑰對簽署的憑證管理請求即可。

	請求憑證時，代理程式會建構一個 `PKCS#10` 的 `CSR`(Certificate Signing Request)，請求 CA 以對應公鑰發出 `example.com` 的憑證。

	CSR 中包含以私鑰對 CSR 中公鑰的簽署，同時代理程式也會用 `example.com` 的驗證金鑰對整個 CSR 進行簽署。

	當 Let's Encrypt CA 收到請求後，會檢查兩者，如果一切正常，將以 Public key 發出 `example.com` 的憑證回傳給代理程式。

	撤銷憑證時，agent 以 `example.com` 驗證金鑰簽發撤銷請求。如果 CA 檢查正確，會將撤銷資訊發布至 `撤銷 Channel 中` (CRLs, OCSP)，各家的瀏覽器得以更新採用。
