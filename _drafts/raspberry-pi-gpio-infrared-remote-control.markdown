---
layout: post
title: "使用 Raspberry Pi 製作紅外線遙控器"
---


*好消息是，困難的部分總是有人幫你做好了。*

**LIRC**(Linux Infrared Remote Control)程式庫套件讓我們可以在 Linux 上控制紅外線遙控器，更棒的是現在的 **Raspbian** OS，也加入了對 LIRC 的支援，只要有些紅外線 LED 電子零件，連接 **Raspberry Pi** 的 **GPIO** 針腳，便可輕鬆遙控家裡的電視機或他紅外線遙控設備。

先來在 Raspberry Pi 上安裝 **LIRC** 套件:

	$sudo apt-get install lirc

在模組引入設定檔 `/etc/modules` 中加入相關設定：

	$lirc_dev
	$lirc_rpi gpio_in_pin=23 gpio_out_pin=22

GPIO 腳位可自訂，這裡使用 Pin **22** 與 Pin **23**，避開經常定義為 UART 或 I2C 用途的腳位，其中 Pin **22** 指定用途為輸入訊號(接收紅外線訊號)， Pin **23** 指定用途為輸出訊號(發射紅外線訊號)。

修改 `/etc/lirc/hardware.conf` 設定檔:

	$########################################################
	# /etc/lirc/hardware.conf
	#
	# Arguments which will be used when launching lircd
	LIRCD_ARGS="--uinput"

	# Don't start lircmd even if there seems to be a good config file
	# START_LIRCMD=false

	# Don't start irexec, even if a good config file seems to exist.
	# START_IREXEC=false

	# Try to load appropriate kernel modules
	LOAD_MODULES=true

	# Run "lircd --driver=help" for a list of supported drivers.
	DRIVER="default"
	# usually /dev/lirc0 is the correct setting for systems using udev
	DEVICE="/dev/lirc0"
	MODULES="lirc_rpi"

	# Default configuration files for your hardware if any
	LIRCD_CONF=""
	LIRCMD_CONF=""
	########################################################

較新版的 Raspbian OS(Debian Jessie) 已經開始改用 Device Tree 來管理硬體設備，使用 Jessie 版本的需要修改 `/boot/config.txt` 開機啟動組態檔。

	dtoverlay=lirc-rpi,gpin_in_pin=23,gpio_out_pin=22

重開機讓設定生效。

---

遙控器的紅外線發射與接收是使用不同的元件，紅外線發射通常使用波長為 940nm 的LED，驅動電壓約在 1.2V ~ 2.0V 之間，而接收器通常使用 38KHz 的感應模組。

紅外線遙控的原理是利用紅外線 LED 發射 PWM(脈波寬度調變) 訊號，而 Raspberry Pi 正好可利用 GPIO 腳位來產生 PWM 訊號，因此接上紅外線 LED 後可用來發射遙控訊號。

每個遙控器按鍵會有一個對應編碼。必須先知道各個按鍵的編碼後，方可正確遙控設備。

LIRC 套件中包含了 `irrecord` 工具程式，可用來記錄遙控器每個按鍵編碼，所以首先必須先製作一個紅外線接收器，方可記錄遙控器編碼。


## 連接紅外線接收器 ##

我這邊使用的是 IRM-3638N3 series 的紅外線接收模組，接收頻率 38KHz，工作電壓為 0~6V。

![IRM3638N3](https://googledrive.com/host/0B3VMyKy-nGUYdUdGOEQzcVpYRDQ)

將接收模組的針腳 1 連接至 RPi GPIO Pin 23(GPIO IN)，針腳 2 連接至 GPIO pin 3(GND)，針腳 3 連接至 GPIO pin 1(+3.3V)，Raspberry Pi 的GPIO 腳位可參考下圖。

![Raspberry GPIO pinout](http://www.elektronik-kompendium.de/sites/raspberry-pi/fotos/raspberry-pi-15.jpg)
 
接著便可測試接收器是否運作正常。先停止 lirc 系統服務，避免設備資源被占用，接著使用 LIRC 提供的工具程式 `mode2` 來讀取輸入資料:

	sudo /etc/init.d/lirc stop
	mode2 -d /dev/lirc0

一切正常的話，應該可以看到類似底下的輸出：

	space 2508
	pulse 738
	space 1263
	pulse 737
	space 663
	pulse 737
	space 1263
	pulse 737
	space 663
	pulse 762
	space 1264
	pulse 710
	space 679
	pulse 748
	space 611
	pulse 788

---
##錄製遙控器訊號##

接下來便可用 `irrecord` 指令來錄製遙控器按鍵編碼：

	$sudo /etc/init.d/lirc stop
	//停止lirc服務，確定設備資源未被占用

	$irrecord -d /dev/lirc0 lircd.conf
	//使用 irrecord 指令，將 /dev/lirc0 設備接收到的編碼，錄製到 lircd.conf 檔案中

irrecord 是一個互動的程式，只要依照說明指示，一步步進行即可。過程大致如下：

第一步會先要求你隨意按下遙控器按鍵，按下時畫面會出現點狀 "." 字符，控制按下的時間長短為出現 10 個 "." 以內。此步驟會重複兩次，用來找出 PWM 訊號的固定長度。

第二步，開始記錄按鍵，先輸入按鍵名稱，雖然按鍵名稱並無硬性規定，但基本上還是參考 lirc 的慣用按鍵名稱，例如電源鍵為 KEY_POWER

第三步會要求對同一個按鍵，快速連續不斷按擊，用來找出延遲設定，避免二次按擊。

完成後，lircd.conf 的內容看起來像這樣:

	begin remote
	
	  name  sony
	  bits            4
	  flags SPACE_ENC|CONST_LENGTH
	  eps            30
	  aeps          100
	
	  header       2432   587
	  one          1211   582
	  zero          612   582
	  post_data_bits  8
	  post_data      0x90
	  gap          45003
	  min_repeat      2
	#  suppress_repeat 2
	#  uncomment to suppress unwanted repeats
	  toggle_bit_mask 0x0
	
	      begin codes
	          KEY_POWER                0xA
	          KEY_MUTE                 0x2
	      end codes
	
	end remote

可看到 `KEY_POWER` 的 code 為 0xA，而 `KEY_MUTE` 為 0x2。


完成後可將錄製好的 lircd.conf 複製為 /etc/lirc/lircd.conf，可供全域性使用。

	$sudo cp lircd.conf /etc/lirc/lircd.conf
	$sudo /etc/init.d/lirc start //啟動 lirc 服務


---

##連接紅外線發射 LED##

![紅外線LED](https://googledrive.com/host/0B3VMyKy-nGUYUk4xZkFKUDlFU1E)

網路上建議的 LED 接法像這樣，利用一個放大電晶體來驅動紅外線 LED，可讓遙控距離增加到數公尺的距離，如果只是要近距離測試，直接將 LED 接在 GPIO 上也是可以的。

直接連接的話，將 LED 較長的針腳(+)接至 Rraspberry Pi GPIO Pin 22(GPIO IN)，較短的針腳(-)連接至 GPIO Pin 14(GND)。

我這邊接起來的測試環境像這樣：

![Raspberry Pi2 GPIO Infrared LED test](https://googledrive.com/host/0B3VMyKy-nGUYaEdTZnpoMzZZeG8)

發送遙控訊號時：

	$irsend LIST sony ""
	//可列出 lircd.conf 中名為 sony 的組態中所有的對應按鍵名稱

	$irsend SEND_ONCE sony KEY_POWER
	//發送一次 KEY_POWER 遙控碼

---

###完成!###

記得不要惡搞鄰居家的電視機...