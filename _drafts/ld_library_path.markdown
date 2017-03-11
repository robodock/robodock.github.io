---
layout: "post"
title: "LD_LIBRARY_PATH 動態連結程式庫搜尋路徑"
date: "2017-02-26 20:11"
---

## LD_LIBRARY_PATH ##

Linux 系統中可用 LD_LIBRARY_PATH 環境變數來指定動態連結程式庫搜尋路徑

在目前登入Shell中快速加入 `LD_LIBRARY_PATH` 環境變數：

    $ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib

## 系統預設搜尋路徑設定檔 ##

預設路徑設定於 `/etc/ld.so.conf` 與 `/etc/ld.so.conf.d` 目錄下的 .conf 檔中。

修改完記得執行 `sudo ldconfig`，確定更新 `/etc/ld.so.cache`
