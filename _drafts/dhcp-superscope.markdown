---
layout: post
title: "DHCP Superscope"
---

##Superscope##

Windows server 2008 DHCP Server 的管理工具

- 支援在單一實體網路中使用多重邏輯IP區段(multinets)
- 支援遠端 DHCP 與 BOOTP relay agents

在 multinet 組態環境下，可使用 DHCP superscopes 來將個別 IP 位址範圍來分組與啟用。

幾種使用狀況:
- 目前使用中的位址範圍已用盡，需要加入更多電腦。
- 準備將用戶端電腦轉移到新的 IP 位址範圍。
- 想要在單一實體網路中使用兩部 DHCP 伺服器來管理不同的邏輯 IP 網路。
 
**Example 1:**

![Single Scope](https://i-technet.sec.s-msft.com/dynimg/IC292472.gif)

**Example 2:**

![Superscope](https://i-technet.sec.s-msft.com/dynimg/IC196411.gif)

在網路 A 中包含了多個子網路，可設定superscope 除了為原本範圍(scope1)的成員，還可支援其他的邏輯子網路(scope 2 與 scope 3)。

**Example 3:**

![Superscope for routed DHCP server](https://i-technet.sec.s-msft.com/dynimg/IC196412.gif)

透過組態為 DHCP relay agent 功能的 Router，可提供遠端網路 Subnet B DHCP 服務。
此例中 multinets 環境在遠端網路(Subnet B)，原來的 scope1 不需要加入 superscope。
