---
layout: post
title: "OS X 下建置 Ruby 開發應用環境"
date: 2016-04-09T19:33:41+08:00
---

## OS X 下建置 Ruby 開發應用環境 ##

### 基本安裝 ###
基本安裝適合只想使用單一 Ruby 版本的使用者，開發人員可直接跳過這一段，直接查看 *進階安裝*

OS X 系統中已內建 Ruby 環境，但通常版本老舊，開放原始碼世界變化快速，建議使用最新穩定版。
若環境單純，無多版本需求者，可使用 Homebrew 套件管理工具來安裝：

	$ brew install ruby

---

### 進階安裝 ###
	
開發與應用開放原始碼軟體時，經常會遇到安裝多個程式庫版本的問題，在 Ruby 的世界中，我們可以使用 **版本管理工具 rbenv ** 來達成在不同 Ruby 版本中的作業管理：

###rbenv###

rbenv 透過在系統路徑最前方加入 `~/.rbenv/shims`路徑，透過 shims 程式攔截 ruby 相關指令，再依據所設定的參數，選擇對應的 ruby 版本據以執行。

rbenv 管理下的不同版本 ruby 系統程式會安裝在 `~/.rbenv/versions/x.x.x` 目錄下。

使用 Homebrew 安裝 rbenv 版本環境管理工具：

	$ brew install rbenv
	
更新 rbenv 

	$ brew upgrade rbenv ruby-build

第一次使用須執行 `rbenv init`，依照說明進行初始化設定。

	$ rbenv init
	
安裝不同版本 Ruby :

	$ rbenv install -l  //列出可安裝版本
	$ rbenv install 2.2.3	//安裝 ruby 2.2.3 版
	$ rbenv rehash		//在安裝的版本目錄中加入 shims
	
要切換使用的 ruby 版本環境：

	$ rbenv version	//查看目前使用版本
	$ rbenv local x.x.x	//選擇使用版本
	
然後便可正常執行諸如 gem 之類的指令。

時間一久，系統中可能累積了許多舊版本，移除舊版本可直接清除 `~/.rbenv/versions` 底下的目錄或透過 `rbenv uninstall` 來移除。

其他參考指令：

	$ rbenv versions //查看系統中安裝了哪些版本
	$ rbenv version	//查看目前使用版本
	