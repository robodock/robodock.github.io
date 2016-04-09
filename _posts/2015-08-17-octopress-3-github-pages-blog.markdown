---
layout: post
title: "使用 Octopress 3 在 GitHub Pages 上快速建立日誌型Blog"
date: 2015-08-17T21:33:56+08:00
---

## 名詞解釋 ##

### *Octopress 3*
* 快速建置與部署 *Jekyll* blogs 的工具程式。

### *Jekyll*
* 使用 *Ruby* 語言開發的靜態網站產生器，可將使用 *Markdown* 之類標記語言撰寫的文字檔轉換為網頁發佈格式文件，透過樣板套用可自由更換版面。

### *GitHub Pages*
* *GitHub* 提供的免費網頁主機服務，主要使用對象為開源社群與資訊技術人員。


## 環境需求

### *Git*
**GitHub Pages** 的服務是架構在 **Git** 工具之上，大部份 Linux 系統都已內建 Git 工具，如果使用的作業系統平台還沒安裝 Git 版本管理工具程式，請先安裝。最好也熟悉一下 Git 分支系統的觀念與操作。

### *Ruby*

Octopress 至少需要 Ruby 1.9.3 版以上，Ruby 在各種平台上除了可由原始碼來安裝外，可方便使用相關安裝管理工具進行安裝：

* **Windows**: 可使用 [RubyInstaller](http://rubyinstaller.org/)。
* **Linux/UNIX**: 可使用第三方工具（如 **rbenv** 或 **RVM**）或使用系統套件管理工具。
* 
* **OS X**: 可透過 **Homebrew** 管理程式來使用第三方工具（如 **rbenv**)。

> 使用 **Homebrew** 來安裝 **rbenv**

>
```
brew update
brew install rbenv
brew install ruby-build
```
> 接著使用 **rbenv** 來安裝 **Ruby**
>
```
rbenv install 1.9.3-p0
rbenv local 1.9.3-p0
rbenv rehash
```

Ruby 環境建置好後，便可以方便使用 Ruby 的套件管理程式 **gem**，輕鬆安裝各項工具程式。

### *Jekyll*
首先安裝 Jekyll
>
```
gem install jekyll
```

### *Octopress*
安裝 Octopress 3
>
```
gem install octopress
```


基本安裝工作到此告一段落，開始進行設定。

## Octopress 3 CLI command
在 Octopress 3 中，大幅簡化了網頁建置程序，集中使用一個 **'octopress'** 指令來達成。

### octopress 的命令參數如下:

	
	init <PATH>         # 初始化網頁框架檔案
	new <PATH>          # 執行 `jekyll new` + `octopress init`
	new post <TITLE>    # 新增一篇日誌貼文
	new page <PATH>     # 新增一篇網站頁面
	new draft <TITLE>   # 新增日誌貼文草稿
	publish <POST>      # 將草稿從 _drafts 發佈至 _posts
	unpublish <POST>    # 將一篇 post 轉換成 draft
	isolate [POST]      # 除了工作中文稿，隱蔽所有貼文，方便快速建置用
	integrate           # 恢復 isolate 指令隱蔽的所有貼文
	deploy              # 透過 S3, Rsync, 或 GitHub pages 發佈網站


## 設定 GitHub pages

要使用 GitHub Pages 服務需要有 GitHub 帳號，事實上建置好後的部落格網址就是 `http://<GitHub 帳號名稱>.github.io` 如果還沒有 GitHub 帳號，先去註冊一個吧。

登入 GitHub 網站，新增一個名為 `username.github.io` 的 Repository，GitHub pages 會使用這個 Repository 的 `master` 分支內容自動作為 `http://username.github.io` 的網站內容。將這個 Repository 的網址連結複製下來，待會用得到。

遠端連線 GitHub 得方式有 HTTPS 與 SSH 等方法，建議使用 **SSH** 方式，設定好金鑰對後，可方便自動連線登入，不會像使用 HTTPS 方法，每次連線都須詢問密碼。 SSH 連結網址URL看起來像這樣 

`git@github.com:<username>/<username>.github.io.git`

注意最後方有 git 字尾。

## 新增 Octopress 3 工作目錄

**New**

	$octopress new <path>

基本上是執行 `jekyll new` + `octopress init` 兩個指令，會在指定的 <path\> 目錄下建立 jekyll 與 octopress 網站的基礎目錄架構與檔案。 

## 起始工作目錄 .git 目錄
在新增的 Octopress 3 工作目錄下，起始 git 管理 

	$ git init


## 張貼新文章

**New Post**

	$octpress new post "我的標題"

新增一篇標題為 "我的標題" 的日誌貼文。這篇文章會存放於 `_post` 目錄中，系統預設會用日期來作子目錄分類。

**New Page**

	$octopress new page some-page

新增一篇網頁頁面，網頁頁面不同於日誌文章，通常用於常時固定於顯示頁面上，存放於 `_page` 目錄中。

	
**New Draft**

	$octopress new draft "我的標題"

新增一篇名為 "我的標題" 的草稿文章，存放於 `_draft` 目錄下。網站發佈時，會自動略過 `_draft` 目錄。


**Publich a draft**

	$octopress publish _draft/some-cool-post

 將 `_draft` 資料夾中的 `some-cool-post` 草稿文章發佈到 `_post` 資料夾中，下次利用 `deploy` 指令發佈網頁時，便會發佈到網站上。

**Unpublish a post**

	$octopress unpublish _post/2015-08-01-some-post

將已發佈的 `2015-08-01-some-post` 文章改成草稿(移至 `_draft` 資料夾，記得網站頁面要在 `deploy` 後才會改變。



## 網頁自動建置

當文章寫好後，準備發佈

	$jekyll build

會利用 `jekyll` 靜態網頁產生工具，來將寫好的 `markdown` 文章轉換成套用網站樣式模板的 HTML 網頁。產生的網站會存放於 `_site` 目錄中。

jekyll 另外提供了簡單的本地端網站伺服器，方便在本機先行預覽結果。

	$jekyll serve

執行上述指令後，可在瀏覽器使用下列網址 `http://locolhost:4000` 預覽網頁。

## 將網站發佈到 GitHub Pages 上

網頁一切就緒後，便可利用前面建好的 GitHub Pages Repository ，將網頁發佈至 GitHub Pages 上。

首次發佈，需要初始化相關設定

	$octopress deploy init git@github.com:<username>/<username>.github.io.git

記得將 <username\> 替換成你自己的 GitHub 帳號名稱。
上述動作會在工作目錄下產生 `_deploy.yml` 設定檔，如果有需要，可手動編輯設定檔內容。

爾後發佈時，只要使用

	$octopress deploy

基本上使用 **Octopress + GitHub Pages** 來建置部落格的過程，到此完成。

注意上述動作僅將網站 ( `_site` 資料夾的內容)發佈至 GitHub Pages上，整個 octopress 工作目錄僅存在於本機上。如果你想在可在多部電腦上寫文章，並讓工作進度維持同步，可利用 GitHub 的分支 (branch) 功能，將工作目錄同步到 GitHub 上的另一個分支下，便可在不同電腦使用 git 的 `push` 與 `pull` 功能達到同步目的。請參考底下步驟：

## 在多台電腦上使用 Octopress + GitHub Pages

Octopress 使用兩個分支 `source` 與 `master`, `source` 分支包含用來產生網頁的工作目錄，包含草稿資料夾，而 `master` 則僅包含網站本身。

在網站 `deploy` 過程中, `master` 分支會同步到 GitHub Pages 上 `<username>.github.io` `Repository` 中的 `Master` 分支。
我們可以另外建立一個名為 `source` 的分支，來存放工作目錄，已方便在不同電腦中同步。

在本機 Octopress 工作目錄下

	$ git remote add origin <GIT-REPO-URL>

其中 <GIT-REPO-URL\> 要換成自己的 GitHub Pages Repository 位址

將本機 Octopress 工作目錄 commit 進 git 資料庫，並 push 到遠端(GitHub)上

	$ git add .
	$ git commit -am 'commit comment'
	$ git checkout -b source

	$ git push -u origin source


想在其他電腦工作時，首先將遠端 Source Repository clone 下來

clone `source` 分支

	$ git clone -b source <GIT-REPO-URL> <本地工作目錄>


這樣一來，這部電腦上也有了一樣的工作環境，只要記得工作完要將 `Source`分支 `push` 回 GitHub 遠端即可。

---
每次使用 Octopress 工作程序如下：

先把遠端 GitHub 的 Source branch 內容 `pull` 下來更新本地端：

	$ cd <本地工作目錄>
	$ git pull origin source	#更新本地 source branch
	

寫完文章後的動作如下：

	$ jekyll build				#產生網頁
	$ octopress deploy			#更新遠端 master branch

再把 Source 更新部分 `push` 回遠端 GitHub Source 上：

	$ git add .
	$ git commit -am "commit comment"
	$ git push origin source 	#更新遠端 source branch

	
	