---
layout: post
title: "解決 Screenly System Info Error 問題"
date: 2015-11-13T10:14:54+08:00
---

# 解決 Screenly System Info error 問題 #

Screenly 為一套為 Raspberry Pi 量身打造的電子看板軟體，可播放網頁，圖片，影片(限h.264格式)，Screenly Pro 版提供雲端商業服務，而 Screenly OSE 則為開放程式碼的社群版本。

相較於其他電子看板軟體，Screenly 的操作簡單直覺，每個播放項目稱為 **ASSET** , 可為每個 ASSET 設定預定播放時間，播放內容(網頁、圖片、影片)，與每個項目的播放長度。

![screenly](https://www.screenlyapp.com/img/screenly-ose-8bcf3db7.png) 

Screenly 的 Asset Name 是可以輸入中文的，可以清楚標示播放內容。但有個問題是，如果使用中文的 Asset Name, 當選擇管理頁面右上方的 `System Info`功能，想查看系統資訊與播放記錄時，則會發生錯誤，觸發 Internal Server Error 500 錯誤訊息。這樣的情形在使用英文 Asset Name 時並不會發生。

存取 Screenly web server 的 log 記錄存放於 ***/var/log/supervisor/screenly-access.log*** 中，檢視 Log 分析後發現，其中觸發上述錯誤訊息的關鍵 Log 記錄中有一行：

	
   	File "system_info", line 70, in render_body
	UnicodeDecodeError: 'ascii' codec can't decode byte 0xe9 in position 40: ordinal not in range(128)

看來應該是顯示 Log 的網頁程式碼部分無法處理中文，找出 `screenly\server.py` 程式檢視，其中處理 Log 顯示部分的程式碼位於 **\#View** 段落內：

	@route('/system_info')
	@auth_basic(check_creds)
	def system_info():
    viewer_log_file = '/tmp/screenly_viewer.log'
    if path.exists(viewer_log_file):
        viewlog = check_output(['tail', '-n', '20', viewer_log_file]).split('\n')
        
    else:
        viewlog = ["(no viewer log present -- is only the screenly server running?)\n"]

在還沒想出更好的方法之前，先在 viewlog 這行後新增一行，強制轉成 UTF-8 編碼，並順便將 Log 顯示順序改成符合一般習慣的最新記錄在最上方的顯示方式。

	viewlog = [unicode(i, encoding='UTF-8') for i in reversed(viewlog)]

修改後這段程式碼看起來像這樣：

	if path.exists(viewer_log_file):
        viewlog = check_output(['tail', '-n', '20', viewer_log_file]).split('\n')
        viewlog = [unicode(i, encoding='UTF-8') for i in reversed(viewlog)]
    else:
        viewlog = ["(no viewer log present -- is only the screenly server running?)\n"]

爾後選擇管理頁面右上方的 `System Info`功能，就可正常查看具有中文 Asset Name 的播放記錄了。
