---
title: "自分用チートシート"
date: 2022-05-29T00:00:00+09:00
draft: false
---
目次
<!-- TOC -->

- [マークダウン](#マークダウン)
  - [表](#表)
  - [Tweet](#tweet)
  - [Youtube](#youtube)
- [SSL証明書](#ssl証明書)
  - [証明書切れたとき](#証明書切れたとき)

<!-- /TOC -->

# マークダウン
## 表
```
|1|2|3|
|---|---|---|
|あ|い|う|
|ああ|いい|うう|
```
|1|2|3|
|---|---|---|
|あ|い|う|
|ああ|いい|うう|

## Tweet
hugo 特有の[Shortcode](https://gohugo.io/content-management/shortcodes/)というのがあるらしい  
ちなみにこれを変換しないようにするためには  
``` {{</*/*yourshortcode*/*/>}} ```とすればよい([参考](https://discourse.gohugo.io/t/solved-how-to-make-hugo-ignore-shortcode-delimiters-e-g-when-used-in-code-blocks/6045))

2023/06/06追記
https://blog.foresta.me/posts/fix-tweet-shortcode-warn-hugo-v0_91_0/

```
{{</* tweet user="mmaakkyyii" id="1466957251437301761" */>}}
```

{{<tweet user="mmaakkyyii" id="1466957251437301761">}}

## Youtube
```
{{</*youtube FbBgSed-JW4*/>}}
```
{{<youtube FbBgSed-JW4>}}

# SSL証明書
## 証明書切れたとき
```
sudo certbot renew --force-renew  
```