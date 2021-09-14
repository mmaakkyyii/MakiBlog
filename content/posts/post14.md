---
title: "Google Search Consoleにサイトを登録する"
date: 2021-09-15T00:39:32+09:00
draft: false
categories: ["blog"]
tags: ["blog","robots.txt","Google Search Console"]
---

# robots.txtの有効化

config.tomlに
```xml
enableRobotsTXT = true
```
を追加

layout/robots.txtを追加し
```
User-Agent: *

Sitemap: https://blog.mmaakkyyii.com/sitemap.xml

```
とする。ここで必要に応じて、特定のタグのフォルダをDisallowしたりできる。

# Google Search Console
[Google Search Console](https://search.google.com/search-console)にアクセスして、プロパティを追加からURLプレフィックスでブログのURLを入力し続行。
HTMLファイルをダウンロードして(ここではgooglexxxx.html)、そのファイルを\static直下にコピーして、{blogURL}/googlexxxx.htmlにアクセスできるようになっていることを確認し、Google Search Consoleで確認をすると所有権の確認が完了する。

config.tomlのParamsに
```
[Params]
    env="production"
```
を追加する。
これはanankeが生成するヘッダーのbaseof.htmlの12行目らへんに説明があって、これを入れていないとHTMLファイルのメタタグにnoindexというのが入って検索できないようにはじかれてしまう。
これをしていれば、
```html
<meta name="ROBOTS" content="INDEX, FOLLOW">
```
となるはず。
![](../google_search_console.JPG)
これを設定しておけばURLを登録することができる。  
これで検索に引っかかるようになりますように。
