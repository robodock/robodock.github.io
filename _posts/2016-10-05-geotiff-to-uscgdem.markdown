---
layout: post
title: "SRTM GeoTiff 數值地形檔案處理"
date: 2016-10-05T22:11:31+08:00
---

## SRTM GeoTIFF 數值地形檔案處理

### 下載 15m 精確度 DEM 數位高程資料
>[ClusterGIS Associate](http://www.theearthsrelief.com/)

### GeoTIFF 檔案轉換為 USGSDEM

需要透過 GDAL 來處理，GDAL 主要在 Unix/Linux 環境下開發，在
Windows 環境下則可使用第三方編譯好的 GDAL binary 可執行檔，目前主要有 **GISInternals**，**MS4W**，**OSGeo4W** 三處主要來源：

下載地點連結:

>[GISInternals](http://www.gisinternals.com/release.php)
>
>[MS4W](http://www.ms4w.com/)
>
>[OSGeo4W](https://trac.osgeo.org/osgeo4w/)

這裡以使用 GISInternals 來源為例，選擇適用的 CPU 與編譯器版本，若不使用其他套件，點進去後只要選取 core component 安裝。

安裝完成後必須將 GDAL 的 bin 目錄加進系統路徑中，另新增一個名為 GDAL_DATA 的系統環境變數，並設定內容為 GDAL 的 gdal-data 目錄。

如果想要利用 Python 來處理，則需要安裝 GDAL Python Library

使用 Anaconda Python 者可用 conda 來安裝：

	$ conda install gdal
	$ conda upgrade numpy


GeoTIFF 轉檔 DEM

	gdal_translate -of USGSDEM input.tif output.dem
