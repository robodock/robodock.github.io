---
layout: "post"
title: "influxDB 使用 HTTP API 寫入資料"
date: "2016-10-15 19:57"
---

# 使用 HTTP API 寫入資料到 influxDB #

## 使用 HTTP API 建立資料庫 ##
透過傳送一個 HTTP `POST` 請求至 `/query` 網頁端點並設定參數 `q=CREATE DATABASE <new_database_name>`，可建立資料庫：

	curl -i -XPOST http://localhost:8086/query --data-urlencode "q=CREATE DATABASE mydb"

## 使用 HTTP API 寫入資料 ##
透過傳送一個 HTTP `POST` 請求至 `/write` 網頁端點並指定相關參數，可將資料寫入 influxDB 資料庫。

	curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server01,region=us-west value=0.64 1434055562000000000'

上述命令會將一筆資料寫入名為 `mydb` (透過指定 `db=mydb`) 的資料庫，資料如下：

- measurement : `cpu_load_short`
- tag keys : `host=server01,region=us-west`
- fidle key : `value=0.64`
- timestamp : `1434055562000000000`


## 資料格式 ##

上述的資料型式即為典型的 influxDB 資料格式，influxDB 稱這為 **`Line Protocol`** ，說明如下：

- measurement : 記錄項目
- tags : 選項欄位，用來標記不同資料來源或地點，有利於查詢效率。tag keys 與 tag values 皆為 **字串** 格式。
- fields : 資料數值欄位。field keys 為 **字串** 格式，而 field value **預設格式** 則為 **float(浮點小數)**。
- timestamp : epoch 格式，但以 nanosecond 單位記錄(所以總位數為19位數)。在 influxDB 中，所有時間皆以 UTC 記錄。


### 寫入多點資料 ###
多筆資料只要以 **new line `\n`** 字元分隔，即可在一個 HTTP 請求中，寫入多筆資料到資料庫中，效率也會比分開多次 HTTP 請求來的好。

> 註：僅能使用 new line 字元 `\n`，不可使用 new line + carriage return `\n\r` 作為分隔。

例如：

	curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary 'cpu_load_short,host=server02 value=0.67
	cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
	cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257'

### 讀取檔案來寫入 ###
透過傳遞 `@filename` 參數給 `curl`，可將整個檔案資料寫入資料庫，當然檔案內容格式必須遵循前述的 `Line Protocol`，注意換行符號僅使用 **`\n`**，而非 windows 環境下使用的 **`\n\r`** 換行字元，在 windows 環境中編輯資料檔案時須特別注意，建議使用 `notepad++` 之類的文書軟體來處理資料檔案。

例如：名為 `cpu_data.txt` 的檔案中資料如下：

	cpu_load_short,host=server02 value=0.67
	cpu_load_short,host=server02,region=us-west value=0.55 1422568543702900257
	cpu_load_short,direction=in,host=server01,region=us-west value=2.0 1422568543702900257

將 `cpu_data.txt` 檔案寫入 `mydb` 資料庫 :

	curl -i -XPOST 'http://localhost:8086/write?db=mydb' --data-binary @cpu_data.txt


> 註：建議單一個檔案的資料少於 5000 點，以避免 HTTP request times out 相關問題。

## Schemaless Design ##
influxDB 是一種 Schemaless 資料庫，資料庫不需要固定樣式的資料表，可隨時加入額外的 measuremen、tag、field。
但對於已使用中的欄位，則須維持相通的資料格式，不可變更。例如原本使用 **integers** 的欄位，則無法改寫入 **string** 格式資料。

## 關於 HTTP 回應訊息 ##
-  2xx : `HTTP 204 No Content` 與 `HTTP 200 OK` 皆代表處理成功。
-  4xx : influxDB 無法處理請求
-  5xx : 系統超載或受損
