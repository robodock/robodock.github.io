---
layout: post
title: "Windows 環境快速安裝 Octopress 3"
date: 2016-05-26T21:57:23+08:00
---

搭配 Chocolatey 套件管理程式(有關 Chocolatey 的安裝，請看[這篇](http://robodock.github.io/2016/05/25/chocolatey.html))，在 Windows 下安裝 Octopress 3 有了更快速的方法：

以管理者權限啟動命令列：

	$ choco install ruby -y

預設安裝目錄為 `C:\Tools\ruby23\` 

安裝完 Ruby 環境後，需以管理者權限重新啟動命令列，相關指令才會生效

 octopress 3 還需要 `Ruby DevKit`： 

同樣以管理者權限啟動命令列，安裝 DevKit

	$ choco install ruby2.devkit

安裝完 devkit 後，需要到 devkit 安裝目錄編輯 config.yml，加入剛剛安裝好 ruby 的目錄位置，然後執行

    ruby dk.rb install

接著安裝 jekyll：

	$ gem install jekyll

安裝 Octopress 3

	$ gem install octopress

中文 Windows 的命令列視窗預設字碼頁為 `CP950`，如果使用 Octopress 或 jekyll 時出現 code page error 訊息例如：`invalid byte sequence in CP950`，可在命令列視窗中執行 `chcp 65001`，變更字碼頁為 `UTF-8`。


接下來關於 octopress 的使用請參考之前[這一篇](http://robodock.github.io/2015/08/17/octopress-3-github-pages-blog.html)。

---

## 2016/12 更新有關 rubygem.org SSL error 問題

rubygems.org 網站原本使用 `sha1` 演算法作為 SSL 憑證計算
，為因應 `sha1` 安全性不足問題，已改用 `sha-256` 演算法。

若使用 `gem install` 命令時發生如下錯誤，表示目前使用的SSL 憑證為 sha1 演算法，無法連接至新的 rubygems.org 網站：

    SSL_connect returned=1 errno=0 state=SSLv3 read server certificate B: certificate verify failed

可手動安裝新的 `rubygem` 來解決。

首先手動下載最新的 [latest RubyGems](https://rubygems.org/pages/download)，存放在易於存取的目錄下，例如 `D:\` ，此例中版本為 `rubygems-update-2.6.10.gem`

接著在命令列視窗下執行：

    D:\>gem install --local D:\rubygems-update-2.6.10.gem
    D:\>update_rubygems

可使用 `gem --version` 確認安裝版本，此例中為 2.6.10

成功後可安全移除上述檔案

    D:\>gem uninstall rubygems-update -x
    Removing update_rubygems
    Successfully uninstalled rubygems-update-2.6.10