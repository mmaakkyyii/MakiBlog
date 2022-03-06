---
title: "オタクはオタク基板を作りがち"
date: 2021-11-13T23:06:33+09:00
draft: false
categories: ["その他"]
tags: ["その他"]
---

オタクはそれぞれできる技術で様々なものを表現する。  
「長野佑紀と河野ひよりのあんどもぁぱちぱち空想部」通称もぁぱちの参加型企画においてもそれは同じである。
{{<tweet 1458268375881224194>}}
#もぁぱち自由 それは自由に何をやっても許されるということ。

まず基板を設計します。外形はもぁぱちのマスコットキャラであるところのもぁぱちちゃんです。  
回路は単純にUSB給電で動く光ゲーミングもぁぱちちゃんです。  
とりあえずフルカラーLEDで光るものを作っておけばいいという考えだ…

Fusionでまず外形になる3Dモデルを作って、その外形をそのままFusionの回路の外形にインポートしてそのうえでEAGLEのノリで回路を作る。中身はSTM32G031とWS2812C-2020光らせるだけなのでそれ以外はない本当に最低限の構成。5Vをマイコン用の3.3Vにするリニアレギュレータがあるだけ。
{{<tweet 1453057840365457416>}}
時間が無かったので大学の基板切削機で切削して基板作製。結構複雑な外形でも普通にきれたね。
{{<tweet 1457564722350288899>}}
{{<tweet 1458135222101233664>}}

最終的にできたのはこれ
{{<tweet 1458134037264941060>}}

# わ～い
ステッカーを貰った
{{<tweet 1460945303670984716>}}
以上！お疲れ様でした！！