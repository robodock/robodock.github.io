---
layout: post
title: "Naze32+CleanFlight PID Setting"
---

#PIDs#

##P##

P 代表修正的強度，應用於讓飛機到達設定角度或迴轉率。
如果 Ｐ 太低，飛機會顯得難以控制，同時無法快速的反應來讓飛機維持穩定。
如果 Ｐ 太高，飛機會因連續的 overshoots（超越設定值） 而快速震盪/搖擺。

## I ##

I 修正過去累積的誤差。
如果 I 值太低，飛機的高度會緩慢的飄移。
如果 I 值太高，飛機亦會呈現震盪/搖擺現象（但反應不會像 P 值的效果來的強烈）。

## D ##

D 監測誤差的變化率，如果誤差值正快速收斂到零，D 會減少修正的強度來避免 overshooting。


## TPA 與 TPA Breakpoint

TPA : Throttle PID Attenuation
設定當油門增加時，適度的減小 PID 強度，以減低因油門加大帶來的 PID 效果加強效應。

