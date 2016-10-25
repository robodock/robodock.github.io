---
layout: "post"
title: "influxDB 備份與回復"
date: "2016-10-15 19:48"
---

# influxDB 備份與回復 #

- influxDB 具備對某個時間點實施**"快照"**與回存的能力。
- influxDB 的備份接為完全備份，目前尚不支援**"增量備份"**。

有兩種資料型式需要備份

- **metastore** : 資料庫的系統資訊，一次備份整個系統資訊。
- **metrics** : 資料內容，依各個資料庫分別備份。

> 註：開源版的 OSS influxDB 與 企業版的 InfluxEnterprise 資料庫備份無法互通使用

## Metastore 備份 ##

influxDB metastore 包含系統狀態、使用者資訊、資料庫、標記資料等等資訊。當節點執行時，可執行底下命令進行備份：

    influxd backup <path-to-backup>

命令未加入任何參數時，備份目前狀態

	$ influxd backup /tmp/backup
	2016/02/01 17:15:03 backing up metastore to /tmp/backup/meta.00
	2016/02/01 17:15:03 backup complete

## Database 備份 ##

**每個資料需要分開單獨備份**

加入 `-database` 參數來備份資料庫：

	influxd backup -database <mydatabase> <path-to-backup>

其他參數選項：

- `-retention <retention policy name>` : 使用特定的資料庫保留策略來進行備份
- `-shard <shard ID>` : 使用特定的 shard ID 進行備份
- `-since <data>` : 指定備份特定的時間起的資料，時間戳記需使用 RFC3339 格式，(例如 **`2015-12-24T08:12:23Z`**)，使用指定時間的方式可間接達成增量備份的目的。

> 註：Database 的備份中同時也會包含 Metastore 資料備份

範例：針對名為 `telegraf` 的資料庫，使用 `autogen` 策略，從 2016/2/1 起進行

	$ influxd backup -database telegraf -retention autogen -since 2016-02-01T00:00:00Z /tmp/backup
	2016/02/01 18:02:36 backing up rp=default since 2016-02-01 00:00:00 +0000 UTC
	2016/02/01 18:02:36 backing up metastore to /tmp/backup/meta.01
	2016/02/01 18:02:36 backing up db=telegraf rp=default shard=2 to /tmp/backup/telegraf.default.00002.01 since 2016-02-01 00:00:00 +0000 UTC
	2016/02/01 18:02:36 backup complete

## 遠端備份 ##
使用 `-host` 參數，指定遠端主機位址與埠號，可對遠端主機進行備份：

	$ influxd backup -database mydatabase -host 10.0.0.1:8088 /tmp/mysnapshot


## 資料庫回復 ##

使用 `influxd restore` 命令來復原資料庫。

> **註：只有在資料庫 daemon 停止執行時，才能進行資料庫回復動作。**

復原資料庫時，須指定備份型式，資料庫位置與備份檔所在位置：

	influxd restore [ -metadir | -datadir ] <path-to-meta-or-data-directory> <path-to-backup>

- `-metadir <path-to-meta-directory>` 如果使用來自系統套件安裝方式，資料庫 metastore 位置應該在 `/var/lib/influxdb/meta`
- `-datadir <path-to-data-directory>` 如果使用來自系統套件安裝方式，資料庫 metastore 位置應該在 `/var/lib/influxdb/data`

其他回復資料庫參數：

- `-database <database>` 指定要回復的資料庫名稱，如果回復的資料型式為 data(亦即指定 `-datadir`參數後)，此項參數為必要項目。
- `-retention <retention policy>` 指定特定的保留策略。
- `-shard <shard id>` 指定回復特定的 shard data，須配合 `-database` 與 `-retention` 參數使用。


資料庫回復範例：

首先必須復原 metastore 資料，influxDB 才知道有哪些資料庫存在。

	$ influxd restore -metadir /var/lib/influxdb/meta /tmp/backup
	Using metastore snapshot: /tmp/backup/meta.00

metastore 資料復原後，再進行資料部分復原，以前述的 `telegraf` 資料庫例子：

	$ influxd restore -database telegraf -datadir /var/lib/influxdb/data /tmp/backup                                                                         
	Restoring from backup /tmp/backup/telegraf.*
	unpacking /var/lib/influxdb/data/telegraf/default/2/000000004-000000003.tsm
	unpacking /var/lib/influxdb/data/telegraf/default/2/000000005-000000001.tsm

> 當資料庫復原後，shards 上的權限可能不正確，可使用下列命令，確保檔案權限正確：
>
> `$ sudo chown -R influxdb:influxdb /var/lib/influxdb`

資料庫復原動作完成後，重新啟動資料庫

	$ service influxdb start

可簡單快速執行資料庫查詢命令 `show database`，確定資料庫回復成功：

	influx -execute 'show databases'
	name: databases
	---------------
	name
	_internal
	telegraf
