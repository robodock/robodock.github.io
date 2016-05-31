---
layout: post
title: "OAuth 2.0"
date: 2016-05-21T23:15:46+08:00
---
## OAuth 開放授權
透過一種 Token 機制，允許使用者讓第三方的應用程式存取該使用者在某一服務網站上儲存的資源(檔案、照片、影片等等)，而不用提供使用者名稱與密碼給第三方應用程式。

## Google OAuth 2.0 的運作方式
- 先從 Google Developers Console 取得 OAuth 2.0 client credentials。
-  Client 應用程式向 Google Authorization Server 請求一個 access token，然後將 token 轉送給欲使用的 Google API。

1. 在 `Google Developers Console` 建立一個新專案
2. 在 `API 和驗證` 項目下，啟用 `Drive API`
3. 前往 `憑證`，選擇 `新增憑證`，新增 `服務帳戶`，下載憑證 json 檔

## 在 Python 中使用 OAuth 2.0
1. 安裝 python 模組 `oauth2client`：

		pip install --upgrade oauth2client

	依系統中未包含 Python OpenSSL 模組時還需安裝：

		pip install PyOpenSSL
	
	在 Raspberry Pi 上還需要安裝 libciff-dev 套件
	
		sudo apt-get install libciff-dev

### 範例：使用 Python gspread 模組存取 Google 試算表

先透過 pip 安裝 gspread 模組

	pip install gspread

程式碼：

	import json
	import gspread
	from oauth2client.client import SignedJwtAssertionCredentials

	json_key = json.load(open('My Project-xxxxxxxxxxxx.json'))
	scope = ['https://spreadsheets.google.com/feeds']

	credentials = SignedJwtAssertionCredentials(json_key['client_email'], json_key['private_key'], scope)

	gc = gspread.authorize(credentials)

	wks = gc.open("Googld Spread Sheet").sheet1
