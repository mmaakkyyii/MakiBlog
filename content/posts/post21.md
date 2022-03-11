---
title: "STM32のNRSTピンをGPIOとして使う NRST_MODEについて"
date: 2022-03-12T00:40:38+09:00
draft: false
categories: ["組み込み"]
tags: ["STM32"]
---

# はじめに
最近8ピンで秋月電子でも売っている[STM32G031](https://akizukidenshi.com/catalog/g/gI-15688/)をよく使っている。  
![](../img/stm32g031_ds_pinout.JPG)  
NRSTピンと他のPA0/PA1/PA2が共存しているので、例えばPA0でGPIOをLOWにするとこの時点でNRSTがLOWになるためマイコンにリセットがかかって所望の動きをしてくれない。

具体的には4番ピンをCubeMXでPA0のGPIO_Outputに設定した際に、コンパイルも書き込みも出来たが、実際にプログラムを走らせるとGPIOの設定をする部分で(おそらくデフォルトがLOWなので)でNRSTピンがLOWになりマイコンのリセットがかかった。  

そのためNRST_MODEを変更する必要がある。

# NRSTピンについて
## 内部プルアップ抵抗
NRSTピンにはプルアップ抵抗がIC内部で接続されてて約40 kΩの抵抗がついている。(DS12992 Rev2 Table 55)

## NRST_MODE
NRST_MODEはリセットピンの挙動を指定するもので、次のようなモードがあります。
|NRST_MODE|description|
|----|----|
|0|予約済み|
|1|リセット入力のみ受け付ける|
|2|GPIOポート利用可能 内部リセットのみを受け付ける|
|3(デフォルト)|入出力でリセット|

これについてはRM0444 3.4 FLASH option bytesあたりを参照するといろいろ書いてある。  
## NRST_MODEの書き換え
このNRST_MODEの書き換えはSTM32CubeProgrmmerを用いる。
![](../img/nRST_setting.JPG)
マイコンをST-LINK等で接続した上で左の[OB]アイコンからOption bytesのページを開く。  
その中のUSER Configuration->NRST_MODEの数値を変更し、ApplyボタンをクリックするとNRST_MODEを変更できる。  
またReadボタンを押せば現在書き込まれている値を確認することもできる。

これによってNRST_MODEを2にすると通常のGPIOピンとして利用することができる。
