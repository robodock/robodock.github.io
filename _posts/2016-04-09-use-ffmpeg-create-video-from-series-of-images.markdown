---
layout: post
title: "使用 FFmpeg 將序列圖片轉換成影片"
date: 2016-04-09T15:32:16+08:00
---

## 使用 FFmpeg 將序列圖片轉換成影片 ##

	ffmpeg -framerate 5 -i img%03d.png -c:v libx264 -r 30 -pix_fmt yuv420p output.mp4

`-framerate` 每秒多少張圖片，預設值為 25, `-r` 影片播放 fps, 省略時使用與輸入framerate 相同數值。

如果想從一系列的圖片中的特定號碼開始，可使用 `-start_number` 參數，

	ffmpeg -framerate 5 -start_number 126 -i img%03d.png -c:v libx264 -r 30 -pix_fmt yuv420p output.mp4

如果影片無法正確顯示時，嘗試使用下列 fps video filter 格式取代 `-r`:

	ffmpeg -framerate 5 -i img%03d.png -c:v libx264 -vf fps=25 -pix_fmt yuv420p output.mp4

或指定 format video filter:

	ffmpeg -framerate 5 -i img%03d.png -c:v libx264 -vf "fps=25,format=yuv420p" output.mp4
