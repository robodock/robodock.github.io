---
layout: post
title: "GitHub fork 專案同步上游來源更新"
date: 2016-12-05T14:12:11+08:00
---
## GitHub fork 專案同步上游原始來源的更新

從 GitHub 上 fork 下來的專案，當原始來源有更新後，要如何同步到自己的 fork repo 中? 至目前為止(2016/12)，尚無法直接在 GitHub 管理網頁上進行此動作，必須在本地端進行，步驟如下：

1. 將 fork 出來的專案 `clone` 到本地端作業。
2. 開啟 `git bash`，切換到 clone 下來的專案目錄中。
3. 先檢視一下目前的遠端 repo 設定。

        $ git remote -v
		origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
		origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
	
4. 新增名為 **upstream** 的 "原始來源" repo 設定

		$ git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
	
5. 確認一下遠端 repo 設定

		$ git remote -v
		origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
		origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
		upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
		upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)

6. 使用 `fetch` 指令來取得上游來源的更新，上游 `master` 分支中的更新，會加到本地端的 `upstream/master` 新分支。

		$ git fetch upstream
		remote: Counting objects: 75, done.
		remote: Compressing objects: 100% (53/53), done.
		remote: Total 62 (delta 27), reused 44 (delta 9)
		Unpacking objects: 100% (62/62), done.
		From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
		* [new branch]      master     -> upstream/master

7. 確定目前工作分支。

		$ git checkout master
		Switched to branch 'master'

8. 使用 `merge` 來將上游更新加入本地端。

		$ git merge upstream/master
		Updating a422352..5fdff0f
		Fast-forward
		 README                    |    9 -------
		 README.md                 |    7 ++++++
		 2 files changed, 7 insertions(+), 9 deletions(-)
		 delete mode 100644 README
		 create mode 100644 README.md

9. 若上游更新部分與本地端有衝突則需另行處理。

10. 在本地端整合完成後，再 push 回自己 GitHub 上的 fork repo。

		$ git push origin master