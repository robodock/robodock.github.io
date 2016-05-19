---
layout: post
title: "使用 BeagleBone Black 或 Raspberry Pi 打造簡易的網路 VoIP/Voice Chat 設備 (2/2)"
date: 2016-05-19T22:20:08+08:00
keywords: beaglebone black, seren, voip, python, raspberry pi
---


##使用 Python 來控制 Seren

###安裝 PyBBIO 模組

我這邊打算使用 Python 來處理 IO 控制，這樣就可透過按鍵或開關，來控制連線通訊，也可接個燈號顯示來電。

PyBBIO 模組應可達到我想要的功能。

先滿足相依性需求：

	# apt-get install python-serial python-setuptools python-dev python-smbus python-pip

註：目前 PyBBIO 穩定版只支援到 3.8 版 Linux 核心。

同時需要 Device Tree 編譯器 dtc，若系統中沒有，可至下列位址安裝:

	# wget -c https://raw.github.com/RobertCNelson/tools/master/pkgs/dtc.sh

上述環境準備好之後，安裝 PyBBIO Python 程式庫模組

	# pip install --upgrade PyBBIO

想要自己編譯的，可至下列位址下載:

	# git clone git:/github.com/alexanderhiam/PyBBIO.git
	# cd PyBBIO
	# python setup.py install


我的 ugly 控制程式如下:

	#!/usr/bin/env python
	from bbio import *
	from bbio.libraries.EventIO import *	//bbio 的 EventIO 可建立額外的事件偵測迴圈
	import pexpect		//使用 pexpect 來建立子程序呼叫其他程式並與其溝通
	
	bbio.bbio_init()
	
	#指定對應GPIO針腳
	pinAccept = GPIO1_13 	//P8_11
	pinHangup = GPIO1_12 	//P8_12
	pinCall = GPIO1_15		//P8_15
	pinLED = GPIO1_14    	//P8_16
	
	#設定 GPIO 針腳功能
	pinMode(pinAccept, INPUT)
	pinMode(pinHangup, INPUT)
	pinMode(pinCall, INPUT)
	pinMode(pinLED, OUTPUT)
	
	#建立子行程，呼叫 seren 程式，並給予相關參數
	child = pexpect.spawn('seren -n Engine -NS -d hw:1,0')
	#方便除錯，先將子行程輸出顯示在 stdout
	child.logfile_read=sys.stdout

	#定義觸發事件與進行的動作，記得要回傳 EVENT_CONTINUE，事件才可重複觸發
	def accept():
	    child.sendline('/y')
    	return EVENT_CONTINUE

	def hangup():
    	child.sendline('/H')
    	digitalWrite(pinLED, LOW)
    	return EVENT_CONTINUE

	def call():
    	child.sendline('/c 192.168.168.114')
    	return EVENT_CONTINUE

	#建立事件偵測迴圈，並加進偵測事件，後方之數值 400 為消除 bounce 的延遲時間
	event_loop = EventLoop()
	event_loop.add_event(DigitalTrigger(pinAccept, HIGH, accept, 400))
	event_loop.add_event(DigitalTrigger(pinHangup, HIGH, hangup, 400))
	event_loop.add_event(DigitalTrigger(pinCall, HIGH, call, 400))
	event_loop.start()

	#透過偵測子行程 seren 的輸出回應，顯示來電燈號，因 expect事件會 blocking 其他行程，這裡指定 timeout 秒數為 1，以持續不斷偵測。
	while True:
    	index = child.expect(['is calling:',pexpect.TIMEOUT],1)
    	if index == 0:
    	    #print '\nIncoming Call!'
    	    digitalWrite(pinLED, HIGH)
    	else:
        	pass
    

