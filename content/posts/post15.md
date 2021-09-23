---
title: "Jetson Nano Developer Kitを買った・セットアップなど"
date: 2021-09-18T12:13:21+09:00
draft: false
categories: ["Jetson Nano"]
tags: ["Jetson Nano"]
---

# はじめに
NVIDIAのJetson Nano Developer Kitを買いました。
![](../jetson_nano.jpg)
# セットアップ
基本的には公式サイトに従う
https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit

必要なものは以下の通り
* Jeston Nano
* SDカード(今回は64GB) https://akizukidenshi.com/catalog/g/gS-15971/
* 5V4AのACアダプタ https://akizukidenshi.com/catalog/g/gM-10660/
* キーボード
* マウス
* HDMIケーブル・ディスプレイ
* USB wifiアダプタ(昔ラズパイで使ってたBuffaloやつ)
* ファン https://www.sengoku.co.jp/mod/sgk_cart/detail.php?code=EEHD-5L6X

# SDカードの準備
* SD card Formatterで使用するSDカードをクイックフォーマットでフォーマットする。
* [Etcher](https://www.balena.io/etcher/)でフォーマットしたSDカードにダウンロードしたOSイメージのzipファイルを読み込んで書き込む。

# スワップ領域の変更

```
free -m
```
これでスワップ領域のサイズを確認する。確認すると1GBだったのでこれを4GBに変更する。

```
sudo fallocate -l 4G /mnt/4GB.swap
sudo chmod 600 /mnt/4GB.swap
sudo mkswap /mnt/4GB.swap
sudo vim /etc/fstab #これで最後の行に /mnt/4GB.swap swap swap defaults 0 0 を追加
sudo reboot
```
これでスワップ領域が4GBになった


# ファンの制御
千石に売っているJetson Nano用のファンをセットする。付属のねじがあるのでこれ単体でJetson Nanoのヒートシンクに固定できる。  
https://www.sengoku.co.jp/mod/sgk_cart/detail.php?code=EEHD-5L6X

![](../jetson_nano_with_fan.jpg)
これをjetson-fan-ctlというものを使って制御する。

## はじめに
```
sudo sh -c ‘echo 255 > /sys/devices/pwm-fan/target_pwm’
```
これで0-255までの値を入れればPWMによって回転数が変わる。

## jtop
CPUとかの使用率などを見るためのコマンドのインストール
```
sudo apt install python-pip
sudo -H pip install jetson-stats
```
これでインストール完了
```
sudo jtop
```
で実行できる

## jetson-fan-ctl
https://github.com/Pyrestone/jetson-fan-ctl
というものがあるみたい
```
git clone https://github.com/Pyrestone/jetson-fan-ctl.git
```
でインストールして、/etc/automagic-fan/config.json　で設定が変えられる
```
sudo service automagic-fan restart
```
これで開始する

# JETSON AI COURSES AND CERTIFICATIONS
https://developer.nvidia.com/embedded/learn/jetson-ai-certification-programs#course_outline
公式のチュートリアルのようなやつをやってみた。
特に問題なくできた。(それはそう)
dockerの環境を持ってくるとほかPCからJupyterLabにアクセスできるようになってAIぽいことをやるチュートリアルがあった。
とりあえずサンプルコードはあって実行できるなぁというかんじ。めちゃめちゃ熱くなるのでファンがないとヤバい感じになるけど、ファンは必須っぽい感じがする。


# その他メモ
# 自動スリープ動作変更
Settingアイコンから
## Power
何もしない状態でのサスペンド設定
## Brightness & Lock
何もしない状態での画面のロック設定