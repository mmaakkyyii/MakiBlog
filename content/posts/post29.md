---
title: "Raspberry Pi PicoのPIOでロータリエンコーダを読む"
date: 2022-05-03T13:41:34+09:00
draft: true
categories: ["組み込み"]
tags: ["RP2040","PIO"]
---

# はじめに
RP2040ではPIO(Programmable Input/Output)というものがあっていい感じにIOピンの挙動をプログラムすることができる。  
RP2040にはエンコーダの入力をいい感じにカウントしれくれるような機能はないのでここで実装する必要がある。  

それらしいライブラリをみつけたのでこれを使ってみる。
https://github.com/GitJer/Some_RPI-Pico_stuff/tree/main/Rotary_encoder


# 文字コード
.pioファイルはSHITF-JISで改行コードをLFにしないとビルド時に怒られた  

はい

# 使ってみた
特に何もせず[自分の環境](/posts/post25/)でも動くことを確認した。
ただこのライブラリはエンコーダ一つ分しか見れない(PIO0のみを使うようになっていたりステートマシンも固定のものを使っている感じがする)

# 中身を理解する
詳しくは[データシート](https://datasheets.raspberrypi.com/rp2040/rp2040-datasheet.pdf)のChapter 3. PIOを見る
