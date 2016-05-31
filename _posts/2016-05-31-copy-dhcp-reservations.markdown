---
layout: post
title: "複製 DHCP 保留位址到其他 DHCP 伺服器上"
date: 2016-05-31T10:03:13+08:00
---

在 **Windows AD Domain** 中，通常也會架設多部之 **DHCP** 伺服器作為備援，避免單一 DHCP 伺服器失效後，工作站無法取得 IP 位址的問題。然而截至 **Windows Server 2008 R2** 版本， DHCP 伺服器尚未支援多部 DHCP 伺服器自動同步設定功能，特別是 DHCP 保留位址，必須在每部 DHCP 伺服器上輸入，底下是個變通的方法，可將設定好的 DHCP 保留位址匯出到其他 DHCP 伺服器使用。

在**來源 DHCP 伺服器**上，執行下列命令：

	netsh dhcp server <伺服器IP> scope <scope 位址> dump>dump.txt

打開 dump.txt 檔，檔案中可找到如下格式之保留設定，複製這些行到新增檔案，將 Server.domain.com 替換為目標伺服器之 IP Address 或 server name。

	Dhcp Server \\Server.domain.com Scope 192.168.0.0 Add reservedip 192.168.0.250 <<MAC Address>> "DeviceName.domain.com" "" "BOTH"

在**目的地 DHCP 伺服器**上執行下列命令，import 進檔案：

	NETSH exec <<Filename>>