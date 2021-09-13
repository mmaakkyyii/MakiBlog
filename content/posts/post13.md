---
title: "GoAccessでアクセスログの可視化をする"
date: 2021-09-13T21:51:09+09:00
draft: false
categories: ["blog"]
tags: ["blog","nginx","GoAccess"]
---

# GoAccess
[GoAccess](https://goaccess.io/)はwebサイト等のアクセスログをいい感じに可視化することができるソフトウェア

# install
[公式ページ](https://goaccess.io/download)より、Raspberry pi上で

```
sudo apt-get install goaccess
```
これでおｋ

インストールができたら
```
sudo goaccess /var/log/nginx/access.log -o /home/maki/accessReport/public/report.html --log-format=COMBINED
```
で、nginxのログファイルを読み込んで、/home/maki/accessReport/public/report.htmlにレポートを出力する。  
これをリアルタイムにアクセスログを反映させるには --real-time-html オプションをつける  

さらにこれをバックグラウンドで行うために  
```
sudo goaccess /var/log/nginx/access.log -o /home/maki/accessReport/public/report.html --log-format=COMBINED --real-time-html &
```
とした。これでよさそう？  
/accessReport/public/というフォルダはローカルのみで開くために新しく作ったフォルダ。
アクセスすると
![](../report.JPG)
こんな感じのダッシュボードが見れる。
