---
layout: "post"
title: "Grafana 安裝程序"
date: "2016-10-15 19:29"
---

# 透過套件管理程式安裝 Grafana #

## CentOS ##
直接安裝最新穩定版

    $ sudo yum install https://grafanarel.s3.amazonaws.com/builds/grafana-3.1.1-1470047149.x86_64.rpm

或者將 grafana 加入套件庫清單，
建立 `/etc/yum.repos.d/grafana.repo` 檔案，爾後便可使用 `$sudo yum install grafana` 命令直接安裝，內容如下：

    [grafana]
    name=grafana
    baseurl=https://packagecloud.io/grafana/stable/el/6/$basearch
    repo_gpgcheck=1
    enabled=1
    gpgcheck=1
    gpgkey=https://packagecloud.io/gpg.key https://grafanarel.s3.amazonaws.com/RPM-GPG-KEY-grafana
    sslverify=1
    sslcacert=/etc/pki/tls/certs/ca-bundle.crt

### 套件安裝細節說明如下： ###

- 執行檔安裝於 `/usr/sbin/grafana-server`
- 建立起動程序檔 `/etc/inint.d/grafana-server`
- 安裝預設環境變數檔 `/etc/sysconfig/grafana-server`
- 建立預設組態設定檔 `/etc/grafana/grafana.ini`
- 安裝 systemd 服務(若系統使用systemd) `grafana-server.service`
- 預設記錄檔路徑為 `/var/log/grafana/grafana.log`
- 預設 grafana 使用 `sqlite3` 資料庫，檔案位置在 `/var/lib/grafana/grafana.db`，當要升級 grafana 版本時記得先備份此檔案。 Grafana 亦支援使用 MySQL 或 Postgres 作為資料庫。


#### 啟動方式： ####

**inid.d service**

    $ sudo service grafana-server start

    設定開機自動執行

    $ sudo /sbin/chkconfig --add grafana-server


**systemd**

    $ systemctl daemon-reload
    $ systemctl start grafana-server
    $ systemctl status grafana-server

    設定開機自動執行

    $ sudo systemctl enable grafana-server.service

不管是使用 `init.d` 或是 `systemd` 方式啟動，皆會讀取 `/etc/default/grafana-server` 設定環境變數。

系統會以名為 `grafana` 的使用者來啟動 `grafana-server` 程序，網頁介面預設使用 HTTP port `3000`，預設使用者為 `admin`

在 CentOS 7 中，開啟防火牆

    $ firewall-cmd --permanent --add-port=3000/tcp

另外還有 SELinux 的問題，無法連線時先檢查

    $ sestatus          # 查看 selinux 狀態
    $ setenforce 0      # 暫時使用 permissive 模式



## Debian/Ubuntu ##
直接安裝最新穩定版

    $ wget https://grafanarel.s3.amazonaws.com/builds/grafana-3.1.1-1470047149_amd64.deb
    $ sudo apt-get install -y adduser libfontconfig
    $ sudo dpkg -i grafana_3.1.1-1470047149_amd64.deb


或者將 grafana 加入套件庫清單，
建立 `/etc/apt/sources.list` 檔案，內容如下：

    deb https://packagecloud.io/grafana/stable/debian/ wheezy main

    如果想使用開發版本:
    deb https://packagecloud.io/grafana/testing/debian/ wheezy main

    加入 Package Cloud key，允許安裝已簽署套件
    $ curl https://packagecloud.io/gpg.key | sudo apt-key add -

    使用下列命令安裝:
    $ sudo apt-get update
    $ sudo apt-get install grafana

    版本較舊的 Debian/Ubuntu 可能需要加裝這個套件才能透過 HTTPS 取得套件：
    $ sudo apt-get install -y apt-transport-https

### 套件安裝細節說明如下： ###

- 執行檔安裝於 `/usr/sbin/grafana-server`
- 建立起動程序檔 `/etc/inint.d/grafana-server`
- 安裝預設環境變數檔 `/etc/default/grafana-server`
- 建立預設組態設定檔 `/etc/grafana/grafana.ini`
- 安裝 systemd 服務(若系統使用systemd) `grafana-server.service`
- 預設記錄檔路徑為 `/var/log/grafana/grafana.log`
- 預設 grafana 使用 sqlite3 資料庫，檔案位置在 `/var/lib/grafana/grafana.db`，當要升級 grafana 版本時記得先備份此檔案。 Grafana 亦支援使用 MySQL 或 Postgres 作為資料庫。


#### 啟動方式： ####

**inid.d service**

    $ sudo service grafana-server start

    設定開機自動執行

    $ sudo update-rc.d grafana-server defaults 95 10


**systemd**

    $ systemctl daemon-reload
    $ systemctl start grafana-server
    $ systemctl status grafana-server

    設定開機自動執行

    $ sudo systemctl enable grafana-server.service
