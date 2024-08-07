---
title: "マイクロマウス関西地区大会に参加した"
date: 2024-07-21T20:27:55+09:00
draft: false
categories: ["マイクロマウス","イベント"]
tags: ["マイクロマウス","ロボコン"]
---

どうも、マッキーです。  
マイクロマウスをやっていきます。

去年までは[社内研修](https://rt-net.jp/mobility/archives/category/original/makihara-originalmouse)としてマイクロマウスをやっていましたが、今年からは趣味でマイクロマウスをやっていきます。

今回は関西地区大会に出てきたのでそのレポートです。
![](../img/kansai_2024.jpeg)
## 目的
今年度もマイクロマウスをやるモチベーションは、研修でやり残したことがあってそれらを回収していきたいということです。
研修では自作マウスを作って完走するところまではやりましたが、これは本当に最低限の機能を作ったにすぎず、高速化にはほぼ向き合ってこなかったです。シンプルに速度・加速度をそこまで出していないところや、斜め走行、32x32の完走などなどまだやり残したことがたくさんあるのでここら辺をやっていきたいと思っています。それに伴って必要なものがいくつもあるのでいろいろやっていきたいです。(小並感)

## コンセプト
研修で製作したマウスに引き続き非吸引2輪マウスです。
最終的に上位のマウスを見ていくと最終的に加速度を出そうとすると必要になってくるのは分かるのですが、それより前にシンプルな機体での限界がどんなものなのか見ておきたいのでしばらくはこの形で行こうと思います。
細かくは他にもいろいろあるので気が向いたら書きます。(一生書かなそう)

## 製作したマウス
こちらはマッキーマウスv2.0です。去年研修で作ったマッキーマウスがv1.0として、そこから新しく作り直したのでv2.0になっています。
![](../img/mmaakkyyii_mouse_v2_0.jpeg)  
[メカ](https://a360.co/3A1gm63)  
[基板](https://github.com/mmaakkyyii/makyi_mouse_v2)  
[ソフト](https://github.com/mmaakkyyii/makyi_mouse_FW)  

細かい解説は気が向いたら書くかもしれないです。

## 大会について
上で述べた32x32の迷路は地区大会で計3ポイント以上取る必要があります。そのため地区大会には積極的に参加していこうと思っています。ということで今回の関西地区にも参加しています。
### 前日
前日は会社の方の車に乗せていただいて移動したのですが、いろいろあって到着が遅れてしまい1時間くらいしか調整の時間が取れませんでした。会場で本番用の迷路台で走らせて見ると基本的には走れていましたが、台のつなぎ目で引っかかるということがはっせいしました。車高を低くしすぎたため、段差があるとタイヤが接地しなくなってしまいました。応急処置として、ホイールに両面テープを貼ってタイヤ径を大きくして対処しました。タイヤ径変更のためソフト側でもパラメータ調整をしたらほぼ問題なく動くようになりました。
![](../img/OECU_2024.jpg)

### 当日
結果は以下の通りです  
1 1:02:549   
2 0:22:550  
3 **0:20:943**  
4 0:21:930  
5 R  

[【結果】マイクロマウス競技](https://mmk.rulez.jp/?p=2886)  
順位は10位でしたが、タイムを見るとかなり壁を感じます。  
走行は6:56~から{{<youtube eUtQLUyBBAQ>}}

1走目は探索で特に怪しいところもなく走ってくれました。2~4走目の最短走行も最大速度700mm/s(多分)でも問題なく走ってくれて用意していたパラメータの最大まで走らました。最後の5走目は一応作っていた斜めありの走行を試してみましたが、斜めの一発目で柱に当たってリタイヤでした。

調整時間がなくて斜めをあまり試せなかったことと、最短走行についてはもう少し加速度を上げたパラメータを追加しておけばよかったと思いました。


## 完走した感想
とりあえず新作を作って完走出来たのはよかったです。今まで作っていたものがベースでしたが、大体去年の全国大会直後あたりから着手していたので大体5か月くらいで作ったことになります。ハードは大体7月頭位に完成して1か月くらいソフトをいじっていたという感じです。

前作ではすごく硬くなってしまっていた足回りが、今回改善された点はよかったです。ただエンコーダ周りがあまりきれいに軸合わせができなくなっていたため、そこのフィルタをかけたり調整したりと時間がとられてしまいました。ここはもう少し改善の余地がありそうなので着手していきたいです。  
また車高を低くしすぎて段差にひかかるというかなり致命的な課題を抱えていることが判明したのでこちらは修正したいと思います。

ソフト的には斜め走行用のコードももう少し追加していきたいです。またその前に壁切れの実装も必要そうなのでそこら辺を追加していきたいです。
