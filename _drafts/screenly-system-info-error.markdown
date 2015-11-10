---

layout: post
title: "Screenly System Info Error"

---

# 解決 Screenly System Info error 問題 #

Screenly 為一套為 Raspberry Pi 量身打造的電子看板軟體，可提供播放網頁，圖片，影片(限h.264格式)，Screenly Pro 版提供雲端商業服務，而 Screenly OSE 則為開放程式碼的社群版本。

相較於其他電子看板軟體，Screenly 的操作簡單直覺，每個播放項目稱為 **ASSET** , 可為每個 ASSET 設定預定播放時間，內容(網頁、圖片、影片)，與每個項目的播放長度。

![screenly](https://www.screenlyapp.com/img/screenly-ose-8bcf3db7.png) 

Screenly 的 Asset Name 是可以輸入中文的，可以清楚標示播放內容。但目前版本存在一個 Bug, 當選擇管理頁面右上方的 `System Info`功能，想查看系統資訊與播放記錄時，若播放中的項目(Asset)的名稱包含中文字碼，則會發生錯誤，觸發 Internal Server Error 500 錯誤訊息。

詳細檢視 Log 進行分析，存取 Screenly web server 的 log 記錄存放於 ***/var/log/supervisor/screenly-access.log*** 中，其中觸發上述錯誤訊息的Log記錄如下：

	Traceback (most recent call last):
	  File "/usr/local/lib/python2.7/dist-packages/bottle.py", line 862, in _handle
    return route.call(**args)
	  File "/usr/local/lib/python2.7/dist-packages/bottle.py", line 1729, in wrapper
    rv = callback(*a, **ka)
	  File "/home/pi/screenly/server.py", line 319, in system_info
    return template('system_info', viewlog=viewlog, loadavg=loadavg, free_space=free_space, uptime=system_uptime, display_info=display_info)
	  File "/home/pi/screenly/server.py", line 97, in template
    return haml_template(template_name, **context)
	  File "/usr/local/lib/python2.7/dist-packages/bottle.py", line 3592, in template
    return TEMPLATES[tplid].render(kwargs)
	  File "/usr/local/lib/python2.7/dist-packages/bottlehaml/__init__.py", line 34, in render
    return self.tpl.render(**_defaults)
	  File "/usr/local/lib/python2.7/dist-packages/mako/template.py", line 412, in render
    return runtime._render(self, self.callable_, args, data)
	  File "/usr/local/lib/python2.7/dist-packages/mako/runtime.py", line 766, in _render
	    **_kwargs_for_callable(callable_, data))
	  File "/usr/local/lib/python2.7/dist-packages/mako/runtime.py", line 798, in _render_context
	    _exec_template(inherit, lclcontext, args=args, kwargs=kwargs)
	  File "/usr/local/lib/python2.7/dist-packages/mako/runtime.py", line 824, in _exec_template
    callable_(context, *args, **kwargs)
	  File "system_info", line 70, in render_body
	UnicodeDecodeError: 'ascii' codec can't decode byte 0xe9 in position 40: ordinal not in range(128)
