---
layout: "post"
title: "Raspberry Pi3 打造 OpenALPR 車牌辨識系統"
date: "2017-02-26 20:21"
---

OpenALPR 為一開源車牌辨識程式庫, [github連結](https://github.com/openalpr/openalpr)。
最新的 Ubuntu 16.04 上已納入系統套件庫，可直接 apt-get 安裝，不過截至目前為止(2017/02) raspbian 上還沒有編譯好的套件，需要手動編譯安裝。

這裡亦列出 Ubuntu 16.04 上的套件安裝方法供參考

### Ubuntu 16.04 ###

    $ sudo apt-get update && sudo apt-get install -y openalpr openalpr-daemon openalpr-utils libopenalpr-dev

用圖片檔測試結果：

    $ wget http://plates.openalpr.com/ea7the.jpg
    $ alpr -c us ea7the.jpg
    
    $ wget http://plates.openalpr.com/h786poj.jpg
    $ alpr -c eu h786poj.jpg

# 在 Raspberry Pi3 上安裝 OpenALPR #

系統使用 raspbian jessie，OpenALPR 需要 OpenCV 與 tesseract-ocr，可分別獨立安裝。

## 安裝 OpenCV  ##

在 Raspberry Pi3 安裝 OpenCV 最詳盡的 step by step 步驟莫過於[這篇](http://www.pyimagesearch.com/2016/04/18/install-guide-raspberry-pi-3-raspbian-jessie-opencv-3/)了。摘要如下：

1. 確定記憶卡上有足夠的空間，下載與編譯需要用個 3~4G，我是用 8G 的卡安裝 raspbian lite，空間還夠用，記得使用 `sudo raspi-config` 設定將檔案系統擴展到使用整張記憶卡空間。

2. 安裝編譯環境與工具

    養成好習慣，先來個更新：
        
        $ sudo apt-get update
        $ sudo apt-get upgrade
        
    安裝編譯環境與工具
    
        $ sudo apt-get install build-essential cmake pkg-config
        
    一些必要的相依性套件
    
        $ sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev
        $ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
        $ sudo apt-get install libxvidcore-dev libx264-dev
        
    要使用 OpenCV 的 `highgui` 圖形介面的需要這個：
    
        $ sudo apt-get install libgtk2.0-dev
    
    矩陣數值運算會用到
    
        $ sudo apt-get install libatlas-base-dev gfortran
        
    需要 python 標頭檔
    
        $ sudo apt-get install python2.7-dev python3-dev
        
3. 下載 OpenCV 與 OpenCV_contrib 程式庫原始碼檔案並解壓縮：

    $ cd ~
    $ wget -O opencv.zip https://github.com/Itseez/opencv/archive/3.1.0.zip
    $ unzip opencv.zip
    
    $ wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.1.0.zip
    $ unzip opencv_contrib.zip
    
4. 接著來搞定 Python 環境，建議使用 virtualenv + virtualenvwrapper 建立獨立環境，可避免干擾系統相關 Python 程式庫。

    首先先重新手動安裝 pip，為什麼要手動安裝? 因為 raspbian 系統的 pip 版本太舊了!
    
    $ wget https://bootstrap.pypa.io/get-pip.py
    $ sudo python get-pip.py
    
    安裝 virtualenv 與 virtualenvwrapper
    
    $ sudo pip install virtualenv virtualenvwrapper
    $ sudo rm -rf ~/.cache/pip
    
    修改 `~/.profile`，在檔案最後加入下列幾行：
    
    # virtualenv and virtualenvwrapper
    export WORKON_HOME=$HOME/.virtualenvs
    source /usr/local/bin/virtualenvwrapper.sh
    
    想要不登出，重新載入系統環境變數，可用：
    
    $ source ~/.profile
    
    想要使用 python 2 的：
    
    $ mkvirtualenv cv2 -p python2
    
    想要使用 python 3 的：
    
    $ mkvirtualenv cv3 -p python3
    
    上面的 cv2/cv3 是自己取的名稱，方便辨識即可
    
    爾後要切換不同 python 環境，使用 `workon xx` 即可，離開環境則使用 `deactivate` 指令，例如：
    
    切換至名為 cv2 的 python 2 環境：
    
    $ workon cv2
    
    切換至名為 cv3 的 python 3 環境：
    
    $ workon cv3
    
    在系統提示符號前方會有一括號提示現在所處的 virtualenv 環境。
    
5. 開始編譯安裝 OpenCV

    $ cd ~/opencv-3.1.0/
    $ mkdir build
    $ cd build
    $ cmake -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D INSTALL_PYTHON_EXAMPLES=ON \
        -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.1.0/modules \
        -D BUILD_EXAMPLES=ON ..
    $ make -j4
    $ sudo make install
    $ sudo ldconfig

    注意 python 2 與 python 3 環境編譯安裝的 CV2.so 檔名有些不同，python 2 版本安裝於 `/usr/local/lib/python2.7/site-packages/cv2.so`， python 3 則安裝為 `/usr/local/lib/python3.4/site-packages/cv2.cpython-34m.so`
    
    在 virtualenv 環境中建立 cvs.so 檔連結
    
    python 2 環境：
    
    $ cd ~/.virtualenvs/cv/lib/python2.7/site-packages/
    $ ln -s /usr/local/lib/python2.7/site-packages/cv2.so cv2.so
    
    python 3 環境：
    
    $ cd ~/.virtualenvs/cv/lib/python3.4/site-packages/
    $ ln -s /usr/local/lib/python3.4/site-packages/cv2.cpython-34m.so cv2.so

6. 測試

    $ python
    >>> import cv2
    >>> cv2.__version__
    '3.1.0'
    >>>
    
    大功告成!

    
## 安裝 tesseract-ocr 文字辨識程式庫 ###

首先準備編譯工具，部分套件在安裝 OpenCV 時亦會安裝。

    sudo apt-get install autoconf automake libtool
    sudo apt-get install autoconf-archive
    sudo apt-get install pkg-config
    sudo apt-get install libpng12-dev
    sudo apt-get install libjpeg8-dev
    sudo apt-get install libtiff5-dev
    sudo apt-get install zlib1g-dev
    
如果需要安裝辨識訓練工具，另需要下列套件
    
    sudo apt-get install libicu-dev
    sudo apt-get install libpango1.0-dev
    sudo apt-get install libcairo2-dev
    
tesseract-ocr 需要 leptonica 影像演算法套件，先來安裝 leptonica 程式庫。

### 安裝 leptonica ###

2017/02 版本[下載](http://www.leptonica.org/source/leptonica-1.74.1.tar.gz)

下載後解壓縮

    $ tar xvzf leptonica-1.74.1.tar.gz
    $ cd leptonica-1.74.1

兩種安裝方式，擇一使用

1. 使用 autoconf

    ./configure --prefix=/usr/local/
    make
    sudo make install

2. 使用 cmake

    mkdir build
    cd build
    cmake ..
    make
    
## 安裝 tesseract-ocr  ##

    git clone --depth 1 https://github.com/tesseract-ocr/tesseract.git
    cd tesseract
    ./autogen.sh
    ./configure --enable-debug LDFLAGS="-L/usr/local/lib" CFLAGS="-I/usr/local/include" 
    make
    sudo make install
    sudo ldconfig
    
另外需要語言資料檔

可從 https://github.com/tesseract-ocr/tesseract/wiki/Data-Files 下載，存放於 tessdata 目錄
可設定環境變數指定資料檔路徑

    export TESSDATA_PREFIX="/usr/local/share/"

## 安裝 OpenALPR ##

    $ git clone https://github.com/openalpr/openalpr.git
    $ cd openalpr/src
    
    修改 CMakeLists.txt， 加入下列兩行：
    SET (OpenCV_DIR "/home/pi/opencv-3.1.0/build")
    SET (Tesseract_DIR "/home/pi/tesseract")
    
    $ cmake ./
    
    如果出現 lib4cplus 找不到，請 `sudo apt-get install lib4cplus-dev`
    
    $ make

    如果出現 curl.h 找不到，請 `sudo apt-get install libcurl4-openssl-dev`
    
    

