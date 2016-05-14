---
layout: post
title: "建立 Chroot 環境"
---

## Chroot
暫時將系統根目錄指到特定的目錄下，建立ㄧ個獨立的環境供測試、開發用。在此環境下執行的程式無法看到系統中的其他部分。

### 建立工具
在 chroot 建立的環境中，需要基本的系統程式才能運作。手動建立環境程序太繁雜，還好有 `dchroot` 與 `debootstrap` 工具程式幫我們處理。

`dchroot` 用來管理不同的 chroot 環境，`debootstrap` 則用來在指定目錄中建立一個基本作業系統。附帶一提的是 `dchroot` 已被 `schroot` 取代，雖然仍可用 `dchroot` 指令存取，事實上會連結至 `schroot`。 

	$sudo apt-get update
	$sudo apt-get install dchroot debootstrap
	
編輯 `dchroot` 設定組態檔

	$sudo vi /etc/schroot/schroot.conf
	
底下是個範例

	[saucy]
	description=Ubuntu Saucy
	location=/test
	priority=3
	users=demouser
	groups=sbuild
	root-groups=root

在指令目錄中安裝系統

	sudo debootstrap --variant=buildd --arch amd64 saucy /test/ http://mirror.cc.columbia.edu/pub/linux/ubuntu/archive/
	
> `--variant＝` 項目表示使用何種類型的環境，`buildd` 表示直接安裝成程式開發工具，也就是會安裝 `build-essential` 套件。
> 
> `--arch` 指定系統架構
> 
> `saucy` 則對應於 `/etc/schroot/schroot.conf` 中的設定
> 
> `/test/` 安裝目錄，後方之網址則為來源路徑

安裝完成後，如果查看 `/test/` 目錄內容，可看到如同系統根目錄般的目錄結構：

	bin   dev  home  lib64  mnt  proc  run   srv  tmp  var boot  etc  lib   media  opt  root  sbin  sys  usr
	
最後，進行主系統的 fstab 檔設定，好讓主系統能認得子系統的系統程序檔：

	$sudo vi /etc/fstab	

<br> 

	proc /test/proc proc defaults 0 0
	sysfs /test/sys sysfs defaults 0 0

掛載進子系統

	$sudo mount proc /test/proc -t proc
	$sudo mount sysfs /test/sys -t sysfs
	
子系統若需要存取與主系統相同的網路環境，可將主系統的 `/etc/hosts` 檔案複製進子系統

	$cp /etc/hosts /test/etc/hosts

### 完成

chroot 到子系統：

	$sudo chroot /test/ /bin/bash

