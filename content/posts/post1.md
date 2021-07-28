---
title: "ブログをつくった"
date: 2021-07-26T23:04:48+09:00
draft: false
categories: ["post"]
tags: ["blog","hugo"]
---

# はじめに
こんにちはマッキーです。ブログ記事を乗せるためのサイトを作りました。  
2年くらい前にRaspberry piでブログサーバを立てていたけど放置してたのですが、もう一度記事が書けそうな場所が欲しくなったのでまたサイトを作りました。  
せっかくmmaakkyyii.comというドメインを取っていたけど何もしないのも流石にもったいないのでまた復活させます。  

# やりたかったこと
以前使っていたRaspberry Pi B+を動かそうとしたところUSBポートが一部使えなかったりして、今後使うのも微妙な気がしたので、最近買って放置していたRaspberry Pi Zero上でブログサーバを立てます。  
以前はWordPressを使っていたのですが、今回は軽くて使いやすそうだったので[Hugo](https://gohugo.io/)というフレームワークを使ってサイトを作ります。  
また前回は記事がローカルに保存されていたため、記事の復旧ができなかったりしたので、記事自体はGitHub上に保存するようにします。

# できたもの
・[このサイト](https://mmaakkyyii.com)  
・[github](https://github.com/mmaakkyyii/MakiBlog)

# まだできてないこと・やりたいこと
## 記事の更新方法
今はラズパイ上のhugoフォルダ内をいじることで記事が更新されるようになっている。  
これをgithubに記事をpushした時にすぐラズパイ側でも記事が更新されるようにしたいGitHub Actionというものをつかえばできそうなのでこれを使う
## 見栄え
アイコンとかテーマも今回はanankeというものを使ったけどデフォルトのままなので今後いじりたい
## ページの上の方とかにメニューバー的なもの
タグみたいなので記事の分類とかできるようにしたい
## 記事
書こう!


　
　
# 作業ログ
作業ログは今回ブログを作ろうと思った理由の一つで、いままでこういった作業の記録を残していなかったため無駄な時間を使うことがあったりしたので自分用に記録を残していくのだ！  
以下ユーザーは/home/makyi
## Goのインストール
今回サーバにするPCはRaspberry Pi ZeroでCPUのアーキテクチャによってダウンロードするべきファイルが違うらしい。今回のラズパイzeroではarmv6
### ダウンロード
```
curl -O https://dl.google.com/go/go1.16.6.linux-armv6l.tar.gz
```
### SHA256チェックサム
```
sha256sum go1.16.6.linux-armv6l.tar.gz
```
これで ```b1ca342e81897da3f25da4e75ae29b267db1674fe7222d9bfc4c666bcf6fce69``` が出力されればおｋ 
https://golang.org/dl/

### ファイルの展開
```
sudo tar -C /usr/local -xzf go1.16.6.linux-armv6l.tar.gz
```

### パスの追加
.bashrcの最後に
```
export PATH=$PATH:/usr/local/go/bin
```
を追加

## hugoのインストール
```
git clone https://github.com/gohugoio/hugo.git
cd hugo
go install  --tags extended
```
最初SSH接続した状態でインストールをしようとしたが、途中で止まったりした。
しかしSSHではなく直接ディスプレイをつないでインストールしたところ問題なくインストールができた。理由は良く分からなかった。

### hugoでサイト作成
ここまでくると普通にhugoのチュートリアルとか通りの操作が普通にできるようになっている。
```
mkdir blog
cd blog
hugo new site MakiBlog
cd MakiBlog
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke

hugo new posts/post1.md
vi config.toml  ->ここでtheme = "ananke"追加
```
この状態で
```
hugo server -D
```
でローカルホストにアクセスすれば生成されたサイトが見れる
また
```
hugo
```
を実行するとpublicというフォルダができてここを公開すればいいというフォルダが完成

## Nginxのインストール
Nginxを使ってWebサーバが作れる。
最初はhugoのサーバでも一応実用的なサーバとして使えるみたいだったのでhugoだけで完結できると思ったが、調べたときの参考例が圧倒的に少なかったので諦めた。

### インストール
```
sudo apt-get install nginx
```
### ポートとかデフォルトのフォルダの変更
デフォルトだとnginxの/var/www/がルートフォルダになっているが、今回はhugoで作ったpublicフォルダをルートフォルダにしたいので、
/etc/nginx/sites-available/defaultの
root /var/www/;をroot /home/makyi/blog/MakiBlog/public/;に変更

この状態で
```
sudo /etc/init.d/nginx start
```
でローカルホストにアクセスすると hugoで生成したサイトが表示できる。
またこの状態でwifiルーターの設定で自分のグローバルIPをPCのIPアドレスに転送するようにすれば外部からのアクセスができるようになる。

## SSLの設定
### certbotのインストール
```
sudo apt-get install certbot
```
### 証明書発行
```
sudo certbot certonly --webroot -w /home/makyi/blog/MakiBlog/public -d mmaakkyyii.com
```
これで証明書が発行される

### nginxのSSL設定
/etc/nginx/site-available/defaultを編集
HTTPの80ポートに来た場合はHTTPSの方にリダイレクトする設定で
ssl証明書は先ほど取得した場所を指定する。
```
server{
    listen 80;
    listen [::]:80;
    server_name mmaakkyyii.com;
    return 301 https://mmaakkyyii.com&request_uri;
}

server {
    listen 443 ssl;
    listen [::]:443 ssl;
    ssl_certificate     /etc/letsencrypt/live/mmaakkyyii.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/mmaakkyyii.com/privkey.pem;

    root /home/makyi/blog/MakiBlog/public/;

    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

}
```
wifiルータの設定で443ポートに来たアクセスも同様にラズパイのIPアドレスに転送するようにすればHTTPSの接続もできるようになってるはず