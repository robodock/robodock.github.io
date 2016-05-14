---
layout: post
title: "BeagleBone Black 的 Device Tree overlays"
date: 2016-05-14T17:55:30+08:00
---

Device Tree (DT)是一種用來描述系統中硬體設備的方法，例如描述 UART 如何與系統介接，接腳定義，啟用與否，使用何種驅動程式等等。

過去數年，各式各樣硬體架構的 Linux 設備蓬勃快速發展，特別是 ARM 架構系統，然而百家爭鳴的場面，加上 ARM 社群無人出面整合，各方的開發者經常隨意的修改並要求 Kernel pull request，讓老大 Torvalds 還因此[動怒](http://article.gmane.org/gmane.linux.ports.arm.omap/55060)，因此 BeagleBone 開發人員便實作了 Device Tree 方法，來處理這些硬體設備。

BeagleBone Black 從 Linux Kernel 3.8 後開始使用 Device Tree，底下來實際看看一個 BB-UART1 DT 檔案內容，如何設定使用 BBB 的 P9\_24 與 P9\_26 來作為 UART 接腳。

	/dts-v1/;
	/plugin/;
	
DT overlay 檔案中的前兩行說明此 dts 檔案的版本，並說明這是一個 plugin。

	/ {
	
接下來的大括號描述此 DT 的根節點(root node)。

	compatible = "ti,beaglebone", "ti,beaglebone-black";
	
"compatible" 說明此 DT 設計用來在何種硬體平台使用，從最相容到最少相容依序排列。

	/* identification */
	part-number = "BB-UART1";
	version = "00A0"; 
	
part-number 與 version 進一步確保載入適當的 DT overlays。

	/* state the resources this cape uses */
        exclusive-use =
                /* the pin header uses */
                "P9.24",        /* uart1_txd */
                "P9.26",        /* uart1_rxd */
                /* the hardware ip uses */
                "uart1";
                
exclusive-use 描述需要的資源，防止其他 overlays 使用這些資源，例如這裏指定的 UART1 TX 與 RX 分別使用 P9.24 與 P9.26 針腳。

接下來是 device tree 片段，描述要 overlay 哪個 target，例如底下指定的 am33x_pinmux，相容於 pinctrl-single driver(?)

\_\_overlay\_\_ 節點中的第一項屬性為 bb\_uart1\_pins，包含針腳的定義，以便供 pinctrl-single driver 使用。

	fragment@0 {
                target = <&am33xx_pinmux>;
                __overlay__ {
                        bb_uart1_pins: pinmux_bb_uart1_pins {
                                pinctrl-single,pins = <
                                        0x184 0x20 /* P9.24 uart1_txd.uart1_txd MODE0 OUTPUT (TX)  */
                                        0x180 0x20 /* P9.26 uart1_rxd.uart1_rxd MODE0 INPUT (RX)  */
                                >;
                        };
                };
        };

最後一段用來啟動 uart 設備，指定 uart2 與相對應的 pin (bb\_uart1\_pins)。

在 `/lib/firmware` 目錄中可以查看許多的 DT overlays 檔案，dts 為原始檔，dtbo則為已編譯檔。而目前系統中已透過 bone cape manager 啟用中的 overlays 則位於 `/sys/devices/bone_capemgr.*` 註：(Jessie 版本改至 `/sys/devices/platform/bone_capemgr/`下)

	root@beaglebone:/lib/firmware# cd /sys/devices/bone_capemgr.*
	
會用 * 的原因為，有時會因啟動順序而改變。接著查看其中的 slots 檔案：  

	root@beaglebone:/sys/devices/bone_capemgr.8# cat slots
	
會出現如下的內容：

	0: 54:PF--- 
	1: 55:PF--- 
	2: 56:PF--- 
	3: 57:PF--- 
	4: ff:P-O-L Bone-LT-eMMC-2G,00A0,Texas Instrument,BB-BONE-EMMC-2G
	5: ff:P-O-L Bone-Black-HDMI,00A0,Texas Instrument,BB-BONELT-HDMI

前三個 slots 是由 capes 的 EEPROM IDs 指定，接下來兩個是開機時載入，第 4 項為板子內建的 EMMC 記憶體，第 5 項用來啟用 HDMI component。

接著來嘗試載入 UART1 overlay，剛剛在 `/lib/firmware` 目錄中可查得 uart1 的 overlay dtbo 檔名為 `BB-UART1.dtbo`，因此我們可以如此做：

	root@beaglebone:/sys/devices/bone_capemgr.8# echo BB-UART1 > slots

此動作會使用 overlay 來啟用 UART1 設備與驅動程式，確認一下：

	root@beaglebone:/sys/devices/bone_capemgr.8# cat slots
	
	0: 54:PF--- 
	1: 55:PF--- 
	2: 56:PF--- 
	3: 57:PF--- 
	4: ff:P-O-L Bone-LT-eMMC-2G,00A0,Texas Instrument,BB-BONE-EMMC-2G
	5: ff:P-O-L Bone-Black-HDMI,00A0,Texas Instrument,BB-BONELT-HDMI
	6: ff:P-O-L Override Board Name,00A0,Override Manuf,BB-UART1
	
要手動卸載時，在項目號碼前加上 `-` 號，echo 進 slots 檔案中：

	root@beaglebone:/sys/devices/bone_capemgr.8# echo -6 > slots
	
上述手動載入的 overlay 會在重開機即消失，要在開機時自動載入，必須修改 /boot/uEnv.txt 檔案，加入此行：

	capemgr.enable_partno=BB-UART1
	
##編譯新的 Device Tree

有時需要加入原始系統中沒有的 DT overlays，需要自己將 dts 檔編譯成 dtbo 檔，例如底下 Adafruit 的 SPI bus overlay，方法如下：

	dtc -O dtb -o ADAFRUIT-SPI0-00A0.dtbo -b 0 -@ ADAFRUIT-SPI0-00A0.dts
	
系統中如果沒有 dtc 編譯器，可至此處下載：

	wget -c https://raw.githubusercontent.com/RobertCNelson/tools/master/pkgs/dtc.sh
	chmod +x dtc.sh
	./dtc.sh 



 