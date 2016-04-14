---
layout: post
title: "vi(vim) Tab 縮排設定"
date: 2016-04-14T21:21:47+08:00
---

Python 使用者都知道，程式需要一致的縮排設定方可正常運作，比如說編輯器的*tab鍵*設定，如果使用的是 tab 字元，程式從頭到尾的縮排就必須使用 tab 字元，如果是使用空白字元，就必須都使用空白字元，不可混用。

為了方便統一，我習慣使用 4 個空白字元當作 Python 程式的縮排，底下是使用 vi 編輯器時的設定。

可設定在 ~/.vimrc 中，做為永久設定： 

    :set expandtab

使用空白字元 (space) 取代 Tab。

    :set tabstop=4

設定插入 tab 時用 4 個空白 (space) 字元取代。

    :set shiftwidth=4

設定程式縮排為 4 個空白字元 (space)。


