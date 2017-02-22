---
layout: "post"
title: "Anaconda 多重版本"
date: "2016-12-10 08:20"
---

使用 Conda 建立虛擬環境

### 建立一個 32bit Python 2.7 環境 ###

	set CONDA_FORCE_32BIT=1
	conda create -n py27_32 python=2.7

啟用:

	set CONDA_FORCE_32BIT=1
	activate py27_32

### 建立一個 64bit python 3.5 ###

	set CONDA_FORCE_32BIT=
	conda create -n py35_64 python=3.5

啟用:

	set CONDA_FORCE_32BIT=
	activate py35_64
