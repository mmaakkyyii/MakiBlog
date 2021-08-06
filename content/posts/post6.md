---
title: "エラーページの設定"
date: 2021-08-07T04:17:24+09:00
draft: false
categories: ["blog"]
tags: ["blog","hugo","nginx"]
---

# はじめに
デフォルトの設定だとブログの存在しないページにアクセスした際にnginxの404ページが表示されるので、
これをhugo上で生成した404.htmlを表示するようにする。

# nginxの設定
/etc/nginx/sites-available/default に以下を追加
```
server {
    listen 443 ssl;
    ... #省略

     error_page 404 /404.html;
        location = /404.html{
            internal;
        }    
}
```
これでエラーページをデフォルトのページからpublic/404.htmlを表示するようになる。  
またinternal でURLはそのままで404ページを表示するようにする。

参考:[https://www.ncaq.net/2018/04/01/17/54/19/](https://www.ncaq.net/2018/04/01/17/54/19/)

# 404ページの設定
hugoの/layouts/404.html に今回は
``` html
{{ define "main"}}
    <main id="main">
      <center>
       <h1 id="title"><a href="{{ "/" | relURL }}">ホームにもどる</a></h1>
      </center>
    </main>
{{ end }}
```
とした。  
ここに書いておくと同じhtmlファイルがpublic直下に生成される