---
title: "Raspberry Pi PicoでPWMを使う"
date: 2022-04-04T20:03:02+09:00
draft: true
categories: ["組み込み"]
tags: ["RP2040"]
---

# はじめに
[前回](../post25)の続きでPWMとかを使ってみる。

# PWM
RP2040のPWMは30本のGPIOどれでも割り当てることが可能で、16個の独立したPWM出力ができます。

# pwm_config_set_wrap()
wrapはデータシートで言うところのTOPレジスタの値カウンタの最大値