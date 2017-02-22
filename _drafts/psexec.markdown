---
layout: "post"
title: "psexec 用法"
date: "2016-12-08 20:40"
---

## Usage: ##
psexec [\\\\computer[,computer2[,...] | @file]] [-u user] [-p passwd] [-n s] [-r servicename] [-h] [-l] [-s] [-e] [-x] [-i] [session]] [-c[-f|-v]][-w directory][-d][-<priority>][-a n,n,...] cmd [arguments]

`-a` 指定程序使用哪一顆 CPU 來執行，例如 "-a 2,4" 表示使用 CPU 2 與 CPU 4.

`-c` 將程式複製到遠端電腦執行.若不指定則程式必須存在於遠端電腦系統路徑上.

`-d` 不等候程式執行完成(非互動式)

`-e` 不要載入指定帳號 profile.

`-f` 強制複製程式到遠端電腦(即使已存在)

`-i` 在遠端桌面上以互動方式執行，若不指定程式則執行命令列視窗.

`-h` 在 Vista 之後版本，以較高權限執行(如果有的話)

`-l` 去除管理者群組權限，改以限制使用者方式執行.

`-n` 指定連線逾時時間

`-p` 指定使用者帳號密碼，未指定時會顯示提示要求輸入

`-r` 指定要建立或溝通的遠端服務

`-s` 以系統帳號執行程式

`-u` 指定使用者帳號名稱

`-v` 只有在程式較新或版次較高時才複製檔案到遠端電腦

`-W` 設定程式的工作目錄(在遠端電腦上)

`-x` 顯示 Winlogon secure desktop UI(僅限本地端)

`-priority` 指定 -low, -belownormal, -abovenormal, -high, -realtime 優先順序，或使用 -background 在 Vista 以後系統以 low memory 與 I/O priority 方式執行.

`computer` 遠端電腦名稱，未指定時表示在本地端執行

`@file` 在 file 檔案中列出的電腦上執行程式.

`cmd` 要執行的程式名稱.

`arguments` 程式參數(注意若有路徑名稱，需使用目標系統上的絕對路徑)

`-accepteula` 避免 license 對話框出現

**程式名稱或檔案路徑中有空白字元時，需用雙引號框起來。**