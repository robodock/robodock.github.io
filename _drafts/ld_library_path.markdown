---
layout: post
title: "Linux 動態連結程式庫搜尋路徑"
date: 2017-02-26 20:11
---

## LD_LIBRARY_PATH ##

Linux 系統中使用 `LD_LIBRARY_PATH` 環境變數來指定動態連結程式庫搜尋路徑

在目前登入 Shell 中快速加入 `LD_LIBRARY_PATH` 環境變數，例如加入 `/usr/local/lib` 到程式庫搜尋路徑中：

    $ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib

修改完記得執行 `sudo ldconfig`，確定更新 `/etc/ld.so.cache`

---

## 系統預設搜尋路徑設定檔 ##

系統的預設路徑設定則在 `/etc/ld.so.conf` 與 `/etc/ld.so.conf.d` 目錄下的 .conf 檔中。

---

## 查看程式使用的連結程式庫 ##

可用 `ldd` 指令查看程式所使用的程式庫有哪些? 例如：

    # ldd /bin/vi
        linux-vdso.so.1 =>  (0x00007fff913fe000)
        libselinux.so.1 => /lib64/libselinux.so.1 (0x00007f3565896000)
        libtinfo.so.5 => /lib64/libtinfo.so.5 (0x00007f356566c000)
        libacl.so.1 => /lib64/libacl.so.1 (0x00007f3565462000)
        libc.so.6 => /lib64/libc.so.6 (0x00007f35650a1000)
        libpcre.so.1 => /lib64/libpcre.so.1 (0x00007f3564e40000)
        libdl.so.2 => /lib64/libdl.so.2 (0x00007f3564c3b000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f3565ac4000)
        libattr.so.1 => /lib64/libattr.so.1 (0x00007f3564a36000)
        libpthread.so.0 => /lib64/libpthread.so.0 (0x00007f356481a000)

