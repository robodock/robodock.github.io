---
layout: post
title: "Chocolatey 軟體套件管理"
date: 2016-05-25T23:48:31+08:00
---


## Chocolatey
**Chocolatey** 是一套 Windows 上的軟體套件管理程式，讓使用者可以用終端機指令的方式進行軟體的自動安裝、升級、移除動作，如同 Debian/Ubuntu 上的 `apt-get` 或是 Fedora/RedHat 上的 `yum` 一般。

### 安裝 Chocolatey

以管理者權限打開命令列模式，輸入底下指令：

	@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin

使用 Chocolatey 安裝軟體：

	$ choco install nodejs
部分套件提供原始安裝版本，套件名稱後方會加上 .install 區分，這類套件可能需要管理者權限，例如：

	$ choco install nodejs.install


升級：

	$ choco upgrade nodejs

移除：

	$ choco uninstall flashplayer

尋找軟體套件：

	$ choco search docker

查看軟體資訊：

	$ choco info firefox

在[官網軟體套件清單](https://chocolatey.org/packages)頁面可查詢可安裝套件。