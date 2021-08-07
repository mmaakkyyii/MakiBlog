---
title: "ブログ用にサブドメインを使う"
date: 2021-08-07T15:14:59+09:00
draft: false
categories: ["blog"]
tags: ["blog","nginx"]
---
# はじめに
現状はmmaakkyyii.comをブログ用に利用しているけど、他の用途にも使うかもしれないのでサブドメイン上でblogを利用することにする。

# nginxの設定
こちらをいじるだけでサブドメインを利用することができる。
/etc/nginx/sites-available/default  
のserver_nameをサブドメインつきの blog.mmaakkyyii.comに変更  
mmaakkyyii.comもとりあえずblog.mmaakkyyii.comにリダイレクト  

# SSL証明書関連
```
sudo certbot certonly --webroot -w /home/makyi/blog/MakiBlog/public --expand -d blog.mmaakkyyii.com
```
これでblog.mmaakkyyii.com向けの証明書発行する  
/etc/nginx/sites-available/default
のSSL証明書の部分のパスも変更blog.mmaakkyyii.comに変更
mmaaakkyyii.comからリダイレクトする部分は今まで通りのmmaakkyyii.comの証明書を利用する。

/etc/nginx/sites-available/default
```
server {
        listen 80;
        listen [::]:80;
        server_name *.mmaakkyyii.com;
        return 301  https://blog.mmaakkyyii.com$request_uri;

}
server {
        listen 443 ssl;
        listen [::]:443 ssl;
        ssl_certificate     /etc/letsencrypt/live/blog.mmaakkyyii.com/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/blog.mmaakkyyii.com/privkey.pem;

        root /home/makyi/blog/MakiBlog/public/;

        index index.html index.htm index.nginx-debian.html;

        server_name blog.mmaakkyyii.com;

        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
        error_page 404 /404.html;
        location = /404.html{
                internal;
        }

}

server {
        listen 443 ssl;
        listen [::]:443 ssl;
        ssl_certificate     /etc/letsencrypt/live/mmaakkyyii.com/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/mmaakkyyii.com/privkey.pem;

        server_name mmaakkyyii.com;

        return 301  https://blog.mmaakkyyii.com$request_uri;

}

```