---
layout: "post"
title: "influxDB-key-concept"
date: "2016-10-15 19:59"
---

# influxDB 基本觀念 #

以下列資料表為例：


name: `Measurement`

time: `Timestamp`

butterflies: `Field Key`

honeybees: `Field key`

location: `Tag key`

scientist: `Tag key`

name: `census`

|time | butterflies | honeybees | location | scientist|
|--- | --- | --- | --- | --- |
|2015-08-18T00:00:00Z | 12 | 23 | 1 | langstroth |
|2015-08-18T00:00:00Z |  1 | 30 | 1 | perpetua |
|2015-08-18T00:06:00Z | 11 | 28 | 1 | langstroth |
|2015-08-18T00:06:00Z |  3 | 28 | 1 | perpetua |
|2015-08-18T05:54:00Z |  2 | 11 | 2 | langstroth |
|2015-08-18T06:00:00Z |  1 | 10 | 2 | langstroth |
|2015-08-18T06:06:00Z |  8 | 23 | 2 | perpetua |
|2015-08-18T06:12:00Z |  7 | 22 | 2 | perpetua |

詳細說明如下：

- **Timestamp** : influxDB 為一時間序列資料庫，時間戳記皆以 UTC 時間記錄，格式標準為 RFC3339

- **Field** : 包含 `Field key` 與 `Field value`，field key 格式為 `string`，field value 格式預設為 `float`，但可為 `string`,`integers`, `booleans`。在 influxDB 資料結構中, 每筆資料中一定要有 `Field` 欄位. `Field` 欄位並未被索引，以 Field value 為條件做查詢時必須掃描所有符合值，所以常用之查詢應使用 `Tag` 來標識。

- **Tag** : 包含 `Tag Key` 與 `Tag value`。兩者皆為 `string` 格式。`Tag` 為非必要項目，但因 `Tag` 欄位會被索引處理，使用 `Tag` 欄位來做查詢可加快速度。

- **measurement**:
