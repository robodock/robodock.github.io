---
layout: "post"
title: "LD_LIBRARY_PATH 程式庫路徑"
date: "2017-02-26 20:11"
---

## LD_LIBRARY_PATH ##

快速加入 LD_LIBRARY_PATH 路徑

    $export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib

## 系統預設檔位置 ##

/etc/ld.so.conf 與 /etc/ld.so.conf.d 目錄下的 .conf 檔

修改完記得執行 sudo ldconfig，確定更新 /etc/ld.so.cache
