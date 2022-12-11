---
title: "ANYCUBIC Mega Zero 2.0のビルドプレートを取り換える"
date: 2022-05-15T10:47:19+09:00
draft: false
categories: ["3D printer"]
tags: ["3D printer","ANYCUBIC Mega Zero 2.0"]
---

3Dプリンターのビルドプレートが壊れたので取り換えた際の記録をしておきます。

久しぶりにABSを使おうと思ってステージ温度を110℃まで上げてプリントしようとして、プリント終了後にステージの温度が高いときにビルドプレートをはがそうとしたところ下の画像のようにマグネット部分が剥がれてしまった。↓
![](../img/3d-printer_broken_plate.jpg)

ビルドプレートはANYCUBIC Mega Zero 2.0標準のもので、見てみるとビルドプレートに両面テープのようなもので磁石のシートがくっついているような構造になっていました。  
3Dプリンタ的には110℃は最大値で使っていたことや温かくてテープの粘着力が落ちた状態で力をいれてしまったのがいけなかったのかもしれないです。
![](../img/3d-printer_stage.jpg)
ステージにもボロボロになったマグネットプレートがくっついていたのでガリガリ剝がした。

裏面がボロボロになってしまったので新しいビルドプレートを買おうと思ったのですが、日本でMega Zero用のビルドプレート売ってないので他の物を使ってみます。(AmazonのANYCUBICショップにもなかったし、公式ショップでも日本には発送できないようでした)

今回はEnderのマグネットプレートを使ってみます。↓
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=mmaakkyyii-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B085NK3KQ8&linkId=d081d0db65fa41ddbd8e341bfa408b7d"></iframe>

![](../img/3d-printer_new_plate.jpg)
横幅が235 mmと説明が書いてあって純正のものと同じだと思っていたのですが、実際はもう少し幅が大きかったのでカッターで5 mm切りました。  
こちらの新しいプレートはビルドプレートと磁石のシートの2枚がついていたのですが、Mega Zeroのステージはもとのも磁石にくっつくようになっています。Enderの磁石プレートを使わないとMega Zero純正より少し磁力が弱く感じますが、問題なく使えました。
![](../img/3d-printer_printing.jpg)

と思ったけど小さい部品なら問題なく出力できるけど大きい部品だとやっぱりプレートごと反ってしまいました。
