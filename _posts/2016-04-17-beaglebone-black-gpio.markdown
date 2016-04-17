---
layout: post
title: "BeableBone Black 使用 GPIO"
date: 2016-04-17T12:08:05+08:00
---

安裝 Adafruit.BBIO python 模組

    $ sudo apt-get update
    $ sudo apt-get install build-essential python-dev python-setuptools python-pip python -smbus

    $ sudo pip install Adafruit_BBIO

執行下列指令測試模組是否成功安裝：

    sudo python -c "import Adafruit_BBIO.GPIO as GPIO; print GPIO"

    #you should see this or similar:
    <module 'Adafruit_BBIO.GPIO' from '/usr/local/lib/python2.7/dist-packages/Adafruit_BBIO/GPIO.so'>


##用法：##

匯入模組:

	import Adafruit_BBIO.GPIO as GPIO
	
設定 GPIO 為輸入或輸出

	GPIO.setup("P8_12", GPIO.IN)

偵測 GPIO 輸入

	if GPIO.input("P8_12"):
   		print("HIGH")
	else:
   		print("LOW")

等候 GPIO 事件發生，此方式會一直等候，直到事件發生

	GPIO.wait_for_edge("P8_12", GPIO.RISING)
	\\GPIO.RISING 從低位變高位
	\\GPIO.FALLING 從高位變低位

另一種偵測事件方式，僅在執行時檢查，通常會搭配迴圈進行持續檢測

	#加入偵測事件
	GPIO.add_event_detect("P8_12", GPIO.FALLING)
	#接著可執行其他程式碼
	#在想要偵測的地方加入:
	if GPIO.event_detected("P8_12"):
   		print "event detected!"


###BeagleBone Black GPIO 腳位參考圖###
   		
![BeagleBone Black GPIO pin](http://www.mathworks.com/help/supportpkg/beagleboneio/ug/beaglebone_black_pinmap.png)

連接按鈕元件(push button)，避免按壓按鈕時多次觸發

	import Adafruit_BBIO.GPIO as GPIO
	import time

	GPIO.setup("P8_12", GPIO.IN)

	while True:
   		if GPIO.input("P8_12"):
      		print('button pressed!')
       	time.sleep(0.1)	//簡單加入延遲迴圈可避免多次觸發