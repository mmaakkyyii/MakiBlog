---
title: "自分用チートシート"
date: 2022-05-29T00:00:00+09:00
draft: false
---
目次
<!-- TOC -->

- [マークダウン](#%E3%83%9E%E3%83%BC%E3%82%AF%E3%83%80%E3%82%A6%E3%83%B3)
    - [表](#%E8%A1%A8)
    - [Tweet](#tweet)
    - [Youtube](#youtube)

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

```
{{</* tweet 1466957251437301761 */>}}
```

{{<tweet 1466957251437301761>}}

## Youtube
```
{{</*youtube FbBgSed-JW4*/>}}
```
{{<youtube FbBgSed-JW4>}}

