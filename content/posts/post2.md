---
title: "CSSの追加など"
date: 2021-07-30T10:30:39+09:00
draft: false
tags: ["hugo","blog"]
---
# はじめに
前回の記事の中で文字がはみ出てることに気づく
[](strange_style.JPG)
現在はanankeというテーマをそのまま引っ張ってきて利用しているが、ちょっとしたスタイルの変更などをしたいので独自のCSSを追加する。

# CSSの追加
config.toml内のthemeをいじる
```
[params]
    custom_css = ["/css/maki_css.css"]
```
static/cssフォルダに"maki_css.css"を次のように追加  

```css:maki_css.css
code {
word-wrap: break-word;
}
```

# サマリーの長さ調整
今まではホームの記事のサマリーが長すぎたのでconfig.tomlに以下を追加
```
hasCJKLanguage = true
summaryLength = 120
```
文字数カウントをに日本語とかを一文字づつカウントできるようにしてサマリー長さを120字に指定する。

