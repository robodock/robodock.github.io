---
layout: post
title: "OAuth 2.0"
---

## OAuth 2.0 的運作方式
1. 先從 Google Developers Console 取得 OAuth 2.0 client credentials。
2. Client 應用程式向 Google Authorization Server 請求一個 access token，然後將 token 轉送給欲使用的 Google API。


## 在 Python 中使用 OAuth 2.0
1. 在 `Google Developers Console` 建立一個新專案
2. 在 `API 和驗證` 項目下，啟用 `Drive API`
3. 前往 `憑證`，選擇 `新增憑證`，新增 `服務帳戶`，下載憑證 json 檔
4. 安裝 python 模組 `oauth2client`：

		pip install --upgrade oauth2client

	依系統中未包含 Python OpenSSL 模組時還需安裝：

		pip install PyOpenSSL


範例：使用 Python gspread 模組存取 Google 試算表

先透過 pip 安裝 gspread 模組

	pip install gspread

程式碼：

	import json
	import gspread
	from oauth2client.client import SignedJwtAssertionCredentials

	json_key = json.load(open('gspread-april-2cd … ba4.json'))
	scope = ['https://spreadsheets.google.com/feeds']

	credentials = SignedJwtAssertionCredentials(json_key['client_email'], json_key['private_key'], scope)

	gc = gspread.authorize(credentials)

	wks = gc.open("Googld Spread Sheet").sheet1
