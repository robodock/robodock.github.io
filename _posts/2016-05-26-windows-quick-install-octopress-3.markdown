---
layout: post
title: "Windows 環境快速安裝 Octopress 3"
date: 2016-05-26T21:57:23+08:00
---

搭配 Chocolatey 套件管理程式(有關 Chocolatey 的安裝，請看[這篇](http://robodock.github.io/2016/05/25/chocolatey.html))，在 Windows 下安裝 Octopress 3 有了更快速的方法：

以管理者權限啟動命令列：

	$ choco install ruby -y

安裝完 Ruby 環境後，需以管理者權限重新啟動命令列，相關指令才會生效，接著安裝 jekyll：

	$ gem install jekyll

 octopress 3 還需要 redcarpet 與 Ruby DevKit： 

同樣以管理者權限安裝 DevKit

	$ choco install ruby2.devkit

安裝 Octopress 3

	$ gem install octopress

接下來關於 octopress 的使用請參考之前[這一篇](http://robodock.github.io/2015/08/17/octopress-3-github-pages-blog.html)。