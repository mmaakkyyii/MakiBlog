---
title: "STM32CubeIDEのファームウェアのダウンロード場所を変更する"
date: 2022-03-07T17:16:42+09:00
draft: false
categories: ["組み込み"]
tags: ["STM32","STM32CubeIDE"]
---

# はじめに
STM32CubeIDEのファームウェアがCドライブを圧迫しているのでHDDの方に移動させたくなったので、設定を変えたときの備忘録
![](../img/cubeIDE_fw_rep.JPG)
10GB以上とっててSSDがヤバい
これを容量の大きいHDDの方に移す

# 方法
Window->Prefernces->STM32Cube->Firmware Update  
よりFirmware installation repositoryでインストール場所を指定する。

![](../img/fw_path.JPG)

ファームウェアがダウンロードされていなければ指定したフォルダにダウンロードされて利用できるようになる。

# 参考
https://community.st.com/s/question/0D53W00000ORQOvSAP/how-to-change-stm32cubemx-firmware-repositort-folder-
