---
title: "STM32でUDP通信 on Jetson Nano"
date: 2021-09-23T15:50:52+09:00
draft: true
categories: ["組み込み"]
tags: ["Jetson Nano","STM32"]
---

# はじめに
タイトルの通り、以前の[STM32マイコンとPC間でUDP通信をする
](https://blog.mmaakkyyii.com/posts/post12/)  
はWindowsPCとマイコン間の通信だったものを、前の記事で導入したJetson Nano上でも同様に行う

# やること
通信用のPythonのプログラム自体も前回と同じsocket_test.pyだが、ipconfigでマイコンと接続してもIPアドレスが与えられていないので
```
sudo ipconfig eth0 169.254.137.86
```
これでipconfigを打つとIPアドレスの指定ができる。
これでIPアドレスが指定されていればsocket_test.pyを実行できる(データの受信ができるようになる)

windowsだと最初にsendしないと行けなかったのが、ubuntu上だと送らなくても正しく通信ができる
