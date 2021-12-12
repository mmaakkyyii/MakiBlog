---
title: "ANYCUBIC Mega Zero 2.0ダイレクトエクストルーダ化とHPフィラメント スーパーフレキシブルタイプ"
date: 2021-12-09T03:38:36+09:00
draft: false
categories: ["3D printer"]
tags: ["3D-printer","ANYCUBIC Mega Zero 2.0","HPフィラメント スーパーフレキシブルタイプ"]
---

# はじめに
[麦茶さん](https://www.youtube.com/channel/UCn23JisBcbb-Nw-3IhofaNA)という方のyoutubeでプレゼント企画というのがあって応募してみたらフィラメントが当たりました。ありがとうございます！

当たったのはホッティーポリマーさんの[HPフィラメント スーパーフレキシブルタイプ](https://www.hotty.co.jp/products/hottypost_17/)という非常に柔らかいフィラメントです。このフィラメントはめちゃくちゃ柔らかいくグリップもあるようなのでいろいろできそうです。

<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="https://rcm-fe.amazon-adsystem.com/e/cm?ref=qf_sp_asin_til&t=mmaakkyyii-22&m=amazon&o=9&p=8&l=as1&IS1=1&detail=1&asins=B08594DV9Y&linkId=21d93aba87c4e73849691924fb5afe41&bc1=FFFFFF&amp;lt1=_top&fc1=333333&lc1=0066C0&bg1=FFFFFF&f=ifr">
</iframe>

# 3Dプリンターの方式について
現在私が利用しているのはANYCUBIC Mega Zero 2.0です。この3Dプリンターはボーデン式と言われるエクストルーダとフィラメント送り出し用のモータが長いチューブで繋がれているものです。
<iframe style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="https://rcm-fe.amazon-adsystem.com/e/cm?ref=qf_sp_asin_til&t=mmaakkyyii-22&m=amazon&o=9&p=8&l=as1&IS1=1&detail=1&asins=B08RYSWLTJ&linkId=c87e82de877cca5008ca0ad2dd410deb&bc1=ffffff&amp;lt1=_top&fc1=333333&lc1=0066c0&bg1=ffffff&f=ifr">
</iframe>

今回利用するHPフィラメントは非常に柔らかいため、ダイレクトエクストルーダが推奨されています。
そこで今回はMega Zeroのダイレクトエクストルーダ化を行います。

# ダイレクトエクストルーダ化
調べてみるとMega Zeroをダイレクトエクストルーダ化するための3Dプリントパーツが公開されていたのでこちらを利用します。  
[https://www.thingiverse.com/thing:4841439](https://www.thingiverse.com/thing:4841439)
![](../MegaZeroDD.jpg)  
印刷して取り付けた写真がこちら。

もともとは右奥の方にあったモータがエクストルーダ付近に取り付けられるようになっています。  
この改造パーツはフィラメント押し出しユニットのフィラメントを押し出す方向が通常と逆になっているので、デフォルトで付いているケーブルを使いまわすと、フィラメントの押し出し動作が引き戻し動作になってしまいます。なので、ケーブルの延長も含めて新しいいケーブルを自作しました。可動範囲まで伸びる長さを確保することと、ステッピングモータの回転が逆回転になるようにケールブを作りました。  
![](../MegaZeroDD2.jpg)  
ダイレクトエクストルーダ化して動かしている様子がこちらです。

これでHPフィラメントが使えそうです。

# HPフィラメントの印刷
フィラメントの推奨設定は、ヘッド温度200~240℃ 速度は20 mm/s以下 プラットフォームは0℃です。  
またノズル径は0.4 mmでもできますが、それより大きい径の方がスムーズに印刷できるということをホッティーポリマーの方から教えていただきました。

自分の環境で利用した設定はヘッド温度230℃ 速度は15 mm/s プラットフォームは110℃(間違えてた...)でした。ノズル径は0.8 mmのものを利用しました。
また専用のビルドテープが付属されていたのでこちらをプラットフォームに貼り付けて印刷しました。手元にあった普通の養生テープでも一応印刷できました。

推奨設定でダイレクトエクストルーダ化・ノズル径を大きくしたためかあまり細かい調整をすることなく印刷することができました。また今回はサポート材を必要とするものについてはまだ試していません。

# できたもの
この動画は以前ANYCUBIC純正のTPUフィラメントで作製したベルトのモデルをHPフィラメントで作り直してみました。
左はHPフィラメント右はTPUフィラメントです。
<iframe width="560" height="315" src="https://www.youtube.com/embed/ZYMMOiDh5T8" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

TPUフィラメントも柔らかい材質ではあるのですが、HPフィラメントはさらに柔らかくお互いを押し付けるとHPフィラメントのほうが大きく変形していることが分かります。またグリップについても明らかにHPフィラメントの方がグリップ力が出ていました。

# まとめ
* ANYCUBIC Mega Zero 2.0のダイレクトエクストルーダ化をした
もともとは必要なかったが、ダイレクトエクストルーダが推奨されるフィラメントのためには有効だと思われます。
* HPフィラメントを使ってみた
ダイレクトエクストルーダの3Dプリンターやノズル径の調整によりスムーズにフィラメントの出射や造形ができました。