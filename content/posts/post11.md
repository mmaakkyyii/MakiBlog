---
title: "hugoでマークダウン中にHTMLタグ等を使う"
date: 2021-08-31T22:34:10+09:00
draft: false
categories: ["blog"]
tags: ["blog","hugo"]
---

# はじめに
デフォルトのままだとhugoのブログのmarkdown中にHTMLタグを入れても無視されてしまうのでこれを何とかしてHTMLタグを使いたい ~~(YouTubeの埋め込みとかいろいろできるようにしたいので)~~ Youtubeの埋め込みは[shortcodeでできるらしい](/my_cheat_sheet/#youtube)

# HTMLタグの有効化
config.tomlに
```
[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true
```
を追加する

https://gohugo.io/getting-started/configuration-markup#goldmark  
より  
### unsafe 
By default, Goldmark does not render raw HTMLs and potentially dangerous links. If you have lots of inline HTML and/or JavaScript, you may need to turn this on.

らしくデフォルトではHTMLが有効になっていないのでこれを追加した

<marquee height="50" width="500" behavior="alternate" direction="up" scrollamount="40" truespeed> >これでHTMLタグが使える！！！< </marquee>

